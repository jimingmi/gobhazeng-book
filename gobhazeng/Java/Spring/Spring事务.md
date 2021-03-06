# Spring事务

## 事务隔离级别

隔离级别是指若干个并发的事务之间的隔离程度。

`TransactionDefinition`接口中定义了五个表示隔离级别的常量：

 - `TransactionDefinition.ISOLATION_DEFAULT`：这是默认值，表示使用底层数据库的默认隔离级别。对大部分数据库而言，通常这值就是`TransactionDefinition.ISOLATION_READ_COMMITTED`。
 - `TransactionDefinition.ISOLATION_READ_UNCOMMITTED`：该隔离级别表示一个事务可以读取另一个事务修改但还没有提交的数据。该级别不能防止脏读，不可重复读和幻读，因此很少使用该隔离级别。比如PostgreSQL实际上并没有此级别。
 - `TransactionDefinition.ISOLATION_READ_COMMITTED`：该隔离级别表示一个事务只能读取另一个事务已经提交的数据。该级别可以防止脏读，这也是大多数情况下的推荐值。
 - `TransactionDefinition.ISOLATION_REPEATABLE_READ`：该隔离级别表示一个事务在整个过程中可以多次重复执行某个查询，并且每次返回的记录都相同。该级别可以防止脏读和不可重复读。
 - `TransactionDefinition.ISOLATION_SERIALIZABLE`：所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

|    |  脏读  |  不可重复读  |  幻读  |
|---|---|---|---|
|  Read Uncommitted  |  √  |  √  |  √  |
|  Read Committed  |  ×  |  √  |  √  |
|  Repeatable Read  |  ×  |  ×  |  √  |
|  Serializable  |  ×  |  ×  |  ×  |

 - **1.脏读**

脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。

 - **2.不可重复读**

是指在一个事务内，多次读同一数据。在这个事务还没有结束时，另外一个事务也访问该同一数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。（即不能读到相同的数据内容）
例如，一个编辑人员两次读取同一文档，但在两次读取之间，作者重写了该文档。当编辑人员第二次读取文档时，文档已更改。原始读取不可重复。如果只有在作者全部完成编写后编辑人员才可以读取文档，则可以避免该问题。

 - **3.幻读**

是指当事务不是独立执行时发生的一种现象，例如第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象
发生了幻觉一样。

## 事务传播行为

所谓事务的传播行为是指，如果在开始当前事务之前，一个事务上下文已经存在，此时有若干选项可以指定一个事务性方法的执行行为。

`TransactionDefinition`定义中包括了如下几个表示传播行为的常量：

 - `TransactionDefinition.PROPAGATION_REQUIRED`：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。这是默认值。
 - `TransactionDefinition.PROPAGATION_REQUIRES_NEW`：创建一个新的事务，如果当前存在事务，则把当前事务挂起。
 - `TransactionDefinition.PROPAGATION_SUPPORTS`：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
 - `TransactionDefinition.PROPAGATION_NOT_SUPPORTED`：以非事务方式运行，如果当前存在事务，则把当前事务挂起。
 - `TransactionDefinition.PROPAGATION_NEVER`：以非事务方式运行，如果当前存在事务，则抛出异常。
 - `TransactionDefinition.PROPAGATION_MANDATORY`：如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
 - `TransactionDefinition.PROPAGATION_NESTED`：如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于`TransactionDefinition.PROPAGATION_REQUIRED`。

## 事务超时

Spring默认的事务超时是没有的（`TransactionDefinition.TIMEOUT_DEFAULT = -1`）

需要设置`@Transactional(timeout = 2)`

> Spring事务超时 = 事务开始时到最后一个Statement创建时时间 + 最后一个Statement的执行时超时时间

```java
// DataSourceTransactionManager
/**
 * This implementation sets the isolation level but ignores the timeout.
 */
@Override
protected void doBegin(Object transaction, TransactionDefinition definition) {
	DataSourceTransactionObject txObject = (DataSourceTransactionObject) transaction;
	Connection con = null;

	try {
		if (!txObject.hasConnectionHolder() ||
				txObject.getConnectionHolder().isSynchronizedWithTransaction()) {
			Connection newCon = obtainDataSource().getConnection();
			if (logger.isDebugEnabled()) {
				logger.debug("Acquired Connection [" + newCon + "] for JDBC transaction");
			}
			txObject.setConnectionHolder(new ConnectionHolder(newCon), true);
		}

		txObject.getConnectionHolder().setSynchronizedWithTransaction(true);
		con = txObject.getConnectionHolder().getConnection();

		Integer previousIsolationLevel = DataSourceUtils.prepareConnectionForTransaction(con, definition);
		txObject.setPreviousIsolationLevel(previousIsolationLevel);

		// Switch to manual commit if necessary. This is very expensive in some JDBC drivers,
		// so we don't want to do it unnecessarily (for example if we've explicitly
		// configured the connection pool to set it already).
		if (con.getAutoCommit()) {
			txObject.setMustRestoreAutoCommit(true);
			if (logger.isDebugEnabled()) {
				logger.debug("Switching JDBC Connection [" + con + "] to manual commit");
			}
			con.setAutoCommit(false);
		}

		prepareTransactionalConnection(con, definition);
		txObject.getConnectionHolder().setTransactionActive(true);

    // 事务超时设置
		int timeout = determineTimeout(definition);
		if (timeout != TransactionDefinition.TIMEOUT_DEFAULT) {
			txObject.getConnectionHolder().setTimeoutInSeconds(timeout);
		}

		// Bind the connection holder to the thread.
		if (txObject.isNewConnectionHolder()) {
			TransactionSynchronizationManager.bindResource(obtainDataSource(), txObject.getConnectionHolder());
		}
	}

	catch (Throwable ex) {
		if (txObject.isNewConnectionHolder()) {
			DataSourceUtils.releaseConnection(con, obtainDataSource());
			txObject.setConnectionHolder(null, false);
		}
		throw new CannotCreateTransactionException("Could not open JDBC Connection for transaction", ex);
	}
}

int timeout = determineTimeout(definition);
if (timeout != TransactionDefinition.TIMEOUT_DEFAULT) {
	txObject.getConnectionHolder().setTimeoutInSeconds(timeout);
}

// ResourceHolderSupport
/**
 * Return the time to live for this object in milliseconds.
 * @return number of millseconds until expiration
 * @throws TransactionTimedOutException if the deadline has already been reached
 */
// 判断事务超时
public long getTimeToLiveInMillis() throws TransactionTimedOutException{
	if (this.deadline == null) {
		throw new IllegalStateException("No timeout specified for this resource holder");
	}
	long timeToLive = this.deadline.getTime() - System.currentTimeMillis();
	checkTransactionTimeout(timeToLive <= 0);
	return timeToLive;
}

/**
 * Set the transaction rollback-only if the deadline has been reached,
 * and throw a TransactionTimedOutException.
 */
// 事务超时的回滚设置
private void checkTransactionTimeout(boolean deadlineReached) throws TransactionTimedOutException {
	if (deadlineReached) {
		setRollbackOnly();
		throw new TransactionTimedOutException("Transaction timed out: deadline was " + this.deadline);
	}
}
```

## 事务只读属性

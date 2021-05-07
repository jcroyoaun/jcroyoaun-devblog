Oracle Recommendation for updating an order (revisited in 2016)
===============
Back in 2015-2016 I was assigned with the task of identifying why some orders couldn't complete the transaction (payment). It turns out that, some orders had some weird bugs (customer address number of characters exceeding the maximum length allowed on a legacy system), but some other orders just didn't show any particular root cause or behavior, they just got stuck, so our solution was to create a scheduled component that would resubmit failed orders.

The following is just some code I found from an Oracle handbook with the proper flow to safely updating an order by opening a TransactionDemarcation first and surrounding the modify order code with some try/catch blocks. (your code should go on //modify order)

ATGUpdate Code 1.1

```
public class MyClass {
    public void myMethod() throws Exception {
        boolean acquireLock = false;
        ClientLockManager lockManager = getLocalLockManager();

        try {
            acquireLock = !lockManager.hasWriteLock(profile.getRepositoryId(), Thread.currentThread());
            if (acquireLock) {
                lockManager.acquireWriteLock(profile.getRepositoryId(), Thread.currentThread());
            }

            boolean shouldRollback = false;

            TransactionDemarcation transactionDemarcation = new TransactionDemarcation();
            TransactionManager transactionManager = getTransactionManager();

            transactionDemarcation.begin(transactionManager, TransactionDemarcation.REQUIRED);
            final AZCSROrderManager orderManager =(AZCSROrderManager)getOrderManager();
            try {
                synchronized (getOrder()) {
                    //modify order
                }
            } catch (final Exception ex) {
                shouldRollback = true;
                vlogError(ce, "There has been an exception during processing of order: {0}", getOrder().getId());
            } finally {
                try {
                    transactionDemarcation.end(shouldRollback);
                } catch (final TransactionDemarcationException tde) {
                    vlogError(tde, "TransactionDemarcationException during finally: {0}", tde.getMessage());
                } finally {
                    vlogDebug("Ending Transaction for orderId: {0}", order.getId());
                }
            }
        } catch (final DeadlockException de) {
            vlogError(de, "There has been an exception during processing of order: {0}", order.getId());
        } catch (final TransactionDemarcationException tde) {
            vlogError(tde, "There has been an exception during processing of order: {0}", order.getId());
        } finally {
            try {
                if (acquireLock) {
                    lockManager.releaseWriteLock(getOrder().getProfileId(), Thread.currentThread(), true);
                }
            } catch (final Throwable th) {
                vlogError(th, "There has been an error during release of write lock: {0}", th.getMessage());
            }
        }
    }
}
```
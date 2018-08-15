# What are the differences between redeployment and rescheduling? {#concept_lct_mtn_vdb .concept}

## Redeployment {#section_fhh_4tn_vdb .section}

Redeployment is to redeploy an application by using the application image.

You can redeploy an application when:

-   You update the application image after deploying the application and want to deploy the application according to the updated image.
-   You want to activate or recreate the stopped or deleted containers. Container Service activates the stopped containers and recreates the deleted containers when you redeploy the application.

## Rescheduling {#section_hhh_4tn_vdb .section}

Rescheduling is to rebalance the number of containers running on each node, move containers from nodes with high loads to newly added nodes or nodes with low loads so as to rebalance the cluster load.

**Note:** Rescheduling only changes the distribution of containers on nodes and does not pull the image to redeploy the application.

For more information, see [Reschedule a service](../../../../intl.en-US/User Guide/Swarm cluster/Services/Reschedule a service.md#).


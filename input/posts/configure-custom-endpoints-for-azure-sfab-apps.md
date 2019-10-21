---
Title: Configuring custom endpoint for Azure Service Fabric applications
Published: 2018-09-27 09:57:00 +0000
Tags: 
  - Azure
  - Networking
  - Service Fabric
---
When creating clusters in Azure Service Fabric, one of the options you have when configuring the node types is to define the custom endpoints needed for your Service Fabric applications.

![Node type creation form](/images/sfab_nodetype_custom_endpoints.png)

However, after creating the cluster, if you go back to the node type details you'll notice that there's no option to allow you to customize the endpoints, either by adding new ones or removing the ones configured initially when creating the cluster.

![Node type details pane](/images/sfab_nodetype_custom_endpoints_2.png)

I've done some searching and found out that there are some people out there that didn't find out a solution for this and come up with various workarounds, one of them was to delete everything and recreate the cluster from scratch! That is never a good option obviously.

Let's try to avoid that kind of solutions and please notice that when you create a cluster one of the resources that gets configured is a load balancer. Is in that resource that you can configure the custom endpoint ports needed to reach your services from the outside world.

Go to the load balancer configuration and open the health probes section. In there you need to add a new health probe configured to the port you need for your endpoint, in this case 21000.

![Node type details pane](/images/app_probe.png)

When Azure finishes the creation of the Health probe, you can go to the Load Balancing Rules section and create a new rule that will use that Health Probe and is configured to the same port, such as the example below.

![Node type details pane](/images/load_balance_rule.png)

And that's it. Your service is now exposed to the outside world via the chosen port.

Happy coding.


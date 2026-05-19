# Terraform VMSS and Alerts Setup

Azure Monitor's responsibility ends at:

- Detect
- Fire alert
- Execute action group

After that:

- Logic App processes the alert
- Service Bus stores or distributes it

## How Azure Monitor Works Here

1. Azure Monitor evaluates alert rules.
   - It checks the VMSS CPU rule.
   - It checks the Load Balancer availability rule.

2. If a rule condition is met, Azure Monitor fires an alert.
   - That alert is associated with the Action Group.

3. The Action Group executes its receivers.
   - Email receiver sends email.
   - Logic App receiver invokes the Logic App HTTP trigger.

4. Logic App receives the alert payload.
   - The HTTP trigger accepts the incoming alert body.

5. Logic App sends that payload to Service Bus.
   - It uses the Service Bus connector or API connection.
   - It publishes the message into the Service Bus topic.

## Monitoring Required Components / Resources

1. `Log Analytics Workspace`
   - Data store / source

2. `Application Insights`
   - Connected to Log Analytics
   - App-level telemetry

3. `Autoscale Settings`
   - Watches a resource metric
   - Changes VMSS instance count when thresholds are crossed

4. `Alert Rules`
   - Thresholds that trigger alerts

5. `Action Group`
   - Defines what Azure should do when an alert happens

6. `Logic App Receiver`
   - Workflow container: `azurerm_logic_app_workflow.this`
   - Trigger: `azurerm_logic_app_trigger_http_request.alert` exposes an HTTP endpoint
   - Azure Monitor Action Group sends alert data to this endpoint
   - Action: `azurerm_logic_app_action_custom.send_to_servicebus` takes the request body and posts it into Service Bus

7. `Service Bus`
   - `data.azurerm_managed_api.servicebus` finds the Azure-managed Service Bus connector in your region
   - `azurerm_api_connection.servicebus` creates an authenticated connector instance using your Service Bus connection string

8. `Service Bus Topic`
   - After the Logic App receives the Azure Monitor alert payload, it sends it to the topic

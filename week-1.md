# Investigating an Azure Resource Naming Violation and Policy Bypass

## Scenario
A resource was discovered in the Mad Hat Labs Azure environment that did not follow the organization’s established resource-group naming convention. The investigation was conducted to determine what was deployed, who or what created it, and why Azure Policy allowed a non-compliant resource to be created.

## Environment
Platform: Microsoft Azure
Subscription: Mad Hat Labs
Services: Azure Resource Groups, Azure Resource Manager (ARM), Azure Policy
Tools: Azure Portal
Access level: Operative access to the Mad Hat Labs Azure training environment
Scope: Subscription and resource-group-level investigation

## Investigation
1. Established the expected naming convention

I began by reviewing the resource groups within the Mad Hat Labs subscription and comparing their names against the organization's established naming convention. The expected pattern uses components identifying the resource-group type, workload, environment, region, and instance number. Most of the resource groups followed this convention, making the outlier relatively easy to identify. One resource group immediately stood out because it lacked the expected rg- prefix and did not identify an environment or region.

Finding: The resource group [REDACTED] was the naming-convention outlier and became the focus of the investigation.
<img width="1702" height="752" alt="Stage 1 Locate Offender" src="https://github.com/user-attachments/assets/6eabd912-761f-48c6-8012-4cb7232643e2" />

2. Inspected the resource inside the offending resource group

I opened the resource group to determine what had actually been deployed. The resource group contained exactly one resource, suggesting this was a relatively isolated deployment rather than a large workload with multiple dependent resources. I opened the resource and reviewed its Tags blade. Tags can provide useful context during an investigation because they often identify ownership, purpose, lifecycle, or other metadata associated with a resource. One tag was named in way that suggested it belonged to an intern. The presence of this tag provided an important clue that the resource was intentionally created as part of an intern/test deployment rather than appearing as an accidental Azure artifact. 

Finding: The resource was associated with the intern through its tagging metadata.
<img width="1690" height="371" alt="Stage 2 Inspect the Payload" src="https://github.com/user-attachments/assets/10740b99-7470-432d-8ba3-a83daea3ff29" />

3. Traced the resource back to its deployment

Next, I returned to the resource group's overview and opened the Deployments blade. Azure Resource Manager records deployments associated with a resource group, providing an audit trail that can include the deployment name, timestamp, parameters, and status. I reviewed the deployments and identified the deployment associated with the creation of the intern's resource. The deployment name itself provided an additional clue about the person or process responsible for the deployment and the approximate time it occurred. I also confirmed that the deployment completed successfully, explaining how the resource ultimately came to exist despite violating the organization's naming standards.

Finding: The resource was created through an ARM deployment rather than appearing independently, and the deployment record provided a useful starting point for establishing the resource's creation timeline.
<img width="1396" height="528" alt="Stage 3 Trace the Deployment" src="https://github.com/user-attachments/assets/3176c694-3985-41e2-89ad-ab8e38cb1da6" />

4. Investigated Azure Policy compliance

The next question was more important from a governance perspective: Why was the naming violation allowed to happen? I opened the resource group's Policies blade and reviewed the compliance information. A naming policy was present and was currently reporting the resource as non-compliant. This confirmed that Azure Policy was aware that the resource violated the expected naming convention. I then examined the policy assignments associated with the resource group and opened the relevant naming policy assignment. Under the Parameters tab, I reviewed the policy's Effect setting. The effect was configured as: Audit

This explained the behavior observed during the investigation. Azure Policy's Audit effect identifies and records non-compliant resources but does not prevent the deployment from occurring. In contrast, the Deny effect would have rejected the request when the deployment attempted to create the non-compliant resource. The existence of the misnamed resource, combined with the policy's Audit configuration, therefore explained why the deployment succeeded.

Finding: The naming policy was active and correctly identified the violation, but its configuration allowed the violation to occur because the policy was set to Audit rather than Deny.
<img width="1699" height="601" alt="Stage 4 Why did policy not stop this" src="https://github.com/user-attachments/assets/f27ed9ad-4da1-4a49-966b-8216343b2016" />

5. Connected the evidence

At this point, the investigation produced a consistent chain of evidence:

A resource group violated the organization's naming convention.
The resource group contained a single resource.
The resource contained an intern-flag identifying it as an intern/test resource.
An ARM deployment record showed how the resource was created.
Azure Policy identified the resource as non-compliant.
The policy's Effect was configured as Audit, allowing the deployment to proceed.

The investigation therefore did not simply identify a naming violation. It established why the violation existed and why the organization's governance controls did not prevent it.

## What broke / what surprised me
What surprised me? How easy it is for one slight misconfiguration to have a HUGE effect.

## Findings and recommendations
Findings: An improperly named resource was deployed by an intern and flagged as non-compliant by Azure Policy. The policy allowed the deployment because its enforcement effect was set to Audit instead of Deny.
Recommendations: Change the naming policy to Deny so future resources that don't follow the naming standard are blocked. Review the existing non-compliant resource and either rename/remove it if it is no longer needed, or document why it should remain.

## What I learned
-Learned how Azure Policy works, especially the difference between Audit (reports violations) and Deny (blocks non-compliant resources).
-Learned that resource tags and deployment history can provide useful clues when investigating who created a resource and why.
-As an Azure novice, I found that following the resource's history step-by-step—resource group → resource → deployment → policy—made the investigation much easier to understand.
-What I'd do differently: Next time, I would check the policy's Effect earlier in the investigation, since that quickly explains whether Azure is configured to prevent or simply report violations.
-This investigation helped me see how naming conventions, resource organization, and Azure Policy work together as basic governance controls.

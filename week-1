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

Finding: The resource group [OFFENDING RESOURCE GROUP NAME] was the naming-convention outlier and became the focus of the investigation.


Screenshot: Azure Portal showing the Mad Hat Labs subscription and resource-group list, with the offending resource group visible.

2. Inspected the resource inside the offending resource group

I opened the resource group to determine what had actually been deployed.

The resource group contained exactly one resource, suggesting this was a relatively isolated deployment rather than a large workload with multiple dependent resources.

I opened the resource and reviewed its Tags blade. Tags can provide useful context during an investigation because they often identify ownership, purpose, lifecycle, or other metadata associated with a resource.

One tag was named intern-flag.

The value of that tag was:

[INTERN-FLAG VALUE]

The presence of this tag provided an important clue that the resource was intentionally created as part of an intern/test deployment rather than appearing as an accidental Azure artifact.

Finding: The resource was associated with the intern through its tagging metadata.

Screenshot: Resource overview showing the single deployed resource.

Screenshot: Tags blade showing the intern-flag tag and its value.

3. Traced the resource back to its deployment

Next, I returned to the resource group's overview and opened the Deployments blade.

Azure Resource Manager records deployments associated with a resource group, providing an audit trail that can include the deployment name, timestamp, parameters, and status.

I reviewed the deployments and identified the deployment associated with the creation of the intern's resource.

The deployment was named:

[DEPLOYMENT NAME]

The deployment name itself provided an additional clue about the person or process responsible for the deployment and the approximate time it occurred.

I also confirmed that the deployment completed successfully, explaining how the resource ultimately came to exist despite violating the organization's naming standards.

Finding: The resource was created through an ARM deployment rather than appearing independently, and the deployment record provided a useful starting point for establishing the resource's creation timeline.

Screenshot: Resource group's Deployments blade showing the deployment list.

Screenshot: Deployment details showing the deployment name, timestamp, and successful status.

Screenshot: Deployment inputs/parameters showing the values used during deployment.

4. Investigated Azure Policy compliance

The next question was more important from a governance perspective: Why was the naming violation allowed to happen?

I opened the resource group's Policies blade and reviewed the compliance information.

A naming policy was present and was currently reporting the resource as non-compliant. This confirmed that Azure Policy was aware that the resource violated the expected naming convention.

I then examined the policy assignments associated with the resource group and opened the relevant naming policy assignment.

Under the Parameters tab, I reviewed the policy's Effect setting.

The effect was configured as:

Audit

This explained the behavior observed during the investigation.

Azure Policy's Audit effect identifies and records non-compliant resources but does not prevent the deployment from occurring. In contrast, the Deny effect would have rejected the request when the deployment attempted to create the non-compliant resource.

The existence of the misnamed resource, combined with the policy's Audit configuration, therefore explained why the deployment succeeded.

Finding: The naming policy was active and correctly identified the violation, but its configuration allowed the violation to occur because the policy was set to Audit rather than Deny.

Screenshot: Policies blade showing the naming policy as non-compliant.

Screenshot: Policy Assignments showing the naming policy assignment.

Screenshot: Parameters tab showing the Effect configured as Audit.

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
The most credible section in the document. Dead ends, wrong guesses, the thing that took an hour. Employers know real work is messy. This section separates you from certificate collectors.

## Findings and recommendations
What you determined, plus 2 or 3 recommendations as if you were reporting to the resource owner.

## What I learned
3 to 5 bullets. At least one technical, one "what I'd do differently."

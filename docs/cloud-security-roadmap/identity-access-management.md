# Understanding Identity and Access Management (IAM)

![alt text](<IAM (1).jpg>)


`Identity and Access Management (IAM)` is the security discipline that enables the right individuals or entities to access the right resources at the right times and for the right reasons. In the context of cloud security, IAM is a cornerstone for protecting sensitive data and ensuring the `principle of least privilege` is enforced. It provides the framework for managing digital identities and controlling their access to cloud resources.   

## Core components of IAM:

- **Users**: These represent individual people or services that interact with your cloud environment. Each user has a unique identity that needs to be authenticated (verified) before they can be authorized (granted permission).
- **Groups**: Groups are collections of users that are managed together. Assigning permissions to groups simplifies the management of access for multiple users with similar roles or responsibilities. Instead of managing permissions for each user individually, you manage them at the group level.
- **Policies**: Policies are sets of rules that define what actions users or groups are allowed or denied to perform on specific resources. These policies are attached to users, groups, or resources to enforce access control. Policies are the heart of authorization in IAM.   
- **Policy Assignment**: This refers to the act of attaching policies to users, groups, or resources. When a user attempts to access a resource, the IAM system evaluates the policies associated with that user (and their groups) and the resource to determine if the action is permitted.

## Recommended Videos on YT
### Azure 
<iframe width="560" height="315" src="https://www.youtube.com/embed/megA6BPpYqo?si=BzFrxyZipSoxk3wb" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### AWS
<iframe width="560" height="315" src="https://www.youtube.com/embed/YMj33ToS8cI?si=1Avk-SnEg-FYVDp_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Practice


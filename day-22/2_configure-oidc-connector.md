# commands to configure IAM OIDC provider (Open ID Connect Provider, for intagrating with Other Identity Providers, attaching them for support, e.g. allowing Github IdP or Google IdP)
- For the Demo **IAM IdP** is allowed to access AWS resources.


```
export cluster_name=demo-cluster
```

```
oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5) 
```

## Check if there is an IAM OIDC provider configured already

- aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4\n 

If not, run the below command

```
eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
```



---
An **IAM OpenID Connect (OIDC) provider in AWS enables your Amazon Web Services Identity and Access Management (IAM) roles to trust an OpenID Connect-compatible identity provider (IdP).** This setup is typically used to securely allow external identities from an OIDC IdP (such as **Amazon EKS**, **GitHub**, **Google**, etc.) to assume IAM roles in your AWS account without the need to create long-term AWS credentials. 

OIDC is an identity layer built on top of the OAuth 2.0 protocol, providing authentication in a simple and standardized manner.

**Use Cases for IAM OIDC Providers:**

1. **Amazon EKS Workloads:**  
   **When using Amazon EKS, you can configure IAM roles for service accounts (IRSA) to assign IAM permissions directly to your Kubernetes workloads using an OIDC provider without needing to manage long-lived AWS credentials.**
   
2. **Federated Access:**  
   Federating access between an external identity provider (like GitHub, Google, or another OIDC-compatible IdP) and AWS allows external users or systems to access AWS resources via IAM roles. 

3. **Security Automation:**  
   Secure, short-lived, and auditable credentials can be issued for users or services without directly exposing AWS credentials.

**How IAM OIDC Providers Work:**

1. **OIDC Provider Setup:**  
   **You create or configure an OIDC identity provider within AWS IAM. This allows AWS to validate and trust identity tokens that are issued by the OIDC IdP.**

2. **IAM Role Trust Policies:**  
   IAM roles are configured with a trust relationship, allowing identities from the OIDC provider to assume the role. You can specify conditions in the trust policy, such as which specific identities (or scopes) are allowed.

3. **Identity Token Validation:**  
   When an external user or system wants to assume a role via OIDC, they provide an OIDC token, which AWS validates against the trusted OIDC provider.

4. **Role Assumption:**  
   If the OIDC token is valid and meets the conditions set in the trust policy, the role is assumed, and the user or system is issued temporary AWS credentials.

**Steps to Set Up IAM OIDC Provider for EKS (Example):**

1. **Create an IAM OIDC Provider for EKS:**
   Amazon EKS automatically creates an OIDC identity provider when the cluster is created. You can verify this with the following AWS CLI command:
   ```bash
   aws eks describe-cluster --name <cluster-name> --query "cluster.identity.oidc.issuer"
   ```

   Example output:
   ```json
   "https://oidc.eks.<region>.amazonaws.com/id/<eks-cluster-id>"
   ```

2. **Associate the OIDC Provider with IAM:**
   You need to create an IAM OIDC identity provider to allow EKS to use OIDC. In the AWS Management Console, you can go to **IAM** > **Identity providers** and create an OpenID Connect provider.

   Using the AWS CLI:
   ```bash
   aws iam create-open-id-connect-provider \
     --url https://oidc.eks.<region>.amazonaws.com/id/<eks-cluster-id> \
     --thumbprint-list <thumbprint> \
     --client-id-list <client-id>
   ```

   Replace `<thumbprint>` and `<client-id>` with appropriate values.

3. **Create an IAM Role for OIDC Federated Access:**
   You can create an IAM role that is assumed by the external identity provider via OIDC. For example, when using EKS, you create a role that Kubernetes pods can assume.
   The role’s trust policy will allow it to be assumed by the OIDC provider:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Federated": "arn:aws:iam::<account-id>:oidc-provider/oidc.eks.<region>.amazonaws.com/id/<eks-cluster-id>"
         },
         "Action": "sts:AssumeRoleWithWebIdentity",
         "Condition": {
           "StringEquals": {
             "oidc.eks.<region>.amazonaws.com/id/<eks-cluster-id>:sub": "system:serviceaccount:<namespace>:<serviceaccount-name>"
           }
         }
       }
     ]
   }
   ```

4. **Assign IAM Permissions:**
   After creating the IAM role, attach the necessary AWS permissions policies (e.g., S3 access, DynamoDB access) to the role so that it grants the desired AWS permissions when assumed by an external identity.

5. **Using OIDC for Kubernetes Workloads:**
   In an EKS cluster, you would map Kubernetes service accounts to the IAM role you created. This allows specific Kubernetes workloads to interact with AWS services using the IAM permissions defined by the role.

**Trust Policy Structure:**

The trust policy of an IAM role for OIDC specifies conditions under which the role can be assumed, such as the identity provider and specific tokens.

An example trust policy for federating with an OIDC provider:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::123456789012:oidc-provider/accounts.google.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "accounts.google.com:sub": "accounts.google.com/<user-id>"
        }
      }
    }
  ]
}
```

**Common OIDC Identity Providers:**

- **Amazon EKS:** Automatically sets up an OIDC provider for Kubernetes service accounts.
- **GitHub Actions:** Can use OIDC to authenticate with AWS and assume IAM roles during CI/CD workflows.
- **Google, Microsoft, or other OIDC IdPs:** These providers can issue OIDC tokens to authenticate users or applications with AWS.

**Benefits of Using OIDC with IAM:**

- **No Long-Term Credentials:** Users or applications can authenticate using short-lived tokens instead of AWS long-term credentials.
- **Security and Fine-Grained Access:** Trust policies can be configured to allow only specific identities or actions, limiting access to AWS resources.
- **Seamless Integration with External IdPs:** Easily federate access for external systems, applications, or users without requiring complex setups.

By integrating an OIDC provider with IAM, you create a flexible and secure way to manage and federate access to your AWS resources using industry-standard authentication mechanisms.
---
title: Authentication issues in Azure HDInsight
description: Authentication issues in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 2ffc3ced360e1fdf00f69ea5826e6c6af7806f71
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215980"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Authentication issues in Azure HDInsight

This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

On secure clusters backed by Azure Data Lake (Gen1 or Gen2), when domain users sign in to the cluster services through HDI Gateway (like signing in to the Apache Ambari portal), HDI Gateway will try to obtain an OAuth token from Azure Active Directory (Azure AD) first, and then get a Kerberos ticket from Azure AD DS. Authentication can fail in either of these stages. This article is aimed at debugging some of those issues.

When the authentication fails, you will get prompted for credentials. If you cancel this dialog, the error message will be printed. Here are some of the common error messages:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant or unauthorized_client, 50126

### <a name="issue"></a>Problema

Sign in fails for federated users with error code 50126 (sign in succeeds for cloud users). Error message is similar to:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Causa

Azure AD error code 50126 means the `AllowCloudPasswordValidation` policy has not been set by the tenant.

### <a name="resolution"></a>Resolução

The Company Administrator of the Azure AD tenant should enable Azure AD to use password hashes for ADFS backed users.  Apply the `AllowCloudPasswordValidationPolicy` as shown in the article [Use Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant or unauthorized_client, 50034

### <a name="issue"></a>Problema

Sign in fails with error code 50034. Error message is similar to:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Causa

User name is incorrect (does not exist). The user is not using the same username that is used in Azure portal.

### <a name="resolution"></a>Resolução

Use the same user name that works in that portal.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant or unauthorized_client, 50053

### <a name="issue"></a>Problema

User account is locked out, error code 50053. Error message is similar to:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Causa

Too many sign in attempts with an incorrect password.

### <a name="resolution"></a>Resolução

Wait for 30 minutes or so, stop any applications that might be trying to authenticate.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant or unauthorized_client, 50053

### <a name="issue"></a>Problema

Password expired, error code 50053. Error message is similar to:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Causa

Password is expired.

### <a name="resolution"></a>Resolução

Change the password in the Azure portal (on your on-premises system) and then wait for 30 minutes for sync to catch up.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problema

Receive error message `interaction_required`.

### <a name="cause"></a>Causa

A política de acesso condicional ou a MFA está a ser aplicada ao utilizador. Como a autenticação interativa ainda não é suportada, a política de Acesso condicional/MFA não deverá estar ativada para o utilizador ou cluster. If you choose to exempt the cluster (IP address based exemption policy), then make sure that the AD `ServiceEndpoints` are enabled for that vnet.

### <a name="resolution"></a>Resolução

Use conditional access policy and exempt the HDInisght clusters from MFA as shown in [Configure a HDInsight cluster with Enterprise Security Package by using Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Sign in denied

### <a name="issue"></a>Problema

Sign in is denied.

### <a name="cause"></a>Causa

To get to this stage, your OAuth authentication is not an issue, but Kerberos authentication is. If this cluster is backed by ADLS, OAuth sign in has succeeded before Kerberos auth is attempted. On WASB clusters, OAuth sign in is not attempted. There could be many reasons for Kerberos failure - like password hashes are out of sync, user account locked out in Azure AD DS, and so on. Password hashes sync only when the user changes password. When you create the Azure AD DS instance, it will start syncing passwords that are changed after the creation. It won't retroactively sync passwords that were set before its inception.

### <a name="resolution"></a>Resolução

If you think passwords may not be in sync, try changing the password and wait for a few minutes to sync.

Try to SSH into a You will need to try to authenticate (kinit) using the same user credentials, from a machine that is joined to the domain. SSH into the head / edge node with a local user and then run kinit.

---

## <a name="kinit-fails"></a>kinit fails

### <a name="issue"></a>Problema

Kinit fails.

### <a name="cause"></a>Causa

Varies.

### <a name="resolution"></a>Resolução

For kinit to succeed, you need to know your `sAMAccountName` (this is the short account name without the realm). `sAMAccountName` is usually the account prefix (like bob in `bob@contoso.com`). For some users, it could be different. You will need the ability to browse / search the directory to learn your `sAMAccountName`.

Ways to find `sAMAccountName`:

* If you can sign in to Ambari using the local Ambari admin, look at the list of users.

* If you have a [domain joined windows machine](../../active-directory-domain-services/manage-domain.md), you can use the standard Windows AD tools to browse. This requires a working account in the domain.

* From the head node, you can use SAMBA commands to search. This requires a valid Kerberos session (successful kinit). net ads search "(userPrincipalName=bob*)"

    The search / browse results should show you the `sAMAccountName` attribute. Also, you could look at other attributes like `pwdLastSet`, `badPasswordTime`, `userPrincipalName` etc. to see if those properties match what you expect.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit fails with Preauthentication failure

### <a name="issue"></a>Problema

Kinit fails with `Preauthentication` failure.

### <a name="cause"></a>Causa

Incorrect username or password.

### <a name="resolution"></a>Resolução

Check your username and password. Also check for other properties described above. To enable verbose debugging, run `export KRB5_TRACE=/tmp/krb.log` from the session before trying kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Job / HDFS command fails due to TokenNotFoundException

### <a name="issue"></a>Problema

Job / HDFS command fails due to `TokenNotFoundException`.

### <a name="cause"></a>Causa

The required OAuth access token was not found for the job / command to succeed. The ADLS / ABFS driver will try to retrieve the OAuth access token from the credential service before making storage requests. This token gets registered when you sign in to the Ambari portal using the same user.

### <a name="resolution"></a>Resolução

Ensure that you have successfully logged in to the Ambari portal once through the username whose identity is used to run the job.

---

## <a name="error-fetching-access-token"></a>Error fetching access token

### <a name="issue"></a>Problema

User receives error message `Error fetching access token`.

### <a name="cause"></a>Causa

This error occurs intermittently when users try to access the ADLS Gen2 using ACLs and the Kerberos token has expired.

### <a name="resolution"></a>Resolução

* For Azure Data Lake Storage Gen1, clean browser cache and log into Ambari again.

* For Azure Data Lake Storage Gen2, Run `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` for the user the user is trying to login as

---

## <a name="next-steps"></a>Passos seguintes

If you didn't see your problem or are unable to solve your issue, visit one of the following channels for more support:

* Get answers from Azure experts through [Azure Community Support](https://azure.microsoft.com/support/community/).

* Connect with [@AzureSupport](https://twitter.com/azuresupport) - the official Microsoft Azure account for improving customer experience. Connecting the Azure community to the right resources: answers, support, and experts.

* If you need more help, you can submit a support request from the [Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Select **Support** from the menu bar or open the **Help + support** hub. For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Access to Subscription Management and billing support is included with your Microsoft Azure subscription, and Technical Support is provided through one of the [Azure Support Plans](https://azure.microsoft.com/support/plans/).

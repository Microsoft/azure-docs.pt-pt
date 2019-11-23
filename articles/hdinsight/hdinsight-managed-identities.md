---
title: Managed identities in Azure HDInsight
description: Provides an overview of the implementation of managed identities in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: e7be8fbf5f6c2c59e93d48729785dd34bae5955e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327379"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Managed identities in Azure HDInsight

A managed identity is an identity registered in Azure Active Directory (Azure AD) whose credentials are managed by Azure. With managed identities, you don't need to register service principals in Azure AD, or maintain credentials such as certificates.

Managed identities can be used in Azure HDInsight to allow your clusters to access Azure AD domain services, access Azure Key Vault, or access files in Azure Data Lake Storage Gen2.

There are two types of managed identities: user-assigned and system-assigned. Azure HDInsight uses user-assigned managed identities. A user-assigned managed identity is created as a standalone Azure resource, which you can then assign to one or more Azure service instances. In contrast, a system-assigned managed identity is created in Azure AD and then enabled directly on a particular Azure service instance automatically. The life of that system-assigned managed identity is then tied to the life of the service instance that it's enabled on.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight managed identity implementation

In Azure HDInsight, managed identities are provisioned on each node of the cluster. These identity components, however, are only usable by the HDInsight service. There's currently no supported method for you to generate access tokens using the managed identities installed on HDInsight cluster nodes. For some Azure services, managed identities are implemented with an endpoint that you can use to acquire access tokens for interacting with other Azure services on your own.

## <a name="create-a-managed-identity"></a>Create a managed identity

Managed identities can be created with any of the following methods:

* [Portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [O Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

The remaining steps for configuring the managed identity depend on the scenario where it will be used.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Managed identity scenarios in Azure HDInsight

Managed identities are used in Azure HDInsight in multiple scenarios. See the related documents for detailed setup and configuration instructions:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Passos seguintes

* [O que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

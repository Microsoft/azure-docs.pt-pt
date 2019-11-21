---
title: Delegate a subdomain - Azure DNS
description: With this learning path, get started delegating an Azure DNS subdomain.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: allensu
ms.openlocfilehash: 462282e9674e7a253f61c96338b54174c80fb03f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212382"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegate an Azure DNS subdomain

You can use the Azure portal to delegate a DNS subdomain. For example, if you own the contoso.com domain, you can delegate a subdomain called *engineering* to another, separate zone that you can administer separately from the contoso.com zone.

If you prefer, you can delegate a subdomain using [Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

To delegate an Azure DNS subdomain, you must first delegate your public domain to Azure DNS. See [Delegate a domain to Azure DNS](./dns-delegate-domain-azure-dns.md) for instructions on how to configure your name servers for delegation. Once your domain is delegated to your Azure DNS zone, you can delegate your subdomain.

> [!NOTE]
> Contoso.com is used as an example throughout this article. Substitua o seu nome de domínio por contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Create a zone for your subdomain

First, create the zone for the **engineering** subdomain.

1. From the Azure portal, select **Create a resource**.
2. In the search box, type **DNS**, and select **DNS zone**.
3. Selecione **Criar**.
4. In the **Create DNS zone** pane, type **engineering.contoso.com** in the **Name** text box.
5. Select the resource group for your zone. You might want to use the same resource group as the parent zone to keep similar resources together.
6. Clique em **Criar**.
7. After the deployment succeeds, go to the new zone.

## <a name="note-the-name-servers"></a>Note the name servers

Next, note the four name servers for the engineering subdomain.

On the **engineering** zone pane, note the four name servers for the zone. You will use these name servers later.

## <a name="create-a-test-record"></a>Create a test record

Create an **A** record to use for testing. For example, create a **www** A record and configure it with a **10.10.10.10** IP address.

## <a name="create-an-ns-record"></a>Create an NS record

Next, create a name server (NS) record  for the **engineering** zone.

1. Navigate to the zone for the parent domain.
2. Selecione **+ Conjunto de registos**.
3. On the **Add record set** pane, type **engineering** in the **Name** text box.
4. For **Type**, select **NS**.
5. Under **Name server**, enter the four name servers that you recorded previously from the **engineering** zone.
6. Clique em **OK**.

## <a name="test-the-delegation"></a>Test the delegation

Use nslookup to test the delegation.

1. Open a PowerShell window.
2. At command prompt, type `nslookup www.engineering.contoso.com.`
3. You should receive a non-authoritative answer showing the address **10.10.10.10**.

## <a name="next-steps"></a>Passos seguintes

Learn how to [configure reverse DNS for services hosted in Azure](dns-reverse-dns-for-azure-services.md).
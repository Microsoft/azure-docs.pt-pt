---
title: 'Tutorial: Enable custom domain & SSL for a static website in Azure'
description: Learn how to configure a custom domain for static website hosting.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327502"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Tutorial: Enable custom domain & SSL for a static website in Azure

Este tutorial é a segunda parte de uma série. In it, you learn to enable a custom domain endpoint with SSL for your static website. 

The tutorial shows how to use [Azure CDN](../../cdn/cdn-overview.md) to configure the custom domain endpoint for your static website. With Azure CDN, you can provision custom SSL certificates, use a custom domain, and configure custom rewrite rules all at the same time. Configuring Azure CDN results in additional charges, but provides consistent low latencies to your website from anywhere in the world. Azure CDN also provides SSL encryption with your own certificate. For information on Azure CDN pricing, see [Azure CDN pricing](https://azure.microsoft.com/pricing/details/cdn/).

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Create a CDN endpoint on the static website endpoint
> * Enable custom domain and SSL

## <a name="prerequisites"></a>Pré-requisitos

Before you begin this tutorial, complete part one, [Tutorial: Host a static website on Blob Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Sign in to the [Azure portal](https://portal.azure.com/) to get started.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Create a CDN endpoint on the static website endpoint

1. Locate your storage account in the Azure portal and display the account overview.
1. Select **Azure CDN** under the **Blob Service** menu to configure Azure CDN.
1. In the **CDN profile** section, specify a new or existing CDN profile. Para obter mais informações, veja [Início Rápido: Criar um perfil e um ponto final da CDN do Azure](../../cdn/cdn-create-new-endpoint.md).
1. Specify a pricing tier for the CDN endpoint. This tutorial uses the **Standard Akamai** pricing tier, because it propagates quickly, typically within a few minutes. Other pricing tiers may take longer to propagate, but may also offer other advantages. For more information, see [Compare Azure CDN product features](../../cdn/cdn-features.md).
1. In the **CDN endpoint name** field, specify a name for your CDN endpoint. The CDN endpoint must be unique across Azure.
1. Specify your the static website endpoint in the **Origin hostname** field. To find your static website endpoint, navigate to the **Static website** settings for your storage account. Copy the primary endpoint and paste it into the CDN configuration, removing the protocol identifier (*e.g.* , HTTPS).

    The following image shows an example endpoint configuration:

    ![Screenshot showing sample CDN endpoint configuration](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Create the CDN endpoint and wait for it to propagate.
1. To verify that the CDN endpoint is configured correctly, click on the endpoint to navigate to its settings. From the CDN overview for your storage account, locate the endpoint hostname, and navigate to the endpoint, as shown in the following image. The format of your CDN endpoint will be similar to `https://staticwebsitesamples.azureedge.net`.

    ![Screenshot showing overview of CDN endpoint](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Once the CDN endpoint propagation is complete, navigating to the CDN endpoint displays the contents of the index.html file that you previously uploaded to your static website.

1. To review the origin settings for your CDN endpoint, navigate to **Origin** under the **Settings** section for your CDN endpoint. You will see that the **Origin type** field is set to *Custom Origin* and that the **Origin hostname** field displays your static website endpoint.

    ![Screenshot showing Origin settings for CDN endpoint](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Enable custom domain and SSL

1. Create a CNAME record with your domain name provider to redirect your custom domain to the CDN endpoint. The CNAME record for the *www* subdomain should be similar to the following:

    ![Specify CNAME record for subdomain www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. In the Azure portal, display the settings for your CDN endpoint. Navigate to **Custom domains** under **Settings** to configure the custom domain and the SSL certificate.
1. Select **Add custom domain** and enter your domain name, then click **Add**.
1. Select the new custom domain mapping to provision an SSL certificate.
1. Set **Custom Domain HTTPS** to **ON**, then click **Save**. It may take several hours to configure your custom domain. The portal displays progress as shown in the following image.

    ![Screenshot showing progress of custom domain configuration](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Test the mapping of your static website to your custom domain by accessing the URL for your custom domain.

For more information about enabling HTTPS for custom domains, see [Tutorial: Configure HTTPS on an Azure CDN custom domain](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Passos seguintes

In part two of this tutorial, you learned how to configure a custom domain with SSL in Azure CDN for your static website.

For more information about configuring and using Azure CDN, see [What is Azure CDN?](../../cdn/cdn-overview.md).
---
title: Adicionar um certificado de autoridade de certificação personalizado-gerenciamento de API do Azure | Microsoft Docs
description: Saiba como adicionar um certificado de autoridade de certificação personalizado no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073610"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Como adicionar um certificado de autoridade de certificação personalizado no gerenciamento de API do Azure

O gerenciamento de API do Azure permite a instalação de certificados de autoridade de certificação no computador dentro dos repositórios de certificados raiz confiáveis e intermediários. Essa funcionalidade deverá ser usada se os serviços exigirem um certificado de autoridade de certificação personalizado.

O artigo mostra como gerenciar certificados de autoridade de certificação de uma instância de serviço de gerenciamento de API do Azure no portal do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Carregar um certificado de autoridade de certificação

![Adicionar certificados de autoridade de certificação](media/api-management-howto-ca-certificates/00.png)

Siga as etapas abaixo para carregar um novo certificado de autoridade de certificação. Se você ainda não criou uma instância de serviço de gerenciamento de API, consulte o tutorial [criar uma instância de serviço de gerenciamento de API](get-started-create-service-instance.md).

1. Navegue até a instância do serviço de gerenciamento de API do Azure no portal do Azure.

2. Selecione **certificados de autoridade de certificação** no menu.

3. Clique no botão **+ Adicionar** .  

    ![Adicionar certificados de autoridade de certificação](media/api-management-howto-ca-certificates/01.png)  

4. Procure o certificado e decida sobre o repositório de certificados. Somente a chave pública é necessária, portanto, a senha não é necessária.

    ![Adicionar certificados de autoridade de certificação](media/api-management-howto-ca-certificates/02.png)  

5. Clique em **Guardar**. Esta operação poderá demorar alguns minutos.

    ![Adicionar certificados de autoridade de certificação](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Você pode carregar um certificado de autoridade de `New-AzApiManagementSystemCertificate` certificação usando o comando do PowerShell.

## <a name="step1a"> </a>Excluir um certificado de cliente

Para excluir um certificado, clique em menu de contexto **...** e selecione **excluir** ao lado do certificado.

![Excluir certificados de autoridade de certificação](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a

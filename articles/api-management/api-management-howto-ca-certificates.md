---
title: Adicione um certificado ac personalizado - Azure API Management | Microsoft Docs
description: Saiba como adicionar um certificado ac personalizado na Azure API Management. Também pode ver instruções para apagar um certificado.
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
ms.openlocfilehash: 124bc053aa2c6e59e205bb6f33a9a96190799499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93102042"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Como adicionar um certificado ac personalizado na Azure API Management

A Azure API Management permite instalar certificados de CA na máquina dentro das lojas de raiz fidedigna e certificados intermédios. Esta funcionalidade deve ser utilizada se os seus serviços necessitarem de um certificado AC personalizado.

O artigo mostra como gerir certificados de CA de uma instância de serviço de gestão API Azure no portal Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Faça upload de um certificado de CA

![Adicionar certificados de CA](media/api-management-howto-ca-certificates/00.png)

Siga os passos abaixo para fazer o upload de um novo certificado de CA. Se ainda não criou uma instância de serviço de Gestão API, consulte o tutorial [Criar uma instância de serviço de Gestão API.](get-started-create-service-instance.md)

1. Navegue para a sua instância de serviço de Gestão AZure no portal Azure.

2. Selecione **certificados ca** do menu.

3. Clique no botão **+ Adicionar**.  

    ![Screenshot que mostra o botão + Adicionar para adicionar um certificado DEA.](media/api-management-howto-ca-certificates/01.png)  

4. Procure o certificado e decida sobre a loja de certificados. Apenas a chave pública é necessária, pelo que a palavra-passe não é necessária.

    ![Screenshot que mostra como navegar para o certificado.](media/api-management-howto-ca-certificates/02.png)  

5. Clique em **Guardar**. Esta operação poderá demorar alguns minutos.

    ![Screenshot que mostra como guardar o certificado.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Pode carregar um certificado de CA utilizando o `New-AzApiManagementSystemCertificate` comando Powershell.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Excluir um certificado de cliente

Para eliminar um certificado, clique no menu de **contexto...** e **selecione Eliminar** ao lado do certificado.

![Eliminar certificados ca](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a

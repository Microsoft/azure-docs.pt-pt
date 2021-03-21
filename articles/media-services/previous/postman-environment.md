---
title: Importe o ambiente do Carteiro para chamadas REST da Azure Media Services
description: Este tópico fornece uma definição do ambiente do Carteiro para chamadas Azure Media Services REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5b1705fb2b3b1b69f79158747827d764f1bef4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007851"
---
# <a name="import-the-postman-environment"></a>Importar o ambiente do Carteiro

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Este artigo contém uma definição das variáveis ambientais do **Carteiro** que são usadas na [coleção Do Carteiro](postman-collection.md) que contém pedidos HTTP agrupados que chamam APIs de Serviços de Mídia. O ambiente e os ficheiros de recolha são utilizados pelo [Carteiro Configurar para Serviços de Media REST API chama](media-rest-apis-with-postman.md) tutorial.

> [!NOTE]
> O valor `AzureADSTSEndpoint `  =  `https://login.microsoftonline.com/{{TenantId}}/oauth2/token` de. Para obter o seu ID do inquilino, pode pairar sobre o seu rato sobre o seu nome de utilizador no portal (no canto superior direito) e estará no "Diretório: Microsoft ({{TENANTID}} ) .

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```

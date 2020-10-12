---
title: Importe o ambiente do Carteiro para chamadas REST da Azure Media Services
description: Este tópico fornece uma definição do ambiente do Carteiro para chamadas Azure Media Services REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 2ccc765a3fb0063d92382edcec260808183f27a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259256"
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

---
title: Atualizar as credenciais de principal de serviço
description: Atualização credencial para um diretor de serviço
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669547"
---
# <a name="update-service-principal-credentials"></a>Atualizar as credenciais de principal de serviço

Quando as credenciais principais do serviço mudarem, é necessário atualizar os segredos no controlador de dados.

Por exemplo, se implementou o controlador de dados usando um conjunto específico de valores para iD do inquilino principal do serviço, ID do cliente e segredo do cliente, e depois alterar um ou mais destes valores, precisa atualizar os segredos no controlador de dados.  Seguem-se as instruções para atualizar o ID do Inquilino, o ID do Cliente ou o segredo do Cliente. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Fundo

O diretor de serviço foi criado na [Create service principal.](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal) 

## <a name="steps"></a>Passos

1. Aceda ao segredo principal do serviço no editor predefinido.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Por exemplo, para editar o segredo principal do serviço a um controlador de dados no `arc` espaço de nomes, executar o seguinte comando:

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   O `kubecl edit` comando abre as credenciais .ficheiro yml no editor predefinido. 


1. Edite o segredo principal do serviço. 

   No editor predefinido, substitua os valores na secção de dados pela informação de credencial atualizada.

   Por exemplo:

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   Editar os valores para `clientID` , `clientSecret` e/ou `tenantID` conforme apropriado. 

> [!NOTE]
>Os valores precisam de ser codificados de base64. Não edite quaisquer outras propriedades.

Se for fornecido um valor `clientId` incorreto, ou verá uma `clientSecret` `tenantID` mensagem de erro da seguinte forma nos registos do `control-xxxx` recipiente pod/controlador:

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Passos seguintes

[Obter o nome de utilizador e a palavra-passe para estabelecer ligação ao Controlador de Dados do Arc](retrieve-the-username-password-for-data-controller.md)

[Criar um principal de serviço](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)

---
author: baanders
description: incluir o ficheiro para a configuração da autenticação local para defaultAzureCredential em amostras Azure Digital Twins - com introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 56c63ee13c5e42350a2b544074a0b0527377ca4c
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494701"
---
### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

Esta amostra utiliza [o DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da `Azure.Identity` biblioteca) para autenticar os utilizadores com a instância Azure Digital Twins quando a executam na sua máquina local. Para obter mais informações sobre formas diferentes de uma aplicação de clientes poder autenticar com a Azure Digital Twins, consulte [*Como escrever código de autenticação de aplicações*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]
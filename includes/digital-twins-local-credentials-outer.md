---
author: baanders
description: incluir o ficheiro para a configuração da autenticação local para defaultAzureCredential em amostras Azure Digital Twins - com introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011272"
---
### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

Esta amostra utiliza [o DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da `Azure.Identity` biblioteca) para autenticar os utilizadores com a instância Azure Digital Twins quando a executam na sua máquina local. Para obter mais informações sobre diferentes formas de uma aplicação do cliente poder autenticar-se com a Azure Digital Twins, consulte [*Como escrever código de autenticação de aplicações*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]
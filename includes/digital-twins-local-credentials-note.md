---
author: baanders
description: incluir ficheiro para DefaultAzureCredential em amostras Azure Digital Twins - nota
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494495"
---
>[!NOTE]
> Esta amostra utiliza [o DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da `Azure.Identity` biblioteca) para autenticar os utilizadores com a instância Azure Digital Twins quando a executam na sua máquina local. Com este tipo de autenticação, a amostra procurará credenciais Azure dentro do seu ambiente local, como um login de um [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) local ou em Visual Studio/Visual Studio Code.
>
> Para obter mais informações sobre a utilização `DefaultAzureCredential` e outras opções de autenticação, consulte [*Como escrever o código de autenticação da aplicação*](../articles/digital-twins/how-to-authenticate-client.md).
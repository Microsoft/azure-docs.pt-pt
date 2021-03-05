---
author: baanders
description: incluir ficheiro para DefaultAzureCredential em amostras Azure Digital Twins - nota
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8aa3cbb2a037e49a694192c9852eeae0215c089c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211792"
---
>[!NOTE]
> Esta amostra utiliza [o DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da `Azure.Identity` biblioteca) para autenticar os utilizadores com a instância Azure Digital Twins quando a executam na sua máquina local. Com este tipo de autenticação, a amostra procurará credenciais Azure dentro do seu ambiente local, como um login de um [Azure CLI](/cli/azure/install-azure-cli) local ou em Visual Studio/Visual Studio Code.
>
> Para obter mais informações sobre a utilização `DefaultAzureCredential` e outras opções de autenticação, consulte [*Como escrever o código de autenticação da aplicação*](../articles/digital-twins/how-to-authenticate-client.md).
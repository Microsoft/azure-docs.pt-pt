---
author: baanders
description: incluir ficheiro para DefaultAzureCredential em amostras Azure Digital Twins - nota
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473861"
---
>[!NOTE]
> Esta amostra utiliza [o DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (parte da `Azure.Identity` biblioteca) para autenticar os utilizadores com a instância Azure Digital Twins quando a executam na sua máquina local. Com este tipo de autenticação, a amostra procurará credenciais Azure dentro do seu ambiente local, como um login de um [Azure CLI](/cli/azure/install-azure-cli) local ou em Visual Studio/Visual Studio Code.
>
> Para obter mais informações sobre a utilização `DefaultAzureCredential` e outras opções de autenticação, consulte [*Como escrever o código de autenticação da aplicação*](../articles/digital-twins/how-to-authenticate-client.md).
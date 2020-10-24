---
author: baanders
description: incluir o ficheiro para a configuração da autenticação local para defaultAzureCredential em amostras Azure Digital Twins - sem introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494638"
---
Com `DefaultAzureCredential` , a amostra procurará credenciais no seu ambiente local, como um login Azure em um [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) local ou em Visual Studio/Visual Studio Code. Isto significa que você deve **entrar em Azure localmente** através de um destes mecanismos para configurar credenciais para a amostra.

Se estiver a utilizar o Visual Studio ou o Visual Studio Code para executar a amostra de código, certifique-se de que está registado nesse editor com as mesmas credenciais Azure que pretende utilizar para aceder à sua instância Azure Digital Twins.

Caso contrário, pode [instalar o **Azure CLI**local,](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)iniciar um pedido de comando na sua máquina e executar o `az login` comando para iniciar sessão na sua conta Azure. Depois disto, quando executar a sua amostra de código, deve iniciar sessão automaticamente.
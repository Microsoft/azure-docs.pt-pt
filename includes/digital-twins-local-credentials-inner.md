---
author: baanders
description: incluir o ficheiro para a configuração da autenticação local para defaultAzureCredential em amostras Azure Digital Twins - sem introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011263"
---
Com `DefaultAzureCredential` , a amostra procurará credenciais no seu ambiente local, como um azure in in em um [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) local ou em Visual Studio ou Visual Studio Code. Por esta razão, deverá *inscrever-se localmente no Azure* através de um destes mecanismos para configurar credenciais para a amostra.

Se estiver a utilizar o Visual Studio ou o Visual Studio Code para executar a amostra de código, certifique-se de que está inscrito nesse editor com as mesmas credenciais Azure que pretende utilizar para aceder à sua instância Azure Digital Twins.

Caso contrário, pode [instalar o Azure CLI local,](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)iniciar um pedido de comando na sua máquina e executar o `az login` comando para iniciar seduca na sua conta Azure. Depois de iniciar sessão, quando executar a sua amostra de código, deve iniciar sessão automaticamente.
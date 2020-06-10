---
author: baanders
description: inclua o ficheiro para Azure Digital Twins - configurar a Cloud Shell e a extensão IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612894"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurar a sessão Cloud Shell

Depois de abrir uma janela Cloud Shell, a primeira coisa a fazer é iniciar sessão e definir o contexto da concha para a sua subscrição para esta sessão. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Se esta é a primeira vez que usa esta subscrição com a Azure Digital Twins, gere este comando para se registar no espaço de nomes Azure Digital Twins. (Se não tem certeza, não faz mal executá-lo novamente, mesmo que já o tenha feito algum tempo no passado.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Em seguida, executar o seguinte comando na sua instância Cloud Shell para adicionar a extensão IoT do Microsoft Azure para Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> Este artigo utiliza a versão mais recente da extensão Azure IoT, chamada `azure-iot` . A versão do legado chama-se `azure-iot-cli-ext` . Só deve ter uma versão instalada de cada vez. Pode utilizar o comando `az extension list` para validar as extensões atualmente instaladas.
> Utilize `az extension remove --name azure-cli-iot-ext` para remover a versão antiga da extensão.
> Utilize `az extension add --name azure-iot` para adicionar a nova versão da extensão. Para ver que extensões instalou, use `az extension list` .

> [!TIP]
> Você pode correr `az dt -h` para ver os comandos Azure Digital Twins de alto nível.
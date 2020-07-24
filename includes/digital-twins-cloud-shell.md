---
author: baanders
description: inclua o ficheiro para Azure Digital Twins - configurar a Cloud Shell e a extensão IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032238"
---
Para começar a trabalhar com a Azure Digital Twins numa janela aberta da [Azure Cloud Shell,](https://shell.azure.com) a primeira coisa a fazer é iniciar sessão e definir o contexto da concha para a sua subscrição para esta sessão. Executar estes comandos na sua Cloud Shell:

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Também pode usar o seu nome de subscrição em vez do ID no comando acima. 

Se esta é a primeira vez que usa esta subscrição com a Azure Digital Twins, gere este comando para se registar no espaço de nomes Azure Digital Twins. (Se não tem certeza, não faz mal executá-lo novamente, mesmo que já o tenha feito algum tempo no passado.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Em seguida, irá adicionar a [**extensão IoT do Microsoft Azure para O Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) à sua Cloud Shell, para permitir comandos para interagir com a Azure Digital Twins e outros serviços IoT. 

Primeiro, faça este comando para ver uma lista de todas as extensões que já instalou.

```azurecli-interactive
az extension list
```

A saída é um conjunto de todas as extensões que você tem atualmente. Procure o `"name"` campo para cada entrada na lista para ver os nomes das extensões.

Utilize a saída para determinar quais dos seguintes comandos a executar para a configuração da extensão (pode executar mais de um).
* Se a lista `azure-iot` contiver: Já tem a extensão. Execute este comando para se certificar de que tem a última atualização e não há mais atualizações disponíveis:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Se a lista **não** `azure-iot` contiver: É necessário instalar a extensão. Utilize este comando:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Se a lista `azure-iot-cli-ext` contiver: Esta é a versão antiga da extensão. Apenas uma versão da extensão deve ser instalada de cada vez, pelo que deve desinstalar a extensão do legado. Utilize este comando:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

Agora estás pronto para trabalhar com a Azure Digital Twins na Cloud Shell.

Pode verificar isto correndo `az dt -h` a qualquer momento para ver uma lista dos comandos Azure Digital Twins de alto nível que estão disponíveis.
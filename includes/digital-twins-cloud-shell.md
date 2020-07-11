---
author: baanders
description: inclua o ficheiro para Azure Digital Twins - configurar a Cloud Shell e a extensão IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277834"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurar a sessão Cloud Shell

Depois de abrir uma janela Cloud Shell, a primeira coisa a fazer é iniciar sessão e definir o contexto da concha para a sua subscrição para esta sessão. Executar estes comandos na sua Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> Também pode definir a sua subscrição usando o seu nome de subscrição. Utilize este comando: 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
Se esta é a primeira vez que usa esta subscrição com a Azure Digital Twins, gere este comando para se registar no espaço de nomes Azure Digital Twins. (Se não tem certeza, não faz mal executá-lo novamente, mesmo que já o tenha feito algum tempo no passado.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Em seguida, irá adicionar a [**extensão IoT do Microsoft Azure para O Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) à sua Cloud Shell, para permitir comandos para interagir com a Azure Digital Twins e outros serviços IoT. 

Primeiro, faça este comando para ver uma lista de todas as extensões que já instalou.

```azurecli-interactive
az extension list
```

Na saída, procure o `"name"` campo para cada entrada de lista para ver os nomes das extensões.

Utilize a saída para determinar quais dos seguintes comandos a executar para a configuração da extensão (pode executar mais de um).
* Se a lista `azure-iot` contiver: Já tem a extensão. Execute este comando para se certificar de que tem a última atualização:

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
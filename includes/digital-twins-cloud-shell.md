---
author: baanders
description: inclua o ficheiro para Azure Digital Twins - configurar a Cloud Shell e a extensão IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296972"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurar a sessão Cloud Shell

Depois de abrir uma janela Cloud Shell, a primeira coisa a fazer é iniciar sessão e definir o contexto da concha para a sua subscrição para esta sessão. Executar estes comandos na sua Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Se esta é a primeira vez que usa esta subscrição com a Azure Digital Twins, gere este comando para se registar no espaço de nomes Azure Digital Twins. (Se não tem certeza, não faz mal executá-lo novamente, mesmo que já o tenha feito algum tempo no passado.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Em seguida, irá adicionar a [**extensão IoT do Microsoft Azure para O Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) à sua Cloud Shell, para permitir comandos para interagir com a Azure Digital Twins e outros serviços IoT. Utilize este comando para adicionar a extensão:

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

Se já instalou a extensão no passado, a saída pode dizer "Extensão 'azure-iot' já está instalada." Se isto acontecer, execute o seguinte para se certificar de que tem a última atualização: 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

Agora estás pronto para trabalhar com a Azure Digital Twins na Cloud Shell.

Pode verificar isto correndo `az dt -h` a qualquer momento para ver uma lista dos comandos Azure Digital Twins de alto nível que estão disponíveis.
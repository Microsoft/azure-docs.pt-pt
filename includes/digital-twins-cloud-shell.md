---
author: baanders
description: inclua o ficheiro para Azure Digital Twins - configurar a Cloud Shell e a extensão IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b664303d86f8588fc210b11b363b21d523d63295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507655"
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

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

Agora estás pronto para trabalhar com a Azure Digital Twins na Cloud Shell.

Pode verificar isto correndo `az dt -h` a qualquer momento para ver uma lista dos comandos Azure Digital Twins de alto nível que estão disponíveis.
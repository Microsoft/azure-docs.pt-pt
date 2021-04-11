---
author: baanders
description: inclua o ficheiro para Azure Digital Twins - configurar a Cloud Shell e a extensão IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: d4d9efd99a60c93dbfef2d6f45971781d71e83fb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105069"
---
Para começar a trabalhar com a Azure Digital Twins numa janela aberta da [Azure Cloud Shell,](https://shell.azure.com) a primeira coisa a fazer é iniciar sessão e definir o contexto da concha para a sua subscrição para esta sessão. Executar estes comandos na sua Cloud Shell:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Também pode usar o seu nome de subscrição em vez do ID no comando acima. 

Se esta é a primeira vez que usa esta subscrição com a Azure Digital Twins, gere este comando para se registar no espaço de nomes Azure Digital Twins. (Se não tem certeza, não faz mal executá-lo novamente, mesmo que já o tenha feito algum tempo no passado.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Em seguida, irá adicionar a [**extensão IoT do Microsoft Azure para O Azure CLI**](/cli/azure/service-page/azure%20iot) à sua Cloud Shell, para permitir comandos para interagir com a Azure Digital Twins e outros serviços IoT. Executar este comando para se certificar de que tem a versão mais recente da extensão:

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

Agora estás pronto para trabalhar com a Azure Digital Twins na Cloud Shell.

Pode verificar isto correndo `az dt -h` a qualquer momento para ver uma lista dos comandos Azure Digital Twins de alto nível que estão disponíveis.
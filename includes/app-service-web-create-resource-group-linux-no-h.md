---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c70e2c166bee14ac58ee88bd18baf0cc61702767
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184733"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Na Cloud Shell, crie um grupo de recursos com o [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) comando. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup*, na localização *Europa Ocidental*. Para ver todas as localizações suportadas para o Serviço de Aplicações no Linux no escalão **Básico**, execute o comando [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Geralmente, o grupo de recursos e os recursos são criados numa região perto de si. 

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.
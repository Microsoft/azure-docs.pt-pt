---
title: Iniciar, parar e excluir seu aplicativo do Azure Spring Cloud | Microsoft Docs
description: Como iniciar, parar e excluir seu aplicativo do Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: ed3db5cfbdeb501e564a8da0020821c550a54787
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461630"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Iniciar, parar e excluir seu aplicativo do Azure Spring Cloud

Este guia explica como alterar o estado de um aplicativo no Azure Spring Cloud usando o portal do Azure ou o CLI do Azure.

## <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Depois de implantar um aplicativo, você pode iniciá-lo, pará-lo e excluí-lo usando o portal do Azure.

1. Vá para sua instância do serviço de nuvem do Azure Spring no portal do Azure.
1. Selecione a guia **painel do aplicativo** .
1. Selecione o aplicativo cujo estado você deseja alterar.
1. Na página **visão geral** do aplicativo, selecione **iniciar/parar**, **reiniciar**ou **excluir**.

## <a name="using-the-azure-cli"></a>Com a CLI do Azure

> [!NOTE]
> Você pode usar parâmetros opcionais e configurar padrões com o CLI do Azure. Saiba mais sobre o CLI do Azure lendo [nossa documentação de referência](spring-cloud-cli-reference.md).  

Primeiro, instale a extensão do Azure Spring Cloud para o CLI do Azure da seguinte maneira:

```azurecli
az extension add --name spring-cloud
```

Em seguida, selecione qualquer uma dessas operações de CLI do Azure:

* Para iniciar seu aplicativo:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para interromper o aplicativo:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para reiniciar o aplicativo:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para excluir seu aplicativo:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

---
title: Iniciar, parar e excluir seu aplicativo do Azure Spring Cloud | Microsoft Docs
description: Como iniciar, parar e excluir seu aplicativo do Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276824"
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

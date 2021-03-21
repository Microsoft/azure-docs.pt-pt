---
title: Iniciar, parar e eliminar a sua aplicação Azure Spring Cloud | Microsoft Docs
description: Como começar, parar e eliminar a sua aplicação Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 46d79de612a17c7b21e7cc51dcf4904209e1625d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212948"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Iniciar, parar e eliminar a sua aplicação Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Este guia explica como alterar o estado de uma aplicação em Azure Spring Cloud utilizando o portal Azure ou o Azure CLI.

## <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Depois de implementar uma aplicação, pode iniciar, parar e eliminá-la utilizando o portal Azure.

1. Aceda ao seu serviço Azure Spring Cloud no portal Azure.
1. Selecione o **separador 'Dashboard' de aplicação.**
1. Selecione a aplicação cujo estado pretende alterar.
1. Na página **'Visão Geral'** para essa aplicação, selecione **Iniciar/Parar,** **Reiniciar** ou **Eliminar**.

## <a name="using-the-azure-cli"></a>Com a CLI do Azure

> [!NOTE]
> Pode utilizar parâmetros opcionais e configurar predefinições com o Azure CLI. Saiba mais sobre o Azure CLI lendo a [nossa documentação de referência.](/cli/azure/ext/spring-cloud/spring-cloud)  

Primeiro, instale a extensão Azure Spring Cloud para o Azure CLI da seguinte forma:

```azurecli
az extension add --name spring-cloud
```

Em seguida, selecione qualquer uma destas operações do Azure CLI:

* Para iniciar a sua candidatura:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para parar a sua aplicação:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para reiniciar a sua aplicação:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para eliminar a sua aplicação:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

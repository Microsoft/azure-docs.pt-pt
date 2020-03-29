---
title: Inicie, pare e elimine a sua aplicação Azure Spring Cloud [ Microsoft Docs
description: Como iniciar, parar e apagar a sua aplicação Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276824"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Inicie, pare e elimine a sua aplicação Azure Spring Cloud

Este guia explica como alterar o estado de uma aplicação em Azure Spring Cloud utilizando o portal Azure ou o Azure CLI.

## <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Depois de implementar uma aplicação, pode iniciar, parar e eliminá-la utilizando o portal Azure.

1. Vá à sua instância de serviço Azure Spring Cloud no portal Azure.
1. Selecione o separador Painel de **Aplicação.**
1. Selecione a aplicação cujo estado pretende alterar.
1. Na página **'Visão Geral'** para essa aplicação, selecione **Start/Stop,** **Restart**, or **Delete**.

## <a name="using-the-azure-cli"></a>Com a CLI do Azure

> [!NOTE]
> Pode utilizar parâmetros opcionais e configurar os predefinições com o CLI Azure. Saiba mais sobre o Azure CLI lendo a [nossa documentação de referência.](spring-cloud-cli-reference.md)  

Em primeiro lugar, instale a extensão azure Spring Cloud para o CLI Azure da seguinte forma:

```azurecli
az extension add --name spring-cloud
```

Em seguida, selecione qualquer uma destas operações azure CLI:

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

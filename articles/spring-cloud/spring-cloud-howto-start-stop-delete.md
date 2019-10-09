---
title: Como iniciar, parar e excluir seu aplicativo do Azure Spring Cloud | Microsoft Docs
description: Como iniciar, parar e excluir seu aplicativo do Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039085"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Como iniciar, parar e excluir seu aplicativo do Azure Spring Cloud

Este guia explica como alterar o estado de um aplicativo no Azure Spring Cloud usando o portal do Azure ou a CLI.

## <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Depois de ter um aplicativo implantado, você pode **iniciá**-lo, **pará**-lo e **excluí** -lo usando o portal do Azure.

1. Vá para sua instância do serviço de nuvem do Azure Spring no portal do Azure.
1. Selecione a guia **painel do aplicativo** .
1. Selecione o aplicativo cujo estado você deseja alterar.
2. Na página **visão geral** do aplicativo, encontre os botões para **iniciar/parar**, **reiniciar**e **excluir** o aplicativo.

## <a name="using-the-azure-cli"></a>Com a CLI do Azure

> [!NOTE]
> Você pode usar parâmetros opcionais e configurar padrões com o CLI do Azure. Saiba mais sobre como ler nossa [documentação de referência](spring-cloud-cli-reference.md).

* Para iniciar seu aplicativo:
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para interromper o aplicativo:
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para reiniciar o aplicativo:
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para excluir seu aplicativo:
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

---
title: Como iniciar, parar e excluir seu aplicativo do Azure Spring Cloud | Microsoft Docs
description: Como iniciar, parar e excluir seu aplicativo do Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607729"
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

Instale a extensão Spring Cloud para o CLI do Azure:

```azurecli
az extension add --name spring-cloud
```

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

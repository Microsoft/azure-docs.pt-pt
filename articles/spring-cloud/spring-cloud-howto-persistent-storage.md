---
title: Como utilizar o armazenamento persistente na Nuvem de primavera de Azure Microsoft Docs
description: Como usar armazenamento persistente em Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9d769cb6e2cc33df259da46a82ad27a8bfd9e74b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888518"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Utilizar o armazenamento persistente no Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

A Azure Spring Cloud fornece dois tipos de armazenamento para a sua aplicação: persistente e temporária.

Por padrão, a Azure Spring Cloud fornece armazenamento temporário para cada instância de aplicação. O armazenamento temporário é limitado a 5 GB por instância com o caminho de montagem predefinido /tmp.

> [!WARNING]
> Se reiniciar uma instância de aplicação, o armazenamento temporário associado é permanentemente eliminado.

O armazenamento persistente é um recipiente de partilha de ficheiros gerido pela Azure e atribuído por pedido. Os dados armazenados em armazenamento persistente são partilhados por todos os casos de uma aplicação. Uma instância Azure Spring Cloud pode ter um máximo de 10 aplicações com armazenamento persistente ativado. Cada aplicação é atribuída a 50 GB de armazenamento persistente. O caminho de montagem predefinido para o armazenamento persistente é /persistente.

> [!WARNING]
> Se desativar o armazenamento persistente de uma aplicação, todo esse armazenamento é transabilitado e todos os dados armazenados são perdidos.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Utilize o portal Azure para permitir o armazenamento persistente

1. Na página **inicial** do seu portal Azure, selecione **Todos os Recursos**.

    >![Localize o ícone de Todos os Recursos](media/portal-all-resources.jpg)

1. Selecione o recurso Azure Spring Cloud que necessita de armazenamento persistente. Neste exemplo, a aplicação selecionada é chamada **de mola.**

    > ![Selecione a sua aplicação](media/select-service.jpg)

1. No título **Definições,** selecione **Apps**.

1. Os seus serviços Azure Spring Cloud aparecem numa mesa.  Selecione o serviço ao qual pretende adicionar armazenamento persistente. Neste exemplo, o serviço **gateway** é selecionado.

    > ![Selecione o seu serviço](media/select-gateway.jpg)

1. A partir da página de configuração do serviço, selecione **Configuração**

1. Selecione o **separador Armazenamento Persistente** e selecione **Ative**.

    > ![Permitir armazenamento persistente](media/enable-persistent-storage.jpg)

Após o armazenamento persistente ser ativado, o seu tamanho e caminho são mostrados na página de configuração.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Utilize o CLI Azure para modificar o armazenamento persistente

Se necessário, instale a extensão da Nuvem de primavera para o CLI Azure:

```azurecli
az extension add --name spring-cloud
```
Outras operações:

* Para criar uma app com armazenamento persistente habilitado:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para permitir o armazenamento persistente para uma aplicação existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para desativar o armazenamento persistente numa aplicação existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Se desativar o armazenamento persistente de uma aplicação, todo esse armazenamento é transabilitado e todos os dados armazenados são permanentemente perdidos.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as quotas de aplicação e serviço.](spring-cloud-quotas.md)
* Saiba como escalar manualmente a [sua aplicação](spring-cloud-tutorial-scale-manual.md).

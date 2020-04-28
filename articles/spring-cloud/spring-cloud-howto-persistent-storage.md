---
title: Como utilizar o armazenamento persistente na Nuvem de primavera do Azure [ Microsoft Docs
description: Como usar o armazenamento persistente na Nuvem de primavera Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 0e49d59386b19aa8da46b8c8e6acfe50e2124541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278541"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Utilizar o armazenamento persistente no Azure Spring Cloud

A Nuvem de primavera Azure fornece dois tipos de armazenamento para a sua aplicação: persistente e temporária.

Por padrão, a Nuvem de primavera Azure fornece armazenamento temporário para cada instância de aplicação. O armazenamento temporário é limitado a 5 GB por instância com o caminho de montagem padrão /tmp.

> [!WARNING]
> Se reiniciar uma instância de candidatura, o armazenamento temporário associado é permanentemente eliminado.

O armazenamento persistente é um recipiente de partilha de ficheiros gerido pelo Azure e atribuído por aplicação. Os dados armazenados em armazenamento persistente são partilhados por todos os casos de uma aplicação. Uma instância azure Spring Cloud pode ter um máximo de 10 aplicações com armazenamento persistente ativado. Cada aplicação é atribuída a 50 GB de armazenamento persistente. O caminho de montagem padrão para armazenamento persistente é /persistente.

> [!WARNING]
> Se desativar o armazenamento persistente de uma aplicação, todo esse armazenamento é transferido e todos os dados armazenados são perdidos.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Utilize o portal Azure para permitir o armazenamento persistente

1. A partir da página **inicial** do seu portal Azure, selecione **Todos os Recursos.**

    >![Localizar o ícone de Todos os Recursos](media/portal-all-resources.jpg)

1. Selecione o recurso Azure Spring Cloud que necessita de armazenamento persistente. Neste exemplo, a aplicação selecionada é chamada **de mola**.

    > ![Selecione a sua aplicação](media/select-service.jpg)

1. Na rubrica **Definições,** selecione **Apps**.

1. Os seus serviços azure Spring Cloud aparecem numa mesa.  Selecione o serviço ao qual pretende adicionar armazenamento persistente. Neste exemplo, o serviço **gateway** é selecionado.

    > ![Selecione o seu serviço](media/select-gateway.jpg)

1. A partir da página de configuração do serviço, **selecione Configuração**

1. Selecione o separador **de armazenamento persistente** e selecione **'Ativar**'.

    > ![Permitir armazenamento persistente](media/enable-persistent-storage.jpg)

Após o armazenamento persistente ser ativado, o seu tamanho e caminho são mostrados na página de configuração.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Utilize o Azure CLI para modificar o armazenamento persistente

Se necessário, instale a extensão da Nuvem de Mola para o ClI Azure:

```azurecli
az extension add --name spring-cloud
```
Outras operações:

* Para criar uma aplicação com armazenamento persistente ativado:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para permitir o armazenamento persistente de uma aplicação existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para desativar o armazenamento persistente numa aplicação existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Se desativar o armazenamento persistente de uma aplicação, todo esse armazenamento é transferido e todos os dados armazenados são permanentemente perdidos.

## <a name="next-steps"></a>Passos seguintes

* Conheça as quotas de [aplicação e serviço.](spring-cloud-quotas.md)
* Aprenda a escalar manualmente a [sua aplicação.](spring-cloud-tutorial-scale-manual.md)

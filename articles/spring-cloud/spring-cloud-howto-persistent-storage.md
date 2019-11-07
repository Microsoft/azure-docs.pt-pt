---
title: Como usar o armazenamento persistente no Azure Spring Cloud | Microsoft Docs
description: Como usar o armazenamento persistente no Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: d70e7ff747b80b661e848f1c208f0d1c2c928248
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607774"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Como usar o armazenamento persistente no Azure Spring Cloud

O Azure Spring Cloud fornece dois tipos de armazenamento para seu aplicativo: persistente e temporário.  O Azure Spring Cloud habilita o armazenamento temporário por padrão para cada instância do aplicativo. O armazenamento temporário é limitado a 5 GB com um caminho de montagem padrão: `/tmp`.

> [!WARNING]
> A reinicialização de uma instância do aplicativo excluirá permanentemente seu armazenamento temporário associado.

O armazenamento persistente é um contêiner de compartilhamento de arquivos gerenciado pelo Azure alocado por aplicativo. Os dados armazenados no armazenamento persistente são compartilhados entre todas as instâncias do aplicativo. Uma instância do serviço de nuvem do Azure Spring pode ter um máximo de 10 aplicativos com disco persistente habilitado. Cada aplicativo recebe 50 GB de armazenamento persistente. O caminho de montagem padrão para o armazenamento persistente é `/persistent`.

> [!WARNING]
> *Desabilitar* o armazenamento persistente irá desalocar o armazenamento para esse aplicativo.  Todos os dados na conta de armazenamento serão perdidos. 

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Habilitar o armazenamento persistente usando o portal do Azure

1. Na tela inicial do seu portal do Azure, selecione **todos os recursos**.

     >![Localize o ícone todos os recursos](media/portal-all-resources.jpg)

1. Localize e selecione o recurso Azure Spring Cloud que precisa de armazenamento persistente.  Neste exemplo, o aplicativo é chamado de *jpspring*.

    > ![Localize seu applicationb](media/select-service.jpg)

1. No título **configurações** , selecione **aplicativos**.

1. Seus serviços de nuvem Spring serão exibidos na tabela.  Selecione o serviço ao qual você deseja adicionar o armazenamento persistente.  Neste exemplo, selecionaremos nosso serviço de **Gateway** .

    > ![Selecione seu serviço](media/select-gateway.jpg)

1. Na folha de configuração do serviço, selecione **configuração**

1. Selecione a guia **armazenamento persistente** e habilite o armazenamento persistente.

    > ![Habilitar armazenamento persistente](media/enable-persistent-storage.jpg)

Quando o armazenamento persistente está habilitado, seu tamanho e caminho são mostrados nessa página.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Usar o CLI do Azure para modificar o armazenamento persistente

Se necessário, instale a extensão Spring Cloud para o CLI do Azure:

```azurecli
az extension add --name spring-cloud
```

Criar um aplicativo com o disco persistente habilitado:
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Habilitar o armazenamento persistente em um aplicativo existente:

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
``` 

Desabilitar o armazenamento persistente em um aplicativo existente:

> [!WARNING]
> Desabilitar o armazenamento persistente desalocará o armazenamento para esse aplicativo, perdendo permanentemente todos os dados que foram armazenados lá. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as [cotas de aplicativo e serviço](spring-cloud-quotas.md)ou saiba como [dimensionar manualmente seu aplicativo](spring-cloud-tutorial-scale-manual.md).
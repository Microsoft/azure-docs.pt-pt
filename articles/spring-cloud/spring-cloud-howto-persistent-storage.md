---
title: Como usar o armazenamento persistente no Azure Spring Cloud | Microsoft Docs
description: Como usar o armazenamento persistente no Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039092"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Como usar o armazenamento persistente no Azure Spring Cloud

O Azure Spring Cloud fornece dois tipos de armazenamento para seu aplicativo: persistente e temporário.  O Azure Spring Cloud habilita o armazenamento temporário por padrão para cada instância do aplicativo. O armazenamento temporário é limitado a 5 GB e seu caminho de montagem padrão é `/tmp`.

> [!WARNING]
> A reinicialização de uma instância do aplicativo excluirá seu armazenamento temporário associado permanentemente.

O armazenamento persistente é um contêiner de compartilhamento de arquivos gerenciado pelo Azure que é alocado em um escopo por aplicativo. Os dados armazenados no armazenamento persistente são compartilhados entre todas as instâncias do aplicativo. Uma instância do serviço de nuvem do Azure Spring pode ter um máximo de 10 aplicativos com disco persistente habilitado e cada aplicativo recebe 50 GB de armazenamento persistente. O caminho de montagem padrão para o armazenamento persistente é `/persistent`.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Habilitar o armazenamento persistente usando o portal do Azure

1. Na página do serviço de nuvem do Azure Spring, selecione **painel do aplicativo**e, em seguida, selecione o aplicativo que requer armazenamento persistente.
1. Na guia **visão geral** , localize o atributo **armazenamento persistente** e selecione **desabilitado**.
1. Clique em **habilitar** para habilitar o armazenamento persistente e selecione o botão **OK** para aplicar a alteração.

Quando o armazenamento persistente está habilitado, seu tamanho e caminho são mostrados no atributo **armazenamento persistente** da página **visão geral** .

> [!WARNING]
> *Desabilitar* o armazenamento persistente irá desalocar o armazenamento para esse aplicativo.  Todos os dados na conta de armazenamento serão perdidos. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Habilitar o armazenamento persistente usando o CLI do Azure

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
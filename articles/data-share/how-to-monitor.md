---
title: Como monitorizar a Partilha de Dados do Azure
description: Saiba como monitorizar o estado dos convites, partilhar subscrições e histórico de instantâneos em Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490511"
---
# <a name="monitor-azure-data-share"></a>Monitorizar o Azure Data Share  

Este artigo explica como pode monitorizar as suas partilhas de dados utilizando a Azure Data Share. Como fornecedor de dados, é capaz de monitorizar vários aspetos das suas relações de partilha de dados. Detalhes como se os seus consumidores de dados aceitaram o seu convite para a partilha de dados, bem como se eles criaram uma subscrição de ação e começaram a usar os seus dados estão todos disponíveis para monitorizar. 

Como consumidor de dados, pode monitorizar as imagens que foram desencadeadas na sua subscrição do Azure. 

## <a name="monitor-invitation-status"></a>Monitorizar o estado do convite

Veja o estado dos seus convites de partilha de dados navegando para as ações enviadas -> Convites. 

![Estatuto do convite](./media/invitation-status.png "Estatuto do convite") 

Há três estados em que o seu convite pode estar em:

* Pendente - Destinatário de partilha de dados ainda não aceitou o convite.
* Aceite - Destinatário de partilha de dados aceitou o convite.
* Rejeitado - O destinatário da partilha de dados rejeitou o convite.

> [!IMPORTANT]
> Se apagar um convite depois de já ter sido aceite, não é equivalente a revogar o acesso. Se quiser impedir que futuras fotos sejam copiadas na sua conta de armazenamento de dados, deve revogar o acesso através do separador *de subscrições Share.* 

## <a name="monitor-share-subscriptions"></a>Monitorizar subscrições de ações

Veja o estado das suas subscrições de ações navegando para Subscrições de Partilha seleções enviadas ->. Isto irá dar-lhe detalhes sobre subscrições ativas criadas pelos seus consumidores de dados depois de aceitar o seu convite. Pode parar futuras atualizações ao seu consumidor de dados selecionando a subscrição de ações e selecionando *Revogar*. 

## <a name="snapshot-history"></a>História instantânea 

No separador de história, você é capaz de ver as fotos que foram copiadas para o inquilino do seu consumidor de dados. É possível monitorizar a frequência e duração de cada intervalo instantâneo. 

![História instantânea](./media/sent-shares.png "História instantânea") 

Pode ver mais detalhes sobre cada instantâneo executado clicando na data de início da execução. 

Até 30 dias de história instantânea é exibido por padrão. Se precisar de ver mais de 30 dias de história, navegue para monitorizar -> Definições de diagnóstico e **selecione Adicionar definição**de diagnóstico . Será necessário selecionar uma conta de armazenamento para armazenar estes registos. 

![História instantânea](./media/diagnostic-settings.png "Definições de diagnóstico") 

## <a name="next-steps"></a>Passos Seguintes 

Saiba mais sobre a [terminologia](terminology.md) da Partilha de Dados do Azure
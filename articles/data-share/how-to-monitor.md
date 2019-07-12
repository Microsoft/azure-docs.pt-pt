---
title: Como monitorizar a pré-visualização de partilha de dados do Azure
description: Como monitorizar a pré-visualização de partilha de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789101"
---
# <a name="monitor-azure-data-share-preview"></a>Visualização de partilha de dados do Azure monitor 

Este artigo explica como pode monitorizar as partilhas de dados com a pré-visualização de partilha de dados do Azure. Como um provedor de dados, é capaz de monitorar vários aspectos de seus relacionamentos de partilha de dados. Detalhes de como se os consumidores de dados aceitou o convite para a partilha de dados, bem como se tiver criado uma subscrição de partilha e começou a utilizar os seus dados estão disponíveis para monitorizar. 

Como um consumidor de dados, pode monitorizar os instantâneos que foram acionados na sua subscrição do Azure. 

## <a name="monitor-invitation-status"></a>Monitorizar o estado de convite

Visualizar o estado de sua convites de partilha de dados ao navegar para partilhas de enviados -> convites. 

![Estado de convite](./media/invitation-status.png "estado de convite") 

Existem três Estados em que o convite pode estar:

* Pendente - destinatário de partilha de dados ainda não aceitou o convite.
* Aceite - destinatário de partilha de dados já aceitou o convite.
* Rejeitado - destinatário de partilha de dados rejeitou o convite.

> [!IMPORTANT]
> Se eliminar um convite depois de já ter sido aceite, não é equivalente a revogar o acesso. Se quiser parar instantâneos futuros de ser copiado para a sua conta de armazenamento de consumidores de dados, tem de revogar o acesso através da *Compartilhe assinaturas* separador. 

## <a name="monitor-share-subscriptions"></a>Subscrições de partilha do monitor

Ver o estado das suas subscrições de partilha ao aceder a partilhas enviados -> subscrições de partilhar. Isso fornece detalhes sobre as subscrições ativas criadas por seus consumidores de dados depois de aceitar o convite. Pode parar as futuras atualizações para o consumidor de dados ao selecionar a subscrição de partilha e selecionando *revogar*. 

## <a name="snapshot-history"></a>Histórico de instantâneo 

No separador de histórico, é possível ver os instantâneos que tenham sido copiados no inquilino do consumidor de dados. É possível monitorizar a frequência e duração de cada intervalo de instantâneos. 

![Histórico de instantâneo](./media/sent-shares.png "histórico de instantâneo") 

Pode ver mais detalhes sobre cada instantâneo executar clicando na data de início da execução. 

## <a name="next-steps"></a>Próximos Passos 

Saiba mais sobre [terminologia de partilha de dados do Azure](terminology.md)
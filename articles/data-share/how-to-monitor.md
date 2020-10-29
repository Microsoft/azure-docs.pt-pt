---
title: Como monitorizar a partilha de dados do Azure
description: Saiba como monitorizar o estado do convite, partilhar subscrições e fazer snapshot no Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910479"
---
# <a name="monitor-azure-data-share"></a>Monitorizar o Azure Data Share  

Este artigo explica como pode monitorizar as suas partilhas de dados usando a Azure Data Share. Como fornecedor de dados, é capaz de monitorizar vários aspetos das suas relações de partilha de dados. Detalhes como se os seus dados os consumidores aceitaram o seu convite para a partilha de dados, bem como se criaram uma subscrição de ações e começaram a usar os seus dados estão todos disponíveis para monitorizar. 

Como consumidor de dados, pode monitorizar as imagens que foram ativadas na sua subscrição do Azure. 

## <a name="monitor-invitation-status"></a>Monitorizar o estado do convite

Consulte o estado dos seus convites de partilha de dados navegando para Envio de ações -> Convites. 

![Estatuto do convite](./media/invitation-status.png "Estatuto do convite") 

Há três estados em que o seu convite pode ser:

* Pendente - O destinatário da partilha de dados ainda não aceitou o convite.
* Aceite - O destinatário da partilha de dados aceitou o convite.
* Rejeitado - O destinatário da partilha de dados rejeitou o convite.

> [!IMPORTANT]
> Se apagar um convite depois de já ter sido aceite, não equivale a revogar o acesso. Se quiser impedir que futuras imagens de instantâneos sejam copiadas na sua conta de armazenamento de consumidores de dados, tem de revogar o acesso através do *separador de subscrições Share.* 

## <a name="monitor-share-subscriptions"></a>Monitorizar subscrições de partilha

Ver o estado das suas subscrições de ações navegando para As Ações Enviadas -> Subscrições de ações. Isto irá dar-lhe detalhes sobre subscrições ativas criadas pelos seus consumidores de dados depois de aceitar o seu convite. Pode parar futuras atualizações ao seu consumidor de dados selecionando a subscrição de ações e selecionando *a Revoke.* 

## <a name="snapshot-history"></a>História do instantâneo 

No **separador Histórico** de uma ação, é possível visualizar quando os dados são copiados do fornecedor de dados para a loja de dados do consumidor. É capaz de monitorizar a frequência, duração e estado de cada instantâneo. 

![Screenshot mostra ações enviadas no portal Azure.](./media/sent-shares.png "História do instantâneo") 

Pode ver mais detalhes sobre cada instantâneo executado clicando na data de início da execução. Em seguida, clique no estado de cada conjunto de dados para ver a quantidade de dados transferidos, número de ficheiros/registos copiados, duração do instantâneo, número de vCores utilizados e mensagem de erro se houver algum. 

São apresentados até 30 dias de história instantânea. Se precisar de poupar e ver mais de 30 dias de história, pode aproveitar a definição de diagnóstico.

## <a name="diagnostic-setting"></a>Definição de diagnóstico

Pode configurar a definição de diagnóstico para guardar dados ou eventos de registo. Navegue para monitorizar as definições de diagnóstico de > e selecione **Adicionar a definição de diagnóstico** . Selecione os dados de registo ou eventos em que está interessado e para onde pretende armazenar ou enviar. 

![A screenshot mostra a página de definições de Diagnóstico no portal Azure.](./media/diagnostic-settings.png "Definições de diagnóstico") 

## <a name="next-steps"></a>Passos Seguintes 

Saiba mais sobre [a terminologia Azure Data Share](terminology.md)
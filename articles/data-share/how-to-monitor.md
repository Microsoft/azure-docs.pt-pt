---
title: Como monitorar o compartilhamento de dados do Azure
description: Saiba como monitorar o status do convite, assinaturas de compartilhamento e histórico de instantâneos no compartilhamento de dados do Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490511"
---
# <a name="monitor-azure-data-share"></a>Monitorizar o Azure Data Share  

Este artigo explica como você pode monitorar seus compartilhamentos de dados usando o compartilhamento de dados do Azure. Como um provedor de dados, você pode monitorar vários aspectos de suas relações de compartilhamento de dados. Detalhes, como se os consumidores de dados tiverem aceitado seu convite para o compartilhamento de dados, bem como se eles criaram uma assinatura de compartilhamento e começarem a usar seus dados estão disponíveis para monitoramento. 

Como um consumidor de dados, você pode monitorar os instantâneos que foram disparados em sua assinatura do Azure. 

## <a name="monitor-invitation-status"></a>Monitorar o status do convite

Exiba o status de seus convites de compartilhamento de dados navegando para compartilhamentos enviados-> convites. 

![Status do convite](./media/invitation-status.png "Status do convite") 

Há três Estados em que seu convite pode estar:

* O destinatário de compartilhamento de dados pendentes ainda não aceitou o convite.
* Aceito-o destinatário do compartilhamento de dados aceitou o convite.
* Rejeitado-o destinatário do compartilhamento de dados rejeitou o convite.

> [!IMPORTANT]
> Se você excluir um convite depois que ele já tiver sido aceito, não será equivalente a revogar o acesso. Se você deseja impedir que instantâneos futuros sejam copiados para sua conta de armazenamento de consumidores de dados, você deve revogar o acesso por meio da guia *assinaturas de compartilhamento* . 

## <a name="monitor-share-subscriptions"></a>Monitorar assinaturas de compartilhamento

Exiba o status de suas assinaturas de compartilhamento navegando para compartilhamentos enviados-> compartilhar assinaturas. Isso fornecerá detalhes sobre as assinaturas ativas criadas por seus consumidores de dados depois de aceitar seu convite. Você pode parar as atualizações futuras para o consumidor de dados selecionando a assinatura de compartilhamento e selecionando *revogar*. 

## <a name="snapshot-history"></a>Histórico de instantâneos 

Na guia histórico, você pode exibir os instantâneos que foram copiados para o locatário do consumidor de dados. Você pode monitorar a frequência e a duração de cada intervalo de instantâneo. 

![Histórico de instantâneos](./media/sent-shares.png "Histórico de instantâneos") 

Você pode exibir mais detalhes sobre cada execução de instantâneo clicando na data de início da execução. 

Por padrão, até 30 dias de histórico de instantâneos são exibidos. Se você precisar ver mais de 30 dias de histórico, navegue até monitoramento-> configurações de diagnóstico e selecione **Adicionar configuração de diagnóstico**. Você será solicitado a selecionar uma conta de armazenamento na qual armazenar esses logs. 

![Histórico de instantâneos](./media/diagnostic-settings.png "Definições de diagnóstico") 

## <a name="next-steps"></a>Próximos Passos 

Saiba mais sobre a [terminologia do compartilhamento de dados do Azure](terminology.md)
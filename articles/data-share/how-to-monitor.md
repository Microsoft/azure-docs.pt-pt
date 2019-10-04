---
title: Como monitorar a visualização do compartilhamento de dados do Azure
description: Como monitorar a visualização do compartilhamento de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: fbc6847dd86b7687d477b2bae0deab1389dc8491
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827066"
---
# <a name="monitor-azure-data-share-preview"></a>Monitorar a visualização do compartilhamento de dados do Azure 

Este artigo explica como você pode monitorar seus compartilhamentos de dados usando a visualização do compartilhamento de dados do Azure. Como um provedor de dados, você pode monitorar vários aspectos de suas relações de compartilhamento de dados. Detalhes, como se os consumidores de dados tiverem aceitado seu convite para o compartilhamento de dados, bem como se eles criaram uma assinatura de compartilhamento e começarem a usar seus dados estão disponíveis para monitoramento. 

Como um consumidor de dados, você pode monitorar os instantâneos que foram disparados em sua assinatura do Azure. 

## <a name="monitor-invitation-status"></a>Monitorar o status do convite

Exiba o status de seus convites de compartilhamento de dados navegando para compartilhamentos enviados-> convites. 

![](./media/invitation-status.png "Status do convite") para status do convite 

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

(./media/sent-shares.png "Histórico de instantâneos") do ![histórico de instantâneos] 

Você pode exibir mais detalhes sobre cada execução de instantâneo clicando na data de início da execução. 

Por padrão, até 30 dias de histórico de instantâneos são exibidos. Se você precisar ver mais de 30 dias de histórico, navegue até monitoramento-> configurações de diagnóstico e selecione **Adicionar configuração de diagnóstico**. Você será solicitado a selecionar uma conta de armazenamento na qual armazenar esses logs. 

(./media/diagnostic-settings.png "Configurações de diagnóstico") do ![histórico de instantâneos] 

## <a name="next-steps"></a>Próximos Passos 

Saiba mais sobre a [terminologia do compartilhamento de dados do Azure](terminology.md)
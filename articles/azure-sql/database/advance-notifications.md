---
title: Notificações antecipadas (Pré-visualização) para eventos de manutenção planeados
description: Receba a notificação antes da manutenção planeada para a Base de Dados Azure SQL ou para a Azure SQL Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 3f63a8d2f75a58a771d837128c2b2498c28948ef
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663619"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Notificações antecipadas para eventos de manutenção planeados (Pré-visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Notificações antecipadas (Pré-visualização), estão disponíveis para bases de dados configuradas para [Janela de Manutenção (Pré-visualização)](maintenance-window.md). As notificações antecipadas permitem aos clientes configurar notificações até 24 horas antes de qualquer evento planeado.

As notificações podem ser configuradas para que possa receber mensagens, e-mails, notificações push Azure e mensagens de voz quando a manutenção planeada começar nas próximas 24 horas. Notificações adicionais são enviadas quando a manutenção começa e quando a manutenção termina.


## <a name="create-an-advance-notification"></a>Criar uma notificação antecipada

Estão disponíveis notificações antecipadas para bases de dados Azure SQL e casos geridos que tenham a sua janela de manutenção configurada. 

Complete os seguintes passos para permitir uma notificação.  

1. Vá à página [de manutenção Planeada,](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) selecione **alertas de saúde,** em **seguida, Adicione o alerta de saúde do serviço**.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="criar uma nova opção de menu alerta de saúde":::

2. Na secção **Ações,** **selecione Adicionar grupos de ação**. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="adicionar uma opção de menu de grupo de ação":::

3. Preencha o formulário **de grupo de ação Criar** e, em seguida, selecione **Seguinte: Notificações**.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="criar forma de grupo de ação":::

1. No **separador Notificações,** selecione o **tipo de Notificação**. A opção **email/SMS/Push/Voice** oferece a maior flexibilidade e é a opção recomendada. Selecione a caneta para configurar a notificação.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="configure notificações":::



   1. Preencha o formulário *de notificação adicionar ou editar* que abre e selecione **OK**: 

   2. As ações e tags são opcionais. Aqui pode configurar ações adicionais para serem desencadeadas ou usar tags para categorizar e organizar os seus recursos Azure. 

   4. Consulte os detalhes no **separador 'Avaliação+' criar** e selecionar **Criar.** 

7. Após a seleção de criar, o ecrã de configuração da regra de alerta abre e o grupo de ação será selecionado. Dê um nome à sua nova regra de alerta, em seguida, escolha o grupo de recursos para ela e selecione **Criar a regra de alerta**. 

8. Clique novamente no menu **de alertas Saúde** e a lista de alertas contém agora o seu novo alerta. 


Está tudo pronto. Da próxima vez que houver um evento de manutenção Azure SQL planeado, receberá uma notificação antecipada.

## <a name="receiving-notifications"></a>Receber notificações

A tabela a seguir mostra as notificações de informação geral que pode receber: 

|Estado|Descrição|
|:---|:---|
|**Implantação planeada**| Recebido 24 horas antes do evento de manutenção. A manutenção está prevista para a DATA entre as 17h e as 8h (hora local) para o DB xyz.|
|**Em curso** | A manutenção da base de dados *xyz*   está a começar.| 
|**Concluído** | A manutenção da base de dados *xyz* está completa. |

O quadro que se segue apresenta notificações adicionais que podem ser enviadas enquanto a manutenção está em curso: 

|Estado|Descrição|
|:---|:---|
|**Extended** | A manutenção está em andamento, mas não foi concluída para a base de dados *xyz.* A manutenção continuará na próxima janela de manutenção.| 
|**Cancelado**| A manutenção da base de dados *xyz* é cancelada e será reagendada mais tarde. |
|**Bloqueado**|Houve um problema durante a manutenção da base de dados *xyz.* Vamos notificá-lo quando retomarmos.| 
|**Retomado**|O problema foi resolvido e a manutenção continuará na próxima janela de manutenção.|


## <a name="next-steps"></a>Passos seguintes

- [Janela de manutenção](maintenance-window.md)
- [FaQ da janela de manutenção](maintenance-window-faq.yml)
- [Descrição geral dos alertas no Microsoft Azure](../../azure-monitor/platform/alerts-overview.md)
- [Enviar e-mail a Função do Azure Resource Manager](../../azure-monitor/platform/action-groups.md#email-azure-resource-manager-role)
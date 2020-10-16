---
title: Como substituir a sua infraestrutura de fita
description: Saiba como o Azure Backup fornece semântica semelhante a fita que lhe permite fazer backup e restaurar dados em Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 695cc2644521384527ecd871f3613a078e987aa7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378445"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Mova o seu armazenamento a longo prazo da fita para a nuvem Azure

Os clientes Azure Backup e System Center Data Protection Manager podem:

* Ressalta os dados em horários que melhor se adequam às necessidades organizacionais.
* Conservar os dados de backup por períodos mais longos.
* Faça do Azure uma parte das suas necessidades de retenção a longo prazo (em vez de fita adesiva).

Este artigo explica como os clientes podem ativar políticas de backup e retenção. Os clientes que utilizam fitas para responder às suas necessidades de retenção a longo prazo têm agora uma alternativa poderosa e viável com a disponibilidade desta funcionalidade. A funcionalidade está ativada no mais recente lançamento do Azure Backup (que está disponível [aqui).](https://aka.ms/azurebackup_agent) Os clientes DPM do System Center devem atualizar para, pelo menos, DPM 2012 R2 UR5 antes de utilizar o DPM com o serviço de Backup Azure.

## <a name="what-is-the-backup-schedule"></a>O que é o horário de reserva?

O horário de reserva indica a frequência da operação de reserva. Por exemplo, as definições no ecrã seguinte indicam que as cópias de segurança são feitas diariamente às 18h e à meia-noite.

![Horário Diário](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Os clientes também podem agendar uma cópia de segurança semanal. Por exemplo, as definições no ecrã seguinte indicam que as cópias de segurança são tomadas todos os domingos alternados & quarta-feira às 9:30 e 1:00AM.

![Horário semanal](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>O que é a Política de Retenção?

A política de retenção especifica a duração para a qual a cópia de segurança deve ser armazenada. Em vez de especificar apenas uma "política plana" para todos os pontos de backup, os clientes podem especificar diferentes políticas de retenção com base na altura em que a cópia de segurança é tomada. Por exemplo, o ponto de apoio tomado diariamente, que serve de ponto de recuperação operacional, é preservado durante 90 dias. O ponto de apoio tomado no final de cada trimestre para efeitos de auditoria é preservado por uma duração mais longa.

![Política de retenção](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

O número total de "pontos de retenção" especificados nesta política é de 90 (pontos diários) + 40 (um por trimestre para 10 anos) = 130.

## <a name="example--putting-both-together"></a>Exemplo – Juntar os dois

![Tela de amostra](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Política diária de retenção**: As cópias de segurança tomadas diariamente são armazenadas durante sete dias.
2. **Política de retenção semanal**: Os backups feitos à meia-noite e às 18:00 de sábado são preservados durante quatro semanas.
3. **Política de retenção mensal**: Os backups feitos à meia-noite e às 18:00 do último sábado de cada mês são preservados por 12 meses.
4. **Política anual de retenção**: Os backups feitos à meia-noite do último sábado de cada março são preservados por 10 anos.

O número total de "pontos de retenção" (pontos a partir dos quais um cliente pode restaurar dados) no diagrama anterior é calculado da seguinte forma:

* dois pontos por dia durante sete dias = 14 pontos de recuperação
* dois pontos por semana durante quatro semanas = 8 pontos de recuperação
* dois pontos por mês durante 12 meses = 24 pontos de recuperação
* um ponto por ano por 10 anos = 10 pontos de recuperação

O número total de pontos de recuperação é de 56.

> [!NOTE]
> Utilizando o Azure Backup pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, um servidor (físico ou virtual) ou uma carga de trabalho, mantido numa cópia de segurança no Azure.
>

## <a name="advanced-configuration"></a>Configuração avançada

Ao selecionar **Modificar** no ecrã anterior, os clientes têm mais flexibilidade na especificação dos horários de retenção.

![Modificar a janela da política](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a Azure Backup, consulte:

* [Introdução ao Azure Backup](./backup-overview.md)
* [Experimentar o Backup do Azure](./backup-windows-with-mars-agent.md)

---
title: Como substituir sua infraestrutura de fita
description: Saiba como o backup do Azure fornece semântica semelhante à fita que permite que você faça backup e restaure dados no Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 4659a4d6fcc7213f8323e23d59411680276fcb28
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173313"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Mover o armazenamento de longo prazo de fita para a nuvem do Azure

Os clientes do backup do Azure e do System Center Data Protection Manager podem:

* Faça backup de dados em agendas que melhor atendam às necessidades da organização.
* Manter os dados de backup por períodos mais longos
* Torne o Azure uma parte de suas necessidades de retenção de longo prazo (em vez de fita).

Este artigo explica como os clientes podem habilitar políticas de backup e retenção. Os clientes que usam fitas para atender às suas necessidades de retenção de longo prazo agora têm uma alternativa poderosa e viável com a disponibilidade desse recurso. O recurso está habilitado na versão mais recente do backup do Azure (que está disponível [aqui](https://aka.ms/azurebackup_agent)). Os clientes do System Center DPM devem atualizar, pelo menos, o DPM 2012 R2 UR5 antes de usar o DPM com o serviço de backup do Azure.

## <a name="what-is-the-backup-schedule"></a>O que é o agendamento de backup?

O agendamento de backup indica a frequência da operação de backup. Por exemplo, as configurações na tela a seguir indicam que os backups são feitos diariamente às 18:00 e à meia-noite.

![Agenda diária](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Os clientes também podem agendar um backup semanal. Por exemplo, as configurações na tela a seguir indicam que os backups são feitos em todos os domingos alternativos & quarta-feira às 9: às 9h30 e 1:10:00.

![Agenda semanal](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>O que é a política de retenção?

A política de retenção especifica a duração para a qual o backup deve ser armazenado. Em vez de especificar apenas uma "política simples" para todos os pontos de backup, os clientes podem especificar políticas de retenção diferentes com base em quando o backup é feito. Por exemplo, o ponto de backup feito diariamente, que serve como um ponto de recuperação operacional, é preservado por 90 dias. O ponto de backup feito no final de cada trimestre para fins de auditoria é preservado por uma duração maior.

![Política de retenção](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

O número total de "pontos de retenção" especificado nesta política é 90 (pontos diários) + 40 (um a cada trimestre por 10 anos) = 130.

## <a name="example--putting-both-together"></a>Exemplo – colocando ambos juntos

![Tela de exemplo](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Política de retenção diária**: os backups feitos diariamente são armazenados por sete dias.
2. **Política de retenção semanal**: os backups feitos todos os dias à meia-noite e às 18:00 sábado são preservados por quatro semanas
3. **Política de retenção mensal**: os backups feitos à meia-noite e 18:00 no último sábado de cada mês são preservados por 12 meses
4. **Política de retenção anual**: os backups feitos à meia-noite no último sábado de cada março são preservados por 10 anos

O número total de "pontos de retenção" (pontos dos quais um cliente pode restaurar dados) no diagrama anterior é calculado da seguinte maneira:

* dois pontos por dia por sete dias = 14 pontos de recuperação
* dois pontos por semana para quatro semanas = 8 pontos de recuperação
* dois pontos por mês por 12 meses = 24 pontos de recuperação
* um ponto por ano por 10 anos = 10 pontos de recuperação

O número total de pontos de recuperação é 56.

> [!NOTE]
> Usando o backup do Azure, você pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho que faz backup para o Azure.
>

## <a name="advanced-configuration"></a>Configuração avançada

Ao clicar em **Modificar** na tela anterior, os clientes têm flexibilidade adicional para especificar agendamentos de retenção.

![Modificar](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o backup do Azure, consulte:

* [Introdução ao Azure Backup](backup-introduction-to-azure-backup.md)
* [Experimente o backup do Azure](backup-try-azure-backup-in-10-mins.md)

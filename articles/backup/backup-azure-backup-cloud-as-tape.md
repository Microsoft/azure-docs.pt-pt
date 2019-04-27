---
title: Utilizar o Azure Backup para substituir a sua infraestrutura de bandas
description: Saiba como cópia de segurança do Azure fornece semântica do tipo da banda que permite-lhe criar cópias de segurança e restaurar dados no Azure
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/10/2017
ms.author: saurse
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59236774f98af927082c78f4b75a1f5880a7cac4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646961"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Mover o armazenamento de longa duração de banda para a cloud do Azure
Os clientes do Azure de cópia de segurança e o System Center Data Protection Manager podem:

* Criar cópias de segurança agendas que melhor se adequar às necessidades organizacionais.
* Manter os dados de cópia de segurança durante períodos mais longos
* Fazem do Azure necessita de uma parte da sua retenção de longa duração (em vez de banda).

Este artigo explica como os clientes podem ativar políticas de cópia de segurança e retenção. Os clientes que utilizam as bandas para resolver seus longo-prazo-retenção agora tem de ter uma alternativa viável e poderosas com a disponibilidade desta funcionalidade. A funcionalidade está ativada na versão mais recente da cópia de segurança do Azure (que está disponível [aqui](https://aka.ms/azurebackup_agent)). Os clientes do System Center DPM tem de atualizar para, pelo menos, o DPM 2012 R2 UR5 antes de utilizar o DPM com o serviço de cópia de segurança do Azure.

## <a name="what-is-the-backup-schedule"></a>O que é a agenda de cópia de segurança?
A agenda de cópia de segurança indica a frequência da operação de cópia de segurança. Por exemplo, as definições no ecrã seguinte indicam que os backups são feitos diariamente para as 18:00 e à meia-noite.

![Agenda diária](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Os clientes também podem agendar uma cópia de segurança semanal. Por exemplo, as definições no ecrã seguinte indicam que as cópias de segurança são executadas cada Domingo alternativo e a quarta-feira no 9H30 e 1:00.

![Agendamento semanal](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>O que é a política de retenção?
A política de retenção Especifica a duração para os quais a cópia de segurança deve ser armazenada. Em vez de especificar apenas uma "política simples" para todos os pontos de cópia de segurança, os clientes podem especificar políticas de retenção diferentes com base em quando é feita a cópia de segurança. Por exemplo, o ponto de cópia de segurança executado diariamente, que funciona como um ponto de recuperação operacional, é preservado durante 90 dias. O ponto de cópia de segurança efetuado no final de cada trimestre para fins de auditoria é mantido durante um longo período.

![Política de Retenção](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

O número total de "pontos de retenção" especificados nesta política é 90 (pontos diários) + 40 (um de cada trimestre para 10 anos) = 130.

## <a name="example--putting-both-together"></a>Exemplo – colocar ambos em conjunto
![Ecrã de exemplo](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Política de retenção diárias**: Cópias de segurança criadas diariamente são armazenadas durante sete dias.
2. **Política de retenção semanal**: Cópias de segurança criadas todos os dias à meia-noite e 6 PM Sábado são preservadas durante quatro semanas
3. **Política de retenção mensais**: Cópias de segurança criadas em meia-noite e 18h00 do último Sábado de cada mês são preservadas durante 12 meses
4. **Política de retenção anuais**: Cópias de segurança criadas à meia-noite no último Sábado de cada Março são preservadas há 10 anos

O número total de "pontos de retenção" (pontos de que um cliente pode restaurar dados) no diagrama anterior é calculada da seguinte forma:

* dois pontos por dia durante sete dias = 14 pontos de recuperação
* dois pontos por semana para quatro semanas = 8 pontos de recuperação
* dois pontos por mês durante 12 meses = 24 de pontos de recuperação
* um ponto por ano e por recuperação de 10 anos = 10 pontos

O número total de pontos de recuperação é 56.

> [!NOTE]
> Cópia de segurança do Azure não tem uma restrição no número de pontos de recuperação.
>
>

## <a name="advanced-configuration"></a>Configuração avançada
Ao clicar em **modificar** no ecrã anterior, os clientes têm mais flexibilidade para especificar agendamentos de retenção.

![Modificar](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações sobre o Azure Backup, consulte:

* [Introdução ao Azure Backup](backup-introduction-to-azure-backup.md)
* [Experimente o Azure Backup](backup-try-azure-backup-in-10-mins.md)

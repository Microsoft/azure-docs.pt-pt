---
title: Como substituir a sua infraestrutura de fita
description: Saiba como o Azure Backup fornece semântica semelhante a fita que lhe permite fazer backup e restaurar dados em Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: aeda1cefc84d425855c40b793f8334936541e63f
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425107"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Mova o seu armazenamento a longo prazo da fita para a nuvem Azure

Os clientes do Gestor de Proteção de Dados do Azure Backup e System Center podem:

* Recue os dados em horários que melhor se adequam às necessidades organizacionais.
* Retenha os dados de backup por períodos mais longos.
* Faça do Azure uma parte das suas necessidades de retenção a longo prazo (em vez de fita).

Este artigo explica como os clientes podem ativar políticas de backup e retenção. Os clientes que usam fitas para responder às suas necessidades de retenção a longo prazo têm agora uma alternativa poderosa e viável com a disponibilidade desta funcionalidade. A funcionalidade está ativada no mais recente lançamento do Azure Backup (que está disponível [aqui).](https://aka.ms/azurebackup_agent) Os clientes DPM do System Center devem atualizar para, pelo menos, DPM 2012 R2 UR5 antes de utilizar em DPM com o serviço de backup Azure.

## <a name="what-is-the-backup-schedule"></a>O que é a Agenda de Apoio?

O horário de reserva indica a frequência da operação de reserva. Por exemplo, as definições no ecrã seguinte indicam que as cópias de segurança são tomadas diariamente às 18h e à meia-noite.

![Horário diário](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Os clientes também podem agendar uma cópia de segurança semanal. Por exemplo, as definições no ecrã seguinte indicam que as cópias de segurança são tomadas todos os domingos e quartas-feiras alternados às 9:30 am e 1:00 AM.

![Horário semanal](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>O que é a Política de Retenção?

A política de retenção especifica a duração da reserva. Em vez de especificar apenas uma "política plana" para todos os pontos de backup, os clientes podem especificar diferentes políticas de retenção com base no momento em que a cópia de segurança é tomada. Por exemplo, o ponto de backup tomado diariamente, que serve de ponto de recuperação operacional, é preservado durante 90 dias. O ponto de apoio tomado no final de cada trimestre para efeitos de auditoria é preservado por uma duração mais longa.

![Política de retenção](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

O número total de "pontos de retenção" especificados nesta política é de 90 (pontos diários) + 40 (um trimestre por 10 anos) = 130.

## <a name="example--putting-both-together"></a>Exemplo – Juntar ambos

![Tela de amostra](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. Política diária de **retenção**: Os backups tomados diariamente são armazenados durante sete dias.
2. Política semanal de **retenção**: Os backups tomados à meia-noite e às 18:00 de sábado estão preservados por quatro semanas.
3. Política mensal de **retenção**: Os backups feitos à meia-noite e às 18h no último sábado de cada mês são preservados por 12 meses.
4. Política anual de **retenção**: Os backups tomados à meia-noite do último sábado de cada março são preservados por 10 anos.

O número total de "pontos de retenção" (pontos a partir dos quais um cliente pode restaurar os dados) no diagrama anterior é calculado da seguinte forma:

* dois pontos por dia durante sete dias = 14 pontos de recuperação
* dois pontos por semana durante quatro semanas = 8 pontos de recuperação
* dois pontos por mês durante 12 meses = 24 pontos de recuperação
* um ponto por ano por 10 anos = 10 pontos de recuperação

O número total de pontos de recuperação é de 56.

> [!NOTE]
> Utilizando o Azure Backup pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, um servidor (físico ou virtual) ou uma carga de trabalho, mantido numa cópia de segurança no Azure.
>

## <a name="advanced-configuration"></a>Configuração avançada

Ao clicar em **Modificar** no ecrã anterior, os clientes têm mais flexibilidade na especificação dos horários de retenção.

![Modificar](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Azure Backup, consulte:

* [Introdução ao Azure Backup](backup-introduction-to-azure-backup.md)
* [Tente backup Azure](backup-try-azure-backup-in-10-mins.md)

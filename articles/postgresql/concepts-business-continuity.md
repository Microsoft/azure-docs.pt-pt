---
title: Descrição geral da continuidade comercial com a base de dados do Azure para PostgreSQL
description: Descrição geral da continuidade comercial com a base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: b1d566ac571ddd2b2be3aff160f669e277887209
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456279"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql"></a>Descrição geral da continuidade comercial com a base de dados do Azure para PostgreSQL

Esta descrição geral descreve as capacidades que a base de dados do Azure para PostgreSQL fornece para continuidade do negócio e recuperação após desastre. Saiba mais sobre as opções para recuperar de eventos problemáticos que podem causar perda de dados ou fazer com que a sua base de dados e a aplicação fiquem indisponíveis. Saiba o que fazer quando um erro de utilizador ou aplicação afeta a integridade dos dados, uma região do Azure fica indisponível ou a aplicação requerer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Recursos que pode usar para fornecer continuidade do negócio

Base de dados do Azure para PostgreSQL fornece funcionalidades de continuidade de negócio que incluem cópias de segurança automáticas e a capacidade dos utilizadores para iniciar o restauro geográfico. Cada uma possui características diferentes para o tempo de recuperação estimado (ERT) e a potencial perda de dados. Assim que compreender estas opções, pode escolher entre eles e utilizá-los em conjunto para diferentes cenários. Desenvolver o seu plano de continuidade do negócio, precisa entender o tempo máximo aceitável antes da aplicação recuperar totalmente após o evento problemático – este é o seu objetivo de tempo de recuperação (RTO). Também precisa de saber a quantidade máxima de dados recentes atualizações (intervalo de tempo) da aplicação pode tolerar perder ao recuperar após o evento problemático – este é o seu objetivo de ponto de recuperação (RPO).

A tabela seguinte compara o ERT e o RPO para as funcionalidades disponíveis:

| **Capacidade** | **Básica** | **Fins gerais** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauro dentro do período de retenção | Qualquer ponto de restauro dentro do período de retenção | Qualquer ponto de restauro dentro do período de retenção |
| Georrestauro a partir de cópias de segurança georreplicado | Não suportado | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Eliminar servidores **não é possível** ser restaurada. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminados e não podem ser recuperados.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de utilizador ou aplicação

Pode utilizar cópias de segurança do serviço para recuperar um servidor de vários eventos problemáticos. Um utilizador poderá acidentalmente eliminar alguns dados, inadvertidamente remover uma tabela importante ou até mesmo remover uma base de dados inteiro. Um aplicativo acidentalmente pode substituir os dados com dados incorretos devido a um defeito da aplicação e assim por diante.

Pode executar um ponto de restauro anterior no tempo-para criar uma cópia do seu servidor para um ponto bom conhecido no tempo. Este ponto anterior no tempo tem de ser dentro do período de retenção de cópia de segurança que configurou para o seu servidor. Depois de restaurar os dados para o novo servidor, pode substituir o servidor original com o servidor recentemente restaurado ou copiar os dados necessários do servidor restaurado para o servidor original.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar a partir de uma indisponibilidade do Centro de dados regionais do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando a falha ocorre, ele faz com que uma interrupção do negócio que só pode durar alguns minutos, mas pode durar horas.

Uma opção é aguardar que o servidor esteja novamente online quando a indisponibilidade do Centro de dados está sobre. Isto funciona para aplicações que podem estar a ter o servidor offline para um determinado período de tempo, por exemplo um ambiente de desenvolvimento. Quando o Centro de dados fica indisponível, não sabe quanto pode durar a indisponibilidade, pelo que esta opção só funciona se não precisar de seu servidor há algum tempo.

A outra opção é usar a base de dados do Azure para a funcionalidade de restauro geográfico do PostgreSQL que restaura o servidor através de cópias de segurança georredundante. Estas cópias de segurança estão acessíveis, mesmo quando o região do que seu servidor esteja hospedado no está offline. Pode restaurar a partir destas cópias de segurança em qualquer outra região e trazer o seu servidor novamente online.

> [!IMPORTANT]
> O restauro geográfico só é possível se aprovisionou o servidor com armazenamento de cópia de segurança georredundante. Se quiser mudar do localmente redundante para cópias de segurança georredundante para um servidor existente, tem de fazer um despejo pg_dump do seu servidor existente a utilizar e restaurá-lo para um criado recentemente configurado com as cópias de segurança georredundante.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o [automatizada de cópias de segurança na base de dados do Azure para PostgreSQL](concepts-backup.md). 
- Aprender a restaurar utilizando [do portal do Azure](howto-restore-server-portal.md) ou [a CLI do Azure](howto-restore-server-cli.md).
- Saiba mais sobre [ler réplicas na base de dados do Azure para PostgreSQL](concepts-read-replicas.md).
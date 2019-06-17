---
title: Descrição geral da continuidade comercial com a base de dados do Azure para MariaDB
description: Descrição geral da continuidade comercial com a base de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4c64f920bf56195ad53ac8acbf3f9199090f0a8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61043021"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mariadb"></a>Descrição geral da continuidade comercial com a base de dados do Azure para MariaDB

Esta descrição geral descreve as capacidades que a base de dados do Azure para MariaDB fornece para continuidade do negócio e recuperação após desastre. Saiba mais sobre as opções para recuperar de eventos problemáticos que podem causar perda de dados ou fazer com que a sua base de dados e a aplicação fiquem indisponíveis. Saiba o que fazer quando um erro de utilizador ou aplicação afeta a integridade dos dados, uma região do Azure fica indisponível ou a aplicação requerer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Recursos que pode usar para fornecer continuidade do negócio

Base de dados do Azure para MariaDB fornece funcionalidades de continuidade de negócio que incluem cópias de segurança automáticas e a capacidade dos utilizadores para iniciar o restauro geográfico. Cada uma possui características diferentes para o tempo de recuperação estimado (ERT) e a potencial perda de dados. Assim que compreender estas opções, pode escolher entre eles e utilizá-los em conjunto para diferentes cenários. Desenvolver o seu plano de continuidade do negócio, precisa entender o tempo máximo aceitável antes da aplicação recuperar totalmente após o evento problemático – este é o seu objetivo de tempo de recuperação (RTO). Também precisa de saber a quantidade máxima de dados recentes atualizações (intervalo de tempo) da aplicação pode tolerar perder ao recuperar após o evento problemático – este é o seu objetivo de ponto de recuperação (RPO).

A tabela seguinte compara o ERT e o RPO para as funcionalidades disponíveis:

| **Capacidade** | **Básica** | **Fins gerais** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauro dentro do período de retenção | Qualquer ponto de restauro dentro do período de retenção | Qualquer ponto de restauro dentro do período de retenção |
| Georrestauro a partir de cópias de segurança georreplicado | Não suportado | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Se eliminar o servidor, todas as bases de dados contidos no servidor também são eliminados e não podem ser recuperados. Não é possível restaurar um servidor foi eliminado.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de utilizador ou aplicação

Pode utilizar cópias de segurança do serviço para recuperar um servidor de vários eventos problemáticos. Um utilizador poderá acidentalmente eliminar alguns dados, inadvertidamente remover uma tabela importante ou até mesmo remover uma base de dados inteiro. Um aplicativo acidentalmente pode substituir os dados com dados incorretos devido a um defeito da aplicação e assim por diante.

Pode executar um ponto de restauro anterior no tempo-para criar uma cópia do seu servidor para um ponto bom conhecido no tempo. Este ponto anterior no tempo tem de ser dentro do período de retenção de cópia de segurança que configurou para o seu servidor. Depois de restaurar os dados para o novo servidor, pode substituir o servidor original com o servidor recentemente restaurado ou copiar os dados necessários do servidor restaurado para o servidor original.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar a partir de uma indisponibilidade do Centro de dados regionais do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando a falha ocorre, ele faz com que uma interrupção do negócio que só pode durar alguns minutos, mas pode durar horas.

Uma opção é aguardar que o servidor esteja novamente online quando a indisponibilidade do Centro de dados está sobre. Isto funciona para aplicações que podem estar a ter o servidor offline para um determinado período de tempo, por exemplo um ambiente de desenvolvimento. Quando o Centro de dados fica indisponível, não sabe quanto pode durar a indisponibilidade, pelo que esta opção só funciona se não precisar de seu servidor há algum tempo.

A outra opção é usar a base de dados do Azure para a funcionalidade de restauro geográfico da MariaDB que restaura o servidor através de cópias de segurança georredundante. Estas cópias de segurança estão acessíveis, mesmo quando o região do que seu servidor esteja hospedado no está offline. Pode restaurar a partir destas cópias de segurança em qualquer outra região e trazer o seu servidor novamente online.

> [!IMPORTANT]
> O restauro geográfico só é possível se aprovisionou o servidor com armazenamento de cópia de segurança georredundante.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre as cópias de segurança automáticas, veja [cópias de segurança na base de dados do Azure para MariaDB](concepts-backup.md).
- Para restaurar para um ponto no tempo, com o portal do Azure, consulte [restaurar base de dados para um ponto no tempo com o portal do Azure](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->

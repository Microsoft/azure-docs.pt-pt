---
title: Continuidade do negócio - Base de Dados Azure para MariaDB
description: Saiba mais sobre a continuidade do negócio (restauro pontual, interrupção do data center, geo-restauração) ao utilizar a Base de Dados Azure para o serviço MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c01e0df1f420c8489ca3445d9fa025b251a870f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79532396"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Compreender a continuidade do negócio na Base de Dados Azure para a MariaDB

Este artigo descreve as capacidades que a Azure Database for MariaDB prevê para a continuidade do negócio e recuperação de desastres. Saiba mais sobre opções para recuperar de eventos disruptivos que possam causar perda de dados ou fazer com que a sua base de dados e aplicação fiquem indisponíveis. Saiba o que fazer quando um utilizador ou erro de aplicação afeta a integridade dos dados, uma região do Azure tem uma paragem ou a sua aplicação requer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Características que pode usar para proporcionar continuidade ao negócio

A Azure Database for MariaDB fornece funcionalidades de continuidade de negócios que incluem cópias de segurança automatizadas e a capacidade de os utilizadores iniciarem o geo-restauro. Cada um tem características diferentes para o tempo estimado de recuperação (ERT) e a perda de dados potenciais. Assim que compreenderes estas opções, podes escolher entre elas e usá-las juntas para diferentes cenários. À medida que desenvolve o seu plano de continuidade de negócio, precisa de compreender o tempo máximo aceitável antes que a aplicação recupere totalmente após o evento disruptivo - este é o seu Objetivo de Tempo de Recuperação (RTO). Também precisa de compreender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que a aplicação pode tolerar perder ao recuperar após o evento disruptivo - este é o seu Objetivo de Ponto de Recuperação (RPO).

A tabela a seguir compara o ERT e o RPO pelas funcionalidades disponíveis:

| **Capacidade** | **Básica** | **Finalidade Geral** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção |
| Geo-restauro a partir de backups geo-replicados | Não suportado | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Se eliminar o servidor, todas as bases de dados contidas no servidor também são eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor apagado.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de utilizador ou aplicação

Pode utilizar as cópias de segurança do serviço para recuperar um servidor de vários eventos disruptivos. Um utilizador pode acidentalmente apagar alguns dados, deixar cair inadvertidamente uma tabela importante ou até mesmo deixar cair uma base de dados inteira. Uma aplicação pode acidentalmente substituir bons dados com dados maus devido a um defeito de aplicação, e assim por diante.

Pode executar um restauro pontual para criar uma cópia do seu servidor num bom momento conhecido. Este ponto no tempo deve estar dentro do período de retenção de backup que configura para o seu servidor. Depois de os dados terem sido restaurados para o novo servidor, pode substituir o servidor original pelo servidor recentemente restaurado ou copiar os dados necessários do servidor restaurado para o servidor original.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar de uma paragem do centro de dados regional do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma paralisação, causa uma perturbação de negócio que pode durar apenas alguns minutos, mas pode durar horas.

Uma opção é esperar que o seu servidor volte a estar online quando a paragem do data center terminar. Isto funciona para aplicações que podem dar ao luxo de ter o servidor offline por algum tempo, por exemplo, um ambiente de desenvolvimento. Quando o data center tem uma paragem, não sabe quanto tempo a paralisação pode durar, por isso esta opção só funciona se não precisar do seu servidor durante algum tempo.

A outra opção é utilizar a Base de Dados Azure para a funcionalidade de geo-restauro da MariaDB que restaura o servidor utilizando cópias de segurança geo-redundantes. Estas cópias de segurança são acessíveis mesmo quando a região em que o seu servidor está hospedado está offline. Pode restaurar destas cópias de segurança para qualquer outra região e voltar a colocar o seu servidor online.

> [!IMPORTANT]
> O geo-restauro só é possível se forte o servidor com armazenamento de backup geo-redundante.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre as cópias de segurança automatizadas, consulte [backups na Base de Dados Azure para MariaDB](concepts-backup.md).
- Para restaurar um ponto no tempo usando o portal Azure, consulte [restaurar a base de dados a um ponto no tempo utilizando o portal Azure](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->

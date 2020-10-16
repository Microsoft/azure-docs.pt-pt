---
title: Continuidade do negócio - Base de Dados Azure para MySQL
description: Saiba mais sobre a continuidade do negócio (restauro pontual, interrupção do data center, geo-restauração) ao utilizar a Base de Dados Azure para o serviço MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: b21062256896ebfc9c11b031413538b39620d1e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613971"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Compreender a continuidade do negócio na Base de Dados Azure para o MySQL

Este artigo descreve as capacidades que a Azure Database para o MySQL prevê para a continuidade do negócio e recuperação de desastres. Saiba mais sobre opções para recuperar de eventos disruptivos que possam causar perda de dados ou fazer com que a sua base de dados e aplicação fiquem indisponíveis. Saiba o que fazer quando um utilizador ou erro de aplicação afeta a integridade dos dados, uma região do Azure tem uma paragem ou a sua aplicação requer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Características que pode usar para proporcionar continuidade ao negócio

A Azure Database for MySQL fornece funcionalidades de continuidade de negócios que incluem cópias de segurança automatizadas e a capacidade de os utilizadores iniciarem o geo-restauro. Cada um tem características diferentes para o tempo estimado de recuperação (ERT) e a perda de dados potenciais. Estima-se que o tempo de recuperação (ERT) seja estimado para que a base de dados esteja totalmente funcional após um pedido de restauro/falha. Assim que compreenderes estas opções, podes escolher entre elas e usá-las juntas para diferentes cenários. À medida que desenvolve o seu plano de continuidade de negócio, precisa de compreender o tempo máximo aceitável antes que a aplicação recupere totalmente após o evento disruptivo - este é o seu Objetivo de Tempo de Recuperação (RTO). Também precisa de compreender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que a aplicação pode tolerar perder ao recuperar após o evento disruptivo - este é o seu Objetivo de Ponto de Recuperação (RPO).

A tabela a seguir compara o ERT e o RPO pelas funcionalidades disponíveis:

| **Capacidade** | **Básica** | **Fins Gerais** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção |
| Geo-restauro a partir de backups geo-replicados | Não suportado | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de utilizador ou aplicação

Pode utilizar as cópias de segurança do serviço para recuperar um servidor de vários eventos disruptivos. Um utilizador pode acidentalmente apagar alguns dados, deixar cair inadvertidamente uma tabela importante ou até mesmo deixar cair uma base de dados inteira. Uma aplicação pode acidentalmente substituir bons dados com dados maus devido a um defeito de aplicação, e assim por diante.

Pode executar um restauro pontual para criar uma cópia do seu servidor num bom momento conhecido. Este ponto no tempo deve estar dentro do período de retenção de backup que configura para o seu servidor. Depois de os dados terem sido restaurados para o novo servidor, pode substituir o servidor original pelo servidor recentemente restaurado ou copiar os dados necessários do servidor restaurado para o servidor original.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar de uma paragem do centro de dados regional do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma paralisação, causa uma perturbação de negócio que pode durar apenas alguns minutos, mas pode durar horas.

Uma opção é esperar que o seu servidor volte a estar online quando a paragem do data center terminar. Isto funciona para aplicações que podem dar ao luxo de ter o servidor offline por algum tempo, por exemplo, um ambiente de desenvolvimento. Quando o data center tem uma paragem, não sabe quanto tempo a paralisação pode durar, por isso esta opção só funciona se não precisar do seu servidor durante algum tempo.

A outra opção é utilizar a Base de Dados Azure para a funcionalidade de geo-restauro do MySQL que restaura o servidor utilizando cópias de segurança geo-redundantes. Estas cópias de segurança são acessíveis mesmo quando a região em que o seu servidor está hospedado está offline. Pode restaurar destas cópias de segurança para qualquer outra região e voltar a colocar o seu servidor online.

> [!IMPORTANT]
> O geo-restauro só é possível se forte o servidor com armazenamento de backup geo-redundante. Se desejar mudar de cópias de segurança locais redundantes para geo-redundantes para um servidor existente, deve descarregar usando o mysqldump do seu servidor existente e restaurá-lo para um servidor recém-criado configurado com cópias de segurança geo-redundantes.

## <a name="cross-region-read-replicas"></a>Réplicas de leitura transversal

Você pode usar réplicas de leitura de região cruzada para melhorar o seu plano de continuidade e recuperação de desastres. As réplicas de leitura são atualizadas assíncronea usando a tecnologia binária de replicação de registos do MySQL. Saiba mais sobre réplicas lidas, regiões disponíveis e como falhar a partir do artigo de [conceitos de réplicas lidos.](concepts-read-replicas.md) 

## <a name="faq"></a>FAQ
### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Onde é que a Azure Database para o MySQL armazena os dados dos clientes?
Por padrão, a Base de Dados Azure para o MySQL não move nem armazena os dados dos clientes para fora da região em que está implantado. No entanto, os clientes podem optar opcionalmente por permitir [cópias de segurança geo-redundantes](concepts-backup.md#backup-redundancy-options) ou criar [réplicas de leitura cruzada](concepts-read-replicas.md#cross-region-replication) para armazenar dados noutra região.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [cópias de segurança automáticas na Base de Dados Azure para o MySQL](concepts-backup.md).
- Saiba como restaurar utilizando [o portal Azure](howto-restore-server-portal.md) ou [o Azure CLI](howto-restore-server-cli.md).
- Saiba mais sobre [réplicas de leitura na Base de Dados Azure para o MySQL](concepts-read-replicas.md).

---
title: Continuidade do negócio - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a continuidade do negócio (restauro de tempo, interrupção do centro de dados, geo-restauro) ao utilizar a Base de Dados Azure para postgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: afa03399933bdc8bd8ff869125955cfd9e0abecb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981918"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Visão geral da continuidade do negócio com base de dados Azure para PostgreSQL - Servidor Único

Esta visão geral descreve as capacidades que a Base de Dados Azure para postgreSQL fornece para a continuidade do negócio e recuperação de desastres. Saiba mais sobre as opções de recuperação de eventos disruptivos que possam causar perda de dados ou fazer com que a sua base de dados e aplicação fiquem indisponíveis. Saiba o que fazer quando um erro de utilizador ou aplicação afeta a integridade dos dados, uma região do Azure tem uma falha, ou a sua aplicação requer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funcionalidades que pode usar para fornecer continuidade ao negócio

A Base de Dados Azure para PostgreSQL fornece funcionalidades de continuidade do negócio que incluem backups automatizados e a capacidade para os utilizadores iniciarem a geo-restauração. Cada um tem características diferentes para o Tempo estimado de recuperação (ERT) e perda de dados potenciais. Assim que compreenderes estas opções, podes escolher entre elas e usá-las juntas para diferentes cenários. À medida que desenvolve o seu plano de continuidade de negócios, precisa de compreender o tempo máximo aceitável antes de a aplicação recuperar totalmente após o evento disruptivo - este é o seu Objetivo de Tempo de Recuperação (RTO). Também precisa entender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que a aplicação pode tolerar perder quando se recuperar após o evento disruptivo - este é o seu Objetivo de Ponto de Recuperação (RPO).

O quadro seguinte compara o ERT e o RPO para as funcionalidades disponíveis:

| **Capacidade** | **Básico** | **Propósito Geral** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauro dentro do período de retenção | Qualquer ponto de restauro dentro do período de retenção | Qualquer ponto de restauro dentro do período de retenção |
| Geo-restauro de backups geo-replicados | Não suportado | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Utilize o bloqueio de [recursos Azure](../azure-resource-manager/management/lock-resources.md) para ajudar a evitar a supressão acidental do seu servidor.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de utilizador ou aplicação

Pode utilizar as cópias de segurança do serviço para recuperar um servidor de vários eventos disruptivos. Um utilizador pode acidentalmente apagar alguns dados, deixar cair inadvertidamente uma tabela importante ou mesmo deixar cair uma base de dados inteira. Uma aplicação pode acidentalmente substituir bons dados com dados maus devido a um defeito de aplicação, e assim por diante.

Pode realizar um **restauro** pontual para criar uma cópia do seu servidor para um bom ponto de tempo conhecido. Este ponto de tempo deve estar dentro do período de retenção de cópia de segurança configurado para o seu servidor. Depois de os dados forem restaurados ao novo servidor, pode substituir o servidor original pelo servidor recém-restaurado ou copiar os dados necessários do servidor restaurado para o servidor original.

## <a name="recover-from-an-azure-data-center-outage"></a>Recuperar de uma paragem do centro de dados do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma paragem, causa uma perturbação no negócio que pode durar apenas alguns minutos, mas pode durar horas.

Uma opção é esperar que o seu servidor volte a funcionar quando a interrupção do centro de dados terminar. Isto funciona para aplicações que podem dar ao luxo de ter o servidor offline por algum tempo, por exemplo, um ambiente de desenvolvimento. Quando um data center tem uma paragem, não sabe quanto tempo a paralisação pode durar, pelo que esta opção só funciona se não precisar do seu servidor por um tempo.

## <a name="geo-restore"></a>Georrestauro

A função de geo-restauro restaura o servidor utilizando backups georedundantes. As cópias de segurança estão alojadas na [região emparelhada](../best-practices-availability-paired-regions.md)do seu servidor. Pode restaurar estes reforços para qualquer outra região. O geo-restauro cria um novo servidor com os dados das cópias de segurança. Saiba mais sobre geo-restauro a partir do artigo de backup e restauro de [conceitos.](concepts-backup.md)

> [!IMPORTANT]
> A geo-restauração só é possível se for metodo o servidor com armazenamento de backup geo-redundante. Se pretender mudar de backups redundantes localmente para cópias de segurança georedundantes para um servidor existente, deve descarregar usando pg_dump do seu servidor existente e restaurá-lo para um servidor recém-criado configurado com backups georedundantes.

## <a name="cross-region-read-replicas"></a>Réplicas de leitura transversal
Você pode usar réplicas de leitura transversal para melhorar a continuidade do seu negócio e planeamento de recuperação de desastres. As réplicas de leitura são atualizadas sincronicamente utilizando a tecnologia de replicação física do PostgreSQL. Saiba mais sobre réplicas de leitura, regiões disponíveis e como falhar com o artigo de [replicas de leitura.](concepts-read-replicas.md) 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as cópias de [segurança automatizadas na Base de Dados Azure para PostgreSQL](concepts-backup.md). 
- Aprenda a restaurar [utilizando o portal Azure](howto-restore-server-portal.md) ou [o Azure CLI](howto-restore-server-cli.md).
- Saiba mais sobre [as réplicas de leitura na Base de Dados Azure para PostgreSQL](concepts-read-replicas.md).
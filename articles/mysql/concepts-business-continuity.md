---
title: Continuidade do negócio - Base de Dados Azure para MySQL
description: Saiba mais sobre a continuidade do negócio (restauro pontual, interrupção do centro de dados, geo-restauro) ao utilizar a Base de Dados Azure para o serviço MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: af0069adc741cfc802c37c90c0c7ec3c3ba74bb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537232"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Compreender a continuidade do negócio na Base de Dados Azure para o MySQL

Este artigo descreve as capacidades que a Base de Dados Azure para o MySQL fornece para a continuidade do negócio e recuperação de desastres. Saiba mais sobre as opções de recuperação de eventos disruptivos que possam causar perda de dados ou fazer com que a sua base de dados e aplicação fiquem indisponíveis. Saiba o que fazer quando um erro de utilizador ou aplicação afeta a integridade dos dados, uma região do Azure tem uma falha, ou a sua aplicação requer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funcionalidades que pode usar para fornecer continuidade ao negócio

A Base de Dados Azure para o MySQL fornece funcionalidades de continuidade de negócioque incluem backups automatizados e a capacidade para os utilizadores iniciarem a geo-restauração. Cada um tem características diferentes para o Tempo estimado de recuperação (ERT) e perda de dados potenciais. Assim que compreenderes estas opções, podes escolher entre elas e usá-las juntas para diferentes cenários. À medida que desenvolve o seu plano de continuidade de negócios, precisa de compreender o tempo máximo aceitável antes de a aplicação recuperar totalmente após o evento disruptivo - este é o seu Objetivo de Tempo de Recuperação (RTO). Também precisa entender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que a aplicação pode tolerar perder quando se recuperar após o evento disruptivo - este é o seu Objetivo de Ponto de Recuperação (RPO).

O quadro seguinte compara o ERT e o RPO para as funcionalidades disponíveis:

| **Capacidade** | **Básico** | **Propósito Geral** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauro dentro do período de retenção | Qualquer ponto de restauro dentro do período de retenção | Qualquer ponto de restauro dentro do período de retenção |
| Geo-restauro de backups geo-replicados | Não suportado | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de utilizador ou aplicação

Pode utilizar as cópias de segurança do serviço para recuperar um servidor de vários eventos disruptivos. Um utilizador pode acidentalmente apagar alguns dados, deixar cair inadvertidamente uma tabela importante ou mesmo deixar cair uma base de dados inteira. Uma aplicação pode acidentalmente substituir bons dados com dados maus devido a um defeito de aplicação, e assim por diante.

Pode realizar um restauro pontual para criar uma cópia do seu servidor para um bom ponto de tempo conhecido. Este ponto de tempo deve estar dentro do período de retenção de cópia de segurança configurado para o seu servidor. Depois de os dados forem restaurados ao novo servidor, pode substituir o servidor original pelo servidor recém-restaurado ou copiar os dados necessários do servidor restaurado para o servidor original.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar de uma paragem do centro de dados regional do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma paragem, causa uma perturbação no negócio que pode durar apenas alguns minutos, mas pode durar horas.

Uma opção é esperar que o seu servidor volte a funcionar quando a interrupção do centro de dados terminar. Isto funciona para aplicações que podem dar ao luxo de ter o servidor offline por algum tempo, por exemplo, um ambiente de desenvolvimento. Quando o data center tem uma paragem, não sabe quanto tempo a paralisação pode durar, pelo que esta opção só funciona se não precisar do seu servidor por um tempo.

A outra opção é utilizar a Base de Dados Azure para a funcionalidade de georestauro da MySQL que restaura o servidor utilizando backups georedundantes. Estas cópias de segurança são acessíveis mesmo quando a região onde o seu servidor está hospedado está offline. Pode restaurar estas cópias de segurança para qualquer outra região e voltar a colocar o seu servidor online.

> [!IMPORTANT]
> A geo-restauração só é possível se for metodo o servidor com armazenamento de backup geo-redundante. Se pretender mudar de backups redundantes localmente para geo-redundantes para um servidor existente, deve descarregar usando o mysqldump do seu servidor existente e restaurá-lo para um servidor recém-criado configurado com backups georedundantes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as cópias de segurança automatizadas na Base de [Dados Azure para MySQL](concepts-backup.md).
- Aprenda a restaurar [utilizando o portal Azure](howto-restore-server-portal.md) ou [o Azure CLI](howto-restore-server-cli.md).
- Saiba mais sobre [réplicas de leitura na Base de Dados Azure para MySQL](concepts-read-replicas.md).

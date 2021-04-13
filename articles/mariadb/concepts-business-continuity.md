---
title: Continuidade do negócio - Base de Dados Azure para MariaDB
description: Saiba mais sobre a continuidade do negócio (restauro pontual, interrupção do data center, geo-restauração) ao utilizar a Base de Dados Azure para o serviço MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: e4bdbd7dc9a751b6ea332ef760e5f016496c615c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306743"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mariadb"></a>Visão geral da continuidade do negócio com Azure Database para MariaDB

Este artigo descreve as capacidades que a Azure Database para o MySQL prevê para a continuidade do negócio e recuperação de desastres. Saiba mais sobre opções para recuperar de eventos disruptivos que possam causar perda de dados ou fazer com que a sua base de dados e aplicação fiquem indisponíveis. Saiba o que fazer quando um utilizador ou erro de aplicação afeta a integridade dos dados, uma região do Azure tem uma paragem ou a sua aplicação requer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Características que pode usar para proporcionar continuidade ao negócio

À medida que desenvolve o seu plano de continuidade de negócio, precisa de compreender o tempo máximo aceitável antes que a aplicação recupere totalmente após o evento disruptivo - este é o seu Objetivo de Tempo de Recuperação (RTO). Também precisa de compreender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que a aplicação pode tolerar perder ao recuperar após o evento disruptivo - este é o seu Objetivo de Ponto de Recuperação (RPO).

A Azure Database for MariaDB fornece funcionalidades de continuidade de negócios e recuperação de desastres que incluem backups geo-redundantes com a capacidade de iniciar o geo-restauro, e implantar réplicas de leitura em uma região diferente. Cada um tem características diferentes para o tempo de recuperação e a perda de dados potenciais. Com a funcionalidade [Geo-restauro,](concepts-backup.md) um novo servidor é criado usando os dados de backup que são replicados a partir de outra região. O tempo total que leva para restaurar e recuperar depende do tamanho da base de dados e da quantidade de registos para recuperar. O tempo geral para estabelecer o servidor varia de poucos minutos a poucas horas. Com [réplicas lidas,](concepts-read-replicas.md)os registos de transações das primárias são assíncronos transmitidos para a réplica. Em caso de interrupção da base de dados primária devido a uma falha ao nível da zona ou a uma falha a nível da região, a falha na réplica fornece um RTO mais curto e uma redução da perda de dados.

> [!NOTE]
> O desfasamento entre o primário e a réplica depende da latência entre os sites, da quantidade de dados a transmitir e, mais importante, da carga de trabalho de escrita do servidor primário. Cargas de trabalho de escrita pesadas podem gerar um atraso significativo. 
>
> Devido à natureza assíncronia da replicação utilizada para réplicas de leitura, **não devem** ser consideradas como uma solução de Alta Disponibilidade (HA), uma vez que os lags mais elevados podem significar rto e RPO mais elevados. Apenas para cargas de trabalho onde o lag permanece menor através dos tempos de pico e não-pico da carga de trabalho, as réplicas lidas podem funcionar como uma alternativa HA. Caso contrário, as réplicas lidas destinam-se a uma verdadeira escala de leitura para cargas de trabalho pesadas prontas e para cenários DR (Recuperação de Desastres).

A tabela a seguir compara RTO e RPO num cenário **típico de carga de trabalho:**

| **Capacidade** | **Básica** | **Fins Gerais** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauração dentro do período de retenção <br/> RTO - Varia <br/>RPO < 15 min| Qualquer ponto de restauração dentro do período de retenção <br/> RTO - Varia <br/>RPO < 15 min | Qualquer ponto de restauração dentro do período de retenção <br/> RTO - Varia <br/>RPO < 15 min |
| Geo-restauro a partir de backups geo-replicados | Não suportado | RTO - Varia <br/>RPO < 1 h | RTO - Varia <br/>RPO < 1 h |
| Réplicas de leitura | RTO - Minutos* <br/>RPO < 5 min* | RTO - Minutos* <br/>RPO < 5 min*| RTO - Minutos* <br/>RPO < 5 min*|

 \* O RTO e o RPO **podem ser muito mais elevados** em alguns casos, dependendo de vários fatores, incluindo a latência entre sites, a quantidade de dados a transmitir, e, importantemente, a base de dados primária escrever carga de trabalho.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de utilizador ou aplicação

Pode utilizar as cópias de segurança do serviço para recuperar um servidor de vários eventos disruptivos. Um utilizador pode acidentalmente apagar alguns dados, deixar cair inadvertidamente uma tabela importante ou até mesmo deixar cair uma base de dados inteira. Uma aplicação pode acidentalmente substituir bons dados com dados maus devido a um defeito de aplicação, e assim por diante.

Pode executar um restauro pontual para criar uma cópia do seu servidor num bom momento conhecido. Este ponto no tempo deve estar dentro do período de retenção de backup que configura para o seu servidor. Depois de os dados terem sido restaurados para o novo servidor, pode substituir o servidor original pelo servidor recentemente restaurado ou copiar os dados necessários do servidor restaurado para o servidor original.

> [!IMPORTANT]
> Os servidores eliminados só podem ser restaurados no prazo de **cinco dias** após o qual as cópias de segurança são eliminadas. A cópia de segurança da base de dados só pode ser acedida e restaurada a partir da subscrição Azure que hospeda o servidor. Para restaurar um servidor abandonado, consulte [os passos documentados](howto-restore-dropped-server.md). Para proteger os recursos do servidor, a implantação pós-implantação, contra a eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](../azure-resource-manager/management/lock-resources.md).

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar de uma paragem do centro de dados regional do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma paralisação, causa uma perturbação de negócio que pode durar apenas alguns minutos, mas pode durar horas.

Uma opção é esperar que o seu servidor volte a estar online quando a paragem do data center terminar. Isto funciona para aplicações que podem dar ao luxo de ter o servidor offline por algum tempo, por exemplo, um ambiente de desenvolvimento. Quando o data center tem uma paragem, não sabe quanto tempo a paralisação pode durar, por isso esta opção só funciona se não precisar do seu servidor durante algum tempo.

## <a name="geo-restore"></a>Georrestauro

A função de geo-restauro restaura o servidor utilizando cópias de segurança geo-redundantes. As cópias de segurança estão hospedadas na [região emparelhada](../best-practices-availability-paired-regions.md)do seu servidor. Estas cópias de segurança são acessíveis mesmo quando a região em que o seu servidor está hospedado está offline. Pode restaurar destas cópias de segurança para qualquer outra região e voltar a colocar o seu servidor online. Saiba mais sobre o geo-restauro a partir da cópia de segurança e restaurar o [artigo conceitos.](concepts-backup.md)

> [!IMPORTANT]
> O geo-restauro só é possível se forte o servidor com armazenamento de backup geo-redundante. Se desejar mudar de cópias de segurança locais redundantes para geo-redundantes para um servidor existente, deve descarregar usando o mysqldump do seu servidor existente e restaurá-lo para um servidor recém-criado configurado com cópias de segurança geo-redundantes.

## <a name="cross-region-read-replicas"></a>Réplicas de leitura transversal

Você pode usar réplicas de leitura de região cruzada para melhorar o seu plano de continuidade e recuperação de desastres. As réplicas de leitura são atualizadas assíncronea usando a tecnologia binária de replicação de registos do MySQL. Saiba mais sobre réplicas lidas, regiões disponíveis e como falhar a partir do artigo de [conceitos de réplicas lidos.](concepts-read-replicas.md) 

## <a name="faq"></a>FAQ

### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Onde é que a Azure Database para o MySQL armazena os dados dos clientes?
Por padrão, a Base de Dados Azure para o MySQL não move nem armazena os dados dos clientes para fora da região em que está implantado. No entanto, os clientes podem optar opcionalmente por permitir [cópias de segurança geo-redundantes](concepts-backup.md#backup-redundancy-options) ou criar [réplicas de leitura cruzada](concepts-read-replicas.md#cross-region-replication) para armazenar dados noutra região.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [cópias de segurança automáticas na Base de Dados Azure para MariaDB](concepts-backup.md).
- Saiba como restaurar utilizando [o portal Azure](howto-restore-server-portal.md) ou [o Azure CLI](howto-restore-server-cli.md).
- Saiba mais sobre [réplicas de leitura na Base de Dados Azure para MariaDB](concepts-read-replicas.md).

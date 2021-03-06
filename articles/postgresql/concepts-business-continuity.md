---
title: Continuidade do negócio - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a continuidade do negócio (ponto no tempo de restauração, interrupção do centro de dados, geo-restauro, réplicas) ao utilizar a Base de Dados Azure para PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: f8d8547c2d4900e6258f7360c50d8dfc17157832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100517249"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Visão geral da continuidade do negócio com base de dados Azure para PostgreSQL - Servidor Único

Esta visão geral descreve as capacidades que a Base de Dados Azure para PostgreSQL fornece para a continuidade do negócio e recuperação de desastres. Saiba mais sobre opções para recuperar de eventos disruptivos que possam causar perda de dados ou fazer com que a sua base de dados e aplicação fiquem indisponíveis. Saiba o que fazer quando um utilizador ou erro de aplicação afeta a integridade dos dados, uma região do Azure tem uma paragem ou a sua aplicação requer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Características que pode usar para proporcionar continuidade ao negócio

À medida que desenvolve o seu plano de continuidade de negócio, precisa de compreender o tempo máximo aceitável antes que a aplicação recupere totalmente após o evento disruptivo - este é o seu Objetivo de Tempo de Recuperação (RTO). Também precisa de compreender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que a aplicação pode tolerar perder ao recuperar após o evento disruptivo - este é o seu Objetivo de Ponto de Recuperação (RPO).

A Base de Dados do Azure para PostgreSQL proporciona funcionalidades de continuidade empresarial que incluem cópias de segurança georredundantes, com a capacidade de iniciar o georrestauro, e a implementação de réplicas de leitura numa região diferente. Cada um tem características diferentes para o tempo de recuperação e a perda de dados potenciais. Com a funcionalidade [Geo-restauro,](concepts-backup.md) um novo servidor é criado usando os dados de backup que são replicados a partir de outra região. O tempo total que leva para restaurar e recuperar depende do tamanho da base de dados e da quantidade de registos para recuperar. O tempo geral para estabelecer o servidor varia de poucos minutos a poucas horas. Com [réplicas lidas,](concepts-read-replicas.md)os registos de transações das primárias são assíncronos transmitidos para a réplica. Em caso de interrupção da base de dados primária devido a uma falha ao nível da zona ou a uma falha a nível da região, a falha na réplica fornece um RTO mais curto e uma redução da perda de dados.

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

Pode executar um **restauro pontual** para criar uma cópia do seu servidor num bom momento conhecido. Este ponto no tempo deve estar dentro do período de retenção de backup que configura para o seu servidor. Depois de os dados terem sido restaurados para o novo servidor, pode substituir o servidor original pelo servidor recentemente restaurado ou copiar os dados necessários do servidor restaurado para o servidor original.

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Utilize [o bloqueio de recursos Azure](../azure-resource-manager/management/lock-resources.md) para evitar a eliminação acidental do seu servidor.

## <a name="recover-from-an-azure-data-center-outage"></a>Recuperar de uma paragem do centro de dados Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma paralisação, causa uma perturbação de negócio que pode durar apenas alguns minutos, mas pode durar horas.

Uma opção é esperar que o seu servidor volte a estar online quando a paragem do data center terminar. Isto funciona para aplicações que podem dar ao luxo de ter o servidor offline por algum tempo, por exemplo, um ambiente de desenvolvimento. Quando um centro de dados tem uma paragem, não sabe quanto tempo a paralisação pode durar, por isso esta opção só funciona se não precisar do seu servidor durante algum tempo.

## <a name="geo-restore"></a>Georrestauro

A função de geo-restauro restaura o servidor utilizando cópias de segurança geo-redundantes. As cópias de segurança estão hospedadas na [região emparelhada](../best-practices-availability-paired-regions.md)do seu servidor. Você pode restaurar destes backups para qualquer outra região. O geo-restauro cria um novo servidor com os dados das cópias de segurança. Saiba mais sobre o geo-restauro a partir da cópia de segurança e restaurar o [artigo conceitos.](concepts-backup.md)

> [!IMPORTANT]
> O geo-restauro só é possível se forte o servidor com armazenamento de backup geo-redundante. Se pretender mudar de cópias de segurança locais redundantes para geo-redundantes para um servidor existente, deve descarregar utilizando pg_dump do seu servidor existente e restaurá-lo para um servidor recém-criado configurado com cópias de segurança geo-redundantes.

## <a name="cross-region-read-replicas"></a>Réplicas de leitura transversal
Você pode usar réplicas de leitura de região cruzada para melhorar o seu plano de continuidade e recuperação de desastres. As réplicas de leitura são atualizadas assíncronea usando a tecnologia de replicação física do PostgreSQL, e podem atrasar a primária. Saiba mais sobre réplicas lidas, regiões disponíveis e como falhar a partir do artigo de [conceitos de réplicas lidos.](concepts-read-replicas.md) 

## <a name="faq"></a>FAQ
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Onde é que a Azure Database para os dados dos clientes da pós-SQL armazena?
Por padrão, a Base de Dados Azure para PostgreSQL não move ou armazena os dados dos clientes para fora da região em que está implantado. No entanto, os clientes podem optar opcionalmente por permitir [cópias de segurança geo-redundantes](concepts-backup.md#backup-redundancy-options) ou criar [réplicas de leitura cruzada](concepts-read-replicas.md#cross-region-replication) para armazenar dados noutra região.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as [cópias de segurança automatizadas na Base de Dados Azure para PostgreSQL](concepts-backup.md). 
- Saiba como restaurar utilizando [o portal Azure](howto-restore-server-portal.md) ou [o Azure CLI](howto-restore-server-cli.md).
- Saiba mais sobre [réplicas de leitura na Base de Dados Azure para PostgreSQL](concepts-read-replicas.md).

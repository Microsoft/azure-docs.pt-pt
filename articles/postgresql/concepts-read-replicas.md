---
title: Ler réplicas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a funcionalidade de réplica de leitura na Base de Dados Azure para PostgreSQL - Servidor Único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 62ef47e7d8f98241009c1c1f3d8c111113be432c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99220773"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Leia réplicas na Base de Dados Azure para PostgreSQL - Servidor Único

A funcionalidade de réplica de leitura permite-lhe replicar dados de uma Base de Dados Azure para servidor PostgreSQL para um servidor apenas de leitura. As réplicas são **atualizadas assíncronamente** com a tecnologia de replicação física nativa do motor PostgreSQL. Pode replicar-se do servidor primário até cinco réplicas.

As réplicas são novos servidores que gere, à semelhança dos servidores da Base de Dados do Azure para PostgreSQL normais. Para cada réplica de leitura, você é cobrado para o cálculo provisionado em vCores e armazenamento em GB/mês.

Saiba como [criar e gerir réplicas.](howto-read-replicas-portal.md)

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e a escala das cargas de trabalho intensivas de leitura. As cargas de trabalho de leitura podem ser isoladas às réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para as primárias. As réplicas de leitura também podem ser implementadas numa região diferente e podem ser promovidas para ser um servidor de leitura/escrita em caso de recuperação de desastres.

Um cenário comum é fazer com que as cargas de trabalho bi e analíticas utilizem a réplica de leitura como fonte de dados para reportar.

Como as réplicas são apenas de leitura, não reduzem diretamente os encargos de capacidade de escrita nas primárias.

### <a name="considerations"></a>Considerações
A funcionalidade destina-se a cenários em que o lag é aceitável e destinado a descarregar consultas. Não se destina a cenários de replicação sincronizados onde se espera que os dados de replicação estejam atualizados. Haverá um atraso mensurável entre as primárias e a réplica. Isto pode ser em minutos ou mesmo horas dependendo da carga de trabalho e da latência entre a primária e a réplica. Os dados sobre a réplica eventualmente tornam-se consistentes com os dados sobre o primário. Utilize esta funcionalidade para cargas de trabalho que possam acomodar este atraso. 

> [!NOTE]
> Para a maioria das cargas de trabalho as réplicas de leitura oferecem atualizações quase em tempo real a partir das primárias. No entanto, com cargas primárias intensivas persistentes, o lag de replicação pode continuar a crescer e pode nunca ser capaz de recuperar com as primárias. Isto também pode aumentar o uso de armazenamento na primária, uma vez que os ficheiros WAL não são eliminados até que sejam recebidos na réplica. Se esta situação persistir, eliminar e recriar a réplica de leitura após o fim das cargas de trabalho intensivas de escrita é a opção de trazer a réplica de volta a um bom estado no que diz respeito ao atraso.
> Réplicas de leitura assíncronea não são adequadas para cargas de trabalho tão pesadas de escrita. Ao avaliar as réplicas de leitura para a sua aplicação, monitorize o lag na réplica para um ciclo completo de carga de trabalho de aplicação através dos seus tempos de pico e não-pico para aceder ao possível lag e ao RTO/RPO esperado em vários pontos do ciclo de carga.
> 
## <a name="cross-region-replication"></a>Replicação entre regiões
Pode criar uma réplica de leitura numa região diferente do seu servidor primário. A replicação transversal pode ser útil para cenários como o planeamento de recuperação de desastres ou a aproximação de dados aos seus utilizadores.

>[!NOTE]
> Os servidores de nível básico só suportam a replicação da mesma região.

Pode ter um servidor primário em qualquer [Base de Dados Azure para a região postgreSQL.](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql) Um servidor primário pode ter uma réplica na sua região emparelhada ou nas regiões réplicas universais. A imagem abaixo mostra quais as regiões réplicas disponíveis dependendo da sua região primária.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Ler regiões réplicas":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões réplicas universais
Pode sempre criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente do local onde o seu servidor principal esteja localizado. Estas são as regiões universais de réplica:

Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Eua Central, Leste Asiático, Leste dos EUA, Leste dos EUA 2, Japão Leste, Japão Ocidental, Coreia Central, Coreia do Sul, Norte Central dos EUA, Norte da Europa Central, Eua Central Do Sul, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Eua Ocidental, Eua Ocidental 2, West Central EUA.

### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, pode criar uma réplica de leitura na região emparelhada Azure do seu servidor primário. Se não conhece o par da sua região, pode aprender mais com o [artigo Azure Paired Regions](../best-practices-availability-paired-regions.md).

Se estiver a utilizar réplicas transversais para o planeamento de recuperação de desastres, recomendamos que crie a réplica na região emparelhada em vez de uma das outras regiões. As regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

Existem limitações a considerar: 
    
* Pares unidirecionais: Algumas regiões azures são emparelhadas apenas numa direção. Estas regiões incluem a Índia Ocidental, Brasil Sul. 
   Isto significa que um servidor primário na Índia Ocidental pode criar uma réplica no Sul da Índia. No entanto, um servidor primário no Sul da Índia não pode criar uma réplica na Índia Ocidental. Isto porque a região secundária da Índia Ocidental é a Índia do Sul, mas a região secundária do Sul da Índia não é a Índia Ocidental.


## <a name="create-a-replica"></a>Criar uma réplica
Quando inicia o fluxo de trabalho de réplicas, é criada uma Base de Dados Azure em branco para servidor PostgreSQL. O novo servidor está cheio dos dados que estavam no servidor primário. O tempo de criação depende da quantidade de dados sobre o primário e o tempo desde o último backup completo semanal. O tempo pode variar entre alguns minutos e várias horas.

Todas as réplicas estão ativadas para o armazenamento [de crescimento automático.](concepts-pricing-tiers.md#storage-auto-grow) A funcionalidade de crescimento automático permite que a réplica acompanhe os dados replicados e evite uma rutura na replicação causada por erros de armazenamento.

A funcionalidade de réplica de leitura utiliza a replicação física postgreSQL, não a replicação lógica. A replicação de streaming utilizando ranhuras de replicação é o modo de funcionamento predefinido. Quando necessário, o envio de registos é utilizado para o seu catch up.

Saiba como [criar uma réplica de leitura no portal Azure.](howto-read-replicas-portal.md)

Se o servidor PostgreSQL da sua fonte estiver encriptado com as teclas geridas pelo cliente, consulte a [documentação](concepts-data-encryption-postgresql.md) para obter considerações adicionais.

## <a name="connect-to-a-replica"></a>Ligar-se a uma réplica
Quando cria uma réplica, não herda as regras de firewall ou o ponto final de serviço VNet do servidor primário. Estas regras devem ser criadas de forma independente para a réplica.

A réplica herda a conta de administração do servidor primário. Todas as contas de utilizador no servidor primário são replicadas nas réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor primário.

Pode ligar-se à réplica utilizando o seu nome de anfitrião e uma conta de utilizador válida, como faria numa base de dados Azure regular para servidor PostgreSQL. Para um servidor chamado **minha réplica** com o nome de utilizador de administração **myadmin,** pode ligar-se à réplica utilizando psql:

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

A pedido, introduza a palavra-passe para a conta de utilizador.

## <a name="monitor-replication"></a>Monitorizar a replicação
A Azure Database for PostgreSQL fornece duas métricas para monitorização da replicação. As duas métricas são **Max Lag Através de Réplicas** e Replica **Lag**. Para aprender a ver estas métricas, consulte o **Monitor uma** secção réplica do artigo de réplica [de leitura](howto-read-replicas-portal.md).

A métrica **Max Lag Across Replicas** mostra o desfasamento nos bytes entre a réplica primária e a réplica mais atrasada. Esta métrica é aplicável e disponível apenas no servidor primário, e só estará disponível se pelo menos uma das réplicas de leitura estiver ligada à primária e a primária estiver em modo de replicação de streaming. As informações sobre o lag não mostram detalhes quando a réplica está em fase de recuperação com o primário utilizando os registos arquivados da primária num modo de replicação de envio de ficheiros.

A métrica **Replica Lag** mostra o tempo desde a última transação reproduzida. Se não houver transações no seu servidor primário, a métrica reflete este desfasamento temporal. Esta métrica é aplicável e está disponível apenas para servidores de réplicas. O Lag de réplica é calculado a partir da `pg_stat_wal_receiver` vista:

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Desaça um alerta para informá-lo quando o lag da réplica atingir um valor que não é aceitável para a sua carga de trabalho. 

Para obter informações adicionais, consultar o servidor primário diretamente para obter o lag de replicação em bytes em todas as réplicas.

> [!NOTE]
> Se um servidor primário ou uma réplica de leitura recomeçarem, o tempo necessário para reiniciar e recuperar é refletido na métrica Replica Lag.

## <a name="stop-replication--promote-replica"></a>Parar a replicação / Promover a réplica
Pode parar a replicação entre uma réplica primária e uma réplica a qualquer momento. A ação stop faz com que a réplica reinicie e promove a réplica para ser um servidor independente e autónomo que escreve. Os dados no servidor autónomo são os dados disponíveis no servidor de réplica no momento em que a replicação é interrompida. Quaisquer atualizações subsequentes na primária não são propagadas à réplica. No entanto, o servidor de réplica pode ter registos acumulados que ainda não são aplicados. Como parte do processo de reinício, a réplica aplica todos os registos pendentes antes de aceitar ligações ao cliente.  

### <a name="considerations"></a>Considerações
- Antes de parar a replicação numa réplica de leitura, verifique se o lag de replicação para garantir que a réplica tem todos os dados que necessita. 
- Como a réplica de leitura tem de aplicar todos os registos pendentes antes de poder ser feito um servidor autónomo, o RTO pode ser mais elevado para escrever cargas de trabalho pesadas quando a replicação de stop acontece, pois pode haver um atraso significativo na réplica. Por favor, preste atenção a isso ao planear promover uma réplica.
- O servidor de réplica promovido não pode ser transformado numa réplica novamente.
- Se promover uma réplica para ser o servidor primário, não é possível estabelecer a replicação de volta ao antigo servidor primário. Se quiser voltar à antiga região primária, pode estabelecer um novo servidor de réplica com um novo nome (ou) eliminar a antiga primária e criar uma réplica usando o antigo nome primário.
- Se tiver várias réplicas de leitura e se promover uma delas para ser o seu servidor primário, outros servidores de réplicas ainda estão ligados às primárias antigas. Pode ter de recriar réplicas do novo servidor promovido.

Quando paras de replicação, a réplica perde todas as ligações com as suas réplicas primárias anteriores e outras.

Aprenda a parar a [replicação a uma réplica.](howto-read-replicas-portal.md)

## <a name="failover-to-replica"></a>Falha na réplica

Em caso de falha do servidor primário, **não** é automaticamente falhada na réplica de leitura. 

Uma vez que a replicação é assíncronea, pode haver um desfasamento considerável entre a primária e a réplica. A quantidade de lag é influenciada por uma série de fatores, tais como o tipo de carga de trabalho que corre no servidor primário e a latência entre o servidor primário e o servidor de réplica. Em casos típicos com carga de trabalho nominal de escrita, espera-se um lag de réplica entre alguns segundos e poucos minutos. No entanto, nos casos em que a primária funciona com uma carga de trabalho intensiva de escrita muito pesada e a réplica não está a recuperar suficientemente rápido, o lag pode ser muito maior. Pode rastrear o lag de replicação de cada réplica utilizando o *Lag métrico de Réplica*. Esta métrica mostra o tempo desde a última transação reproduzida na réplica. Recomendamos que identifique o atraso médio observando o lag da réplica durante um período de tempo. Pode definir um alerta no lag de réplica, de modo que se for fora do seu alcance esperado, será notificado para tomar medidas.

> [!Tip]
> Se falhar na réplica, o lag no momento em que desvincular a réplica da primária indicará quantos dados são perdidos.

Uma vez que tenha decidido que quer falhar para uma réplica, 

1. Pare a replicação na réplica<br/>
   Este passo é necessário para fazer com que o servidor de réplica se torne um servidor autónomo e seja capaz de aceitar escritas. Como parte deste processo, o servidor de réplica reiniciará e será desvinculado da primária. Uma vez iniciado a replicação stop, o processo de backend normalmente leva alguns minutos para aplicar quaisquer registos residuais que ainda não foram aplicados e para abrir a base de dados como um servidor de leitura-writeable. Consulte a secção de replicação de [paragem](#stop-replication--promote-replica) deste artigo para entender as implicações desta ação.
    
2. Aponte a sua aplicação para a (antiga) réplica<br/>
   Cada servidor tem uma cadeia de ligação única. Atualize o fio de ligação da aplicação para apontar para a (antiga) réplica em vez da primária.
    
Uma vez que a sua aplicação esteja a processar com sucesso as leituras e as escritas, completou o failover. A quantidade de tempo de inatividade das suas experiências de aplicação dependerá de quando detetar um problema e completar os passos 1 e 2 acima.

### <a name="disaster-recovery"></a>Recuperação após desastre

Quando há um grande evento de desastre, como a disponibilidade de zonas ou falhas regionais, você pode realizar uma operação de recuperação de desastres promovendo a sua réplica de leitura. A partir do portal UI, pode navegar para o servidor de réplicas de leitura. Em seguida, clique no separador de replicação e pode parar a réplica para promovê-la para ser um servidor independente. Em alternativa, pode utilizar o [CLI Azure](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) para parar e promover o servidor de réplicas.

## <a name="considerations"></a>Considerações

Esta secção resume considerações sobre a funcionalidade de réplica de leitura.

### <a name="prerequisites"></a>Pré-requisitos
Leia réplicas e [descodões lógicas](concepts-logical.md) ambos dependem do postgres escrever antecipadamente log (WAL) para obter informações. Estas duas funcionalidades precisam de diferentes níveis de registo de postgres. A descodão lógica precisa de um nível mais elevado de registo do que réplicas lidas.

Para configurar o nível certo de registo, utilize o parâmetro de suporte de replicação Azure. O suporte de replicação Azure tem três opções de definição:

* **Off** - Coloca a menor informação no WAL. Esta definição não está disponível na maioria dos servidores Azure Database para servidores PostgreSQL.  
* **Réplica** - Mais verboso do que **desligado**. Este é o nível mínimo de exploração madeireira necessário para [que as réplicas de leitura](concepts-read-replicas.md) funcionem. Esta definição é o padrão na maioria dos servidores.
* **Lógico** - Mais verboso do que **réplica.** Este é o nível mínimo de exploração madeireira para a descodão lógica para funcionar. As réplicas de leitura também funcionam neste cenário.


### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como uma nova Base de Dados Azure para servidor PostgreSQL. Um servidor existente não pode ser transformado numa réplica. Não se pode criar uma réplica de outra réplica lida.

### <a name="replica-configuration"></a>Configuração de réplica
Uma réplica é criada utilizando as mesmas definições de computação e armazenamento que as primárias. Após a criação de uma réplica, várias configurações podem ser alteradas, incluindo o período de armazenamento e retenção de backup.

As regras de firewall, as regras de rede virtual e as definições de parâmetros não são herdadas do servidor primário para a réplica quando a réplica é criada ou posteriormente.

### <a name="scaling"></a>Dimensionamento
Escalar vCores ou entre Propósito Geral e Memória Otimizada:
* O PostgreSQL requer que a `max_connections` definição num servidor secundário seja [maior ou igual à definição na primária,](https://www.postgresql.org/docs/current/hot-standby.html)caso contrário o secundário não será iniciado.
* Na Base de Dados Azure para PostgreSQL, as ligações máximas permitidas para cada servidor são fixadas ao sku compute uma vez que as ligações ocupam a memória. Você pode saber mais sobre o [mapeamento entre max_connections e compute skus](concepts-limits.md).
* **Escalonamento**: Primeiro escalone o cálculo de uma réplica, em seguida, escalone o primário. Esta ordem evitará que os erros violem a `max_connections` exigência.
* **Escalonamento :** Primeiro escale o cálculo da primária, em seguida, reduza a réplica. Se tentar escalar a réplica abaixo da primária, haverá um erro, uma vez que isto viola o `max_connections` requisito.

Armazenamento de escalonamento:
* Todas as réplicas têm armazenamento automático habilitado para evitar problemas de replicação de uma réplica completa de armazenamento. Esta definição não pode ser desativada.
* Também pode escalar manualmente o armazenamento, como faria em qualquer outro servidor


### <a name="basic-tier"></a>Escalão básico
Os servidores de nível básico só suportam a replicação da mesma região.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL exige que](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) o valor do `max_prepared_transactions` parâmetro na réplica de leitura seja maior ou igual ao valor primário; caso contrário, a réplica não será iniciada. Se quiser mudar `max_prepared_transactions` nas primárias, primeiro altere-a nas réplicas.

### <a name="stopped-replicas"></a>Réplicas paradas
Se parar a replicação entre um servidor primário e uma réplica de leitura, a réplica reinicia para aplicar a alteração. A réplica parada torna-se um servidor autónomo que aceita tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente.

### <a name="deleted-primary-and-standalone-servers"></a>Servidores primários e autónomos eliminados
Quando um servidor primário é eliminado, todas as suas réplicas de leitura tornam-se servidores autónomos. As réplicas são reiniciadas para refletir esta mudança.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [criar e gerir réplicas de leitura no portal Azure.](howto-read-replicas-portal.md)
* Saiba como [criar e gerir réplicas de leitura na ALI Azure e NA API REST.](howto-read-replicas-cli.md)
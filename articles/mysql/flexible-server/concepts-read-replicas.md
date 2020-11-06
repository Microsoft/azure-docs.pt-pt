---
title: Ler réplicas - Azure Database for MySQL - Servidor Flexível
description: 'Saiba mais sobre réplicas de leitura na Base de Dados Azure para o MySQL Flexible Server: criar réplicas, ligar-se a réplicas, monitorizar a replicação e parar a replicação.'
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: ae73885016a40cd3cf79de968ca7c07c51f1400a
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94336068"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Leia réplicas na Base de Dados Azure para MySQL - Servidor Flexível

> [!IMPORTANT]
> Leia réplicas na Base de Dados Azure para MySQL - Servidor Flexível está em pré-visualização.

MySQL é um dos motores de base de dados populares para executar aplicações web e móveis à escala da Internet. Muitos dos nossos clientes usam-na para os seus serviços de educação online, serviços de streaming de vídeo, soluções de pagamento digital, plataformas de e-commerce, serviços de jogos, portais de notícias, governo e sites de saúde. Estes serviços são necessários para servir e escalar à medida que o tráfego na web ou aplicação móvel aumenta.

Do lado das aplicações, a aplicação é normalmente desenvolvida em Java ou php e migrada para funcionar em conjuntos de escala de máquina virtual Azure ou Azure App Services ou são contentorizadas para funcionar no Serviço Azure Kubernetes (AKS). Com o conjunto de escalas de máquinas virtuais, o App Service ou a AKS como infraestrutura subjacente, o dimensionamento de aplicações é simplificado através do fornecimento instantâneo de novos VMs e replicando os componentes apátridas de aplicações para atender aos pedidos, mas muitas vezes, a base de dados acaba por ser um estrangulamento como componente imponente centralizado.

A funcionalidade de réplica de leitura permite-lhe replicar dados de uma Base de Dados Azure para o servidor flexível MySQL para um servidor apenas de leitura. Pode replicar-se a partir do servidor de origem até **10** réplicas. As réplicas são atualizadas de forma assíncrona com a tecnologia de replicação baseada na posição dos ficheiros de registo binário nativo (binlog) do motor MySQL. Para saber mais sobre a replicação do binlog, consulte a visão geral da [replicação do binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

As réplicas são novos servidores que geres similares à base de dados Azure de origem para servidores flexíveis MySQL. Incorrerá em taxas de faturação para cada réplica lida com base no cálculo provisionado em vCores e armazenamento em GB/mês. Para mais informações, consulte os [preços.](./concepts-compute-storage.md#pricing)

Para saber mais sobre funcionalidades e problemas de replicação do MySQL, consulte a [documentação de replicação mySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Comunicação sem preconceitos
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra _escravo._ O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque atualmente é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

## <a name="common-use-cases-for-read-replica"></a>Casos de uso comum para réplica de leitura

A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e a escala das cargas de trabalho intensivas de leitura. As cargas de trabalho de leitura podem ser isoladas às réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para a fonte.

Os cenários comuns são:

* Dimensionamento de cargas de trabalho provenientes da aplicação utilizando um proxy de conexão leve como [o ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) ou usando padrão baseado em microserviços para escalar as suas consultas de leitura provenientes da aplicação para ler réplicas
* Cargas de trabalho de relatórios bi ou analíticos podem usar réplicas de leitura como fonte de dados para reportar
* Para o cenário de IoT ou Fabricação onde as informações de telemetria são ingeridas no motor da base de dados MySQL, enquanto réplicas de leitura múltiplas são usadas para reportar dados

Como as réplicas são apenas de leitura, não reduzem diretamente os encargos de capacidade de escrita na fonte. Esta funcionalidade não está direcionada para cargas de trabalho de escrita intensa.

A funcionalidade de réplica de leitura utiliza a replicação assíncronea mySQL. A funcionalidade não se destina a cenários de replicação sincronizados. Haverá um atraso mensurável entre a fonte e a réplica. Os dados sobre a réplica eventualmente tornam-se consistentes com os dados da fonte. Utilize esta funcionalidade para cargas de trabalho que possam acomodar este atraso.

## <a name="create-a-replica"></a>Criar uma réplica

Se um servidor de origem não tiver servidores de réplica existentes, a fonte reiniciará primeiro para se preparar para a replicação.

Quando inicia o fluxo de trabalho de réplicas, é criada uma Base de Dados Azure em branco para o servidor MySQL. O novo servidor está preenchido com os dados que estavam no servidor de origem. O tempo de criação depende da quantidade de dados sobre a fonte e do tempo desde o último backup completo semanal. O tempo pode variar entre alguns minutos e várias horas.

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração do servidor que a fonte. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. O servidor de réplica é sempre criado no mesmo grupo de recursos, na mesma localização e na mesma subscrição que o servidor de origem. Se pretender criar um servidor de réplica para um grupo de recursos diferente ou uma subscrição diferente, pode [mover o servidor de réplica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) após a criação. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores à fonte para garantir que a réplica seja capaz de acompanhar a origem.

Saiba como [criar uma réplica de leitura no portal Azure.](how-to-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Ligar-se a uma réplica

Na criação, uma réplica herda o método de conectividade do servidor de origem. Não é possível alterar o método de conectividade da réplica. Por exemplo, se o servidor de origem tiver **acesso privado (VNet Integration)** então a réplica não pode estar no **acesso público (endereços IP permitidos)**.

A réplica herda a conta de administração do servidor de origem. Todas as contas de utilizador no servidor de origem são replicadas nas réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor de origem.

Pode ligar-se à réplica utilizando o seu nome de anfitrião e uma conta de utilizador válida, como faria numa base de dados Azure regular para servidor flexível MySQL. Para um servidor chamado **myreplica** com o nome de utilizador de administração **myadmin,** pode ligar-se à réplica utilizando o mysql CLI:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

A pedido, introduza a palavra-passe para a conta de utilizador.

## <a name="monitor-replication"></a>Monitorizar a replicação

A base de dados Azure para o MySQL Flexible Server fornece o **lag de replicação em segundos** métrica no Azure Monitor. Esta métrica está disponível apenas para réplicas. Esta métrica é calculada usando a `seconds_behind_master` métrica disponível no comando do MySQL. `SHOW SLAVE STATUS` Desaça um alerta para informá-lo quando o lag de replicação atingir um valor que não é aceitável para a sua carga de trabalho.

Se vir um atraso de replicação aumentado, consulte a [latência da replicação da resolução de problemas](./../howto-troubleshoot-replication-latency.md) para resolver problemas e compreender possíveis causas.

## <a name="stop-replication"></a>Parar replicação

Pode parar a replicação entre uma fonte e uma réplica. Após a replicação ser interrompida entre um servidor de origem e uma réplica de leitura, a réplica torna-se um servidor autónomo. Os dados no servidor autónomo são os dados disponíveis na réplica no momento em que o comando de replicação de paragem foi iniciado. O servidor autónomo não alcança o servidor de origem.

Quando opta por parar a replicação de uma réplica, perde todas as ligações com a sua fonte anterior e outras réplicas. Não há falha automatizada entre uma fonte e a sua réplica.

> [!IMPORTANT]
> O servidor autónomo não pode ser transformado numa réplica novamente.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados necessários.

Aprenda a parar a [replicação a uma réplica.](how-to-read-replicas-portal.md)

## <a name="failover"></a>Ativação pós-falha

Não existe falha automatizada entre servidores de origem e réplicas. 

As réplicas de leitura destinam-se a dimensionamento de cargas de trabalho intensivas de leitura e não são concebidas para satisfazer necessidades de alta disponibilidade de um servidor. Não existe falha automatizada entre servidores de origem e réplicas. Parar a replicação na réplica de leitura para a colocar on-line no modo de escrita de leitura é o meio pelo qual este failover manual é realizado.

Como a replicação é assíncronea, há um desfasamento entre a fonte e a réplica. A quantidade de lag pode ser influenciada por uma série de fatores como o peso da carga de trabalho que funciona no servidor de origem e a latência entre centros de dados. Na maioria dos casos, o atraso da réplica varia entre alguns segundos e alguns minutos. Pode rastrear o seu lag de replicação real usando o *Lag métrico de Réplica,* que está disponível para cada réplica. Esta métrica mostra o tempo desde a última transação reproduzida. Recomendamos que identifique qual é o seu atraso médio observando o seu atraso de réplica durante um período de tempo. Pode definir um alerta no lag de réplica, para que, se for fora do alcance esperado, possa tomar medidas.

> [!Tip]
> Se falhar na réplica, o lag no momento em que desvincular a réplica da fonte indicará a quantidade de dados perdidos.

Uma vez que tenha decidido que quer falhar para uma réplica, 

1. Pare a replicação na réplica<br/>
   Este passo é necessário para que o servidor de réplica seja capaz de aceitar escritas. Como parte deste processo, o servidor de réplica será desvinculado da fonte. Uma vez iniciado a replicação stop, o processo de backend normalmente demora cerca de 2 minutos a ser concluído. Consulte a secção de replicação de [paragem](#stop-replication) deste artigo para entender as implicações desta ação.
    
2. Aponte a sua aplicação para a (antiga) réplica<br/>
   Cada servidor tem uma cadeia de ligação única. Atualize a sua aplicação para indicar a (antiga) réplica em vez da fonte.
    
Uma vez que a sua aplicação esteja a processar com sucesso as leituras e as escritas, completou o failover. A quantidade de tempo de inatividade das suas experiências de aplicação dependerá de quando detetar um problema e completar os passos 1 e 2 acima.

## <a name="considerations-and-limitations"></a>Considerações e limitações

| Cenário | Limitação/Consideração |
|:-|:-|
| Réplica no servidor com HA redundante de zona ativada | Não suportado |
| Região transversal leia replicação | Não suportado |
| Preços | O custo de execução do servidor de réplica baseia-se na região onde o servidor de réplica está em execução |
| Reinício do servidor de origem | Quando se cria uma réplica para uma fonte que não tem réplicas existentes, a fonte reinicia primeiro para se preparar para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico |
| Novas réplicas | Uma réplica de leitura é criada como uma nova Base de Dados Azure para o servidor flexível MySQL. Um servidor existente não pode ser transformado numa réplica. Não se pode criar uma réplica de outra réplica de leitura |
| Configuração de réplica | Uma réplica é criada utilizando a mesma configuração do servidor que a fonte. Após a criação de uma réplica, várias configurações podem ser alteradas independentemente do servidor de origem: geração de cálculo, vCores, armazenamento e período de retenção de backup. O nível de cálculo também pode ser alterado de forma independente.<br> <br> **IMPORTANTE**  <br> - Antes de uma configuração do servidor de origem ser atualizada para novos valores, atualize a configuração da réplica para valores iguais ou maiores. Esta ação garante que a réplica pode acompanhar quaisquer alterações feitas no original. <br/> O método de conectividade e as definições de parâmetros são herdados do servidor de origem para a réplica quando a réplica é criada. Depois, as regras da réplica são independentes. |
| Réplicas paradas | Se parar a replicação entre um servidor de origem e uma réplica de leitura, a réplica parada torna-se um servidor autónomo que aceita tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente. |
| Fonte eliminada e servidores autónomos | Quando um servidor de origem é eliminado, a replicação é interrompida para todas as réplicas lidas. Estas réplicas tornam-se automaticamente servidores autónomos e podem aceitar tanto as leituras como as escritas. O servidor de origem em si é eliminado. |
| Contas de utilizador | Os utilizadores do servidor de origem são replicados nas réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador disponíveis no servidor de origem. |
| Parâmetros do servidor | Para impedir que os dados fiquem dessincronizados e evitar potenciais perdas de dados ou corrupção, a atualização de alguns parâmetros de servidor é bloqueada ao utilizar réplicas de leitura. <br> Os seguintes parâmetros do servidor estão bloqueados tanto nos servidores de origem como em réplicas:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> O [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parâmetro está bloqueado nos servidores de réplicas. <br> Para atualizar um dos parâmetros acima no servidor de origem, por favor, elimine os servidores de réplicas, atualize o valor do parâmetro na fonte e recrie réplicas. |
| Outro | - A criação de uma réplica de uma réplica não é suportada. <br> - As tabelas de memória podem fazer com que as réplicas fiquem dessincronizadas. Esta é uma limitação da tecnologia de replicação MySQL. Leia mais na documentação de referência do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para obter mais informações. <br>- Certifique-se de que as tabelas do servidor de origem têm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre a fonte e as réplicas.<br>- Rever a lista completa das limitações de replicação do MySQL na [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) |

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar e gerir réplicas de leitura usando o portal Azure](how-to-read-replicas-portal.md)
- Saiba como [criar e gerir réplicas de leitura usando o Azure CLI](how-to-read-replicas-cli.md)

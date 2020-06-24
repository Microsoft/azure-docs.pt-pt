---
title: Leia réplicas - Base de Dados Azure para MySQL.
description: 'Saiba mais sobre réplicas lidas na Base de Dados Azure para o MySQL: escolher regiões, criar réplicas, ligar-se a réplicas, monitorizar a replicação e parar a replicação.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: 35aedbc4a3d1dcb87b23633acd413c4a55448ef9
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84710410"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Réplicas de leitura na Base de Dados do Azure para MySQL

A funcionalidade de réplica de leitura permite replicar dados de um servidor de Base de Dados do Azure para MySQL para um servidor só de leitura. Pode replicar do servidor mestre para até cinco réplicas. As réplicas são atualizadas de forma assíncrona com a tecnologia de replicação baseada na posição dos ficheiros de registo binário nativo (binlog) do motor MySQL. Para saber mais sobre a replicação do binlog, consulte a visão geral da [replicação do binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

As réplicas são novos servidores que geres similares ao Azure Database regular para servidores MySQL. Para cada réplica de leitura, você é cobrado para o cálculo provisionado em vCores e armazenamento em GB/mês.

Para saber mais sobre funcionalidades e problemas de replicação do MySQL, consulte a [documentação de replicação mySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Comunicação sem preconceitos
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra _escravo._ O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque atualmente é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e a escala das cargas de trabalho intensivas de leitura. As cargas de trabalho de leitura podem ser isoladas às réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para o mestre.

Um cenário comum é fazer com que as cargas de trabalho bi e analíticas utilizem a réplica de leitura como fonte de dados para reportar.

Como as réplicas são apenas de leitura, não reduzem diretamente os encargos de capacidade de escrita para o mestre. Esta funcionalidade não é direcionada a cargas de trabalho intensivas.

A funcionalidade de réplica de leitura utiliza a replicação assíncronea mySQL. A funcionalidade não se destina a cenários de replicação sincronizados. Haverá um atraso mensurável entre o mestre e a réplica. Os dados sobre a réplica eventualmente tornam-se consistentes com os dados do mestre. Utilize esta funcionalidade para cargas de trabalho que possam acomodar este atraso.

## <a name="cross-region-replication"></a>Replicação inter-região
Pode criar uma réplica de leitura numa região diferente do seu servidor principal. A replicação transversal pode ser útil para cenários como o planeamento de recuperação de desastres ou a aproximação de dados aos seus utilizadores.

Pode ter um servidor principal em qualquer [Base de Dados Azure para a região mySQL.](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)  Um servidor principal pode ter uma réplica na sua região emparelhada ou nas regiões universais de réplicas. A imagem abaixo mostra quais as regiões réplicas disponíveis dependendo da sua região principal.

[![Ler regiões réplicas](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões réplicas universais
Pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente do local onde o seu servidor principal está localizado. As regiões de réplica universal apoiadas incluem:

Austrália Leste, Austrália Sudeste, Eua Central, Ásia Oriental, Leste dos EUA, Leste dos EUA 2, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul, Norte central dos EUA, Norte da Europa, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Eua Ocidental.

*West US 2 está temporariamente indisponível como uma localização réplica da região transversal.

### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões universais de réplicas, pode criar uma réplica de leitura na região emparelhada Azure do seu servidor principal. Se não conhece o par da sua região, pode aprender mais com o [artigo Azure Paired Regions](../best-practices-availability-paired-regions.md).

Se estiver a utilizar réplicas transversais para o planeamento de recuperação de desastres, recomendamos que crie a réplica na região emparelhada em vez de uma das outras regiões. As regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

No entanto, existem limitações a considerar: 

* Disponibilidade regional: A Azure Database for MySQL está disponível em West US 2, France Central, UAE North e Germany Central. No entanto, as suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões azures são emparelhadas apenas numa direção. Estas regiões incluem a Índia Ocidental, o Sul do Brasil e o Gov Virginia dos EUA. 
   Isto significa que um servidor principal na Índia Ocidental pode criar uma réplica no Sul da Índia. No entanto, um servidor principal no Sul da Índia não pode criar uma réplica na Índia Ocidental. Isto porque a região secundária da Índia Ocidental é a Índia do Sul, mas a região secundária do Sul da Índia não é a Índia Ocidental.

## <a name="create-a-replica"></a>Criar uma réplica

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores MySQL nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor principal está num destes níveis de preços.

Se um servidor principal não tiver servidores de réplicas existentes, o mestre reiniciará primeiro para se preparar para a replicação.

Quando inicia o fluxo de trabalho de réplicas, é criada uma Base de Dados Azure em branco para o servidor MySQL. O novo servidor está cheio dos dados que estavam no servidor principal. O tempo de criação depende da quantidade de dados sobre o mestre e do tempo desde o último backup completo semanal. O tempo pode variar entre alguns minutos e várias horas. O servidor de réplica é sempre criado no mesmo grupo de recursos e na mesma subscrição que o servidor principal. Se pretender criar um servidor de réplica para um grupo de recursos diferente ou uma subscrição diferente, pode [mover o servidor de réplica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) após a criação.

Todas as réplicas estão ativadas para o armazenamento [de crescimento automático.](concepts-pricing-tiers.md#storage-auto-grow) A funcionalidade de crescimento automático permite que a réplica acompanhe os dados replicados e evite uma interrupção na replicação causada por erros fora de armazenamento.

Saiba como [criar uma réplica de leitura no portal Azure.](howto-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Ligar-se a uma réplica

Na criação, uma réplica herda as regras de firewall do servidor principal. Depois, estas regras são independentes do servidor principal.

A réplica herda a conta de administração do servidor principal. Todas as contas de utilizador no servidor principal são replicadas nas réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor principal.

Pode ligar-se à réplica utilizando o seu nome de anfitrião e uma conta de utilizador válida, como faria numa base de dados Azure regular para o servidor MySQL. Para um servidor chamado **myreplica** com o nome de utilizador de administração **myadmin,** pode ligar-se à réplica utilizando o mysql CLI:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

A pedido, introduza a palavra-passe para a conta de utilizador.

## <a name="monitor-replication"></a>Monitorizar a replicação

A base de dados Azure para o MySQL fornece o **lag de replicação em segundos** métrica no Azure Monitor. Esta métrica está disponível apenas para réplicas.

Esta métrica é calculada usando a `seconds_behind_master` métrica disponível no comando do MySQL. `SHOW SLAVE STATUS`

Desaça um alerta para informá-lo quando o lag de replicação atingir um valor que não é aceitável para a sua carga de trabalho.

## <a name="stop-replication"></a>Parar replicação

Pode parar a replicação entre um mestre e uma réplica. Após a replicação ser interrompida entre um servidor principal e uma réplica de leitura, a réplica torna-se um servidor autónomo. Os dados no servidor autónomo são os dados disponíveis na réplica no momento em que o comando de replicação de paragem foi iniciado. O servidor autónomo não alcança o servidor principal.

Quando escolhes parar a replicação a uma réplica, perde todas as ligações com o seu mestre anterior e outras réplicas. Não há falha automatizada entre um mestre e a sua réplica.

> [!IMPORTANT]
> O servidor autónomo não pode ser transformado numa réplica novamente.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados necessários.

Aprenda a parar a [replicação a uma réplica.](howto-read-replicas-portal.md)

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Escalões de preço

Atualmente, as réplicas de leitura só estão disponíveis nos níveis de preços otimizados para fins gerais e memória.

> [!NOTE]
> O custo de execução do servidor de réplica baseia-se na região onde o servidor de réplica está em execução.

### <a name="master-server-restart"></a>Reinício do servidor principal

Quando se cria uma réplica para um mestre que não tem réplicas existentes, o mestre recomeçará a preparar-se para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como uma nova Base de Dados Azure para o servidor MySQL. Um servidor existente não pode ser transformado numa réplica. Não se pode criar uma réplica de outra réplica lida.

### <a name="replica-configuration"></a>Configuração de réplica

Uma réplica é criada utilizando a mesma configuração do servidor que o mestre. Após a criação de uma réplica, várias configurações podem ser alteradas independentemente do servidor principal: geração de cálculo, vCores, armazenamento e período de retenção de backup. O nível de preços também pode ser alterado de forma independente, exceto de ou para o nível básico.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada para novos valores, atualize a configuração de réplica para valores iguais ou superiores. Esta ação garante que a réplica pode acompanhar quaisquer alterações feitas no mestre.

As regras de firewall e as definições de parâmetros são herdadas do servidor principal para a réplica quando a réplica é criada. Depois, as regras da réplica são independentes.

### <a name="stopped-replicas"></a>Réplicas paradas

Se parar a replicação entre um servidor principal e uma réplica de leitura, a réplica parada torna-se um servidor autónomo que aceita tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autónomos apagados

Quando um servidor principal é eliminado, a replicação é interrompida para todas as réplicas lidas. Estas réplicas tornam-se automaticamente servidores autónomos e podem aceitar tanto as leituras como as escritas. O servidor principal em si é apagado.

### <a name="user-accounts"></a>Contas de utilizador

Os utilizadores do servidor principal são replicados nas réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador disponíveis no servidor principal.

### <a name="server-parameters"></a>Parâmetros do servidor

Para impedir que os dados fiquem dessincronizados e evitar potenciais perdas de dados ou corrupção, a atualização de alguns parâmetros de servidor é bloqueada ao utilizar réplicas de leitura.

Os seguintes parâmetros do servidor estão bloqueados tanto nos servidores master como replica:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

O [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parâmetro está bloqueado nos servidores de réplicas. 

Para atualizar um dos parâmetros acima no servidor principal, por favor, elimine os servidores de réplicas, atualize o valor do parâmetro no master e recrie réplicas.

### <a name="other"></a>Outro

- Os identificadores globais de transações (GTID) não são suportados.
- A criação de uma réplica de uma réplica não é suportada.
- As tabelas de memória podem fazer com que as réplicas fiquem dessincronizadas. Esta é uma limitação da tecnologia de replicação MySQL. Leia mais na documentação de referência do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para obter mais informações.
- Certifique-se de que as tabelas do servidor principal têm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre o mestre e as réplicas.
- Reveja a lista completa das limitações de replicação do [MySQL na documentação MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar e gerir réplicas de leitura usando o portal Azure](howto-read-replicas-portal.md)
- Saiba como [criar e gerir réplicas de leitura usando o ALI Azure CLI e REST API](howto-read-replicas-cli.md)

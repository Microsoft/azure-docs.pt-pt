---
title: Arquiteturas de referência para bases de dados oracle em Azure | Microsoft Docs
description: Referências arquiteturas para executar bases de dados Da Oracle Database Enterprise Edition em Microsoft Azure Virtual Machines.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: bbaf34c977546891c6ac05fbd4b5feb15f333e04
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737817"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Arquiteturas de referência para Oracle Database Enterprise Edition em Azure

Este guia detalha informações sobre a implementação de uma base de dados oracle altamente disponível no Azure. Além disso, este guia mergulha em considerações de recuperação de desastres. Estas arquiteturas foram criadas com base em implementações de clientes. Este guia aplica-se apenas à Oracle Database Enterprise Edition.

Se está interessado em saber mais sobre maximizar o desempenho da sua base de dados Oráculo, consulte [Architect a Oráculo DB](oracle-design.md).

## <a name="assumptions"></a>Pressupostos

- Você tem uma compreensão dos diferentes conceitos de Azure, tais como [zonas de disponibilidade](../../../availability-zones/az-overview.md)
- Está a executar a Oracle Database Enterprise Edition 12c ou mais tarde
- Você está ciente e reconhece as implicações de licenciamento ao usar as soluções neste artigo

## <a name="high-availability-for-oracle-databases"></a>Alta disponibilidade para bases de dados da Oracle

Alcançar uma elevada disponibilidade na nuvem é uma parte importante do planeamento e design de cada organização. O Microsoft Azure oferece [zonas de disponibilidade](../../../availability-zones/az-overview.md) e conjuntos de disponibilidade (a serem utilizados em regiões onde as zonas de disponibilidade não estão disponíveis). Leia mais sobre [a gestão da disponibilidade das suas máquinas virtuais](../../manage-availability.md) para projetar para a nuvem.

Além de ferramentas e ofertas nativas em nuvem, a Oracle fornece soluções para uma alta disponibilidade, como [Oracle Data Guard,](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7) [Data Guard com FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)e [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) que podem ser configurados no Azure. Este guia abrange arquiteturas de referência para cada uma destas soluções.

Finalmente, ao migrar ou criar aplicações para a nuvem, é importante ajustar o seu código de aplicação para adicionar padrões nativos da nuvem, tais como padrão de [retagem](/azure/architecture/patterns/retry) e [padrão de disjuntor](/azure/architecture/patterns/circuit-breaker)de circuitos . Padrões adicionais definidos no [guia Cloud Design Patterns](/azure/architecture/patterns/) podem ajudar a sua aplicação a ser mais resistente.

### <a name="oracle-rac-in-the-cloud"></a>Oráculo RAC na nuvem

O Oracle Real Application Cluster (RAC) é uma solução da Oracle para ajudar os clientes a alcançar altos resultados, tendo muitas instâncias a aceder a um armazenamento de base de dados (padrão de arquitetura partilhado). Embora o Oracle RAC também possa ser usado para alta disponibilidade no local, o Oracle RAC por si só não pode ser usado para alta disponibilidade na nuvem, uma vez que apenas protege contra falhas de nível de instância e não contra falhas de nível de rack ou de nível de dados. Por esta razão, a Oracle recomenda a utilização da Oracle Data Guard com a sua base de dados (seja uma instância única ou RAC) para uma elevada disponibilidade. Os clientes geralmente exigem um SLA elevado para executar as suas aplicações críticas de missão. A Oracle RAC não é atualmente certificada ou apoiada pela Oracle em Azure. No entanto, o Azure oferece funcionalidades como a Azure oferece Zonas de Disponibilidade e janelas de manutenção planeadas para ajudar a proteger contra falhas de nível de exemplo. Além disso, os clientes podem usar tecnologias como Oracle Data Guard, Oracle GoldenGate e Oracle Sharding para alto desempenho e resiliência, protegendo as suas bases de dados de falhas ao nível do rack, bem como falhas de nível de datacenter e geopolítica.

Ao executar as Bases de Dados da Oracle em [várias zonas de disponibilidade](../../../availability-zones/az-overview.md) em conjunto com a Oracle Data Guard ou GoldenGate, os clientes são capazes de obter um SLA de 99,99%. Nas regiões de Azure onde as zonas de disponibilidade ainda não estão presentes, os clientes podem utilizar [Conjuntos de Disponibilidade](../../manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) e alcançar um SLA de 99,95%.

>NOTA: Pode ter um alvo de uptime muito superior ao SLA de uptime fornecido pela Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Recuperação de desastres para bases de dados da Oracle

Ao hospedar as suas aplicações críticas de missão na nuvem, é importante projetar para alta disponibilidade e recuperação de desastres.

Para a Oracle Database Enterprise Edition, a Oracle Data Guard é uma característica útil para a recuperação de desastres. Pode configurar uma instância de base de dados de espera numa [região de Azure emparelhada](../../../best-practices-availability-paired-regions.md) e configurar o fracasso da Data Guard para a recuperação de desastres. Para perda zero de dados, recomenda-se que implemente uma instância oracle Data Guard Far Sync, além da Ative Data Guard. 

Considere configurar a instância Data Guard Far Sync numa zona de disponibilidade diferente da sua base de dados primária oracle se a sua aplicação permitir a latência (é necessário um teste minucioso). Utilize um modo **de Disponibilidade Máxima** para configurar o transporte sincronizado dos seus ficheiros de redo para a instância Far Sync. Estes ficheiros são então transferidos assíncroneamente para a base de dados de espera. 

Se a sua aplicação não permitir a perda de desempenho ao configurar a instância Far Sync em outra zona de disponibilidade no modo **Disponibilidade Máxima** (sincronizada), poderá configurar uma instância Far Sync na mesma zona de disponibilidade que a sua base de dados primária. Para obter uma disponibilidade adicional, considere configurar várias instâncias Far Sync perto da sua base de dados primária e pelo menos um caso perto da sua base de dados de espera (se a função transitar). Leia mais sobre o Oracle Data Guard Far Sync neste [Oracle Ative Data Guard Far Sync whitepaper](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Ao utilizar as bases de dados Oracle Standard Edition, existem soluções ISV como o DBVisit Standby que permitem configurar uma elevada disponibilidade e recuperação de desastres.

## <a name="reference-architectures"></a>Arquiteturas de referência

### <a name="oracle-data-guard"></a>Proteção de Dados Oracle

A Oracle Data Guard garante uma elevada disponibilidade, proteção de dados e recuperação de desastres para dados da empresa. A Data Guard mantém bases de dados de espera como cópias transacionais consistentes da base de dados primária. Dependendo da distância entre as bases de dados primárias e secundárias e a tolerância à aplicação para a latência, pode configurar uma replicação sincronizada ou assíncronea. Em seguida, se a base de dados primária não estiver disponível devido a uma paragem planeada ou não planeada, a Data Guard pode mudar qualquer base de dados de espera para o papel principal, minimizando o tempo de inatividade. 

Ao utilizar o Oracle Data Guard, também pode abrir a sua base de dados secundária apenas para efeitos de leitura. Esta configuração chama-se Ative Data Guard. Oracle Database 12c introduziu um recurso chamado Data Guard Far Sync Instance. Este caso permite-lhe configurar uma configuração de perda de dados zero da sua base de dados Oracle sem ter de comprometer o desempenho.

> [!NOTE]
> A Ative Data Guard requer licenças adicionais. Esta licença também é necessária para utilizar a funcionalidade Far Sync. Por favor, conecte-se com o seu representante da Oracle para discutir as implicações de licenciamento.

#### <a name="oracle-data-guard-with-fsfo"></a>Guarda de Dados da Oracle com FSFO
A Oracle Data Guard com Fast-Start Failover (FSFO) pode fornecer resiliência adicional através da configuração do corretor numa máquina separada. O corretor da Guarda de Dados e a base de dados secundária executam o observador e observam a base de dados primária para o tempo de inatividade. Isto permite a redundância na configuração do observador da Data Guard também. 

Com a versão 12.2 ou superior da Oracle Database, também é possível configurar vários observadores com uma única configuração de corretor oracle Data Guard. Esta configuração proporciona disponibilidade adicional, caso um observador e a base de dados secundária experimentem tempo de inatividade. Data Guard Broker é leve e pode ser hospedado numa máquina virtual relativamente pequena. Para saber mais sobre o Data Guard Broker e as suas vantagens, visite a [documentação](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) da Oracle sobre este tema.

O diagrama seguinte é uma arquitetura recomendada para a utilização da Oracle Data Guard em Azure com zonas de disponibilidade. Esta arquitetura permite-lhe obter um SLA de 99,99%.

![Diagrama que mostra uma arquitetura recomendada para usar o Oracle Data Guard em Azure com zonas de disponibilidade.](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

No diagrama anterior, o sistema de clientes acede a uma aplicação personalizada com backend Oracle através da web. O frontend web está configurado num equilibrador de carga. O frontend web faz uma chamada para o servidor de aplicações apropriado para lidar com o trabalho. O servidor de aplicações consulta a base de dados primária do Oráculo. A base de dados Oracle foi configurada usando uma [máquina virtual otimizada de memória](../../sizes-memory.md) hiper-lida com [vCPUs de núcleo limitado](../../../virtual-machines/constrained-vcpu.md) para economizar nos custos de licenciamento e maximizar o desempenho. Vários discos premium ou ultra (Discos Geridos) são utilizados para desempenho e alta disponibilidade.

As bases de dados da Oracle são colocadas em múltiplas zonas de disponibilidade para uma elevada disponibilidade. Cada zona é composta por um ou mais centros de dados equipados com potência, arrefecimento e networking independentes. Para garantir a resiliência, são criadas no mínimo três zonas distintas em todas as regiões habilititdas. A separação física das zonas de disponibilidade dentro de uma região protege os dados de falhas do data center. Adicionalmente, dois observadores FSFO são criados em duas zonas de disponibilidade para iniciar e falhar sobre a base de dados para o secundário quando ocorre uma paragem. 

Pode configurar observadores adicionais e/ou bases de dados de espera numa zona de disponibilidade diferente (AZ 1, neste caso) do que a zona mostrada na arquitetura anterior. Finalmente, as bases de dados da Oracle são monitorizadas para o uptime e desempenho pela Oracle Enterprise Manager (OEM). O OEM também permite gerar vários relatórios de desempenho e utilização.

Em regiões onde as zonas de disponibilidade não são suportadas, pode utilizar conjuntos de disponibilidade para implementar a sua Base de Dados Oráculo de uma forma altamente disponível. Os conjuntos de disponibilidade permitem-lhe obter um tempo de uptime VM de 99,95%. O seguinte diagrama é uma arquitetura de referência desta utilização:

![Oracle Database usando conjuntos de disponibilidade com Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * O Oracle Enterprise Manager VM não precisa de ser colocado num conjunto de disponibilidade, uma vez que há apenas uma ocorrência de OEM a ser implementada.
> * Os discos ultra não são suportados atualmente numa configuração definida por disponibilidade.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Far Sync

Oracle Data Guard Far Sync fornece capacidade de proteção de perda de dados zero para bases de dados oracle. Esta capacidade permite-lhe proteger contra a perda de dados em caso de falha na sua máquina de base de dados. O Oracle Data Guard Far Sync precisa de ser instalado num VM separado. Far Sync é uma instância leve da Oracle que tem apenas um ficheiro de controlo, ficheiro de senha, spfile e registos de espera. Não existem ficheiros de dados ou ficheiros de registo de rego. 

Para uma proteção de perda de dados zero, deve haver uma comunicação sincronizada entre a sua base de dados primária e a instância Far Sync. A instância Far Sync recebe o redo do primário de forma sincronizada e encaminha-a imediatamente para todas as bases de dados de espera de forma assíncronia. Esta configuração também reduz a sobrecarga na base de dados primária, porque só tem de enviar o redo para a instância Far Sync em vez de todas as bases de dados de espera. Se uma instância Far Sync falhar, a Data Guard utiliza automaticamente o transporte assíncronos para a base de dados secundária a partir da base de dados primária para manter a proteção contra a perda de dados quase nula. Para uma maior resiliência, os clientes podem implementar várias instâncias Far Sync por cada instância de base de dados (primárias e secundárias).

O diagrama a seguir é uma arquitetura de alta disponibilidade utilizando o Oracle Data Guard Far Sync:

![Base de dados Oracle usando zonas de disponibilidade com Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

Na arquitetura anterior, existe um caso Far Sync implantado na mesma zona de disponibilidade que a instância da base de dados para reduzir a latência entre os dois. Nos casos em que a aplicação seja sensível à latência, considere implementar a sua base de dados e instâncias de Far Sync ou instâncias num [grupo de colocação de proximidade](../../../virtual-machines/linux/proximity-placement-groups.md).

O seguinte diagrama é uma arquitetura que utiliza o Oracle Data Guard FSFO e Far Sync para alcançar uma elevada disponibilidade e recuperação de desastres:

![Oracle Database usando zonas de disponibilidade para recuperação de desastres com Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

O GoldenGate permite a troca e manipulação de dados ao nível da transação entre múltiplas plataformas heterogéneas em toda a empresa. Movimenta transações comprometidas com integridade de transações e sobrecarga mínima na sua infraestrutura existente. A sua arquitetura modular dá-lhe a flexibilidade para extrair e replicar registos de dados selecionados, alterações transacionais e alterações no DDL (linguagem de definição de dados) através de uma variedade de topologias.

O Oracle GoldenGate permite-lhe configurar a sua base de dados para uma elevada disponibilidade, fornecendo replicação bidirecional. Isto permite-lhe configurar uma configuração **multi-master** ou **ativa.** O diagrama seguinte é uma arquitetura recomendada para a configuração ativa do Oracle GoldenGate em Azure. Na arquitetura seguinte, a base de dados Oracle foi configurada usando uma [máquina virtual otimizada de memória](../../sizes-memory.md) hiper-lida com [vCPUs de núcleo limitado](../../../virtual-machines/constrained-vcpu.md) para economizar nos custos de licenciamento e maximizar o desempenho. Vários discos premium ou ultra (discos geridos) são utilizados para desempenho e disponibilidade.

![Oracle Database usando zonas de disponibilidade com Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Uma arquitetura semelhante pode ser criada usando conjuntos de disponibilidade em regiões onde as zonas de disponibilidade não estão atualmente disponíveis.

O Oracle GoldenGate tem processos como Extract, Pump e Replicat que o ajudam a replicar assíncronos os seus dados de um servidor de base de dados da Oracle para outro. Estes processos permitem-lhe configurar uma replicação bidirecional para garantir uma elevada disponibilidade da sua base de dados se houver tempo de inatividade ao nível da zona de disponibilidade. No diagrama anterior, o processo extrato funciona no mesmo servidor que a base de dados Oráculo, enquanto os processos de Bomba de Dados e Replicat funcionam num servidor separado na mesma zona de disponibilidade. O processo Replicat é utilizado para receber dados da base de dados na outra zona de disponibilidade e comprometer os dados para a base de dados da Oracle na sua zona de disponibilidade. Da mesma forma, o processo de Bomba de Dados envia dados que foram extraídos pelo processo de Extração para o processo Replicat na outra zona de disponibilidade. 

Enquanto o diagrama de arquitetura anterior mostra o processo de Bomba de Dados e Replicat configurado num servidor separado, pode configurar todos os processos Oracle GoldenGate no mesmo servidor, com base na capacidade e utilização do seu servidor. Consulte sempre o seu relatório de AWR e as métricas em Azure para entender o padrão de utilização do seu servidor.

Ao configurar a replicação bidirecional Oracle GoldenGate em diferentes zonas de disponibilidade ou diferentes regiões, é importante garantir que a latência entre os diferentes componentes é aceitável para a sua aplicação. A latência entre zonas de disponibilidade e regiões pode variar e depende de múltiplos fatores. Recomenda-se que crie testes de desempenho entre o seu nível de aplicação e o nível da base de dados em diferentes zonas de disponibilidade e/ou regiões para confirmar que cumpre os requisitos de desempenho da sua aplicação.

O nível de aplicação pode ser configurado na sua própria sub-rede e o nível de base de dados pode ser separado na sua própria sub-rede. Quando possível, considere utilizar [o Gateway de Aplicação Azure](../../../application-gateway/overview.md) para equilibrar o tráfego entre os servidores da aplicação. O Azure Application Gateway é um equilibrador robusto de carga de tráfego web. Fornece afinidade de sessão baseada em cookies que mantém uma sessão de utilizador no mesmo servidor, minimizando assim os conflitos na base de dados. Alternativas ao Gateway de Aplicações são [O Equilibrador de Carga Azure](../../../load-balancer/load-balancer-overview.md) e [Gestor de Tráfego Azure](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Oráculo sharding

O cacimento é um padrão de nível de dados que foi introduzido no Oráculo 12.2. Permite-lhe dividir horizontalmente e escalar os seus dados através de bases de dados independentes. É uma arquitetura de nada de partilha onde cada base de dados está hospedada numa máquina virtual dedicada, o que permite uma alta leitura e produção de escrita, além da resiliência e da maior disponibilidade. Este padrão elimina pontos únicos de falha, proporciona isolamento de falhas e permite atualizações rolantes sem tempo de inatividade. O tempo de inatividade de um fragmento ou uma falha de nível de data não afeta o desempenho ou disponibilidade dos outros fragmentos em outros centros de dados. 

O sharding é adequado para aplicações OLTP de alta produção que não podem pagar qualquer tempo de inatividade. Todas as linhas com a mesma chave de fragmentos estão sempre garantidas para estar no mesmo fragmento, aumentando assim o desempenho proporcionando a alta consistência. As aplicações que utilizam o sharding devem ter um modelo de dados bem definido e uma estratégia de distribuição de dados (haxixe consistente, intervalo, lista ou compósito) que acede principalmente a dados usando uma chave de fragmento (por exemplo, *clienteId* ou *accountNum).* O Sharding também permite armazenar conjuntos específicos de dados mais próximos dos clientes finais, ajudando-o a cumprir os seus requisitos de desempenho e conformidade.

Recomenda-se que replique os seus fragmentos para uma elevada disponibilidade e recuperação de desastres. Esta configuração pode ser feita utilizando tecnologias Oracle, como a Oracle Data Guard ou a Oracle GoldenGate. Uma unidade de replicação pode ser um fragmento, uma parte de um fragmento, ou um grupo de fragmentos. A disponibilidade de uma base de dados de fragmentos não é afetada por uma interrupção ou abrandamento de um ou mais fragmentos. Para uma elevada disponibilidade, os fragmentos de espera podem ser colocados na mesma zona de disponibilidade onde os fragmentos primários são colocados. Para a recuperação de desastres, os fragmentos de espera podem ser localizados noutra região. Você também pode implantar fragmentos em várias regiões para servir o tráfego nessas regiões. Leia mais sobre a configuração da alta disponibilidade e replicação da sua base de dados em documentação de fragmentos da [Oracle.](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)

O oráculo sharding consiste principalmente dos seguintes componentes. Mais informações sobre estes componentes podem ser encontradas na [documentação do Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Catálogo Shard** - Base de dados Oracle de uso especial que é uma loja persistente para todos os dados de configuração da base de dados Shard. Todas as alterações de configuração, tais como adicionar ou remover fragmentos, mapeamento dos dados e DDLs numa base de dados de fragmentos são iniciadas no catálogo de fragmentos. O catálogo de fragmentos também contém a cópia principal de todas as tabelas duplicadas num SDB. 

  O catálogo de fragmentos utiliza vistas materializadas para replicar automaticamente alterações em tabelas duplicadas em todos os fragmentos. A base de dados do catálogo de fragmentos também funciona como um coordenador de consultas usado para processar consultas e consultas multi-fragmentos que não especificam uma chave de fragmentos. 
  
  A utilização da Oracle Data Guard em conjunto com zonas de disponibilidade ou conjuntos de disponibilidade para alta disponibilidade do catálogo de fragmentos é uma das melhores práticas recomendadas. A disponibilidade do catálogo de fragmentos não tem qualquer impacto na disponibilidade da base de dados de fragmentos. Um tempo de paragem no catálogo de fragmentos apenas afeta as operações de manutenção e consultas multi-hard durante o breve período que a Guarda de Dados falha. As transações online continuam a ser encaminhadas e executadas pelo SDB e não são afetadas por uma paragem de catálogo.

- **Diretores de fragmentos - Serviços leves** que precisam de ser implantados em cada região/zona de disponibilidade em que os seus fragmentos residem. Os Diretores de Fragmentos são Gestores de Serviços Globais implantados no contexto da Oracle Sharding. Para uma elevada disponibilidade, recomenda-se que implemente pelo menos um diretor de fragmentos em cada zona de disponibilidade em que os seus fragmentos existam. 

  Ao ligar-se inicialmente à base de dados, a informação de encaminhamento é criada por um diretor de fragmentos e está em cache para pedidos subsequentes, contornando o diretor de fragmentos. Uma vez que a sessão é estabelecida com um fragmento, todas as consultas SQL e DMLs são suportadas e executadas no âmbito do fragmento dado. Este encaminhamento é rápido e é utilizado para todas as cargas de trabalho OLTP que realizam transações intra-fragmentos. Recomenda-se a utilização de encaminhamento direto para todas as cargas de trabalho OLTP que exijam o maior desempenho e disponibilidade. A cache de encaminhamento atualiza-se automaticamente quando um fragmento fica indisponível ou ocorre alterações na topologia de fragmentos. 
  
  Para um encaminhamento de alto desempenho e dependente de dados, a Oracle recomenda a utilização de um pool de ligação ao aceder aos dados na base de dados de fragmentos. Piscinas de conexão Oráculo, bibliotecas específicas da linguagem e motoristas suportam o Oracle Sharding. Consulte a [documentação do Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) para obter mais detalhes.

- **Serviço global** - O serviço global é semelhante ao serviço de base de dados regular. Além de todas as propriedades de um serviço de base de dados, um serviço global tem propriedades para bases de dados de fragmentos, como a afinidade da região entre clientes e tolerância de shard e replicação lag. Apenas um serviço Global precisa de ser criado para ler/escrever dados de/para uma base de dados de fragmentos. Ao utilizar a Ative Data Guard e configurar réplicas apenas de leitura dos fragmentos, pode criar outro serviço gGobal para cargas de trabalho apenas de leitura. O cliente pode usar estes serviços Globais para se conectar à base de dados.

- **Bases de dados de fragmentos** - Bases de dados de fragmentos são as bases de dados do Oracle. Cada base de dados é replicada utilizando a Oracle Data Guard numa configuração de corretor com Fast-Start Failover (FSFO) ativada. Não é preciso configurar a falha da Data Guard e a replicação em cada fragmento. Isto é configurado automaticamente e implementado quando a base de dados partilhada é criada. Se um fragmento em particular falhar, a Oracle Sharing falha automaticamente nas ligações de base de dados da primária para a espera.

Pode implementar e gerir bases de dados de fragmentos da Oracle com duas interfaces: Oracle Enterprise Manager Cloud Control GUI e/ou o `GDSCTL` utilitário da linha de comando. Pode até monitorizar os diferentes fragmentos para disponibilidade e desempenho utilizando o controlo cloud. O `GDSCTL DEPLOY` comando cria automaticamente os fragmentos e os respetivos ouvintes. Além disso, este comando implementa automaticamente a configuração de replicação utilizada para uma alta disponibilidade de nível de fragmento especificada pelo administrador.

Há diferentes formas de fragmentos de uma base de dados:

* Fragmento gerido pelo sistema - Distribui-se automaticamente por fragmentos utilizando divisórias
* Fragmento definido pelo utilizador - Permite especificar o mapeamento dos dados para os fragmentos, que funciona bem quando existem requisitos regulamentares ou de localização de dados)
* Fragmento composto - Uma combinação de fragmentos geridos pelo sistema e definidos pelo utilizador para _diferentes espaços de fragmentos_
* Subpartições de tabela - Semelhante a uma tabela divisória regular.

Leia mais sobre os [diferentes métodos de fragmentos](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) na documentação da Oracle.

Embora uma base de dados de fragmentos possa parecer uma única base de dados para aplicações e desenvolvedores, ao migrar de uma base de dados não-fragmentos para uma base de dados de fragmentos, é necessário um planeamento cuidadoso para determinar quais as tabelas que serão duplicadas versus fragmentos. 

As mesas duplicadas são armazenadas em todos os fragmentos, enquanto as mesas de cacos são distribuídas por diferentes fragmentos. A recomendação é duplicar as tabelas pequenas e dimensionais e distribuir/escassear as tabelas de factos. Os dados podem ser carregados na sua base de dados com fragmentos utilizando o catálogo de fragmentos como coordenador central ou executando a Bomba de Dados em cada fragmento. Leia mais sobre [a migração de dados para uma base de dados cace na](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) documentação da Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Oráculo sharding com guarda de dados

A Oracle Data Guard pode ser utilizada para o sharding com métodos de fragmentos geridos pelo sistema, definidos pelo utilizador e compósitos.

O diagrama seguinte é uma arquitetura de referência para o Oráculo Sharding com Oracle Data Guard usado para alta disponibilidade de cada fragmento. O diagrama de arquitetura mostra um _método de fragmento composto._ O diagrama de arquitetura provavelmente diferirá para aplicações com diferentes requisitos para a localidade dos dados, equilíbrio de carga, alta disponibilidade, recuperação de desastres, etc. e pode usar diferente método para o caco. O Oracle Sharding permite-lhe satisfazer estes requisitos e escalar horizontal e eficientemente, fornecendo estas opções. Uma arquitetura semelhante pode até ser implantada usando o Oracle GoldenGate.

![Oracle Database Sharding usando zonas de disponibilidade com Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Embora o fragmento gerido pelo sistema seja o mais fácil de configurar e gerir, o fragmento ou o fragmento composto definido pelo utilizador são adequados para cenários em que os seus dados e aplicações são geo-distribuídos ou em cenários em que é necessário ter controlo sobre a replicação de cada fragmento. 

Na arquitetura anterior, o fragmento composto é usado para geo-distribuir os dados e escalar horizontalmente os seus níveis de aplicação. O fragmento composto é uma combinação de fragmentos geridos pelo sistema e definidos pelo utilizador, proporcionando assim o benefício de ambos os métodos. No cenário anterior, os dados são primeiro fragmentos em vários espaços de fragmentos separados por região. Em seguida, os dados são ainda divididos por haxixe consistente através de múltiplos fragmentos no espaço de fragmentos. Cada espaço de cacos contém vários grupos de fragmentos. Cada grupo de fragmentos tem vários fragmentos e é uma "unidade" de replicação, neste caso. Cada grupo de fragmentos contém todos os dados no espaço de fragmentos. Os grupos de fragmentos A1 e B1 são grupos primários, enquanto os grupos de fragmentos A2 e B2 são de prontidão. Pode optar por ter fragmentos individuais como unidade de replicação, em vez de um grupo de fragmentos.

Na arquitetura anterior, um diretor GSM/shard é implantado em todas as zonas de disponibilidade para uma elevada disponibilidade. A recomendação é a de implantar pelo menos um GSM/shard diretor por data center/região. Além disso, uma instância do servidor de aplicações é implementada em todas as zonas de disponibilidade que contenham um grupo de fragmentos. Esta configuração permite que a aplicação mantenha a latência entre o servidor de aplicação e a base de dados/grupo de shardgroup baixo. Se uma base de dados falhar, o servidor de aplicação na mesma zona que a base de dados de espera pode tratar os pedidos assim que a transição da função de base de dados acontecer. A Azure Application Gateway e o diretor de fragmentos acompanham o pedido e a latência de resposta e os pedidos de rota em conformidade.

Do ponto de vista da aplicação, o sistema de clientes faz um pedido ao Azure Application Gateway (ou outras tecnologias de equilíbrio de carga em Azure) que redireciona o pedido para a região mais próxima do cliente. O Azure Application Gateway também suporta sessões pegajosas, pelo que quaisquer pedidos provenientes do mesmo cliente são encaminhados para o mesmo servidor de aplicações. O servidor de aplicações utiliza o agrupamento de ligações nos controladores de acesso a dados. Esta funcionalidade está disponível em condutores como JDBC, ODP.NET, OCI, etc. Os condutores podem reconhecer as chaves de caco especificadas como parte do pedido. [O Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) para clientes JDBC pode permitir que clientes de aplicações não-Oracle, como Apache Tomcat e IIS, trabalhem com o Oracle Sharding. 

Durante o pedido inicial, o servidor de aplicações conecta-se ao diretor de fragmentos da sua região para obter informações de encaminhamento para o fragmento para o que o pedido precisa de ser encaminhado para. Com base na chave de fragmentos passada, o diretor encaminha o servidor de aplicação para o respetivo fragmento. O servidor de aplicações cachesta informação construindo um mapa, e para pedidos subsequentes, contorna o diretor de fragmentos e as rotas solicitam diretamente para o fragmento.

#### <a name="oracle-sharding-with-goldengate"></a>Oráculo sharding com GoldenGate

O diagrama seguinte é uma arquitetura de referência para o Oráculo Sharding com Oracle GoldenGate para a alta disponibilidade de cada fragmento na região. Ao contrário da arquitetura anterior, esta arquitetura apenas retrata a alta disponibilidade dentro de uma única região de Azure (múltiplas zonas de disponibilidade). Pode-se implementar uma base de dados de alta disponibilidade de várias regiões (semelhante ao exemplo anterior) utilizando o Oracle GoldenGate.

![Oráculo Database Sharding usando zonas de disponibilidade com GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

A arquitetura de referência anterior utiliza o método de fragmento _gerido pelo sistema_ para fragmentos dos dados. Uma vez que a replicação do Oracle GoldenGate é feita a um nível de pedaço, metade dos dados replicados num fragmento podem ser replicados para outro fragmento. A outra metade pode ser replicada a um fragmento diferente. 

A forma como os dados são replicados depende do fator de replicação. Com um fator de replicação de 2, você terá duas cópias de cada pedaço de dados através dos seus três fragmentos no grupo de fragmentos. Da mesma forma, com um fator de replicação de 3 e três fragmentos no seu grupo de fragmentos, todos os dados em cada fragmento serão replicados a todos os outros fragmentos do grupo de fragmentos. Cada fragmento no grupo de fragmentos pode ter um fator de replicação diferente. Esta configuração ajuda-o a definir o seu design de alta disponibilidade e recuperação de desastres de forma eficiente dentro de um grupo de fragmentos e em vários grupos de fragmentos.

Na arquitetura anterior, o grupo de fragmentos A e o grupo B contêm os mesmos dados, mas residem em diferentes zonas de disponibilidade. Se tanto o grupo de fragmentos A como o grupo B tiverem o mesmo fator de replicação de 3, cada linha/pedaço da sua mesa desordenada será replicado 6 vezes nos dois grupos de fragmentos. Se o grupo A tiver um fator de replicação de 3 e o grupo B tiver um fator de replicação de 2, cada linha/pedaço será replicado 5 vezes nos dois grupos de fragmentos.

Esta configuração evita a perda de dados se ocorrer uma falha de nível de instância ou de nível de zona de disponibilidade. A camada de aplicação é capaz de ler e escrever para cada fragmento. Para minimizar os conflitos, o Oracle Sharding designa um "pedaço mestre" para cada gama de valores de haxixe. Esta funcionalidade garante que os pedidos de um determinado pedaço são direcionados para o pedaço correspondente. Além disso, a Oracle GoldenGate fornece deteção e resolução automática de conflitos para lidar com quaisquer conflitos que possam surgir. Para obter mais informações e limitações da implementação do GoldenGate com o Oracle Sharding, consulte a documentação da Oracle sobre a utilização [do Oracle GoldenGate com uma base de dados de fragmentos.](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)

Na arquitetura anterior, um diretor GSM/shard é implantado em todas as zonas de disponibilidade para uma elevada disponibilidade. A recomendação é a de implantar pelo menos um GSM/shard diretor por data center ou região. Além disso, uma instância do servidor de aplicações é implementada em todas as zonas de disponibilidade que contenham um grupo de fragmentos. Esta configuração permite que a aplicação mantenha a latência entre o servidor de aplicação e a base de dados/grupo de shardgroup baixo. Se uma base de dados falhar, o servidor de aplicação na mesma zona que a base de dados de espera pode tratar os pedidos assim que a função de base de dados transite. A Azure Application Gateway e o diretor de fragmentos acompanham o pedido e a latência de resposta e os pedidos de rota em conformidade.

Do ponto de vista da aplicação, o sistema de clientes faz um pedido ao Azure Application Gateway (ou outras tecnologias de equilíbrio de carga em Azure) que redireciona o pedido para a região mais próxima do cliente. O Azure Application Gateway também suporta sessões pegajosas, pelo que quaisquer pedidos provenientes do mesmo cliente são encaminhados para o mesmo servidor de aplicações. O servidor de aplicações utiliza o agrupamento de ligações nos controladores de acesso a dados. Esta funcionalidade está disponível em condutores como JDBC, ODP.NET, OCI, etc. Os condutores podem reconhecer as chaves de caco especificadas como parte do pedido. [O Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) para clientes JDBC pode permitir que clientes de aplicações não-Oracle, como Apache Tomcat e IIS, trabalhem com o Oracle Sharding. 

Durante o pedido inicial, o servidor de aplicações conecta-se ao diretor de fragmentos da sua região para obter informações de encaminhamento para o fragmento para o que o pedido precisa de ser encaminhado para. Com base na chave de fragmentos passada, o diretor encaminha o servidor de aplicação para o respetivo fragmento. O servidor de aplicações cachesta informação construindo um mapa, e para pedidos subsequentes, contorna o diretor de fragmentos e as rotas solicitam diretamente para o fragmento.

## <a name="patching-and-maintenance"></a>Remendação e manutenção

Ao implementar as suas cargas de trabalho da Oracle para o Azure, a Microsoft cuida de todos os patchings de nível OS do hospedeiro. Qualquer manutenção planeada ao nível do SO é comunicada antecipadamente aos clientes para permitir ao cliente esta manutenção planeada. Dois servidores de duas zonas de disponibilidade diferentes nunca são remendados simultaneamente. Consulte [Gerir a disponibilidade de máquinas virtuais](../../manage-availability.md) para obter mais detalhes sobre manutenção e remendos em VM. 

Remendar o sistema operativo da máquina virtual pode ser automatizado utilizando [a Azure Automation Update Management](../../../automation/update-management/overview.md). Remendar e manter a base de dados oracle pode ser automatizado e programado usando [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) ou [Azure Automation Update Management](../../../automation/update-management/overview.md) para minimizar o tempo de inatividade. Consulte [a Entrega Contínua e As Implementações Azuis/Verdes](/azure/devops/learn/what-is-continuous-delivery) para entender como pode ser usada no contexto das suas bases de dados Oráculos.

## <a name="architecture-and-design-considerations"></a>Considerações de arquitetura e design

- Considere usar a [máquina virtual otimizada de memória hiperligada](../../sizes-memory.md) com [vCPUs de núcleo limitado](../../../virtual-machines/constrained-vcpu.md) para o seu Oracle Database VM para economizar nos custos de licenciamento e maximizar o desempenho. Utilize vários discos premium ou ultra (discos geridos) para desempenho e disponibilidade.
- Ao utilizar discos geridos, o nome do disco/dispositivo pode ser alterado em reboots. Recomenda-se que utilize o dispositivo UUID em vez do nome para garantir que os seus suportes persistam em reboots. Pode encontrar mais informações [aqui](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab).
- Utilize zonas de disponibilidade para obter uma elevada disponibilidade na região.
- Considere utilizar discos ultra (quando disponíveis) ou discos premium para a sua base de dados Oracle.
- Considere criar uma base de dados de standby Oracle em outra região de Azure usando a Oracle Data Guard.
- Considere usar [grupos de colocação de proximidade](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) para reduzir a latência entre a sua aplicação e o nível de base de dados.
- Configurar [o Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) para gestão, monitorização e registo.
- Considere utilizar a Oracle Automatic Storage Management (ASM) para uma gestão de armazenamento simplificada para a sua base de dados.
- Utilize [pipelines Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines) para gerir remendos e atualizações na sua base de dados sem qualquer tempo de inatividade.
- Ajuste o seu código de aplicação para adicionar padrões nativos da nuvem, tais como padrão de [retrip,](/azure/architecture/patterns/retry) [padrão de disjuntor](/azure/architecture/patterns/circuit-breaker)de circuitos, e outros padrões definidos no [guia Cloud Design Patterns](/azure/architecture/patterns/) que podem ajudar a sua aplicação a ser mais resistente.

## <a name="next-steps"></a>Próximos passos

Reveja os seguintes artigos de referência da Oracle que se aplicam ao seu cenário.

- [Introdução à Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Conceitos de corretor de guarda de dados da Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Configurar o Oracle GoldenGate para Active-Active alta disponibilidade](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Visão geral do Oráculo Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Ative Data Guard Far Sync Zero Data Loss a qualquer distância](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)

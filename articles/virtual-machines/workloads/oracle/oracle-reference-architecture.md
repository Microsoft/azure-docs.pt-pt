---
title: Arquiteturas de referência para bases de dados Oracle no Azure Microsoft Docs
description: Referências arquiteturas para executar bases de dados Oracle Database Enterprise Edition em Máquinas Virtuais Microsoft Azure.
services: virtual-machines-linux
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: bbb6665299ce9b6521eeb8801d8621dfbdc17f4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683494"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Arquiteturas de referência para Oracle Database Enterprise Edition em Azure

Este guia detalha informações sobre a implementação de uma base de dados da Oracle altamente disponível no Azure. Além disso, este guia mergulha em considerações de recuperação de desastres. Estas arquiteturas foram criadas com base em implementações de clientes. Este guia aplica-se apenas à Oracle Database Enterprise Edition.

Se está interessado em aprender mais sobre maximizar o desempenho da sua base de dados Oracle, consulte [Architect an Oracle DB](oracle-design.md).

## <a name="assumptions"></a>Pressupostos

- Você tem uma compreensão dos diferentes conceitos de Azure, tais como [zonas de disponibilidade](../../../availability-zones/az-overview.md)
- Você está executando Oracle Database Enterprise Edition 12c ou mais tarde
- Você está ciente e reconhece as implicações de licenciamento ao usar as soluções neste artigo

## <a name="high-availability-for-oracle-databases"></a>Alta disponibilidade para bases de dados oracle

Alcançar uma elevada disponibilidade na nuvem é uma parte importante do planeamento e design de cada organização. O Microsoft Azure oferece [zonas de disponibilidade](../../../availability-zones/az-overview.md) e conjuntos de disponibilidade (para ser usado em regiões onde as zonas de disponibilidade não estão disponíveis). Leia mais sobre [a gestão da disponibilidade das suas máquinas virtuais](../../../virtual-machines/linux/manage-availability.md) para projetar para a nuvem.

Além de ferramentas e ofertas nativas de nuvem, a Oracle fornece soluções para elevada disponibilidade, como [a Oracle Data Guard,](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7) [Data Guard com FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)e [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) que podem ser configuradas no Azure. Este guia abrange arquiteturas de referência para cada uma destas soluções.

Finalmente, ao migrar ou criar aplicações para a nuvem, é importante ajustar o seu código de aplicação para adicionar padrões nativos de nuvem, tais como padrão de [retry](https://docs.microsoft.com/azure/architecture/patterns/retry) e padrão de [disjuntor.](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) Padrões adicionais [definidos](https://docs.microsoft.com/azure/architecture/patterns/) no guia cloud design patterns poderia ajudar a sua aplicação a ser mais resiliente.

### <a name="oracle-rac-in-the-cloud"></a>Oráculo RAC na nuvem

Oracle Real Application Cluster (RAC) é uma solução da Oracle para ajudar os clientes a obter em alta supõeção, tendo muitos casos acedendo a um armazenamento de base de dados (padrão de arquitetura compartilhado). Embora o Oracle RAC também possa ser usado para alta disponibilidade no local, o Oracle RAC por si só não pode ser usado para alta disponibilidade na nuvem, uma vez que protege apenas contra falhas de nível de instância e não contra falhas de nível de rack ou de nível central de dados. Por esta razão, a Oracle recomenda a utilização da Oracle Data Guard com a sua base de dados (seja uma instância única ou RAC) para uma elevada disponibilidade. Os clientes geralmente exigem um SLA elevado para executar as suas aplicações críticas de missão. A Oracle RAC não é atualmente certificada ou apoiada pela Oracle em Azure. No entanto, o Azure oferece funcionalidades como o Azure oferece Zonas de Disponibilidade e janelas de manutenção planeadas para ajudar a proteger contra falhas de nível de instância. Além disso, os clientes podem usar tecnologias como a Oracle Data Guard, Oracle GoldenGate e Oracle Sharding para alto desempenho e resiliância, protegendo as suas bases de dados de falhas ao nível do rack, bem como falhas geopolíticas e geopolíticas ao nível dos datacenters.

Ao executar as bases de dados da Oracle em [várias zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview) em conjunto com a Oracle Data Guard ou goldenGate, os clientes são capazes de obter um SLA uptime de 99,99%. Nas regiões de Azure onde as zonas de disponibilidade ainda não estão presentes, os clientes podem utilizar [conjuntos](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) de disponibilidade e obter um SLA de 99,95%.

>NOTA: Pode ter um alvo de tempo de entrada muito superior ao sLA de tempo de entrada fornecido pela Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Recuperação de desastres para bases de dados da Oracle

Ao hospedar as suas aplicações críticas de missão na nuvem, é importante projetar para alta disponibilidade e recuperação de desastres.

Para a Oracle Database Enterprise Edition, a Oracle Data Guard é uma característica útil para a recuperação de desastres. Pode configurar uma instância de base de dados de espera numa [região de Azure emparelhada](/azure/best-practices-availability-paired-regions) e configurar a falha da Guarda de Dados para a recuperação de desastres. Para perda zero de dados, recomenda-se que implemente uma instância de Guarda de Dados Da Oracle Far Sync para além da Ative Data Guard. 

Considere a configuração da instância Data Guard Far Sync numa zona de disponibilidade diferente da sua base de dados primária oracle se a sua aplicação permitir a latência (são necessários testes minuciosos). Utilize um modo de **disponibilidade máxima** para configurar o transporte sincronizado dos seus ficheiros de redopara a instância Far Sync. Estes ficheiros são então transferidos assincronicamente para a base de dados de espera. 

Se a sua aplicação não permitir a perda de desempenho ao configurar a instância Far Sync noutra zona de disponibilidade no modo de **Disponibilidade Máxima** (sincronizado), poderá configurar uma instância Far Sync na mesma zona de disponibilidade que a sua base de dados principal. Para uma maior disponibilidade, considere a criação de várias instâncias Far Sync perto da sua base de dados primária e pelo menos uma instância próxima da sua base de dados de espera (se o papel transitar). Leia mais sobre a Oracle Data Guard Far Sync neste [livro branco Oracle Ative Data Guard Far Sync](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Ao utilizar as bases de dados da Oracle Standard Edition, existem soluções ISV como dbVisit Standby que lhe permitem configurar alta disponibilidade e recuperação de desastres.

## <a name="reference-architectures"></a>Arquiteturas de referência

### <a name="oracle-data-guard"></a>Proteção de Dados Oracle

A Oracle Data Guard garante alta disponibilidade, proteção de dados e recuperação de desastres para dados da empresa. O Data Guard mantém as bases de dados de espera como cópias transacionais consistentes da base de dados primária. Dependendo da distância entre as bases de dados primárias e secundárias e a tolerância à aplicação para a latência, pode configurar uma replicação sincronizada ou assíncrona. Em seguida, se a base de dados primária não estiver disponível devido a uma paragem planeada ou não planeada, o Guarda de Dados pode mudar qualquer base de dados de espera para o papel principal, minimizando o tempo de inatividade. 

Ao utilizar o Oracle Data Guard, também pode abrir a sua base de dados secundária para fins de leitura. Esta configuração chama-se Ative Data Guard. A Oracle Database 12c introduziu uma funcionalidade chamada Data Guard Far Sync Instance. Esta instância permite-lhe configurar uma configuração de perda de dados zero da sua base de dados Oracle sem ter de comprometer o desempenho.

> [!NOTE]
> A Ative Data Guard requer licenciamento adicional. Esta licença também é necessária para utilizar a funcionalidade Far Sync. Por favor, conecte-se com o seu representante da Oracle para discutir as implicações de licenciamento.

#### <a name="oracle-data-guard-with-fsfo"></a>Guarda de Dados Oracle com FSFO
A Oracle Data Guard com Fast-Start Failover (FSFO) pode fornecer resiliência adicional configurando o corretor numa máquina separada. O corretor da Guarda de Dados e a base de dados secundária executam o observador e observam a base de dados primária para o tempo de inatividade. Isto permite também a redundância na configuração do observador da Guarda de Dados. 

Com a oracle Database versão 12.2 ou superior, também é possível configurar vários observadores com uma única configuração de corretor de guarda de dados Oracle. Esta configuração proporciona disponibilidade adicional, caso um observador e a base de dados secundária experimentem tempo de inatividade. O Corretor de Guarda de Dados é leve e pode ser hospedado numa máquina virtual relativamente pequena. Para saber mais sobre o Corretor de Guarda de Dados e as suas vantagens, visite a [documentação](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) da Oracle sobre este tema.

O diagrama seguinte é uma arquitetura recomendada para a utilização da Oracle Data Guard em Azure com zonas de disponibilidade. Esta arquitetura permite-lhe obter um SLA de 99,99%.

![Oracle Database usando zonas de disponibilidade com corretor de guarda de dados - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

No diagrama anterior, o sistema cliente acede a uma aplicação personalizada com o backend da Oracle através da web. O frontend web é configurado num equilibrador de carga. O frontend web faz uma chamada para o servidor de aplicação apropriado para lidar com o trabalho. O servidor de aplicações consulta a base de dados primária do Oráculo. A base de dados Oracle foi configurada utilizando uma [máquina virtual otimizada](../../../virtual-machines/windows/sizes-memory.md) de memória hiperthreaded com [vCPUs de núcleo limitado](../../../virtual-machines/windows/constrained-vcpu.md) para economizar nos custos de licenciamento e maximizar o desempenho. Vários discos premium ou ultra (Discos Geridos) são utilizados para desempenho e alta disponibilidade.

As bases de dados oracle são colocadas em múltiplas zonas de disponibilidade para alta disponibilidade. Cada zona é composta por um ou mais centros de dados equipados com potência independente, arrefecimento e networking. Para garantir a resiliência, são criadas no mínimo três zonas separadas em todas as regiões habilitadas. A separação física das zonas de disponibilidade dentro de uma região protege os dados de falhas nos centros de dados. Além disso, dois observadores fSFO são criados em duas zonas de disponibilidade para iniciar e falhar sobre a base de dados para o secundário quando ocorre uma paragem. 

Pode configurar observadores adicionais e/ou bases de dados de espera numa zona de disponibilidade diferente (AZ 1, neste caso) do que a zona mostrada na arquitetura anterior. Finalmente, as bases de dados oracle são monitorizadas para o uptime e desempenho pela Oracle Enterprise Manager (OEM). O OEM também permite gerar vários relatórios de desempenho e utilização.

Em regiões onde as zonas de disponibilidade não são suportadas, pode utilizar conjuntos de disponibilidade para implementar a sua Base de Dados Oracle de forma altamente disponível. Os conjuntos de disponibilidade permitem-lhe atingir um tempo de uptime VM de 99,95%. O diagrama a seguir é uma arquitetura de referência desta utilização:

![Oracle Database usando conjuntos de disponibilidade com Corretor de Guarda de Dados - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * O VM do Gestor Empresarial oracle não precisa de ser colocado num conjunto de disponibilidade, uma vez que há apenas um caso de OEM a ser implementado.
> * Os discos ultra não são suportados atualmente numa configuração de conjunto de disponibilidade.

#### <a name="oracle-data-guard-far-sync"></a>Guarda de Dados Oracle Far Sync

A Oracle Data Guard Far Sync fornece uma capacidade de proteção de perda de dados zero para as bases de dados da Oracle. Esta capacidade permite-lhe proteger contra a perda de dados se a sua máquina de base de dados falhar. O Oracle Data Guard Far Sync precisa de ser instalado num VM separado. Far Sync é um caso oráculo leve que só tem um ficheiro de controlo, ficheiro de senha, spfile e registos de espera. Não existem ficheiros de dados ou ficheiros de registo de rego. 

Para uma proteção contra a perda de dados zero, deve haver uma comunicação sincronizada entre a sua base de dados primária e a instância Far Sync. O caso Far Sync recebe redodo do primário de forma sincronizada e encaminha-o imediatamente para todas as bases de dados de espera de forma assíncrona. Esta configuração também reduz as despesas gerais na base de dados primária, porque só tem de enviar o redo para a instância Far Sync em vez de todas as bases de dados de espera. Se uma instância de Far Sync falhar, o Data Guard utiliza automaticamente o transporte assíncrono para a base de dados secundária a partir da base de dados primária para manter a proteção contra perdas de dados quase nulas. Para uma maior resiliência, os clientes podem implementar múltiplas instâncias Far Sync por cada instância de base de dados (primárias e secundárias).

O diagrama a seguir é uma arquitetura de alta disponibilidade usando o Oracle Data Guard Far Sync:

![Base de dados da Oracle que usa zonas de disponibilidade com data guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

Na arquitetura anterior, existe uma instância Far Sync implantada na mesma zona de disponibilidade que a instância de base de dados para reduzir a latência entre os dois. Nos casos em que a aplicação seja sensível à latência, considere a implementação da sua base de dados e instâncias de Far Sync ou instâncias num grupo de [colocação de proximidade](../../../virtual-machines/linux/proximity-placement-groups.md).

O diagrama seguinte é uma arquitetura que utiliza a Oracle Data Guard FSFO e Far Sync para alcançar uma elevada disponibilidade e recuperação de desastres:

![Oracle Database usando zonas de disponibilidade para recuperação de desastres com Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

O GoldenGate permite a troca e manipulação de dados ao nível da transação entre várias plataformas heterogéneas em toda a empresa. Movimenta transações comprometidas com integridade de transações e despesas mínimas na sua infraestrutura existente. A sua arquitetura modular dá-lhe a flexibilidade para extrair e replicar registos de dados selecionados, alterações transacionais e alterações ao DDL (linguagem de definição de dados) através de uma variedade de topoologias.

O Oracle GoldenGate permite-lhe configurar a sua base de dados para uma elevada disponibilidade, fornecendo replicação bidirecional. Isto permite-lhe configurar uma **configuração** **multi-master** ou ativa. O diagrama seguinte é uma arquitetura recomendada para a configuração activa-activa oracle GoldenGate em Azure. Na seguinte arquitetura, a base de dados Oracle foi configurada usando uma [máquina virtual otimizada](../../../virtual-machines/windows/sizes-memory.md) de memória hiperthreaded com [vCPUs de núcleo limitado](../../../virtual-machines/windows/constrained-vcpu.md) para economizar em custos de licenciamento e maximizar o desempenho. Vários discos premium ou ultra (discos geridos) são utilizados para o desempenho e disponibilidade.

![Oracle Database usando zonas de disponibilidade com corretor de guarda de dados - FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Uma arquitetura semelhante pode ser criada usando conjuntos de disponibilidade em regiões onde as zonas de disponibilidade não estão atualmente disponíveis.

O Oracle GoldenGate tem processos como Extrato, Bomba e Replicat que o ajudam a replicar assincronicamente os seus dados de um servidor de base de dados da Oracle para outro. Estes processos permitem-lhe configurar uma replicação bidirecional para garantir uma elevada disponibilidade da sua base de dados se houver tempo de paragem ao nível da zona de disponibilidade. No diagrama anterior, o processo extrato funciona no mesmo servidor que a sua base de dados Oracle, enquanto os processos Data Pump e Replicat funcionam num servidor separado na mesma zona de disponibilidade. O processo Replicat é utilizado para receber dados da base de dados na outra zona de disponibilidade e comprometer os dados na base de dados da Oracle na sua zona de disponibilidade. Da mesma forma, o processo data Pump envia dados que foram extraídos pelo processo de extrato para o processo De replicat na outra zona de disponibilidade. 

Enquanto o diagrama de arquitetura anterior mostra o processo data pump e replicat configurado num servidor separado, pode configurar todos os processos Oracle GoldenGate no mesmo servidor, com base na capacidade e utilização do seu servidor. Consulte sempre o seu relatório AWR e as métricas em Azure para entender o padrão de utilização do seu servidor.

Ao configurar a replicação bidirecional Oracle GoldenGate em diferentes zonas de disponibilidade ou regiões diferentes, é importante garantir que a latência entre os diferentes componentes é aceitável para a sua aplicação. A latência entre zonas de disponibilidade e regiões pode variar e depende de múltiplos fatores. Recomenda-se que estabeleça testes de desempenho entre o seu nível de aplicação e o seu nível de base de dados em diferentes zonas de disponibilidade e/ou regiões para confirmar que satisfaz os requisitos de desempenho da sua aplicação.

O nível de aplicação pode ser criado na sua própria sub-rede e o nível de base de dados pode ser separado na sua própria sub-rede. Quando possível, considere utilizar o Portal de [Aplicações Azure](../../../application-gateway/overview.md) para equilibrar o tráfego entre os seus servidores de aplicações. O Azure Application Gateway é um equilibrista robusto de carga de tráfego web. Fornece afinidade de sessão baseada em cookies que mantém uma sessão de utilizador no mesmo servidor, minimizando assim os conflitos na base de dados. Alternativas à Aplicação Gateway são O Equilíbrio de [Carga Azure](../../../load-balancer/load-balancer-overview.md) e [O Gestor de Tráfego Azure.](../../../traffic-manager/traffic-manager-overview.md)

### <a name="oracle-sharding"></a>Oráculo Sharding

Sharding é um padrão de nível de dados que foi introduzido no Oracle 12.2. Permite-lhe dividir horizontalmente e dimensionar os seus dados através de bases de dados independentes. É uma arquitetura de nada partilhada onde cada base de dados está alojada numa máquina virtual dedicada, que permite uma alta leitura e escrita de entrada, além da resiliência e do aumento da disponibilidade. Este padrão elimina pontos únicos de falha, proporciona isolamento de falhas e permite a realização de upgrades sem tempo de inatividade. O tempo de inatividade de um fragmento ou de uma falha de nível de data center não afeta o desempenho ou a disponibilidade dos outros fragmentos noutros centros de dados. 

Sharding é adequado para aplicações OLTP de alta entrada que não podem pagar qualquer tempo de inatividade. Todas as linhas com a mesma chave de sharding são sempre garantidas para estar no mesmo fragmento, aumentando assim o desempenho proporcionando a elevada consistência. As aplicações que utilizam sharding devem ter um modelo de dados bem definido e estratégia de distribuição de dados (haxixe consistente, gama, lista ou compósito) que aceda principalmente a dados utilizando uma chave de sharding (por exemplo, *clienteId* ou *contaNum).* A Sharding também permite armazenar conjuntos específicos de dados mais próximos dos clientes finais, ajudando-o assim a cumprir os seus requisitos de desempenho e conformidade.

Recomenda-se que reproduza os seus fragmentos para alta disponibilidade e recuperação de desastres. Esta configuração pode ser feita usando tecnologias Oracle, como A Oracle Data Guard ou Oracle GoldenGate. Uma unidade de replicação pode ser um fragmento, uma parte de um fragmento, ou um grupo de fragmentos. A disponibilidade de uma base de dados espumosa não é afetada por uma paragem ou desaceleração de um ou mais fragmentos. Para uma elevada disponibilidade, os fragmentos de espera podem ser colocados na mesma zona de disponibilidade onde os fragmentos primários são colocados. Para a recuperação de desastres, os fragmentos de espera podem ser localizados em outra região. Também pode implantar fragmentos em várias regiões para servir o tráfego nessas regiões. Leia mais sobre configurar alta disponibilidade e replicação da sua base de dados em [conjunto Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

O Oracle Sharding consiste principalmente nos seguintes componentes. Mais informações sobre estes componentes podem ser encontradas na [documentação da Oracle Sharding:](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)

- **Catálogo Shard** - Base de dados Oracle de uso especial que é uma loja persistente para todos os dados de configuração da base de dados Shard. Todas as alterações de configuração, tais como a adição ou remoção de fragmentos, o mapeamento dos dados e os DDLs numa base de dados de fragmentos são iniciados no catálogo do fragmento. O catálogo de fragmentos também contém a cópia principal de todas as tabelas duplicadas num SDB. 

  O catálogo de fragmentos utiliza vistas materializadas para replicar automaticamente alterações em tabelas duplicadas em todos os fragmentos. A base de dados do catálogo de fragmentos também funciona como um coordenador de consulta usado para processar consultas e consultas multi-fragmentos que não especificam uma chave de sharding. 
  
  Utilizar a Oracle Data Guard em conjunto com zonas de disponibilidade ou conjuntos de disponibilidade para o catálogo de fragmentos alta disponibilidade é uma boa prática recomendada. A disponibilidade do catálogo de fragmentos não tem qualquer impacto na disponibilidade da base de dados espumosa. Um tempo de inatividade no catálogo de fragmentos só afeta operações de manutenção e consultas multishard durante o breve período em que a Guarda de Dados falha. As transações online continuam a ser encaminhadas e executadas pelo SDB e não são afetadas por uma falha no catálogo.

- **Diretores** de fragmentos - Serviços leves que precisam de ser implantados em cada região/zona de disponibilidade onde os seus fragmentos residem. Os Diretores de Fragmentos são Global Service Managers implantados no contexto da Oracle Sharding. Para uma elevada disponibilidade, recomenda-se que implemente pelo menos um diretor de fragmentos em cada zona de disponibilidade onde os seus fragmentos existam. 

  Ao ligar-se à base de dados inicialmente, a informação de encaminhamento é criada por um diretor de fragmentos e é colocada em cache para pedidos subsequentes, contornando o diretor do fragmento. Uma vez que a sessão é estabelecida com um fragmento, todas as consultas SQL e DMLs são suportadas e executadas no âmbito do fragmento dado. Este encaminhamento é rápido e é usado para todas as cargas de trabalho OLTP que realizam transações intra-shard. É aconselhável utilizar o encaminhamento direto para todas as cargas de trabalho OLTP que requerem o maior desempenho e disponibilidade. A cache de encaminhamento atualiza-se automaticamente quando um fragmento fica indisponível ou ocorrem alterações na topologia do sharding. 
  
  Para o encaminhamento dependente de dados de alto desempenho, a Oracle recomenda a utilização de um conjunto de ligação ao aceder a dados na base de dados esofanada. Piscinas de conexão Oracle, bibliotecas específicas da linguagem e motoristas apoiam o Oracle Sharding. Consulte a documentação da [Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) para mais detalhes.

- **Serviço global** - O serviço global é semelhante ao serviço de base de dados regular. Além de todas as propriedades de um serviço de base de dados, um serviço global tem propriedades para bases de dados esfumadas, tais como a finidade da região entre clientes e tolerância ao lag de fragmentos e replicação. Apenas um serviço Global precisa de ser criado para ler/escrever dados de/para uma base de dados espumosa. Ao utilizar o Ative Data Guard e configurar réplicas apenas de leitura dos fragmentos, pode criar outro serviço gGobal para cargas de trabalho apenas de leitura. O cliente pode usar estes serviços Globais para se conectar à base de dados.

- **Bases** de dados - Bases de dados de fragmentos são as suas bases de dados Oracle. Cada base de dados é replicada utilizando o Oracle Data Guard numa configuração de Corretor com falha de arranque rápido (FSFO) ativada. Não precisas de configurar a falha do Guarda de Dados e a replicação em cada fragmento. Isto é configurado e implementado automaticamente quando a base de dados partilhada é criada. Se um determinado fragmento falhar, a Oracle Sharing falha automaticamente nas ligações de base de dados do primário ao modo de espera.

Pode implementar e gerir bases de dados da Oracle com duas interfaces: `GDSCTL` Oracle Enterprise Manager Cloud Control GUI e/ou o utilitário da linha de comando. Pode até monitorizar os diferentes fragmentos para disponibilidade e desempenho utilizando o controlo cloud. O `GDSCTL DEPLOY` comando cria automaticamente os fragmentos e os respetivos ouvintes. Além disso, este comando implementa automaticamente a configuração de replicação utilizada para uma elevada disponibilidade de nível de fragmento especificada pelo administrador.

Há diferentes maneiras de estoirar uma base de dados:

* Sharding gerido pelo sistema - Distribui automaticamente em fragmentos usando divisórias
* Sharding definido pelo utilizador - Permite especificar o mapeamento dos dados aos fragmentos, que funciona bem quando existem requisitos regulamentares ou de localização de dados)
* Sharding compósito - Uma combinação de sharding gerido pelo sistema e definido pelo utilizador para _diferentes espaços_ de caco
* Subpartições de mesa - Semelhantes a uma tabela regular de divisórias.

Leia mais sobre os [diferentes métodos de sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) na documentação da Oracle.

Embora uma base de dados esfardita possa parecer uma única base de dados para aplicações e desenvolvedores, quando migrar de uma base de dados não esfardita para uma base de dados esfardita, é necessário um planeamento cuidadoso para determinar quais as tabelas que serão duplicadas versus fragmentos. 

As mesas duplicadas são armazenadas em todos os cacos, enquanto as mesas esferruadas são distribuídas por diferentes fragmentos. A recomendação é duplicar as tabelas pequenas e dimensionais e distribuir/fragmentos das tabelas de factos. Os dados podem ser carregados na sua base de dados com o catálogo de fragmentos como coordenador central ou através da execução da Bomba de Dados em cada fragmento. Leia mais sobre [dados migratórios para uma base de dados espumosa](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) na documentação da Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle Sharding com Guarda de Dados

A Oracle Data Guard pode ser utilizada para sharding com métodos de sharding geridos pelo sistema, definidos pelo utilizador e compósitos.

O diagrama seguinte é uma arquitetura de referência para Oracle Sharding com A oracle Data Guard usado para alta disponibilidade de cada fragmento. O diagrama de arquitetura mostra um _método composto de sharding_. O diagrama de arquitetura provavelmente diferirá para aplicações com diferentes requisitos para a localização de dados, equilíbrio de carga, alta disponibilidade, recuperação de desastres, etc. e pode usar métodos diferentes para sharding. A Oracle Sharding permite-lhe cumprir estes requisitos e escalar horizontal e eficientemente, fornecendo estas opções. Uma arquitetura semelhante pode até ser implantada usando o Oracle GoldenGate.

![Oracle Database Sharding usando zonas de disponibilidade com corretor de guarda de dados - FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Embora o sharding gerido pelo sistema seja o mais fácil de configurar e gerir, o sharding ou compósito definido pelo utilizador é adequado para cenários em que os seus dados e aplicação são geodistribuídos ou em cenários em que você precisa ter controlo sobre a replicação de cada fragmento. 

Na arquitetura anterior, o sharding compósito é usado para geodistribuir os dados e dimensionar horizontalmente os seus níveis de aplicação. O sharding composto é uma combinação de sharding gerido pelo sistema e definido pelo utilizador e, assim, proporciona o benefício de ambos os métodos. No cenário anterior, os dados são primeiro espalhados por vários espaços separados por região. Em seguida, os dados são ainda divididos por hash consistente através de vários fragmentos no espaço de fragmentos. Cada espaço de fragmentos contém vários grupos de fragmentos. Cada grupo tem vários fragmentos e é uma "unidade" de replicação, neste caso. Cada grupo contém todos os dados no espaço de fragmentos. Os grupos Deseixos A1 e B1 são grupos primários, enquanto os grupos de fragmentos A2 e B2 são standbys. Pode optar por que os fragmentos individuais sejam a unidade de replicação, em vez de um grupo de fragmentos.

Na arquitetura anterior, um diretor gSM/fragmento é implantado em todas as zonas de disponibilidade para alta disponibilidade. A recomendação é a de implantar pelo menos um diretor gSM/fragmento por centro de dados/região. Além disso, uma instância do servidor de aplicações é implementada em todas as zonas de disponibilidade que contenham um grupo de fragmentos. Esta configuração permite que a aplicação mantenha a latência entre o servidor de aplicações e a base de dados/grupo de fragmentos baixo. Se uma base de dados falhar, o servidor de aplicação na mesma zona que a base de dados de espera pode lidar com pedidos assim que a transição de funções de base de dados ocorrer. O Portal de Aplicação Azure e o diretor do shard acompanham o pedido e resposta de latência e pedidos de rota em conformidade.

Do ponto de vista da aplicação, o sistema cliente faz um pedido ao Portal de Aplicação Azure (ou outras tecnologias de equilíbrio de carga em Azure) que redireciona o pedido para a região mais próxima do cliente. O Portal de Aplicações Azure também suporta sessões pegajosas, pelo que quaisquer pedidos provenientes do mesmo cliente são encaminhados para o mesmo servidor de aplicações. O servidor de aplicações utiliza o pool de ligação em controladores de acesso a dados. Esta funcionalidade está disponível em controladores como JDBC, ODP.NET, OCI, etc. Os condutores podem reconhecer as chaves de sharding especificadas como parte do pedido. O [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) para clientes JDBC pode permitir que clientes de aplicação não-Oracle, como apache Tomcat e IIS, trabalhem com a Oracle Sharding. 

Durante o pedido inicial, o servidor de aplicação conecta-se ao diretor de fragmentos da sua região para obter informações de encaminhamento para o fragmento a que o pedido precisa de ser encaminhado. Com base na chave sharding aprovada, o diretor direciona o servidor da aplicação para o respetivo fragmento. O servidor de aplicação cacheesta informação através da construção de um mapa, e para pedidos subsequentes, contorna o diretor de fragmentos e solicita diretamente para o fragmento.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle Sharding com GoldenGate

O diagrama seguinte é uma arquitetura de referência para Oracle Sharding com Oracle GoldenGate para a alta disponibilidade na região de cada fragmento. Ao contrário da arquitetura anterior, esta arquitetura apenas retrata a elevada disponibilidade dentro de uma única região de Azure (múltiplas zonas de disponibilidade). Poderia-se implantar uma base de dados de alta disponibilidade multi-regiões (semelhante ao exemplo anterior) utilizando o Oracle GoldenGate.

![Oracle Database Sharding usando zonas de disponibilidade com GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

A arquitetura de referência anterior utiliza o método de sharding _gerido pelo sistema_ para fragmentos dos dados. Uma vez que a replicação do Oracle GoldenGate é feita a um nível de pedaços, metade dos dados replicados a um fragmento podem ser replicados para outro fragmento. A outra metade pode ser replicada a um fragmento diferente. 

A forma como os dados são replicados depende do fator de replicação. Com um fator de replicação de 2, terá duas cópias de cada pedaço de dados em todos os seus três fragmentos no grupo de fragmentos. Da mesma forma, com um fator de replicação de 3 e três fragmentos no seu grupo de fragmentos, todos os dados de cada fragmento serão replicados a todos os outros fragmentos do grupo de fragmentos. Cada fragmento no grupo de fragmentos pode ter um fator de replicação diferente. Esta configuração ajuda-o a definir o seu design de alta disponibilidade e recuperação de desastres de forma eficiente dentro de um grupo de fragmentos e em vários grupos de fragmentos.

Na arquitetura anterior, o grupo A e o grupo B contêm os mesmos dados, mas residem em diferentes zonas de disponibilidade. Se tanto o grupo A como o grupo B tiverem o mesmo fator de replicação de 3, cada linha/pedaço da sua mesa de cacos será replicado 6 vezes através dos dois grupos de fragmentos. Se o grupo A tiver um fator de replicação de 3 e o grupo B tiver um fator de replicação de 2, cada linha/pedaço será replicado 5 vezes através dos dois grupos de fragmentos.

Esta configuração evita a perda de dados se ocorrer uma falha de nível de instância ou de nível de zona de disponibilidade. A camada de aplicação é capaz de ler e escrever para cada fragmento. Para minimizar os conflitos, a Oracle Sharding designa um "pedaço mestre" para cada gama de valores de hash. Esta funcionalidade garante que os pedidos de escrita de um determinado pedaço são direcionados para o pedaço correspondente. Além disso, a Oracle GoldenGate fornece deteção e resolução automática de conflitos para lidar com quaisquer conflitos que possam surgir. Para obter mais informações e limitações de implementação do GoldenGate com a Oracle Sharding, consulte a documentação da Oracle sobre a utilização do Oracle GoldenGate com uma base de [dados esfarvidada](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

Na arquitetura anterior, um diretor gSM/fragmento é implantado em todas as zonas de disponibilidade para alta disponibilidade. A recomendação é a de implantar pelo menos um diretor gSM/fragmento por centro de dados ou região. Além disso, uma instância do servidor de aplicações é implementada em todas as zonas de disponibilidade que contenham um grupo de fragmentos. Esta configuração permite que a aplicação mantenha a latência entre o servidor de aplicações e a base de dados/grupo de fragmentos baixo. Se uma base de dados falhar, o servidor de aplicação na mesma zona que a base de dados de espera pode lidar com pedidos assim que a função de base de dados transitar. O Portal de Aplicação Azure e o diretor do shard acompanham o pedido e resposta de latência e pedidos de rota em conformidade.

Do ponto de vista da aplicação, o sistema cliente faz um pedido ao Portal de Aplicação Azure (ou outras tecnologias de equilíbrio de carga em Azure) que redireciona o pedido para a região mais próxima do cliente. O Portal de Aplicações Azure também suporta sessões pegajosas, pelo que quaisquer pedidos provenientes do mesmo cliente são encaminhados para o mesmo servidor de aplicações. O servidor de aplicações utiliza o pool de ligação em controladores de acesso a dados. Esta funcionalidade está disponível em controladores como JDBC, ODP.NET, OCI, etc. Os condutores podem reconhecer as chaves de sharding especificadas como parte do pedido. O [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) para clientes JDBC pode permitir que clientes de aplicação não-Oracle, como apache Tomcat e IIS, trabalhem com a Oracle Sharding. 

Durante o pedido inicial, o servidor de aplicação conecta-se ao diretor de fragmentos da sua região para obter informações de encaminhamento para o fragmento a que o pedido precisa de ser encaminhado. Com base na chave sharding aprovada, o diretor direciona o servidor da aplicação para o respetivo fragmento. O servidor de aplicação cacheesta informação através da construção de um mapa, e para pedidos subsequentes, contorna o diretor de fragmentos e solicita diretamente para o fragmento.

## <a name="patching-and-maintenance"></a>Remendos e manutenção

Ao implementar as suas cargas de trabalho Oracle para o Azure, a Microsoft trata de todos os patchings de nível OS do hospedeiro. Qualquer manutenção planeada ao nível de OS é comunicada antecipadamente aos clientes para permitir ao cliente esta manutenção planeada. Dois servidores de duas zonas de disponibilidade diferentes nunca são remendados simultaneamente. Consulte [A disponibilidade de máquinas virtuais](../../../virtual-machines/linux/manage-availability.md) para obter mais detalhes sobre manutenção e remendos VM. 

Remendar o seu sistema operativo de máquinavirtual pode ser automatizado utilizando a [Automatização Azure](../../../automation/automation-tutorial-update-management.md). Remendar e manter a sua base de dados Oracle pode ser automatizado e programado utilizando [pipelines Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) ou [Azure Automation](../../../automation/automation-tutorial-update-management.md) para minimizar o tempo de inatividade. Consulte a [Entrega Contínua e as Implementações Azuis/Verdes](/azure/devops/learn/what-is-continuous-delivery) para entender como pode ser utilizado no contexto das suas bases de dados Oracle.

## <a name="architecture-and-design-considerations"></a>Considerações de arquitetura e design

- Considere utilizar [a máquina virtual otimizada](../../../virtual-machines/windows/sizes-memory.md) de memória hiperthreaded com [vCPUs de núcleo limitado](../../../virtual-machines/windows/constrained-vcpu.md) para o seu VM de Base de Dados Oracle para economizar nos custos de licenciamento e maximizar o desempenho. Utilize vários discos premium ou ultra (discos geridos) para o desempenho e disponibilidade.
- Ao utilizar discos geridos, o nome do disco/dispositivo pode ser alterado nas reinicializações. Recomenda-se que utilize o UUID do dispositivo em vez do nome para garantir que os seus suportes persistem em reboots. Mais informações podem ser encontradas [aqui.](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab)
- Utilize zonas de disponibilidade para alcançar uma elevada disponibilidade na região.
- Considere utilizar discos ultra (quando disponíveis) ou discos premium para a sua base de dados Oracle.
- Considere a criação de uma base de dados de oráculo em outra região de Azure usando a Guarda de Dados Oracle.
- Considere utilizar grupos de [colocação](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) de proximidade para reduzir a latência entre a sua aplicação e o nível de base de dados.
- Criar a [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) para gestão, monitorização e exploração madeireira.
- Considere utilizar a Oracle Automatic Storage Management (ASM) para uma gestão de armazenamento simplificada para a sua base de dados.
- Utilize [os Pipelines Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines) para gerir patchings e atualizações na sua base de dados sem qualquer tempo de inatividade.
- Ajuste o seu código de aplicação para adicionar padrões nativos de nuvem, tais como padrão de [retry,](/azure/architecture/patterns/retry) [padrão de disjuntor,](/azure/architecture/patterns/circuit-breaker)e outros padrões definidos no [guia Cloud Design Patterns](/azure/architecture/patterns/) que podem ajudar a sua aplicação a ser mais resiliente.

## <a name="next-steps"></a>Passos seguintes

Reveja os seguintes artigos de referência da Oracle que se aplicam ao seu cenário.

- [Introdução à Guarda de Dados Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Conceitos de corretor de guarda de dados oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Configurar o Oracle GoldenGate para alta disponibilidade ativa](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Visão geral do Oráculo Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Ative Data Guard Far Sync Zero Data Loss a qualquer distância](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)

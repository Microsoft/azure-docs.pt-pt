---
title: Distribuição global com Azure Cosmos DB- sob o capot
description: Este artigo fornece detalhes técnicos relativos à distribuição global do Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: a46a69476a2ad6550bc7b3a533fd09565d461db3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74872133"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribuição global de dados com Azure Cosmos DB - sob o capot

Azure Cosmos DB é um serviço fundacional em Azure, por isso está implantado em todas as regiões de Azure em todo o mundo, incluindo o público, soberano, Departamento de Defesa (DoD) e nuvens governamentais. Dentro de um centro de dados, implantamos e gerimos o Azure Cosmos DB em selos maciços de máquinas, cada um com armazenamento local dedicado. Dentro de um centro de dados, o Azure Cosmos DB é implantado em muitos clusters, cada um potencialmente executando várias gerações de hardware. As máquinas dentro de um cluster são normalmente distribuídas por 10-20 domínios de falha para alta disponibilidade dentro de uma região. A imagem que se segue mostra a topologia do sistema de distribuição global Cosmos DB:

![Topologia do Sistema](./media/global-dist-under-the-hood/distributed-system-topology.png)

**A distribuição global em Azure Cosmos DB é chave na mão:** A qualquer momento, com alguns cliques ou programáticamente com uma única chamada API, pode adicionar ou remover as regiões geográficas associadas à sua base de dados Cosmos. Uma base de dados cosmos, por sua vez, consiste num conjunto de contentores Cosmos. No Cosmos DB, os contentores servem como unidades lógicas de distribuição e escalabilidade. As coleções, mesas e gráficos que cria são (internamente) apenas recipientes Cosmos. Os recipientes são completamente antinecróticos e fornecem uma margem para uma consulta. Os dados de um contentor cosmos são automaticamente indexados à ingestão. A indexação automática permite que os utilizadores questionem os dados sem os problemas de schema ou gestão de índices, especialmente numa configuração distribuída globalmente.  

- Numa determinada região, os dados dentro de um contentor são distribuídos através de uma chave de partição, que fornece e é gerida de forma transparente pelas divisórias físicas subjacentes *(distribuição local).*  

- Cada partição física também é replicada em regiões geográficas *(distribuição global).* 

Quando uma aplicação que utiliza a Cosmos DB escala elástico a entrada num contentor cosmos ou consome mais armazenamento, a Cosmos DB lida de forma transparente com operações de gestão de divisórias (split, clone, delete) em todas as regiões. Independentemente da escala, distribuição ou falhas, a Cosmos DB continua a fornecer uma imagem única do sistema dos dados dentro dos contentores, que são distribuídos globalmente por várias regiões.  

Como mostra a seguinte imagem, os dados dentro de um contentor são distribuídos ao longo de duas dimensões - dentro de uma região e de regiões, em todo o mundo:  

![divisórias físicas](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Uma partição física é implementada por um grupo de réplicas, chamada *réplica-set*. Cada máquina acolhe centenas de réplicas que correspondem a várias divisórias físicas dentro de um conjunto fixo de processos, como mostra a imagem acima. As réplicas correspondentes às divisórias físicas são colocadas dinamicamente e a carga equilibrada em todas as máquinas dentro de um cluster e centros de dados dentro de uma região.  

Uma réplica pertence exclusivamente a um inquilino da Azure Cosmos DB. Cada réplica acolhe uma instância do motor de base de [dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)da Cosmos DB, que gere os recursos, bem como os índices associados. O motor de base de dados Cosmos funciona num sistema de tipo baseado em sequência de átomos (ARS). O motor é agnóstico ao conceito de um esquema, desfocando a fronteira entre a estrutura e os valores de exemplo dos registos. Cosmos DB alcança o agnosticismo de esquema completo indexando automaticamente tudo sobre a ingestão de forma eficiente, o que permite que os utilizadores questionem os seus dados distribuídos globalmente sem terem de lidar com esquemas ou gestão de índices.

O motor de base de dados Cosmos é composto por componentes que incluem a implementação de vários primitivos de coordenação, tempos de execução de linguagem, processador de consulta, e subsistemas de armazenamento e indexação responsáveis pelo armazenamento e indexação de dados transacionais, respectivamente. Para proporcionar durabilidade e elevada disponibilidade, o motor de base de dados persiste os seus dados e índice em SSDs e replica-os entre as instâncias do motor da base de dados dentro do conjunto de réplicas, respectivamente. Os inquilinos maiores correspondem a uma maior escala de entrada e armazenamento e têm réplicas maiores ou mais ou ambas. Cada componente do sistema é totalmente assíncrono – nenhum fio bloqueia, e cada fio faz trabalho de curta duração sem incorrer em interruptores de fio desnecessários. A limitação das taxas e a pressão nas costas são canalizadas através de toda a pilha, desde o controlo de admissão a todos os caminhos de I/S. O motor de base de dados Cosmos foi concebido para explorar a moeda de grãos finos e para fornecer alta potência enquanto opera dentro de quantidades frugal de recursos do sistema.

A distribuição global da Cosmos DB baseia-se em duas abstrações fundamentais – *conjuntos de réplicas* e *conjuntos de divisórias.* Um conjunto de réplicas é um bloco lego modular para coordenação, e um conjunto de divisórias é uma sobreposição dinâmica de uma ou mais divisórias físicas distribuídas geograficamente. Para entender como funciona a distribuição global, precisamos entender estas duas abstrações fundamentais. 

## <a name="replica-sets"></a>Conjuntos de réplicas

Uma partição física é materializada como um grupo de réplicas auto-gerida e dinamicamente equilibrada de carga espalhadas por vários domínios de falha, chamados de replica-set. Este conjunto implementa coletivamente o protocolo de máquina estatal replicado para tornar os dados dentro da divisória física altamente disponíveis, duráveis e consistentes. A associação de réplicas *N* é dinâmica – continua a oscilar entre *nMin* e *NMax* com base nas falhas, operações administrativas e o tempo para as réplicas falhadas se regenerarem/recuperarem. Com base nas alterações de adesão, o protocolo de replicação também reconfigura o tamanho da leitura e da escrita de quórums. Para distribuir uniformemente o misto que é atribuído a uma determinada partição física, empregamos duas ideias: 

- Em primeiro lugar, o custo de processamento dos pedidos de escrita sobre o líder é superior ao custo de aplicação das atualizações no seguidor. Consequentemente, o líder está orçamentado mais recursos do sistema do que os seguidores. 

- Em segundo lugar, na medida do possível, o quórum de leitura para um determinado nível de consistência é composto exclusivamente pelas réplicas do seguidor. Evitamos contactar o líder para servir leituras a menos que seja necessário. Empregamos uma série de ideias da pesquisa feita sobre a relação de [carga e capacidade](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) nos sistemas baseados em quórum para os cinco [modelos](consistency-levels.md) de consistência que a Cosmos DB suporta.  

## <a name="partition-sets"></a>Conjuntos de partição

Um grupo de divisórias físicas, uma de cada uma das regiões de base de dados cosmos, é composta para gerir o mesmo conjunto de chaves replicadas em todas as regiões configuradas. Este primitivo de coordenação superior é chamado *de conjunto de divisórias* - uma sobreposição dinâmica geograficamente distribuída de divisórias físicas que gerem um determinado conjunto de teclas. Enquanto uma partição física dada (um conjunto de réplicas) é ampladentro de um cluster, um conjunto de divisórias pode abranger aglomerados, centros de dados e regiões geográficas, como mostrado na imagem abaixo:  

![Conjuntos de partição](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Pode pensar-se num conjunto de divisórias como um "super replica-set" geograficamente disperso, que é composto por múltiplos conjuntos de réplicas que possuem o mesmo conjunto de teclas. Semelhante a um conjunto de réplicas, a adesão de um conjunto de divisórias também é dinâmica – flutua com base em operações implícitas de gestão de divisórias físicas para adicionar/remover novas divisórias de/para um determinado conjunto de divisórias (por exemplo, quando se escala a entrada num recipiente, adicione/remova uma região na sua base de dados Cosmos ou quando ocorram falhas). Em virtude de cada uma das divisórias (de um conjunto de divisórias) gerir a associação de divisórias dentro do seu próprio conjunto de réplicas, a adesão é totalmente descentralizada e altamente disponível. Durante a reconfiguração de um conjunto de divisórias, a topologia da sobreposição entre divisórias físicas também é estabelecida. A topologia é selecionada dinamicamente com base no nível de consistência, distância geográfica e largura de banda disponível entre a fonte e as divisórias físicas-alvo.  

O serviço permite-lhe configurar as suas bases de dados Cosmos com uma única região de escrita ou várias regiões de escrita, e dependendo da escolha, os conjuntos de partição são configurados para aceitar escritos exatamente em uma ou todas as regiões. O sistema emprega um protocolo de consenso aninhada de dois níveis – um nível funciona dentro das réplicas de uma réplica de uma divisória física que aceita as escritas, e o outro opera ao nível de um conjunto de divisórias para fornecer garantias de encomenda completas para todas as escritas comprometidas dentro do conjunto de divisórias. Este consenso aninhado em várias camadas é fundamental para a implementação dos nossos rigorosos SLAs para alta disponibilidade, bem como para a implementação dos modelos de consistência, que a Cosmos DB oferece aos seus clientes.  

## <a name="conflict-resolution"></a>Resolução de conflitos

O nosso design para a propagação da atualização, resolução de conflitos e rastreio de causalidade é inspirado no trabalho prévio sobre [algoritmos epidémicos](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) e o sistema [Bayou.](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) Embora os núcleos das ideias tenham sobrevivido e fornecem um conveniente quadro de referência para comunicar o design do sistema do Cosmos DB, eles também sofreram uma transformação significativa à medida que as aplicamos ao sistema Cosmos DB. Isto era necessário, porque os sistemas anteriores não foram concebidos nem com a governação dos recursos nem com a escala a que a Cosmos DB precisa de operar, nem para fornecer as capacidades (por exemplo, consistência de stalete limitada) e os SLAs rigorosos e abrangentes que a Cosmos DB entrega aos seus clientes.  

Recorde-se que um conjunto de divisórias é distribuído por várias regiões e segue o protocolo de replicação de DBs cosmos (multi-master) para replicar os dados entre as divisórias físicas que compõem um determinado conjunto de divisórias. Cada partição física (de um conjunto de partição) aceita escritos e serve leituras tipicamente aos clientes que são locais daquela região. As escritas aceites por uma partição física dentro de uma região são duramente comprometidas e disponibilizadas dentro da partição física antes de serem reconhecidas ao cliente. Estas são escritas provisórias e são propagadas a outras divisórias físicas dentro do conjunto de divisórias usando um canal anti-entropia. Os clientes podem solicitar escritos provisórios ou comprometidos, aprovando um cabeçalho de pedido. A propagação anti-entropia (incluindo a frequência da propagação) é dinâmica, com base na topologia do conjunto de divisórias, na proximidade regional das divisórias físicas e no nível de consistência configurado. Dentro de um conjunto de divisórias, cosmos DB segue um esquema de compromisso primário com uma partição de árbitros dinamicamente selecionada. A seleção do árbitro é dinâmica e é parte integrante da reconfiguração do conjunto de divisórias com base na topologia da sobreposição. As escritas comprometidas (incluindo atualizações de várias filas/lotadas) são garantidas para serem encomendadas. 

Empregamos relógios vetores codificados (contendo ID da região e relógios lógicos correspondentes a cada nível de consenso no conjunto de réplicas e divisórias, respectivamente) para rastreio de causalidade e vetores de versão para detetar e resolver conflitos de atualização. A topologia e o algoritmo de seleção de pares são projetados para garantir armazenamento fixo e mínimo e sobrecarga mínima de rede de vetores de versão. O algoritmo garante a estrita propriedade de convergência.  

Para as bases de dados cosmos configuradas com várias regiões de escrita, o sistema oferece uma série de políticas flexíveis de resolução automática de conflitos para os desenvolvedores escolherem, incluindo: 

- **Last-Write-Wins (LWW),** que, por padrão, utiliza uma propriedade de carimbo de tempo definido pelo sistema (que se baseia no protocolo do relógio de sincronização do tempo). Cosmos DB também permite especificar qualquer outra propriedade numérica personalizada para ser usada para resolução de conflitos.  
- Política de **resolução de conflitos definida pela aplicação (expressa** através de procedimentos de fusão), concebida para a reconciliação semântica definida pela aplicação de conflitos. Estes procedimentos são invocados após a deteção dos conflitos de escrita sob os auspícios de uma transação de base de dados no lado do servidor. O sistema fornece exatamente uma vez garantia para a execução de um procedimento de fusão como parte do protocolo de compromisso. Existem [várias amostras](how-to-manage-conflicts.md) de resolução de conflitos disponíveis para que possa brincar.  

## <a name="consistency-models"></a>Modelos de consistência

Quer configure a sua base de dados Cosmos com uma única ou múltipla região de escrita, pode escolher entre os cinco modelos de consistência bem definidos. Com várias regiões de escrita, seguem-se alguns aspetos notáveis dos níveis de consistência:  

A consistência deslimitada garante que todas as leituras estarão dentro dos prefixos *K* ou *T* segundos da última escrita em qualquer uma das regiões. Além disso, as leituras com a consistência de tensão limitada são garantidas como monotónicas e com garantias prefixos consistentes. O protocolo anti-entropia funciona de forma limitada e garante que os prefixos não se acumulam e que a pressão de fundo sobre as escritas não tem de ser aplicada. A consistência da sessão garante a leitura monotónica, a escrita monotónica, a leitura das suas próprias escritas, a escrita de segue sondar e garantias de prefixo consistentes, em todo o mundo. Para as bases de dados configuradas com forte consistência, os benefícios (baixa latência por escrito, alta disponibilidade de escrita) de várias regiões de escrita não se aplicam, devido à replicação sincronizada entre regiões.

A semântica dos cinco modelos de consistência em Cosmos DB são descritas [aqui,](consistency-levels.md)e matematicamente descritas usando uma especificação TLA+ de alto nível [aqui](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Passos seguintes

Em seguida, aprenda a configurar a distribuição global utilizando os seguintes artigos:

* [Adicionar/remover regiões da conta de base de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como configurar clientes para multi-homing](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Como criar uma política personalizada de resolução de conflitos](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

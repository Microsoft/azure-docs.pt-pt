---
title: Distribuição global com Azure Cosmos DB- sob o capot
description: Este artigo fornece detalhes técnicos relativos à distribuição global da Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 1b47ad27abbe59eceabd15d091f88f4659d8dad6
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486391"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribuição global de dados com Azure Cosmos DB - sob o capot
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB é um serviço fundamental em Azure, por isso é implantado em todas as regiões do Azure em todo o mundo, incluindo o público, soberano, Departamento de Defesa (DoD) e nuvens governamentais. Dentro de um centro de dados, implantamos e gerimos o Azure Cosmos DB em selos maciços de máquinas, cada um com armazenamento local dedicado. Dentro de um centro de dados, a Azure Cosmos DB é implantada em muitos clusters, cada um potencialmente executando várias gerações de hardware. As máquinas dentro de um cluster são normalmente distribuídas por 10-20 domínios de falha para uma alta disponibilidade dentro de uma região. A imagem a seguir mostra a topologia do sistema de distribuição global Cosmos DB:

:::image type="content" source="./media/global-dist-under-the-hood/distributed-system-topology.png" alt-text="Topologia do Sistema" border="false":::

**A distribuição global em Azure Cosmos DB é chave na mão:** A qualquer momento, com alguns cliques ou programáticamente com uma única chamada API, pode adicionar ou remover as regiões geográficas associadas à sua base de dados Cosmos. Uma base de dados cosmos, por sua vez, consiste num conjunto de contentores cosmos. Na Cosmos DB, os contentores servem como unidades lógicas de distribuição e escalabilidade. As coleções, tabelas e gráficos que cria são (internamente) apenas recipientes Cosmos. Os recipientes são completamente esquema-agnósticos e fornecem uma margem para uma consulta. Os dados num recipiente Cosmos são automaticamente indexados após a ingestão. A indexação automática permite que os utilizadores consultam os dados sem os problemas de schema ou gestão de índices, especialmente numa configuração distribuída globalmente.  

- Numa determinada região, os dados dentro de um recipiente são distribuídos através de uma chave de partição, que fornece e é gerida de forma transparente pelas divisórias físicas subjacentes *(distribuição local).*  

- Cada partição física é também replicada em regiões geográficas *(distribuição global).* 

Quando uma aplicação que utiliza o Cosmos DB escala elasticamente a produção num recipiente cosmos ou consome mais armazenamento, a Cosmos DB lida de forma transparente com operações de gestão de partição (dividida, clone, apagar) em todas as regiões. Independentemente da escala, distribuição ou falhas, a Cosmos DB continua a fornecer uma única imagem do sistema dos dados dentro dos contentores, que são distribuídos globalmente por qualquer número de regiões.  

Como mostra a seguinte imagem, os dados dentro de um contentor são distribuídos ao longo de duas dimensões - dentro de uma região e de regiões, em todo o mundo:  

:::image type="content" source="./media/global-dist-under-the-hood/distribution-of-resource-partitions.png" alt-text="divisórias físicas" border="false":::

Uma partição física é implementada por um grupo de réplicas, chamada *réplica-set*. Cada máquina acolhe centenas de réplicas que correspondem a várias divisórias físicas dentro de um conjunto fixo de processos, como mostrado na imagem acima. As réplicas correspondentes às divisórias físicas são colocadas dinamicamente e a carga equilibrada através das máquinas dentro de um cluster e centros de dados dentro de uma região.  

Uma réplica pertence exclusivamente a um inquilino da Azure Cosmos DB. Cada réplica acolhe uma instância do motor de base de [dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)da Cosmos DB, que gere os recursos, bem como os índices associados. O motor de base de dados Cosmos opera num sistema de tipo de sequência de registo de átomos (ARS). O motor é agnóstico ao conceito de esquema, desfocando a fronteira entre a estrutura e os valores de instância dos registos. Cosmos DB alcança o agnosticismo de esquema completo indexando automaticamente tudo após a ingestão de uma forma eficiente, o que permite aos utilizadores consultar os seus dados distribuídos globalmente sem ter que lidar com schema ou gestão de índices.

O motor da base de dados Cosmos é composto por componentes, incluindo a implementação de vários primitivos de coordenação, tempos de execução da linguagem, processador de consulta, e subsistemas de armazenamento e indexação responsáveis pelo armazenamento e indexação transacional de dados, respectivamente. Para proporcionar durabilidade e elevada disponibilidade, o motor de base de dados persiste nos seus dados e índice em SSDs e replica-os entre as instâncias do motor de base de dados dentro do(s) conjuntos de réplicas, respectivamente. Os inquilinos maiores correspondem a uma maior escala de produção e armazenamento e têm réplicas maiores ou mais ou ambas. Todos os componentes do sistema são totalmente assíncronos – nenhum fio bloqueia, e cada fio faz trabalho de curta duração sem incorrer em interruptores de rosca desnecessários. A limitação das taxas e a pressão de trás são canalizadas por toda a pilha desde o controlo de admissão a todos os caminhos de E/S. O motor da base de dados cosmos é projetado para explorar a concurrency de grãos finos e para fornecer alta produção enquanto opera dentro de quantidades frugal de recursos do sistema.

A distribuição global da Cosmos DB baseia-se em duas abstrações fundamentais - *conjuntos de réplicas* e *conjuntos de divisórias*. Um conjunto de réplicas é um bloco de Lego modular para coordenação, e um conjunto de divisórias é uma sobreposição dinâmica de uma ou mais divisórias físicas distribuídas geograficamente. Para entender como funciona a distribuição global, precisamos de compreender estas duas principais abstrações. 

## <a name="replica-sets"></a>Conjuntos de réplicas

Uma partição física é materializada como um grupo de réplicas auto-gerido e dinamicamente equilibrado de carga espalhado por vários domínios de falha, chamado replica-set. Este conjunto implementa coletivamente o protocolo da máquina estatal replicada para tornar os dados dentro da partição física altamente disponíveis, duráveis e consistentes. A subscrição de conjunto de *réplicaS N* é dinâmica – continua a oscilar entre *nMin* e *NMax* com base nas falhas, operações administrativas e no tempo para as réplicas falhadas regenerarem/recuperarem. Com base nas alterações de adesão, o protocolo de replicação também reconfigura o tamanho dos quórumes de leitura e escrita. Para distribuir uniformemente a produção que é atribuída a uma determinada partição física, empregamos duas ideias: 

- Em primeiro lugar, o custo do processamento dos pedidos de escrita sobre o líder é superior ao custo de aplicação das atualizações sobre o seguidor. Consequentemente, o líder tem orçamentado mais recursos do sistema do que os seguidores. 

- Em segundo lugar, na medida do possível, o quórum de leitura para um determinado nível de consistência é composto exclusivamente pelas réplicas dos seguidores. Evitamos contactar o líder por servir leituras a menos que seja necessário. Empregamos uma série de ideias da pesquisa feita sobre a relação de [carga e capacidade](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) nos sistemas baseados no quórum para os cinco [modelos de consistência](consistency-levels.md) que a Cosmos DB suporta.  

## <a name="partition-sets"></a>Conjuntos de partição

Um grupo de divisórias físicas, uma de cada uma das configuradas com as regiões de base de dados cosmos, é composta para gerir o mesmo conjunto de chaves replicadas em todas as regiões configuradas. Esta coordenação superior primitiva é chamada *de conjunto de partição* - uma sobreposição dinâmica geograficamente distribuída de divisórias físicas gerindo um determinado conjunto de chaves. Enquanto uma determinada partição física (um conjunto de réplicas) é definida dentro de um cluster, um conjunto de divisões pode abranger aglomerados, centros de dados e regiões geográficas, como mostrado na imagem abaixo:  

:::image type="content" source="./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png" alt-text="Conjuntos de partição" border="false":::

Pode pensar num conjunto de partição como um "super conjunto de réplicas" geograficamente disperso, que é composto por múltiplos conjuntos de réplicas que possuem o mesmo conjunto de teclas. Semelhante a um conjunto de réplicas, a adesão de um conjunto de divisórias também é dinâmica – flutua com base em operações implícitas de gestão de partição física para adicionar/remover novas divisórias de/para um determinado conjunto de partição (por exemplo, quando escala a produção num recipiente, adiciona/remove uma região à sua base de dados Cosmos, ou quando ocorrem falhas). Em virtude de cada uma das divisórias (de um conjunto de partição) gerir a filiação do conjunto de partição dentro do seu próprio conjunto de réplicas, a adesão é totalmente descentralizada e altamente disponível. Durante a reconfiguração de um conjunto de partição, a topologia da sobreposição entre divisórias físicas também é estabelecida. A topologia é selecionada dinamicamente com base no nível de consistência, distância geográfica e largura de banda disponível entre a fonte e as divisórias físicas-alvo.  

O serviço permite-lhe configurar as suas bases de dados cosmos com uma única região de escrita ou várias regiões de escrita, e dependendo da escolha, os conjuntos de divisões são configurados para aceitar escritos em exatamente uma ou em todas as regiões. O sistema emprega um protocolo de consenso de dois níveis e aninhado – um nível funciona dentro das réplicas de uma réplica de uma partição física que aceita as escritas, e o outro opera ao nível de um conjunto de partição para fornecer garantias completas de encomenda para todas as escritas comprometidas dentro do conjunto de partição. Este consenso aninhado em várias camadas é fundamental para a implementação dos nossos rigorosos SLAs para uma elevada disponibilidade, bem como para a implementação dos modelos de consistência, que a Cosmos DB oferece aos seus clientes.  

## <a name="conflict-resolution"></a>Resolução de conflitos

O nosso design para a propagação de atualização, resolução de conflitos e rastreio de causalidade é inspirado no trabalho prévio sobre [algoritmos epidémicos](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) e o sistema [Bayou.](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) Embora os núcleos das ideias tenham sobrevivido e fornecendo um conveniente quadro de referência para comunicar o design do sistema cosmos DB, eles também sofreram uma transformação significativa à medida que as aplicamos ao sistema Cosmos DB. Isto era necessário, porque os sistemas anteriores não foram concebidos nem com a governação dos recursos nem com a escala a que a Cosmos DB precisa de operar, nem para fornecer as capacidades (por exemplo, a consistência limitada) e as SLAs rigorosas e abrangentes que a Cosmos DB fornece aos seus clientes.  

Lembre-se que um conjunto de divisórias é distribuído por várias regiões e segue o protocolo de replicação de Cosmos DBs (multi-região escreve) para replicar os dados entre as divisórias físicas que compreendem um determinado conjunto de divisórias. Cada divisória física (de um conjunto de divisórias) aceita escritas e serve leituras tipicamente para os clientes que são locais para aquela região. As gravações aceites por uma partição física dentro de uma região são duramente comprometidas e disponibilizadas altamente dentro da partição física antes de serem reconhecidas ao cliente. Estas são escritas provisórias e são propagadas a outras divisórias físicas dentro do conjunto de partição usando um canal anti-entropia. Os clientes podem solicitar escritos provisórios ou comprometidos, passando um cabeçalho de pedido. A propagação anti-entropia (incluindo a frequência de propagação) é dinâmica, baseada na topologia do conjunto de partição, na proximidade regional das divisórias e no nível de consistência configurado. Dentro de um conjunto de partição, Cosmos DB segue um esquema de compromisso primário com uma partição de árbitros selecionado dinamicamente. A seleção do árbitro é dinâmica e é parte integrante da reconfiguração do conjunto de partição com base na topologia da sobreposição. As escritas comprometidas (incluindo atualizações multi-linha/lotadas) são garantidas para serem encomendadas. 

Empregamos relógios vetoriais codificados (contendo ID de região e relógios lógicos correspondentes a cada nível de consenso no conjunto de réplicas e partição, respectivamente) para rastreio de causalidade e vetores de versão para detetar e resolver conflitos de atualização. A topologia e o algoritmo de seleção de pares são projetados para garantir o armazenamento fixo e mínimo e a sobrecarga mínima de rede de vetores de versão. O algoritmo garante a estrita propriedade de convergência.  

Para as bases de dados cosmos configuradas com várias regiões de escrita, o sistema oferece uma série de políticas flexíveis de resolução automática de conflitos para os desenvolvedores escolherem, incluindo: 

- **Last-Write-Wins (LWW)**, que, por padrão, utiliza uma propriedade de relógios definidos pelo sistema (que se baseia no protocolo do relógio de sincronização de tempo). Cosmos DB também permite especificar qualquer outra propriedade numérica personalizada para ser usada para resolução de conflitos.  
- **Política de resolução de conflitos definida pela aplicação (personalizada)** (expressa através de procedimentos de fusão), concebida para a reconciliação semântica definida pela aplicação de conflitos. Estes procedimentos são invocados após a deteção dos conflitos de escrita sob os auspícios de uma transação de base de dados no lado do servidor. O sistema fornece exatamente uma garantia única para a execução de um procedimento de fusão como parte do protocolo de compromisso. Existem [várias amostras](how-to-manage-conflicts.md) de resolução de conflitos disponíveis para você brincar.  

## <a name="consistency-models"></a>Modelos de consistência

Quer configuure a sua base de dados Cosmos com uma única ou múltiplas regiões de escrita, pode escolher entre os cinco modelos de consistência bem definidos. Com várias regiões de escrita, os seguintes são alguns aspetos notáveis dos níveis de consistência:  

A consistência limitada garante que todas as leituras estarão dentro dos prefixos *K* ou *T* segundos a partir da última escrita em qualquer uma das regiões. Além disso, as leituras com uma consistência deslimícula são garantidas monotónicas e com garantias de prefixo consistentes. O protocolo anti-entropia funciona de forma limitada e garante que os prefixos não se acumulam e que a retropressão nas escritas não tem de ser aplicada. A consistência da sessão garante a leitura monotónica, a escrita monótona, a leitura dos seus próprios escritos, a escrita segue a leitura e as garantias de prefixo consistentes em todo o mundo. Para as bases de dados configuradas com forte consistência, os benefícios (baixa latência de escrita, elevada disponibilidade de escrita) de múltiplas regiões de escrita não se aplicam, devido à replicação sincronizada entre regiões.

A semântica dos cinco modelos de consistência em Cosmos DB são descritas [aqui,](consistency-levels.md)e matematicamente descritas usando uma especificação TLA+ de alto nível [aqui](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Passos seguintes

Em seguida, aprenda a configurar a distribuição global utilizando os seguintes artigos:

* [Adicionar/remover regiões da conta de base de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como criar uma política de resolução de conflitos personalizada](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

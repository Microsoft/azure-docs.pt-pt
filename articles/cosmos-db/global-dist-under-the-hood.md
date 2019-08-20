---
title: Distribuição global com Azure Cosmos DB-nos bastidores
description: Este artigo fornece detalhes técnicos relacionados com a distribuição global do Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: ce943fbed0774667100f6de4c60f91c0b02de6c3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615357"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribuição de dados global com Azure Cosmos DB-nos bastidores

Azure Cosmos DB é um serviço fundamental no Azure, portanto, ele é implantado em todas as regiões do Azure no mundo inteiro, incluindo as nuvens pública, soberanas, departamento de defesa (DoD) e governamental. No Centro de dados, implementar e gerir o Azure Cosmos DB em enormes carimbos de máquinas, cada um deles com armazenamento local dedicado. Dentro de um centro de dados do Azure Cosmos DB é implementado em muitos clusters, cada qual potencialmente executando várias gerações de hardware. Os computadores em um cluster normalmente são distribuídos entre 10-20 domínios de falha para alta disponibilidade em uma região. A imagem seguinte mostra a topologia de sistema de distribuição global do Cosmos DB:

![Topologia do sistema](./media/global-dist-under-the-hood/distributed-system-topology.png)

**A distribuição global no Azure Cosmos DB está pronta para uso:** A qualquer momento, com alguns cliques ou programaticamente com uma única chamada à API, você pode adicionar ou remover as regiões geográficas associadas ao banco de dados Cosmos. Um banco de dados Cosmos, por sua vez, consiste em um conjunto de contêineres Cosmos. No Cosmos DB, os contentores servem como as unidades lógicas de distribuição e a escalabilidade. As coleções, tabelas e gráficos que cria são (internamente) apenas os contentores de Cosmos. Os contêineres são completamente independentes de esquema e fornecem um escopo para uma consulta. Dados num contentor do Cosmos são indexados automaticamente após a ingestão. A indexação automática permite que os usuários consultem os dados sem os aborrecimentos do gerenciamento de esquema ou índice, especialmente em uma instalação distribuída globalmente.  

- Em uma determinada região, os dados dentro de um contêiner são distribuídos usando uma chave de partição, que você fornece e é gerenciado de forma transparente pelas partições físicas subjacentes (*distribuição local*).  

- Cada partição física também é replicada entre regiões geográficas (*distribuição global*). 

Quando um aplicativo que usa Cosmos DB dimensiona a taxa de transferência de forma elástica em um contêiner Cosmos ou consome mais armazenamento, Cosmos DB manipula de modo transparente as operações de gerenciamento de partição (Split, clone e Delete) em todas as regiões. Independente da escala, distribuição ou falhas, o Cosmos DB continua a fornecer uma imagem de sistema único dos dados dentro de contentores, o que são distribuídos globalmente em qualquer número de regiões.  

Conforme mostrado na imagem a seguir, os dados dentro de um contêiner são distribuídos juntamente com duas dimensões – dentro de uma região e entre regiões, em todo o mundo:  

![Partições físicas](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Uma partição física é implementada por um grupo de réplicas, chamado de *conjunto*de réplicas. Cada computador hospeda centenas de réplicas que correspondem a várias partições físicas em um conjunto fixo de processos, conforme mostrado na imagem acima. Réplicas correspondente para as partições físicas são colocadas de forma dinâmica e balanceamento de carga em máquinas dentro de um cluster e datacenters dentro de uma região.  

Uma réplica pertence exclusivamente a um inquilino do Azure Cosmos DB. Cada réplica aloja uma instância do Cosmos DB [motor de base de dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), que gere os recursos, bem como os índices associados. O mecanismo de banco de dados Cosmos opera em um sistema de tipos baseado em ARS (Atom-Record-Sequence). O mecanismo é independente do conceito de um esquema, desfocando o limite entre a estrutura e os valores de instância de registros. O cosmos DB alcança agnosticism de esquema completo ao indexar automaticamente tudo o que após a ingestão de uma forma eficiente, que permite aos utilizadores consultar seus dados distribuídos globalmente sem ter de lidar com a gestão de índices ou esquemas.

O mecanismo de banco de dados Cosmos consiste em componentes, incluindo a implementação de vários primitivos de coordenação, tempos de execução de linguagem, o processador de consultas e os subsistemas de armazenamento e indexação responsáveis pelo armazenamento transacional e indexação de dados, respectivos. Para fornecer durabilidade e elevada disponibilidade, o motor de base de dados persistir os dados e índice SSDs e replica-a entre as instâncias de motor de base de dados dentro da réplica-conjuntos, respetivamente. Locatários maiores correspondem a uma escala maior de taxa de transferência e armazenamento e têm maior ou maior réplicas ou ambos. Cada componente do sistema é totalmente assíncrono – nenhum thread nunca bloqueia e cada thread faz o trabalho de curta duração sem incorrer em quaisquer comutadores de thread desnecessários. Limitação de velocidade e a pressão de back são inseridas em toda a pilha de controle de admissão para todos os caminhos de e/s. O mecanismo de banco de dados Cosmos foi projetado para explorar a simultaneidade refinada e fornecer alta taxa de transferência enquanto opera em frugal quantidades de recursos do sistema.

A distribuição global de Cosmos DB depende de duas abstrações de chave – *conjuntos* de réplicas e *conjuntos de partições*. Um conjunto de réplicas é um bloco modular de Lego para coordenação e um conjunto de partição é uma sobreposição de dinâmica de um ou mais partições físicas distribuídas geograficamente. Para entender a distribuição global como funciona, é preciso compreender estas duas abstrações de chave. 

## <a name="replica-sets"></a>Conjuntos de réplicas

Uma partição física é materializada como um grupo autogerenciado e com balanceamento de carga dinâmico de réplicas espalhadas por vários domínios de falha, chamados de conjunto de réplicas. Esse conjunto implementa coletivamente o protocolo de máquina de estado replicado para tornar os dados na partição física altamente disponíveis, duráveis e consistentes. A associação de conjunto de réplicas *N* é dinâmica – ela continua flutuando entre *nmín* e *nmáx* com base nas falhas, operações administrativas e no tempo de réplicas com falha para gerar/recuperar novamente. Com base nas alterações de associação, a replicação de protocolo também reconfigura o tamanho de leitura e escrita quorums. Para distribuir uniformemente a taxa de transferência atribuída a uma determinada partição física, empregamos duas ideias: 

- Primeiro, o custo do processamento das solicitações de gravação no líder é maior do que o custo da aplicação das atualizações no acompanhamento. Do mesmo modo, o coordenador é orçado mais recursos do sistema que o seguidores. 

- Em segundo lugar, máximo possível, o quórum de leitura para um nível de consistência determinado é composto exclusivamente as réplicas de descritos abaixo. Podemos evitar entrar em contato com o líder para satisfazer as necessidades leituras, a menos que necessário. Empregamos várias ideias da pesquisa feita na relação de [carga e capacidade](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) nos sistemas baseados em quorum para os [cinco modelos de consistência](consistency-levels.md) aos quais Cosmos DB dá suporte.  

## <a name="partition-sets"></a>Conjuntos de partição

Um grupo de partições físicas, um de cada um dos configurados com as regiões de banco de dados Cosmos, é composto para gerenciar o mesmo conjunto de chaves replicadas em todas as regiões configuradas. Esse primitivo de coordenação mais alto é chamado de *conjunto* de partições, uma sobreposição dinâmica geograficamente distribuída de partições físicas que gerenciam um determinado conjunto de chaves. Embora uma determinada partição física (um conjunto de réplicas) esteja no escopo de um cluster, um conjunto de partições pode abranger clusters, data centers e regiões geográficas, conforme mostrado na imagem abaixo:  

![Conjuntos de partição](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Pode pensar um conjunto de partição como um geograficamente disperso "super-conjunto de réplicas", que é composto de vários conjuntos de réplicas proprietário o mesmo conjunto de chaves. Semelhante a um conjunto de réplicas, a associação de um conjunto de partições também é dinâmica – ela flutua com base nas operações de gerenciamento de partição física implícita para adicionar/remover novas partições de/para um determinado conjunto de partições (por exemplo, ao escalar horizontalmente a taxa de transferência em um contêiner, adicionar/remover uma região para o banco de dados Cosmos ou quando ocorreram falhas). Em virtude de ter cada uma das partições (de um conjunto de partição) gerenciar a associação de conjunto de partições em seu próprio conjunto de réplicas, a associação é totalmente descentralizada e altamente disponível. Durante a reconfiguração de um conjunto de partição, a topologia da sobreposição entre partições físicas também é estabelecida. A topologia é selecionada dinamicamente com base no nível de consistência, distância geográfica e largura de banda de rede disponível entre as partições físicas de origem e de destino.  

O serviço permite-lhe configurar as bases de dados do Cosmos com uma região de escrita única ou várias regiões de escrita e, consoante a opção, os conjuntos de partição estão configurados para aceitarem escritas em exatamente uma ou todas as regiões. O sistema emprega um protocolo de consenso aninhado de dois níveis – um nível opera dentro das réplicas de um conjunto de réplicas de uma partição física que aceita as gravações e o outro opera no nível de um conjunto de partições para fornecer garantias de ordenação completas para todos as gravações confirmadas no conjunto de partições. Este consenso em várias camadas, aninhado é essencial para a implementação dos SLAs rigorosas para elevada disponibilidade, bem como a implementação dos modelos de consistência, Cosmos DB oferece aos seus clientes.  

## <a name="conflict-resolution"></a>Resolução de conflitos

Nosso design para a propagação de atualização, resolução de conflitos e de controlo de causalidade é inspirado de trabalho anterior no [algoritmos epidemic](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) e o [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) system. Embora os kernels das idéias tem Sobreviveu e fornecem uma arquitetura de referência conveniente para comunicar o design de sistema do Cosmos DB, eles têm também passou por transformações significativas como Aplicamos no sistema de Cosmos DB. Isso era necessário, porque os sistemas anteriores não foram projetados nem com a governança de recursos nem com a escala na qual Cosmos DB precisa operar, nem para fornecer os recursos (por exemplo, consistência de desatualização limitada) e os rigorosos e SLAs abrangentes que Cosmos DB fornece aos seus clientes.  

Lembre-se de que um conjunto de partição é distribuído em várias regiões e segue o protocolo de replicação de Cosmos BD (com vários mestres) para replicar dados entre as partições físicas que consiste num determinado conjunto de partição. Cada partição física (de um conjunto de partições) aceita gravações e atende às leituras normalmente para os clientes que são locais para essa região. As gravações aceitas por uma partição física dentro de uma região são permanentemente confirmadas e tornam-se altamente disponíveis na partição física antes de serem confirmadas para o cliente. Estas são escritas provisórias e são propagadas para outras partições físicas no conjunto de partição utilizando um canal de entropia anti. Os clientes podem pedir escritas provisórias ou consolidadas, passando um cabeçalho de pedido. A propagação de entropia anti (incluindo a frequência de propagação) é dinâmica, com base na topologia de proximidade partição-set, regional das partições físicas e a consistência, nível configurado. Dentro de um conjunto de partição, o Cosmos DB segue um esquema de consolidação primário com uma partição do arbiter dinamicamente selecionado. A seleção do arbiter é dinâmica e é uma parte integral da reconfiguração do conjunto de partição com base na topologia de sobreposição. As gravações confirmadas (incluindo várias-row/batches criados de atualizações) são garantidas para ser ordenada. 

Empregamos relógios de vetor com codificação (que contém o ID de região e relógios lógicos correspondentes a cada nível de consenso no conjunto de réplicas e o conjunto de partição, respectivamente) para controlo de causalidade e vetores para detetar e resolver conflitos de atualização de versão. A topologia e o algoritmo de seleção de elemento de rede foram concebidos para garantir fixo e um mínimo de armazenamento e a sobrecarga de rede mínima de vetores de versão. O algoritmo garante a propriedade de convergência rigorosa.  

Para as bases de dados Cosmos configurados com várias regiões de escrita, o sistema oferece vários automática de conflitos flexível políticas de resolução para os desenvolvedores à sua escolha, incluindo: 

- **Last-Write-WINS (LWW)** , que, por padrão, usa uma propriedade Timestamp definida pelo sistema (que é baseada no protocolo de relógio de sincronização de tempo). Também o cosmos DB permite-lhe especificar qualquer outra propriedade personalizada numérica a ser utilizado para resolução de conflitos.  
- **Política de resolução de conflitos definida pelo aplicativo (personalizada)** (expresso por meio de procedimentos de mesclagem), que é projetado para a reconciliação de semânticas definida pelo aplicativo de conflitos. Estes procedimentos são invocados após a deteção de conflitos de escrita-escrita dos auspícios de uma transação de base de dados no lado do servidor. O sistema fornece exatamente uma vez a garantia para a execução de um procedimento de mesclagem como parte do protocolo de compromisso. Há [várias amostras de resolução de conflitos](how-to-manage-conflicts.md) disponíveis para você brincar com.  

## <a name="consistency-models"></a>Modelos de consistência

Se você configurar o banco de dados cosmos com uma única ou várias regiões de gravação, poderá escolher entre os cinco modelos de consistência bem definidos. Com várias regiões de gravação, os seguintes são alguns aspectos notáveis dos níveis de consistência:  

A consistência de desatualização limitada garante que todas as leituras estarão dentro dos prefixos *K* ou *T* segundos da gravação mais recente em qualquer uma das regiões. Além disso, as leituras com consistência de desatualização limitada são garantidas como monotônico e com garantias de prefixo consistentes. O protocolo de entropia anti funciona de forma limitada de taxa e garante que os prefixos não acumular e backpressure nas gravações não tem de ser aplicada. A consistência da sessão garante leitura monotônico, gravação monotônico, leitura de suas próprias gravações, gravação de leitura e garantias de prefixo consistentes, em todo o mundo. Para os bancos de dados configurados com consistência forte, os benefícios (baixa latência de gravação, alta disponibilidade de gravação) de várias regiões de gravação não se aplicam, devido à replicação síncrona entre regiões.

A semântica dos cinco modelos de consistência no Cosmos DB é descrita [aqui](consistency-levels.md)e é descrita matematicamente usando uma especificação TLA + de alto nível [aqui](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Passos seguintes

Em seguida, saiba como configurar a distribuição global utilizando os seguintes artigos:

* [Adicionar/remover regiões da sua conta de base de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como configurar clientes para multi-homing](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Como criar uma política de resolução de conflito personalizada](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

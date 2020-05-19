---
title: O que é azure Cosmos DB Analytical Store (Pré-visualização)?
description: Saiba mais sobre a loja transacional Azure Cosmos DB (baseada em linhas) e loja analítica (baseada em colunas). Benefícios da loja analítica, impacto de desempenho para cargas de trabalho em larga escala e sincronização automática de dados desde loja transacional a loja analítica
author: SriChintala
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: c78a7d26100d3c3454cd96e2ac79e1767e5efcdb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597220"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>O que é azure Cosmos DB Analytical Store (Pré-visualização)?

> [!IMPORTANT]
> A loja analítica Azure Cosmos DB está atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os termos suplementares de utilização para as pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A loja analítica Azure Cosmos DB é uma loja de colunas totalmente isolada para permitir análises em larga escala contra dados operacionais no seu Azure Cosmos DB, sem qualquer impacto nas suas cargas de trabalho transacionais.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Desafios com análise em larga escala em dados operacionais

Os dados operacionais multi-modelo num contentor Da BD Azure Cosmos são armazenados internamente numa "loja transacional" indexada a linhas. O formato da loja de filas foi concebido para permitir leituras e escritos transacionais rápidos nos tempos de resposta de ordem de milissegundos e consultas operacionais. Se o seu conjunto de dados crescer grandes e complexas consultas analíticas podem ser caras em termos de produção disponibilizada nos dados armazenados neste formato. O elevado consumo de entrada aprovisionada por sua vez, impacta o desempenho das cargas de trabalho transacionais que são utilizadas pelas suas aplicações e serviços em tempo real.

Tradicionalmente, para analisar grandes quantidades de dados, os dados operacionais são extraídos da loja transacional da Azure Cosmos DB e armazenados numa camada de dados separada. Por exemplo, os dados são armazenados num armazém de dados ou num lago de dados num formato adequado. Estes dados são mais tarde utilizados para análises em larga escala e analisados utilizando motores computacionais como os clusters Apache Spark. Esta separação de camadas de armazenamento analítico e de cálculo de dados operacionais resulta em latência adicional, porque os gasodutos ETL (Extrato, Transformação, Carga) são executados com menos frequência para minimizar o impacto potencial nas suas cargas de trabalho transacionais.

Os gasodutos ETL também se tornam complexos ao manusear atualizações aos dados operacionais quando comparados com o manuseamento apenas de dados operacionais recentemente ingeridos. 

## <a name="column-oriented-analytical-store"></a>Loja analítica orientada para colunas

A loja analítica Azure Cosmos DB aborda os desafios de complexidade e latência que ocorrem com os tradicionais oleodutos ETL. A loja analítica Azure Cosmos DB pode sincronizar automaticamente os seus dados operacionais numa loja de colunas separada. O formato da loja de colunas é adequado para consultas analíticas em larga escala a serem realizadas de forma otimizada, resultando na melhoria da latência de tais consultas.

Utilizando o Azure Synapse Link, pode agora construir soluções HTAP sem ETL ligando-se diretamente à loja analítica Azure Cosmos DB da Synapse Analytics. Permite-lhe executar análises em grande escala em tempo real nos seus dados operacionais.

## <a name="analytical-store-details"></a>Detalhes da loja analítica

Quando ativa a loja analítica num recipiente Azure Cosmos DB, uma nova loja de colunas é criada internamente com base nos dados operacionais do seu recipiente. Esta loja de colunas é perspercada separadamente da loja transacional orientada para a linha para esse contentor. As inserções, atualizações e eliminações dos seus dados operacionais são automaticamente sincronizadas na loja analítica. Não precisa do feed de mudança ou da ETL para sincronizar os dados.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Loja de colunas para cargas de trabalho analíticas em dados operacionais

As cargas de trabalho analíticas normalmente envolvem agregações e digitalizações sequenciais de campos selecionados. Ao armazenar os dados numa ordem principal de colunas, a loja analítica permite que um grupo de valores para cada campo seja serializado em conjunto. Este formato reduz o IOPS necessário para digitalizar ou calcular estatísticas em campos específicos. Melhora drasticamente os tempos de resposta à consulta para os exames em grandes conjuntos de dados. 

Por exemplo, se as suas tabelas operacionais estiverem no seguinte formato:

![Tabela operacional de exemplo](./media/analytical-store-introduction/sample-operational-data-table.png)

A loja de filas persiste os dados acima num formato serializado, por linha, no disco. Este formato permite leituras transacionais mais rápidas, escrevem e consultas operacionais, tais como, "Devolver informações sobre o Produto1". No entanto, à medida que o conjunto de dados cresce e se quiser executar consultas analíticas complexas nos dados, pode ser caro. Por exemplo, se quiser obter "as tendências de venda de um produto na categoria denominada 'Equipamentos' em diferentes unidades de negócio e meses", precisa de fazer uma consulta complexa. Grandes digitalizações neste conjunto de dados podem ficar caras em termos de produção provisionada e também podem afetar o desempenho das cargas de trabalho transacionais que alimentam as suas aplicações e serviços em tempo real.

A loja analítica, que é uma loja de colunas, é mais adequada para tais consultas porque serializa campos de dados semelhantes em conjunto e reduz o IOPS do disco.

A imagem seguinte mostra loja de linhas transacional vs. loja de colunas analíticas em Azure Cosmos DB:

![Loja de fileiras transacional Vs loja de colunas analíticas em Azure Cosmos DB](./media/analytical-store-introduction/transactional-analytical-data-stores.png)

### <a name="decoupled-performance-for-analytical-workloads"></a>Desempenho dissociado para cargas de trabalho analíticas

Não há qualquer impacto no desempenho das suas cargas de trabalho transacionais devido a consultas analíticas, uma vez que a loja analítica está separada da loja transacional.  A loja analítica não necessita de unidades de pedido separadas (RUs) para serem atribuídas.

### <a name="auto-sync"></a>Auto-Sincronização

O Auto-Sync refere-se à capacidade totalmente gerida do Azure Cosmos DB, onde as inserções, atualizações, eliminações para dados operacionais são automaticamente sincronizadas da loja transacional para a loja analítica em quase 5 minutos.

A capacidade de sincronização automática juntamente com a loja analítica fornece os seguintes benefícios-chave:

#### <a name="scalability--elasticity"></a>A elasticidade & da escalabilidade

Ao utilizar divisórias horizontais, a loja transacional Azure Cosmos DB pode escalar elástico o armazenamento e a entrada sem qualquer tempo de inatividade. A partilha horizontal na loja transacional proporciona escalabilidade & elasticidade em auto-sincronização para garantir que os dados são sincronizados na loja analítica em tempo real. A sincronização de dados ocorre independentemente da entrada de tráfego transacional, quer se trate de 1000 operações/sec ou 1 milhão de operações/seg, e não afeta a produção prevista na loja transacional. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Manuseie automaticamente as atualizações de esquemas

A loja transacional Azure Cosmos DB é schema-agnóstica, e permite-lhe iterar nas suas aplicações transacionais sem ter de lidar com esquemas ou gestão de índices. Ao contrário disso, a loja analítica Azure Cosmos DB é schematizada para otimizar para o desempenho da consulta analítica. Com capacidade de sincronização automática, a Azure Cosmos DB gere a inferência do esquema sobre as últimas atualizações da loja de transações.  Também gere a representação do esquema na loja analítica fora da caixa, que inclui o manuseamento de tipos de dados aninhados.

Em curso há uma evolução de esquemas, onde novas propriedades são adicionadas ao longo do tempo, a loja analítica apresenta automaticamente um esquema sindicalizado em todos os esquemas históricos da loja transacional.

Se todos os dados operacionais da Azure Cosmos DB seguirem um esquema bem definido para análise, então o esquema é automaticamente inferido e representado corretamente na loja analítica. Se o esquema bem definido para a análise, tal como definido abaixo, for violado por certos itens, não serão incluídos na loja analítica. Se tiver cenários bloqueados devido a esquemas bem definidos para definição de análise, envie um e-mail para a equipa DoB Do [MB.](mailto:cosmosdbsynapselink@microsoft.com)

Um esquema bem definido para análise é definido com as seguintes considerações:

* Uma propriedade tem sempre o mesmo tipo em vários itens

  * Por exemplo, `{"a":123} {"a": "str"}` não tem um esquema bem definido porque às `"a"` vezes é uma corda e às vezes um número. 
  
    Neste caso, a loja analítica regista o tipo de `“a”` dados como o tipo de dados do primeiro `“a”` item que ocorre no tempo de vida do recipiente. Os itens em que o tipo de dados difere `“a”` não serão incluídos na loja analítica.
  
    Esta condição não se aplica a propriedades nulas. Por exemplo, `{"a":123} {"a":null}` ainda está bem definido.

* Os tipos de matriz devem conter um único tipo repetido

  * Por exemplo, `{"a": ["str",12]}` não é um esquema bem definido porque a matriz contém uma mistura de tipos inteiros e de cordas

* Existe um máximo de 200 propriedades em qualquer nível de nidificação de um esquema e uma profundidade máxima de nidificação de 5

  * Um item com 201 propriedades no nível superior não tem um esquema bem definido.

  * Um item com mais de cinco níveis aninhados no esquema também não tem um esquema bem definido. Por exemplo, `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* Os nomes de propriedade são únicos quando comparados de forma insensível

  * Por exemplo, os seguintes itens não têm um esquema bem definido `{"Name": "fred"} {"name": "john"}` – e são os `"Name"` `"name"` mesmos quando comparados num caso insensível

### <a name="cost-effective-archival-of-historical-data"></a>Arquivo rentável de dados históricos

O tiering de dados refere-se à separação de dados entre infraestruturas de armazenamento otimizadas para diferentes cenários. Melhorando assim o desempenho global e a custo-eficácia da pilha de dados de ponta a ponta. Com a loja analítica, a Azure Cosmos DB suporta agora o tiering automático de dados da loja transacional para a loja analítica com diferentes layouts de dados. Com loja analítica otimizada em termos de custo de armazenamento em comparação com a loja transacional, permite-lhe reter horizontes muito mais longos de dados operacionais para análise histórica.

Após a ativação da loja analítica, com base nas necessidades de retenção de dados das cargas de trabalho transacionais, pode configurar a propriedade 'Transactional Store Time to Live (Transactional Store Time to Live (Transactional TTL)' para ter registos automaticamente apagados da loja transacional após um determinado período de tempo. Da mesma forma, a 'Analytical Store Time To Live (Analytical TTL)' permite-lhe gerir o ciclo de vida dos dados retidos na loja analítica independente da loja transacional. Ao permitir a loja analítica e configurar propriedades TTL, pode ser perfeitamente nidificada e definir o período de retenção de dados para as duas lojas.

### <a name="global-distribution"></a>Distribuição Global

Se tiver uma conta Azure Cosmos DB distribuída globalmente, depois de ativar a loja analítica para um contentor, estará disponível em todas as regiões dessa conta.  Quaisquer alterações aos dados operacionais são globalmente replicadas em todas as regiões. Pode executar consultas analíticas eficazmente contra a cópia regional mais próxima dos seus dados em Azure Cosmos DB.

### <a name="security"></a>Segurança

A autenticação com a loja analítica é a mesma que a loja transacional para uma determinada base de dados. Pode utilizar chaves master ou apenas de leitura para autenticação. Você pode aproveitar o serviço ligado no Estúdio Synapse para evitar a colagem das chaves Azure Cosmos DB nos cadernos Spark. O acesso a este Serviço Linked está disponível para qualquer pessoa que tenha acesso ao espaço de trabalho.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Suporte para vários tempos de execução da Azure Synapse Analytics

A loja analítica está otimizada para proporcionar escalabilidade, elasticidade e desempenho para cargas de trabalho analíticas sem qualquer dependência dos tempos de execução da computação. A tecnologia de armazenamento é autogerida para otimizar as suas cargas de trabalho de análise sem esforços manuais.

Ao dissociar o sistema de armazenamento analítico do sistema de computação analítica, os dados na loja analítica Azure Cosmos DB podem ser consultados simultaneamente a partir dos diferentes tempos de análise suportados pela Azure Synapse Analytics. A partir de hoje, a Synapse Analytics suporta a Apache Spark e a SQL sem servidor com a loja analítica Azure Cosmos DB.

> [!NOTE]
> Só é possível ler a partir de uma loja analítica utilizando o tempo de execução da Synapse Analytics. Pode escrever os dados de volta na sua loja de transações como uma camada de serviço.

## <a name="pricing"></a><a id="analytical-store-pricing"></a>Preços

A loja analítica segue um modelo de preços baseado no consumo onde é cobrado:

* Armazenamento: o volume dos dados retidos na loja analítica todos os meses, incluindo dados históricos definidos pela Analytical TTL.

* Operações de escrita analítica: a sincronização integral das atualizações de dados operacionais para a loja analítica a partir da loja transacional (auto-sincronização)

* Operações de leitura analítica: as operações de leitura realizadas contra a loja analítica da Synapse Analytics Spark e os tempos de execução sql Serverless.

> [!NOTE]
> A loja analítica Azure Cosmos DB está disponível em pré-visualização pública gratuitamente até 30 de agosto de 2020.

Os preços da loja analítica são separados do modelo de preços da loja de transações. Não existe nenhum conceito de RUs provisionado na loja analítica. Consulte a página de [preços do Azure Cosmos DB,](https://azure.microsoft.com/pricing/details/cosmos-db/)para mais detalhes sobre o modelo de preços para loja analítica.

Para obter uma estimativa de custos de alto nível para permitir a loja analítica num recipiente Azure Cosmos DB, você pode usar o planejador de capacidade DB [Azure Cosmos](https://cosmos.azure.com/capacitycalculator/) e obter uma estimativa dos seus custos de armazenamento e escrita analíticos. Os custos de operações de leitura analítica dependem das características da carga de trabalho analítica, mas como estimativa de alto nível, a digitalização de 1 TB de dados na loja analítica normalmente resulta em 130.000 operações de leitura analítica, e resulta num custo de $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a>Tempo analítico para viver (TTL)

A TTL analítica indica quanto tempo os dados devem ser retidos na sua loja analítica, para um recipiente. 

As inserções, atualizações, eliminações para dados operacionais são automaticamente sincronizadas de loja transacional para loja analítica, independentemente da configuração ttl transacional. A conservação destes dados operacionais na loja analítica pode ser controlada pelo valor Analítico TTL ao nível do contentor, conforme especificado abaixo:

TTL analítico num recipiente é definido utilizando a `AnalyticalStoreTimeToLiveInSeconds` propriedade:

* Se o valor for definido para "0", em falta (ou definido para nulo): a loja analítica está desativada e nenhum dado é replicado de loja transacional para loja analítica

* Se presente e o valor for definido para "-1": a loja analítica retém todos os dados históricos, independentemente da conservação dos dados na loja transacional. Esta definição indica que a loja analítica tem uma retenção infinita dos seus dados operacionais

* Se presente e o valor for definido para algum número positivo "n": os itens expirarão a partir da loja analítica "n" segundos após o seu último tempo modificado na loja transacional. Esta definição pode ser alavancada se pretender reter os seus dados operacionais por um período limitado de tempo na loja analítica, independentemente da conservação dos dados na loja transacional.

Alguns pontos a considerar:
*   Depois de a loja analítica ser ativada com um valor TTL analítico, pode ser atualizada para um valor válido diferente mais tarde. 
*   Embora o TTL transacional possa ser definido ao nível do recipiente ou do item, o TTL analítico só pode ser definido ao nível do recipiente atualmente.
*   Pode conseguir uma maior retenção dos seus dados operacionais na loja analítica, definindo analítico TTL >= TTL transacional ao nível do contentor.
*   A loja analítica pode ser feita para espelhar a loja transacional definindo TTL analítico = TTL transacional .

Para saber mais, veja [como configurar o TTL analítico num recipiente](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte os seguintes docs:

* [Ligação Azure Synapse para Azure Cosmos DB](synapse-link.md)

* [Começa com azure synapse Link para Azure Cosmos DB](configure-synapse-link.md)

* [Perguntas frequentes sobre Synapse Link for Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Ligação Azure Synapse para casos de utilização de Azure Cosmos DB](synapse-link-use-cases.md)

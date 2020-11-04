---
title: O que é Azure Cosmos DB Analytical Store (Preview)?
description: Saiba mais sobre a loja transacional Azure Cosmos DB (baseada em linha) e analítica (baseada em colunas). Benefícios da loja analítica, impacto no desempenho para cargas de trabalho em larga escala e sincronização automática de dados da loja transacional para a loja analítica
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: rosouz
ms.openlocfilehash: d0727f512ab24342b493ba2563262a9253d95d8c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339977"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>O que é a loja analítica Azure Cosmos DB (Preview)?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A loja analítica Azure Cosmos DB está atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A loja analítica Azure Cosmos DB é uma loja de colunas totalmente isolada para permitir análises em larga escala contra dados operacionais no seu Azure Cosmos DB, sem qualquer impacto nas suas cargas de trabalho transacionais.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Desafios com análises em larga escala em dados operacionais

Os dados operacionais multi-modelo num contentor DB Azure Cosmos são armazenados internamente numa "loja transacional" indexada à linha. O formato da loja row foi projetado para permitir leituras e escritos transacionais rápidos nos tempos de resposta de ordem de milissegundos e consultas operacionais. Se o seu conjunto de dados crescer grandes e complexos consultas analíticas podem ser dispendiosas em termos de produção a forerada nos dados armazenados neste formato. O elevado consumo de produção aprovisionada, por sua vez, tem impacto no desempenho de cargas de trabalho transacionais que são utilizadas pelas suas aplicações e serviços em tempo real.

Tradicionalmente, para analisar grandes quantidades de dados, os dados operacionais são extraídos da loja transacional da Azure Cosmos DB e armazenados numa camada de dados separada. Por exemplo, os dados são armazenados num armazém de dados ou num lago de dados num formato adequado. Estes dados são mais tarde utilizados para análises em larga escala e analisados utilizando o motor computacional, como os clusters Apache Spark. Esta separação das camadas de armazenamento e cálculo analíticos dos dados operacionais resulta em latência adicional, porque os gasodutos ETL (Extract, Transform, Load) são executados com menos frequência para minimizar o impacto potencial nas suas cargas de trabalho transacionais.

Os gasodutos ETL tornam-se também complexos ao lidar com atualizações aos dados operacionais quando comparados com o manuseamento apenas de dados operacionais recém-ingeridos. 

## <a name="column-oriented-analytical-store"></a>Loja analítica orientada para colunas

A loja analítica Azure Cosmos DB aborda os desafios de complexidade e latência que ocorrem com os oleodutos tradicionais da ETL. A loja analítica Azure Cosmos DB pode sincronizar automaticamente os seus dados operacionais numa loja de colunas separada. O formato da loja de colunas é adequado para consultas analíticas em larga escala a serem realizadas de forma otimizada, resultando em melhorar a latência de tais consultas.

Utilizando o Azure Synapse Link, pode agora construir soluções HTAP sem ETL ligando diretamente à loja analítica Azure Cosmos DB da Synapse Analytics. Permite-lhe executar análises em larga escala em tempo real nos seus dados operacionais.

## <a name="features-of-analytical-store"></a>Características da loja analítica 

Quando ativa a loja analítica num recipiente DB Azure Cosmos, uma nova loja de colunas é criada internamente com base nos dados operacionais do seu recipiente. Esta loja de colunas é persistido separadamente da loja transacional orientada para a linha para aquele recipiente. As inserções, atualizações e eliminações dos seus dados operacionais são automaticamente sincronizadas na loja analítica. Não precisa do feed de alteração ou etl para sincronizar os dados.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Loja de colunas para cargas de trabalho analíticas em dados operacionais

Cargas de trabalho analíticas normalmente envolvem agregações e análises sequenciais de campos selecionados. Ao armazenar os dados numa ordem de coluna-grande, a loja analítica permite que um grupo de valores para cada campo seja serializado em conjunto. Este formato reduz o IOPS necessário para digitalizar ou calcular estatísticas sobre campos específicos. Melhora drasticamente os tempos de resposta da consulta para digitalizações em grandes conjuntos de dados. 

Por exemplo, se as suas tabelas operacionais estiverem no seguinte formato:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Quadro operacional de exemplo" border="false":::

A loja de linha persiste os dados acima num formato serializado, por linha, no disco. Este formato permite leituras, escritas e consultas operacionais mais rápidas, tais como" Devolução de informações sobre o Produto1". No entanto, à medida que o conjunto de dados cresce grande e se quiser executar consultas analíticas complexas sobre os dados, pode ser caro. Por exemplo, se quiser obter "as tendências de venda de um produto na categoria denominada 'Equipamento' em diferentes unidades de negócio e meses", é necessário fazer uma consulta complexa. As grandes análises neste conjunto de dados podem ser dispendiosas em termos de produção aprovisionada e também podem ter impacto no desempenho das cargas de trabalho transacionais que alimentam as suas aplicações e serviços em tempo real.

A loja analítica, que é uma loja de colunas, é mais adequada para tais consultas porque serializa campos de dados semelhantes em conjunto e reduz o disco IOPS.

A imagem a seguir mostra loja de linha transacional vs. loja de colunas analíticas em Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Loja de linha transacional Vs loja de colunas analíticas em Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Desempenho dissociado para cargas analíticas

Não há qualquer impacto no desempenho das suas cargas de trabalho transacionais devido a consultas analíticas, uma vez que a loja analítica está separada da loja transacional.  A loja analítica não necessita de unidades de pedido separadas (RUs) para ser atribuída.

### <a name="auto-sync"></a>Auto-Sincronização

Auto-Sync refere-se à capacidade totalmente gerida do Azure Cosmos DB onde os inserções, atualizações, eliminações para dados operacionais são automaticamente sincronizados da loja transacional para a loja analítica em tempo real. A latência de sincronização automática é geralmente dentro de 2 minutos. Em caso de base de dados de produção partilhada com um grande número de contentores, a latência auto-sincronizada de contentores individuais pode ser maior e demorar até 5 minutos. Gostaríamos de saber mais como esta latência se encaixa nos seus cenários. Para isso, por favor contacte a equipa DB da [Azure Cosmos.](mailto:cosmosdbsynapselink@microsoft.com)

A capacidade de auto-sincronização juntamente com a loja analítica proporciona os seguintes benefícios principais:

#### <a name="scalability--elasticity"></a>Escalabilidade & elasticidade

Ao utilizar divisórias horizontais, a loja transacional Azure Cosmos DB pode dimensionar elasticamente o armazenamento e a produção sem qualquer tempo de inatividade. A divisória horizontal na loja transacional proporciona uma escalabilidade & elasticidade em auto-sincronização para garantir que os dados são sincronizados na loja analítica em tempo real. A sincronização de dados acontece independentemente da produção transacional de tráfego, quer se trate de 1000 operações/seg ou 1 milhão de operações/seg, e não afeta o rendimento previsto na loja transacional. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Lidar automaticamente com atualizações de esquemas

A loja de transações Azure Cosmos DB é schema-agnóstica, e permite-lhe iterar nas suas aplicações transacionais sem ter de lidar com schema ou gestão de índices. Em contraste com isso, a loja analítica Azure Cosmos DB é schematizada para otimizar para o desempenho de consulta analítica. Com a capacidade de auto-sincronização, a Azure Cosmos DB gere a inferência de esquema sobre as últimas atualizações da loja transacional.  Também gere a representação de esquemas na loja analítica fora da caixa, o que inclui o tratamento de tipos de dados aninhados.

À medida que o seu esquema evolui, e novas propriedades são adicionadas ao longo do tempo, a loja analítica apresenta automaticamente um esquema sindicalizado em todos os esquemas históricos na loja transacional.

##### <a name="schema-constraints"></a>Restrições de esquema

Os seguintes constrangimentos são aplicáveis nos dados operacionais da Azure Cosmos DB quando permite que a loja analítica infera e represente corretamente o esquema:

* Você pode ter um máximo de 200 propriedades em qualquer nível de nidificação no esquema e uma profundidade máxima de nidificação de 5.
  
  * Um item com 201 propriedades ao nível superior não satisfaz este constrangimento e, portanto, não será representado na loja analítica.
  * Um item com mais de cinco níveis aninhados no esquema também não satisfaz este constrangimento e, portanto, não será representado na loja analítica. Por exemplo, o seguinte item não satisfaz o requisito:

     `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* Os nomes dos imóveis devem ser únicos quando comparados com o caso de forma insensível. Por exemplo, os seguintes itens não satisfazem esta restrição e, portanto, não estarão representados na loja analítica:

  `{"Name": "fred"} {"name": "john"}` – "Nome" e "nome" são os mesmos quando comparados de forma insensível.

##### <a name="schema-representation"></a>Representação de Schema

Há dois modos de representação do esquema no arquivo analítico. Estes modos têm compromissos entre a simplicidade de uma representação em colunas, o processamento de esquemas polimórficos e a simplicidade da experiência de consulta:

* Representação de esquemas bem definidos
* Representação total do esquema de fidelidade

> [!NOTE]
> Para as contas API SQL (Core), quando a loja analítica está ativada, a representação de esquemas padrão na loja analítica está bem definida. Enquanto que para a Azure Cosmos DB API para contas MongoDB, a representação de esquemas padrão na loja analítica é uma representação total do esquema de fidelidade. Se tiver cenários que exijam uma representação de esquema diferente da oferta padrão para cada uma destas APIs, contacte a equipa DB do [Azure Cosmos](mailto:cosmosdbsynapselink@microsoft.com) para o permitir.

**Representação de esquemas bem definidos**

A representação de esquema bem definida cria uma simples representação tabular dos dados schema-agnósticos na loja transacional. A representação de esquemas bem definidas tem as seguintes considerações:

* Uma propriedade tem sempre o mesmo tipo em vários itens.

  * Por exemplo, `{"a":123} {"a": "str"}` não tem um esquema bem definido porque às `"a"` vezes é uma corda e às vezes um número. Neste caso, a loja analítica regista o tipo de dados `“a”` como o tipo de dados do primeiro item que ocorre durante o tempo de vida do `“a”` recipiente. Os itens em que o tipo de `“a”` dados difere não serão incluídos na loja analítica.
  
    Esta condição não se aplica a propriedades nulas. Por exemplo, `{"a":123} {"a":null}` ainda está bem definido.

* Os tipos de matriz devem conter um único tipo repetido.

  * Por exemplo, `{"a": ["str",12]}` não é um esquema bem definido porque a matriz contém uma mistura de tipos inteiros e de cordas.

> [!NOTE]
> Se a loja analítica Azure Cosmos DB seguir a representação de esquemas bem definida e a especificação acima for violada por certos itens, esses itens não serão incluídos na loja analítica.

**Representação total do esquema de fidelidade**

A representação total do esquema de fidelidade foi concebida para lidar com toda a amplitude de esquemas polimórficos nos dados operacionais schema-agnósticos. Nesta representação de esquema, nenhum item é retirado da loja analítica, mesmo que as restrições de esquema bem definidas (que não são campos de tipo de dados mistos nem matrizes de tipo de dados mistos) sejam violadas.

Isto é conseguido traduzindo as propriedades das folhas dos dados operacionais para a loja analítica com colunas distintas com base no tipo de dados de valores na propriedade. Os nomes da propriedade da folha são estendidos com tipos de dados como um sufixo no esquema de loja analítica de modo que podem ser consultas sem ambiguidade.

Por exemplo, vamos recolher o seguinte documento de amostra na loja transacional:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

A propriedade da folha `streetNo` dentro do objeto aninhado `address` será representada no esquema de loja analítica como uma coluna `address.object.streetNo.int32` . O tipo de dados é adicionado como um sufixo à coluna. Desta forma, se outro documento for adicionado à loja transacional onde o valor da propriedade da folha `streetNo` é "123" (note que é uma corda), o esquema da loja analítica evolui automaticamente sem alterar o tipo de coluna previamente escrita. Uma nova coluna adicionada à loja analítica `address.object.streetNo.string` como onde este valor de "123" é armazenado.

**Tipo de dados para mapa de sufixo**

Aqui está um mapa de todos os tipos de dados de propriedade e suas representações de sufixo na loja analítica:

|Tipo de dados original  |Sufixo  |Exemplo  |
|---------|---------|---------|
| Double (Duplo) |  ".float64" |    24.99|
| Matriz | ".array" |    ["a", "b"]|
|Binário | ".binário" |0|
|Booleano    | ".bool"   |Verdadeiro|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Nulo   | ".nulo"   | nulo|
|String|    ".string" | "ABC"|
|Timestamp |    ".timetamp" |  Tempo de carregamento (0,0)|
|DateTime   |".data"    | ISODate ("2020-08-21T07:43:07.375Z")|
|ObjectId   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|Documento   |".objeto" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Arquivo rentável de dados históricos

O tiering de dados refere-se à separação de dados entre infraestruturas de armazenamento otimizadas para diferentes cenários. Melhorando assim o desempenho global e a eficácia de custos da pilha de dados de ponta a ponta. Com a loja analítica, a Azure Cosmos DB suporta agora o tiering automático de dados da loja transacional para a loja analítica com diferentes layouts de dados. Com a loja analítica otimizada em termos de custo de armazenamento em comparação com a loja transacional, permite-lhe reter horizontes muito mais longos de dados operacionais para análise histórica.

Depois de a loja analítica estar ativada, com base nas necessidades de retenção de dados das cargas de trabalho transacionais, pode configurar a propriedade 'Transactional Store Time to Live (Transactional TTL)' para ter registos automaticamente apagados da loja transacional após um determinado período de tempo. Da mesma forma, o 'Tempo de Loja Analítica Para Viver (TTL Analítico)' permite-lhe gerir o ciclo de vida dos dados retidos na loja analítica independente da loja transacional. Ao ativar a loja analítica e configurar propriedades TTL, pode tiering perfeitamente e definir o período de retenção de dados para as duas lojas.

### <a name="global-distribution"></a>Distribuição Global

Se tiver uma conta DB Azure Cosmos distribuída globalmente, depois de ativar uma loja analítica para um recipiente, estará disponível em todas as regiões dessa conta.  Quaisquer alterações aos dados operacionais são globalmente replicadas em todas as regiões. Pode executar consultas analíticas eficazmente contra a cópia regional mais próxima dos seus dados em Azure Cosmos DB.

### <a name="security"></a>Segurança

A autenticação com a loja analítica é a mesma que a loja transacional para uma determinada base de dados. Pode utilizar as chaves primárias ou apenas de leitura para a autenticação. Você pode aproveitar o serviço ligado no Synapse Studio para evitar colar as chaves DB do Azure Cosmos nos cadernos Spark. O acesso a este Serviço Linked está disponível para qualquer pessoa que tenha acesso ao espaço de trabalho.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Suporte para vários tempos de análise Azure Synapse

A loja analítica está otimizada para proporcionar escalabilidade, elasticidade e desempenho para cargas de trabalho analíticas sem qualquer dependência dos tempos de cálculo. A tecnologia de armazenamento é auto-gerida para otimizar as suas cargas de trabalho de análise sem esforços manuais.

Ao dissociar o sistema de armazenamento analítico do sistema de computação analítica, os dados na loja analítica Azure Cosmos DB podem ser consultados simultaneamente a partir dos diferentes tempos de execução analíticos suportados pela Azure Synapse Analytics. A partir de hoje, a Synapse Analytics suporta o Apache Spark e o SQL sem servidor com a loja analítica Azure Cosmos DB.

> [!NOTE]
> Só é possível ler na loja de análise utilizando o tempo de funcionação do Synapse Analytics. Pode escrever os dados de volta à sua loja transacional como uma camada de serviço.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Preços

A loja analítica segue um modelo de preços baseado no consumo onde é cobrado:

* Armazenamento: o volume dos dados retidos na loja analítica todos os meses, incluindo dados históricos definidos pela Analytical TTL.

* Operações de escrita analítica: a sincronização totalmente gerida das atualizações de dados operacionais para a loja analítica a partir da loja transacional (auto-sincronização)

* Operações de leitura analítica: as operações de leitura realizadas contra a loja analítica da Synapse Analytics Spark e dos tempos de funcionação sem servidor SQL.

> [!NOTE]
> A loja analítica Azure Cosmos DB está atualmente disponível em pré-visualização pública, livre de quaisquer encargos.

Os preços das lojas analíticas são separados do modelo de preços da loja de transações. Não existe nenhum conceito de RUs a provisionados na loja analítica. Consulte [a página de preços da Azure Cosmos DB,](https://azure.microsoft.com/pricing/details/cosmos-db/)para obter todos os detalhes sobre o modelo de preços para a loja analítica.

Para obter uma estimativa de custos de alto nível para permitir uma loja analítica num recipiente Azure Cosmos DB, você pode usar o [planejador Azure Cosmos DB Capacity](https://cosmos.azure.com/capacitycalculator/) e obter uma estimativa do seu armazenamento analítico e escrever custos de operações. Os custos das operações de leitura analítica dependem das características da carga de trabalho analítica, mas como estimativa de alto nível, a verificação de 1 TB de dados na loja analítica resulta tipicamente em 130.000 operações de leitura analítica, e resulta num custo de $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Tempo analítico para viver (TTL)

O TTL Analítico indica quanto tempo os dados devem ser retidos no arquivo analítico, para um contentor. 

Se a loja analítica estiver ativada, insira, atualizações, eliminações para dados operacionais são automaticamente sincronizadas da loja transacional para a loja analítica, independentemente da configuração transacional de TTL. A conservação destes dados operacionais na loja analítica pode ser controlada pelo valor Analítico TTL ao nível do contentor, conforme especificado abaixo:

A TTL Analítica num recipiente é definida utilizando a `AnalyticalStoreTimeToLiveInSeconds` propriedade:

* Se o valor for definido para "0", em falta (ou definido para nulo): a loja analítica é desativada e nenhum dado é replicado da loja transacional para a loja analítica

* Se presente e o valor for definido para "-1": a loja analítica conserva todos os dados históricos, independentemente da conservação dos dados na loja transacional. Esta definição indica que a loja analítica tem uma retenção infinita dos seus dados operacionais

* Se o presente e o valor for definido para algum número positivo "n": os itens expirarão a partir da loja analítica "n" segundos após o seu último tempo modificado na loja transacional. Esta definição pode ser alavancada se pretender reter os seus dados operacionais por um período limitado de tempo na loja analítica, independentemente da conservação dos dados na loja transacional

Alguns pontos a considerar:

*   Depois de a loja analítica ser ativada com um valor TTL analítico, pode ser atualizada para um valor válido diferente mais tarde. 
*   Embora a TTL transacional possa ser definida ao nível do recipiente ou do item, a TTL analítica só pode ser definida ao nível do recipiente atualmente.
*   Pode obter uma maior retenção dos seus dados operacionais na loja analítica, definindo >TTL Analítica = TTL transacional ao nível do recipiente.
*   A loja analítica pode ser feita para espelhar a loja transacional, definindo TTL analítico = TTL transacional.

Quando ativa a loja analítica num recipiente:

* A partir do portal Azure, a opção TTL analítica é definida para o valor padrão de -1. Pode alterar este valor para 'n' segundos, navegando para as definições do contentor no Data Explorer. 
 
* A partir do Azure SDK ou PowerShell ou CLI, a opção TTL analítica pode ser ativada definindo-a para -1 ou 'n'. 

Para saber mais, consulte [como configurar a TTL analítica num recipiente](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte os seguintes documentos:

* [Ligação Azure Synapse para Azure Cosmos DB](synapse-link.md)

* [Introdução ao Azure Synapse Link para o Azure Cosmos DB](configure-synapse-link.md)

* [Perguntas mais frequentes sobre o Synapse Link para o Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Casos de utilização do Azure Synapse Link para o Azure Cosmos DB](synapse-link-use-cases.md)

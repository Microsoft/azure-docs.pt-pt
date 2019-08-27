---
title: Otimizando o custo da taxa de transferência no Azure Cosmos DB
description: Este artigo explica como otimizar os custos de taxa de transferência para os dados armazenados no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: rimman
ms.openlocfilehash: d874f1ba8823ceddbef378decde127cef4ff8885
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020112"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Otimizar o custo de taxa de transferência provisionada no Azure Cosmos DB

Ao oferecer o modelo de taxa de transferência provisionada, Azure Cosmos DB oferece desempenho previsível em qualquer escala. Reservar ou provisionar a taxa de transferência antecipadamente elimina o "efeito de vizinho ruidosa" em seu desempenho. Você especifica a quantidade exata de produtividade de que precisa e Azure Cosmos DB garante a taxa de transferência configurada, apoiada por SLA.

Você pode começar com uma taxa de transferência mínima de 400 RU/s e escalar verticalmente até dezenas de milhões de solicitações por segundo ou ainda mais. Cada solicitação que você emite em seu contêiner ou banco de dados Cosmos do Azure, como uma solicitação de leitura, solicitação de gravação, solicitação de consulta, procedimentos armazenados têm um custo correspondente que é deduzido de sua taxa de transferência provisionada. Se você provisionar 400 RU/s e emitir uma consulta que custa 40 RUs, você poderá emitir 10 consultas por segundo. Qualquer solicitação além disso terá uma taxa limitada e você deverá repetir a solicitação. Se você estiver usando drivers de cliente, eles dão suporte à lógica de repetição automática.

Você pode provisionar a taxa de transferência em bancos de dados ou contêineres e cada estratégia pode ajudá-lo a economizar em custos dependendo do cenário.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Otimizar pela taxa de transferência de provisionamento em diferentes níveis

* Se você provisionar a taxa de transferência em um banco de dados, todos os contêineres, por exemplo coleções/tabelas/grafos dentro desse banco de dados, poderão compartilhar a taxa de transferência com base na carga. A taxa de transferência reservada no nível do banco de dados é compartilhada de maneira desigual, dependendo da carga de trabalho em um conjunto específico de contêineres.

* Se você provisionar a taxa de transferência em um contêiner, a taxa de transferência será garantida para esse contêiner, apoiada pelo SLA. A escolha de uma chave de partição lógica é crucial para a distribuição uniforme da carga em todas as partições lógicas de um contêiner. Consulte os artigos [particionamento](partitioning-overview.md) e [dimensionamento horizontal](partition-data.md) para obter mais detalhes.

Veja a seguir algumas diretrizes para decidir sobre uma estratégia de produtividade provisionada:

**Considere provisionar a taxa de transferência em um banco de dados Cosmos do Azure (que contém um conjunto de contêineres) se**:

1. Você tem algumas dúzias de contêineres de Cosmos do Azure e deseja compartilhar a taxa de transferência em algumas ou em todas elas. 

2. Você está migrando de um banco de dados de locatário único projetado para ser executado em VMs hospedadas em IaaS ou no local, por exemplo, NoSQL ou bancos de dados relacionais para Azure Cosmos DB. E se você tiver muitas coleções/tabelas/grafos e não quiser fazer alterações no modelo de dados. Observe que talvez você precise comprometer alguns dos benefícios oferecidos pelo Azure Cosmos DB se não estiver atualizando seu modelo de dados ao migrar de um banco de dado local. É recomendável que você sempre acesse o modelo de dados para obter o máximo em termos de desempenho e também para otimizar os custos. 

3. Você deseja absorver picos não planejados em cargas de trabalho em virtude da taxa de transferência em pool no nível do banco de dados sujeito a um pico inesperado na carga de trabalho. 

4. Em vez de definir uma taxa de transferência específica em contêineres individuais, você se preocupa em obter a taxa de transferência agregada em um conjunto de contêineres no banco de dados.

**Considere provisionar a taxa de transferência em um contêiner individual se:**

1. Você tem alguns contêineres de Cosmos do Azure. Como Azure Cosmos DB não é independente de esquema, um contêiner pode conter itens que têm esquemas heterogêneos e não exige que os clientes criem vários tipos de contêineres, um para cada entidade. É sempre uma opção a ser considerada se o agrupamento separado de dizer 10-20 contêineres em um único contêiner faz sentido. Com um mínimo de 400 RUs para contêineres, o pool de todos os contêineres 10-20 em um pode ser mais econômico. 

2. Você deseja controlar a taxa de transferência em um contêiner específico e obter a taxa de transferência garantida em um determinado contêiner apoiado pelo SLA.

**Considere um híbrido das duas estratégias acima:**

1. Como mencionado anteriormente, Azure Cosmos DB permite misturar e corresponder as duas estratégias acima, de modo que agora você pode ter alguns contêineres no banco de dados Cosmos do Azure, o que pode compartilhar a taxa de transferência provisionada no banco de dados, bem como alguns contêineres no mesmo banco de dados , que pode ter quantidades dedicadas de taxa de transferência provisionada. 

2. Você pode aplicar as estratégias acima para surgir com uma configuração híbrida, em que você tem a taxa de transferência provisionada no nível do banco de dados com alguns contêineres com taxa de transferência dedicada.

Conforme mostrado na tabela a seguir, dependendo da escolha da API, você pode provisionar a taxa de transferência em diferentes granularidades.

|API|Para a taxa de transferência **compartilhada** , configure |Para taxa de transferência **dedicada** , configure |
|----|----|----|
|SQL API|Base de Dados|Contentor|
|API do Azure Cosmos DB para MongoDB|Base de Dados|Collection|
|API de Cassandra|Espaço de chaves|Tabela|
|API do Gremlin|Conta de base de dados|Graph|
|API de Tabela|Conta de base de dados|Tabela|

Ao provisionar a taxa de transferência em diferentes níveis, você pode otimizar seus custos com base nas características da sua carga de trabalho. Conforme mencionado anteriormente, você pode programaticamente e a qualquer momento aumentar ou diminuir sua taxa de transferência provisionada para contêineres individuais ou coletivamente em um conjunto de contêineres. Ao dimensionar de forma elástica a taxa de transferência conforme a carga de trabalho é alterada, você paga apenas pela taxa de transferência que configurou. Se o contêiner ou um conjunto de contêineres for distribuído entre várias regiões, a taxa de transferência configurada no contêiner ou em um conjunto de contêineres será garantida para ser disponibilizada em todas as regiões.

## <a name="optimize-with-rate-limiting-your-requests"></a>Otimizar com limitação de taxa de suas solicitações

Para cargas de trabalho que não são sensíveis à latência, você pode provisionar menos produtividade e permitir que o aplicativo manipule a limitação de taxa quando a taxa de transferência real excede a taxa de transferência provisionada. O servidor encerrará de forma preventiva a solicitação `RequestRateTooLarge` com (código de status http 429) e `x-ms-retry-after-ms` retornará o cabeçalho indicando o tempo, em milissegundos, que o usuário deve aguardar antes de repetir a solicitação. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Lógica de repetição em SDKs 

Os SDKs nativos (.NET/.NET Core, Java, Node. js e Python) capturam implicitamente essa resposta, respeitam o cabeçalho Retry-After especificado pelo servidor e tentam a solicitação novamente. A menos que sua conta seja acessada simultaneamente por vários clientes, a próxima tentativa terá sucesso.

Se você tiver mais de um cliente operando de forma cumulativa consistentemente acima da taxa de solicitação, a contagem de repetição padrão atualmente definida como 9 pode não ser suficiente. Nesse caso, o cliente gera um `DocumentClientException` com código de status 429 para o aplicativo. A contagem de repetição padrão pode ser alterada definindo o `RetryOptions` na instância ConnectionPolicy. Por padrão, o `DocumentClientException` com código de status 429 é retornado após um tempo de espera cumulativo de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Isso ocorre mesmo quando a contagem de repetições atual é menor que a contagem máxima de tentativas, seja o padrão de 9 ou um valor definido pelo usuário. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) é definido como 3, portanto, nesse caso, se uma operação de solicitação tiver uma taxa limitada excedendo a taxa de transferência reservada para o contêiner, a operação de solicitação tentará novamente três vezes antes de lançar a exceção para o aplicativo. [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) é definido como 60, portanto, nesse caso, se o tempo de espera de repetição cumulativo em segundos desde a primeira solicitação exceder 60 segundos, a exceção será lançada.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Estratégia de particionamento e custos de taxa de transferência provisionados

Boa estratégia de particionamento é importante para otimizar os custos em Azure Cosmos DB. Verifique se não há nenhuma distorção de partições, que são expostas por meio de métricas de armazenamento. Verifique se não há nenhuma distorção de taxa de transferência para uma partição, que é exposta com métricas de taxa de transferência. Verifique se não há distorção em direção a chaves de partição específicas. As chaves dominantes no armazenamento são expostas por meio de métricas, mas a chave será dependente do padrão de acesso do aplicativo. É melhor pensar na chave de partição lógica correta. Espera-se que uma boa chave de partição tenha as seguintes características:

* Escolha uma chave de partição que espalha a carga de trabalho uniformemente em todas as partições e uniformemente ao longo do tempo. Em outras palavras, você não deve ter algumas chaves para a maioria dos dados e algumas chaves com menos ou nenhum dado. 

* Escolha uma chave de partição que permita que os padrões de acesso sejam distribuídos uniformemente entre partições lógicas. A carga de trabalho é razoavelmente uniforme em todas as chaves. Em outras palavras, a maior parte da carga de trabalho não deve ser focada em algumas chaves específicas. 

* Escolha uma chave de partição que tenha uma ampla gama de valores. 

A ideia básica é distribuir os dados e a atividade em seu contêiner pelo conjunto de partições lógicas, para que os recursos de armazenamento e taxa de transferência de dados possam ser distribuídos entre as partições lógicas. Os candidatos às chaves de partição podem incluir as propriedades que aparecem com frequência como um filtro em suas consultas. As consultas podem ser roteadas com eficiência, incluindo a chave de partição no predicado de filtro. Com essa estratégia de particionamento, otimizar a taxa de transferência provisionada será muito mais fácil. 

### <a name="design-smaller-items-for-higher-throughput"></a>Criar itens menores para maior taxa de transferência 

O encargo da solicitação ou o custo de processamento da solicitação de uma determinada operação está correlacionado diretamente com o tamanho do item. As operações em itens grandes custarão mais do que as operações em itens menores. 

## <a name="data-access-patterns"></a>Padrões de acesso a dados 

É sempre uma boa prática separar logicamente os dados em categorias lógicas com base na frequência com que você acessa os dados. Categorizando-o como dados quentes, médios ou frios, você pode ajustar o armazenamento consumido e a taxa de transferência necessária. Dependendo da frequência de acesso, você pode colocá-los em contêineres separados (por exemplo, tabelas, gráficos e coleções) e ajustar a taxa de transferência provisionada neles para acomodar as necessidades desse segmento de dados. 

Além disso, se você estiver usando Azure Cosmos DB e souber que não vai Pesquisar por determinados valores de dados ou raramente os acessará, armazene os valores compactados desses atributos. Com esse método, você economiza espaço de armazenamento, espaço de índice e taxa de transferência provisionada e resulta em custos mais baixos.

## <a name="optimize-by-changing-indexing-policy"></a>Otimizar alterando a política de indexação 

Por padrão, Azure Cosmos DB indexa automaticamente cada propriedade de cada registro. Isso destina-se a facilitar o desenvolvimento e garantir um desempenho excelente entre vários tipos diferentes de consultas ad hoc. Se você tiver grandes registros com milhares de propriedades, pagar o custo da taxa de transferência para indexação de todas as propriedades poderá não ser útil, especialmente se você consultar apenas 10 ou 20 dessas propriedades. À medida que você se aproximar da obtenção de um identificador em sua carga de trabalho específica, nossa orientação é ajustar sua política de índice. Detalhes completos sobre a política de indexação de Azure Cosmos DB podem ser encontrados [aqui](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitorando a produtividade provisionada e consumida 

Você pode monitorar o número total de RUs provisionadas, o número de solicitações limitadas por taxa, bem como o número de RUs que você consumiu na portal do Azure. A imagem a seguir mostra uma métrica de uso de exemplo:

![Monitorar unidades de solicitação no portal do Azure](./media/optimize-cost-throughput/monitoring.png)

Você também pode definir alertas para verificar se o número de solicitações limitadas por taxa excede um limite específico. Consulte [como monitorar Azure Cosmos DB](use-metrics.md) artigo para obter mais detalhes. Esses alertas podem enviar um email aos administradores da conta ou chamar um webhook HTTP personalizado ou uma função do Azure para aumentar automaticamente a taxa de transferência provisionada. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Dimensione sua taxa de transferência de forma elástica e sob demanda 

Como você é cobrado pela taxa de transferência provisionada, corresponder à taxa de transferência provisionada às suas necessidades pode ajudá-lo a evitar os encargos para a taxa de transferência não utilizada. Você pode dimensionar sua taxa de transferência provisionada para cima ou para baixo a qualquer momento, conforme necessário.  

* Monitorar o consumo de seu RUs e a taxa de solicitações limitadas por taxa pode revelar que não é necessário manter o provisionamento em toda a constante ao longo do dia ou da semana. Você pode receber menos tráfego à noite ou durante o fim de semana. Usando portal do Azure ou Azure Cosmos DB SDKs nativos ou a API REST, você pode dimensionar sua taxa de transferência provisionada a qualquer momento. A API REST de Azure Cosmos DB fornece pontos de extremidade para atualizar programaticamente o nível de desempenho de seus contêineres, tornando-o simples de ajustar a taxa de transferência do seu código, dependendo da hora do dia ou do dia da semana. A operação é executada sem nenhum tempo de inatividade e normalmente entra em vigor em menos de um minuto. 

* Uma das áreas que você deve dimensionar a taxa de transferência é quando você está ingerindo dados em Azure Cosmos DB, por exemplo, durante a migração de dados. Depois de concluir a migração, você pode dimensionar a taxa de transferência provisionada para lidar com o estado estável da solução.  

* Lembre-se de que a cobrança está na granularidade de uma hora, portanto, você não economizará nenhum dinheiro se alterar sua taxa de transferência provisionada com mais frequência de uma hora por vez.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Determinar a taxa de transferência necessária para uma nova carga de trabalho 

Para determinar a taxa de transferência provisionada para uma nova carga de trabalho, você pode usar as seguintes etapas: 

1. Execute uma avaliação inicial e aproximada usando o planejador de capacidade e ajuste suas estimativas com a ajuda do Azure Cosmos Explorer no portal do Azure. 

2. É recomendável criar os contêineres com maior taxa de transferência do que o esperado e, em seguida, reduzir verticalmente conforme necessário. 

3. É recomendável usar um dos SDKs de Azure Cosmos DB nativos para se beneficiar de repetições automáticas quando as solicitações obtiverem a taxa limitada. Se você estiver trabalhando em uma plataforma sem suporte e usar a API REST de Cosmos DB, implemente sua própria política de repetição usando `x-ms-retry-after-ms` o cabeçalho. 

4. Certifique-se de que o código do aplicativo é normalmente compatível com o caso em que todas as tentativas falham. 

5. Você pode configurar alertas do portal do Azure para obter notificações de limitação de taxa. Você pode começar com limites conservadores como 10 solicitações limitadas de taxa nos últimos 15 minutos e mudar para mais regras adiantadas depois de descobrir seu consumo real. Os limites de taxa ocasionais são bons, eles mostram que você está jogando com os limites que você definiu e que é exatamente o que você deseja fazer. 

6. Use o monitoramento para entender seu padrão de tráfego, para que você possa considerar a necessidade de ajustar dinamicamente seu provisionamento de taxa de transferência durante o dia ou uma semana. 

7. Monitore sua taxa de produtividade provisionada e consumida regularmente para certificar-se de que você não provisionou mais do que o número necessário de contêineres e bancos de dados. Ter um pouco sobre a taxa de transferência provisionada é uma boa verificação de segurança.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Práticas recomendadas para otimizar a taxa de transferência provisionada 

As etapas a seguir ajudam você a tornar suas soluções altamente escalonáveis e econômicas ao usar o Azure Cosmos DB.  

1. Se você tiver uma taxa de transferência provisionada significativamente em contêineres e bancos de dados, você deverá examinar RUS provisionadas vs consumidas e ajustar as cargas de trabalho.  

2. Um método para estimar a quantidade de taxa de transferência reservada exigida pelo seu aplicativo é registrar a carga de RU da unidade de solicitação associada à execução de operações típicas em um contêiner ou banco de dados do Azure Cosmos representativo usado pelo seu aplicativo e em seguida, estime o número de operações que você prevê para executar a cada segundo. Certifique-se de medir e incluir consultas típicas e seu uso também. Para saber como estimar os custos de RU de consultas de forma programática ou usando o portal, consulte [otimizando o custo de consultas](online-backup-and-restore.md). 

3. Outra maneira de obter operações e seus custos no RUs é habilitar os logs de Azure Monitor, o que lhe dará a divisão de operação/duração e o encargo da solicitação. Azure Cosmos DB fornece a cobrança de solicitação para cada operação, de modo que cada encargo de operação pode ser armazenado de volta da resposta e, em seguida, usado para análise. 

4. É possível escalar e reduzir de forma elástica a taxa de transferência provisionada conforme necessário para acomodar suas necessidades de carga de trabalho. 

5. Você pode adicionar e remover regiões associadas à sua conta do Azure Cosmos conforme necessário e controlar os custos. 

6. Verifique se você tem até mesmo a distribuição de dados e cargas de trabalho em partições lógicas de seus contêineres. Se você tiver uma distribuição de partição desigual, isso poderá fazer com que o Provisione uma maior quantidade de taxa de transferência do que o valor necessário. Se você identificar que tem uma distribuição distorcida, é recomendável redistribuir a carga de trabalho uniformemente entre as partições ou reparticionar os dados. 

7. Se você tiver muitos contêineres e esses contêineres não exigirem SLAs, você poderá usar a oferta baseada em banco de dados para os casos em que os SLAs de taxa de transferência por contêiner não se aplicam. Você deve identificar quais dos contêineres de Cosmos do Azure você deseja migrar para a oferta de taxa de transferência no nível do banco de dados e migrá-los usando uma solução baseada em feed de alteração. 

8. Considere usar a "Cosmos DB camada gratuita" (gratuita por um ano), experimente Cosmos DB (até três regiões) ou o emulador de Cosmos DB baixável para cenários de desenvolvimento/teste. Ao usar essas opções para desenvolvimento de teste, você pode reduzir consideravelmente os custos.  

9. Você pode executar ainda mais otimizações de custo específicas de carga de trabalho – por exemplo, aumentar o tamanho do lote, leituras de balanceamento de carga em várias regiões e eliminação de duplicação de dados, se aplicável.

10. Com Azure Cosmos DB capacidade reservada, você pode obter descontos significativos por até 65% por três anos. Azure Cosmos DB modelo de capacidade reservada é um compromisso antecipado em unidades de solicitações necessárias ao longo do tempo. Os descontos são em camadas, de forma que quanto mais unidades de solicitação você usar em um período mais longo, mais seu desconto será. Esses descontos são aplicados imediatamente. Qualquer RUs usado acima de seus valores provisionados é cobrado com base no custo de capacidade não reservada. Consulte [Cosmos DB capacidade reservada](cosmos-db-reserved-capacity.md)) para obter mais detalhes. Considere a possibilidade de comprar capacidade reservada para reduzir ainda mais os custos de taxa de transferência provisionados.  

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode prosseguir para saber mais sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre como [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre como [entender sua fatura de Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre como [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre como [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre como [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre como [otimizar o custo de contas do Azure Cosmos de várias regiões](optimize-cost-regions.md)


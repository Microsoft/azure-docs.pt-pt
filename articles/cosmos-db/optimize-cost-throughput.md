---
title: Otimizar o custo de débito no Azure Cosmos DB
description: Este artigo explica como otimizar os custos de débito para os dados armazenados no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: e4d4d15ebb8200f16be8953e955b2e793be03c3a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452195"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Otimizar o débito aprovisionado de custo no Azure Cosmos DB

Ao fornecer o modelo de débito aprovisionado, o Azure Cosmos DB oferece um desempenho previsível em qualquer escala. Reservar ou débito antes do tempo de aprovisionamento elimina o "efeito de vizinhos ruidosos" no seu desempenho. Especifique a quantidade exata de débito de que precisa e Azure Cosmos DB garante o débito configurado, apoiado por um SLA.

Pode começar com um débito mínimo de 400 RU/seg e dimensionar até dezenas de milhões de pedidos por segundo ou ainda mais. Cada solicitação, emitir contra o seu contentor do Cosmos do Azure ou a base de dados, como uma solicitação de leitura, a solicitação de gravação, a pedido de consulta, procedimentos armazenados têm um custo correspondente, que é deduzido da débito aprovisionado. Se aprovisionar 400 RU/s e emite uma consulta que custa 40 RUs, será capaz de emitir 10 essas consultas por segundo. Qualquer pedido além do que irá obter a taxa limitado e deverá repetir o pedido. Se estiver a utilizar controladores de cliente, eles oferecem suporte a lógica de repetição automática.

Pode aprovisionar o débito em bases de dados ou contentores e cada estratégia podem ajudar a poupar nos custos, dependendo do cenário.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Otimizar o débito em diferentes níveis de aprovisionamento

* Se aprovisionar o débito, numa base de dados, todos os contentores, por exemplo coleções/tabelas/gráficos nesse banco de dados pode partilhar o débito com base na carga. Débito reservado ao nível da base de dados é compartilhado de forma desigual, dependendo da carga de trabalho num conjunto específico de contentores.

* Se aprovisionar o débito num contentor, o débito é garantido para esse contentor, apoiado pelo SLA. A escolha de uma chave de partição lógica é crucial para igualar distribuição de carga em todas as partições de um contentor lógicas. Ver [Partitioning](partitioning-overview.md) e [dimensionamento horizontal](partition-data.md) artigos para obter mais detalhes.

Seguem-se algumas diretrizes para decidir qual uma estratégia de débito aprovisionado:

**Considere o aprovisionamento de débito, numa base de dados do Azure Cosmos DB (que contém um conjunto de contentores) se**:

1. Tem alguns contentores do Azure Cosmos doze e pretende partilhar a taxa de transferência entre alguns ou todos eles. 

2. Está a migrar de uma base de dados de inquilino único, concebido para ser executado em VMs de IaaS alojado ou no local, por exemplo, NoSQL ou bases de dados relacionais para o Azure Cosmos DB. Anf se tiver muitas coleções/tabelas/gráficos e não quero fazer quaisquer alterações ao seu modelo de dados. Tenha em atenção de que poderá ter de comprometer alguns dos benefícios oferecidos pelo Azure Cosmos DB, se não estiver a atualizar o modelo de dados ao migrar de uma base de dados no local. Recomenda-se que sempre acessar novamente o modelo de dados para tirar o máximo em termos de desempenho e também para otimizar os custos. 

3. Quer absorver picos não planeados de cargas de trabalho devido à taxa de transferência agrupada ao nível da base de dados sujeito a pico inesperado na carga de trabalho. 

4. Em vez de débito de definição específico em contentores individuais, que mais lhe interessa a obter o débito agregado num conjunto de contentores na base de dados.

**Considere o aprovisionamento de débito num contentor individual se:**

1. Tem alguns contentores do Azure Cosmos. Como o Azure Cosmos DB é independente de esquema, um contentor pode conter itens que têm esquemas heterogêneas e requer que possam criar vários tipos de contentor, um para cada entidade. É sempre uma opção a considerar se disser que separado de agrupamento contentores de 10 a 20 num único contentor faz sentido. Com um 400 RUs mínimo para contentores, agrupamento de todos os contentores de 10 a 20 em um possível custo-eficiência. 

2. Pretende controlar o débito num contentor específico e obter a taxa de transferência garantida num contentor especificado apoiado pelo SLA.

**Considere um híbrido das duas estratégias acima:**

1. Como mencionado anteriormente, o Azure Cosmos DB permite que misturar e combinar duas estratégias acima, agora pode ter alguns contentores dentro do banco de dados do Cosmos do Azure, que pode partilhar o débito aprovisionado no banco de dados, bem como, alguns contentores no mesmo banco de dados , que pode ter dedicados quantidades de débito aprovisionado. 

2. É possível aplicar as estratégias acima propor configuração híbrida, em que tem o nível débito aprovisionado ambas as bases de dados com alguns contentores dedicada de débito.

Conforme mostrado na tabela a seguir, consoante a opção da API, pode aprovisionar o débito em granularidades diferentes.

|API|Para **partilhado** débito, configurar |Para **dedicado** débito, configurar |
|----|----|----|
|SQL API|Base de Dados|Contentor|
|API do Azure Cosmos DB para MongoDB|Base de Dados|Coleção|
|API de Cassandra|Espaço de chaves|Tabela|
|API do Gremlin|Conta de base de dados|Graph|
|API de Tabela|Conta de base de dados|Tabela|

Ao débito de aprovisionamento em níveis diferentes, é possível otimizar os custos com base nas características da carga de trabalho. Como mencionado anteriormente, pode por meio de programação e em qualquer aumento do tempo ou diminuir o débito aprovisionado para qualquer um dos contentores individuais ou coletivamente num conjunto de contentores. Ao dimensionar elasticamente o débito como as alterações de carga de trabalho, paga apenas o débito que configurou. Se o seu contentor ou um conjunto de contentores é distribuído por várias regiões, em seguida, o débito configura no contentor ou um conjunto de contentores é garantido que estejam disponíveis em todas as regiões.

## <a name="optimize-with-rate-limiting-your-requests"></a>Otimize com os pedidos de limitação de velocidade

Para cargas de trabalho que não são sensíveis à latência, pode aprovisionar o menos débito e permitir que o aplicativo processe a limitação de velocidade quando o débito real excede o débito aprovisionado. O servidor preventivamente irá terminar o pedido com RequestRateTooLarge (código de estado HTTP 429) e retornar o `x-ms-retry-after-ms` cabeçalho que indica a quantidade de tempo, em milissegundos, que o utilizador tem de aguardar antes de repetir o pedido. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Lógica de repetição no SDKs 

Os SDKs nativos (.NET/.NET Core, Java, node. js e Python) implicitamente capturar essa resposta, respeitem o servidor especificado cabeçalho retry-after e repetir o pedido. A menos que a sua conta é acessada em simultâneo por vários clientes, a próxima repetição será concluída com êxito.

Se tiver mais do que um cliente cumulativamente e a funcionar consistentemente acima a taxa de pedidos, a predefinição contagem de repetições atualmente que está definida para 9 pode não ser suficiente. Nesse caso, o cliente gera um `DocumentClientException` com o estado de código 429 à aplicação. A contagem de repetições padrão pode ser alterada ao definir o `RetryOptions` na instância ConnectionPolicy. Por predefinição, o DocumentClientException com código de estado 429 é devolvido após um tempo cumulativo de espera de 30 segundos se o pedido continuar a operar acima a taxa de pedidos. Isto ocorre mesmo quando o número atual de tentativas é menor que o número máximo de tentativas, seja ele o padrão de 9 ou de um valor definido pelo utilizador. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) está definido para 3, então, neste caso, se uma operação de pedido tem velocidade limitada por ter excedido o débito reservado para a coleção, a operação de pedido tentará novamente três vezes antes que ocorra a exceção ao aplicativo.  [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) está definida como 60, então, nesse caso se a repetição cumulativa aguarda o tempo em segundos desde o primeiro pedido excede 60 segundos, a exceção é lançada.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 

connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 

connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Estratégia de particionamento e os custos de débito aprovisionado

Boa estratégia de criação de partições é importante para otimizar os custos no Azure Cosmos DB. Certifique-se de que existe não distorção de partições, que são expostas por meio de métricas de armazenamento. Certifique-se de que existe não distorção de débito para uma partição, que é exposta com métricas de débito. Certifique-se de que existe não distorção para chaves de partição específica. Chaves dominantes no armazenamento são expostas por meio de métricas, mas a chave será dependente de seu padrão de acesso da aplicação. É ideal pensar sobre a chave de partição lógica à direita. Espera-se que uma chave de partição boa têm as seguintes características:

* Escolha uma chave de partição que propaga uniformemente a carga de trabalho de todas as partições e de forma uniforme ao longo do tempo. Em outras palavras, não deve ter algumas chaves para com a maioria dos dados e algumas chaves com dados menos ou não. 

* Escolha uma chave de partição que permite aceder aos padrões para ser distribuídos uniformemente por partições lógicas. A carga de trabalho é razoavelmente mesmo entre todas as chaves. Em outras palavras, a maioria da carga de trabalho não deve ser focada em algumas chaves específicas. 

* Escolha uma chave de partição que tenha uma vasta gama de valores. 

A idéia básica é distribuir os dados e a atividade no seu contentor entre o conjunto de partições lógicas, para que os recursos de armazenamento e débito de dados podem ser distribuídos por partições lógicas. Candidatos para as chaves de partição podem incluir as propriedades que são apresentados frequentemente como um filtro em suas consultas. Consultas podem ser encaminhadas com eficiência, incluindo a chave de partição no predicado de filtro. Com esse uma estratégia de particionamento, otimizar o débito aprovisionado será muito mais fácil. 

### <a name="design-smaller-items-for-higher-throughput"></a>Itens de menores de design para um débito mais elevado 

O custo de pedido ou o custo de processamento de solicitação de uma determinada operação diretamente é correlacionado com o tamanho do item. Operações nos itens grandes custará mais do que operações nos itens mais pequenos. 

## <a name="data-access-patterns"></a>Padrões de acesso de dados 

É sempre uma boa prática separar logicamente os dados em categorias lógicas com base na frequência com a qual acessar os dados. Categorizando-lo como dados de acesso frequente, médios ou frios pode ajustar o armazenamento consumido e débito necessário. Dependendo da frequência de acesso, pode colocar os dados em separado contentores (por exemplo, tabelas, gráficos e coleções) e otimizar o débito aprovisionado neles para acomodar as necessidades do segmento de dados. 

Além disso, se estiver a utilizar o Azure Cosmos DB, e sabe, não vai procurar por certos valores de dados ou raramente será acessá-los, deve armazenar os valores comprimidos desses atributos. Com esse método guardar no espaço de armazenamento, o espaço de índice e o débito aprovisionado e resultar em custos mais baixos.

## <a name="optimize-by-changing-indexing-policy"></a>Otimizar ao alterar a política de indexação 

Por predefinição, o Azure Cosmos DB indexa automaticamente todas as propriedades de cada registo. Isso se destina a facilitar o desenvolvimento e certifique-se excelente desempenho em muitos tipos diferentes de consultas ad hoc. Se tiver registos grandes com milhares de propriedades, prestar o custo de débito para todas as propriedades de indexação pode não ser útil, especialmente se apenas uma consulta em relação a 10 ou 20 dessas propriedades. À medida que se aproximar-se para obter um identificador de sua carga de trabalho específica, a nossa documentação de orientação é otimizar a sua política de índice. Todos os detalhes sobre a política de indexação do Azure Cosmos DB podem ser encontrados [aqui](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitorização aprovisionado e consumidos débito 

Pode monitorizar o número total de RUs aprovisionadas, o número de pedidos de taxa limitado, bem como o número de RUs consumiu no portal do Azure. A imagem seguinte mostra uma métrica de utilização de exemplo:

![Monitor de unidades de pedido no portal do Azure](./media/optimize-cost-throughput/monitoring.png)

Também pode definir alertas para verificar se o número de pedidos de taxa limitado excede um limiar específico. Ver [como monitorizar o Azure Cosmos DB](use-metrics.md) artigo para obter mais detalhes. Estes alertas, podem enviar um e-mail para os administradores de conta ou chamar um Webhook de HTTP personalizado ou uma função do Azure para aumentar automaticamente o débito aprovisionado. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Dimensionar o débito de forma elástica e a pedido 

Uma vez que é faturado pelo débito aprovisionado, o débito aprovisionado às suas necessidades de correspondência pode ajudar a evitar os custos para o débito não utilizado. Pode dimensionar o débito aprovisionado ou reduzir verticalmente a qualquer momento, conforme necessário.  

* Monitorizar o consumo de sua RUs e a taxa de pedidos de taxa limitado pode revelar que não é necessário manter aprovisionado em toda a constante em todo o dia ou semana. Poderá receber menos tráfego durante a noite ou durante o fim de semana. Ao utilizar o portal do Azure ou SDKs nativos do Azure Cosmos DB ou REST API, pode dimensionar o débito aprovisionado em qualquer altura. API de REST do Azure Cosmos DB fornece os pontos de extremidade por meio de programação atualizar o nível de desempenho de seus contentores torna simples ajustar a taxa de transferência a partir do código consoante a hora do dia ou dia da semana. A operação é executada sem qualquer período de inatividade e normalmente tem efeito em menos de um minuto. 

* Uma das áreas deve aumentar o débito é quando ingerir dados no Azure Cosmos DB, por exemplo, durante a migração de dados. Depois de concluir a migração, pode dimensionar o débito aprovisionado para baixo para processar o estado de repouso da solução.  

* Lembre-se de que a faturação é com a granularidade de uma hora, para que isso não economize dinheiro se alterar o débito aprovisionado com mais frequência do que uma hora cada vez.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Determinar o débito necessário para novas cargas de trabalho 

Para determinar o débito aprovisionado para novas cargas de trabalho, pode utilizar os seguintes passos: 

1. Realizar uma avaliação inicial, aproximada, usando o planeador de capacidade e ajuste suas estimativas com a ajuda do Explorador do Cosmos do Azure no portal do Azure. 

2. É recomendado para criar os contentores com um débito mais elevado do que o esperado e, em seguida, reduzir verticalmente, conforme necessário. 

3. É recomendado utilizar uma das nativo SDKs do Azure Cosmos DB para beneficiar de tentativas automáticas, quando limitado de taxa de pedidos. Se estiver trabalhando numa plataforma que não é suportada e utiliza a API de REST do Cosmos DB, implementar sua própria política de repetição usando o `x-ms-retry-after-ms` cabeçalho. 

4. Certifique-se de que o código da aplicação suporta normalmente o caso quando falham todas as tentativas. 

5. Pode configurar alertas do portal do Azure para obter notificações para a limitação de velocidade. Pode começar com limites conservadoras, como 10 limitado de taxa de pedidos durante os últimos 15 minutos e comutador regras mais ansiosos depois que o consumo real. Limites de velocidade ocasionais são muito bem, mostram que estiver brincando com os limites que definiu e é exatamente o que deseja fazer. 

6. Utilizar a monitorização para compreender o seu padrão de tráfego, pelo que pode considerar a necessidade de ajuste dinamicamente o aprovisionamento de débito ao longo do dia ou de uma semana. 

7. Monitorize a sua aprovisionado vs. a taxa de débito consumida regularmente para se certificar de que não aprovisionado mais do que o número necessário de contentores e bases de dados. Ter um pouco sobre o débito aprovisionado é uma verificação de segurança de bom.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Melhores práticas para otimizar o débito aprovisionado 

Os seguintes passos ajudá-lo a tornar as suas soluções altamente escalável e acessível ao utilizar o Azure Cosmos DB.  

1. Se tiver significativamente ao longo do débito aprovisionado em contentores e bases de dados, deve rever o RUs Vs consumidas RUs aprovisionadas e ajustar as cargas de trabalho.  

2. Um método para estimar a quantidade de débito reservado exigida pela sua aplicação é registrar o custo da unidade RU de pedido associado à execução de operações típicas em relação a um contentor do Cosmos do Azure ou da base de dados utilizado pela sua aplicação representativos e em seguida, estime o número de operações que prevê para executar a cada segundo. Certifique-se de que medir e incluem consultas típicas e também seu uso. Para saber como estimar os custos de RU de consultas através de programação ou através de ver portal [otimizar o custo de consultas](online-backup-and-restore.md). 

3. Outra forma de obter operações e os custos no RUs é, permitindo que os registos do Azure Monitor, que lhe dará a divisão de operação/duração e o custo de pedido. O Azure Cosmos DB fornece encargos de pedidos para cada operação, para que todos os custos de operação podem ser armazenados volta da resposta e, em seguida, utilizado para análise. 

4. Pode dimensionar verticalmente o débito aprovisionado, precisa acomodar suas necessidades de carga de trabalho. 

5. Pode adicionar e remover regiões à sua conta do Cosmos do Azure à medida que precisa e controla os custos. 

6. Certificar-se de que tem uma distribuição uniforme de cargas de trabalho e dados em partições lógicas de seus contentores. Se tiver uma distribuição desigual de partição, isso pode causar para Aprovisionar uma quantidade maior de débito que o valor que é necessária. Se identificar que tenha uma distribuição distorcida, é recomendável redistribuir a carga de trabalho uniformemente pelas partições ou criar novas partições de dados. 

7. Se tiver o número de contentores e estes contentores não necessitam de SLAs, pode utilizar a oferta com base na base de dados para os casos em que o débito de contentores por SLAs não se aplicam. Deve identificar quais os contentores do Cosmos do Azure que pretende migrar para o débito de nível de base de dados oferecem e, em seguida, migração-los através de uma solução baseada no feed de alterações. 

8. Considere utilizar o "Cosmos DB escalão gratuito" (gratuito durante um ano), do Cosmos DB (até três regiões) ou o emulador do Cosmos DB que pode ser baixado para cenários de desenvolvimento/teste. Ao utilizar estas opções para desenvolvimento de teste, pode reduzir consideravelmente os custos.  

9. Pode efetuar ainda mais carga de trabalho específica, otimizar custos – por exemplo, aumentando a leituras de tamanho de lote, o balanceamento de carga em várias regiões e anular a duplicação de dados, se aplicável.

10. Com capacidade de reservada do Azure Cosmos DB, pode obter descontos significativos para até 65% para três anos. Modelo de capacidade de reserva do Azure Cosmos DB é um compromisso inicial no necessário ao longo do tempo de unidades de pedido. Os descontos são dispostos em camadas de forma a que mais unidades de pedido que utilizar por um período mais longo, mais seu desconto será. Estes descontos são aplicados de imediato. O custo de capacidade não reservados são cobradas qualquer RUs utilizados acima seus valores aprovisionados. Ver [capacidade de reserva do Cosmos DB](cosmos-db-reserved-capacity.md)) para obter mais detalhes. Considere a aquisição de capacidade de reserva para reduzir os custos de débito aprovisionado.  

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode avançar para obter mais informações sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [entender a sua fatura do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo de contas do Cosmos do Azure de várias regiões](optimize-cost-regions.md)


---
title: Otimização do custo de produção em Azure Cosmos DB
description: Este artigo explica como otimizar os custos de produção dos dados armazenados na Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c95fc9e416399b5c8fe032e0d3af0c3b7f9cf6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433578"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Otimizar o débito aprovisionado no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ao oferecer um modelo de produção a provisionado, a Azure Cosmos DB oferece um desempenho previsível em qualquer escala. Reservar ou aussentando a produção antes do tempo elimina o "efeito de vizinho barulhento" no seu desempenho. Você especifica a quantidade exata de produção que você precisa e Azure Cosmos DB garante a produção configurada, apoiada pela SLA.

Pode começar com uma produção mínima de 400 RU/seg e escalar até dezenas de milhões de pedidos por segundo ou até mais. Cada pedido que emite contra o seu contentor ou base de dados Azure Cosmos, como um pedido de leitura, pedido de escrita, pedido de consulta, procedimentos armazenados têm um custo correspondente que é deduzido do seu rendimento provisitado. Se você providenciar 400 RU/s e emitir uma consulta que custa 40 RUs, você será capaz de emitir 10 tais consultas por segundo. Qualquer pedido para além disso será limitado pela taxa e deverá voltar a julgar o pedido. Se estiver a utilizar motoristas de clientes, eles suportam a lógica de repetição automática.

Pode aprovisionar o débito em bases de dados ou contentores e cada estratégia pode ajudá-lo a poupar nos custos, consoante o cenário.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Otimizar através do fornecimento de produção a diferentes níveis

* Se forre o rendimento numa base de dados, todos os contentores, por exemplo, recolhas/tabelas/gráficos dentro dessa base de dados podem partilhar o rendimento com base na carga. A produção reservada ao nível da base de dados é partilhada de forma desigual, dependendo da carga de trabalho num conjunto específico de contentores.

* Se forrou a produção num contentor, a produção é garantida para esse contentor, apoiado pelo SLA. A escolha de uma chave de partição lógica é crucial para a distribuição uniforme da carga em todas as divisórias lógicas de um recipiente. Consulte os artigos [de partição](partitioning-overview.md) e [de escala horizontal](partitioning-overview.md) para obter mais detalhes.

Seguem-se algumas orientações para decidir sobre uma estratégia de produção prevista:

**Considere o fornecimento de produção numa base de dados Azure Cosmos (contendo um conjunto de contentores) se:**

1. Você tem algumas dúzias de contentores Azure Cosmos e quer partilhar a produção através de alguns ou todos eles. 

2. Você está migrando de uma base de dados de inquilino único projetado para funcionar em VMs hospedados em IaaS ou no local, por exemplo, NoSQL ou bases de dados relacionais para Azure Cosmos DB. E se tiver muitas coleções/tabelas/gráficos e não quiser fazer alterações no seu modelo de dados. Note que poderá ter de comprometer alguns dos benefícios oferecidos pela Azure Cosmos DB se não estiver a atualizar o seu modelo de dados ao migrar de uma base de dados no local. Recomenda-se que reaculte sempre o seu modelo de dados para obter o máximo em termos de desempenho e também para otimizar os custos. 

3. Pretende absorver picos não planeados em cargas de trabalho em virtude da produção agamada no nível da base de dados sujeita a um aumento inesperado na carga de trabalho. 

4. Em vez de definir a produção específica em recipientes individuais, preocupa-se em obter a produção agregada através de um conjunto de contentores dentro da base de dados.

**Considere o fornecimento de rendimento num recipiente individual se:**

1. Tem alguns contentores Azure Cosmos. Como a Azure Cosmos DB é schema-agnóstica, um recipiente pode conter itens que têm esquemas heterogéneos e não exige que os clientes criem vários tipos de recipientes, um para cada entidade. É sempre uma opção a considerar se agrupar recipientes separados digamos 10-20 em um único recipiente faz sentido. Com um mínimo de 400 RUs para contentores, juntar todos os contentores de 10 a 20 em um poderia ser mais rentável. 

2. Você quer controlar a produção de um recipiente específico e obter a produção garantida em um dado recipiente apoiado pela SLA.

**Considere um híbrido das duas estratégias acima:**

1. Como mencionado anteriormente, a Azure Cosmos DB permite-lhe misturar e combinar as duas estratégias acima, para que agora possa ter alguns contentores dentro da base de dados Azure Cosmos, que podem partilhar o rendimento previsto na base de dados, bem como alguns contentores dentro da mesma base de dados, que podem ter quantidades dedicadas de produção a forerada. 

2. Pode aplicar as estratégias acima para criar uma configuração híbrida, onde tem ambos os níveis de base de dados auser a produção com alguns contentores com produção dedicada.

Como mostrado na tabela seguinte, dependendo da escolha da API, você pode provisão de produção em diferentes granularidades.

|API|Para **produção partilhada,** configurar |Para **produção dedicada,** configurar |
|----|----|----|
|API SQL|Base de Dados|Contentor|
|API do Azure Cosmos DB para MongoDB|Base de Dados|Coleção|
|API de Cassandra|Espaço-chave|Tabela|
|API do Gremlin|Conta de base de dados|Graph|
|API de Tabela|Conta de base de dados|Tabela|

Ao auspor produção a diferentes níveis, pode otimizar os seus custos com base nas características da sua carga de trabalho. Como mencionado anteriormente, pode aumentar programática e a qualquer momento o seu rendimento proviscionado para cada um dos contentores ou coletivamente através de um conjunto de contentores. Ao escalonar elasticamente a produção à medida que a sua carga de trabalho muda, só paga pela produção que configura. Se o seu recipiente ou um conjunto de contentores forem distribuídos por várias regiões, então a produção que configura no contentor ou um conjunto de contentores é garantidamente disponibilizada em todas as regiões.

## <a name="optimize-with-rate-limiting-your-requests"></a>Otimizar os pedidos com a limitação da taxa

Para cargas de trabalho que não sejam sensíveis à latência, pode prever menos produção e deixar a aplicação limitar a taxa quando a produção real exceder a produção prevista. O servidor terminará preventivamente o pedido com `RequestRateTooLarge` (código de estado HTTP 429) e devolverá o `x-ms-retry-after-ms` cabeçalho indicando o tempo, em milissegundos, de que o utilizador deve esperar antes de voltar a tentar o pedido. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Redatória lógica em SDKs 

Os SDKs nativos (.NET/.NET Core, Java, Node.js e Python) capturam implicitamente esta resposta, respeitam o cabeçalho retípeido especificado pelo servidor e recandidam o pedido. A menos que a sua conta seja acedida simultaneamente por vários clientes, a próxima repetição será bem sucedida.

Se tiver mais de um cliente a operar cumulativamente acima da taxa de pedido, a contagem de retíria por defeito, que está atualmente definida para 9, pode não ser suficiente. Nesses casos, o cliente lança um `RequestRateTooLargeException` código de estado 429 para a aplicação. A contagem de repetições por defeito pode ser alterada definindo `RetryOptions` a instância "ConnectionPolicy". Por predefinição, o `RequestRateTooLargeException` código de estado 429 é devolvido após um tempo de espera acumulado de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Isto ocorre mesmo quando a contagem de repetição atual é inferior à contagem máxima de repetição, seja o padrão de 9 ou um valor definido pelo utilizador. 

[MaxRetryAttemptsOnThrottledRequests](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests) está definido para 3, por isso, neste caso, se uma operação de pedido for limitada por exceder a produção reservada para o contentor, a operação de pedido retrifique três vezes antes de lançar a exceção ao pedido. [MaxRetryWaitTimeInSegundos](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) está definido para 60, por isso, neste caso, se o tempo de espera acumulado em segundos, uma vez que o primeiro pedido excede 60 segundos, a exceção é lançada.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Estratégia de criação de partições e custos do débito aprovisionado

Uma boa estratégia de partição é importante para otimizar os custos na Azure Cosmos DB. Certifique-se de que não existe uma distorção das divisórias, que são expostas através de métricas de armazenamento. Certifique-se de que não existe uma distorção de produção para uma partição, que está exposta com métricas de produção. Certifique-se de que não há inclinação para as chaves de partição específicas. As chaves dominantes no armazenamento são expostas através de métricas, mas a chave estará dependente do padrão de acesso à sua aplicação. É melhor pensar na chave de partição lógica certa. Espera-se que uma boa chave de partição tenha as seguintes características:

* Escolha uma chave de partição que espalhe a carga de trabalho uniformemente em todas as divisórias e uniformemente ao longo do tempo. Por outras palavras, não deve ter algumas chaves para a maioria dos dados e algumas chaves com menos ou nenhum dado. 

* Escolha uma chave de partição que permita que os padrões de acesso sejam distribuídos uniformemente por divisórias lógicas. A carga de trabalho é razoavelmente mesmo através de todas as chaves. Por outras palavras, a maior parte da carga de trabalho não deve estar focada em algumas chaves específicas. 

* Escolha uma chave de partição que tenha uma vasta gama de valores. 

A ideia básica é espalhar os dados e a atividade no seu contentor através do conjunto de divisórias lógicas, para que os recursos para armazenamento e produção de dados possam ser distribuídos pelas divisórias lógicas. Os candidatos às chaves de partição podem incluir as propriedades que aparecem frequentemente como um filtro nas suas consultas. As consultas podem ser encaminhadas com eficiência ao incluir a chave de partição no predicado de filtro. Com esta estratégia de partição, a otimização da produção a provisionada será muito mais fácil. 

### <a name="design-smaller-items-for-higher-throughput"></a>Desenhe itens menores para maior produção 

A taxa de pedido ou o custo de processamento do pedido de uma determinada operação está diretamente correlacionado com a dimensão do artigo. As operações em grandes itens custarão mais do que operações em itens menores. 

## <a name="data-access-patterns"></a>Padrões de acesso a dados 

É sempre uma boa prática separar logicamente os seus dados em categorias lógicas com base na frequência com que acede aos dados. Ao categorizá-lo como dados quentes, médios ou frios, pode afinar o armazenamento consumido e a produção necessária. Dependendo da frequência de acesso, pode colocar os dados em recipientes separados (por exemplo, tabelas, gráficos e coleções) e afinar a produção prevista para acomodar as necessidades desse segmento de dados. 

Além disso, se estiver a usar a Azure Cosmos DB, e souber que não vai procurar por certos valores de dados ou raramente os acederá, deverá armazenar os valores comprimidos destes atributos. Com este método economiza-se no espaço de armazenamento, no espaço de índice e na produção a provisionada e resulta em custos mais baixos.

## <a name="optimize-by-changing-indexing-policy"></a>Otimizar alterando a política de indexação 

Por padrão, a Azure Cosmos DB indexa automaticamente todas as propriedades de cada registo. Isto destina-se a facilitar o desenvolvimento e garantir um excelente desempenho em vários tipos de consultas ad hoc. Se você tem grandes registos com milhares de propriedades, pagar o custo de produção para indexar cada propriedade pode não ser útil, especialmente se você apenas consultar contra 10 ou 20 dessas propriedades. À medida que se aproxima de saber qual é a sua carga de trabalho específica, a nossa orientação é afinar a sua política de índices. Todos os detalhes sobre a política de indexação DB do Azure Cosmos podem ser [consultados aqui.](index-policy.md) 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Produção de monitorização a provisionada e consumida 

Pode monitorizar o número total de RUs provisões, o número de pedidos limitados de taxa, bem como o número de RUs que consumiu no portal Azure. A imagem a seguir mostra uma métrica de utilização de exemplo:

:::image type="content" source="./media/optimize-cost-throughput/monitoring.png" alt-text="Monitor de unidades de solicitação no portal Azure":::

Também pode definir alertas para verificar se o número de pedidos limitados de taxa excede um limiar específico. Veja como monitorizar o artigo [da Azure Cosmos DB](use-metrics.md) para mais detalhes. Estes alertas podem enviar um e-mail para os administradores da conta ou chamar um webhook http personalizado ou uma Função Azure para aumentar automaticamente o rendimento do provisionado. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Dimensione a sua produção elástica e a pedido 

Uma vez que é cobrado para o produto previsto, o que corresponde ao rendimento previsto às suas necessidades pode ajudá-lo a evitar as taxas para o rendimento não percebido. Pode escalar o seu rendimento a provisionado para cima ou para baixo a qualquer momento, se necessário. Se as suas necessidades de produção forem muito previsíveis, pode utilizar funções Azure e utilizar um Gatilho temporizador para aumentar ou diminuir a [produção num horário](scale-on-schedule.md). 

* Monitorizar o consumo das suas RUs e o rácio de pedidos limitados de taxa pode revelar que não precisa de manter o fornecimento ao longo da constante durante todo o dia ou a semana. Pode receber menos tráfego durante a noite ou durante o fim de semana. Ao utilizar o portal Azure ou o Azure Cosmos DB native SDKs ou REST API, pode escalar a sua produção a qualquer momento. A AAPI REST da Azure Cosmos DB fornece pontos finais para atualizar programaticamente o nível de desempenho dos seus contentores, tornando simples a produção a partir do seu código, dependendo da hora do dia ou do dia da semana. A operação é realizada sem tempo de inatividade, e normalmente faz efeito em menos de um minuto. 

* Uma das áreas que deve escalar é quando ingere dados em Azure Cosmos DB, por exemplo, durante a migração de dados. Uma vez concluída a migração, pode reduzir a produção a provisionada para lidar com o estado estável da solução.  

* Lembre-se, a faturação está na granularidade de uma hora, por isso não poupará dinheiro se mudar a sua produção mais frequente do que uma hora de cada vez.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Determinar a produção necessária para uma nova carga de trabalho 

Para determinar a produção prevista para uma nova carga de trabalho, pode utilizar os seguintes passos: 

1. Realize uma avaliação inicial e áspera utilizando o planejador de capacidades e ajuste as suas estimativas com a ajuda do Azure Cosmos DB Explorer no portal Azure. 

2. Recomenda-se criar os recipientes com maior potência do que o esperado e, em seguida, reduzir-se conforme necessário. 

3. Recomenda-se usar um dos nativos Azure Cosmos DB SDKs para beneficiar de retrações automáticas quando os pedidos ficam limitados. Se estiver a trabalhar numa plataforma que não seja suportada e utilizar a API REST da Cosmos DB, implemente a sua própria política de relagem utilizando o `x-ms-retry-after-ms` cabeçalho. 

4. Certifique-se de que o seu código de aplicação suporta graciosamente o caso quando todas as recauchuções falharem. 

5. Pode configurar alertas a partir do portal Azure para obter notificações para limitar a taxa. Você pode começar com limites conservadores como 10 pedidos limitados de taxas ao longo dos últimos 15 minutos e mudar para regras mais ansiosas assim que você descobrir o seu consumo real. Os limites de tarifas ocasionais são bons, mostram que estás a jogar com os limites que estabeleceste e é exatamente isso que queres fazer. 

6. Utilize a monitorização para entender o seu padrão de tráfego, para que possa considerar a necessidade de ajustar dinamicamente o seu fornecimento de produção ao longo do dia ou de uma semana. 

7. Monitorize regularmente o seu rácio de produção a provisionado vs. consumido para se certificar de que não a provisionou mais do que o número exigido de contentores e bases de dados. Ter um pouco de produção a mais é uma boa verificação de segurança.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Melhores práticas para otimizar a produção a provisionada 

Os seguintes passos ajudam-no a tornar as suas soluções altamente escaláveis e económicas ao utilizar o Azure Cosmos DB.  

1. Se tiver sobressaída significativamente a produção de contentores e bases de dados, deverá rever as RUs a provisionadas vs e afinar as cargas de trabalho.  

2. Um método para estimar a quantidade de produção reservada exigida pela sua aplicação é registar a taxa RU da unidade de pedido associada à execução de operações típicas contra um contentor ou base de dados representativo da Azure Cosmos utilizado pela sua aplicação e, em seguida, estimar o número de operações que espera realizar a cada segundo. Certifique-se de medir e incluir consultas típicas e seu uso também. Para aprender a estimar os custos ru de consultas programáticas ou usando o portal consulte [Otimizar o custo das consultas.](./optimize-cost-reads-writes.md) 

3. Outra forma de obter operações e os seus custos em RUs é permitindo registos do Azure Monitor, o que lhe dará a repartição da operação/duração e a taxa de pedido. A Azure Cosmos DB fornece taxa de pedido para cada operação, para que cada carga de operação possa ser armazenada a partir da resposta e depois usada para análise. 

4. Pode escalar elasticamente a produção a provisionada, pois necessita para acomodar as suas necessidades de carga de trabalho. 

5. Pode adicionar e remover regiões associadas à sua conta Azure Cosmos conforme precisar e controlar os custos. 

6. Certifique-se de que tem distribuição uniforme de dados e cargas de trabalho através de divisórias lógicas dos seus recipientes. Se tiver uma distribuição de partição desigual, isto pode causar uma maior quantidade de produção do que o valor necessário. Se identificar que tem uma distribuição distorcida, recomendamos redistribuir a carga de trabalho uniformemente através das divisórias ou repartir os dados. 

7. Se tiver muitos contentores e estes contentores não necessitarem de SLAs, pode utilizar a oferta baseada na base de dados para os casos em que os SLAs de produção por contentor não se aplicam. Deve identificar quais dos recipientes Azure Cosmos que pretende migrar para a oferta de produção de nível de base de dados e depois migrar usando uma solução baseada em feed de mudança. 

8. Considere usar o "Cosmos DB Free Tier" (gratuito durante um ano), experimente cosmos DB (até três regiões) ou o emulador cosmos DB descarregado para cenários de dev/teste. Ao utilizar estas opções para teste-dev, pode baixar substancialmente os seus custos.  

9. Pode ainda realizar otimizações de custos específicas da carga de trabalho – por exemplo, aumentar o tamanho do lote, leituras de equilíbrio de carga em várias regiões e des duplicar dados, se aplicável.

10. Com a capacidade reservada da Azure Cosmos, você pode obter descontos significativos até 65% durante três anos. O modelo de capacidade reservada da Azure Cosmos DB é um compromisso inicial sobre as unidades de pedidos necessárias ao longo do tempo. Os descontos são hierárquicos de modo a que quanto mais unidades de pedido utilizar durante um período mais longo, mais o seu desconto será. Estes descontos são aplicados imediatamente. Quaisquer RUs utilizados acima dos seus valores provisões são cobrados com base no custo de capacidade não reservado. Consulte [a capacidade reservada da Cosmos DB)](cosmos-db-reserved-capacity.md)para mais detalhes. Considere a capacidade reservada de compra para reduzir ainda mais os seus custos de produção provisões.  

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode proceder para saber mais sobre a otimização de custos na Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](./optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das contas da Azure Cosmos em várias regiões](optimize-cost-regions.md)

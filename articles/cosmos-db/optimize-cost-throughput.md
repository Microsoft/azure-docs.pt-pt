---
title: Otimização do custo de entrada em Azure Cosmos DB
description: Este artigo explica como otimizar os custos de produção dos dados armazenados no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: c6c3e9462b26b44857eea6b53092baeeb5034364
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79501463"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Otimizar o débito aprovisionado no Azure Cosmos DB

Ao oferecer um modelo de entrada provisionado, o Azure Cosmos DB oferece um desempenho previsível a qualquer escala. Reservar ou fornecer a entrada antes do tempo elimina o "efeito ruidoso do vizinho" no seu desempenho. Especifica a quantidade exata de entrada que necessita e o Azure Cosmos DB garante a entrada configurada, apoiada por SLA.

Pode começar com uma entrada mínima de 400 RU/seg e escalar até dezenas de milhões de pedidos por segundo ou até mais. Cada pedido que emite contra o seu contentor ou base de dados Azure Cosmos, como um pedido de leitura, pedido de escrita, pedido de consulta, procedimentos armazenados têm um custo correspondente que é deduzido da sua produção provisionada. Se fornecer 400 RU/s e emitir uma consulta que custa 40 RUs, poderá emitir 10 consultas por segundo. Qualquer pedido para além disso será limitado à taxa e deverá voltar a tentar o pedido. Se estiver a utilizar os condutores de clientes, eles suportam a lógica de retry automática.

Pode aprovisionar o débito em bases de dados ou contentores e cada estratégia pode ajudá-lo a poupar nos custos, consoante o cenário.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Otimizar através do fornecimento de entrada em diferentes níveis

* Se fornecer a entrada numa base de dados, todos os recipientes, por exemplo, coleções/tabelas/gráficos nessa base de dados podem partilhar a entrada com base na carga. A entrada reservada ao nível da base de dados é partilhada de forma desigual, dependendo da carga de trabalho num conjunto específico de contentores.

* Se fornecer a entrada num recipiente, a entrada é garantida para esse recipiente, apoiado pelo SLA. A escolha de uma chave de partição lógica é crucial para equilibrar a distribuição da carga em todas as divisórias lógicas de um recipiente. Consulte artigos [de partilha](partitioning-overview.md) e [escala horizontal](partition-data.md) para obter mais detalhes.

Seguem-se algumas orientações para decidir sobre uma estratégia de provisão prevista:

Considere o fornecimento de **uma base de dados Azure Cosmos (contendo um conjunto de contentores) se:**

1. Você tem algumas dúzias de contentores Azure Cosmos e quer partilhar a entrada em alguns ou todos eles. 

2. Está a migrar de uma base de dados de um único inquilino projetada para funcionar em VMs ou no local hospedados em IaaS, por exemplo, NoSQL ou bases de dados relacionais para Azure Cosmos DB. E se tiver muitas coleções/tabelas/gráficos e não quiser fazer alterações no seu modelo de dados. Note que poderá ter de comprometer alguns dos benefícios oferecidos pela Azure Cosmos DB se não estiver a atualizar o seu modelo de dados ao migrar de uma base de dados no local. Recomenda-se que reaceace sempre o seu modelo de dados para obter o máximo em termos de desempenho e também para otimizar custos. 

3. Pretende absorver picos não planeados em cargas de trabalho em virtude da entrada em conjunto ao nível da base de dados sujeita a um pico inesperado na carga de trabalho. 

4. Em vez de definir uma entrada específica em recipientes individuais, preocupa-se em obter a entrada agregada através de um conjunto de recipientes dentro da base de dados.

**Considere o fornecimento de entrada num recipiente individual se:**

1. Tem alguns contentores Azure Cosmos. Como o Azure Cosmos DB é schema-agnóstico, um recipiente pode conter itens que têm esquemas heterogéneos e não exige que os clientes criem vários tipos de contentores, um para cada entidade. É sempre uma opção a considerar se o agrupamento separado, digamos, 10-20 contentores num único recipiente faz sentido. Com um mínimo de 400 RUs para contentores, juntar todos os 10-20 contentores em um poderia ser mais rentável. 

2. Você quer controlar a entrada num recipiente específico e obter a entrada garantida num determinado recipiente apoiado pela SLA.

**Considere um híbrido das duas estratégias acima:**

1. Como mencionado anteriormente, o Azure Cosmos DB permite-lhe misturar e combinar as duas estratégias acima referidas, para que agora possa ter alguns contentores dentro da base de dados Azure Cosmos, que podem partilhar o resultado aprovisionado na base de dados, bem como alguns contentores dentro da mesma base de dados, que podem ter quantidades dedicadas de entrada aprovisionada. 

2. Pode aplicar as estratégias acima para criar uma configuração híbrida, onde tem ambos os níveis de base de dados aprovisionados com alguns recipientes que têm uma entrada dedicada.

Como mostrado na tabela seguinte, dependendo da escolha da API, pode fornecer a entrada em diferentes granularidades.

|API|Para uma entrada **partilhada,** configure |Para uma entrada **dedicada,** configure |
|----|----|----|
|SQL API|Base de Dados|Contentor|
|API do Azure Cosmos DB para MongoDB|Base de Dados|Coleção|
|API de Cassandra|Espaço-chave|Tabela|
|API do Gremlin|Conta de base de dados|Graph|
|API de Tabela|Conta de base de dados|Tabela|

Ao fornecer a entrada em diferentes níveis, pode otimizar os seus custos com base nas características da sua carga de trabalho. Como mencionado anteriormente, pode aumentar ou diminuir programáticamente e a qualquer momento aumentar ou diminuir a sua entrada prevista para recipientes individuais ou colectivamente através de um conjunto de contentores. Ao escalonar elástico a entrada à medida que a sua carga de trabalho muda, só paga pela entrada que configura. Se o seu contentor ou um conjunto de contentores fordistribuído por várias regiões, então a entrada que configura no recipiente ou num conjunto de contentores é garantidamente disponibilizada em todas as regiões.

## <a name="optimize-with-rate-limiting-your-requests"></a>Otimize com a limitação da taxa dos seus pedidos

Para cargas de trabalho que não sejam sensíveis à latência, pode fornecer menos produção e deixar que a taxa de manuseamento da aplicação limite a taxa quando a produção real exceda a produção prevista. O servidor terminará preventivamente o `RequestRateTooLarge` pedido com (código de estado `x-ms-retry-after-ms` HTTP 429) e devolverá o cabeçalho indicando a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de voltar a experimentar o pedido. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Retry lógica em SDKs 

Os SDKs nativos (.NET/.NET Core, Java, Node.js e Python) captam implicitamente esta resposta, respeitam o cabeçalho de retry-after especificado pelo servidor e retentam novamente o pedido. A menos que a sua conta seja acedida simultaneamente por vários clientes, a próxima reprovação terá sucesso.

Se tiver mais de um cliente a operar de forma consistente acima da taxa de pedido, a contagem de retry predefinido, que está atualmente fixada para 9, pode não ser suficiente. Nesses casos, o cliente `RequestRateTooLargeException` lança um código de estado 429 para a aplicação. A contagem de retry predefinido `RetryOptions` pode ser alterada definindo a instância ConnectionPolicy. Por predefinição, o `RequestRateTooLargeException` código de estado 429 é devolvido após um tempo acumulado de espera de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Isto ocorre mesmo quando a contagem de retry atual é inferior à contagem máxima de retry, seja o padrão de 9 ou um valor definido pelo utilizador. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) está definido para 3, por isso, neste caso, se uma operação de pedido for limitada por exceder a entrada reservada para o recipiente, a operação de pedido se retenta três vezes antes de lançar a exceção ao pedido. [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) está definido para 60, por isso, neste caso, se o tempo de espera acumulado de retry em segundos desde que o primeiro pedido excede 60 segundos, a exceção é lançada.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Estratégia de criação de partições e custos do débito aprovisionado

Uma boa estratégia de partição é importante para otimizar os custos no Azure Cosmos DB. Certifique-se de que não há distorções de divisórias, que são expostas através de métricas de armazenamento. Certifique-se de que não existe um desvio de entrada para uma partição, que é exposta com métricas de entrada. Certifique-se de que não há alterações em relação a chaves de partição específicas. As chaves dominantes no armazenamento são expostas através de métricas, mas a chave será dependente do seu padrão de acesso à aplicação. É melhor pensar na chave de partição lógica certa. Espera-se que uma boa chave de partição tenha as seguintes características:

* Escolha uma chave de partição que espalhe a carga de trabalho uniformemente em todas as divisórias e uniformemente ao longo do tempo. Por outras palavras, não deve ter algumas chaves com a maioria dos dados e algumas chaves com menos ou nenhum dado. 

* Escolha uma chave de partição que permita que os padrões de acesso sejam distribuídos uniformemente por divisórias lógicas. A carga de trabalho é razoavelmente até através de todas as chaves. Por outras palavras, a maior parte da carga de trabalho não deve estar focada em algumas chaves específicas. 

* Escolha uma chave de partição que tenha uma ampla gama de valores. 

A ideia básica é espalhar os dados e a atividade no seu recipiente através do conjunto de divisórias lógicas, para que os recursos para armazenamento e entrada de dados possam ser distribuídos pelas divisórias lógicas. Os candidatos a chaves de partição podem incluir as propriedades que aparecem frequentemente como filtro nas suas consultas. As consultas podem ser eficientemente encaminhadas, incluindo a chave de partição no predicado do filtro. Com uma estratégia de divisão, a otimização do contributo previsto será muito mais fácil. 

### <a name="design-smaller-items-for-higher-throughput"></a>Design de itens menores para maior estoque 

A taxa de pedido ou o custo de processamento de pedido de uma determinada operação está diretamente correlacionado com o tamanho do artigo. As operações em grandes itens custarão mais do que as operações em itens mais pequenos. 

## <a name="data-access-patterns"></a>Padrões de acesso a dados 

É sempre uma boa prática separar logicamente os seus dados em categorias lógicas com base na frequência com que acede aos dados. Classificando-os como dados quentes, médios ou frios, pode afinar o armazenamento consumido e a entrada necessária. Dependendo da frequência de acesso, pode colocar os dados em recipientes separados (por exemplo, tabelas, gráficos e coleções) e afinar a entrada disponibilizada neles para acomodar as necessidades desse segmento de dados. 

Além disso, se estiver a usar o Azure Cosmos DB, e souber que não vai pesquisar por determinados valores de dados ou raramente aceder á-los, deverá armazenar os valores comprimidos destes atributos. Com este método você economiza no espaço de armazenamento, espaço de índice, e aprovisionado de entrada e resulta em custos mais baixos.

## <a name="optimize-by-changing-indexing-policy"></a>Otimizar alterando a política de indexação 

Por padrão, o Azure Cosmos DB indexa automaticamente todas as propriedades de cada registo. Isto destina-se a facilitar o desenvolvimento e garantir um excelente desempenho em muitos tipos diferentes de consultas ad hoc. Se tiver grandes registos com milhares de propriedades, pagar o custo de entrada para indexar cada propriedade pode não ser útil, especialmente se você só questionar contra 10 ou 20 dessas propriedades. À medida que se aproxima da sua carga de trabalho específica, a nossa orientação é afinar a sua política de índices. Todos os detalhes sobre a política de indexação do Azure Cosmos DB podem ser consultados [aqui](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitorização aprovisionada e consumida 

Pode monitorizar o número total de RUs aprovisionados, o número de pedidos limitados de taxas, bem como o número de RUs que consumiu no portal Azure. A imagem que se segue mostra uma métrica de utilização de exemplo:

![Monitorize unidades de pedido no portal Azure](./media/optimize-cost-throughput/monitoring.png)

Também pode definir alertas para verificar se o número de pedidos limitados de taxa excede um limiar específico. Veja como monitorizar o artigo da [Azure Cosmos DB](use-metrics.md) para mais detalhes. Estes alertas podem enviar um e-mail para os administradores da conta ou chamar um Webhook http personalizado ou uma Função Azure para aumentar automaticamente a produção provisionada. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Dimensione a sua entrada elástica e a pedido 

Uma vez que é cobrado para a entrada prevista, a correspondência do serviço de entrada previsto para as suas necessidades pode ajudá-lo a evitar as taxas para a entrada não utilizada. Pode escalar a sua entrada aqualquer hora, conforme necessário. Se as suas necessidades de entrada forem muito previsíveis, pode utilizar funções Azure e utilizar um Gatilho temporizador para aumentar ou diminuir a [entrada num horário](scale-on-schedule.md). 

* Monitorizar o consumo das suas RUs e o rácio de pedidos limitados de taxas pode revelar que não precisa de manter o fornecimento durante toda a constante ao longo do dia ou da semana. Pode receber menos tráfego à noite ou durante o fim de semana. Ao utilizar o portal Azure ou o Azure Cosmos DB sDKs nativo ou rest API, pode escalar a sua entrada a qualquer momento. A API REST da Azure Cosmos DB fornece pontos finais para atualizar programáticamente o nível de desempenho dos seus contentores, tornando simples ajustar a produção do seu código dependendo da hora do dia ou do dia da semana. A operação é realizada sem qualquer tempo de inatividade, e normalmente faz efeito em menos de um minuto. 

* Uma das áreas que deve escalar a entrada é quando ingere dados no Azure Cosmos DB, por exemplo, durante a migração de dados. Uma vez concluída a migração, pode dimensionar a entrada prevista para baixo para lidar com o estado estável da solução.  

* Lembre-se, a faturação está na granularidade de uma hora, por isso não poupará dinheiro se alterar a sua faturação mais frequentemente do que uma hora de cada vez.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Determine a entrada necessária para uma nova carga de trabalho 

Para determinar a entrada prevista para uma nova carga de trabalho, pode utilizar os seguintes passos: 

1. Faça uma avaliação inicial e áspera utilizando o planejador de capacidades e ajuste as suas estimativas com a ajuda do Azure Cosmos Explorer no portal Azure. 

2. É recomendado criar os recipientes com uma entrada mais alta do que o esperado e depois escalonar-se conforme necessário. 

3. Recomenda-se usar um dos SDKs DB da Azure Cosmos para beneficiar de repetições automáticas quando os pedidos ficam limitados à taxa. Se estiver a trabalhar numa plataforma que não seja suportada e utilize a API REST `x-ms-retry-after-ms` da Cosmos DB, implemente a sua própria política de retry usando o cabeçalho. 

4. Certifique-se de que o seu código de aplicação suporta graciosamente o caso quando todas as tentativas falharem. 

5. Pode configurar alertas do portal Azure para obter notificações para limitar as taxas. Você pode começar com limites conservadores como 10 pedidos limitados de taxas nos últimos 15 minutos e mudar para regras mais ansiosas uma vez que você descobrir o seu consumo real. Os limites de taxas ocasionais são bons, mostram que estás a jogar com os limites que estabeleceste e é exatamente isso que queres fazer. 

6. Utilize a monitorização para compreender o seu padrão de tráfego, para que possa considerar a necessidade de ajustar de forma dinâmica o seu fornecimento de entrada ao longo do dia ou uma semana. 

7. Monitorize regularmente a sua relação de entrada consumida vs. consumida para se certificar de que não disponibilizou mais do que o número necessário de contentores e bases de dados. Ter um pouco mais de entrada é uma boa verificação de segurança.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Boas práticas para otimizar o provisão provisionado 

Os seguintes passos ajudam-no a tornar as suas soluções altamente escaláveis e rentáveis ao utilizar o Azure Cosmos DB.  

1. Se tiver sobressaltado significativamente a entrada em contentores e bases de dados, deve rever as RUs aprovisionadas vs consumidas e afinar as cargas de trabalho.  

2. Um método para estimar a quantidade de entrada reservada exigida pela sua aplicação é registar a taxa da unidade de pedido RU associada à execução de operações típicas contra um recipiente ou base de dados representativo do Azure Cosmos utilizado pela sua aplicação e, em seguida, estimar o número de operações que antecipa realizar a cada segundo. Certifique-se de medir e incluir consultas típicas e seu uso também. Para aprender a estimar os custos de consultas de RU programáticamente ou usando o portal ver [Otimizar o custo das consultas](online-backup-and-restore.md). 

3. Outra forma de obter operações e os seus custos em RUs é permitindo registos do Monitor Azure, o que lhe dará a repartição da operação/duração e a cobrança de pedidos. A Azure Cosmos DB fornece o pedido de cobrança de cada operação, para que cada carga de operação possa ser armazenada de volta da resposta e depois usada para análise. 

4. Pode escalar elástico para cima e para baixo, à medida que necessita para acomodar as suas necessidades de carga de trabalho. 

5. Pode adicionar e remover regiões associadas à sua conta Azure Cosmos conforme necessitar e controlar os custos. 

6. Certifique-se de que tem até distribuição de dados e cargas de trabalho através de divisórias lógicas dos seus contentores. Se tiver uma distribuição de partição desigual, isto pode causar uma maior quantidade de entrada do que o valor necessário. Se identificar que tem uma distribuição distorcida, recomendamos que redistribua a carga de trabalho uniformemente através das divisórias ou repartipartifique os dados. 

7. Se tiver muitos contentores e estes contentores não necessitarem de SLAs, pode utilizar a oferta baseada na base de dados para os casos em que o slé de entrada por contentor não se aplica. Deve identificar qual dos contentores Azure Cosmos pretende migrar para a oferta de entrada de nível de base de dados e depois emigrar utilizando uma solução baseada em alterações. 

8. Considere usar o "Cosmos DB Free Tier" (gratuito por um ano), Experimente cosmos DB (até três regiões) ou emulador Cosmos DB descarregado para cenários de dev/teste. Ao utilizar estas opções para teste-dev, pode reduzir substancialmente os seus custos.  

9. Pode ainda realizar otimizações de custos específicas para a carga de trabalho – por exemplo, aumentar o tamanho do lote, leituras de equilíbrio de carga em várias regiões e desduplicar dados, se aplicável.

10. Com a capacidade reservada à Azure Cosmos DB, pode obter descontos significativos até 65% durante três anos. O modelo de capacidade reservado da Azure Cosmos DB é um compromisso inicial sobre as unidades de pedidos necessárias ao longo do tempo. Os descontos são hierárquicos de modo a que quanto mais unidades de pedido utilizar durante um período mais longo, mais o seu desconto será. Estes descontos são aplicados imediatamente. Quaisquer RUs utilizados acima dos seus valores provisionados são cobrados com base no custo de capacidade não reservado. Consulte a capacidade reservada da [Cosmos DB)](cosmos-db-reserved-capacity.md)para mais detalhes. Considere comprar capacidade reservada para reduzir ainda mais os custos de entrada previstos.  

## <a name="next-steps"></a>Passos seguintes

Em seguida, poderá proceder a mais informações sobre a otimização de custos em Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimização do custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas multi-regiões da Azure Cosmos](optimize-cost-regions.md)


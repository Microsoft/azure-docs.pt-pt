---
title: Criar uma solução de IoT com o Azure Stream Analytics
description: Tutorial de introdução para a solução de IoT do Stream Analytics de um cenário de tollbooth
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 5b2c153646021aeb8ee0dbb787cfce41af19568d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443681"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Criar uma solução de IoT com o Stream Analytics

## <a name="introduction"></a>Introdução
Nesta solução, irá aprender a utilizar o Azure Stream Analytics para obter informações em tempo real a partir dos seus dados. Os desenvolvedores podem facilmente combinar fluxos de dados, como fluxos de clique, registos e eventos gerados por dispositivos, com registos históricos ou dados de referência para derivar informações de negócio. Como um serviço de computação de fluxos em tempo real totalmente gerido que está alojado no Microsoft Azure, Azure Stream Analytics fornece resiliência incorporada, a baixa latência e a escalabilidade para-lhe começar em minutos.

Depois de concluir esta solução, é possível:

* Familiarize-se com o portal do Azure Stream Analytics.
* Configurar e implementar uma tarefa de transmissão em fluxo.
* Articular problemas do mundo real e resolvê-los através da linguagem de consulta do Stream Analytics.
* Desenvolva a transmissão em fluxo soluções para os seus clientes com o Stream Analytics com confiança.
* Utilize a monitorização e registo experiência para resolver problemas.

## <a name="prerequisites"></a>Pré-requisitos
É necessário para concluir esta solução, os seguintes pré-requisitos:
* Uma [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Introdução de cenário: "Olá, incidir!"
Uma estação de ligação é um fenômeno comuns. Se deparar com elas em muitas expressways, pontes e túneis em todo o mundo. Cada estação de ligação tem vários estandes de ligação. Em estandes manuais, parar para pagar a ligação para um atendedor. Em estandes automatizadas, um sensor na parte superior de cada stand analisa um cartão RFID afixação windshield de sua veículo conforme passa o pedágio. É fácil de visualizar a passagem para dos veículos através destas estações de ligação como um fluxo de eventos através do qual podem ser executadas operações interessantes.

![Imagem de carros em estandes de ligação](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Dados recebidos
Esta solução funciona com dois fluxos de dados. Instalado na entrada e saída das estações de número de sensores produzem o primeiro fluxo. O fluxo segundo é um conjunto de dados de pesquisa estática que tem dados de registo de veículos.

### <a name="entry-data-stream"></a>Fluxo de dados de entrada
O fluxo de dados de entrada contém informações sobre carros à medida que entra o número de estações. Os eventos de dados de saída estão em direto transmitido numa fila do Hub de eventos de uma aplicação Web incluídos na aplicação de exemplo.

| TollID | EntryTime | LicensePlate | Estado | Criar | Modelo | VehicleType | VehicleWeight | Ligação | Etiqueta |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Eis uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| TollID |O ID de portagens de número que identifica exclusivamente um pedágio |
| EntryTime |A data e hora de entrada do veículo para o pedágio em UTC |
| LicensePlate |O número de pratos de licença do veículo |
| Estado |Um Estado nos Estados Unidos |
| Criar |Fabricante do automóvel |
| Modelo |O número de modelo do automóvel |
| VehicleType |1 para os veículos de passageiros ou 2 para veículos comerciais |
| WeightType |Peso do veículo em toneladas; 0 para veículos de passageiros |
| Ligação |O valor de ligação em dólares americanos |
| Etiqueta |A marca e sobre o automóvel que automatiza o pagamento; em branco de onde o pagamento foi feito manualmente |

### <a name="exit-data-stream"></a>Fluxo de dados de saída
O fluxo de dados de saída contém informações sobre carros deixando a estação de ligação. Os eventos de dados de saída estão em direto transmitido numa fila do Hub de eventos de uma aplicação Web incluídos na aplicação de exemplo.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Eis uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| TollID |O ID de portagens de número que identifica exclusivamente um pedágio |
| ExitTime |A data e hora de saída do veículo de pedágio em UTC |
| LicensePlate |O número de pratos de licença do veículo |

### <a name="commercial-vehicle-registration-data"></a>Dados de registro de veículos comerciais
A solução utiliza um instantâneo de uma base de dados de registo de veículos comerciais. Estes dados são guardados como um ficheiro JSON para o armazenamento de Blobs do Azure, incluído no exemplo.

| LicensePlate | RegistrationId | Fora do prazo |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| I UM 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Eis uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| LicensePlate |O número de pratos de licença do veículo |
| RegistrationId |ID de registo o veículo |
| Fora do prazo |O estado do registo do veículo: 0 se o registo de veículos estiver ativo, 1 se o registro está vencido |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurar o ambiente para o Azure Stream Analytics
Para concluir esta solução, precisa de uma subscrição do Microsoft Azure. Se não tiver uma conta do Azure, pode [solicitar uma versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Certifique-se de que siga os passos na secção "Limpar a sua conta do Azure" no final deste artigo, para que pode fazer o melhor uso do seu crédito do Azure.

## <a name="deploy-the-sample"></a>Implementar o exemplo
Existem vários recursos que podem ser facilmente implementados num grupo de recursos, juntamente com alguns cliques. A definição de solução está alojada no repositório do GitHub na [ https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Implementar o modelo de TollApp no portal do Azure
1. Para implementar o ambiente de TollApp no Azure, utilize esta ligação para [implementar o modelo do Azure TollApp](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Se lhe for pedido, inicie sessão no portal do Azure.

3. Escolha a subscrição na qual são faturados os vários recursos.

4. Especifique um novo grupo de recursos, com um nome exclusivo, por exemplo `MyTollBooth`.

5. Selecione uma localização do Azure.

6. Especifique um **intervalo** como um número de segundos. Este valor é utilizado no aplicativo da web de exemplo, para saber como frequentemente enviar dados para o Hub de eventos.

7. **Verificar** para aceitar os termos e condições.

8. Selecione **afixar ao dashboard** para que possa localizar facilmente os recursos mais tarde.

9. Selecione **Compra** para implementar o modelo de exemplo.

10. Após alguns instantes, é apresentada uma notificação confirmar a **implementação concluída com êxito**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Reveja os recursos do Azure Stream Analytics TollApp

1. Iniciar sessão no portal do Azure

2. Localize o grupo de recursos com o nome na secção anterior.

3. Certifique-se de que os seguintes recursos estão listados no grupo de recursos:
   - Conta um Cosmos DB
   - Uma tarefa do Azure Stream Analytics
   - Uma conta de armazenamento do Azure
   - Um Hub de eventos do Azure
   - Duas aplicações Web

## <a name="examine-the-sample-tollapp-job"></a>Examine a tarefa de TollApp de exemplo
1. A partir do grupo de recursos na secção anterior, selecione a tarefa de transmissão em fluxo do Stream Analytics começando com o nome **tollapp** (o nome contém caracteres aleatórios exclusividade).

2. Sobre o **descrição geral** página da tarefa, tenha em atenção a **consulta** caixa para ver a sintaxe de consulta.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Parafraseando a intenção da consulta, digamos que precisa contar o número de veículos introduza pedágio. Como pedágio autoestrada tem um fluxo contínuo de veículos introduzir, esses são os eventos de entrada são análogos para um fluxo que nunca deixa. Para quantificar o fluxo, tem de definir um "período de tempo" para medir o ao longo. Vamos refinar ainda mais, a pergunta para "quantos veículos introduzir pedágio a cada três minutos?" Isto é frequentemente referido como a contagem de em cascata.

   Como pode ver, o Azure Stream Analytics utiliza uma linguagem de consulta que é parecida com SQL e adiciona algumas extensões para especificar os aspetos relacionados com a hora da consulta.  Para obter mais detalhes, leia sobre [gerenciamento de tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) construções utilizadas na consulta.

3. Examine as entradas da tarefa de exemplo TollApp. Apenas a entrada de EntryStream é utilizada na consulta atual.
   - **EntryStream** entrada é uma ligação do Hub de eventos que enfileira dados que representam sempre que um carro insere um tollbooth na autoestrada. Uma aplicação web que é parte do exemplo é a criação de eventos, e esses dados são enfileirados em deste Hub de eventos. Tenha em atenção que esta entrada é consultada na cláusula FROM da consulta de transmissão em fluxo.
   - **ExitStream** entrada é uma ligação do Hub de eventos que enfileira dados que representam sempre que um carro é encerrado um tollbooth a autoestrada. Esta entrada de transmissão em fluxo é utilizada em posteriores variações da sintaxe de consulta.
   - **Registo** entrada é uma ligação de armazenamento de Blobs do Azure, que aponta para um arquivo de registration.json estático, usado para pesquisas, conforme necessário. Esta entrada de dados de referência é utilizada na posteriores variações da sintaxe de consulta.

4. Examine as saídas da tarefa de exemplo TollApp.
   - **O cosmos DB** saída é um contentor de base de dados do Cosmos que recebe os eventos de sink de saída. Tenha em atenção que esta saída é utilizada na cláusula de consulta de transmissão em fluxo INTO.

## <a name="start-the-tollapp-streaming-job"></a>Iniciar a tarefa de transmissão em fluxo TollApp
Siga estes passos para iniciar a tarefa de transmissão em fluxo:

1. Sobre o **descrição geral** página da tarefa, selecione **iniciar**.

2. Sobre o **tarefa de início** painel, selecione **agora**.

3. Após alguns instantes, uma vez que a tarefa está em execução, no **descrição geral** página da tarefa de transmissão em fluxo, ver o **monitorização** gráfico. O gráfico deve mostrar vários eventos de entrada dos milhares e dezenas de eventos de saída.

## <a name="review-the-cosmosdb-output-data"></a>Reveja os dados de saída do cosmos DB
1. Localize o grupo de recursos que contém os recursos de TollApp.

2. Selecione a conta do Azure Cosmos DB com o padrão de nome **tollapp\<aleatório\>-cosmos**.

3. Selecione o **Data Explorer** cabeçalho para abrir a página de Explorador de dados.

4. Expanda a **tollAppDatabase** > **tollAppCollection** > **documentos**.

5. Na lista de ids, vários documentos são apresentados depois da saída está disponível.

6. Selecione cada id para examinar o documento JSON. Observe que cada tollid, windowend tempo e a contagem de carros nessa janela.

7. Após um três minutos adicionais, outro conjunto de quatro documentos que está disponível, um documento por tollid.


## <a name="report-total-time-for-each-car"></a>Tempo total do relatório para cada carro
O tempo médio necessário para um carro percorrer a ligação ajuda a avaliar a eficiência do processo e a experiência do cliente.

Para localizar o tempo total, Junte-se o fluxo de EntryTime com o fluxo de ExitTime. Associe dois fluxos de entrada nas colunas de TollId e LicencePlate correspondentes iguais. O **associar** operador requer que especifique leeway temporal que descreve a diferença de tempo aceitável entre os eventos associados. Utilize o **DATEDIFF** função para especificar que eventos devem ser mais do que 15 minutos entre si. Também se aplicam a **DATEDIFF** função para sair e horas de entrada para calcular o tempo real que um carro gasta na estação de ligação. Tenha em atenção a diferença do uso de **DATEDIFF** quando ela é usada num **SELECIONE** instrução em vez de um **associar** condição.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Para atualizar o TollApp sintaxe de consulta da tarefa de transmissão em fluxo:

1. Sobre o **descrição geral** página da tarefa, selecione **parar**.

2. Aguarde alguns momentos para a notificação de que a tarefa foi parada.

3. Sob o cabeçalho da TOPOLOGIA da tarefa, selecione **< > consulta**

4. Cole a consulta SQL de transmissão em fluxo ajustada.

5. Selecione **guardar** para guardar a consulta. Confirme **Sim** para guardar as alterações.

6. Sobre o **descrição geral** página da tarefa, selecione **iniciar**.

7. Sobre o **tarefa de início** painel, selecione **agora**.

### <a name="review-the-total-time-in-the-output"></a>Reveja o tempo total na saída
Repita os passos na secção anterior para rever os dados de saída do CosmosDB provenientes da tarefa de transmissão em fluxo. Reveja os documentos JSON mais recente.

Por exemplo, este documento mostra um carro de exemplo com uma determinada placa, o tempo de entrytime e de saída e o campo de durationinminutes calculada DATEDIFF que mostra a duração de portagens incidir como dois minutos:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Relatório de veículos com o registo de expirada
O Azure Stream Analytics pode utilizar instantâneos estáticos de dados de referência para associar com fluxos de dados temporais. Para demonstrar esta funcionalidade, utilize a seguinte pergunta de exemplo. A entrada de registo é um ficheiro de json do blob estático que lista as Expirações de etiquetas de licença. Ao aderir ao sobre o que eu preciso licença, os dados de referência é comparada com cada veículo passar através da ligação.

Se um veículo de comercial estiver registado com a empresa de ligação, pode passar por meio do pedágio sem ser parada para inspeção. Utilize a tabela de pesquisa de registo para identificar todos os veículos comerciais que já passaram da validade registos.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Repita os passos na secção anterior para atualizar o TollApp sintaxe de consulta da tarefa de transmissão em fluxo.

2. Repita os passos na secção anterior para rever os dados de saída do CosmosDB provenientes da tarefa de transmissão em fluxo.

Exemplo de saída:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Aumentar horizontalmente o trabalho
O Azure Stream Analytics foi concebido para dimensionar de forma para que ele pode lidar com grandes volumes de dados. A consulta do Azure Stream Analytics pode utilizar um **PARTITION BY** cláusula informar ao sistema que este passo aumenta horizontalmente. **PartitionId** é uma coluna especial que o sistema adiciona a coincide com o ID de partição da entrada (hub de eventos).

Para aumentar horizontalmente a consulta para partições, edite a sintaxe de consulta para o código a seguir:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Para aumentar verticalmente a tarefa de transmissão em fluxo mais unidades de transmissão em fluxo:

1. **Parar** a tarefa atual.

2. Atualize a sintaxe de consulta no **< > consulta** de páginas e guardar as alterações.

3. Sob o cabeçalho de configurar na tarefa de transmissão em fluxo, selecione **dimensionamento**.

4. Deslize o **unidades transmissão em fluxo** controlo de deslize de 1 a 6. Unidades de transmissão em fluxo, defina a quantidade de potência de computação que pode receber o trabalho. Selecione **Guardar**.

5. **Iniciar** a tarefa de transmissão em fluxo para demonstrar o dimensionamento adicional. O Azure Stream Analytics distribui o trabalho mais recursos de computação e alcançar um débito melhor, o trabalho de criação de partições todos os recursos usando a coluna designada na cláusula PARTITION BY.

## <a name="monitor-the-job"></a>Monitorizar a tarefa
O **MONITOR** área contém estatísticas sobre a tarefa em execução. Configuração de iniciantes é necessária para utilizar a conta de armazenamento na mesma região (nome de ligação como o restante deste documento).

![Monitorização da tarefa de Stream Analytics do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Pode acessar **registos de atividades** partir do dashboard da tarefa **definições** área também.

## <a name="clean-up-the-tollapp-resources"></a>Limpar os recursos de TollApp
1. Pare a tarefa de Stream Analytics no portal do Azure.

2. Localize o grupo de recursos contém oito recursos relacionados com o modelo de TollApp.

3. Selecione **Eliminar grupo de recursos**. Escreva o nome do grupo de recursos para confirmar a eliminação.

## <a name="conclusion"></a>Conclusão
Esta solução apresentou-o para o serviço Azure Stream Analytics. Ele demonstrou como configurar as entradas e saídas de tarefa do Stream Analytics. Utilizando o cenário de ligação de dados, a solução explicado tipos comuns de problemas que possam surgir no espaço de dados em movimento e como eles podem ser resolvidos com consultas simples do tipo SQL no Azure Stream Analytics. A solução descrito construções de extensão do SQL para trabalhar com dados temporais. Ele mostrou como associar fluxos de dados, como enriquecer o fluxo de dados com dados de referência estáticos e como aumentar horizontalmente uma consulta para alcançar um débito mais elevado.

Embora esta solução fornece uma boa introdução, não estão concluída por qualquer meio. Pode encontrar mais padrões de consulta usando a linguagem SAQL na [consultar exemplos para padrões de utilização comuns do Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).

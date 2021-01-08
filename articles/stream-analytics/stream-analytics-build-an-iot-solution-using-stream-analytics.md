---
title: Construa uma solução IoT utilizando o Azure Stream Analytics
description: Tutorial de início para a solução IoT stream Analytics de um cenário de portagem
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ddec53b18cd6f374a5665298b43b46122bcfa143
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016155"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Construa uma solução IoT utilizando o Stream Analytics

## <a name="introduction"></a>Introdução
Nesta solução, aprende a usar o Azure Stream Analytics para obter informações em tempo real a partir dos seus dados. Os desenvolvedores podem facilmente combinar fluxos de dados, tais como streams de cliques, registos e eventos gerados por dispositivos, com registos históricos ou dados de referência para obter insights de negócio. Como um serviço de computação de streaming totalmente gerido em tempo real que está hospedado no Microsoft Azure, o Azure Stream Analytics fornece resiliência incorporada, baixa latência e escalabilidade para o pôr a funcionar em minutos.

Depois de completar esta solução, pode:

* Familiarize-se com o portal Azure Stream Analytics.
* Configure e implemente um trabalho de streaming.
* Articular os problemas do mundo real e resolvê-los utilizando a linguagem de consulta stream analytics.
* Desenvolva soluções de streaming para os seus clientes utilizando o Stream Analytics com confiança.
* Utilize a experiência de monitorização e registo para resolver problemas.

## <a name="prerequisites"></a>Pré-requisitos
Precisa dos seguintes pré-requisitos para completar esta solução:
* Uma [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Introdução do cenário: "Olá, Portagem!"
Uma estação de portagem é um fenómeno comum. Encontra-se com eles em muitas vias expressas, pontes e túneis por todo o mundo. Cada portagem tem várias portagens. Nas cabines manuais, paramos para pagar a portagem a um empregado. Em cabines automáticas, um sensor em cima de cada cabine digitaliza um cartão RFID que está afixado no para-brisas do seu veículo à medida que passa pela portagem. É fácil visualizar a passagem de veículos através destas estações de portagem como um fluxo de eventos sobre o qual podem ser realizadas operações interessantes.

![Imagem de carros em cabines de portagem](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Dados de entrada
Esta solução funciona com dois fluxos de dados. Os sensores instalados na entrada e saída das estações de pedágio produzem o primeiro fluxo. O segundo fluxo é um conjunto de dados de procura estática que tem dados de registo de veículos.

### <a name="entry-data-stream"></a>Fluxo de dados de entrada
O fluxo de dados de entrada contém informações sobre carros à medida que entram nas estações de portagem. Os eventos de dados de saída são transmitidos em direto para uma fila do Event Hub a partir de uma Aplicação Web incluída na aplicação da amostra.

| TollID | Tempo de Entrada | Placa de Licença | Estado | Criação | Modelo | VehicleType | Peso do veículo | Portagem | Etiqueta |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Rio Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Touro |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Rio Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Aqui está uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| TollID |O ID da portagem que identifica exclusivamente uma portagem |
| Tempo de Entrada |A data e hora de entrada do veículo na cabine de portagem na UTC |
| Placa de Licença |O número da matrícula do veículo |
| Estado |Um estado nos Estados Unidos |
| Criação |O fabricante do automóvel |
| Modelo |O número de modelo do automóvel |
| VehicleType |1 para veículos de passageiros ou 2 para veículos comerciais |
| Type de peso |Peso do veículo em toneladas; 0 para veículos de passageiros |
| Portagem |O valor das portagens em USD |
| Etiqueta |O e-Tag no automóvel que automatiza o pagamento; em branco onde o pagamento foi feito manualmente |

### <a name="exit-data-stream"></a>Fluxo de dados de saída
O fluxo de dados de saída contém informações sobre carros que saem da estação de portagem. Os eventos de dados de saída são transmitidos em direto para uma fila do Event Hub a partir de uma Aplicação Web incluída na aplicação da amostra.

| **TollId** | **Hora da Saída** | **Placa de Licença** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Aqui está uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| TollID |O ID da portagem que identifica exclusivamente uma portagem |
| Hora da Saída |A data e hora de saída do veículo da portagem na UTC |
| Placa de Licença |O número da matrícula do veículo |

### <a name="commercial-vehicle-registration-data"></a>Dados de registo de veículos comerciais
A solução utiliza uma imagem estática de uma base de dados de registo de veículos comerciais. Estes dados são guardados como um ficheiro JSON no armazenamento de bolhas Azure, incluídos na amostra.

| Placa de Licença | RegistroD | Fora do prazo |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Aqui está uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| Placa de Licença |O número da matrícula do veículo |
| RegistroD |ID de matrícula do veículo |
| Fora do prazo |O estado de matrícula do veículo: 0 se a matrícula do veículo estiver ativa, 1 se a matrícula for caducada |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurar o ambiente para a Azure Stream Analytics
Para completar esta solução, precisa de uma subscrição do Microsoft Azure. Se não tiver uma conta Azure, pode [solicitar uma versão de teste gratuita](https://azure.microsoft.com/pricing/free-trial/).

Certifique-se de seguir os passos na secção "Limpe a sua conta Azure" no final deste artigo para que possa fazer o melhor uso do seu crédito Azure.

## <a name="deploy-the-sample"></a>Implementar a amostra
Existem vários recursos que podem ser facilmente implantados num grupo de recursos juntamente com alguns cliques. A definição de solução está hospedada no repositório GitHub em [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp) .

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Implemente o modelo TollApp no portal Azure
1. Para implementar o ambiente TollApp para Azure, utilize este link para implementar o [Modelo de Azure do TollApp.](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json)

2. Inscreva-se no portal Azure se solicitado.

3. Escolha a subscrição na qual os vários recursos são faturados.

4. Especificar um novo grupo de recursos, com um nome único, por `MyTollBooth` exemplo.

5. Selecione uma localização do Azure.

6. Especifique um **intervalo** como um número de segundos. Este valor é utilizado na aplicação web da amostra, para a frequência com que envia dados para o Event Hub.

7. **Verifique** para concordar com os termos e condições.

8. Selecione **Pin para painel de instrumentos** para que possa localizar facilmente os recursos mais tarde.

9. Selecione **Comprar** para implementar o modelo de amostra.

10. Após alguns momentos, uma notificação parece confirmar que a **Implementação foi bem sucedida**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Reveja os recursos do Azure Stream Analytics TollApp

1. Iniciar sessão no portal do Azure

2. Localize o Grupo de Recursos que nomeou na secção anterior.

3. Verifique se os seguintes recursos estão listados no grupo de recursos:
   - Uma conta DB cosmos
   - Um trabalho de Azure Stream Analytics Job
   - Uma conta de armazenamento Azure
   - Um hub de eventos Azure
   - Duas aplicações web

## <a name="examine-the-sample-tollapp-job"></a>Examine a amostra do trabalho do TollApp
1. Partindo do grupo de recursos na secção anterior, selecione o trabalho de streaming Stream Analytics a começar pelo número **de nomes tollapp** (o nome contém caracteres aleatórios para singularidade).

2. Na página **geral** do trabalho, note a caixa **de consulta** para ver a sintaxe de consulta.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Parafraseando a intenção da consulta, digamos que é preciso contar o número de veículos que entram numa portagem. Como uma cabine de portagem rodoviária tem um fluxo contínuo de veículos entrando, estes são eventos de entrada são análogos a um riacho que nunca para. Para quantificar o fluxo, é preciso definir um "período de tempo" para medir. Vamos refinar a pergunta mais: "Quantos veículos entram numa portagem a cada três minutos?" Isto é comumente referido como a contagem de tumbling.

   Como pode ver, o Azure Stream Analytics usa uma linguagem de consulta que é como o SQL e adiciona algumas extensões para especificar aspetos relacionados com o tempo da consulta.  Para mais detalhes, leia sobre [a Gestão do Tempo](/stream-analytics-query/time-management-azure-stream-analytics) e construções de [janelas](/stream-analytics-query/windowing-azure-stream-analytics) utilizadas na consulta.

3. Examine as entradas do trabalho de amostragem do TollApp. Apenas a entrada ''s EntryStream' é utilizada na consulta atual.
   - A entrada **EntryStream** é uma ligação Event Hub que faz filas de dados que representam cada vez que um carro entra numa portagem na autoestrada. Uma aplicação web que faz parte da amostra está a criar os eventos, e esses dados estão em fila neste Centro de Eventos. Note que esta entrada é consultada na cláusula FROM da consulta de streaming.
   - A entrada **ExitStream** é uma ligação Event Hub que faz filas de dados que representam cada vez que um carro sai de uma portagem na autoestrada. Esta entrada de streaming é utilizada em variações posteriores da sintaxe de consulta.
   - **A** entrada de registo é uma ligação de armazenamento Azure Blob, que aponta para uma registration.jsestática no ficheiro, utilizada para procurar, conforme necessário. Esta entrada de dados de referência é utilizada em variações posteriores da sintaxe de consulta.

4. Examine as saídas do trabalho de amostragem do TollApp.
   - **Cosmos DB** output é um recipiente de base de dados Cosmos que recebe os eventos de sumidouro de saída. Note que esta saída é utilizada na cláusula INTO da consulta de streaming.

## <a name="start-the-tollapp-streaming-job"></a>Inicie o trabalho de streaming do TollApp
Siga estes passos para iniciar o trabalho de streaming:

1. Na página **geral** do trabalho, selecione **Iniciar**.

2. No painel **de trabalho Iniciar,** selecione **Agora**.

3. Após alguns momentos, uma vez que o trabalho está em execução, na página **geral** do trabalho de streaming, veja o gráfico **de monitorização.** O gráfico deve mostrar vários milhares de eventos de entrada, e dezenas de eventos de saída.

## <a name="review-the-cosmosdb-output-data"></a>Reveja os dados de saída do CosmosDB
1. Localize o grupo de recursos que contém os recursos do TollApp.

2. Selecione a Conta DB Azure Cosmos com o nome padrão **tollapp \<random\> -cosmos**.

3. Selecione o título **do Data Explorer** para abrir a página Data Explorer.

4. Expandir as **portagensAppDatabase**  >  **TollAppCollection**  >  **Documents**.

5. Na lista de ids, vários docs são mostrados uma vez que a saída está disponível.

6. Selecione cada id para rever o documento JSON. Reparem em cada portagem, hora de fim de janela, e a contagem de carros daquela janela.

7. Após mais três minutos, outro conjunto de quatro documentos está disponível, um documento por portagem.


## <a name="report-total-time-for-each-car"></a>Reportar o tempo total para cada carro
O tempo médio necessário para que um carro passe pela portagem ajuda a avaliar a eficiência do processo e a experiência do cliente.

Para encontrar o tempo total, junte-se ao stream 'Tempo de Entrada' com o fluxo ExitTime. Junte-se aos dois fluxos de entrada nas colunas TollId e LicensePlate iguais. O operador **JOIN** requer que especifique a margem de manobra temporal que descreve a diferença de tempo aceitável entre os eventos associados. Utilize a função **DATEDIFF** para especificar que os eventos não devem estar a mais de 15 minutos uns dos outros. Aplicar também a função **DATEDIFF** aos tempos de saída e de entrada para calcular o tempo real que um carro passa na estação de portagem. Note a diferença da utilização de **DATEDIFF** quando é usado numa declaração **SELECT** em vez de uma condição **DE JOIN.**

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Para atualizar a sintaxe de trabalho de streaming do TollApp:

1. Na página **geral** do trabalho, selecione **Stop**.

2. Espere alguns momentos para a notificação de que o trabalho parou.

3. No título DE TOPOLOGIA DO JOB, selecione **< > Consulta**

4. Cole a consulta SQL de streaming ajustada.

5. **Selecione Guardar** para guardar a consulta. Confirme **Sim** para guardar as alterações.

6. Na página **geral** do trabalho, selecione **Iniciar**.

7. No painel **de trabalho Iniciar,** selecione **Agora**.

### <a name="review-the-total-time-in-the-output"></a>Reveja o tempo total na saída
Repita os passos na secção anterior para rever os dados de saída do CosmosDB a partir do trabalho de streaming. Reveja os documentos mais recentes da JSON.

Por exemplo, este documento mostra um carro de exemplo com uma determinada placa, o tempo de entrada e o tempo de saída, e o campo de duração calculada da DATAIFF indicando a duração da portagem como dois minutos:
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

## <a name="report-vehicles-with-expired-registration"></a>Reportar veículos com matrícula caducada
O Azure Stream Analytics pode usar instantâneos estáticos de dados de referência para se juntar aos fluxos de dados temporais. Para demonstrar esta capacidade, utilize a seguinte pergunta da amostra. A entrada Registo é um ficheiro estático blob json que lista as expirações das etiquetas de licença. Ao juntar-se à placa, os dados de referência são comparados com cada veículo que passa pela portagem.

Se um veículo comercial estiver registado na empresa de portagem, pode passar pela portagem sem ser parado para inspeção. Utilize a tabela de procuração de matrícula para identificar todos os veículos comerciais que tenham caducado as matrículas.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Repita os passos na secção anterior para atualizar a sintaxe de trabalho de streaming do TollApp.

2. Repita os passos na secção anterior para rever os dados de saída do CosmosDB a partir do trabalho de streaming.

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

## <a name="scale-out-the-job"></a>Escale o trabalho
O Azure Stream Analytics foi concebido para escalar elasticamente para que possa lidar com grandes volumes de dados. A consulta Azure Stream Analytics pode usar uma cláusula **PARTITION BY** para dizer ao sistema que este passo se escala. **PartitionId** é uma coluna especial que o sistema adiciona para corresponder ao ID de partição da entrada (centro de eventos).

Para escalonar a consulta às divisórias, edite a sintaxe de consulta para o seguinte código:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Para aumentar o trabalho de streaming para mais unidades de streaming:

1. **Pare** o trabalho atual.

2. Atualize a sintaxe de consulta na página **< > Consulta** e guarde as alterações.

3. Sob o título CONFIGURE no trabalho de streaming, selecione **Scale**.

4. Deslize o deslizador das **unidades** de streaming de 1 para 6. As unidades de streaming definem a quantidade de poder de computação que o trabalho pode receber. Selecione **Save** (Guardar).

5. **Inicie** o trabalho de streaming para demonstrar a escala adicional. O Azure Stream Analytics distribui o trabalho através de mais recursos computacional e consegue uma melhor produção, dividindo o trabalho através dos recursos utilizando a coluna designada na cláusula PARTITION BY.

## <a name="monitor-the-job"></a>Monitorizar a tarefa
A área **monitor** contém estatísticas sobre o trabalho em execução. A primeira configuração é necessária para utilizar a conta de armazenamento na mesma região (portagem de nome como o resto deste documento).

![Monitorização de trabalho do Azure Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Também pode aceder **aos Registos** de Atividade a partir da área de **Definições** do painel de trabalho.

## <a name="clean-up-the-tollapp-resources"></a>Limpar os recursos do TollApp
1. Pare o trabalho de Stream Analytics no portal Azure.

2. Localize o grupo de recursos que contém oito recursos relacionados com o modelo TollApp.

3. Selecione **Eliminar grupo de recursos**. Digite o nome do grupo de recursos para confirmar a eliminação.

## <a name="conclusion"></a>Conclusão
Esta solução apresentou-o ao serviço Azure Stream Analytics. Demonstrou como configurar entradas e saídas para o trabalho stream Analytics. Utilizando o cenário de Dados de Portagem, a solução explicou tipos comuns de problemas que surgem no espaço de dados em movimento e como podem ser resolvidos com consultas simples semelhantes a SQL no Azure Stream Analytics. A solução descreveu as construções de extensão SQL para trabalhar com dados temporais. Mostrou como juntar fluxos de dados, como enriquecer o fluxo de dados com dados de referência estáticos, e como escalar uma consulta para alcançar uma produção mais elevada.

Embora esta solução proporcione uma boa introdução, não está completa por qualquer meio. Pode encontrar mais padrões de consulta utilizando a língua SAQL em [exemplos de consulta para padrões comuns de utilização do Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
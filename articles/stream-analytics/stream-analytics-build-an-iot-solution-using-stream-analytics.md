---
title: Construa uma solução IoT utilizando o Azure Stream Analytics
description: Iniciando tutorial para a solução Stream Analytics IoT de um cenário de portagem
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f506cc526a824d45ae2d6b7a75e1c1a99dae4d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426448"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Construa uma solução IoT utilizando o Stream Analytics

## <a name="introduction"></a>Introdução
Nesta solução, aprende-se a utilizar o Azure Stream Analytics para obter informações em tempo real dos seus dados. Os desenvolvedores podem facilmente combinar fluxos de dados, tais como fluxos de cliques, registos e eventos gerados por dispositivos, com registos históricos ou dados de referência para obter insights de negócio. Como um serviço de computação de fluxo em tempo real totalmente gerido e hospedado no Microsoft Azure, o Azure Stream Analytics fornece resiliência incorporada, baixa latência e escalabilidade para o pôr a funcionar em minutos.

Depois de completar esta solução, é capaz de:

* Familiarize-se com o portal Azure Stream Analytics.
* Configure e implante um trabalho de streaming.
* Articular problemas do mundo real e resolvê-los usando a linguagem de consulta Stream Analytics.
* Desenvolva soluções de streaming para os seus clientes utilizando o Stream Analytics com confiança.
* Utilize a experiência de monitorização e registo para resolver problemas.

## <a name="prerequisites"></a>Pré-requisitos
Precisa dos seguintes pré-requisitos para completar esta solução:
* Uma [subscrição azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Introdução do cenário: "Olá, Portagem!"
Uma estação de portagens é um fenómeno comum. Encontra-se em muitas vias rápidas, pontes e túneis por todo o mundo. Cada estação de portagens tem várias portagens. Nas cabines manuais, paras para pagar a portagem a um empregado. Em cabines automatizadas, um sensor em cima de cada cabine digitaliza um cartão RFID que é afixado no para-brisas do seu veículo enquanto passa pela cabine de portagem. É fácil visualizar a passagem de veículos através destas estações de portagem como um fluxo de eventos sobre o qual podem ser realizadas operações interessantes.

![Foto de carros em portagens](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Dados de entrada
Esta solução funciona com dois fluxos de dados. Os sensores instalados na entrada e saída das estações de portagem produzem o primeiro fluxo. O segundo fluxo é um conjunto de dados estático de procura que tem dados de registo do veículo.

### <a name="entry-data-stream"></a>Fluxo de dados de entrada
O fluxo de dados de entrada contém informações sobre carros à medida que entram nas estações de portagem. Os eventos de dados de saída são transmitidos em direto para uma fila do Event Hub a partir de uma Aplicação Web incluída na aplicação de amostras.

| Portagem | Hora de Entrada | Placa de matrícula | Estado | Fabrico | Modelo | Tipo de Veículo | Peso do veículo | Portagem | Etiqueta |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Rio Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Rio Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Rio Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Aqui está uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| Portagem |A identificação da portagem que identifica exclusivamente uma portagem |
| Hora de Entrada |Data e hora de entrada do veículo na portagem da UTC |
| Placa de matrícula |O número da matrícula do veículo |
| Estado |Um estado nos Estados Unidos |
| Fabrico |O fabricante do automóvel |
| Modelo |O número modelo do automóvel |
| Tipo de Veículo |1 para veículos de passageiros ou 2 para veículos comerciais |
| Tipo de Peso |Peso do veículo em toneladas; 0 para veículos de passageiros |
| Portagem |O valor das portagens em USD |
| Etiqueta |O e-Tag no automóvel que automatiza o pagamento; em branco onde o pagamento foi feito manualmente |

### <a name="exit-data-stream"></a>Fluxo de dados de saída
O fluxo de dados de saída contém informações sobre carros que saem da estação de pedágio. Os eventos de dados de saída são transmitidos em direto para uma fila do Event Hub a partir de uma Aplicação Web incluída na aplicação de amostras.

| **Portagem** | **Hora de Saída** | **Placa de matrícula** |
| --- | --- | --- |
| 1 |2014-09-10t12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10t12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10t12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10t12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10t12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10t12:07:00.0000000Z |CDE 1007 |

Aqui está uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| Portagem |A identificação da portagem que identifica exclusivamente uma portagem |
| Hora de Saída |A data e hora de saída do veículo da portagem na UTC |
| Placa de matrícula |O número da matrícula do veículo |

### <a name="commercial-vehicle-registration-data"></a>Dados de registo de veículos comerciais
A solução utiliza uma imagem estática de uma base de dados de registo de veículos comerciais. Estes dados são guardados como um ficheiro JSON no armazenamento de blob Azure, incluído na amostra.

| Placa de matrícula | RegistroId | Fora do prazo |
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
| Placa de matrícula |O número da matrícula do veículo |
| RegistroId |Identificação de matrícula do veículo |
| Fora do prazo |O estado de matrícula do veículo: 0 se a matrícula do veículo estiver ativa, 1 se a matrícula for vencida |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurar o ambiente para azure stream analytics
Para completar esta solução, precisa de uma subscrição do Microsoft Azure. Se não tiver uma conta Azure, pode [solicitar uma versão de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)

Certifique-se de seguir os passos na secção "Limpar a sua conta Azure" no final deste artigo para que possa fazer o melhor uso do seu crédito Azure.

## <a name="deploy-the-sample"></a>Implementar a amostra
Existem vários recursos que podem ser facilmente implantados num grupo de recursos juntamente com alguns cliques. A definição de solução está alojada no [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp)repositório GitHub em .

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Implementar o modelo TollApp no portal Azure
1. Para implementar o ambiente TollApp para o Azure, utilize este link para implementar o [modelo TollApp Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Inscreva-se no portal Azure, se solicitado.

3. Escolha a subscrição na qual os vários recursos são cobrados.

4. Especifique um novo grupo de `MyTollBooth`recursos, com um nome único, por exemplo.

5. Selecione uma localização do Azure.

6. Especifique um **intervalo** como um número de segundos. Este valor é utilizado na aplicação web da amostra, para a frequência de enviar dados para o Event Hub.

7. **Verifique** se concorda com os termos e condições.

8. Selecione **Pin para dashboard** para que possa localizar facilmente os recursos mais tarde.

9. **Selecione Comprar** para implementar o modelo de amostra.

10. Após alguns momentos, uma notificação parece confirmar que o **Destacamento foi bem sucedido**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Reveja os recursos da TollApp da Análise de Fluxo saqueado do Azure Stream

1. Iniciar sessão no portal do Azure

2. Localize o Grupo de Recursos que nomeou na secção anterior.

3. Verifique se os seguintes recursos estão listados no grupo de recursos:
   - Uma conta Cosmos DB
   - One Azure Stream Analytics Job
   - Conta de armazenamento Azure
   - Um Hub de Eventos Azure
   - Duas aplicações web

## <a name="examine-the-sample-tollapp-job"></a>Examine o trabalho da amostra da TollApp
1. Partindo do grupo de recursos na secção anterior, selecione o trabalho de streaming Stream Analytics a partir do nome **tollapp** (o nome contém caracteres aleatórios para singularidade).

2. Na página **geral** do trabalho, note na caixa **de consulta** para ver a sintaxe de consulta.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Parafraseando a intenção da consulta, digamos que é preciso contar o número de veículos que entram numa portagem. Como uma portagem rodoviária tem um fluxo contínuo de veículos entrando, estes são eventos de entrada são análogos a um riacho que nunca para. Para quantificar o fluxo, é preciso definir um "período de tempo" para medir. Vamos refinar ainda mais a pergunta, para : "Quantos veículos entram numa portagem a cada três minutos?" Isto é comumente referido como a contagem de tropeçar.

   Como pode ver, o Azure Stream Analytics usa uma linguagem de consulta que é como a SQL e adiciona algumas extensões para especificar aspetos relacionados com o tempo da consulta.  Para mais detalhes, leia sobre as construções de [Gestão](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) do Tempo e [Janelas](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) utilizadas na consulta.

3. Examine as inputs do trabalho de amostra gema. Apenas a entrada EntryStream é utilizada na consulta atual.
   - A entrada **EntryStream** é uma ligação Do Event Hub que faz fila de dados que representam cada vez que um carro entra numa portagem na autoestrada. Uma aplicação web que faz parte da amostra está a criar os eventos, e que os dados estão em fila neste Hub de Eventos. Note que esta entrada é questionada na cláusula FROM da consulta de streaming.
   - A entrada **ExitStream** é uma ligação Do Event Hub que faz fila de dados que representam cada vez que um carro sai de uma portagem na autoestrada. Esta entrada de streaming é utilizada em variações posteriores da sintaxe de consulta.
   - **A** entrada de registo é uma ligação de armazenamento Azure Blob, apontando para um ficheiro estático registration.json, usado para procuras conforme necessário. Esta entrada de dados de referência é utilizada em variações posteriores da sintaxe de consulta.

4. Examine as saídas do trabalho da amostra da TollApp.
   - A saída **cosmos DB** é um recipiente de base de dados cosmos que recebe os eventos de sumidouro de saída. Note que esta saída é utilizada na cláusula INTO da consulta de streaming.

## <a name="start-the-tollapp-streaming-job"></a>Inicie o trabalho de streaming da TollApp
Siga estes passos para iniciar o trabalho de streaming:

1. Na página **de visão geral** do trabalho, selecione **Iniciar**.

2. No painel **de trabalho iniciar,** selecione **Agora**.

3. Após alguns momentos, uma vez que o trabalho esteja em execução, na página **geral** do trabalho de streaming, veja o gráfico **de Monitorização.** O gráfico deve mostrar vários milhares de eventos de entrada e dezenas de eventos de saída.

## <a name="review-the-cosmosdb-output-data"></a>Reveja os dados de saída do CosmosDB
1. Localize o grupo de recursos que contém os recursos da TollApp.

2. Selecione a Conta DB Azure Cosmos com o padrão de nome **tollapp\<random\>-cosmos**.

3. Selecione o data **explorer** que vai abrir a página do Data Explorer.

4. Expandir a **portagemAppDatabase** > **portagemAppCollection** > **Documents**.

5. Na lista de ids, vários docs são mostrados uma vez que a saída está disponível.

6. Selecione cada id para rever o documento JSON. Reparem em cada portagem, tempo de janela e a contagem de carros daquela janela.

7. Após mais três minutos, outro conjunto de quatro documentos está disponível, um documento por portagem.


## <a name="report-total-time-for-each-car"></a>Reportar o tempo total para cada carro
O tempo médio necessário para que um carro passe pela portagem ajuda a avaliar a eficiência do processo e a experiência do cliente.

Para encontrar o tempo total, junte-se ao fluxo 'EntradaTempo' com o fluxo ExitTime. Junte-se aos dois fluxos de entrada nas colunas TollId e LicencePlate correspondentes. O operador **JOIN** exige que especifique a margem temporal que descreve a diferença de tempo aceitável entre os eventos aquecidos. Utilize a função **DATEDIFF** para especificar que os eventos não devem estar a mais de 15 minutos um do outro. Aplique também a função **DATEDIFF** nos horários de saída e entrada para calcular o tempo real que um carro passa na estação de portagens. Note a diferença de utilização do **DATEDIFF** quando é utilizado numa declaração **SELECT** em vez de uma condição **de JOIN.**

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Para atualizar a sintaxe de consulta de trabalho em streaming da TollApp:

1. Na página **de visão geral** do trabalho, selecione **Stop**.

2. Espere alguns momentos para a notificação de que o trabalho parou.

3. Sob a rubrica JOB TOPOLOGY, selecione **< > Consulta**

4. Colhe a consulta SQL de streaming ajustada.

5. Selecione **Guardar** para guardar a consulta. Confirme **sim** para salvar as alterações.

6. Na página **de visão geral** do trabalho, selecione **Iniciar**.

7. No painel **de trabalho iniciar,** selecione **Agora**.

### <a name="review-the-total-time-in-the-output"></a>Reveja o tempo total na saída
Repita os passos na secção anterior para rever os dados de saída da CosmosDB a partir do trabalho de streaming. Reveja os últimos documentos da JSON.

Por exemplo, este documento mostra um carro exemplo com uma determinada matrícula, o tempo de entrada e saída, e o campo de duração calculado DATEDIFF mostrando a duração da portagem como dois minutos:
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

## <a name="report-vehicles-with-expired-registration"></a>Reportar veículos com matrícula vencida
O Azure Stream Analytics pode utilizar instantâneos estáticos de dados de referência para se juntar a fluxos de dados temporais. Para demonstrar esta capacidade, utilize a seguinte questão da amostra. A entrada de registo é um ficheiro json de blob estático que lista as caducidade das etiquetas de licença. Ao juntar-se à placa, os dados de referência são comparados a cada veículo que passa pela portagem.

Se um veículo comercial estiver registado na empresa de portagens, pode passar pela portagem sem ser parado para inspeção. Utilize a tabela de procura de registo para identificar todos os veículos comerciais que tenham caducidade nas matrículas.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Repita os passos na secção anterior para atualizar a sintaxe de consulta de trabalho de streaming da TollApp.

2. Repita os passos na secção anterior para rever os dados de saída da CosmosDB a partir do trabalho de streaming.

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

## <a name="scale-out-the-job"></a>Esforce o trabalho
O Azure Stream Analytics foi concebido para escalar elástico para que possa lidar com grandes volumes de dados. A consulta Azure Stream Analytics pode usar uma cláusula **PARTITION BY** para dizer ao sistema que este passo se esescala. **PartitionId** é uma coluna especial que o sistema adiciona para combinar com o ID de partição da entrada (centro de eventos).

Para escalar a consulta às divisórias, edite a sintaxe de consulta para o seguinte código:
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

3. Sob a direção configure no trabalho de streaming, **selecione Escala**.

4. Deslize o slider das **unidades** de streaming de 1 a 6. As unidades de streaming definem a quantidade de poder computacional que o trabalho pode receber. Selecione **Guardar**.

5. **Inicie** o trabalho de streaming para demonstrar a escala adicional. O Azure Stream Analytics distribui o trabalho através de mais recursos computacionais e consegue um melhor contributo, dividindo o trabalho através dos recursos utilizando a coluna designada na cláusula PARTITION BY.

## <a name="monitor-the-job"></a>Monitorizar a tarefa
A área **monitor** contém estatísticas sobre o trabalho em execução. A configuração pela primeira vez é necessária para usar a conta de armazenamento na mesma região (portagem de nome como o resto deste documento).

![Monitorização de emprego sintetizar Azure Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Também pode aceder aos **Registos** de Atividade a partir da área de **definições** do painel de trabalho.

## <a name="clean-up-the-tollapp-resources"></a>Limpar os recursos da TollApp
1. Pare o trabalho de Stream Analytics no portal Azure.

2. Localize o grupo de recursos que contém oito recursos relacionados com o modelo tollApp.

3. Selecione **Eliminar grupo de recursos**. Digite o nome do grupo de recursos para confirmar a eliminação.

## <a name="conclusion"></a>Conclusão
Esta solução apresentou-o ao serviço Azure Stream Analytics. Demonstrou como configurar as inputs e saídas para o trabalho de Stream Analytics. Utilizando o cenário de Dados de Portagem, a solução explicou tipos comuns de problemas que surgem no espaço dos dados em movimento e como podem ser resolvidos com simples consultas semelhantes a SQL no Azure Stream Analytics. A solução descreveu as construções de extensão SQL para trabalhar com dados temporais. Mostrou como juntar fluxos de dados, como enriquecer o fluxo de dados com dados estáticos de referência e como escalar uma consulta para obter uma maior entrada.

Embora esta solução proporcione uma boa introdução, não está completa por qualquer meio. Você pode encontrar mais padrões de consulta usando o idioma SAQL em [exemplos de Query para padrões comuns](stream-analytics-stream-analytics-query-patterns.md)de utilização de Stream Analytics .

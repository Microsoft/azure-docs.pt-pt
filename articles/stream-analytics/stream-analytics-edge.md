---
title: Azure Stream Analytics do IoT Edge
description: Criar tarefas do edge no Azure Stream Analytics e implementá-las em dispositivos com o Azure IoT Edge.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: c64bf11a5e0d95e2896bb717d4069f9b0d7ea721
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569999"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics do IoT Edge
 
O Azure Stream Analytics (ASA) do IoT Edge permite aos programadores implementar intelligence análise quase em tempo real mais próxima dos dispositivos IoT para que possam desbloquear todo o valor dos dados gerados pelo dispositivo. O Azure Stream Analytics foi concebido para baixa latência, resiliência, utilização eficiente da largura de banda e conformidade. As empresas agora podem implementar a lógica de controle de perto as operações industriais e complementar feito na cloud de análise de macrodados.  

O Azure Stream Analytics do IoT Edge é executado dentro de [do Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) framework. Depois de criar a tarefa no ASA, pode implementar e geri-lo através do IoT Hub.

## <a name="scenarios"></a>Cenários
![Diagrama de alto nível do IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Comando de baixa latência e controlo**: Por exemplo, sistemas de segurança de fabrico tem de responder aos dados operacionais com latência ultrabaixa. Com o ASA do IoT Edge, pode analisar o sensor, dados em tempo quase real e emita comandos quando detetar anomalias para parar uma máquina ou acionar alertas.
*   **Conectividade para a cloud limitada**: Missão sistemas críticos, como o equipamento de extração remoto, vessels ligados ou a desagregação no exterior, tem de analisar e reagir a dados, mesmo quando a conectividade da cloud é intermitente. Com o ASA, sua lógica de transmissão em fluxo é executado independentemente da conectividade de rede e pode escolher o que envia para a cloud, para processamento adicional ou de armazenamento.
* **Largura de banda limitada**: O volume de dados produzidos pelos mecanismos de jet ou veículos podem ser tão grandes que dados tem de ser filtrados ou previamente processados antes de os enviar para a cloud. Utilizar o ASA, pode filtrar ou agregar os dados que precisam ser enviada para a cloud.
* **Conformidade**: Conformidade a normas pode requerer alguns dados tornados anónimos localmente ou agregadas antes de serem enviados para a cloud.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Tarefas do Edge no Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>O que é uma tarefa de "edge"?

As tarefas do ASA Edge executadas em contentores implementados [dispositivos Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Eles são compostos de duas partes:
1.  Uma parte de cloud que é responsável pela definição de tarefa: os utilizadores definir entradas, saída, consulta e outras definições (eventos fora de ordem, etc.) na cloud.
2.  Um módulo em execução nos seus dispositivos IoT. Ele contém o mecanismo ASA e recebe a definição de tarefa a partir da cloud. 

ASA utiliza o IoT Hub para implementar as tarefas do edge no dispositivo (s). Obter mais informações sobre [implementação do IoT Edge pode ser vista aqui](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Tarefa do Edge do Stream Analytics do Azure](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Instruções de instalação
As etapas de alto nível são descritas na tabela seguinte. São fornecidos mais detalhes nas seções a seguir.
|      |Passo   | Notas   |
| ---   | ---   |  ---      |
| 1   | **Criar um contentor de armazenamento**   | Contentores de armazenamento são utilizadas para guardar a definição de tarefa onde podem ser acedidos pelos seus dispositivos IoT. <br>  Pode reutilizar qualquer contentor de armazenamento existente.     |
| 2   | **Criar uma tarefa do edge ASA**   |  Criar uma nova tarefa, selecione **Edge** como **ambiente de alojamento**. <br> Estas tarefas são criados/geridos a partir da cloud e executar nos seus próprios dispositivos IoT Edge.     |
| 3   | **Configurar o ambiente do IoT Edge no seu dispositivo (s)**   | Instruções para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Implementar o ASA no seu dispositivo ou dispositivos do IoT Edge**   |  Definição de tarefa ASA é exportada para o contentor de armazenamento que criou anteriormente.       |
Pode seguir [esse tutorial passo a passo](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) para implementar a sua primeira tarefa ASA do IoT Edge. O vídeo seguinte deve ajudá-lo a compreender o processo para executar uma tarefa de Stream Analytics num dispositivo IoT edge:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
Um contentor de armazenamento é necessário para exportar a consulta do ASA compilado e a configuração da tarefa. É utilizado para configurar a imagem do ASA Docker com a sua consulta específica. 
1. Siga [estas instruções](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para criar uma conta de armazenamento a partir do portal do Azure. Pode manter todas as opções predefinidas para utilizar esta conta com ASA.
2. A conta de armazenamento recentemente criada, crie um contentor de armazenamento de BLOBs:
    1. Clique em **Blobs**, em seguida, **+ contentor**. 
    2. Introduza um nome e manter o contentor como **privada**.

#### <a name="create-an-asa-edge-job"></a>Criar uma tarefa do ASA Edge
> [!Note]
> Este tutorial concentra-se na criação da tarefa ASA através do portal do Azure. Também pode [utilizar plug-in do Visual Studio para criar uma tarefa do ASA Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. A partir do portal do Azure, crie uma nova "tarefa de Stream Analytics". [Ligação direta para criar uma nova tarefa ASA aqui](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. No ecrã de criação, selecione **Edge** como **ambiente de alojamento** (veja a imagem seguinte)

   ![Criar tarefa do Stream Analytics do Edge](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definição de tarefa
    1. **Definir a entrada Stream(s)**. Defina um ou vários fluxos de entrada para a sua tarefa.
    2. Defina os dados de referência (opcionais).
    3. **Definir a saída Stream(s)**. Defina um ou vários fluxos de saídas para a sua tarefa. 
    4. **Definir consulta**. Defina a consulta do ASA na cloud com o editor de inline. O compilador verifica automaticamente a sintaxe ativada para o ASA edge. Também pode testar a consulta através do carregamento de dados de exemplo. 

4. Definir as informações de contentor de armazenamento na **definições do IoT Edge** menu.

5. Definir definições opcionais
    1. **Ordenação de eventos**. Pode configurar a política de fora de ordem no portal. A documentação está disponível [aqui](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Localidade**. Defina o formato de internacionalização.



> [!Note]
> Quando é criada uma implementação, o ASA exporta a definição de tarefa para um contentor de armazenamento. Esta definição de tarefa permanecer o mesmo durante a duração de uma implementação. Como conseqüência, se quiser atualizar uma tarefa em execução na borda, terá de editar a tarefa no ASA e, em seguida, crie uma nova implementação do IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configurar o ambiente do IoT Edge no seu dispositivo (s)
As tarefas do Edge podem ser implementadas em dispositivos com o Azure IoT Edge.
Para isso, tem de seguir estes passos:
- Crie um Hub Iot.
- Instale o tempo de execução do Docker e do IoT Edge nos seus dispositivos periféricos.
- Definir os seus dispositivos como **dispositivos IoT Edge** no IoT Hub.

Estes passos são descritos na documentação do IoT Edge para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implementação do ASA no seu dispositivo ou dispositivos do IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Adicionar o ASA à sua implementação
- No portal do Azure, abra o IoT Hub, navegue para **IoT Edge** e clique no dispositivo que pretende direcionar para esta implementação.
- Selecione **definir módulos**, em seguida, selecione **+ adicionar** e escolha **módulo do Azure Stream Analytics**.
- Selecione a subscrição e a tarefa de ASA Edge que criou. Clique em Guardar.
![Adicionar módulo do ASA na sua implementação](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Durante este passo, o ASA cria uma pasta denominada "EdgeJobs" no contentor de armazenamento (se não existir já). Para cada implementação, uma nova subpasta é criada na pasta "EdgeJobs".
> Para implementar o seu trabalho em dispositivos periféricos, ASA cria uma assinatura de acesso partilhado (SAS) para o ficheiro de definição de tarefa. A chave SAS com segurança é transmitida para os dispositivos de IoT Edge com o dispositivo duplo. A expiração desta chave é de três anos a partir do dia da criação.


Para obter mais informações sobre implementações do IoT Edge, veja a [esta página](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Configurar as rotas
IoT Edge fornece uma forma de forma declarativa a rotear mensagens entre módulos e entre módulos e o IoT Hub. A sintaxe completa é descrita [aqui](https://docs.microsoft.com/azure/iot-edge/module-composition).
Nomes das entradas e saídas criadas na tarefa ASA podem ser utilizados como pontos finais para o encaminhamento.  

###### <a name="example"></a>Exemplo

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
Este exemplo mostra as rotas para o cenário descrito na imagem seguinte. Contém uma tarefa do edge chamada "**ASA**", com uma entrada com o nome "**temperatura**"e uma saída com o nome"**alerta**".
![Exemplo de diagrama de encaminhamento de mensagens](media/stream-analytics-edge/edge-message-routing-example.png)

Este exemplo define as rotas seguintes:
- Cada mensagem a partir do **tempSensor** é enviado para o módulo com o nome **ASA** para a entrada com o nome **temperatura**,
- Todos os resultados da **ASA** módulo são enviados para o IoT Hub ligado a este dispositivo (a montante$),
- Todos os resultados da **ASA** módulo são enviados para o **controle** ponto final da **tempSensor**.


## <a name="technical-information"></a>Informações técnicas
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Limitações atuais para as tarefas de IoT Edge em comparação comparadas tarefas na cloud
O objetivo é ter paridade entre as tarefas do IoT Edge e tarefas na cloud. A maioria dos recursos de linguagem de consulta SQL já são suportados.
No entanto as seguintes funcionalidades não são suportadas ainda para tarefas do edge:
* Definido pelo utilizador Udfs (funções) em JavaScript. UDF estão disponíveis no [ C# para as tarefas do IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (pré-visualização).
* Agregações definidas pelo utilizador (UDA).
* Funções do Azure ML
* Utilizar mais de 14 agregados num único passo.
* Formato AVRO para entrada/saída. Neste momento, apenas CSV e JSON são suportadas.
* Os seguintes operadores SQL:
    * Operadores de Geoespacial:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTIÇÃO POR
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Requisitos de hardware e de tempo de execução
Para executar o ASA do IoT Edge, precisa de dispositivos que podem ser executadas [do Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA e utilizar o Azure IoT Edge **Docker** contentores para fornecer uma solução portátil que é executado em vários sistemas operativos de anfitrião (Windows, Linux).

O ASA do IoT Edge é disponibilizado como imagens do Windows e Linux, em execução no x86-64 ou arquiteturas ARM (Advanced máquinas de RISC). 


### <a name="input-and-output"></a>Entrada e saída
#### <a name="input-and-output-streams"></a>Entrada e de fluxos de saída
As tarefas do ASA Edge podem obter entradas e saídas outros módulos em execução em dispositivos do IoT Edge. Para ligar de e para módulos específicos, pode definir a configuração de roteamento no momento da implementação. Obter mais informações são descritas no [documentação de composição do módulo do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Para entradas e saídas, são suportados os formatos CSV e JSON.

Para cada entrada e fluxo de saída cria na sua tarefa ASA, é criado um ponto de final correspondente no seu módulo implementado. Estes pontos finais podem ser utilizados em rotas da sua implementação.

Pelo presente, o único suportado entrada de fluxo e os tipos de saída de fluxo são Hub do Edge. Referência ao tipo de ficheiro de referência de entrada suporta. Outras saídas podem ser contactadas através uma tarefa na cloud de downstream. Por exemplo, uma tarefa do Stream Analytics alojada no Edge envia o resultado para o Hub do Edge, que, em seguida, pode enviar a saída para o IoT Hub. Pode usar uma segunda tarefa do Azure Stream Analytics alojado na cloud com a entrada do IoT Hub e de saída para o Power BI ou outro tipo de saída.



##### <a name="reference-data"></a>Dados de referência
Dados de referência (também conhecido como uma tabela de pesquisa) são um conjunto finito de dados que são estáticos ou lenta alterar por natureza. É utilizado para efetuar uma pesquisa ou para correlacionar com seu fluxo de dados. Para tornar usar dados de referência na sua tarefa do Azure Stream Analytics, geralmente utilizará um [associação de dados de referência](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) na sua consulta. Para obter mais informações, consulte a [usando dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md).

Apenas os dados de referência local são suportados. Quando uma tarefa é implementada em dispositivos do IoT Edge, ele carrega dados de referência do caminho de ficheiro de definidas pelo utilizador.

Para criar uma tarefa com dados de referência do Edge:

1. Crie uma nova entrada para a sua tarefa.

2. Escolher **dados de referência** como o **tipo de origem**.

3. Possui um arquivo de dados referência pronto a no dispositivo. Para um contentor do Windows, colocar o arquivo de dados de referência na unidade local e partilhar o disco local com o contentor do Docker. Para um contentor do Linux, criar um volume do Docker e preencha o ficheiro de dados no volume.

4. Defina o caminho de ficheiro. Para o SO de Host do Windows e um contentor do Windows, utilize o caminho absoluto: `E:\<PathToFile>\v1.csv`. Para um contentor de SO de Host do Windows e Linux ou um SO Linux e contentores de Linux, utilize o caminho do volume: `<VolumeName>/file1.txt`.

![Nova entrada de dados de referência para a tarefa do Azure Stream Analytics do IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Os dados de referência na atualização do IoT Edge são acionados por uma implementação. Quando acionado, o módulo ASA escolhe os dados atualizados sem parar a tarefa em execução.

Existem duas maneiras de atualizar os dados de referência:
* Caminho de dados de referência de atualização na sua tarefa ASA do portal do Azure.
* Atualize a implementação de IoT Edge.

## <a name="license-and-third-party-notices"></a>Licença e avisos de terceiros
* [Licença do Azure Stream Analytics do IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Aviso de terceiros para o Azure Stream Analytics do IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passos Seguintes

* [Obter mais informações sobre o Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA do tutorial do IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Desenvolver trabalhos do Edge do Stream Analytics com ferramentas do Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementar o CI/CD para análise de Stream com APIs](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

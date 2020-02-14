---
title: Azure Stream Analytics no IoT Edge
description: Criar tarefas do edge no Azure Stream Analytics e implementá-las em dispositivos com o Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: 7e4a1cf43931b56cfdb3c31ffe870a07dbaa75af
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201760"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics no IoT Edge
 
O Azure Stream Analytics (ASA) no IoT Edge permite aos programadores implementarem analytical intelligence em tempo quase real mais próxima dos dispositivos IoT para que possam desbloquear o valor total dos dados gerados por dispositivos. O Azure Stream Analytics foi concebido para baixa latência, resiliência, utilização eficiente da largura de banda e conformidade. Agora, as empresas podem implementar a lógica de controlo perto das operações industriais e complementar as análises de macrodados feitas na cloud.  

O Azure Stream Analytics em IoT Edge funciona dentro da estrutura [Azure IoT Edge.](https://azure.microsoft.com/campaigns/iot-edge/) Depois de criar a tarefa no ASA, pode implementar e geri-lo através do IoT Hub.

## <a name="scenarios"></a>Cenários
![Diagrama de alto nível do IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Comando e controlo de baixa latência**: Por exemplo, os sistemas de segurança de fabrico devem responder a dados operacionais com latência ultra-baixa. Com o ASA do IoT Edge, pode analisar o sensor, dados em tempo quase real e emita comandos quando detetar anomalias para parar uma máquina ou acionar alertas.
*   **Conectividade limitada à nuvem**: Os sistemas críticos da missão, tais como equipamentos de mineração remota, embarcações conectadas ou perfuração offshore, precisam analisar e reagir aos dados mesmo quando a conectividade da nuvem é intermitente. Com o ASA, sua lógica de transmissão em fluxo é executado independentemente da conectividade de rede e pode escolher o que envia para a cloud, para processamento adicional ou de armazenamento.
* **Largura de banda limitada**: O volume de dados produzidos por motores a jato ou carros conectados pode ser tão grande que os dados devem ser filtrados ou pré-processados antes de enviá-los para a nuvem. Utilizar o ASA, pode filtrar ou agregar os dados que precisam ser enviada para a cloud.
* **Conformidade**: A conformidade regulamentar pode exigir que alguns dados sejam anoonizados ou agregados localmente antes de serem enviados para a nuvem.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Tarefas do Edge no Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>O que é uma tarefa de "edge"?

Os postos de trabalho da ASA Edge funcionam em contentores implantados para [dispositivos Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Eles são compostos de duas partes:
1.  Uma parte de cloud que é responsável pela definição de tarefa: os utilizadores definir entradas, saída, consulta e outras definições (eventos fora de ordem, etc.) na cloud.
2.  Um módulo em execução nos seus dispositivos IoT. Ele contém o mecanismo ASA e recebe a definição de tarefa a partir da cloud. 

ASA utiliza o IoT Hub para implementar as tarefas do edge no dispositivo (s). Mais informações sobre a implantação de [IoT Edge podem ser vistas aqui](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Tarefa do Edge do Stream Analytics do Azure](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Instruções de instalação
As etapas de alto nível são descritas na tabela seguinte. São fornecidos mais detalhes nas seções a seguir.

|      |Passo   | Notas   |
| ---   | ---   |  ---      |
| 1   | **Criar um recipiente de armazenamento**   | Contentores de armazenamento são utilizadas para guardar a definição de tarefa onde podem ser acedidos pelos seus dispositivos IoT. <br>  Pode reutilizar qualquer contentor de armazenamento existente.     |
| 2   | **Criar um trabalho de borda ASA**   |  Crie um novo emprego, selecione **Edge** como **ambiente de hospedagem.** <br> Estas tarefas são criados/geridos a partir da cloud e executar nos seus próprios dispositivos IoT Edge.     |
| 3   | **Instale o seu ambiente IoT Edge no seu(s) dispositivo(s)**   | Instruções para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Implemente as ASA nos seus dispositivos IoT Edge**   |  Definição de tarefa ASA é exportada para o contentor de armazenamento que criou anteriormente.       |

Pode seguir [este tutorial passo a passo](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) para implementar o seu primeiro trabalho de ASA no IoT Edge. O vídeo seguinte deve ajudá-lo a compreender o processo para executar uma tarefa de Stream Analytics num dispositivo IoT edge:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
Um contentor de armazenamento é necessário para exportar a consulta do ASA compilado e a configuração da tarefa. É utilizado para configurar a imagem do ASA Docker com a sua consulta específica. 
1. Siga [estas instruções](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para criar uma conta de armazenamento a partir do portal Azure. Pode manter todas as opções predefinidas para utilizar esta conta com ASA.
2. A conta de armazenamento recentemente criada, crie um contentor de armazenamento de BLOBs:
    1. Clique em **Blobs,** em seguida **+ Recipiente**. 
    2. Introduza um nome e mantenha o recipiente como **Privado**.

#### <a name="create-an-asa-edge-job"></a>Criar uma tarefa do ASA Edge
> [!Note]
> Este tutorial concentra-se na criação da tarefa ASA através do portal do Azure. Também pode [usar plugin Visual Studio para criar um trabalho ASA Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. A partir do portal do Azure, crie uma nova "tarefa de Stream Analytics". [Link direto para criar um novo trabalho ASA aqui](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. No ecrã de criação, selecione **Edge** como ambiente de **hospedagem** (ver a imagem seguinte)

   ![Criar tarefa do Stream Analytics do Edge](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definição de tarefa
    1. **Definir Fluxo de Entrada(s)** . Defina um ou vários fluxos de entrada para a sua tarefa.
    2. Defina os dados de referência (opcionais).
    3. **Definir fluxo de saída(s)** . Defina um ou vários fluxos de saídas para a sua tarefa. 
    4. **Definir consulta.** Defina a consulta do ASA na cloud com o editor de inline. O compilador verifica automaticamente a sintaxe ativada para o ASA edge. Também pode testar a consulta através do carregamento de dados de exemplo. 

4. Delineie as informações do recipiente de armazenamento no menu de **definições do IoT Edge.**

5. Definir definições opcionais
    1. **Encomenda de eventos.** Pode configurar a política de fora de ordem no portal. A documentação está disponível [aqui.](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)
    2. **Local.** Defina o formato de internacionalização.



> [!Note]
> Quando é criada uma implementação, o ASA exporta a definição de tarefa para um contentor de armazenamento. Esta definição de tarefa permanecer o mesmo durante a duração de uma implementação. Como conseqüência, se quiser atualizar uma tarefa em execução na borda, terá de editar a tarefa no ASA e, em seguida, crie uma nova implementação do IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configurar o ambiente do IoT Edge no seu dispositivo (s)
As tarefas do Edge podem ser implementadas em dispositivos com o Azure IoT Edge.
Para isso, tem de seguir estes passos:
- Crie um Hub Iot.
- Instale o tempo de execução do Docker e do IoT Edge nos seus dispositivos periféricos.
- Desloque os seus dispositivos como **dispositivos IoT Edge** no IoT Hub.

Estes passos são descritos na documentação IoT Edge para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implementação do ASA no seu dispositivo ou dispositivos do IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Adicionar o ASA à sua implementação
- No portal Azure, abra o IoT Hub, navegue até **IoT Edge** e clique no dispositivo que pretende atingir para esta implementação.
- Selecione **módulos set,** em seguida, selecione **+ Adicione** e escolha **módulo de análise de fluxo Azure**.
- Selecione a subscrição e a tarefa de ASA Edge que criou. Clique em Guardar.
![Adicionar módulo ASA na sua](media/stream-analytics-edge/add-stream-analytics-module.png) de implantação


> [!Note]
> Durante este passo, o ASA cria uma pasta denominada "EdgeJobs" no contentor de armazenamento (se não existir já). Para cada implementação, uma nova subpasta é criada na pasta "EdgeJobs".
> Ao implementar o seu trabalho em dispositivos IoT Edge, a ASA cria uma assinatura de acesso partilhado (SAS) para o ficheiro de definição de trabalho. A chave SAS com segurança é transmitida para os dispositivos de IoT Edge com o dispositivo duplo. A expiração desta chave é de três anos a partir do dia da criação. Quando atualizar um trabalho ioT Edge, o SAS mudará, mas a versão de imagem não mudará. Assim que **atualizar,** siga o fluxo de trabalho de implementação e uma notificação de atualização é registada no dispositivo.


Para mais informações sobre as implementações do IoT Edge, consulte [esta página](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Configurar as rotas
IoT Edge fornece uma forma de forma declarativa a rotear mensagens entre módulos e entre módulos e o IoT Hub. A sintaxe completa é descrita [aqui.](https://docs.microsoft.com/azure/iot-edge/module-composition)
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
Este exemplo mostra as rotas para o cenário descrito na imagem seguinte. Contém um trabalho de borda chamado "**ASA**", com uma entrada chamada "**temperatura**" e uma saída chamada "**alerta**".
exemplo do diagrama de ![de](media/stream-analytics-edge/edge-message-routing-example.png) de encaminhamento de mensagens

Este exemplo define as rotas seguintes:
- Cada mensagem do **tempSensor** é enviada para o módulo denominado **ASA** para a temperatura **denominada,**
- Todas as saídas do módulo **ASA** são enviadas para o Hub IoT ligado a este dispositivo ($upstream),
- Todas as saídas do módulo **ASA** são enviadas para o ponto final de **controlo** do **tempSensor**.


## <a name="technical-information"></a>Informações técnicas
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Limitações atuais para as tarefas de IoT Edge em comparação comparadas tarefas na cloud
O objetivo é ter paridade entre as tarefas do IoT Edge e tarefas na cloud. A maioria das funcionalidades de linguagem de consulta SQL são suportadas, permitindo executar a mesma lógica tanto na nuvem como no IoT Edge.
No entanto as seguintes funcionalidades não são suportadas ainda para tarefas do edge:
* Definido pelo utilizador Udfs (funções) em JavaScript. A UDF está disponível [ C# para trabalhos ioT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (pré-visualização).
* Agregações definidas pelo utilizador (UDA).
* Funções do Azure ML.
* Utilizar mais de 14 agregados num único passo.
* Formato AVRO para entrada/saída. Neste momento, apenas CSV e JSON são suportadas.
* Os seguintes operadores SQL:
    * PARTIÇÃO POR
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Requisitos de hardware e de tempo de execução
Para executar as ASA no IoT Edge, precisa de dispositivos que possam executar [O Edge Azure IoT](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA e Azure IoT Edge utilizam os recipientes **Docker** para fornecer uma solução portátil que funciona em vários sistemas operativos hospedeiros (Windows, Linux).

AsA on IoT Edge é disponibilizado como imagens Windows e Linux, funcionando em ambas as arquiteturas x86-64 ou ARM (Advanced RISC Machines). 


### <a name="input-and-output"></a>Entrada e saída
#### <a name="input-and-output-streams"></a>Entrada e de fluxos de saída
As tarefas do ASA Edge podem obter entradas e saídas outros módulos em execução em dispositivos do IoT Edge. Para ligar de e para módulos específicos, pode definir a configuração de roteamento no momento da implementação. Mais informações são descritas na [documentação de composição do módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Para entradas e saídas, são suportados os formatos CSV e JSON.

Para cada entrada e fluxo de saída cria na sua tarefa ASA, é criado um ponto de final correspondente no seu módulo implementado. Estes pontos finais podem ser utilizados em rotas da sua implementação.

Pelo presente, o único suportado entrada de fluxo e os tipos de saída de fluxo são Hub do Edge. Referência ao tipo de ficheiro de referência de entrada suporta. Outras saídas podem ser contactadas através uma tarefa na cloud de downstream. Por exemplo, uma tarefa do Stream Analytics alojada no Edge envia o resultado para o Hub do Edge, que, em seguida, pode enviar a saída para o IoT Hub. Pode usar uma segunda tarefa do Azure Stream Analytics alojado na cloud com a entrada do IoT Hub e de saída para o Power BI ou outro tipo de saída.



##### <a name="reference-data"></a>Dados de referência
Dados de referência (também conhecido como uma tabela de pesquisa) são um conjunto finito de dados que são estáticos ou lenta alterar por natureza. É utilizado para efetuar uma pesquisa ou para correlacionar com seu fluxo de dados. Para utilizar os dados de referência no seu trabalho de Análise de Fluxo de Fluxo, geralmente utilizará um [Join de Dados](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) de Referência na sua consulta. Para mais informações, consulte os dados de [referência da Utilização para procurar no Stream Analytics](stream-analytics-use-reference-data.md).

Apenas os dados de referência local são suportados. Quando uma tarefa é implementada em dispositivos do IoT Edge, ele carrega dados de referência do caminho de ficheiro de definidas pelo utilizador.

Para criar uma tarefa com dados de referência do Edge:

1. Crie uma nova entrada para a sua tarefa.

2. Escolha **os dados** de Referência como o Tipo **fonte**.

3. Possui um arquivo de dados referência pronto a no dispositivo. Para um contentor do Windows, colocar o arquivo de dados de referência na unidade local e partilhar o disco local com o contentor do Docker. Para um contentor do Linux, criar um volume do Docker e preencha o ficheiro de dados no volume.

4. Defina o caminho de ficheiro. Para o windows host OS e windows container, use o caminho absoluto: `E:\<PathToFile>\v1.csv`. Para um recipiente de anfitriões do Windows OS e Linux ou um recipiente Linux OS e Linux, utilize o caminho no volume: `<VolumeName>/file1.txt`.

![Nova entrada de dados de referência para a tarefa do Azure Stream Analytics do IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Os dados de referência na atualização do IoT Edge são acionados por uma implementação. Quando acionado, o módulo ASA escolhe os dados atualizados sem parar a tarefa em execução.

Existem duas maneiras de atualizar os dados de referência:
* Caminho de dados de referência de atualização na sua tarefa ASA do portal do Azure.
* Atualize a implementação de IoT Edge.

## <a name="license-and-third-party-notices"></a>Licença e avisos de terceiros
* [Azure Stream Analytics na licença IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Aviso de terceiros para Azure Stream Analytics no IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informação de imagem do módulo Azure Stream Analytics 

Esta informação da versão foi atualizada pela última vez em 2019-06-27:

- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - imagem base: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - plataforma:
      - arquitetura: amd64
      - os: linux
  
- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - imagem base: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - plataforma:
      - arquitetura: braço
      - os: linux
  
- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - imagem base: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - plataforma:
      - arquitetura: amd64
      - os: janelas
      
      
## <a name="get-help"></a>Obter ajuda
Para mais assistência, experimente o [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Mais informações sobre O Edge Azure](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA no tutorial IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Desenvolver trabalhos stream analytics edge usando ferramentas do Estúdio Visual](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementar CI/CD para Stream Analytics usando APIs](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

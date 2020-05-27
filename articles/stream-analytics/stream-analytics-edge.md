---
title: Azure Stream Analytics no IoT Edge
description: Crie trabalhos de borda no Azure Stream Analytics e implemente-os em dispositivos que executem o Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 9d4df4efc6dbee88e80e620860487636cc9210dd
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837217"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics no IoT Edge
 
O Azure Stream Analytics (ASA) no IoT Edge permite aos programadores implementarem analytical intelligence em tempo quase real mais próxima dos dispositivos IoT para que possam desbloquear o valor total dos dados gerados por dispositivos. O Azure Stream Analytics foi concebido para baixa latência, resiliência, utilização eficiente da largura de banda e conformidade. Agora, as empresas podem implementar a lógica de controlo perto das operações industriais e complementar as análises de macrodados feitas na cloud.  

O Azure Stream Analytics em IoT Edge funciona dentro da estrutura [Azure IoT Edge.](https://azure.microsoft.com/campaigns/iot-edge/) Uma vez que o trabalho é criado na ASA, você pode implantá-lo e geri-lo usando IoT Hub.

## <a name="scenarios"></a>Cenários
![Diagrama de alto nível de Borda IoT](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Comando e controlo de baixa latência**: Por exemplo, os sistemas de segurança de fabrico devem responder a dados operacionais com latência ultra-baixa. Com a ASA no IoT Edge, pode analisar os dados do sensor em tempo real e emitir comandos quando detetar anomalias para parar uma máquina ou disparar alertas.
*   **Conectividade limitada à nuvem**: Os sistemas críticos da missão, tais como equipamentos de mineração remota, embarcações conectadas ou perfuração offshore, precisam analisar e reagir aos dados mesmo quando a conectividade da nuvem é intermitente. Com a ASA, a sua lógica de streaming funciona independentemente da conectividade da rede e pode escolher o que envia para a nuvem para posterior processamento ou armazenamento.
* **Largura de banda limitada**: O volume de dados produzidos por motores a jato ou carros conectados pode ser tão grande que os dados devem ser filtrados ou pré-processados antes de enviá-los para a nuvem. Utilizando a ASA, pode filtrar ou agregar os dados que precisam de ser enviados para a nuvem.
* **Conformidade**: A conformidade regulamentar pode exigir que alguns dados sejam anoonizados ou agregados localmente antes de serem enviados para a nuvem.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Trabalhos de borda em Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>O que é um trabalho de "vantagem"?

Os postos de trabalho da ASA Edge funcionam em contentores implantados para [dispositivos Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). São compostas por duas partes:
1.  Uma parte na nuvem responsável pela definição de emprego: os utilizadores definem inputs, saída, consulta e outras configurações (fora de ordem, etc.) na nuvem.
2.  Um módulo em execução nos seus dispositivos IoT. Contém o motor ASA e recebe a definição de trabalho da nuvem. 

A ASA utiliza o IoT Hub para implementar trabalhos de borda para dispositivos. Mais informações sobre a implantação de [IoT Edge podem ser vistas aqui](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Trabalho de Ponta analítica De Fluxo Azure](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Instruções de instalação
Os passos de alto nível são descritos na tabela seguinte. Mais detalhes são dados nas seguintes secções.

|      |Passo   | Notas   |
| ---   | ---   |  ---      |
| 1   | **Criar um recipiente de armazenamento**   | Os recipientes de armazenamento são utilizados para salvar a sua definição de trabalho onde podem ser acedidos pelos seus dispositivos IoT. <br>  Pode reutilizar qualquer recipiente de armazenamento existente.     |
| 2   | **Criar um trabalho de borda ASA**   |  Crie um novo emprego, selecione **Edge** como **ambiente de hospedagem.** <br> Estes trabalhos são criados/geridos a partir da nuvem, e funcionam com os seus próprios dispositivos IoT Edge.     |
| 3   | **Instale o seu ambiente IoT Edge no seu(s) dispositivo(s)**   | Instruções para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Implemente as ASA nos seus dispositivos IoT Edge**   |  A definição de emprego asa é exportada para o recipiente de armazenamento criado anteriormente.       |

Pode seguir [este tutorial passo a passo](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) para implementar o seu primeiro trabalho de ASA no IoT Edge. O seguinte vídeo deve ajudá-lo a entender o processo de executar um trabalho de Stream Analytics num dispositivo de borda IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Criar um recipiente de armazenamento
É necessário um recipiente de armazenamento para exportar a consulta compilada ASA e a configuração do trabalho. É usado para configurar a imagem ASA Docker com a sua consulta específica. 
1. Siga [estas instruções](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para criar uma conta de armazenamento a partir do portal Azure. Pode manter todas as opções predefinidas para utilizar esta conta com a ASA.
2. Na conta de armazenamento recém-criada, crie um recipiente de armazenamento de bolhas:
    1. Clique em **Blobs,** em seguida **+ Recipiente**. 
    2. Introduza um nome e mantenha o recipiente como **Privado**.

#### <a name="create-an-asa-edge-job"></a>Criar um trabalho asa edge
> [!Note]
> Este tutorial centra-se na criação de emprego da ASA utilizando o portal Azure. Também pode [usar plugin Visual Studio para criar um trabalho ASA Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. A partir do portal Azure, crie um novo "trabalho de Stream Analytics". [Link direto para criar um novo trabalho ASA aqui](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. No ecrã de criação, selecione **Edge** como ambiente de **hospedagem** (ver a imagem seguinte)

   ![Criar trabalho de Stream Analytics em Edge](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definição de Emprego
    1. **Definir Fluxo de Entrada(s)**. Defina um ou vários fluxos de entrada para o seu trabalho.
    2. Definir dados de referência (opcional).
    3. **Definir fluxo de saída(s)**. Defina um ou vários fluxos de saída para o seu trabalho. 
    4. **Definir consulta.** Defina a consulta ASA na nuvem utilizando o editor inline. O compilador verifica automaticamente a sintaxe ativada para a borda ASA. Também pode testar a sua consulta fazendo o upload de dados da amostra. 

4. Delineie as informações do recipiente de armazenamento no menu de **definições do IoT Edge.**

5. Definir definições opcionais
    1. **Encomenda de eventos.** Pode configurar uma política fora de ordem no portal. A documentação está disponível [aqui.](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)
    2. **Local.** Desinserte o formato de internalização.



> [!Note]
> Quando uma implantação é criada, a ASA exporta a definição de emprego para um recipiente de armazenamento. Esta definição de trabalho permanece a mesma durante a duração de uma implantação. Como consequência, se quiser atualizar um trabalho no limite, precisa editar o trabalho na ASA e, em seguida, criar uma nova implementação no IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Instale o seu ambiente IoT Edge no seu(s) dispositivo(s)
Os trabalhos de borda podem ser implantados em dispositivos que executam o Azure IoT Edge.
Para isso, é necessário seguir estes passos:
- Crie um hub.
- Instale o tempo de execução do Docker e do IoT Edge nos seus dispositivos de borda.
- Desloque os seus dispositivos como **dispositivos IoT Edge** no IoT Hub.

Estes passos são descritos na documentação IoT Edge para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implementação ASA nos seus dispositivos IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Adicione asA à sua implementação
- No portal Azure, abra o IoT Hub, navegue até **IoT Edge** e clique no dispositivo que pretende atingir para esta implementação.
- Selecione **módulos set,** em seguida, selecione **+ Adicione** e escolha **módulo de análise de fluxo Azure**.
- Selecione a subscrição e o trabalho ASA Edge que criou. Clique em Guardar.
![Adicione módulo ASA na sua implementação](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Durante este passo, a ASA cria uma pasta chamada "EdgeJobs" no recipiente de armazenamento (se já não existir). Para cada implementação, é criada uma nova subpasta na pasta "EdgeJobs".
> Ao implementar o seu trabalho em dispositivos IoT Edge, a ASA cria uma assinatura de acesso partilhado (SAS) para o ficheiro de definição de trabalho. A tecla SAS é transmitida de forma segura para os dispositivos IoT Edge utilizando o dispositivo twin. A expiração desta chave está a três anos do dia da sua criação. Quando atualizar um trabalho ioT Edge, o SAS mudará, mas a versão de imagem não mudará. Assim que **atualizar,** siga o fluxo de trabalho de implementação e uma notificação de atualização é registada no dispositivo.


Para mais informações sobre as implementações do IoT Edge, consulte [esta página](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Configure rotas
O IoT Edge fornece uma forma de direcionar as mensagens de forma declarativa entre módulos e entre módulos e IoT Hub. A sintaxe completa é descrita [aqui.](https://docs.microsoft.com/azure/iot-edge/module-composition)
Os nomes das inputs e saídas criadas no trabalho asa podem ser usados como pontos finais para o encaminhamento.  

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
![Exemplo diagrama de encaminhamento de mensagens](media/stream-analytics-edge/edge-message-routing-example.png)

Este exemplo define as seguintes rotas:
- Cada mensagem do **tempSensor** é enviada para o módulo denominado **ASA** para a temperatura **denominada,**
- Todas as saídas do módulo **ASA** são enviadas para o Hub IoT ligado a este dispositivo ($upstream),
- Todas as saídas do módulo **ASA** são enviadas para o ponto final de **controlo** do **tempSensor**.


## <a name="technical-information"></a>Informação técnica
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Limitações atuais para empregos em IoT Edge em comparação com empregos na nuvem
O objetivo é ter paridade entre empregos ioT Edge e empregos em nuvem. A maioria das funcionalidades de linguagem de consulta SQL são suportadas, permitindo executar a mesma lógica tanto na nuvem como no IoT Edge.
No entanto, as seguintes características ainda não são apoiadas para empregos de vanguarda:
* Funções definidas pelo utilizador (UDF) no JavaScript. A UDF está disponível em [C# para trabalhos IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (pré-visualização).
* Agregados definidos pelo utilizador (UDA).
* Funções Azure ML.
* Usando mais de 14 agregados num único passo.
* Formato AVRO para entrada/saída. Neste momento, apenas a CSV e a JSON são apoiadas.
* Os seguintes operadores SQL:
    * PARTIÇÃO POR
    * GetMetadataPropertyValue
* Política de chegada tardia

### <a name="runtime-and-hardware-requirements"></a>Requisitos de tempo de execução e hardware
Para executar as ASA no IoT Edge, precisa de dispositivos que possam executar [O Edge Azure IoT](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA e Azure IoT Edge utilizam os recipientes **Docker** para fornecer uma solução portátil que funciona em vários sistemas operativos hospedeiros (Windows, Linux).

AsA on IoT Edge é disponibilizado como imagens Windows e Linux, funcionando em ambas as arquiteturas x86-64 ou ARM (Advanced RISC Machines). 


### <a name="input-and-output"></a>Entrada e saída
#### <a name="input-and-output-streams"></a>Fluxos de entrada e saída
Os trabalhos asa edge podem obter inputs e saídas de outros módulos em execução em dispositivos IoT Edge. Para ligar de e para módulos específicos, pode definir a configuração de encaminhamento no momento da implantação. Mais informações são descritas na [documentação de composição do módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Tanto para as inputs como para as saídas, os formatos CSV e JSON são suportados.

Para cada fluxo de entrada e saída que cria no seu trabalho ASA, é criado um ponto final correspondente no seu módulo implantado. Estes pontos finais podem ser utilizados nas rotas da sua implantação.

Atualmente, os únicos tipos de entrada e saída de fluxo suportados são o Edge Hub. A entrada de referência suporta o tipo de ficheiro de referência. Outras saídas podem ser alcançadas usando um trabalho em nuvem a jusante. Por exemplo, um trabalho de Stream Analytics hospedado em Edge envia saída para Edge Hub, que pode então enviar saída para IoT Hub. Pode utilizar um segundo trabalho de Cloud Hosted Azure Stream Analytics com entrada do IoT Hub e saída para Power BI ou outro tipo de saída.



##### <a name="reference-data"></a>Dados de referência
Os dados de referência (também conhecidos como mesa de lookup) são um conjunto de dados finito que é estático ou lento mudando na natureza. É utilizado para realizar uma procura ou para se relacionar com o seu fluxo de dados. Para utilizar os dados de referência no seu trabalho de Análise de Fluxo de Fluxo, geralmente utilizará um [Join de Dados](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) de Referência na sua consulta. Para mais informações, consulte os dados de [referência da Utilização para procurar no Stream Analytics](stream-analytics-use-reference-data.md).

Apenas os dados de referência locais são suportados. Quando uma função é implementada para o dispositivo IoT Edge, ele carrega dados de referência do caminho de ficheiro definido pelo utilizador.

Para criar um trabalho com dados de referência no Edge:

1. Crie uma nova entrada para o seu trabalho.

2. Escolha **os dados** de Referência como o Tipo **fonte**.

3. Tenha um ficheiro de dados de referência pronto no dispositivo. Para um recipiente Windows, coloque o ficheiro de dados de referência na unidade local e partilhe a unidade local com o recipiente Docker. Para um recipiente Linux, crie um volume Docker e povoe o ficheiro de dados para o volume.

4. Desloque o caminho do ficheiro. Para o windows host OS e windows container, use o caminho absoluto: `E:\<PathToFile>\v1.csv` . Para um recipiente de anfitriões windows OS e Linux ou um recipiente Linux OS e Linux, utilize o caminho no volume: `<VolumeName>/file1.txt` .

![Nova entrada de dados de referência para o trabalho do Azure Stream Analytics no IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Os dados de referência na atualização IoT Edge são desencadeados por uma implementação. Uma vez acionado, o módulo ASA escolhe os dados atualizados sem parar o trabalho de funcionamento.

Existem duas formas de atualizar os dados de referência:
* Atualize o caminho de dados de referência no seu trabalho ASA a partir do portal Azure.
* Atualize a implantação do IoT Edge.

## <a name="license-and-third-party-notices"></a>Avisos de licença e de terceiros
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
Para mais assistência, experimente o [Microsoft Q&Uma página de perguntas para o Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

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

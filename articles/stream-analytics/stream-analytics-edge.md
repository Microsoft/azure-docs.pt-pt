---
title: Azure Stream Analytics no IoT Edge
description: Crie trabalhos de borda no Azure Stream Analytics e implemente-os em dispositivos que executam a Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 10/29/2020
ms.custom: seodec18
ms.openlocfilehash: cba81b8415f0f9cf7253e674e90ae09718b94d54
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130481"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics no IoT Edge
 
O Azure Stream Analytics (ASA) no IoT Edge permite aos programadores implementarem analytical intelligence em tempo quase real mais próxima dos dispositivos IoT para que possam desbloquear o valor total dos dados gerados por dispositivos. O Azure Stream Analytics foi concebido para baixa latência, resiliência, utilização eficiente da largura de banda e conformidade. Agora, as empresas podem implementar a lógica de controlo perto das operações industriais e complementar as análises de macrodados feitas na cloud.  

O Azure Stream Analytics on IoT Edge funciona dentro da estrutura [Azure IoT Edge.](https://azure.microsoft.com/campaigns/iot-edge/) Uma vez que o trabalho é criado em ASA, você pode implantá-lo e geri-lo usando IoT Hub.

## <a name="scenarios"></a>Cenários
![Diagrama de alto nível de IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Comando e controlo de baixa latência** : Por exemplo, os sistemas de segurança de fabrico devem responder a dados operacionais com latência ultra-baixa. Com o ASA no IoT Edge, pode analisar dados de sensores em quase tempo real e emitir comandos quando detetar anomalias para parar uma máquina ou alertas de gatilho.
*   **Conectividade limitada à nuvem** : Os sistemas críticos da missão, tais como equipamentos de mineração remota, vasos conectados ou perfuração offshore, precisam analisar e reagir aos dados mesmo quando a conectividade na nuvem é intermitente. Com a ASA, a sua lógica de streaming funciona independentemente da conectividade da rede e pode escolher o que envia para a nuvem para posterior processamento ou armazenamento.
* **Largura de banda limitada** : O volume de dados produzidos por motores a jato ou carros conectados pode ser tão grande que os dados devem ser filtrados ou pré-processados antes de os enviar para a nuvem. Utilizando o ASA, pode filtrar ou agregar os dados que precisam de ser enviados para a nuvem.
* **Conformidade** : A conformidade regulamentar pode exigir que alguns dados sejam localmente anonimizados ou agregados antes de serem enviados para a nuvem.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Empregos de borda em Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>O que é um trabalho de "borda"?

Os postos de trabalho da ASA Edge funcionam em contentores implantados nos [dispositivos Azure IoT Edge](../iot-edge/about-iot-edge.md). São compostas por duas partes:
1.  Uma parte em nuvem responsável pela definição de emprego: os utilizadores definem entradas, saídas, consultas e outras configurações (eventos fora de ordem, etc.) na nuvem.
2.  Um módulo a funcionar nos seus dispositivos IoT. Contém o motor ASA e recebe a definição de trabalho da nuvem. 

A ASA utiliza o IoT Hub para implantar trabalhos de borda nos dispositivos. Mais informações sobre [a implementação do IoT Edge podem ser vistas aqui.](../iot-edge/module-deployment-monitoring.md)

![Trabalho de Azure Stream Analytics Edge](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Instruções de instalação
Os passos de alto nível são descritos na tabela seguinte. Mais detalhes são dados nas seguintes secções.

| Passo | Notas |
| --- | --- |
| **Criar um contentor de armazenamento** | Os recipientes de armazenamento são utilizados para salvar a definição de trabalho onde podem ser acedidos pelos seus dispositivos IoT. <br>  Pode reutilizar qualquer recipiente de armazenamento existente. |
| **Criar um trabalho de borda ASA** | Crie um novo emprego, selecione **Edge** como **ambiente de hospedagem** . <br> Estes trabalhos são criados/geridos a partir da nuvem, e funcionam nos seus próprios dispositivos IoT Edge. |
| **Configurar o seu ambiente IoT Edge nos seus dispositivos(s)** | Instruções para [Windows](../iot-edge/quickstart.md) ou [Linux](../iot-edge/quickstart-linux.md).|
| **Coloque a ASA no seu(s) dispositivo IoT Edge** | A definição de emprego asa é exportada para o recipiente de armazenamento criado anteriormente. |

Você pode seguir [este tutorial passo a passo](../iot-edge/tutorial-deploy-stream-analytics.md) para implementar o seu primeiro trabalho ASA no IoT Edge. O vídeo a seguir deve ajudá-lo a entender o processo para executar um trabalho stream Analytics num dispositivo de borda IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
É necessário um recipiente de armazenamento para exportar a consulta compilada asa e a configuração do trabalho. É usado para configurar a imagem ASA Docker com a sua consulta específica. 
1. Siga [estas instruções](../storage/common/storage-account-create.md) para criar uma conta de armazenamento a partir do portal Azure. Pode manter todas as opções padrão para utilizar esta conta com ASA.
2. Na conta de armazenamento recém-criada, crie um recipiente de armazenamento blob:
    1. Clique em **Blobs,** em seguida **, + Recipiente** . 
    2. Introduza um nome e mantenha o recipiente como **Privado** .

#### <a name="create-an-asa-edge-job"></a>Criar um trabalho ASA Edge
> [!Note]
> Este tutorial centra-se na criação de emprego da ASA utilizando o portal Azure. Também pode [usar plugin visual Studio para criar um trabalho ASA Edge](./stream-analytics-tools-for-visual-studio-edge-jobs.md)

1. A partir do portal Azure, crie um novo "stream analytics job". [Ligação direta para criar um novo trabalho asa aqui](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. No ecrã de criação, selecione **Edge** como **ambiente de hospedagem** (ver a seguinte imagem)

   ![Criar trabalho stream analytics no Edge](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definição de Emprego
    1. **Definir fluxo de entrada(s)** . Defina um ou vários fluxos de entrada para o seu trabalho.
    2. Definir dados de referência (opcional).
    3. **Defina fluxo de saída.s.** . Defina um ou vários fluxos de saídas para o seu trabalho. 
    4. **Defina consulta.** Defina a consulta ASA na nuvem utilizando o editor inline. O compilador verifica automaticamente a sintaxe ativada para a borda ASA. Também pode testar a sua consulta carregando dados de amostras. 

4. Defina as informações do recipiente de armazenamento no menu **de definições IoT Edge.**

5. Definir configurações opcionais
    1. **Pedido de eventos** . Pode configurar a política fora de ordem no portal. A documentação está disponível [aqui.](/stream-analytics-query/time-skew-policies-azure-stream-analytics)
    2. **Local.** Desa estaleia o formato de internalização.



> [!Note]
> Quando uma implantação é criada, a ASA exporta a definição de emprego para um recipiente de armazenamento. Esta definição de emprego permanece a mesma durante a duração de uma implantação. Como consequência, se quiser atualizar um trabalho em execução no limite, precisa editar o trabalho na ASA e, em seguida, criar uma nova implementação no IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configurar o seu ambiente IoT Edge no seu(s) dispositivos(s)
Os trabalhos de borda podem ser implantados em dispositivos que executam a Azure IoT Edge.
Para isso, tem de seguir estes passos:
- Criar um hub de iot.
- Instale o tempo de funcionaamento do Docker e do IoT Edge nos dispositivos de borda.
- Desaça os seus dispositivos como **dispositivos IoT Edge** no IoT Hub.

Estes passos são descritos na documentação IoT Edge para [Windows](../iot-edge/quickstart.md) ou [Linux](../iot-edge/quickstart-linux.md).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implantação ASA no seu(s) dispositivo IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Adicione ASA à sua implantação
- No portal Azure, abra o Hub IoT, navegue até **IoT Edge** e clique no dispositivo que pretende direcionar para esta implementação.
- Selecione **definir módulos,** em seguida, selecione **+ Adicione** e escolha o **Módulo Azure Stream Analytics** .
- Selecione a subscrição e o trabalho ASA Edge que criou. Clique em Guardar.
![Adicione o módulo ASA na sua implementação](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Durante este passo, a ASA cria uma pasta chamada "EdgeJobs" no recipiente de armazenamento (se já não existir). Para cada implantação, é criada uma nova sub-dobradeira na pasta "EdgeJobs".
> Quando implementa o seu trabalho em dispositivos IoT Edge, a ASA cria uma assinatura de acesso partilhado (SAS) para o ficheiro de definição de trabalho. A tecla SAS é transmitida de forma segura aos dispositivos IoT Edge utilizando o dispositivo twin. A expiração desta chave é de três anos a partir do dia da sua criação. Quando atualizar um trabalho IoT Edge, o SAS mudará, mas a versão de imagem não mudará. Assim que **atualizar,** siga o fluxo de trabalho da implementação e uma notificação de atualização é registada no dispositivo.


Para obter mais informações sobre as implementações do IoT Edge, consulte [esta página](../iot-edge/module-deployment-monitoring.md).


##### <a name="configure-routes"></a>Rotas deconfigure
O IoT Edge fornece uma forma de encaminhar declarativamente mensagens entre módulos e entre módulos e IoT Hub. A sintaxe completa é descrita [aqui.](../iot-edge/module-composition.md)
Os nomes das entradas e saídas criadas no trabalho asa podem ser usados como pontos finais para o encaminhamento.  

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
Este exemplo mostra as rotas para o cenário descrito na imagem seguinte. Contém um trabalho de borda chamado " **ASA** ", com uma entrada chamada " **temperatura** " e uma saída chamada " **alerta** ".
![Exemplo do diagrama do encaminhamento de mensagens](media/stream-analytics-edge/edge-message-routing-example.png)

Este exemplo define as seguintes rotas:
- Cada mensagem do **tempSensor** é enviada para o módulo chamado **ASA** para a **temperatura** indicada,
- Todas as saídas do módulo **ASA** são enviadas para o Hub IoT ligado a este dispositivo ($upstream),
- Todas as saídas do módulo **ASA** são enviadas para o ponto final de **controlo** do **tempSensor** .


## <a name="technical-information"></a>Informação técnica
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Limitações atuais para empregos IoT Edge em comparação com empregos em nuvem
O objetivo é ter paridade entre empregos IoT Edge e empregos em nuvem. A maioria das funcionalidades de linguagem de consulta SQL são suportadas, permitindo executar a mesma lógica tanto na nuvem como no IoT Edge.
No entanto, as seguintes características ainda não são suportadas para empregos de ponta:
* Funções definidas pelo utilizador (UDF) em JavaScript. UDF está disponível em [C# para trabalhos IoT Edge](./stream-analytics-edge-csharp-udf.md) (pré-visualização).
* Agregados definidos pelo utilizador (UDA).
* Funções Azure ML.
* Usando mais de 14 agregados num único passo.
* Formato AVRO para entrada/saída. Neste momento, apenas o CSV e o JSON são apoiados.
* Os seguintes operadores SQL:
    * PARTIÇÃO POR
    * GetMetadataPropertyValue
* Política de chegada tardia

### <a name="runtime-and-hardware-requirements"></a>Requisitos de tempo de execução e hardware
Para executar o ASA no IoT Edge, precisa de dispositivos que possam executar [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA e Azure IoT Edge utilizam recipientes **Docker** para fornecer uma solução portátil que funciona em vários sistemas operativos hospedeiros (Windows, Linux).

AsA on IoT Edge é disponibilizada como imagens Windows e Linux, funcionando em arquiteturas x86-64 ou ARM (Advanced RISC Machines). 


### <a name="input-and-output"></a>Entrada e saída
#### <a name="input-and-output-streams"></a>Entradas e fluxos de saída
Os trabalhos da ASA Edge podem obter entradas e saídas de outros módulos em execução em dispositivos IoT Edge. Para ligar a partir e a módulos específicos, pode definir a configuração do encaminhamento na hora de implantação. Mais informações são descritas na [documentação de composição do módulo IoT Edge](../iot-edge/module-composition.md).

Tanto para entradas como para saídas, os formatos CSV e JSON são suportados.

Para cada entrada e fluxo de saída que cria no seu trabalho ASA, é criado um ponto final correspondente no módulo implantado. Estes pontos finais podem ser utilizados nas rotas da sua implantação.

Atualmente, os únicos tipos suportados de entrada de fluxo e saída de fluxo são o Edge Hub. A entrada de referência suporta o tipo de ficheiro de referência. Outras saídas podem ser alcançadas usando um trabalho em nuvem a jusante. Por exemplo, um trabalho stream Analytics hospedado no Edge envia a saída para Edge Hub, que pode então enviar a saída para o IoT Hub. Você pode usar um segundo trabalho cloud hospedado Azure Stream Analytics com entrada do IoT Hub e saída para Power BI ou outro tipo de saída.



##### <a name="reference-data"></a>Dados de referência
Os dados de referência (também conhecidos como tabela de procuração) são um conjunto de dados finito que é estático ou muda lentamente na natureza. É utilizado para realizar uma procura ou para se correlacionar com o seu fluxo de dados. Para utilizar dados de referência no seu trabalho Azure Stream Analytics, utilizará geralmente um [Data JOIN de referência](/stream-analytics-query/reference-data-join-azure-stream-analytics) na sua consulta. Para obter mais informações, consulte os [dados de referência de utilização para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md).

Apenas os dados de referência locais são suportados. Quando um trabalho é implantado no dispositivo IoT Edge, carrega dados de referência a partir do caminho do ficheiro definido pelo utilizador.

Para criar um trabalho com dados de referência no Edge:

1. Crie uma nova entrada para o seu trabalho.

2. Escolha **os dados de referência** como o Tipo de **Origem.**

3. Tenha um ficheiro de dados de referência pronto no dispositivo. Para um recipiente Windows, coloque o ficheiro de dados de referência na unidade local e partilhe a unidade local com o recipiente Docker. Para um recipiente Linux, crie um volume Docker e povoe o ficheiro de dados ao volume.

4. Estabeleça o caminho do ficheiro. Para o recipiente OS e Windows do Windows, utilize o caminho absoluto: `E:\<PathToFile>\v1.csv` . Para um recipiente DeSemescório e Linux do Windows Host ou um recipiente Linux OS e Linux, utilize o caminho no volume: `<VolumeName>/file1.txt` .

![Nova entrada de dados de referência para o trabalho do Azure Stream Analytics no IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Os dados de referência na atualização IoT Edge são desencadeados por uma implementação. Uma vez acionado, o módulo ASA escolhe os dados atualizados sem parar o trabalho de funcionamento.

Existem duas formas de atualizar os dados de referência:
* Atualizar o caminho dos dados de referência no seu trabalho ASA a partir do portal Azure.
* Atualize a implementação IoT Edge.

## <a name="license-and-third-party-notices"></a>Avisos de licença e de terceiros
* [Azure Stream Analytics na licença IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Aviso de terceiros para Azure Stream Analytics no IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informação de imagem do módulo Azure Stream Analytics 

Esta informação foi atualizada pela última vez em 2019-06-27:

- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - imagem base: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - plataforma:
      - arquitetura: amd64
      - o: linux
 
- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - imagem base: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - plataforma:
      - arquitetura: braço
      - o: linux
 
- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - imagem base: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - plataforma:
      - arquitetura: arm64
      - o: linux
      
      
## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente a [página de perguntas do Microsoft Q&A para o Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes

* [Mais informações sobre Azure Iot Edge](../iot-edge/about-iot-edge.md)
* [ASA no tutorial IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Desenvolver trabalhos stream Analytics Edge usando ferramentas de Estúdio Visual](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Implementar CI/CD para stream analytics usando APIs](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
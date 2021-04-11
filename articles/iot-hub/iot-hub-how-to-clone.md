---
title: Como clonar um hub Azure IoT
description: Como clonar um hub Azure IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 7f5553cc51927d878487b0875e72873451a3de3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059586"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Como clonar um hub Azure IoT para outra região

Este artigo explora formas de clonar um Hub IoT e fornece algumas perguntas que precisa responder antes de começar. Aqui estão várias razões para clonar um hub IoT:
 
* Está a mover a sua empresa de uma região para outra, como da Europa para a América do Norte (ou vice-versa), e quer que os seus recursos e dados estejam geograficamente perto da sua nova localização, por isso precisa de mudar o seu centro.

* Está a criar um centro para um ambiente de desenvolvimento versus produção.

* Você quer fazer uma implementação personalizada de multi-hub alta disponibilidade. Para obter mais informações, consulte [a secção HA de região transversal do IoT Hub alta disponibilidade e recuperação de desastres.](iot-hub-ha-dr.md#achieve-cross-region-ha)

* Quer aumentar o número de [divisórias](iot-hub-scaling.md#partitions) configuradas para o seu centro. Isto é definido quando crias o teu hub pela primeira vez, e não podes ser mudado. Pode utilizar a informação neste artigo para clonar o seu hub e quando o clone for criado, aumente o número de divisórias.

Para clonar um hub, você precisa de uma subscrição com acesso administrativo ao centro original. Você pode colocar o novo hub em um novo grupo de recursos e região, na mesma subscrição que o hub original, ou mesmo em uma nova subscrição. Não pode usar o mesmo nome porque o nome do centro tem de ser globalmente único.

> [!NOTE]
> Neste momento, não há nenhuma funcionalidade disponível para clonagem automática de um hub IoT. É principalmente um processo manual, e, portanto, é bastante propenso a erros. A complexidade da clonagem de um centro é diretamente proporcional à complexidade do centro. Por exemplo, clonar um hub IoT sem encaminhamento de mensagens é bastante simples. Se adicionar o encaminhamento de mensagens como apenas uma complexidade, a clonagem do hub torna-se, pelo menos, uma ordem de magnitude mais complicada. Se também mover os recursos usados para encaminhar pontos finais, é outra ordem de magnitura mais complicada. 

## <a name="things-to-consider"></a>Coisas a considerar

Há várias coisas a considerar antes de clonar um hub IoT.

* Certifique-se de que todas as funcionalidades disponíveis na localização original também estão disponíveis na nova localização. Alguns serviços estão em pré-visualização, e nem todas as funcionalidades estão disponíveis em todo o lado.

* Não remova os recursos originais antes de criar e verificar a versão clonada. Uma vez removido um hub, ele se foi para sempre, e não há como recuperá-lo para verificar as definições ou dados para se certificar de que o hub é replicado corretamente.

* Muitos recursos requerem nomes globalmente únicos, por isso deve usar nomes diferentes para as versões clonadas. Também deve usar um nome diferente para o grupo de recursos a que pertence o hub clonado. 

* Os dados para o hub IoT original não são migrados. Isto inclui mensagens de telemetria, comandos cloud-to-device (C2D) e informações relacionadas com o trabalho, tais como horários e histórico. As métricas e os resultados da exploração madeireira também não são migrados. 

* Para dados ou mensagens encaminhados para o Azure Storage, pode deixar os dados na conta de armazenamento original, transferir esses dados para uma nova conta de armazenamento na nova região, ou deixar os dados antigos no lugar e criar uma nova conta de armazenamento na nova localização para os novos dados. Para obter mais informações sobre os dados em movimento no armazenamento blob, consulte [Começar com a AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Os dados relativos aos Centros de Eventos e para Tópicos e Filas de Autocarros de Serviço não podem ser migrados. Trata-se de dados pontuais e não são armazenados após o processamento das mensagens.

* Tens de marcar tempo de inatividade para a migração. A clonagem dos dispositivos para o novo centro leva tempo. Se estiver a utilizar o método De Importação/Exportação, os testes de referência revelaram que pode levar cerca de duas horas a mover 500.000 dispositivos e quatro horas para mover um milhão de dispositivos. 

* Pode copiar os dispositivos para o novo hub sem desligar ou alterar os dispositivos. 

    * Se os dispositivos foram originalmente provisionados utilizando DPS, re-provisionando-os atualiza as informações de ligação armazenadas em cada dispositivo. 
    
    * Caso contrário, tem de utilizar o método de Importação/Exportação para mover os dispositivos e, em seguida, os dispositivos têm de ser modificados para utilizar o novo hub. Por exemplo, pode configurar o seu dispositivo para consumir o nome de anfitrião IoT Hub a partir das propriedades duplas desejadas. O dispositivo irá pegar no nome do anfitrião IoT Hub, desligar o dispositivo do antigo hub e reconectá-lo ao novo.
    
* Tem de atualizar quaisquer certificados que esteja a utilizar para que possa usá-los com os novos recursos. Além disso, provavelmente tem o hub definido numa tabela DNS em algum lugar - terá de atualizar essa informação do DNS.

## <a name="methodology"></a>Metodologia

Este é o método geral que recomendamos para mover um hub IoT de uma região para outra. Para o encaminhamento de mensagens, isto pressupõe que os recursos não estão a ser transferidos para a nova região. Para obter mais informações, consulte a [secção de Encaminhamento de Mensagens](#how-to-handle-message-routing).

   1. Exporte o hub e as suas definições para um modelo de Gestor de Recursos. 
   
   1. Faça as alterações necessárias ao modelo, tais como a atualização de todas as ocorrências do nome e a localização do hub clonado. Para quaisquer recursos no modelo utilizado para os pontos finais de encaminhamento de mensagens, atualize a chave no modelo para esse recurso.
   
   1. Importe o modelo para um novo grupo de recursos na nova localização. Isto cria o clone.

   1. Depurar o necessário. 
   
   1. Adicione qualquer coisa que não tenha sido exportada para o modelo. 
   
       Por exemplo, os grupos de consumidores não são exportados para o modelo. É necessário adicionar os grupos de consumidores ao modelo manualmente ou utilizar o [portal Azure](https://portal.azure.com) após a criação do hub. Existe um exemplo de adicionar um grupo de consumidores a um modelo no artigo [Utilize um modelo de Gestor de Recursos Azure para configurar o encaminhamento de mensagens IoT Hub](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Copie os dispositivos do centro original para o clone. Isto está coberto na secção [Gerir os dispositivos registados no hub IoT](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Como lidar com o encaminhamento de mensagens

Se o seu hub utilizar [o encaminhamento personalizado,](iot-hub-devguide-messages-read-custom.md)exportar o modelo para o hub inclui a configuração de encaminhamento, mas não inclui os próprios recursos. Deve escolher se deve mover os recursos de encaminhamento para o novo local ou deixá-los no lugar e continuar a usá-los "como está". 

Por exemplo, digamos que tem um hub no Oeste dos EUA que está a encaminhar mensagens para uma conta de armazenamento (também nos EUA Ocidentais), e quer mudar o centro para os EUA Orientais. Pode mover o hub e fazê-lo ainda encaminhar mensagens para a conta de armazenamento no Oeste dos EUA, ou pode mover o hub e também mover a conta de armazenamento. Pode haver um pequeno desempenho atingido desde o encaminhamento de mensagens até recursos de ponta final numa região diferente.

Pode mover um hub que utilize o encaminhamento de mensagens facilmente se não mover também os recursos utilizados para os pontos finais de encaminhamento. 

Se o centro utilizar o encaminhamento de mensagens, tem duas opções. 

1. Mova os recursos utilizados para os pontos finais de encaminhamento para o novo local.

    * Você mesmo deve criar os novos recursos manualmente no [portal Azure](https://portal.azure.com) ou através da utilização de modelos de Gestor de Recursos. 

    * Você deve renomear todos os recursos quando os cria no novo local, uma vez que eles têm nomes globalmente únicos. 
     
    * Tem de atualizar os nomes dos recursos e as teclas de recursos no modelo do novo hub, antes de criar o novo hub. Os recursos devem estar presentes quando o novo hub for criado.

1. Não mova os recursos usados para os pontos finais de encaminhamento. Use-os "no lugar".

   * No passo em que edita o modelo, terá de recuperar as teclas de cada recurso de encaminhamento e colocá-las no modelo antes de criar o novo hub. 

   * O centro ainda refere os recursos de encaminhamento originais e encaminha mensagens para eles como configurados.

   * Você terá um pequeno desempenho atingido porque o hub e os recursos do ponto final de encaminhamento não estão no mesmo local.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Prepare-se para migrar o centro para outra região

Esta secção fornece instruções específicas para migrar o hub.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Encontre o hub original e exporte-o para um modelo de recurso.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

1. Vá a **Grupos de Recursos** e selecione o grupo de recursos que contém o hub que pretende mover. Você também pode ir a **Recursos** e encontrar o centro assim. Selecione o centro.

1. Selecione o modelo de **exportação** da lista de propriedades e configurações para o hub. 

   ![Screenshot mostrando o comando para exportar o modelo para o IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Selecione **Baixar** para descarregar o modelo. Guarde o ficheiro em algum lugar onde possa encontrá-lo novamente. 

   ![Screenshot mostrando o comando para descarregar o modelo para o IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Ver o modelo 

1. Aceda à pasta Downloads (ou à pasta que usou quando exportou o modelo) e encontre o ficheiro zip. Abra o ficheiro zip e encontre o ficheiro chamado `template.json` . Selecione-o e, em seguida, selecione Ctrl+C para copiar o modelo. Vá a uma pasta diferente que não esteja no ficheiro zip e cole o ficheiro (Ctrl+V). Agora pode editá-lo.
 
    O exemplo a seguir é para um hub genérico sem configuração de encaminhamento. É um hub de nível S1 (com 1 unidade) chamado **ContosoTestHub29358** na região **oeste.** Aqui está o modelo exportado.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Editar o modelo 

Você tem que fazer algumas alterações antes de usar o modelo para criar o novo hub na nova região. Utilize [o Código VS](https://code.visualstudio.com) ou um editor de texto para editar o modelo.

#### <a name="edit-the-hub-name-and-location"></a>Editar o nome e a localização do hub

1. Remova a secção de parâmetros no topo. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Altere o nome para utilizar o nome real (novo) em vez de o recuperar de um parâmetro (que removeu no passo anterior). 

    Para o novo hub, use o nome do hub original mais o *clone* de cordas para comur o novo nome. Comece por limpar o nome e a localização do hub.
    
    Versão antiga:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nova versão: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Em seguida, você vai descobrir que os valores para **o caminho** contêm o nome antigo do hub. Mude-os para usar o novo. Estes são os valores de caminho no âmbito do **eventHubEndpoints** **chamados eventos** e **OperationsMonitoringEvents**.

    Quando terminar, a secção de pontos finais do centro de eventos deve ser assim:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Atualizar as chaves para os recursos de encaminhamento que não estão sendo movidos

Ao exportar o modelo de Gestor de Recursos para um hub que tenha o encaminhamento configurado, verá que as chaves desses recursos não são fornecidas no modelo exportado -- a sua colocação é denotada por asteriscos. Você deve preenchê-los indo para esses recursos no portal e recuperando as chaves **antes** de importar o modelo do novo hub e criar o hub. 

1. Recupere as teclas necessárias para qualquer um dos recursos de encaminhamento e coloque-as no modelo. Pode recuperar as teclas do recurso no [portal Azure](https://portal.azure.com). 

   Por exemplo, se estiver a encaminhar mensagens para um recipiente de armazenamento, encontre a conta de armazenamento no portal. Na secção Definições, selecione **as teclas de acesso** e, em seguida, copie uma das teclas. Aqui está como é a chave quando você exporta o modelo pela primeira vez:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Depois de recuperar a chave de conta para a conta de armazenamento, coloque-a no modelo na cláusula `AccountKey=****` no lugar dos asteriscos. 

1. Para filas de autocarros de serviço, obtenha a Chave de Acesso Compartilhada correspondente ao SharedAccessKeyName. Aqui está a chave e `SharedAccessKeyName` o no json:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. O mesmo se aplica às ligações Service Bus Bus e Event Hub.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Criar os novos recursos de encaminhamento no novo local

Esta secção só se aplica se estiver a mover os recursos utilizados pelo hub para os pontos finais de encaminhamento.

Se quiser mover os recursos de encaminhamento, deve configurar manualmente os recursos no novo local. Pode criar os recursos de encaminhamento utilizando o [portal Azure,](https://portal.azure.com)ou exportando o modelo de Gestor de Recursos para cada um dos recursos utilizados pelo encaminhamento de mensagens, editando-os e importando-os. Após a configuração dos recursos, pode importar o modelo do hub (que inclui a configuração de encaminhamento).

1. Crie cada recurso utilizado pelo encaminhamento. Pode fazê-lo manualmente utilizando o [portal Azure,](https://portal.azure.com)ou criar os recursos utilizando modelos de Gestor de Recursos. Se quiser utilizar modelos, estes são os passos a seguir:

    1. Para cada recurso utilizado pelo encaminhamento, exporte-o para um modelo de Gestor de Recursos.
    
    1. Atualize o nome e a localização do recurso. 

    1. Atualize quaisquer referências cruzadas entre os recursos. Por exemplo, se criar um modelo para uma nova conta de armazenamento, precisa atualizar o nome da conta de armazenamento nesse modelo e qualquer outro modelo que o faça referência. Na maioria dos casos, a secção de encaminhamento no modelo para o hub é o único outro modelo que faz referência ao recurso. 

    1. Importe cada um dos modelos, que implementa cada recurso.

    Uma vez que os recursos utilizados pelo encaminhamento estejam configurado e em funcionamento, pode continuar.

1. No modelo para o hub IoT, altere o nome de cada um dos recursos de encaminhamento para o seu novo nome e atualize a localização se necessário. 

Agora você tem um modelo que vai criar um novo hub que se parece quase exatamente com o antigo hub, dependendo de como você decidiu lidar com o encaminhamento.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Move -- criar o novo hub na nova região carregando o modelo

Crie o novo hub na nova localização utilizando o modelo. Se tiver recursos de encaminhamento que se vão mover, os recursos devem ser configurado na nova localização e as referências no modelo atualizado para combinar. Se não estiver a mover os recursos de encaminhamento, eles devem estar no modelo com as teclas atualizadas.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**. 

1. Na caixa de pesquisa, coloque em "implementação do modelo" e selecione Enter.

1. Selecione **a implementação do modelo (implementar usando modelos personalizados)**. Isto leva-o a um ecrã para a implementação do modelo. Selecione **Criar**. Verá este ecrã:

   ![Screenshot mostrando o comando para construir o seu próprio modelo](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. **Selecione Construa o seu próprio modelo no editor, o** que lhe permite carregar o seu modelo a partir de um ficheiro. 

1. Selecione **O ficheiro de carga**. 

   ![Screenshot mostrando o comando para carregar um ficheiro de modelo](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Navegue pelo novo modelo que editou e selecione-o e, em seguida, selecione **Open**. Carrega o seu modelo na janela de edição. Selecione **Guardar**. 

   ![Screenshot mostrando o carregamento do modelo](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Preencha os campos seguintes.

   **Assinatura**: selecione a subscrição a utilizar.

   **Grupo de recursos:** criar um novo grupo de recursos num novo local. Se já tiver um novo conjunto, pode selecioná-lo em vez de criar um novo.

   **Localização**: Se selecionou um grupo de recursos existente, este é preenchido para que você corresponda à localização do grupo de recursos. Se criou um novo grupo de recursos, esta será a sua localização.

   **Concordo com a caixa de verificação:** basicamente diz que concorda em pagar os recursos que está a criar.

1. Selecione o botão **Comprar.**

O portal valida agora o seu modelo e implanta o seu hub clonado. Se tiver dados de configuração de encaminhamento, este será incluído no novo hub, mas apontará para os recursos na localização anterior.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Gestão dos dispositivos registados no hub IoT

Agora que tens o teu clone a funcionar, tens de copiar todos os dispositivos do centro original para o clone. 

Há várias maneiras de conseguir isto. Ou usou originalmente [o Serviço de Provisionamento de Dispositivos (DPS)](../iot-dps/about-iot-dps.md)para forragem dos dispositivos, ou não o fez. Se o fez, isto não é difícil. Se não o fez, isto pode ser muito complicado. 

Se não utilizar o DPS para forragens dos seus dispositivos, pode saltar a secção seguinte e começar com a [Utilização de Importação/Exportação para mover os dispositivos para o novo hub](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Utilização de DPS para re-fornecimento dos dispositivos no novo hub

Para utilizar o DPS para mover os dispositivos para a nova localização, consulte [como re-provisões dispositivos](../iot-dps/how-to-reprovision.md). Quando terminar, pode ver os dispositivos no [portal Azure](https://portal.azure.com) e verificar se estão na nova localização.

Vá para o novo hub utilizando o [portal Azure](https://portal.azure.com). Selecione o seu hub e, em seguida, selecione **Dispositivos IoT**. Vê-se os dispositivos que foram re-a provisionados para o centro clonado. Também pode ver as propriedades do centro clonado. 

Se implementou o encaminhamento, teste e certifique-se de que as suas mensagens são encaminhadas para os recursos corretamente.

### <a name="committing-the-changes-after-using-dps"></a>Cometer as alterações após a utilização do DPS

Esta alteração foi cometida pelo serviço DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Reversão das alterações após a utilização do DPS. 

Se pretender reverter as alterações, volte a realocar os dispositivos do novo hub para o antigo.

Já terminou de migrar o seu centro e os seus dispositivos. Pode saltar para [a Limpeza.](#clean-up)

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Usando Import-Export para mover os dispositivos para o novo hub

A aplicação tem como alvos .NET Core, para que possa executá-lo no Windows ou linux. Você pode baixar a amostra, recuperar as suas cordas de conexão, definir as bandeiras para as partes que você quer executar, e executá-la. Podes fazer isto sem nunca abrires o código.

### <a name="downloading-the-sample"></a>Descarregar a amostra

1. Utilize as amostras IoT C# desta página: [Amostras Azure IoT para C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Descarregue o ficheiro zip e desaperte-o no seu computador. 

1. O código pertinente está em ./iot-hub/Samples/service/ImportExportDevicesSample. Não é necessário visualizar ou editar o código para executar a aplicação.

1. Para executar a aplicação, especifique três cadeias de ligação e cinco opções. Você transmite estes dados como argumentos de linha de comando ou use variáveis ambientais, ou use uma combinação dos dois. Vamos passar as opções como argumentos de linha de comando, e as cordas de conexão como variáveis ambientais. 

   A razão para isso é porque as cordas de ligação são longas e desajeitadas, e é improvável que mude, mas você pode querer mudar as opções e executar a aplicação mais de uma vez. Para alterar o valor de uma variável ambiental, tem de fechar a janela de comando e o Visual Studio ou o Código VS, seja qual for o seu uso. 

### <a name="options"></a>Opções

Aqui estão as cinco opções que especifica quando executar a aplicação. Vamos pôr isto na linha de comando num minuto.

*   **addDevices** (argumento 1) -- deserete isto para ser verdadeiro se quiser adicionar dispositivos virtuais que são gerados para si. Estes são adicionados ao centro de origem. Além disso, desenhe **numToAdd** (argumento 2) para especificar quantos dispositivos pretende adicionar. O número máximo de dispositivos que pode registar num centro é de um milhão. O objetivo desta opção é testar- pode gerar um número específico de dispositivos e copiá-los para outro centro.

*   **copyDevices** (argumento 3) -- desemote isto para copiar os dispositivos de um hub para outro. 

*   **deleteSourceDevices** (argumento 4) -- descreva-o para eliminar todos os dispositivos registados no centro de origem. Recomendamos esperar até ter a certeza de que todos os dispositivos foram transferidos antes de executar isto. Uma vez apagados os dispositivos, não os podes recuperar.

*   **deleteDestDevices** (argumento 5) -- descreva-o para eliminar todos os dispositivos registados no centro de destino (o clone). É melhor fazer isto se quiser copiar os dispositivos mais de uma vez. 

O comando básico será *dotnet run* -- isto diz .NET para construir o arquivo csproj local e, em seguida, executá-lo. Adicione os seus argumentos de linha de comando até ao fim antes de executá-lo. 

A sua linha de comando será semelhante a estes exemplos:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Usando variáveis ambientais para as cordas de ligação

1. Para executar a amostra, você precisa das cordas de ligação aos antigos e novos hubs IoT, e a uma conta de armazenamento que você pode usar para ficheiros de trabalho temporário. Armazenaremos os valores para estes em variáveis ambientais.

1. Para obter os valores das cordas de ligação, inscreva-se no [portal Azure](https://portal.azure.com). 

1. Coloque as cordas de ligação em algum lugar onde possa recuperá-las, como o NotePad. Se copiar o seguinte, pode colar as cordas de ligação diretamente para onde vão. Não adicione espaços em torno do sinal igual, ou muda o nome variável. Além disso, não precisa de citações duplas em torno das cordas de ligação. Se colocar as cotações à volta da cadeia de ligação da conta de armazenamento, não funcionará.

   Para o Windows, é assim que se definem as variáveis ambientais:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Para o Linux, é assim que se definem as variáveis ambientais:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Para as cordas de ligação do hub IoT, vá a cada centro do portal. Pode procurar em **Recursos** o centro. Se conhecer o Grupo de Recursos, pode ir a **grupos de Recursos,** selecionar o seu grupo de recursos e, em seguida, selecionar o hub da lista de ativos desse grupo de recursos. 

1. Selecione **as políticas** de acesso partilhado a partir das Definições para o hub, em seguida, selecione **iothubowner** e copie uma das cadeias de ligação. Faça o mesmo para o centro de destino. Adicione-os aos comandos SET apropriados.

1. Para a cadeia de ligação da conta de armazenamento, encontre a conta de armazenamento em **Recursos** ou no seu **grupo de Recursos** e abra-a. 
   
1. Na secção Definições, selecione **as teclas de acesso** e copie uma das cadeias de ligação. Coloque o fio de ligação no seu ficheiro de texto para o comando SET apropriado. 

Agora tem as variáveis ambientais num ficheiro com os comandos SET, e sabe quais são os seus argumentos de linha de comando. Vamos analisar a amostra.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Executar a aplicação da amostra e utilizar argumentos de linha de comando

1. Abra uma janela da linha de comandos. Selecione Windows e digite `command prompt` para obter a janela de pedido de comando.

1. Copie os comandos que definem as variáveis ambientais, uma de cada vez, e cole-as na janela de pedido de comando e selecione Enter. Quando terminar, digite `SET` a janela de aviso de comando para ver as variáveis ambientais e os seus valores. Depois de copiar isto para a janela de ordem, não precisa copiá-los novamente, a menos que abra uma nova janela de ordem de comando.

1. Na janela de pedido de comando, altere os diretórios até estar em ./ImportExportDevicesSample (onde existe o ficheiro ImportExportDevicesSample.csproj). Em seguida, digite os seguintes e inclua os seus argumentos de linha de comando.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    O comando dotnet constrói e executa a aplicação. Como está a passar as opções quando executar a aplicação, pode alterar os valores deles cada vez que executar a aplicação. Por exemplo, é melhor executá-lo uma vez e criar novos dispositivos, em seguida, executá-lo novamente e copiar esses dispositivos para um novo hub, e assim por diante. Também pode executar todos os passos na mesma corrida, embora recomendemos que não extingue nenhum dispositivo até ter a certeza de que está terminado com a clonagem. Aqui está um exemplo que cria 1000 dispositivos e depois os copia para o outro centro.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Depois de verificar se os dispositivos foram copiados com sucesso, pode remover os dispositivos do centro de origem desta forma:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Executando a aplicação da amostra usando o Visual Studio

1. Se pretender executar a aplicação no Visual Studio, altere o seu diretório atual para a pasta onde reside o ficheiro IoTHubServiceSamples.sln. Em seguida, executar este comando na janela de ordem para abrir a solução no Estúdio Visual. Deve fazê-lo na mesma janela de comando onde define as variáveis ambientais, para que essas variáveis sejam conhecidas.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Clique com o botão direito no projeto *ImportExportDevicesSample* e selecione **set como projeto de arranque**.    
    
1. Coloque as variáveis no topo do Programa.cs na pasta ImportExportDevicesSample para as cinco opções.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Selecione F5 para executar a aplicação. Depois de terminar de correr, pode ver os resultados.

### <a name="view-the-results"></a>Ver os resultados 

Pode ver os dispositivos no [portal Azure](https://portal.azure.com) e verificar se estão na nova localização.

1. Vá para o novo hub utilizando o [portal Azure](https://portal.azure.com). Selecione o seu hub e, em seguida, selecione **Dispositivos IoT**. Vês os dispositivos que acabaste de copiar do antigo centro para o centro clonado. Também pode ver as propriedades do centro clonado. 

1. Verifique se há erros de importação/exportação indo à conta de armazenamento Azure no [portal Azure](https://portal.azure.com) e procurando no `devicefiles` recipiente para o `ImportErrors.log` . Se este ficheiro estiver vazio (o tamanho é 0), não houve erros. Se tentar importar o mesmo dispositivo mais de uma vez, rejeita o dispositivo pela segunda vez e adiciona uma mensagem de erro ao ficheiro de registo.

### <a name="committing-the-changes"></a>Cometer as alterações 

Neste momento, copiou o seu hub para o novo local e emigrou os dispositivos para o novo clone. Agora tens de fazer alterações para que os dispositivos funcionem com o centro clonado.

Para cometer as alterações, aqui estão os passos que precisa de realizar: 

* Atualize cada dispositivo para alterar o nome do anfitrião IoT Hub para apontar o nome do anfitrião IoT Hub para o novo hub. Deve fazê-lo utilizando o mesmo método utilizado quando o dispositivo foriu pela primeira vez.

* Altere quaisquer aplicações que tenha que se refiram ao antigo hub para apontar para o novo hub.

* Depois de terminar, o novo centro deve estar a funcionar. O antigo centro não deve ter dispositivos ativos e estar num estado desligado. 

### <a name="rolling-back-the-changes"></a>Reverter as mudanças

Se decidir reverter as alterações, aqui estão os passos a executar:

* Atualize cada dispositivo para alterar o nome de anfitrião do IoT Hub para apontar o nome de anfitrião do IoT Hub para o antigo hub. Deve fazê-lo utilizando o mesmo método utilizado quando o dispositivo foriu pela primeira vez.

* Altere quaisquer aplicações que tenha que se refiram ao novo hub para apontar para o antigo hub. Por exemplo, se estiver a utilizar o Azure Analytics, poderá ter de reconfigurar a [sua entrada Azure Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Apague o novo centro. 

* Se tiver recursos de encaminhamento, a configuração no antigo hub ainda deve apontar para a configuração correta do encaminhamento, e deve trabalhar com esses recursos após o hub ser reiniciado.

### <a name="checking-the-results"></a>Verificação dos resultados 

Para verificar os resultados, altere a sua solução IoT para apontar para o seu hub no novo local e executá-lo. Por outras palavras, execute as mesmas ações com o novo hub que executou com o hub anterior e certifique-se de que funcionam corretamente. 

Se implementou o encaminhamento, teste e certifique-se de que as suas mensagens são encaminhadas para os recursos corretamente.

## <a name="clean-up"></a>Limpeza

Não limpe até ter a certeza de que o novo hub está a funcionar e os dispositivos estão a funcionar corretamente. Também certifique-se de testar o encaminhamento se estiver a utilizar essa função. Quando estiver pronto, limpe os recursos antigos executando estes passos:

* Se ainda não o fez, apague o antigo centro. Isto remove todos os dispositivos ativos do centro.

* Se tiver recursos de encaminhamento que mudou para o novo local, pode apagar os antigos recursos de encaminhamento.

## <a name="next-steps"></a>Passos seguintes

Clonou um hub IoT para um novo centro numa nova região, com os dispositivos. Para obter mais informações sobre a realização de operações a granel contra o registo de identidade num hub IoT, consulte [as identidades do dispositivo IoT Hub de importação e exportação a granel](iot-hub-bulk-identity-mgmt.md).

Para mais informações sobre o IoT Hub e desenvolvimento para o centro, consulte os seguintes artigos.

* [Guia de desenvolvedores do IoT Hub](iot-hub-devguide.md)

* [Tutorial de encaminhamento IoT Hub](tutorial-routing.md)

* [Visão geral da gestão de dispositivos IoT Hub](iot-hub-device-management-overview.md)

* Se pretender implementar a aplicação da amostra, consulte a [implementação da aplicação .NET Core](/dotnet/core/deploying/index).
---
title: Como clonar um hub Azure IoT
description: Como clonar um hub Azure IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75429156"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Como clonar um hub Azure IoT para outra região

Este artigo explora formas de clonar um Hub IoT e fornece algumas perguntas que precisa de responder antes de começar. Aqui estão várias razões para que queira clonar um hub IoT:
 
* Está a mover a sua empresa de uma região para outra, como da Europa para a América do Norte (ou vice-versa), e quer que os seus recursos e dados estejam geograficamente perto da sua nova localização, por isso precisa de mudar o seu hub.

* Está a criar um centro para um ambiente de desenvolvimento contra produção.

* Você quer fazer uma implementação personalizada de multi-hub alta disponibilidade. Para mais informações, consulte a [secção HA da região transversal de IoT Hub](iot-hub-ha-dr.md#achieve-cross-region-ha)de alta disponibilidade e recuperação de desastres.

* Quer aumentar o número de [divisórias](iot-hub-scaling.md#partitions) configuradas para o seu hub. Isto é definido quando crias o teu centro pela primeira vez, e não podes ser mudado. Pode utilizar a informação neste artigo para clonar o seu hub e quando o clone for criado, aumente o número de divisórias.

Para clonar um hub, precisa de uma subscrição com acesso administrativo ao centro original. Você pode colocar o novo hub em um novo grupo de recursos e região, na mesma subscrição que o hub original, ou mesmo em uma nova subscrição. Não pode usar o mesmo nome porque o nome do centro tem que ser globalmente único.

> [!NOTE]
> Neste momento, não há nenhuma funcionalidade disponível para clonagem automática de um hub IoT. É principalmente um processo manual, e assim é bastante propenso a erros. A complexidade da clonagem de um hub é diretamente proporcional à complexidade do centro. Por exemplo, clonar um hub IoT sem encaminhamento de mensagens é bastante simples. Se adicionar mossa de mensagem como apenas uma complexidade, clonagem do centro torna-se pelo menos uma ordem de magnitude mais complicada. Se também mover os recursos usados para encaminhamento de pontos finais, é outra ordem de ampliação mais complicada. 

## <a name="things-to-consider"></a>Coisas a considerar

Há várias coisas a considerar antes de clonar um centro de IoT.

* Certifique-se de que todas as funcionalidades disponíveis na localização original também estão disponíveis na nova localização. Alguns serviços estão em pré-visualização, e nem todas as funcionalidades estão disponíveis em todo o lado.

* Não remova os recursos originais antes de criar e verificar a versão clonada. Uma vez removido um hub, desaparece para sempre, e não há como recuperá-lo para verificar as definições ou dados para se certificar de que o hub é replicado corretamente.

* Muitos recursos requerem nomes globalmente únicos, por isso deve usar nomes diferentes para as versões clonadas. Também deve usar um nome diferente para o grupo de recursos a que pertence o centro clonado. 

* Os dados para o centro ioT original não são migrados. Isto inclui mensagens de telemetria, comandos cloud-to-device (C2D) e informações relacionadas com o trabalho, tais como horários e história. As métricas e os resultados da exploração madeireira também não são migrados. 

* Para dados ou mensagens encaminhados para o Armazenamento Do Azure, pode deixar os dados na conta de armazenamento original, transferir esses dados para uma nova conta de armazenamento na nova região, ou deixar os dados antigos no local e criar uma nova conta de armazenamento no novo local para os novos dados. Para obter mais informações sobre a movimentação de dados no armazenamento blob, consulte [Iniciar-se com AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Os dados relativos aos Centros de Eventos e aos Tópicos e Filas de Autocarros de Serviço não podem ser migrados. Estes são dados ponto-a-tempo e não são armazenados após o processamento das mensagens.

* Tens de marcar tempo de descanso para a migração. A clonagem dos dispositivos para o novo centro leva tempo. Se estiver a utilizar o método import/exportação, os testes de referência revelaram que pode demorar cerca de duas horas a mover 500.000 dispositivos e quatro horas para mover um milhão de dispositivos. 

* Pode copiar os dispositivos para o novo hub sem desligar ou alterar os dispositivos. 

    * Se os dispositivos foram originalmente aprovisionados utilizando DPS, reaprovisioná-los atualiza as informações de ligação armazenadas em cada dispositivo. 
    
    * Caso contrário, tem de utilizar o método import/exportação para mover os dispositivos e, em seguida, os dispositivos têm de ser modificados para utilizar o novo hub. Por exemplo, pode configurar o seu dispositivo para consumir o nome de anfitrião ioT Hub a partir das propriedades gémeas desejadas. O dispositivo tomará o nome do ioT Hub, desligará o dispositivo do antigo hub e reconectar-se-á com o novo.
    
* Precisa atualizar quaisquer certificados que esteja a usar para que possa usá-los com os novos recursos. Além disso, provavelmente tem o hub definido numa tabela DNS em algum lugar - você precisará atualizar essa informação DNS.

## <a name="methodology"></a>Metodologia

Este é o método geral que recomendamos para mover um hub IoT de uma região para outra. Para o encaminhamento de mensagens, isto pressupõe que os recursos não estão a ser transferidos para a nova região. Para mais informações, consulte a [secção de Encaminhamento de Mensagens](#how-to-handle-message-routing).

   1. Exporte o hub e as suas definições para um modelo de Gestor de Recursos. 
   
   1. Faça as alterações necessárias ao modelo, tais como a atualização de todas as ocorrências do nome e a localização do centro clonado. Para quaisquer recursos no modelo utilizado para pontos finais de encaminhamento de mensagens, atualize a chave no modelo para esse recurso.
   
   1. Importe o modelo para um novo grupo de recursos no novo local. Isto cria o clone.

   1. Depuração, se necessário. 
   
   1. Adicione qualquer coisa que não tenha sido exportada para o modelo. 
   
       Por exemplo, os grupos de consumidores não são exportados para o modelo. Você precisa adicionar os grupos de consumidores ao modelo manualmente ou usar o [portal Azure](https://portal.azure.com) após a criação do hub. Há um exemplo de adicionar um grupo de consumidores a um modelo no artigo Use um modelo de Gestor de [Recursos Azure para configurar o encaminhamento de mensagens IoT Hub](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Copie os dispositivos do centro original para o clone. Isto está coberto na secção [Gestão dos dispositivos registados no centro IoT](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Como lidar com o encaminhamento de mensagens

Se o seu hub usa [encaminhamento personalizado,](iot-hub-devguide-messages-read-custom.md)exportar o modelo para o hub inclui a configuração de encaminhamento, mas não inclui os próprios recursos. Tem de escolher se transfere os recursos de encaminhamento para o novo local ou para os deixar no lugar e continuar a usá-los "como está". 

Por exemplo, digamos que tem um hub nos EUA Ocidentais que está a encaminhar mensagens para uma conta de armazenamento (também nos EUA Ocidentais), e quer mudar o centro para os EUA Orientais. Você pode mover o hub e tê-lo ainda encaminhando mensagens para a conta de armazenamento no Oeste dos EUA, ou você pode mover o hub e também mover a conta de armazenamento. Pode haver um pequeno desempenho atingido de mensagens de encaminhamento para recursos de ponta numa região diferente.

Você pode mover um hub que usa o encaminhamento de mensagens muito facilmente se você também não mover os recursos usados para os pontos finais de encaminhamento. 

Se o centro usa o encaminhamento de mensagens, tem duas opções. 

1. Mova os recursos utilizados para os pontos finais de encaminhamento para o novo local.

    * Você mesmo deve criar os novos recursos manualmente no [portal Azure](https://portal.azure.com) ou através da utilização de modelos de Gestor de Recursos. 

    * Deve renomear todos os recursos quando os criar no novo local, uma vez que têm nomes globalmente únicos. 
     
    * Tem de atualizar os nomes de recursos e as teclas de recursos no modelo do novo hub, antes de criar o novo hub. Os recursos devem estar presentes quando o novo hub for criado.

1. Não mova os recursos usados para os pontos finais de encaminhamento. Use-os "no lugar".

   * No passo em que edita o modelo, terá de recuperar as chaves para cada recurso de encaminhamento e colocá-las no modelo antes de criar o novo hub. 

   * O centro ainda refere os recursos de encaminhamento originais e encaminha as mensagens para eles como configurados.

   * Você terá um pequeno desempenho atingido porque o hub e os recursos de ponto final de encaminhamento não estão no mesmo local.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Prepare-se para migrar o centro para outra região

Esta secção fornece instruções específicas para migrar o centro.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Encontre o centro original e exporte-o para um modelo de recurso.

1. Assine no [portal Azure.](https://portal.azure.com) 

1. Vá aos **Grupos de Recursos** e selecione o grupo de recursos que contém o hub que pretende mover. Também pode ir à **Resources** e encontrar o centro dessa forma. Selecione o centro.

1. Selecione modelo de **exportação** a partir da lista de propriedades e configurações para o centro. 

   ![Screenshot mostrando o comando para exportar o modelo para o IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Selecione **Baixar** para descarregar o modelo. Guarde o ficheiro em algum lugar onde possa encontrá-lo de novo. 

   ![Screenshot mostrando o comando para descarregar o modelo para o IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Ver o modelo 

1. Vá à pasta Downloads (ou a qualquer pasta que tenha usado quando exportou o modelo) e encontre o ficheiro zip. Abra o ficheiro zip e `template.json`encontre o ficheiro chamado . Selecione-o e, em seguida, selecione Ctrl+C para copiar o modelo. Vá para uma pasta diferente que não esteja no ficheiro zip e colhe o ficheiro (Ctrl+V). Agora pode editá-lo.
 
    O exemplo que se segue é para um centro genérico sem configuração de encaminhamento. É um hub de nível S1 (com 1 unidade) chamado **ContosoTestHub29358** na região **westus**. Aqui está o modelo exportado.

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

Tem de fazer algumas alterações antes de poder usar o modelo para criar o novo hub na nova região. Utilize o [Código VS](https://code.visualstudio.com) ou um editor de texto para editar o modelo.

#### <a name="edit-the-hub-name-and-location"></a>Editar o nome e a localização do hub

1. Remova a secção de parâmetros na parte superior. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Mude o nome para utilizar o nome real (novo) em vez de o recuperar de um parâmetro (que removeu no passo anterior). 

    Para o novo hub, use o nome do hub original mais o *clone* de cordas para compor o novo nome. Comece por limpar o nome e a localização do centro.
    
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

    Em seguida, você vai descobrir que os valores para **o caminho** contêm o nome antigo do centro. Mude-os para usar o novo. Estes são os valores de percurso em **eventoSPontos HubEndchamados** **eventos** e **Eventos de Monitorização de Operações**.

    Quando terminar, a secção de pontos finais do seu centro de eventos deve ser assim:

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

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Atualize as chaves para os recursos de encaminhamento que não estão a ser movidos

Ao exportar o modelo de Gestor de Recursos para um hub configurado, verá que as chaves desses recursos não são fornecidas no modelo exportado -- a sua colocação é denotada por asteriscos. Deve preenchê-los indo para esses recursos no portal e recuperando as chaves **antes** de importar o modelo do novo hub e criar o hub. 

1. Recupere as chaves necessárias para qualquer um dos recursos de encaminhamento e coloque-as no modelo. Pode recuperar a chave(s) do recurso no [portal Azure](https://portal.azure.com). 

   Por exemplo, se estiver a encaminhar mensagens para um recipiente de armazenamento, encontre a conta de armazenamento no portal. Na secção Definições, selecione **teclas de acesso**e, em seguida, copie uma das teclas. Aqui está o que a chave parece quando você exporta o modelo pela primeira vez:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Depois de recuperar a chave da conta para a conta `AccountKey=****` de armazenamento, coloque-a no modelo na cláusula no lugar dos asteriscos. 

1. Para filas de ônibus de serviço, obtenha a chave de acesso partilhada correspondente ao SharedAccessKeyName. Aqui está a `SharedAccessKeyName` chave e o em json:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. O mesmo se aplica às ligações Service Bus Topics e Event Hub.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Criar os novos recursos de encaminhamento na nova localização

Esta secção só se aplica se estiver a mover os recursos utilizados pelo hub para os pontos finais de encaminhamento.

Se quiser mover os recursos de encaminhamento, tem de configurar manualmente os recursos no novo local. Pode criar os recursos de encaminhamento utilizando o [portal Azure,](https://portal.azure.com)ou exportando o modelo de Gestor de Recursos para cada um dos recursos utilizados pelo encaminhamento de mensagens, editando-os e importando-os. Após a configuração dos recursos, pode importar o modelo do hub (que inclui a configuração de encaminhamento).

1. Crie cada recurso utilizado pelo encaminhamento. Pode fazê-lo manualmente utilizando o [portal Azure,](https://portal.azure.com)ou criar os recursos utilizando modelos de Gestor de Recursos. Se quiser utilizar modelos, estes são os passos a seguir:

    1. Para cada recurso utilizado pelo encaminhamento, exporte-o para um modelo de Gestor de Recursos.
    
    1. Atualize o nome e a localização do recurso. 

    1. Atualize quaisquer referências cruzadas entre os recursos. Por exemplo, se criar um modelo para uma nova conta de armazenamento, precisa atualizar o nome da conta de armazenamento nesse modelo e qualquer outro modelo que o refira. Na maioria dos casos, a secção de encaminhamento no modelo para o hub é o único outro modelo que faz referência ao recurso. 

    1. Importar cada um dos modelos, que implanta cada recurso.

    Uma vez que os recursos utilizados pelo encaminhamento são configurados e funcionando, você pode continuar.

1. No modelo para o hub IoT, altere o nome de cada um dos recursos de encaminhamento para o seu novo nome e atualize a localização se necessário. 

Agora você tem um modelo que vai criar um novo hub que se parece quase exatamente com o antigo hub, dependendo de como você decidiu lidar com o encaminhamento.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Move - criar o novo hub na nova região carregando o modelo

Crie o novo hub no novo local usando o modelo. Se tiver recursos de encaminhamento que se vão mover, os recursos devem ser criados no novo local e as referências no modelo atualizados para combinar. Se não estiver a mover os recursos de encaminhamento, devem estar no modelo com as teclas atualizadas.

1. Assine no [portal Azure.](https://portal.azure.com)

1. Selecione **Criar um recurso**. 

1. Na caixa de pesquisa, coloque em "implementação do modelo" e selecione Enter.

1. Selecione **a implementação do modelo (implemente utilizando modelos personalizados)**. Isto leva-o a um ecrã para a implementação do Modelo. Selecione **Criar**. Verá este ecrã:

   ![Screenshot mostrando o comando para construir o seu próprio modelo](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Selecione **Construir o seu próprio modelo no editor, o**que lhe permite carregar o seu modelo a partir de um ficheiro. 

1. Selecione **ficheiro load**. 

   ![Screenshot mostrando o comando para carregar um ficheiro de modelo](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Procure o novo modelo que editou e selecione-o e, em seguida, **selecione Open**. Carrega o seu modelo na janela de edição. Selecione **Guardar**. 

   ![Screenshot mostrando carregando o modelo](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Preencha os campos seguintes.

   **Subscrição**: selecione a subscrição a utilizar.

   **Grupo**de recursos : criar um novo grupo de recursos num novo local. Se já tiver uma nova configurada, pode selecioná-la em vez de criar uma nova.

   **Localização**: Se selecionou um grupo de recursos existente, este é preenchido para que possa corresponder à localização do grupo de recursos. Se criou um novo grupo de recursos, esta será a sua localização.

   **Concordo com a caixa**de verificação : isto basicamente diz que concorda em pagar pelos recursos que está a criar.

1. Selecione o botão **Comprar.**

O portal agora valida o seu modelo e implanta o seu hub clonado. Se tiver dados de configuração de encaminhamento, será incluído no novo hub, mas irá apontar para os recursos na localização anterior.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Gestão dos dispositivos registados no centro ioT

Agora que tem o seu clone a funcionar, tem de copiar todos os dispositivos do centro original para o clone. 

Há várias maneiras de conseguir isto. Ou usou originalmente o Serviço de Provisionamento de [Dispositivos (DPS)](/azure/iot-dps/about-iot-dps)para fornecer os dispositivos, ou não o fez. Se o fez, isto não é difícil. Se não o fez, isto pode ser muito complicado. 

Se não usou dPS para fornecer os seus dispositivos, pode saltar a secção seguinte e começar por [Utilizar a Importação/Exportação para mover os dispositivos para o novo hub](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Utilização de DPS para reabastecer os dispositivos no novo hub

Para utilizar o DPS para mover os dispositivos para o novo local, consulte [como reabastecer os dispositivos](../iot-dps/how-to-reprovision.md). Quando terminar, pode ver os dispositivos no [portal Azure](https://portal.azure.com) e verificar se estão no novo local.

Vá ao novo hub usando o [portal Azure.](https://portal.azure.com) Selecione o seu hub e, em seguida, selecione **Dispositivos IoT**. Vês os dispositivos que foram reprovisionados para o centro clonado. Também pode ver as propriedades do centro clonado. 

Se implementou o encaminhamento, teste e certifique-se de que as suas mensagens são encaminhadas para os recursos corretamente.

### <a name="committing-the-changes-after-using-dps"></a>Comprometer as alterações após a utilização de DPS

Esta mudança foi cometida pelo serviço DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Reverter as alterações após a utilização do DPS. 

Se quiser reverter as alterações, reforte os dispositivos do novo hub para o antigo.

Já acabou de migrar o seu hub e os seus dispositivos. Pode saltar para [a Limpeza.](#clean-up)

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Utilização da Importação-Exportação para mover os dispositivos para o novo hub

A aplicação tem como alvo .NET Core, para que possa executá-la no Windows ou no Linux. Você pode baixar a amostra, recuperar as cordas de ligação, definir as bandeiras para que pedaços você quer executar, e executá-lo. Podes fazer isto sem nunca abrir estoque.

### <a name="downloading-the-sample"></a>Descarregar a amostra

1. Utilize as amostras IoT C# desta página: [Amostras Azure IoT para C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Faça o download do ficheiro zip e desaperte-o no seu computador. 

1. O código pertinente está em ./iot-hub/Samples/service/ImportExportDevicesSample. Não precisa de ver ou editar o código para executar a aplicação.

1. Para executar a aplicação, especifique três cordas de ligação e cinco opções. Passa estes dados como argumentos de linha de comando ou utiliza variáveis ambientais, ou utiliza uma combinação dos dois. Vamos passar as opções como argumentos de linha de comando, e as cordas de ligação como variáveis ambientais. 

   A razão para isso é porque as cordas de ligação são longas e desajeitadas, e pouco prováveis de mudar, mas você pode querer mudar as opções e executar a aplicação mais de uma vez. Para alterar o valor de uma variável ambiental, tem de fechar a janela de comando e o Visual Studio ou o Código VS, seja qual for o uso. 

### <a name="options"></a>Opções

Aqui estão as cinco opções que especifica quando executa a aplicação. Vamos colocar isto na linha de comando num minuto.

*   **adicionarDispositivos** (argumento 1) -- detete isto como verdadeiro se quiser adicionar dispositivos virtuais que são gerados para si. Estes são adicionados ao centro de origem. Além disso, detete **o numToAdd** (argumento 2) para especificar quantos dispositivos pretende adicionar. O número máximo de dispositivos que pode registar num hub é de um milhão. O objetivo desta opção é testar.

*   **copiarDispositivos** (argumento 3) -- detetete isto como verdadeiro para copiar os dispositivos de um hub para outro. 

*   **eliminarSourceDevices** (argumento 4) -- detete te de forma verdadeira para eliminar todos os dispositivos registados no centro de origem. Recomendamos que esteja certo de que todos os dispositivos foram transferidos antes de executar isto. Uma vez apagado os dispositivos, não pode recuperá-los.

*   **eliminarDestDevices** (argumento 5) -- detetete te de forma verdadeira para eliminar todos os dispositivos registados no centro de destino (o clone). Talvez queira fazer isto se quiser copiar os dispositivos mais de uma vez. 

O comando básico será *executado dotnet* - isto diz .NET para construir o arquivo csproj local e, em seguida, executá-lo. Adicione os seus argumentos de linha de comando até ao fim antes de o executar. 

A sua linha de comando será parecida com estes exemplos:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Utilização de variáveis ambientais para as cordas de ligação

1. Para executar a amostra, precisa das cordas de ligação aos antigos e novos centros IoT, e a uma conta de armazenamento que pode usar para ficheiros de trabalho temporários. Armazenaremos os valores para estes em variáveis ambientais.

1. Para obter os valores de cadeia de ligação, inscreva-se no [portal Azure](https://portal.azure.com). 

1. Coloque as cordas de ligação em algum lugar onde possa recuperá-las, como o NotePad. Se copiar o seguinte, pode colar as cordas de ligação diretamente para onde vão. Não adicione espaços em torno do sinal igual, ou altera o nome variável. Além disso, não precisa de citações duplas em torno das cordas de ligação. Se colocar cotações em torno da cadeia de ligação da conta de armazenamento, não funcionará.

   Para windows, é assim que se define as variáveis ambientais:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Para o Linux, é assim que se define as variáveis ambientais:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Para as cordas de ligação do hub IoT, vá a cada centro no portal. Pode procurar em **Recursos** para o centro. Se conhece o Grupo de Recursos, pode ir a **grupos de Recursos,** selecionar o seu grupo de recursos e, em seguida, selecionar o hub a partir da lista de ativos desse grupo de recursos. 

1. Selecione as políticas de **acesso partilhada** saem das Definições para o hub e, em seguida, selecione **iothubowner** e copie uma das cordas de ligação. Faça o mesmo para o centro de destino. Adicione-os aos comandos SET apropriados.

1. Para a cadeia de ligação à conta de armazenamento, encontre a conta de armazenamento em **Recursos** ou sob o seu **grupo de Recursos** e abra-a. 
   
1. Na secção Definições, selecione **teclas de acesso** e copie uma das cordas de ligação. Coloque a cadeia de ligação no seu ficheiro de texto para o comando SET apropriado. 

Agora tem as variáveis ambientais num ficheiro com os comandos SET, e sabe quais são os seus argumentos de linha de comando. Vamos ver a amostra.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Executar a aplicação da amostra e usar argumentos de linha de comando

1. Abra uma janela da linha de comandos. Selecione Windows `command prompt` e escreva para obter a janela de solicitação de comando.

1. Copie os comandos que configuram as variáveis ambientais, uma de cada vez, e cole-as na janela de instrução de comando e selecione Enter. Quando terminar, digite `SET` a janela de solicitação de comando para ver as variáveis ambientais e os seus valores. Depois de copiá-los para a janela de instrução, não terá que copiá-los novamente, a menos que abra uma nova janela de comando.

1. Na janela de solicitação de comando, altere os diretórios até que esteja em ./ImportExportDevicesSample (onde existe o ficheiro ImportExportDevicesSample.csproj). Em seguida, digite o seguinte e inclua os seus argumentos de linha de comando.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    O comando dotnet constrói e executa a aplicação. Como está a passar as opções quando executa a aplicação, pode alterar os valores das mesmas sempre que executar a aplicação. Por exemplo, pode querer executá-lo uma vez e criar novos dispositivos, em seguida, executá-lo novamente e copiar esses dispositivos para um novo hub, e assim por diante. Também pode executar todos os passos na mesma execução, embora recomendamos que não alelete nenhum dispositivo até ter a certeza de que está acabado com a clonagem. Aqui está um exemplo que cria 1000 dispositivos e depois copia-os para o outro centro.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Depois de verificar se os dispositivos foram copiados com sucesso, pode remover os dispositivos do centro de origem como este:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Executar a aplicação da amostra usando o Estúdio Visual

1. Se quiser executar a aplicação no Visual Studio, altere o seu diretório atual para a pasta onde reside o ficheiro IoTHubServiceSamples.sln. Em seguida, executar este comando na janela de comando pronta para abrir a solução em Estúdio Visual. Deve fazê-lo na mesma janela de comando onde define as variáveis ambientais, para que essas variáveis sejam conhecidas.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Clique à direita no projeto *ImportExportDevicesSample* e selecione **set como projeto de arranque**.    
    
1. Detete as variáveis no topo da Program.cs na pasta ImportExportDevicesSample para as cinco opções.

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

1. Selecione F5 para executar a aplicação. Depois de terminar a corrida, pode ver os resultados.

### <a name="view-the-results"></a>Ver os resultados 

Pode ver os dispositivos no [portal Azure](https://portal.azure.com) e verificar se estão no novo local.

1. Vá ao novo hub usando o [portal Azure.](https://portal.azure.com) Selecione o seu hub e, em seguida, selecione **Dispositivos IoT**. Vês os dispositivos que acabaste de copiar do antigo centro para o centro clonado. Também pode ver as propriedades do centro clonado. 

1. Verifique se há erros de importação/exportação indo para a conta `devicefiles` de `ImportErrors.log`armazenamento Azure no [portal Azure](https://portal.azure.com) e procurando no recipiente para o . Se este ficheiro estiver vazio (o tamanho é 0), não houve erros. Se tentar importar o mesmo dispositivo mais de uma vez, rejeita o dispositivo pela segunda vez e adiciona uma mensagem de erro ao ficheiro de registo.

### <a name="committing-the-changes"></a>Comprometer as alterações 

Neste momento, copiou o seu hub para o novo local e emigrou os dispositivos para o novo clone. Agora tens de fazer alterações para que os dispositivos funcionem com o centro clonado.

Para comprometer as alterações, aqui estão os passos que precisa de executar: 

* Atualize cada dispositivo para alterar o nome de anfitrião do IoT Hub para indicar o nome de anfitrião do IoT Hub para o novo hub. Deve fazê-lo utilizando o mesmo método utilizado quando forprovisionou o dispositivo pela primeira vez.

* Altere quaisquer aplicações que tenha que se refiram ao antigo hub para apontar para o novo hub.

* Depois de terminar, o novo centro deve estar a funcionar. O antigo hub não deve ter dispositivos ativos e estar em estado dedesconectado. 

### <a name="rolling-back-the-changes"></a>Reverter as alterações

Se decidir reverter as alterações, aqui estão os passos a executar:

* Atualize cada dispositivo para alterar o nome de anfitrião do IoT Hub para indicar o nome de anfitrião Do Hub IoT para o antigo hub. Deve fazê-lo utilizando o mesmo método utilizado quando forprovisionou o dispositivo pela primeira vez.

* Altere quaisquer aplicações que tenha que se refiram ao novo hub para apontar para o antigo hub. Por exemplo, se estiver a utilizar o Azure Analytics, poderá ter de reconfigurar a sua [entrada azure Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Apague o novo centro. 

* Se tiver recursos de encaminhamento, a configuração no antigo hub deve ainda apontar para a configuração correta de encaminhamento, e deve trabalhar com esses recursos após o reinício do hub.

### <a name="checking-the-results"></a>Verificação dos resultados 

Para verificar os resultados, altere a sua solução IoT para apontar para o seu hub na nova localização e executá-la. Por outras palavras, execute as mesmas ações com o novo hub que realizou com o hub anterior e certifique-se de que funcionam corretamente. 

Se implementou o encaminhamento, teste e certifique-se de que as suas mensagens são encaminhadas para os recursos corretamente.

## <a name="clean-up"></a>Limpeza

Não limpe até ter a certeza de que o novo hub está a funcionar e os dispositivos estão a funcionar corretamente. Certifique-se também de testar o encaminhamento se estiver a utilizar essa funcionalidade. Quando estiver pronto, limpe os recursos antigos executando estes passos:

* Se ainda não o fez, apague o velho centro. Isto remove todos os dispositivos ativos do centro.

* Se tiver recursos de encaminhamento que mudou para o novo local, pode eliminar os antigos recursos de encaminhamento.

## <a name="next-steps"></a>Passos seguintes

Clonaste um hub ioT num novo centro numa nova região, com os dispositivos. Para obter mais informações sobre a realização de operações a granel contra o registo de identidade num Hub IoT, consulte as identidades do [dispositivo IoT Hub de importação e exportação](iot-hub-bulk-identity-mgmt.md)a granel.

Para mais informações sobre o IoT Hub e desenvolvimento para o hub, consulte os seguintes artigos.

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)

* [Tutorial de encaminhamento do Hub IoT](tutorial-routing.md)

* [Visão geral da gestão do dispositivo IoT Hub](iot-hub-device-management-overview.md)

* Se pretender implementar a aplicação da amostra, consulte a implementação da [aplicação .NET Core](https://docs.microsoft.com/dotnet/core/deploying/index).

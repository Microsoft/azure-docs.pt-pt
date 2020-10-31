---
title: Executar Azure Stream Analytics em Azure Stack (Pré-visualização)
description: Crie um trabalho de borda Azure Stream Analytics e implemente-o para o hub Azure Stack através do tempo de execução IoT Edge.
ms.service: stream-analytics
author: raan
ms.author: raan
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: 21cf432576829b575d70a94227f28df373a4d899
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126163"
---
# <a name="run-azure-stream-analytics-on-azure-stack-preview"></a>Executar Azure Stream Analytics em Azure Stack (Pré-visualização)

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização e não é recomendada para utilização na produção.

Pode executar a Azure Stream Analytics no Azure Stack Hub como um módulo IoT Edge. As configurações foram adicionadas ao módulo IoT Edge que lhe permite interagir com o armazenamento de bolhas, os Centros de Eventos e os Hubs IoT em execução numa subscrição do Azure Stack Hub, permitindo URLs personalizados encontrados em cada emprego do Azure Stack Hub.

Com o Stream Analytics no Azure Stack, pode construir arquiteturas verdadeiramente híbridas para processamento de streaming na sua própria nuvem privada e autónoma – que pode ser conectada ou desligada com aplicações nativas da nuvem usando serviços Azure consistentes no local. 

Este artigo mostra-lhe como transmitir dados do IoT Hub ou Do Event Hub para outro Event Hub ou Blob Storage numa subscrição do Azure Stack Hub e processá-lo com Stream Analytics.

## <a name="set-up-environments"></a>Configurar ambientes

Azure Stream Analytics é um serviço híbrido no Azure Stack Hub. É um módulo IoT Edge que está configurado em Azure mas pode ser executado no Azure Stack Hub.  

Se for novo no Azure Stack Hub ou no IoT Edge, siga as instruções abaixo para configurar ambientes.

### <a name="prepare-the-azure-stack-hub-environment"></a>Prepare o ambiente Azure Stack Hub

Crie uma subscrição do Azure Stack Hub. Para mais informações, consulte o [tutorial para criar uma subscrição do Azure Stack Hub.](/azure-stack/user/azure-stack-subscribe-services/)

Se quiser avaliar o Azure Stack Hub no seu próprio servidor, pode utilizar o Kit de Desenvolvimento de Pilhas Azure (ASDK).  Para obter mais informações sobre a ASDK, consulte a visão geral da [ASDK](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do IoT Edge

Para executar o Azure Stream Analytics no Azure Stack Hub, o seu dispositivo deve ter o tempo de funcionamento IoT Edge e deve ter conectividade de rede com o Azure Stack Hub ou ser um VM a funcionar no Azure Stack Hub. O tempo de execução IoT Edge permite que os trabalhos stream Analytics Edge se integrem com Azure Storage e Azure Event Hubs que estão a funcionar no seu Azure Stack Hub. Para mais informações, consulte [a Azure Stream Analytics no IoT Edge](stream-analytics-edge.md) 

Além de ter acesso à rede aos recursos do Azure Stack Hub, o dispositivo IoT Edge (ou VM) precisa de acesso ao Azure IoT Hub na nuvem pública Azure para configurar o módulo Stream Analytics. 

Os seguintes guias mostram como configurar o tempo de execução IoT Edge no seu dispositivo ou VM:

* [Instalar o runtime do Azure IoT Edge no Windows](../iot-edge/how-to-install-iot-edge.md)
* [Instalar o runtime do Azure IoT Edge em sistemas Linux baseados em Debian](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Criar um trabalho Azure Stream Analytics Edge

Os trabalhos da ASA Edge funcionam em contentores implantados nos dispositivos Azure IoT Edge. São compostas por duas partes:
* Uma parte em nuvem responsável pela definição de emprego: os utilizadores definem entradas, saídas, consultas e outras configurações (eventos fora de ordem, etc.) na nuvem.
* Um módulo a funcionar nos seus dispositivos IoT. Contém o motor ASA e recebe a definição de trabalho da nuvem.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Quando cria um trabalho do Azure Stream Analytics para ser executado num dispositivo IoT Edge, o trabalho tem de ser armazenado de forma a poder ser chamado a partir do dispositivo. Pode utilizar uma conta de armazenamento Azure existente ou criar uma nova.
1. No portal Azure, vá para **criar um recurso > conta de armazenamento > armazenamento - blob, arquivo, mesa, fila** .
2. Indique os valores seguintes para criar a conta de armazenamento:

   | Campo | Valor |
   | --- | --- |
   | Nome | Introduza um nome exclusivo para a conta de armazenamento. |
   | Localização | Escolha uma localização perto de si.|
   | Subscrição | Escolha a mesma subscrição do hub IoT.|
   | Grupo de Recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os recursos de teste que cria durante os quickstarts e tutoriais [IoT Edge.](../iot-edge/quickstart.md) Por exemplo, **IoTEdgeResources** . |

3. Mantenha os valores predefinidos nos outros campos e selecione **Criar** .


### <a name="create-a-new-job"></a>Criar uma nova tarefa

1. No portal Azure, vá para **criar um recurso > Internet of Things > Stream Analytics Job** .
2. Indique os valores seguintes para criar a conta de armazenamento:

   | Campo | Valor |
   | --- | --- |
   | Nome da Tarefa | Indique um nome para o trabalho. Por exemplo, **IoTEdgeJob** |
   | Subscrição | Escolha a mesma subscrição do hub IoT.|
   | Grupo de Recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os recursos de teste que cria durante os quickstarts e tutoriais [IoT Edge.](../iot-edge/quickstart.md) Por exemplo, **IoTEdgeResources** . |
   | Localização | Escolha uma localização perto de si. |
   | Ambiente de Hospedagem | Selecione **Edge** . |

3. Selecione **Criar** .

### <a name="configure-your-job"></a>Configurar o trabalho

Quando o trabalho do Stream Analytics estiver criado no portal do Azure, pode configurá-lo com uma entrada, uma saída e uma consulta para ser executada nos dados que são transmitidos. Pode especificar manualmente as entradas de um Hub IoT ou de um Centro de Eventos numa subscrição do Azure Stack Hub.

1. Navegue para o trabalho do Stream Analytics no portal do Azure.
2. Em **Configuração,** selecione **as definições da conta de Armazenamento** e escolha a conta de armazenamento que criou no passo anterior.
   > [!div class="mx-imgBorder"]
   > [![Definição de conta de armazenamento de emprego ](media/on-azure-stack/storage-account-settings.png)](media/on-azure-stack/storage-account-settings.png#lightbox)
3. Em **Job Topology** , selecione **Entradas** e adicione a entrada **de fluxo.**
4. Escolha **IoT Hub,** **Event Hub** ou Edge **Hub** da lista de drop-down. 
5. Se a entrada for um Event Hub ou IoT Hub numa subscrição do Azure Stack Hub, forneça informações manualmente, como mostrado abaixo.

   #### <a name="event-hub"></a>Hub de Eventos

   | Campo | Valor |
   | --- | --- |
   | Alias de entrada | Um nome amigável que usa na consulta do trabalho para fazer referência a esta entrada. |
   | Espaço de nome de ônibus de serviço | O espaço de nome é um recipiente para um conjunto de entidades de mensagens. Quando cria um novo centro de eventos, também cria o espaço de nomes. (Exemplo: *sb:// <Event Hub Name> .eventhub.shanghai.azurestack.corp.microsoft.com)* |
   | Nome do Hub de Eventos | O nome do centro de eventos para usar como entrada. |
   | Nome da política do Hub de Eventos | A política de acesso partilhado que dá acesso ao Centro de Eventos. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. Esta opção é preenchida automaticamente, a menos que selecione a opção para fornecer manualmente as definições do Centro de Eventos. |
   | Chave política do Centro de Eventos | A chave de acesso partilhada utilizada para autorizar o acesso ao Centro de Eventos. Esta opção é preenchida automaticamente a menos que selecione a opção para fornecer manualmente as definições do Centro de Eventos. Pode encontrá-lo nas definições do Event Hub. |
   | Grupo de consumidores Event Hub (opcional) | É altamente recomendado utilizar um grupo de consumidores distinto para cada trabalho stream Analytics. Esta cadeia identifica o grupo de consumidores para usar para ingerir dados do centro de eventos. Se nenhum grupo de consumidores for especificado, o trabalho stream Analytics utiliza o grupo de consumidores $Default. |
   | Contagem de divisórias | A contagem de divisórias é o número de divisórias num Centro de Eventos. |

   > [!div class="mx-imgBorder"]
   > [![Entrada do Centro de Eventos ](media/on-azure-stack/event-hub-input.png)](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT Hub

   | Campo | Valor |
   | --- | --- |
   | Alias de entrada | Um nome amigável que usa na consulta do trabalho para fazer referência a esta entrada. |
   | IoT Hub | O nome do Hub IoT para usar como entrada. (Exemplo: *<IoT Hub Name> .shanghai.azurestack.corp.microsoft.com)* |
   | Nome da política de acesso partilhado | A política de acesso partilhado que dá acesso ao IoT Hub. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
   | Chave de política de acesso compartilhado | A chave de acesso partilhada usada para autorizar o acesso ao IoT Hub. Esta opção é automaticamente preenchida a menos que selecione a opção para fornecer manualmente as definições do Iot Hub. |
   | Grupo de consumidores (opcional) | É altamente recomendável que utilize um grupo de consumidores diferente para cada trabalho stream Analytics. O grupo de consumidores é utilizado para ingerir dados do IoT Hub. O Stream Analytics utiliza o grupo de consumidores $Default, a menos que especifique o contrário. |
   | Contagem de divisórias | A contagem de divisórias é o número de divisórias num Centro de Eventos. |

   > [!div class="mx-imgBorder"]
   > [Entrada IoT ![ ](media/on-azure-stack/iot-hub-input.png) Hub ](media/on-azure-stack/iot-hub-input.png#lightbox)

6. Mantenha os valores predefinidos nos outros campos e selecione Guardar.
7. Em Topologia do Trabalho, abra Saídas e selecione Adicionar.
8. Escolha Blob Storage, Event Hub ou Edge Hub na lista de drop-down.
9. Se a saída for um Event Hub ou Blob Storage numa subscrição do Azure Stack Hub, por favor forneça informações manualmente como mostrado abaixo.

   #### <a name="event-hub"></a>Hub de Eventos

   | Campo | Valor |
   | --- | --- |
   | Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para este centro de eventos. |
   | Espaço de nome de ônibus de serviço | Um contentor para um conjunto de entidades de mensagens. Quando criou um novo centro de eventos, também criou um espaço de nome de autocarro de serviço. (Exemplo: *sb:// <Event Hub Name> .eventhub.shanghai.azurestack.corp.microsoft.com)* |
   | Nome do Hub de Eventos | O nome da saída do seu centro de eventos. |
   | Nome da política do Hub de Eventos | A política de acesso partilhado, que pode criar no separador Configure do centro de eventos. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
   | Chave política do Centro de Eventos | A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nome do centro de eventos. |

   > [!div class="mx-imgBorder"]
   > [![Saída do Hub de Eventos ](media/on-azure-stack/event-hub-output.png)](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Armazenamento de Blobs 

   | Campo | Valor |
   | --- | --- |
   | Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para este armazenamento de bolhas. |
   | Conta de armazenamento | O nome da conta de armazenamento para onde está a enviar a sua produção. (Exemplo: *<Storage Account Name> .blob.shanghai.azurestack.corp.microsoft.com* ) |
   | Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento. Esta opção é automaticamente preenchida a menos que selecione a opção de fornecer manualmente as definições de armazenamento Blob. |

> [!NOTE]
> O formato parquet não é suportado para trabalhos de borda no Azure Stack Hub. Para filas mínimas e tempo máximo, utilize 0 ou deixe-as em branco.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Implementar stream analytics em um VM ou dispositivo ligado à Pilha Azure

1. No portal Azure, abrir o Hub IoT. Navegue para **IoT Edge** e clique no dispositivo (VM) que pretende apontar para esta implementação.
2. Selecione **Definir módulos** . Em seguida, **selecione + Adicionar** e escolha O Módulo **Azure Stream Analytics** . 
3. Selecione a subscrição e o trabalho Steam Analytics Edge que criou. Clique **em Guardar** e selecionar **Seguinte:Rotas** .

   > [!div class="mx-imgBorder"]
   > [![Adicionar Módulos ](media/on-azure-stack/edge-modules.png)](media/on-azure-stack/edge-modules.png#lightbox)

4. Clique **em Rever + criar >** .
5. No **Review + criar** passo, selecione **Criar.** 
   > [!div class="mx-imgBorder"]
   > [![Manifesto ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. Confirme se o módulo é adicionado à lista.
   > [!div class="mx-imgBorder"]
   > [Página de ![ ](media/on-azure-stack/edge-deployment.png) implementação ](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Passos seguintes
- [Azure Stream Analytics no IoT Edge](./stream-analytics-edge.md)
- [Desenvolver trabalhos stream Analytics Edge](/stream-analytics-query/stream-analytics-query-language-reference)
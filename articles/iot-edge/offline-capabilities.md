---
title: Opere os dispositivos offline - Azure IoT Edge | Documentos da Microsoft
description: Compreenda como os dispositivos do IoT Edge e os módulos podem funcionar sem ligação à internet por longos períodos de tempo e, como IoT Edge pode permitir que os dispositivos de IoT regulares operar offline também.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4f3e5c1566271573b43e24a1749b42daa7530555
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051962"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Compreender as capacidades offline expandidas para dispositivos do IoT Edge, módulos e dispositivos de subordinados

O Azure IoT Edge suporta operações offline expandidas nos seus dispositivos IoT Edge e permite operações offline nos dispositivos de subordinado de não - IoT Edge também. Desde que um dispositivo IoT Edge teve uma oportunidade para se ligar ao IoT Hub, ele e quaisquer dispositivos de subordinados podem continuar a função intermitente com ou sem ligação à internet. 


## <a name="how-it-works"></a>Como funciona

Quando um dispositivo IoT Edge entra em modo offline, o hub IoT Edge assume três funções. Em primeiro lugar, ele armazena todas as mensagens que iriam a montante e salva-os até que o dispositivo volta a ligar. Em segundo lugar, ele atua em nome do IoT Hub para autenticar dispositivos de subordinados e de módulos para que eles podem continuar a funcionar. Em terceiro lugar, ele permite a comunicação entre dispositivos de subordinados que normalmente seria passar pelo IoT Hub. 

O exemplo seguinte mostra como um cenário de IoT Edge opera no modo offline:

1. **Configurar dispositivos**

   Dispositivos IoT Edge têm automaticamente capacidades offline ativadas. Para expandir esse recurso para outros dispositivos de IoT, tem de declarar uma relação principal-subordinado entre os dispositivos no IoT Hub. Em seguida, configurar os dispositivos de subordinados para confiar no respetivo dispositivo principal atribuído e encaminhar as comunicações de dispositivo para a cloud através do pai como um gateway. 

2. **Sincronização com o IoT Hub**

   Pelo menos uma vez após a instalação do runtime do IoT Edge, o dispositivo do IoT Edge tem de estar online para sincronizar com o IoT Hub. Nesta sincronização, o dispositivo do IoT Edge obtém os detalhes sobre todos os dispositivos filho atribuídos ao mesmo. O dispositivo do IoT Edge com a segurança também atualiza a respetiva cache local para permitir operações offline e obtém as definições para o armazenamento local de mensagens de telemetria. 

3. **Ficam offline**

   Enquanto estiver desconectado do IoT Hub, o dispositivo do IoT Edge, os módulos implementados e quaisquer dispositivos de IoT de elementos subordinados podem operar indefinidamente. Módulos e dispositivos de subordinado podem iniciar e reiniciar ao autenticar com o hub IoT Edge ao mesmo tempo offline. Telemetria vinculada a montante para o IoT Hub é armazenada localmente. A comunicação entre os módulos ou entre dispositivos de IoT de subordinado é mantida por meio de métodos diretos ou mensagens. 

4. **Voltar a ligar e volte a sincronizar com o IoT Hub**

   Assim que a ligação com o IoT Hub é restaurada, o dispositivo do IoT Edge é sincronizada novamente. Mensagens armazenadas localmente são entregues na mesma ordem em que foram armazenados. As diferenças entre dispositivos e as propriedades pretendidas e comunicadas dos módulos são reconciliadas. O dispositivo do IoT Edge atualiza todas as alterações ao seu conjunto de dispositivos de IoT de subordinados atribuído.

## <a name="restrictions-and-limits"></a>Restrições e limites

As capacidades offline expandidas descritas neste artigo estão disponíveis no [IoT Edge versão 1.0.4 ou superior](https://github.com/Azure/azure-iotedge/releases). Versões anteriores com um subconjunto de funcionalidades offline. Existente do IoT Edge dispositivos que não têm recursos offline expandidos não podem ser atualizados, alterando a versão de tempo de execução, mas têm de ser reconfigurados com uma nova identidade de dispositivo do IoT Edge para obter esses recursos. 

O suporte offline expandido está disponível em todas as regiões onde o IoT Hub está disponível, **exceto** E.U.A. Leste.

Apenas os dispositivos de IoT de Edge podem ser adicionados como dispositivos de subordinados. 

Dispositivos IoT Edge e os respetivos dispositivos atribuídos filho podem funcionar indefinidamente offline depois da sincronização inicial e de uso individual. No entanto, o armazenamento de mensagens depende do tempo de duração definição (TTL) e o espaço disponível no disco para armazenar as mensagens. 

## <a name="set-up-parent-and-child-devices"></a>Configurar dispositivos principais e subordinados

Para um dispositivo IoT Edge expandir as suas capacidades offline expandidas para dispositivos de IoT de subordinado, terá de concluir dois passos. Em primeiro lugar, declare principal-subordinado relações no portal do Azure. Em segundo lugar, crie uma relação de confiança entre o dispositivo de principal e todos os dispositivos filho, em seguida, configurar comunicações de dispositivo para a cloud para percorrer o elemento principal como um gateway. 

### <a name="assign-child-devices"></a>Atribuir dispositivos de subordinados

Dispositivos de subordinado podem ser qualquer dispositivo de IoT de Edge registado no mesmo IoT Hub. Dispositivos de principal podem ter vários dispositivos de subordinado, mas um dispositivo de filho tem apenas um elemento principal. Existem três opções para definir os dispositivos de subordinados para um dispositivo de limite: através do portal do Azure, utilizar a CLI do Azure ou utilizar o SDK do serviço IoT Hub. 

As secções seguintes fornecem exemplos de como pode declarar a relação de pai/filho no IoT Hub para dispositivos de IoT existentes. Se estiver a criar novas identidades de dispositivo para o menor dispositivos, veja [autenticar um dispositivo hub IoT do Azure de downstream](how-to-authenticate-downstream-device.md) para obter mais informações.

#### <a name="option-1-iot-hub-portal"></a>Opção 1: Portal do IoT Hub

É possível declarar a relação principal-subordinado ao criar um novo dispositivo. Ou para os dispositivos existentes, pode declarar a relação da página de detalhes de dispositivo do principal dispositivo IoT Edge ou o dispositivo de IoT filho. 

   ![Gerir dispositivos de subordinado a partir da página de detalhes do dispositivo IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Opção 2: Utilize o `az` ferramenta da linha de comandos

Utilizar o [interface de linha de comandos do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) com [extensão de IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 ou mais recente), pode gerir as relações de subordinados de principal com o [identidade de dispositivo](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) subcomandos. O exemplo abaixo utiliza uma consulta para atribuir todos os dispositivos de IoT de Edge no hub para ser dispositivos de subordinados de um dispositivo IoT Edge. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

Pode modificar os [consulta](../iot-hub/iot-hub-devguide-query-language.md) para selecionar um subconjunto de diferente de dispositivos. O comando pode demorar vários segundos, se especificar um grande conjunto de dispositivos.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opção 3: Utilizar o SDK do serviço IoT Hub 

Por fim, pode gerir as relações de subordinados principal por meio de programação através de um C#, Java ou node. js SDK do serviço do Hub IoT. Aqui está uma [exemplo de atribuição de um dispositivo de subordinado](https://aka.ms/set-child-iot-device-c-sharp) utilizando o C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configurar o dispositivo principal como um gateway

Pode considerar uma relação de pai/filho como um gateway transparente, em que o dispositivo filho tem sua própria identidade do IoT Hub, mas comunica através da cloud através do respetivo elemento principal. Para uma comunicação segura, o dispositivo de subordinado tem de ser capaz de verificar que o dispositivo principal vem de uma origem fidedigna. Caso contrário, das entidades de segurança podem configurar dispositivos maliciosos para representar pais e interceptar comunicações. 

Uma forma de criar esta relação de confiança é descrita em detalhe nos seguintes artigos: 
* [Configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md)
* [Ligar um dispositivo de downstream (filho) para um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Especifique os servidores DNS 

Para melhorar a robustez, é altamente recomendável que especifique os endereços de servidor DNS utilizados no seu ambiente. Veja as duas opções para [defina o servidor DNS no artigo de resolução de problemas](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Definições opcionais de offline

Se os dispositivos ficam offline, o dispositivo do IoT Edge principal armazena todas as mensagens do dispositivo para a cloud até que a ligação for restabelecida. O módulo do IoT Edge hub gere o armazenamento e o reencaminhamento de mensagens offline. Para dispositivos que podem ir offline por longos períodos de tempo, Otimize o desempenho ao configurar duas definições de hub do IoT Edge. 

Em primeiro lugar, aumente o tempo de definição em direto, para que o hub IoT Edge irá manter as mensagens tempo suficiente para o seu dispositivo para restabelecer a ligação. Em seguida, adicione o espaço em disco adicional para armazenamento de mensagens. 

### <a name="time-to-live"></a>Time to live

O tempo de duração a definição é a quantidade de tempo (em segundos) que uma mensagem pode aguardar para ser entregue antes de expirar. A predefinição é 7200 segundos (duas horas). O valor máximo é limitado apenas pelo valor máximo de uma variável integer, que é cerca de 2 mil milhões. 

Esta definição é uma propriedade pretendida do hub IoT Edge, que é armazenado em duplo do módulo. Pode configurá-la no portal do Azure ou diretamente no manifesto de implantação. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>Armazenamento offline adicional

As mensagens são armazenadas por padrão no sistema de ficheiros de contentor do hub IoT Edge. Se essa quantidade de armazenamento não é suficiente para as suas necessidades offline, pode dedicar armazenamento local no dispositivo IoT Edge. Crie uma variável de ambiente para o hub do IoT Edge que aponta para uma pasta de armazenamento no contentor. Em seguida, utilize as opções de criar para vincular essa pasta de armazenamento para uma pasta no computador anfitrião. 

Pode configurar as variáveis de ambiente e as opções de criar para o módulo do IoT Edge hub no portal do Azure no **configurar definições de Runtime do Edge avançadas** secção. Em alternativa, pode configurá-lo diretamente no manifesto de implantação. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Substitua `<HostStoragePath>` e `<ModuleStoragePath>` com o armazenamento de anfitrião e o módulo de caminho de armazenamento do caminho; anfitrião e módulo tem de ser um caminho absoluto. As opções de criar, ligar os caminhos de armazenamento do anfitrião e o módulo em conjunto. Em seguida, crie uma variável de ambiente que aponta para o caminho do módulo de armazenamento.  

Por exemplo, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` significa que o diretório **/etc/iotedge/storage** no anfitrião do sistema é mapeado para o diretório **/iotedge/armazenamento/** no contentor. Ou outro exemplo para sistemas Windows, `"Binds":["C:\\temp:C:\\contemp"]` significa que o diretório **c:\\temp** no anfitrião do sistema é mapeado para o diretório **c:\\contemp** no contentor. 

Também pode encontrar mais detalhes sobre criar opções partir [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como configurar um gateway transparente para as suas ligações de dispositivo de pai/filho: 

* [Configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um downstream dispositivo hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)

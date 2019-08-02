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
ms.openlocfilehash: 4a46128d3b0e77ff7921e1f4875c318a95309769
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598614"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Entender os recursos offline estendidos para dispositivos IoT Edge, módulos e dispositivos filho

O Azure IoT Edge dá suporte a operações offline estendidas em seus dispositivos IoT Edge e habilita operações offline em dispositivos não IoT Edge filhos também. Desde que um dispositivo IoT Edge teve uma oportunidade para se ligar ao IoT Hub, ele e quaisquer dispositivos de subordinados podem continuar a função intermitente com ou sem ligação à internet. 


## <a name="how-it-works"></a>Como funciona

Quando um dispositivo IoT Edge entra no modo offline, o Hub de IoT Edge assume três funções. Em primeiro lugar, ele armazena todas as mensagens que iriam a montante e salva-os até que o dispositivo volta a ligar. Em segundo lugar, ele atua em nome do IoT Hub para autenticar dispositivos de subordinados e de módulos para que eles podem continuar a funcionar. Em terceiro lugar, ele permite a comunicação entre dispositivos de subordinados que normalmente seria passar pelo IoT Hub. 

O exemplo seguinte mostra como um cenário de IoT Edge opera no modo offline:

1. **Configurar dispositivos**

   Dispositivos IoT Edge têm automaticamente capacidades offline ativadas. Para expandir esse recurso para outros dispositivos de IoT, tem de declarar uma relação principal-subordinado entre os dispositivos no IoT Hub. Em seguida, configure os dispositivos filho para confiar no dispositivo pai atribuído e encaminhe as comunicações do dispositivo para a nuvem por meio do pai como um gateway. 

2. **Sincronizar com o Hub IoT**

   Pelo menos uma vez após a instalação do runtime do IoT Edge, o dispositivo do IoT Edge tem de estar online para sincronizar com o IoT Hub. Nesta sincronização, o dispositivo do IoT Edge obtém os detalhes sobre todos os dispositivos filho atribuídos ao mesmo. O dispositivo do IoT Edge com a segurança também atualiza a respetiva cache local para permitir operações offline e obtém as definições para o armazenamento local de mensagens de telemetria. 

3. **Ficar offline**

   Enquanto estiver desconectado do IoT Hub, o dispositivo do IoT Edge, os módulos implementados e quaisquer dispositivos de IoT de elementos subordinados podem operar indefinidamente. Os módulos e dispositivos filho podem iniciar e reiniciar Autenticando-se com o Hub de IoT Edge enquanto estiverem offline. Telemetria vinculada a montante para o IoT Hub é armazenada localmente. A comunicação entre os módulos ou entre dispositivos de IoT de subordinado é mantida por meio de métodos diretos ou mensagens. 

4. **Reconectar e ressincronizar com o Hub IoT**

   Assim que a ligação com o IoT Hub é restaurada, o dispositivo do IoT Edge é sincronizada novamente. Mensagens armazenadas localmente são entregues na mesma ordem em que foram armazenados. As diferenças entre dispositivos e as propriedades pretendidas e comunicadas dos módulos são reconciliadas. O dispositivo do IoT Edge atualiza todas as alterações ao seu conjunto de dispositivos de IoT de subordinados atribuído.

## <a name="restrictions-and-limits"></a>Restrições e limites

Os recursos offline estendidos descritos neste artigo estão disponíveis em [IOT Edge versão 1.0.7 ou superior](https://github.com/Azure/azure-iotedge/releases). Versões anteriores com um subconjunto de funcionalidades offline. Existente do IoT Edge dispositivos que não têm recursos offline expandidos não podem ser atualizados, alterando a versão de tempo de execução, mas têm de ser reconfigurados com uma nova identidade de dispositivo do IoT Edge para obter esses recursos. 

O suporte offline expandido está disponível em todas as regiões onde o IoT Hub está disponível, **exceto** E.U.A. Leste.

Somente dispositivos não IoT Edge podem ser adicionados como dispositivos filho. 

Dispositivos IoT Edge e os respetivos dispositivos atribuídos filho podem funcionar indefinidamente offline depois da sincronização inicial e de uso individual. No entanto, o armazenamento de mensagens depende do tempo de duração definição (TTL) e o espaço disponível no disco para armazenar as mensagens. 

## <a name="set-up-parent-and-child-devices"></a>Configurar dispositivos pai e filho

Para um dispositivo IoT Edge estender seus recursos offline estendidos para dispositivos IoT filho, você precisa concluir duas etapas. Primeiro, declare as relações pai-filho no portal do Azure. Em segundo lugar, crie uma relação de confiança entre o dispositivo pai e todos os dispositivos filho e configure as comunicações do dispositivo para a nuvem para passar pelo pai como um gateway. 

### <a name="assign-child-devices"></a>Atribuir dispositivos de subordinados

Os dispositivos filho podem ser qualquer dispositivo não IoT Edge registrado no mesmo Hub IoT. Os dispositivos pai podem ter vários dispositivos filho, mas um dispositivo filho tem apenas um pai. Há três opções para definir dispositivos filho para um dispositivo de borda: por meio do portal do Azure, usando o CLI do Azure ou usando o SDK do serviço de Hub IoT. 

As seções a seguir fornecem exemplos de como você pode declarar a relação pai/filho no Hub IoT para dispositivos IoT existentes. Se você estiver criando novas identidades de dispositivo para seus dispositivos filho, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md) para obter mais informações.

#### <a name="option-1-iot-hub-portal"></a>Opção 1: Portal do Hub IoT

Você pode declarar a relação pai-filho ao criar um novo dispositivo. Ou para dispositivos existentes, você pode declarar a relação na página de detalhes do dispositivo do dispositivo pai IoT Edge ou do dispositivo IoT filho. 

   ![Gerir dispositivos de subordinado a partir da página de detalhes do dispositivo IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Opção 2: Usar a `az` ferramenta de linha de comando

Usando a [interface de linha de comando do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) com a [extensão de IOT](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 ou mais recente), você pode gerenciar relações filho pai com os subcomandos de [identidade do dispositivo](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) . O exemplo a seguir usa uma consulta para atribuir todos os dispositivos não IoT Edge no Hub para serem dispositivos filho de um dispositivo IoT Edge. 

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

Você pode modificar a [consulta](../iot-hub/iot-hub-devguide-query-language.md) para selecionar um subconjunto diferente de dispositivos. O comando pode levar vários segundos se você especificar um grande conjunto de dispositivos.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opção 3: Usar o SDK do serviço do Hub IoT 

Por fim, você pode gerenciar relações filho pai programaticamente usando o C#SDK do serviço de Hub IOT Java ou node. js. Aqui está um [exemplo de como atribuir um dispositivo filho](https://aka.ms/set-child-iot-device-c-sharp) usando o C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configurar o dispositivo pai como um gateway

Você pode considerar uma relação pai/filho como um gateway transparente, em que o dispositivo filho tem sua própria identidade no Hub IoT, mas se comunica através da nuvem por meio de seu pai. Para uma comunicação segura, o dispositivo filho precisa ser capaz de verificar se o dispositivo pai vem de uma fonte confiável. Caso contrário, terceiros poderiam configurar dispositivos mal-intencionados para representar os pais e interceptar as comunicações. 

Uma maneira de criar essa relação de confiança é descrita detalhadamente nos seguintes artigos: 
* [Configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md)
* [Conectar um dispositivo downstream (filho) a um gateway de Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Especificar servidores DNS 

Para melhorar a robustez, é altamente recomendável especificar os endereços do servidor DNS usados em seu ambiente. Consulte duas opções para [definir o servidor DNS no artigo de solução de problemas](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Definições opcionais de offline

Se os dispositivos ficarem offline, o dispositivo pai IoT Edge armazenará todas as mensagens do dispositivo para a nuvem até que a conexão seja restabelecida. O módulo Hub de IoT Edge gerencia o armazenamento e o encaminhamento de mensagens offline. Para dispositivos que podem ficar offline por longos períodos de tempo, otimize o desempenho definindo duas configurações de Hub de IoT Edge. 

Primeiro, aumente a configuração de vida útil para que o Hub de IoT Edge Mantenha as mensagens longas o suficiente para que seu dispositivo se reconecte. Em seguida, adicione mais espaço em disco para o armazenamento de mensagens. 

### <a name="time-to-live"></a>Time to live

O tempo de duração a definição é a quantidade de tempo (em segundos) que uma mensagem pode aguardar para ser entregue antes de expirar. A predefinição é 7200 segundos (duas horas). O valor máximo é limitado apenas pelo valor máximo de uma variável de inteiro, que é cerca de 2.000.000.000. 

Essa configuração é uma propriedade desejada do hub de IoT Edge, que é armazenada no módulo... Você pode configurá-lo no portal do Azure ou diretamente no manifesto de implantação. 

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

As mensagens são armazenadas por padrão no sistema de arquivos de contêiner do hub de IoT Edge. Se essa quantidade de armazenamento não é suficiente para as suas necessidades offline, pode dedicar armazenamento local no dispositivo IoT Edge. Crie uma variável de ambiente para o Hub de IoT Edge que aponta para uma pasta de armazenamento no contêiner. Em seguida, utilize as opções de criar para vincular essa pasta de armazenamento para uma pasta no computador anfitrião. 

Você pode configurar variáveis de ambiente e as opções de criação para o módulo Hub de IoT Edge no portal do Azure na seção **Configurar configurações de tempo de execução avançadas do Edge** . Em alternativa, pode configurá-lo diretamente no manifesto de implantação. 

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

Substitua `<HostStoragePath>` e `<ModuleStoragePath>` com o armazenamento de anfitrião e o módulo de caminho de armazenamento do caminho; anfitrião e módulo tem de ser um caminho absoluto. Nas opções criar, associe os caminhos de armazenamento do host e do módulo juntos. Em seguida, crie uma variável de ambiente que aponte para o caminho de armazenamento do módulo.  

Por exemplo, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` significa que o diretório **/etc/iotedge/Storage** no seu sistema host é mapeado para o diretório **/iotedge/Storage/** no contêiner. Ou outro exemplo para sistemas Windows, `"Binds":["C:\\temp:C:\\contemp"]` significa que o diretório **c\\: Temp** no seu sistema host é mapeado para o diretório **c\\: contemp** no contêiner. 

Você também pode encontrar mais detalhes sobre como criar opções de [documentos](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)do Docker.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como configurar um gateway transparente para suas conexões de dispositivo pai/filho: 

* [Configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo downstream no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Conectar um dispositivo downstream a um gateway de Azure IoT Edge](how-to-connect-downstream-device.md)

---
title: Opere os dispositivos offline - Azure IoT Edge | Documentos da Microsoft
description: Compreenda como os dispositivos do IoT Edge e os módulos podem funcionar sem ligação à internet por longos períodos de tempo e, como IoT Edge pode permitir que os dispositivos de IoT regulares operar offline também.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e82c842ec8fce703c48c98eaf09ea5c8d91be9be
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307994"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Compreender as capacidades offline expandidas para dispositivos do IoT Edge, módulos e dispositivos de subordinado (pré-visualização)

O Azure IoT Edge suporta operações offline expandidas nos seus dispositivos IoT Edge e permite operações offline nos dispositivos de subordinado de não-Edge demasiado. Desde que um dispositivo IoT Edge teve uma oportunidade para se ligar ao IoT Hub, ele e quaisquer dispositivos de subordinados podem continuar a função intermitente com ou sem ligação à internet. 

>[!NOTE]
>O suporte offline para o IoT Edge está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Como funciona

Quando um dispositivo IoT Edge entra em modo offline, o hub IoT Edge assume três funções. Em primeiro lugar, ele armazena todas as mensagens que iriam a montante e salva-os até que o dispositivo volta a ligar. Em segundo lugar, ele atua em nome do IoT Hub para autenticar dispositivos de subordinados e de módulos para que eles podem continuar a funcionar. Em terceiro lugar, ele permite a comunicação entre dispositivos de subordinados que normalmente seria passar pelo IoT Hub. 

O exemplo seguinte mostra como um cenário de IoT Edge opera no modo offline:

1. **Configure um dispositivo IoT Edge.**

   Dispositivos IoT Edge têm automaticamente capacidades offline ativadas. Para expandir esse recurso para outros dispositivos de IoT, tem de declarar uma relação principal-subordinado entre os dispositivos no IoT Hub. 

2. **Sincronização com o IoT Hub.**

   Pelo menos uma vez após a instalação do runtime do IoT Edge, o dispositivo do IoT Edge tem de estar online para sincronizar com o IoT Hub. Nesta sincronização, o dispositivo do IoT Edge obtém os detalhes sobre todos os dispositivos filho atribuídos ao mesmo. O dispositivo do IoT Edge com a segurança também atualiza a respetiva cache local para permitir operações offline e obtém as definições para o armazenamento local de mensagens de telemetria. 

3. **Ficam offline.**

   Enquanto estiver desconectado do IoT Hub, o dispositivo do IoT Edge, os módulos implementados e quaisquer dispositivos de IoT de elementos subordinados podem operar indefinidamente. Módulos e dispositivos de subordinado podem iniciar e reiniciar ao autenticar com o hub IoT Edge ao mesmo tempo offline. Telemetria vinculada a montante para o IoT Hub é armazenada localmente. A comunicação entre os módulos ou entre dispositivos de IoT de subordinado é mantida por meio de métodos diretos ou mensagens. 

4. **Voltar a ligar e volte a sincronizar com o IoT Hub.**

   Assim que a ligação com o IoT Hub é restaurada, o dispositivo do IoT Edge é sincronizada novamente. Mensagens armazenadas localmente são entregues na mesma ordem em que foram armazenados. As diferenças entre dispositivos e as propriedades pretendidas e comunicadas dos módulos são reconciliadas. O dispositivo do IoT Edge atualiza todas as alterações ao seu conjunto de dispositivos de IoT de subordinados atribuído.

## <a name="restrictions-and-limits"></a>Restrições e limites

As capacidades offline expandidas descritas neste artigo estão disponíveis no [IoT Edge versão 1.0.4 ou superior](https://github.com/Azure/azure-iotedge/releases). Versões anteriores com um subconjunto de funcionalidades offline. Existente do IoT Edge dispositivos que não têm recursos offline expandidos não podem ser atualizados, alterando a versão de tempo de execução, mas têm de ser reconfigurados com uma nova identidade de dispositivo do IoT Edge para obter esses recursos. 

O suporte offline expandido está disponível em todas as regiões onde o IoT Hub está disponível, **exceto** E.U.A. Leste.

Apenas os dispositivos não Edge IoT podem ser adicionados como dispositivos de subordinados. 

Dispositivos IoT Edge e os respetivos dispositivos atribuídos filho podem funcionar indefinidamente offline depois da sincronização inicial e de uso individual. No entanto, o armazenamento de mensagens depende do tempo de duração definição (TTL) e o espaço disponível no disco para armazenar as mensagens. 

## <a name="set-up-an-iot-edge-device"></a>Configurar um dispositivo IoT Edge

Para um dispositivo IoT Edge expandir as suas capacidades offline expandidas para dispositivos de IoT filho, precisa declarar as relações principal-subordinado no portal do Azure.

### <a name="assign-child-devices"></a>Atribuir dispositivos de subordinados

Dispositivos filho podem ser qualquer dispositivo de não-Edge registado no mesmo IoT Hub. Pode gerenciar a relação principal-subordinado sobre como criar um novo dispositivo ou a partir da página de detalhes do dispositivo de que o principal dispositivo IoT Edge ou o dispositivo de IoT filho. 

   ![Gerir dispositivos de subordinado a partir da página de detalhes do dispositivo IoT Edge](./media/offline-capabilities/manage-child-devices.png)

Dispositivos de principal podem ter vários dispositivos de subordinado, mas um dispositivo de subordinado só pode ter um elemento principal.

### <a name="specifying-dns-servers"></a>Especificar os servidores DNS 

Para melhorar a robustez, recomenda-se que especifique os endereços de servidor DNS utilizados no seu ambiente. Por exemplo, no Linux, atualize **/etc/docker/daemon.json** (poderá ter de criar o ficheiro) para incluir:

```json
{
    "dns": ["1.1.1.1"]
}
```

Se estiver a utilizar um servidor DNS local, substitua o 1.1.1.1 o endereço IP do servidor DNS local. Reinicie o serviço de docker para que as alterações entrem em vigor.


## <a name="optional-offline-settings"></a>Definições opcionais de offline

Se esperar recolher todas as mensagens que seus dispositivos geram durante períodos de tempo offline, configure o hub IoT Edge, para que ele pode armazenar todas as mensagens. Existem duas alterações que pode fazer ao hub IoT Edge para ativar o armazenamento de mensagens de longo prazo. Em primeiro lugar, aumente o tempo de duração de definição. Em seguida, adicione o espaço em disco adicional para armazenamento de mensagens. 

### <a name="time-to-live"></a>Time to live

O tempo de duração a definição é a quantidade de tempo (em segundos) que uma mensagem pode aguardar para ser entregue antes de expirar. A predefinição é 7200 segundos (duas horas). 

Esta definição é uma propriedade pretendida do hub IoT Edge, que é armazenado em duplo do módulo. Pode configurá-la no portal do Azure, no **configurar definições de Runtime do Edge avançadas** secção ou diretamente na implementação do manifesto. 

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

Ativar as operações offline expandidas no seu [gateway transparente](how-to-create-transparent-gateway.md) cenários.

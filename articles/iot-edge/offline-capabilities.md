---
title: Operar dispositivos offline-Azure IoT Edge | Microsoft Docs
description: Entenda como IoT Edge dispositivos e módulos podem operar sem conexão com a Internet por longos períodos de tempo e como IoT Edge pode permitir que dispositivos IoT regulares operem offline também.
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3fc90e685a3c6a077250028bae5602e95f114c03
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293452"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Entender os recursos offline estendidos para dispositivos IoT Edge, módulos e dispositivos filho

O Azure IoT Edge dá suporte a operações offline estendidas em seus dispositivos IoT Edge e habilita operações offline em dispositivos não IoT Edge filhos também. Desde que um dispositivo IoT Edge tenha uma oportunidade de se conectar ao Hub IoT, ele e todos os dispositivos filho podem continuar a funcionar com a conexão intermitente ou sem a Internet. 


## <a name="how-it-works"></a>Como funciona

Quando um dispositivo IoT Edge entra no modo offline, o Hub de IoT Edge assume três funções. Primeiro, ele armazena todas as mensagens que seriam costream e as salva até que o dispositivo se reconecte. Em segundo lugar, ele age em nome do Hub IoT para autenticar módulos e dispositivos filho para que eles possam continuar a operar. Terceiro, ele permite a comunicação entre os dispositivos filho que normalmente passariam pelo Hub IoT. 

O exemplo a seguir mostra como um cenário de IoT Edge opera no modo offline:

1. **Configurar dispositivos**

   Os dispositivos IoT Edge têm automaticamente recursos offline habilitados. Para estender essa capacidade para outros dispositivos IoT, você precisa declarar uma relação pai-filho entre os dispositivos no Hub IoT. Em seguida, configure os dispositivos filho para confiar no dispositivo pai atribuído e encaminhe as comunicações do dispositivo para a nuvem por meio do pai como um gateway. 

2. **Sincronizar com o Hub IoT**

   Pelo menos uma vez após a instalação do IoT Edge Runtime, o dispositivo IoT Edge precisa estar online para sincronizar com o Hub IoT. Nessa sincronização, o dispositivo IoT Edge Obtém detalhes sobre todos os dispositivos filho atribuídos a ele. O dispositivo IoT Edge também atualiza com segurança seu cache local para habilitar operações offline e recupera as configurações de armazenamento local de mensagens de telemetria. 

3. **Ficar offline**

   Enquanto estiver desconectado do Hub IoT, o dispositivo IoT Edge, seus módulos implantados e quaisquer dispositivos de IoT filhos poderão operar indefinidamente. Os módulos e dispositivos filho podem iniciar e reiniciar Autenticando-se com o Hub de IoT Edge enquanto estiverem offline. O upstream vinculado à telemetria ao Hub IoT é armazenado localmente. A comunicação entre módulos ou entre dispositivos IoT filho é mantida por meio de métodos ou mensagens diretas. 

4. **Reconectar e ressincronizar com o Hub IoT**

   Depois que a conexão com o Hub IoT for restaurada, o IoT Edge dispositivo será sincronizado novamente. As mensagens armazenadas localmente são entregues na mesma ordem em que foram armazenadas. Todas as diferenças entre as propriedades desejadas e relatadas dos módulos e dos dispositivos são reconciliadas. O dispositivo IoT Edge atualiza as alterações em seu conjunto de dispositivos IoT filho atribuídos.

## <a name="restrictions-and-limits"></a>Restrições e limites

Os recursos offline estendidos descritos neste artigo estão disponíveis em [IOT Edge versão 1.0.7 ou superior](https://github.com/Azure/azure-iotedge/releases). As versões anteriores têm um subconjunto de recursos offline. Os dispositivos IoT Edge existentes que não têm recursos offline estendidos não podem ser atualizados alterando a versão de tempo de execução, mas devem ser reconfigurados com uma nova identidade de dispositivo de IoT Edge para obter esses recursos. 

O suporte offline estendido está disponível em todas as regiões em que o Hub IoT está disponível, **exceto** leste dos EUA.

Somente dispositivos não IoT Edge podem ser adicionados como dispositivos filho. 

IoT Edge dispositivos e seus dispositivos filho atribuídos podem funcionar indefinidamente offline após a sincronização inicial e única. No entanto, o armazenamento de mensagens depende da configuração TTL (vida útil) e do espaço em disco disponível para armazenar as mensagens. 

## <a name="set-up-parent-and-child-devices"></a>Configurar dispositivos pai e filho

Para um dispositivo IoT Edge estender seus recursos offline estendidos para dispositivos IoT filho, você precisa concluir duas etapas. Primeiro, declare as relações pai-filho no portal do Azure. Em segundo lugar, crie uma relação de confiança entre o dispositivo pai e todos os dispositivos filho e configure as comunicações do dispositivo para a nuvem para passar pelo pai como um gateway. 

### <a name="assign-child-devices"></a>Atribuir dispositivos filho

Os dispositivos filho podem ser qualquer dispositivo não IoT Edge registrado no mesmo Hub IoT. Os dispositivos pai podem ter vários dispositivos filho, mas um dispositivo filho tem apenas um pai. Há três opções para definir dispositivos filho para um dispositivo de borda: por meio do portal do Azure, usando o CLI do Azure ou usando o SDK do serviço de Hub IoT. 

As seções a seguir fornecem exemplos de como você pode declarar a relação pai/filho no Hub IoT para dispositivos IoT existentes. Se você estiver criando novas identidades de dispositivo para seus dispositivos filho, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md) para obter mais informações.

#### <a name="option-1-iot-hub-portal"></a>Opção 1: portal do Hub IoT

Você pode declarar a relação pai-filho ao criar um novo dispositivo. Ou para dispositivos existentes, você pode declarar a relação na página de detalhes do dispositivo do dispositivo pai IoT Edge ou do dispositivo IoT filho. 

   ![Gerenciar dispositivos filho na página de detalhes do dispositivo IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Opção 2: usar a ferramenta de linha de comando `az`

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

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opção 3: usar o SDK do serviço do Hub IoT 

Por fim, você pode gerenciar relações filho pai programaticamente usando o C#SDK do serviço de Hub IOT Java ou node. js. Aqui está um [exemplo de como atribuir um dispositivo filho](https://aka.ms/set-child-iot-device-c-sharp) usando o C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configurar o dispositivo pai como um gateway

Você pode considerar uma relação pai/filho como um gateway transparente, em que o dispositivo filho tem sua própria identidade no Hub IoT, mas se comunica através da nuvem por meio de seu pai. Para uma comunicação segura, o dispositivo filho precisa ser capaz de verificar se o dispositivo pai vem de uma fonte confiável. Caso contrário, terceiros poderiam configurar dispositivos mal-intencionados para representar os pais e interceptar as comunicações. 

Uma maneira de criar essa relação de confiança é descrita detalhadamente nos seguintes artigos: 
* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Conectar um dispositivo downstream (filho) a um gateway de Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Especificar servidores DNS 

Para melhorar a robustez, é altamente recomendável especificar os endereços do servidor DNS usados em seu ambiente. Para definir o servidor DNS para IoT Edge, consulte o módulo Resolution for [Edge Agent relata continuamente ' arquivo de configuração vazio ' e nenhum módulo inicia no dispositivo](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) no artigo de solução de problemas.

## <a name="optional-offline-settings"></a>Configurações offline opcionais

Se os dispositivos ficarem offline, o dispositivo pai IoT Edge armazenará todas as mensagens do dispositivo para a nuvem até que a conexão seja restabelecida. O módulo Hub de IoT Edge gerencia o armazenamento e o encaminhamento de mensagens offline. Para dispositivos que podem ficar offline por longos períodos de tempo, otimize o desempenho definindo duas configurações de Hub de IoT Edge. 

Primeiro, aumente a configuração de vida útil para que o Hub de IoT Edge Mantenha as mensagens longas o suficiente para que seu dispositivo se reconecte. Em seguida, adicione mais espaço em disco para o armazenamento de mensagens. 

### <a name="time-to-live"></a>Time to live

A configuração vida útil é a quantidade de tempo (em segundos) que uma mensagem pode aguardar para ser entregue antes de expirar. O padrão é 7200 segundos (duas horas). O valor máximo é limitado apenas pelo valor máximo de uma variável de inteiro, que é cerca de 2.000.000.000. 

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

### <a name="host-storage-for-system-modules"></a>Armazenamento de host para módulos do sistema

As mensagens e as informações de estado do módulo são armazenadas no sistema de arquivos de contêiner local do hub de IoT Edge por padrão. Para maior confiabilidade, especialmente ao operar offline, você também pode dedicar o armazenamento no dispositivo IoT Edge host. Para obter mais informações, consulte [fornecer aos módulos acesso ao armazenamento local de um dispositivo](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como configurar um gateway transparente para suas conexões de dispositivo pai/filho: 

* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo a jusante no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)

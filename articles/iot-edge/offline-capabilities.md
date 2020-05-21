---
title: Operar dispositivos offline - Azure IoT Edge [ Azure IoT Edge ] Microsoft Docs
description: Compreenda como os dispositivos e módulos IoT Edge podem funcionar sem ligação à Internet por longos períodos de tempo, e como o IoT Edge pode permitir que dispositivos IoT regulares também funcionem offline.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ef6ed74149f106b801049da429dfe7b79b984a70
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725249"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Compreender capacidades offline estendidas para dispositivos, módulos e dispositivos infantis IoT Edge

O Azure IoT Edge suporta operações offline prolongadas nos seus dispositivos IoT Edge e permite operações offline em dispositivos infantis não IoT Edge também. Desde que um dispositivo IoT Edge tenha tido uma oportunidade de se ligar ao IoT Hub, esse dispositivo e quaisquer dispositivos infantis podem continuar a funcionar com ligação intermitente ou sem internet.

## <a name="how-it-works"></a>Como funciona

Quando um dispositivo IoT Edge entra em modo offline, o hub IoT Edge assume três funções. Primeiro, armazena quaisquer mensagens que iriam a montante e as guardaaté que o dispositivo volte a ligar. Em segundo lugar, atua em nome do IoT Hub para autenticar módulos e dispositivos infantis para que possam continuar a operar. Em terceiro lugar, permite a comunicação entre dispositivos infantis que normalmente passariam pelo IoT Hub.

O exemplo que se segue mostra como um cenário IoT Edge funciona em modo offline:

1. **Dispositivos configurados**

   Os dispositivos IoT Edge têm automaticamente capacidades offline ativadas. Para estender essa capacidade a outros dispositivos IoT, é necessário declarar uma relação pai-filho entre os dispositivos no IoT Hub. Em seguida, configura os dispositivos infantis para confiar no dispositivo-mãe atribuído e encaminhar as comunicações dispositivo-para-nuvem através do progenitor como um portal.

2. **Sincronizar com o Hub IoT**

   Pelo menos uma vez após a instalação do tempo de execução do IoT Edge, o dispositivo IoT Edge precisa de estar online para sincronizar com o IoT Hub. Nesta sincronização, o dispositivo IoT Edge obtém detalhes sobre quaisquer dispositivos infantis que lhe forem atribuídos. O dispositivo IoT Edge também atualiza de forma segura a sua cache local para permitir operações offline e recupera configurações para armazenamento local de mensagens de telemetria.

3. **Ir offline**

   Embora desligado do IoT Hub, o dispositivo IoT Edge, os seus módulos implantados e quaisquer dispositivos IoT para crianças podem funcionar indefinidamente. Os módulos e dispositivos infantis podem iniciar e reiniciar autenticando-se com o hub IoT Edge enquanto estão offline. A telemetria ligada a montante ao IoT Hub é armazenada localmente. A comunicação entre módulos ou entre dispositivos IoT infantis é mantida através de métodos ou mensagens diretas.

4. **Reconectar e resincronizar com IoT Hub**

   Uma vez restaurada a ligação com o IoT Hub, o dispositivo IoT Edge sincroniza-se novamente. As mensagens armazenadas localmente são entregues imediatamente no IoT Hub, mas dependem da velocidade da ligação, latência do Hub IoT e fatores relacionados. São entregues na mesma ordem em que foram armazenados.

   As diferenças entre as propriedades desejadas e comunicadas dos módulos e dispositivos são reconciliadas. O dispositivo IoT Edge atualiza quaisquer alterações ao seu conjunto de dispositivos IoT para crianças atribuídos.

## <a name="restrictions-and-limits"></a>Restrições e limites

As capacidades offline estendidas descritas neste artigo estão disponíveis na [versão 1.0.7 do IoT Edge](https://github.com/Azure/azure-iotedge/releases). As versões anteriores têm um subconjunto de funcionalidades offline. Os dispositivos IoT Edge existentes que não tenham capacidades offline estendidas não podem ser atualizados alterando a versão de tempo de funcionamento, mas devem ser reconfigurados com uma nova identidade de dispositivo IoT Edge para obter estas funcionalidades.

Apenas dispositivos não IoT Edge podem ser adicionados como dispositivos infantis.

Os dispositivos IoT Edge e os seus dispositivos infantis atribuídos podem funcionar indefinidamente offline após a sincronização inicial e única. No entanto, o armazenamento de mensagens depende da hora de viver (TTL) e do espaço disponível para armazenar as mensagens.

## <a name="set-up-parent-and-child-devices"></a>Configurar dispositivos de pais e filhos

Para que um dispositivo IoT Edge aumente as suas capacidades offline estendidas para dispositivos IoT infantis, é necessário completar dois passos. Primeiro, declare as relações pai-filho no portal Azure. Em segundo lugar, crie uma relação de confiança entre o dispositivo-mãe e quaisquer dispositivos infantis, em seguida, configurar as comunicações dispositivo-to-cloud para passar pelo progenitor como uma porta de entrada.

### <a name="assign-child-devices"></a>Atribuir dispositivos infantis

Dispositivos infantis podem ser qualquer dispositivo não IoT Edge registado no mesmo Hub IoT. Os dispositivos dos pais podem ter vários dispositivos infantis, mas um dispositivo infantil só tem um dos pais. Existem três opções para definir dispositivos infantis para um dispositivo de borda: através do portal Azure, utilizando o Azure CLI, ou utilizando o serviço IoT Hub SDK.

As seguintes secções fornecem exemplos de como pode declarar a relação pai/filho no IoT Hub para dispositivos IoT existentes. Se estiver a criar novas identidades de dispositivo para os seus dispositivos infantis, consulte [o Authenticate um dispositivo a jusante para o Azure IoT Hub](how-to-authenticate-downstream-device.md) para obter mais informações.

#### <a name="option-1-iot-hub-portal"></a>Opção 1: Portal IoT Hub

Pode declarar a relação pai-filho ao criar um novo dispositivo. Ou para dispositivos existentes, pode declarar a relação a partir da página de detalhes do dispositivo do dispositivo ioT Edge ou do dispositivo IoT infantil.

   ![Gerir dispositivos infantis a partir da página de detalhes do dispositivo IoT Edge](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Opção 2: Utilize a `az` ferramenta de linha de comando

Utilizando a [interface de linha de comando Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) com [extensão IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 ou mais recente), pode gerir as relações parentais com os subcomandos de [identidade do dispositivo.](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) O exemplo abaixo utiliza uma consulta para atribuir todos os dispositivos não IoT Edge no hub para serem dispositivos infantis de um dispositivo IoT Edge.

```azurecli
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

Pode modificar a [consulta](../iot-hub/iot-hub-devguide-query-language.md) para selecionar um subconjunto diferente de dispositivos. O comando pode demorar alguns segundos se especificar um grande conjunto de dispositivos.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opção 3: Utilizar o Serviço IoT Hub SDK

Finalmente, você pode gerir as relações infantis dos pais programáticamente usando C#, Java ou Node.js IoT Hub Service SDK. Aqui está um [exemplo de atribuição de um dispositivo infantil](https://aka.ms/set-child-iot-device-c-sharp) usando o C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configurar o dispositivo-mãe como um portal

Pode pensar numa relação pai/filho como uma porta de entrada transparente, onde o dispositivo infantil tem a sua própria identidade no IoT Hub mas comunica através da nuvem através do seu progenitor. Para uma comunicação segura, o dispositivo infantil precisa de ser capaz de verificar se o dispositivo-mãe provém de uma fonte fidedigna. Caso contrário, terceiros poderiam criar dispositivos maliciosos para personificar os pais e intercetar comunicações.

Uma forma de criar esta relação de confiança é descrita em detalhe nos seguintes artigos:

* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Ligue um dispositivo a jusante (criança) a um gateway Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Especificar servidores DNS

Para melhorar a robustez, é altamente recomendável especificar os endereços do servidor DNS utilizados no seu ambiente. Para definir o seu servidor DNS para IoT Edge, consulte a resolução do [módulo Edge Agent relata continuamente "ficheiro config vazio" e nenhum módulo começa no dispositivo](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device) no artigo de resolução de problemas.

## <a name="optional-offline-settings"></a>Configurações offline opcionais

Se os seus dispositivos ficarem offline, o dispositivo-mãe IoT Edge armazena todas as mensagens dispositivo-nuvem até que a ligação seja restabelecida. O módulo hub IoT Edge gere o armazenamento e reencaminhamento de mensagens offline. Para dispositivos que podem ficar offline por longos períodos de tempo, otimize o desempenho configurando duas configurações do hub IoT Edge.

Em primeiro lugar, aumente o tempo de vida para que o hub IoT Edge mantenha mensagens tempo suficiente para o seu dispositivo voltar a ligar. Em seguida, adicione espaço adicional em disco para armazenamento de mensagens.

### <a name="time-to-live"></a>Time to live

O tempo para viver é a quantidade de tempo (em segundos) que uma mensagem pode esperar para ser entregue antes de expirar. O padrão é de 7200 segundos (duas horas). O valor máximo é limitado apenas pelo valor máximo de uma variável inteiro, que ronda os 2 mil milhões.

Esta configuração é uma propriedade desejada do hub IoT Edge, que é armazenado no módulo twin. Pode configurá-lo no portal Azure ou diretamente no manifesto de implantação.

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

### <a name="host-storage-for-system-modules"></a>Armazenamento de hospedeiro para módulos de sistema

As mensagens e as informações estatais do módulo são armazenadas no sistema de ficheiros de contentores local do centro IoT Edge por padrão. Para uma maior fiabilidade, especialmente quando opera offline, também pode dedicar armazenamento no dispositivo IoT Edge do hospedeiro. Para mais informações, consulte [Dar aos módulos acesso ao armazenamento local de um dispositivo](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como configurar uma porta de entrada transparente para as ligações entre pais e filhos:

* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo a jusante no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)

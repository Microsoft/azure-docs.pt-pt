---
title: Ligado à solução de fábrica FAQ - Azure | Documentos da Microsoft
description: Perguntas mais frequentes sobre o acelerador de solução de fábrica ligada
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: ed429d923cad2c715621990c146d4cf3a23e7bca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904761"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Perguntas mais frequentes sobre acelerador de solução de fábrica ligada

Além disso, consulte gerais [FAQ](iot-accelerators-faq.md) para Aceleradores de solução de IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Onde posso encontrar o código-fonte para o solution accelerator?

O código-fonte é armazenado no repositório GitHub do seguinte:

* [Acelerador de solução de fábrica ligada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>O que é o OPC UA?

OPC Unified arquitetura (UA), lançado em 2008, é uma interoperabilidade independente de plataforma, orientadas a serviços padrão. OPC UA é utilizado por vários sistemas industriais e dispositivos, como setor PCs, PLCs e sensores. OPC UA integra-se a funcionalidade das especificações de OPC clássica numa estrutura extensível com segurança incorporada. É um padrão que é orientado pela OPC Foundation. O [OPC Foundation](https://opcfoundation.org/) é uma organização não fins lucrativos com mais de 440 membros. O objetivo da organização é usar as especificações de OPC para facilitar a interoperabilidade de vários fornecedor, várias plataforma, segura e confiável por meio de:

* Infraestrutura
* Especificações
* Tecnologia
* Processos

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Por que Microsoft escolher OPC UA para o acelerador de solução de fábrica ligada?

Microsoft escolheu o OPC UA porque é um padrão de plataforma aberta e não-proprietários, independentes, reconhecidas no setor e comprovadas. É um requisito para soluções de arquitetura de referência de indústria 4.0 (RAMI4.0) garantia da interoperabilidade entre um vasto conjunto de processos de produção e de equipamento. A Microsoft considera a pedido a partir de seus clientes para criar soluções da indústria 4.0. Suporte para OPC UA ajuda a diminuir a barreira para os clientes a atingir suas metas e fornece o valor de negócios imediatas aos mesmos.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Como posso adicionar um endereço IP público para a simulação de VM?

Tem duas opções para adicionar o endereço IP:

* Utilize o script do PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory). Transmita o nome da sua implementação como um parâmetro. Para uma implementação local, utilize `<your username>ConnFactoryLocal`. O script imprime o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos da sua implementação. Exceto para uma implementação local, o grupo de recursos tem o nome que especificou como solução ou o nome de implementação. Para uma implementação local utilizando o script de compilação, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Agora, adicione uma nova **endereço IP público** recurso para o grupo de recursos.

> [!NOTE]
> Em ambos os casos, certifique-se de instalar os patches mais recentes ao seguir as instruções a [Web site do Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Manter atualizados para a instalação, desde que a sua VM está acessível através de um endereço IP público.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Como posso remover o endereço IP público para a simulação de VM?

Tem duas opções para remover o endereço IP:

* Utilize o script do PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 do [repositório](https://github.com/Azure/azure-iot-connected-factory). Transmita o nome da sua implementação como um parâmetro. Para uma implementação local, utilize `<your username>ConnFactoryLocal`. O script imprime o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos da sua implementação. Exceto para uma implementação local, o grupo de recursos tem o nome que especificou como solução ou o nome de implementação. Para uma implementação local utilizando o script de compilação, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Agora remover os **endereço IP público** recursos do grupo de recursos.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Como posso iniciar sessão na VM de simulação?

A iniciar sessão na VM de simulação só é suportada se tiver implementado a sua solução com o script do PowerShell `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Se implementou a solução da www.azureiotsolutions.com, não consigo iniciar sessão na VM. Não é possível iniciar sessão, porque a palavra-passe é gerada aleatoriamente e não pode redefini-lo.

1. Adicione um endereço IP público à VM. Consulte [como adicionar um endereço IP público para a simulação de VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Crie uma sessão SSH à VM através do endereço IP da VM.
1. É o nome de utilizador a utilizar: `docker`.
1. A palavra-passe a utilizar depende da versão utilizada para implementar:
    * Para soluções implementadas utilizando o script de build.ps1 antes de 1 de Junho de 2017, a palavra-passe é: `Passw0rd`.
    * Para soluções implementadas utilizando o script de build.ps1 após 1 de Junho de 2017, pode encontrar a palavra-passe no `<name of your deployment>.config.user` ficheiro. A palavra-passe é armazenada na **VmAdminPassword** definição. A palavra-passe é gerada aleatoriamente no momento da implementação, a menos que especifique-o com o `build.ps1` parâmetro do script `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Como parar e iniciar todos os processos de docker na VM de simulação?

1. Inicie sessão na VM de simulação. Consulte [como posso iniciar sessão na VM de simulação?](#how-do-i-sign-in-to-the-simulation-vm)
1. Para verificar os contentores estão ativos, execute: `docker ps`.
1. Para parar todos os contentores de simulação, execute: `./stopsimulation`.
1. Para iniciar todos os contentores de simulação:
    * Exportar uma variável de shell com o nome **IOTHUB_CONNECTIONSTRING**. Utilize o valor do **IotHubOwnerConnectionString** definição `<name of your deployment>.config.user` ficheiro. Por exemplo:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Execute `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Como posso atualizar a simulação na VM?

Se efetuar quaisquer alterações na simulação, pode usar o script do PowerShell `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory) usando o `updatedimulation` comando. Este script baseia-se todos os componentes de simulação, interrompe a simulação na VM, carrega, instala e inicia-los.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Como posso saber a cadeia de ligação do hub IoT utilizada pela minha solução?

Se implementou a sua solução com o `build.ps1` script no [repositório](https://github.com/Azure/azure-iot-connected-factory), a cadeia de ligação é o valor de **IotHubOwnerConnectionString** no `<name of your deployment>.config.user` ficheiro.

Também pode encontrar a cadeia de ligação no portal do Azure. No recurso do IoT Hub no grupo de recursos da sua implementação, localize as definições de cadeia de ligação.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Os dispositivos do IoT Hub usar a simulação de fábrica ligada?

A simulação auto-regista os seguintes dispositivos:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Utilizar o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [a extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) ferramenta, pode verificar quais os dispositivos que estão registrados com o sua solução está a utilizar o hub IoT. Para utilizar o Explorador de dispositivos, precisa da cadeia de ligação para o hub IoT na sua implementação. Para utilizar a extensão de IoT para a CLI do Azure, terá de nome do seu IoT Hub.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Como posso obter dados de registo dos componentes de simulação?

Todos os componentes na simulação registar informações em arquivos de log. Estes ficheiros podem ser encontrados na VM, na pasta `home/docker/Logs`. Para obter os registos, pode utilizar o script do PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Este script tem de iniciar sessão na VM. Terá de fornecer credenciais para o início de sessão. Ver [como posso iniciar sessão na VM de simulação?](#how-do-i-sign-in-to-the-simulation-vm) para encontrar as credenciais.

O script adiciona/remove um endereço IP público à VM, se ele ainda não tem um e -lo. O script coloca todos os arquivos de log num arquivo morto e transfere o arquivo para a estação de trabalho de desenvolvimento.

Em alternativa, iniciar sessão na VM através de SSH e inspecionar os ficheiros de registo em tempo de execução.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Como posso verificar se a simulação está a enviar dados para a cloud?

Com o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou o [monitor-eventos de extensão de CLI do Azure IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) de comando, pode inspecionar os dados enviados para o IoT Hub do determinados dispositivos. Para usar essas ferramentas, precisa saber a cadeia de ligação do hub IoT na sua implementação. Consulte [como posso saber a cadeia de ligação do hub IoT utilizada pela minha solução?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Inspecione os dados enviados por um dos dispositivos publicador:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Se não vir dados enviados para o IoT Hub, em seguida, há um problema com a simulação. Como uma primeira etapa de análise, deve analisar os ficheiros de registo dos componentes da simulação. Consulte [como posso obter dados de registo dos componentes de simulação?](#how-can-i-get-log-data-from-the-simulation-components) Em seguida, tente parar e iniciar a simulação e se ainda não houver dados enviados, atualize a simulação completamente. Consulte [como atualizar a simulação na VM?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Como posso ativar um mapa interativo na minha solução de fábrica ligada?

Para ativar um mapa interativo na sua solução de fábrica ligada, tem de ter uma conta do Azure Maps.

Quando implementar a partir de [www.azureiotsolutions.com](https://www.azureiotsolutions.com), o processo de implantação adiciona uma conta do Azure Maps para o grupo de recursos que contém os serviços de acelerador de solução.

Ao implementar utilizando o `build.ps1` script no conjunto de repositório de GitHub de fábrica ligada, a variável de ambiente `$env:MapApiQueryKey` na janela de compilação para o [chave da sua conta do Azure Maps](../azure-maps/how-to-manage-account-keys.md). O mapa interativo, em seguida, é ativado automaticamente.

Também pode adicionar uma chave de conta do Azure Maps do solution accelerator após a implementação. Navegue para o portal do Azure e aceder ao recurso de serviço de aplicações na sua implementação de fábrica ligada. Navegue para **as configurações do aplicativo**, onde encontrará uma seção **configurações de aplicativo**. Definir o **MapApiQueryKey** para o [chave da sua conta do Azure Maps](../azure-maps/how-to-manage-account-keys.md). Guardar as definições e, em seguida, navegue até **descrição geral** e reinicie o serviço de aplicações.

### <a name="how-do-i-create-an-azure-maps-account"></a>Como posso criar uma conta do Azure Maps?

Ver, [como gerir a sua conta do Azure Maps e as chaves](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Como obter a chave da conta do Azure Maps

Ver, [como gerir a sua conta do Azure Maps e as chaves](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Como ativar o mapa interativo durante a depuração localmente?

Para ativar o mapa interativo, enquanto faz a depuração localmente, defina o valor da definição `MapApiQueryKey` nos arquivos `local.user.config` e `<yourdeploymentname>.user.config` na raiz da sua implementação para o valor da **QueryKey** copiou anteriormente.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Como posso utilizar uma imagem diferente na home page do meu dashboard?

Para alterar a imagem estática mostrada de e/s a home page do dashboard, substituir a imagem `WebApp\Content\img\world.jpg`. Em seguida, reconstruir e voltar a implementar a aplicação Web.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Como posso utilizar dispositivos não OPC UA com a fábrica ligada?

Enviar dados telemétricos de não OPC UA dispositivos para a fábrica ligada:

1. [Configurar uma estação nova na topologia de fábrica ligada](iot-accelerators-connected-factory-configure.md) no `ContosoTopologyDescription.json` ficheiro.

1. Incorporar os dados de telemetria no formato JSON compatível de fábrica ligada:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. O formato de `<timestamp>` é: `2017-12-08T19:24:51.886753Z`

1. Reinicie o serviço de aplicação de fábrica ligada.

### <a name="next-steps"></a>Passos Seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implementar o acelerador de solução de fábrica ligada](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)

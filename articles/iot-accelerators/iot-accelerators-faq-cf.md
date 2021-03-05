---
title: Solução de fábrica conectada FAQ - Azure | Microsoft Docs
description: Este artigo responde às perguntas frequentes para o acelerador de solução Connected Factory. Inclui ligações ao repositório GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c6a695030d399cb1609c63ef5652419a9bef1bbf
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198987"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Perguntas frequentes para acelerador de solução de fábrica conectada

Consulte também o [FAQ](iot-accelerators-faq.md) geral para aceleradores de solução IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Onde posso encontrar o código-fonte para o acelerador de solução?

O código fonte é armazenado no seguinte repositório GitHub:

* [Acelerador de solução de fábrica conectado](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>O que é OPC UA?

A OPC Unified Architecture (UA), lançada em 2008, é uma norma de interoperabilidade independente e orientada para o serviço. OPC UA é usado por vários sistemas industriais e dispositivos, tais como PCs da indústria, PLCs e sensores. O OPC UA integra a funcionalidade das especificações clássicas do OPC numa estrutura extensível com segurança incorporada. É um padrão que é impulsionado pela Fundação OPC. A [Fundação OPC](https://opcfoundation.org/) é uma organização sem fins lucrativos com mais de 440 membros. O objetivo da organização é utilizar as especificações OPC para facilitar a interoperabilidade multi-fornecedor, multi-plataforma, segura e fiável através de:

* Infraestrutura
* Especificações
* Tecnologia
* Processos

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Porque é que a Microsoft escolheu o OPC UA para o acelerador de soluções Connected Factory?

A Microsoft escolheu a OPC UA porque é um padrão aberto, não proprietário, independente da plataforma, reconhecido pela indústria e comprovado. É um requisito para soluções de arquitetura de referência industrie 4.0 (RAMI4.0) que garantam a interoperabilidade entre um vasto conjunto de processos de fabrico e equipamentos. A Microsoft vê a procura dos seus clientes para construir soluções Industrie 4.0. O suporte à UA da OPC ajuda a reduzir a barreira para que os clientes atinjam os seus objetivos e lhes proporcione valor empresarial imediato.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Como adiciono um endereço IP público à simulação VM?

Tem duas opções para adicionar o endereço IP:

* Utilize o script PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory). Passe o seu nome de implantação como parâmetro. Para uma implantação local, use `<your username>ConnFactoryLocal` . O script imprime o endereço IP do VM.

* No portal Azure, localize o grupo de recursos da sua implantação. Com exceção de uma implantação local, o grupo de recursos tem o nome especificado como solução ou nome de implantação. Para uma implantação local utilizando o script de construção, o nome do grupo de recursos é `<your username>ConnFactoryLocal` . Adicione agora um novo recurso **de endereço IP público** ao grupo de recursos.

> [!NOTE]
> Em qualquer dos casos, certifique-se de que instala as últimas correções seguindo as instruções no [site da Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Mantenha a instalação atualizada enquanto o seu VM estiver acessível através de um endereço IP público.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Como posso remover o endereço IP público para o VM de simulação?

Tem duas opções para remover o endereço IP:

* Utilize a simulação/fábrica/Remove-SimulationPublicIp.ps1 do [repositório](https://github.com/Azure/azure-iot-connected-factory). Passe o seu nome de implantação como parâmetro. Para uma implantação local, use `<your username>ConnFactoryLocal` . O script imprime o endereço IP do VM.

* No portal Azure, localize o grupo de recursos da sua implantação. Com exceção de uma implantação local, o grupo de recursos tem o nome especificado como solução ou nome de implantação. Para uma implantação local utilizando o script de construção, o nome do grupo de recursos é `<your username>ConnFactoryLocal` . Agora remova o recurso **de endereço IP público** do grupo de recursos.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Como posso inscrever-me na simulação VM?

A entrada na simulação de VM só é suportada se tiver implementado a sua solução utilizando o script PowerShell `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Se implementou a solução a partir de www.azureiotsolutions.com, não pode entrar no VM. Não é possível iniciar seduca, porque a palavra-passe é gerada aleatoriamente e não pode reiniciá-la.

1. Adicione um endereço IP público ao VM. Veja [como adiciono um endereço IP público à simulação VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Crie uma sessão de SSH para o seu VM utilizando o endereço IP do VM.
1. O nome de utilizador a utilizar é: `docker` .
1. A palavra-passe a utilizar depende da versão utilizada para implementar:
    * Para as soluções implementadas utilizando o script build.ps1 antes de 1 de junho de 2017, a palavra-passe é: `Passw0rd` .
    * Para soluções implementadas usando o script build.ps1 após 1 de junho de 2017, pode encontrar a palavra-passe no `<name of your deployment>.config.user` ficheiro. A palavra-passe é armazenada na definição **VmAdminPassword.** A palavra-passe é gerada aleatoriamente no tempo de implementação, a menos que especifique usando o `build.ps1` parâmetro do script `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Como paro e insindo todos os processos de estivadores na simulação VM?

1. Inscreva-se na simulação VM. Vê [como me inscrevo na simulação VM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Para verificar quais os recipientes ativos, corra: `docker ps` .
1. Para parar todos os recipientes de simulação, corra: `./stopsimulation` .
1. Para iniciar todos os recipientes de simulação:
    * Exporte uma variável de concha com o nome **IOTHUB_CONNECTIONSTRING**. Utilize o valor da **definição IotHubOwnerConnectionString** no `<name of your deployment>.config.user` ficheiro. Por exemplo:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Execute o `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Como actualo a simulação no VM?

Se tiver feito alterações na simulação, pode utilizar o script PowerShell `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory) utilizando o `updatedimulation` comando. Este script constrói todos os componentes de simulação, para a simulação no VM, carrega, instala e inicia-os.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Como é que descundo a cadeia de ligação do hub IoT usada pela minha solução?

Se implementou a sua solução com o `build.ps1` script no [repositório,](https://github.com/Azure/azure-iot-connected-factory)a cadeia de ligação é o valor do **IotHubOwnerConnectionString** no `<name of your deployment>.config.user` ficheiro.

Também pode encontrar a cadeia de ligação utilizando o portal Azure. No recurso IoT Hub no grupo de recursos da sua implantação, localize as definições de cadeia de ligação.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Que dispositivos IoT Hub utiliza a simulação da Fábrica Conectada?

A simulação auto regista os seguintes dispositivos:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munique0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munique0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Utilizando o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) ou a extensão IoT para a ferramenta [Azure CLI,](https://github.com/Azure/azure-iot-cli-extension) pode verificar quais os dispositivos registados no hub IoT que a sua solução está a utilizar. Para utilizar o explorador do dispositivo, precisa da cadeia de ligação para o hub IoT na sua implantação. Para utilizar a extensão IoT para Azure CLI, precisa do seu nome IoT Hub.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Como posso obter dados de registo dos componentes de simulação?

Todos os componentes da informação de login de simulação entram nos ficheiros de registo. Estes ficheiros podem ser encontrados no VM da pasta `home/docker/Logs` . Para recuperar os registos, pode utilizar o script PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Este guião precisa de entrar no VM. Talvez precise de fornecer credenciais para a inscrição. Vês [como posso inscrever-me na simulação vM](#how-do-i-sign-in-to-the-simulation-vm) para encontrar as credenciais.

O script adiciona/remove um endereço IP público para o VM, se ainda não tiver um e o remover. O script coloca todos os ficheiros de registo num arquivo e descarrega o arquivo para a sua estação de trabalho de desenvolvimento.

Alternativamente, inicie sessão no VM via SSH e inspecione os ficheiros de registo em tempo de execução.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Como posso verificar se a simulação está a enviar dados para a nuvem?

Com o [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) ou o comando [de monitorização de extensão Azure IoT CLI,](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) pode inspecionar os dados enviados para o IoT Hub a partir de determinados dispositivos. Para utilizar estas ferramentas, precisa de saber a cadeia de ligação do hub IoT na sua implantação. Veja [como descuito a cadeia de ligação do hub IoT usado pela minha solução?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Inspecione os dados enviados por um dos dispositivos editoriais:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munique0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Se não vir nenhum dado enviado para o IoT Hub, então há um problema com a simulação. Como primeiro passo de análise, deve analisar os ficheiros de registo dos componentes de simulação. Veja [como posso obter dados de registo dos componentes de simulação?](#how-can-i-get-log-data-from-the-simulation-components) Em seguida, tente parar e iniciar a simulação e se ainda não houver dados enviados, atualize completamente a simulação. Veja [como atualizei a simulação no VM?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Como posso ativar um mapa interativo na minha solução de Fábrica Conectada?

Para ativar um mapa interativo na sua solução De Fábrica Conectada, tem de ter uma conta Azure Maps.

Ao ser implantado a partir de [www.azureiotsolutions.com,](https://www.azureiotsolutions.com)o processo de implantação adiciona uma conta Azure Maps ao grupo de recursos que contém os serviços de acelerador de solução.

Quando implementar o `build.ps1` script no repositório GitHub da Fábrica Conectada, desloque a variável ambiente `$env:MapApiQueryKey` na janela de construção para a chave da [sua conta Azure Maps](../azure-maps/how-to-manage-account-keys.md). O mapa interativo é ativado automaticamente.

Também pode adicionar uma chave de conta Azure Maps ao seu acelerador de solução após a implementação. Navegue até ao portal Azure e aceda ao recurso do Serviço de Aplicações na sua implementação de Fábrica Conectada. Navegue para **as definições de Aplicação**, onde encontra uma secção **Definições de aplicação**. Desapaça o **MapApiQueryKey** na [chave da sua conta Azure Maps](../azure-maps/how-to-manage-account-keys.md). Guarde as definições e, em seguida, navegue para **a visão geral** e reinicie o Serviço de Aplicações.

### <a name="how-do-i-create-an-azure-maps-account"></a>Como posso criar uma conta Azure Maps?

Veja, [como gerir a sua conta e chaves Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Como obter a sua chave de conta Azure Maps

Veja, [como gerir a sua conta e chaves Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Como ativar o mapa interativo enquanto depura localmente?

Para ativar o mapa interativo enquanto estiver a depurar localmente, defina o valor da definição `MapApiQueryKey` nos ficheiros `local.user.config` e na raiz da sua `<yourdeploymentname>.user.config` implementação para o valor do **QueryKey** que copiou anteriormente.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Como uso uma imagem diferente na página inicial do meu painel?

Para alterar a imagem estática mostrada na página inicial do painel, substitua a imagem `WebApp\Content\img\world.jpg` . Em seguida, reconstruir e redistribuir o WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Como uso dispositivos UA não OPC com fábrica conectada?

Para enviar dados de telemetria de dispositivos UA não OPC para a Fábrica Conectada:

1. [Configure uma nova estação na topologia da Fábrica Conectada](iot-accelerators-connected-factory-configure.md) no `ContosoTopologyDescription.json` ficheiro.

1. Ingerir os dados de telemetria no formato JSON compatível com a Fábrica Conectada:

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

1. O formato `<timestamp>` de: `2017-12-08T19:24:51.886753Z`

1. Reinicie o Serviço de Aplicações de Fábrica Conectada.

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Implementar acelerador de solução de fábrica conectado](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](../iot-fundamentals/iot-security-ground-up.md)
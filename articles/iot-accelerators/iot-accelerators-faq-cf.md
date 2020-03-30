---
title: Solução de Fábrica Conectada FAQ - Azure Microsoft Docs
description: Este artigo responde às perguntas frequentes para o acelerador de soluções Connected Factory. Inclui links para o repositório GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c84452ff71fa34a65b2e56ec753b68bf551c7e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826284"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Perguntas frequentes para acelerador de soluções Connected Factory

Consulte também as [FAQ](iot-accelerators-faq.md) gerais para aceleradores de soluções IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Onde posso encontrar o código fonte para o acelerador de soluções?

O código fonte é armazenado no seguinte repositório GitHub:

* [Acelerador de solução de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>O que é OPC UA?

A OPC Unified Architecture (UA), lançada em 2008, é uma norma de interoperabilidade independente com plataforma, orientada para o serviço. O OPC UA é utilizado por vários sistemas industriais e dispositivos tais como Computadores da indústria, PLCs e sensores. O OPC UA integra a funcionalidade das especificações OPC Classic num quadro extensível com segurança incorporada. É um padrão que é impulsionado pela Fundação OPC. A [Fundação OPC](https://opcfoundation.org/) é uma organização sem fins lucrativos com mais de 440 membros. O objetivo da organização é usar especificações oPC para facilitar interoperabilidade multi-fornecedor, multiplataforma, segura e fiável através de:

* Infraestrutura
* Especificações
* Tecnologia
* Processos

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Porque é que a Microsoft escolheu o OPC UA para o acelerador de soluções Connected Factory?

A Microsoft escolheu o OPC UA porque é um padrão aberto, não proprietário, independente de plataforma, reconhecido pela indústria e comprovado. É um requisito para soluções de arquitetura de referência Industrie 4.0 (RAMI4.0) que garantam a interoperabilidade entre um vasto conjunto de processos e equipamentos de fabrico. A Microsoft vê a procura dos seus clientes para construir soluções Industrie 4.0. O apoio à OPC UA ajuda a reduzir a barreira para os clientes atingirem os seus objetivos e fornece-lhes valor de negócio imediato.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Como adiciono um endereço IP público ao VM de simulação?

Tem duas opções para adicionar o endereço IP:

* Utilize o script `Simulation/Factory/Add-SimulationPublicIp.ps1` PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory). Passe o seu nome de implantação como parâmetro. Para uma implantação `<your username>ConnFactoryLocal`local, utilize . O script imprime o endereço IP do VM.

* No portal Azure, localize o grupo de recursos da sua implantação. Com exceção de uma implantação local, o grupo de recursos tem o nome que especifica como solução ou nome de implantação. Para uma implantação local usando o script de `<your username>ConnFactoryLocal`construção, o nome do grupo de recursos é . Adicione agora um novo recurso **de endereço IP público** ao grupo de recursos.

> [!NOTE]
> Em qualquer dos casos, certifique-se de instalar os patches mais recentes seguindo as instruções no site da [Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Mantenha a instalação atualizada durante enquanto o seu VM estiver acessível através de um endereço IP público.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Como retiro o endereço IP público para a simulação VM?

Tem duas opções para remover o endereço IP:

* Utilize a simulação/simulação do script PowerShell/Factory/Remove-SimulationPublicIp.ps1 do [repositório](https://github.com/Azure/azure-iot-connected-factory). Passe o seu nome de implantação como parâmetro. Para uma implantação `<your username>ConnFactoryLocal`local, utilize . O script imprime o endereço IP do VM.

* No portal Azure, localize o grupo de recursos da sua implantação. Com exceção de uma implantação local, o grupo de recursos tem o nome que especifica como solução ou nome de implantação. Para uma implantação local usando o script de `<your username>ConnFactoryLocal`construção, o nome do grupo de recursos é . Retire agora o recurso de **endereço IP público** do grupo de recursos.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Como posso entrar na simulação vM?

A sessão no VM de simulação só é suportada se `build.ps1` tiver implementado a sua solução utilizando o script PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Se implementou a solução a partir de www.azureiotsolutions.com, não pode iniciar sessão no VM. Não é possível iniciar sessão, porque a palavra-passe é gerada aleatoriamente e não pode resetá-la.

1. Adicione um endereço IP público ao VM. Veja [como adiciono um endereço IP público ao VM de simulação?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Crie uma sessão SSH no seu VM utilizando o endereço IP do VM.
1. O nome de utilizador `docker`a utilizar é: .
1. A palavra-passe a utilizar depende da versão que usou para implementar:
    * Para soluções implementadas utilizando o script build.ps1 antes de 1 `Passw0rd`de junho de 2017, a palavra-passe é: .
    * Para soluções implementadas utilizando o script build.ps1 após 1 de junho `<name of your deployment>.config.user` de 2017, pode encontrar a palavra-passe no ficheiro. A palavra-passe é armazenada na definição **VmAdminPassword.** A palavra-passe é gerada aleatoriamente no `build.ps1` momento de implementação, a menos que especifique-a usando o parâmetro do script`-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Como paro e começo todos os processos de estivadores na simulação vM?

1. Inscreva-se na simulação VM. Vê [como posso entrar na simulação vM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Para verificar quais os recipientes `docker ps`ativos, corra: .
1. Para parar todos os recipientes de simulação, corra: `./stopsimulation`.
1. Para iniciar todos os recipientes de simulação:
    * Exportar uma variável de concha com o nome **IOTHUB_CONNECTIONSTRING**. Utilize o valor da definição **IotHubOwnerConnectionString** no `<name of your deployment>.config.user` ficheiro. Por exemplo:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Execute `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Como atualiza a simulação no VM?

Se tiver feito alterações na simulação, pode `build.ps1` utilizar o script PowerShell `updatedimulation` no [repositório](https://github.com/Azure/azure-iot-connected-factory) utilizando o comando. Este script constrói todos os componentes de simulação, para a simulação no VM, carrega, instala e inicia-os.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Como desculhei a cadeia de ligação do centro ioT usado pela minha solução?

Se implementou a sua `build.ps1` solução com o script no [repositório,](https://github.com/Azure/azure-iot-connected-factory)a cadeia de `<name of your deployment>.config.user` ligação é o valor do **IotHubOwnerConnectionString** no ficheiro.

Também pode encontrar a cadeia de ligação utilizando o portal Azure. No recurso IoT Hub no grupo de recursos da sua implementação, localize as definições de cadeia de ligação.

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
* editor.munique0.corp.contoso
* editor.rio.corp.contoso
* publisher.seattle.corp.contoso

Utilizando o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [a extensão IoT para](https://github.com/Azure/azure-iot-cli-extension) a ferramenta Azure CLI, pode verificar quais os dispositivos registados no hub IoT que a sua solução está a utilizar. Para utilizar o explorador do dispositivo, necessita da cadeia de ligação para o hub IoT na sua implantação. Para utilizar a extensão IoT para o Azure CLI, precisa do seu nome IoT Hub.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Como posso obter dados de registo dos componentes de simulação?

Todos os componentes da informação de simulação para registar ficheiros. Estes ficheiros podem ser encontrados `home/docker/Logs`no VM na pasta . Para recuperar os registos, pode utilizar `Simulation/Factory/Get-SimulationLogs.ps1` o script PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Este guião precisa de entrar no VM. Pode ser necessário fornecer credenciais para o inserido. Vê [como posso entrar na simulação vM?](#how-do-i-sign-in-to-the-simulation-vm)

O script adiciona/remove um endereço IP público para o VM, se ainda não tiver um e o remove. O script coloca todos os ficheiros de registo num arquivo e descarrega o arquivo para a sua estação de trabalho de desenvolvimento.

Em alternativa, inicie sessão no VM via SSH e inspecione os ficheiros de registo no prazo de execução.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Como posso verificar se a simulação está a enviar dados para a nuvem?

Com o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou o comando de [monitorização de extensão ClI Azure IoT,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) pode inspecionar os dados enviados para o IoT Hub a partir de certos dispositivos. Para utilizar estas ferramentas, precisa de saber a cadeia de ligação para o hub IoT na sua implantação. Vê [como desculhei a cadeia de ligação do centro ioT usado pela minha solução?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Inspecione os dados enviados por um dos dispositivos da editora:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* editor.munique0.corp.contoso
* editor.rio.corp.contoso
* publisher.seattle.corp.contoso

Se não vir dados enviados para o IoT Hub, então há um problema com a simulação. Como primeiro passo de análise, deve analisar os ficheiros de registo dos componentes de simulação. Vê [como posso obter dados de registo dos componentes de simulação?](#how-can-i-get-log-data-from-the-simulation-components) Em seguida, tente parar e iniciar a simulação e se ainda não houver dados enviados, atualize completamente a simulação. Veja [como atualizo a simulação no VM?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Como posso permitir um mapa interativo na minha solução Connected Factory?

Para ativar um mapa interativo na sua solução Connected Factory, deve ter uma conta Azure Maps.

Ao ser implantado a partir de [www.azureiotsolutions.com,](https://www.azureiotsolutions.com)o processo de implantação adiciona uma conta Azure Maps ao grupo de recursos que contém os serviços de aceleração de soluções.

Quando implementa `build.ps1` o script no repositório GitHub de `$env:MapApiQueryKey` fábrica conectada definir a variável ambiente na janela de construção para a [chave da sua conta Azure Maps](../azure-maps/how-to-manage-account-keys.md). O mapa interativo é então ativado automaticamente.

Também pode adicionar uma chave de conta Azure Maps ao seu acelerador de soluções após a implementação. Navegue para o portal Azure e aceda ao recurso do Serviço de Aplicações na sua implantação da Fábrica Conectada. Navegue para as definições de **Aplicação,** onde encontre uma secção **As definições**de aplicação . Desloque o **MapApiQueryKey** na [chave da sua conta Azure Maps](../azure-maps/how-to-manage-account-keys.md). Guarde as definições e, em seguida, navegue para **a visão geral** e reinicie o Serviço de Aplicações.

### <a name="how-do-i-create-an-azure-maps-account"></a>Como crio uma conta Azure Maps?

Veja, [Como gerir a sua conta Azure Maps e chaves](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Como obter a chave de conta Do Mapa Azure

Veja, [Como gerir a sua conta Azure Maps e chaves](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Como permitir o mapa interativo enquanto depura localmente?

Para ativar o mapa interativo enquanto está a depurar localmente, defina o valor da `MapApiQueryKey` definição nos ficheiros `local.user.config` e `<yourdeploymentname>.user.config` na raiz da sua implementação para o valor do **QueryKey** que copiou anteriormente.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Como uso uma imagem diferente na página inicial do meu painel?

Para alterar a imagem estática mostrada io a página `WebApp\Content\img\world.jpg`inicial do painel de instrumentos, substitua a imagem . Em seguida, reconstruir e reimplantar o WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Como uso dispositivos UA não OPC com fábrica conectada?

Para enviar dados de telemetria de dispositivos ua não OPC para fábrica conectada:

1. [Configure uma nova estação na topologia](iot-accelerators-connected-factory-configure.md) da Fábrica Conectada no `ContosoTopologyDescription.json` ficheiro.

1. Ingerir os dados de telemetria em formato JSON compatível com fábrica conectada:

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

1. O formato é: `<timestamp>``2017-12-08T19:24:51.886753Z`

1. Reiniciar o Serviço de Aplicações de Fábrica Conectada.

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implementar acelerador de solução de fábrica conectada](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)

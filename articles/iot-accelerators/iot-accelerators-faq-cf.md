---
title: Perguntas frequentes da solução de fábrica conectada – Azure | Microsoft Docs
description: Este artigo responde às perguntas frequentes sobre o Solution Accelerator da fábrica conectada. Ele inclui links para o repositório GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c84452ff71fa34a65b2e56ec753b68bf551c7e35
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826284"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Perguntas frequentes sobre o Solution Accelerator da fábrica conectada

Consulte também as [perguntas frequentes](iot-accelerators-faq.md) gerais para os aceleradores de solução de IOT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Onde posso encontrar o código-fonte do Solution Accelerator?

O código-fonte é armazenado no seguinte repositório GitHub:

* [Acelerador de soluções Fábrica Ligada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>O que é o OPC UA?

A arquitetura unificada OPC (UA), lançada em 2008, é um padrão de interoperabilidade orientado a serviços e independente de plataforma. O OPC UA é usado por vários sistemas e dispositivos industriais, como PCs, PLCs e sensores do setor. O OPC UA integra a funcionalidade das especificações do OPC Classic em uma estrutura extensível com segurança interna. É um padrão orientado pela OPC Foundation. A [OPC Foundation](https://opcfoundation.org/) é uma organização sem fins lucrativos com mais de 440 membros. O objetivo da organização é usar as especificações OPC para facilitar a interoperabilidade segura e confiável de vários fornecedores por meio de:

* Infraestrutura
* Especificações
* Tecnologia
* Processos

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Por que a Microsoft escolheu o OPC UA para o Solution Accelerator da fábrica conectada?

A Microsoft escolheu o OPC UA porque é um padrão aberto, não proprietário, independente de plataforma, reconhecido pelo setor e comprovado pela indústria. É um requisito para soluções de arquitetura de referência do Industrie 4,0 (RAMI 4.0) garantindo a interoperabilidade entre um amplo conjunto de processos e equipamentos de fabricação. A Microsoft vê a demanda de seus clientes para criar soluções Industrie 4,0. O suporte para OPC UA ajuda a diminuir a barreira para os clientes alcançarem suas metas e fornecer um valor comercial imediato a eles.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Como fazer adicionar um endereço IP público à VM de simulação?

Você tem duas opções para adicionar o endereço IP:

* Use o `Simulation/Factory/Add-SimulationPublicIp.ps1` de script do PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory). Passe seu nome de implantação como um parâmetro. Para uma implantação local, use `<your username>ConnFactoryLocal`. O script imprime o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos de sua implantação. Exceto para uma implantação local, o grupo de recursos tem o nome especificado como solução ou nome da implantação. Para uma implantação local usando o script de compilação, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Agora, adicione um novo recurso de **endereço IP público** ao grupo de recursos.

> [!NOTE]
> Em ambos os casos, certifique-se de instalar os patches mais recentes seguindo as instruções no [site do Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Mantenha a instalação atualizada por enquanto sua VM estiver acessível por meio de um endereço IP público.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Como fazer remover o endereço IP público para a VM de simulação?

Você tem duas opções para remover o endereço IP:

* Use a simulação de script do PowerShell/Factory/Remove-SimulationPublicIp. ps1 do [repositório](https://github.com/Azure/azure-iot-connected-factory). Passe seu nome de implantação como um parâmetro. Para uma implantação local, use `<your username>ConnFactoryLocal`. O script imprime o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos de sua implantação. Exceto para uma implantação local, o grupo de recursos tem o nome especificado como solução ou nome da implantação. Para uma implantação local usando o script de compilação, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Agora, remova o recurso de **endereço IP público** do grupo de recursos.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Como fazer entrar na VM de simulação?

Entrar na VM de simulação só terá suporte se você tiver implantado sua solução usando o script do PowerShell `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Se você implantou a solução do www.azureiotsolutions.com, não é possível entrar na VM. Não é possível entrar, pois a senha é gerada aleatoriamente e você não pode redefini-la.

1. Adicione um endereço IP público à VM. Consulte [como fazer adicionar um endereço IP público à VM de simulação?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Crie uma sessão SSH para sua VM usando o endereço IP da VM.
1. O nome de usuário a ser usado é: `docker`.
1. A senha a ser usada depende da versão que você usou para implantar:
    * Para soluções implantadas usando o script Build. ps1 antes de 1 de junho de 2017, a senha é: `Passw0rd`.
    * Para soluções implantadas usando o script Build. ps1 após 1 de junho de 2017, você pode encontrar a senha no arquivo `<name of your deployment>.config.user`. A senha é armazenada na configuração **VmAdminPassword** . A senha é gerada aleatoriamente no momento da implantação, a menos que você a especifique usando o parâmetro de script `build.ps1` `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Como fazer parar e iniciar todos os processos do Docker na VM de simulação?

1. Entre na VM de simulação. Consulte [como fazer entrar na VM de simulação?](#how-do-i-sign-in-to-the-simulation-vm)
1. Para verificar quais contêineres estão ativos, execute: `docker ps`.
1. Para interromper todos os contêineres de simulação, execute: `./stopsimulation`.
1. Para iniciar todos os contêineres de simulação:
    * Exporte uma variável do shell com o nome **IOTHUB_CONNECTIONSTRING**. Use o valor da configuração **IotHubOwnerConnectionString** no arquivo `<name of your deployment>.config.user`. Por exemplo:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Execute `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Como fazer atualizar a simulação na VM?

Se você tiver feito alterações na simulação, poderá usar o script do PowerShell `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory) usando o comando `updatedimulation`. Esse script compila todos os componentes de simulação, interrompe a simulação na VM, carrega, instala e inicia-as.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Como fazer descobrir a cadeia de conexão do Hub IoT usada pela minha solução?

Se você implantou sua solução com o script `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory), a cadeia de conexão é o valor de **IotHubOwnerConnectionString** no arquivo `<name of your deployment>.config.user`.

Você também pode encontrar a cadeia de conexão usando o portal do Azure. No recurso do Hub IoT no grupo de recursos da sua implantação, localize as configurações da cadeia de conexão.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Quais dispositivos do Hub IoT a simulação de fábrica conectada usa?

A simulação registra automaticamente os seguintes dispositivos:

* proxy. Pequim. Corp. contoso
* proxy. Capetown. Corp. contoso
* proxy. Mumbai. Corp. contoso
* proxy. munich0. Corp. contoso
* proxy. rio. Corp. contoso
* proxy. Seattle. Corp. contoso
* Publisher. Pequim. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Publisher. Mumbai. Corp. contoso
* Publisher. munich0. Corp. contoso
* Publisher. rio. Corp. contoso
* Publisher. Seattle. Corp. contoso

Usando o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [a extensão de IOT para a ferramenta CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) , você pode verificar quais dispositivos estão registrados no Hub IOT que sua solução está usando. Para usar o Gerenciador de dispositivos, você precisa da cadeia de conexão para o Hub IoT em sua implantação. Para usar a extensão de IoT para CLI do Azure, você precisa do nome do Hub IoT.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Como posso obter dados de log dos componentes de simulação?

Todos os componentes na simulação registram informações em arquivos de log. Esses arquivos podem ser encontrados na VM na pasta `home/docker/Logs`. Para recuperar os logs, você pode usar o script do PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Esse script precisa entrar na VM. Talvez seja necessário fornecer credenciais para a entrada. Consulte [como fazer entrar na VM de simulação?](#how-do-i-sign-in-to-the-simulation-vm) para localizar as credenciais.

O script adiciona/remove um endereço IP público para a VM, se ainda não tiver uma e a remove. O script coloca todos os arquivos de log em um arquivo morto e baixa o arquivo em sua estação de trabalho de desenvolvimento.

Como alternativa, faça logon na VM via SSH e inspecione os arquivos de log em tempo de execução.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Como posso verificar se a simulação está enviando dados para a nuvem?

Com o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou o comando [Monitor-Events de extensão da CLI do Azure IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) , você pode inspecionar os dados enviados ao Hub IOT de determinados dispositivos. Para usar essas ferramentas, você precisa saber a cadeia de conexão para o Hub IoT em sua implantação. Veja [como fazer descobrir a cadeia de conexão do Hub IOT usada pela minha solução?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Inspecione os dados enviados por um dos dispositivos do Publicador:

* Publisher. Pequim. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Publisher. Mumbai. Corp. contoso
* Publisher. munich0. Corp. contoso
* Publisher. rio. Corp. contoso
* Publisher. Seattle. Corp. contoso

Se você não vir dados enviados ao Hub IoT, haverá um problema com a simulação. Como uma primeira etapa de análise, você deve analisar os arquivos de log dos componentes de simulação. Veja [como posso obter dados de log dos componentes de simulação?](#how-can-i-get-log-data-from-the-simulation-components) Em seguida, tente parar e iniciar a simulação e, se ainda não houver dados enviados, atualize a simulação completamente. Veja [como fazer atualizar a simulação na VM?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Como fazer habilitar um mapa interativo em minha solução de fábrica conectada?

Para habilitar um mapa interativo em sua solução de fábrica conectada, você deve ter uma conta do Azure Maps.

Ao implantar do [www.azureiotsolutions.com](https://www.azureiotsolutions.com), o processo de implantação adiciona uma conta do Azure Maps ao grupo de recursos que contém os serviços do Solution Accelerator.

Quando você implanta usando o script `build.ps1` no repositório GitHub da fábrica conectada, defina a variável de ambiente `$env:MapApiQueryKey` na janela de compilação como a [chave de sua conta do Azure Maps](../azure-maps/how-to-manage-account-keys.md). O mapa interativo é então habilitado automaticamente.

Você também pode adicionar uma chave de conta do Azure Maps ao Solution Accelerator após a implantação. Navegue até a portal do Azure e acesse o recurso do serviço de aplicativo em sua implantação de fábrica conectada. Navegue até **configurações do aplicativo**, onde você encontrará uma seção **configurações do aplicativo**. Defina o **MapApiQueryKey** como a [chave de sua conta do Azure Maps](../azure-maps/how-to-manage-account-keys.md). Salve as configurações e, em seguida, navegue até **visão geral** e reinicie o serviço de aplicativo.

### <a name="how-do-i-create-an-azure-maps-account"></a>Como fazer criar uma conta do Azure Maps?

Consulte [como gerenciar sua conta e chaves do Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Como obter sua chave de conta do Azure Maps

Consulte [como gerenciar sua conta e chaves do Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Como habilitar o mapa interativo durante a depuração local?

Para habilitar o mapa interativo enquanto você está Depurando localmente, defina o valor da configuração `MapApiQueryKey` no `local.user.config` arquivos e `<yourdeploymentname>.user.config` na raiz da sua implantação para o valor do **QueryKey** que você copiou anteriormente.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Como fazer usar uma imagem diferente na home page do meu painel?

Para alterar a imagem estática mostrada e/s a home page do painel, substitua a imagem `WebApp\Content\img\world.jpg`. Em seguida, recompile e reimplante o WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Como fazer usar dispositivos fora do OPC UA com a fábrica conectada?

Para enviar dados de telemetria de dispositivos não OPC UA para a fábrica conectada:

1. [Configure uma nova estação na topologia da fábrica conectada](iot-accelerators-connected-factory-configure.md) no arquivo `ContosoTopologyDescription.json`.

1. Ingerir os dados de telemetria no formato JSON compatível com a fábrica conectada:

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

1. Reinicie o serviço de aplicativo de fábrica conectado.

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implantar o Solution Accelerator da fábrica conectada](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)

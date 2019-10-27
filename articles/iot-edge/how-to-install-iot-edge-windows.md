---
title: Instalar o Azure IoT Edge no Windows | Microsoft Docs
description: Azure IoT Edge instruções de instalação no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: ae3f866ff5e4266983117e93f2aab5982065947e
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964394"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o Azure IoT Edge Runtime no Windows

O tempo de execução de Azure IoT Edge é o que transforma um dispositivo em um dispositivo IoT Edge. O tempo de execução pode ser implantado em dispositivos tão pequenos quanto um Raspberry Pi ou tão grande quanto um servidor industrial. Depois que um dispositivo é configurado com o tempo de execução de IoT Edge, você pode começar a implantar lógica de negócios para ele da nuvem. 

Para saber mais sobre o tempo de execução de IoT Edge, consulte [entender o tempo de execução de Azure IOT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o Azure IoT Edge Runtime no seu sistema Windows x64 (AMD/Intel) usando contêineres do Windows.

> [!NOTE]
> Um problema conhecido do sistema operacional Windows impede a transição para os Estados de energia de suspensão e hibernação quando IoT Edge módulos (contêineres do Windows nano Server com isolamento de processo) estão em execução. Esse problema afeta a vida útil da bateria no dispositivo.
>
> Como alternativa, use o comando `Stop-Service iotedge` para interromper qualquer módulo IoT Edge em execução antes de usar esses Estados de energia. 

O uso de contêineres do Linux em sistemas Windows não é uma configuração de produção recomendada ou com suporte para Azure IoT Edge. No entanto, ele pode ser usado para fins de desenvolvimento e teste. Para saber mais, confira [usar o IOT Edge no Windows para executar contêineres do Linux](how-to-install-iot-edge-windows-with-linux.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Pré-requisitos

Use esta seção para examinar se o dispositivo Windows pode dar suporte a IoT Edge e prepará-lo para um mecanismo de contêiner antes da instalação. 

### <a name="supported-windows-versions"></a>Versões do Windows com suporte

Para cenários de desenvolvimento e teste, Azure IoT Edge com contêineres do Windows podem ser instalados em qualquer versão do Windows 10 ou Windows Server 2019 (Build 17763) que ofereça suporte ao recurso de contêineres. Para obter informações sobre quais sistemas operacionais têm suporte no momento para cenários de produção, consulte [Azure IOT Edge sistemas com suporte](support.md#operating-systems). 

Os dispositivos IoT Core devem incluir o recurso opcional de contêineres IoT core-Windows para dar suporte ao tempo de execução de IoT Edge. Use o seguinte comando em uma [sessão remota do PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) para verificar se os contêineres do Windows têm suporte em seu dispositivo: 

```powershell
Get-Service vmcompute
```

Se o serviço estiver presente, você deverá obter uma resposta bem-sucedida com o status do serviço listado como **em execução**. Se o serviço vmcompute não for encontrado, o dispositivo não atenderá aos requisitos para IoT Edge. Entre em contato com seu provedor de hardware para perguntar sobre o suporte para esse recurso. 

### <a name="prepare-for-a-container-engine"></a>Preparar para um mecanismo de contêiner 

Azure IoT Edge se baseia em um mecanismo de contêiner [compatível com o OCI](https://www.opencontainers.org/) . Para cenários de produção, use o mecanismo Moby incluído no script de instalação para executar contêineres do Windows em seu dispositivo Windows. 

## <a name="install-iot-edge-on-a-new-device"></a>Instalar IoT Edge em um novo dispositivo

>[!NOTE]
>Azure IoT Edge pacotes de software estão sujeitos aos termos de licença localizados nos pacotes (no diretório de licença). Leia os termos de licença antes de usar o pacote. A instalação e o uso do pacote constituem sua aceitação desses termos. Se você não concordar com os termos de licença, não use o pacote.

Um script do PowerShell baixa e instala o daemon de segurança Azure IoT Edge. Em seguida, o daemon de segurança inicia o primeiro de dois módulos de tempo de execução, o agente de IoT Edge, que habilita as implantações remotas de outros módulos. 

>[!TIP]
>Para dispositivos IoT Core, é recomendável executar os comandos de instalação usando uma sessão RemotePowerShell. Para obter mais informações, consulte [usando o PowerShell para Windows IOT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Ao instalar o IoT Edge Runtime pela primeira vez em um dispositivo, você precisa provisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo de IoT Edge pode ser provisionado manualmente usando uma cadeia de conexão de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o DPS (serviço de provisionamento de dispositivos) para provisionar automaticamente os dispositivos, o que é útil quando você tem muitos dispositivos a serem configurados. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado. 

As seções a seguir descrevem os casos de uso comuns e os parâmetros para o script de instalação IoT Edge em um novo dispositivo. 

### <a name="option-1-install-and-manually-provision"></a>Opção 1: instalar e provisionar manualmente

Nesta primeira opção, você fornece uma **cadeia de conexão de dispositivo** gerada pelo Hub IOT para provisionar o dispositivo. 

Este exemplo demonstra uma instalação manual com contêineres do Windows:

1. Se você ainda não fez isso, registre um novo dispositivo de IoT Edge e recupere a **cadeia de conexão do dispositivo**. Copie a cadeia de conexão a ser usada posteriormente nesta seção. Você pode concluir esta etapa usando as seguintes ferramentas:

   * [Portal do Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [CLI do Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Execute o PowerShell como administrador.

   >[!NOTE]
   >Use uma sessão AMD64 do PowerShell para instalar IoT Edge, não o PowerShell (x86). Se você não tiver certeza de qual tipo de sessão está usando, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se o computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do Moby e o tempo de execução do IOT Edge. O padrão do comando é usar contêineres do Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste ponto, os dispositivos IoT Core podem ser reiniciados automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar a reinicialização. Nesse caso, reinicie o dispositivo agora. Quando o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

5. O comando **Initialize-IoTEdge** configura o tempo de execução de IOT Edge em seu computador. O comando usa como padrão o provisionamento manual com contêineres do Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando solicitado, forneça a cadeia de conexão do dispositivo que você recuperou na etapa 1. A cadeia de conexão do dispositivo associa o dispositivo físico a uma ID do dispositivo no Hub IoT. 

   A cadeia de conexão do dispositivo usa o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Use as etapas em [verificar a instalação bem-sucedida](#verify-successful-installation) para verificar o status de IOT Edge em seu dispositivo. 

Ao instalar e provisionar um dispositivo manualmente, você pode usar parâmetros adicionais para modificar a instalação, incluindo:

* Direcionar o tráfego para passar por um servidor proxy
* Aponte o instalador para um diretório offline
* Declare uma imagem de contêiner de agente específica e forneça credenciais se ele estiver em um registro privado

Para obter mais informações sobre essas opções de instalação, pule adiante para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: instalar e provisionar automaticamente

Nessa segunda opção, você provisiona o dispositivo usando o serviço de provisionamento de dispositivos do Hub IoT. Forneça a **ID de escopo** de uma instância do serviço de provisionamento de dispositivos junto com outras informações específicas ao seu [mecanismo de atestado](../iot-dps/concepts-security.md#attestation-mechanism)preferido:

* [Criar e provisionar um dispositivo de IoT Edge simulado com um TPM virtual no Windows](how-to-auto-provision-simulated-device-windows.md)
* [Criar e provisionar um dispositivo IoT Edge usando o atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Ao instalar e provisionar um dispositivo automaticamente, você pode usar parâmetros adicionais para modificar a instalação, incluindo:

* Direcionar o tráfego para passar por um servidor proxy
* Aponte o instalador para um diretório offline
* Declare uma imagem de contêiner de agente específica e forneça credenciais se ele estiver em um registro privado

Para obter mais informações sobre essas opções de instalação, continue lendo este artigo ou pule para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-installation"></a>Instalação offline

Durante a instalação, dois arquivos são baixados:

* Microsoft Azure IoT Edge CAB, que contém o daemon de segurança do IoT Edge (iotedged), o mecanismo de contêiner do Moby e a CLI do Moby.
* MSI C++ do pacote redistribuível do Visual (tempo de execução do vc)

Você pode baixar um ou ambos os arquivos antes do tempo para o dispositivo e, em seguida, apontar o script de instalação no diretório que contém os arquivos. O instalador verifica o diretório primeiro e, em seguida, baixa apenas os componentes que não foram encontrados. Se todos os arquivos estiverem disponíveis offline, você poderá instalar o sem conexão com a Internet. Você também pode usar esse recurso para instalar uma versão específica dos componentes do.  

Para obter os arquivos de instalação mais recentes do IoT Edge juntamente com as versões anteriores, consulte [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases).

Para instalar com componentes offline, use o parâmetro `-OfflineInstallationPath` como parte do comando Deploy-IoTEdge e forneça o caminho absoluto para o diretório de arquivos. Por exemplo,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Você também pode usar o parâmetro caminho de instalação offline com o comando Update-IoTEdge, apresentado posteriormente neste artigo. 

## <a name="verify-successful-installation"></a>Verificar a instalação bem-sucedida

Verifique o estado do serviço IoT Edge. Ele deve estar listado como em execução.  

```powershell
Get-Service iotedge
```

Examine os logs de serviço dos últimos 5 minutos. Se acabar de instalar o IoT Edge Runtime, você poderá ver uma lista de erros do tempo entre a execução de **Deploy-IoTEdge** e **Initialize-IoTEdge**. Esses erros são esperados, pois o serviço está tentando iniciar antes de ser configurado. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Execute uma verificação automatizada para os erros de configuração e de rede mais comuns.

```powershell
iotedge check
```

Listar módulos em execução. Após uma nova instalação, o único módulo que você deve ver em execução é **edgeAgent**. Depois de [implantar os módulos do IOT Edge](how-to-deploy-modules-portal.md) pela primeira vez, o outro módulo do sistema, **edgeHub**, também será iniciado no dispositivo. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gerenciar contêineres de módulo

O serviço de IoT Edge requer um mecanismo de contêiner em execução em seu dispositivo. Quando você implanta um módulo em um dispositivo, o tempo de execução de IoT Edge usa o mecanismo de contêiner para efetuar pull da imagem de contêiner de um registro na nuvem. O serviço de IoT Edge permite que você interaja com seus módulos e recupere logs, mas, às vezes, convém usar o mecanismo de contêiner para interagir com o próprio contêiner. 

Para obter mais informações sobre conceitos de módulo, consulte [entender módulos Azure IOT Edge](iot-edge-modules.md). 

Se você estiver executando contêineres do Windows em seu dispositivo Windows IoT Edge, a instalação do IoT Edge incluirá o mecanismo de contêiner Moby. O mecanismo Moby foi baseado nos mesmos padrões que o Docker e foi projetado para ser executado em paralelo no mesmo computador que o Docker desktop. Por esse motivo, se você quiser direcionar contêineres gerenciados pelo mecanismo do Moby, será necessário direcionar especificamente esse mecanismo em vez do Docker. 

Por exemplo, para listar todas as imagens do Docker, use o seguinte comando:

```powershell
docker images
```

Para listar todas as imagens Moby, modifique o mesmo comando com um ponteiro para o mecanismo Moby: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

O URI do mecanismo é listado na saída do script de instalação ou você pode encontrá-lo na seção Configurações de tempo de execução do contêiner para o arquivo config. YAML. 

![moby_runtime URI em config. YAML](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Para obter mais informações sobre os comandos que você pode usar para interagir com contêineres e imagens em execução em seu dispositivo, consulte [interfaces de linha de comando do Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Atualizar uma instalação existente

Se você já tiver instalado o IoT Edge tempo de execução em um dispositivo antes e provisioná-lo com uma identidade do Hub IoT, poderá atualizar o tempo de execução sem precisar inserir novamente as informações do dispositivo. 

Para obter mais informações, consulte [atualizar o daemon de segurança IOT Edge e o tempo de execução](how-to-update-iot-edge.md).

Este exemplo mostra uma instalação que aponta para um arquivo de configuração existente e usa contêineres do Windows: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Ao atualizar IoT Edge, você pode usar parâmetros adicionais para modificar a atualização, incluindo:

* Direcionar o tráfego para passar por um servidor proxy ou
* Aponte o instalador para um diretório offline 
* Reiniciando sem uma solicitação, se necessário

Você não pode declarar uma imagem de contêiner de agente de IoT Edge com parâmetros de script porque essa informação já está definida no arquivo de configuração da instalação anterior. Se você quiser modificar a imagem de contêiner do agente, faça isso no arquivo config. YAML. 

Para obter mais informações sobre essas opções de atualização, use o comando `Get-Help Update-IoTEdge -full` ou consulte [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Se você quiser remover a instalação do IoT Edge do seu dispositivo Windows, use o comando a seguir em uma janela administrativa do PowerShell. Esse comando Remove o tempo de execução IoT Edge, juntamente com a configuração existente e os dados do mecanismo Moby. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

O comando Uninstall-IoTEdge não funciona no Windows IoT Core. Para remover IoT Edge de dispositivos Windows IoT Core, você precisa reimplantar sua imagem do Windows IoT Core. 

Para obter mais informações sobre as opções de desinstalação, use o comando `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As seções anteriores introduziram cenários de instalação comuns com exemplos de como usar parâmetros para modificar o script de instalação. Esta seção fornece tabelas de referência dos parâmetros comuns usados para instalar, atualizar ou desinstalar o IoT Edge. 

### <a name="deploy-iotedge"></a>Implantar-IoTEdge

O comando Deploy-IoTEdge baixa e implanta o daemon de segurança IoT Edge e suas dependências. O comando de implantação aceita esses parâmetros comuns, entre outros. Para obter a lista completa, use o comando `Get-Help Deploy-IoTEdge -full`.  

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres do Windows, IoT Edge usa o mecanismo de contêiner Moby incluído na instalação. Para contêineres do Linux, você precisa instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Acionista** | URL do proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se esse parâmetro estiver incluído, o instalador verificará o diretório listado quanto aos arquivos MSI de tempo de execução do IoT Edge cab e do VC Runtime necessários para a instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você poderá instalar IoT Edge sem uma conexão com a Internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web são feitas. Use este campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | nenhuma | Esse sinalizador permite que o script de implantação reinicie o computador sem avisar, se necessário. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

O comando Initialize-IoTEdge configura IoT Edge com a cadeia de conexão do dispositivo e detalhes operacionais. Grande parte das informações geradas por esse comando é armazenada no arquivo iotedge\config.YAML. O comando de inicialização aceita esses parâmetros comuns, entre outros. Para obter a lista completa, use o comando `Get-Help Initialize-IoTEdge -full`. 

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhuma | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, manual será o valor padrão.<br><br>Declara que você fornecerá uma cadeia de conexão do dispositivo para provisionar o dispositivo manualmente |
| **DPS** | Nenhuma | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, manual será o valor padrão.<br><br>Declara que você fornecerá uma ID de escopo do DPS (serviço de provisionamento de dispositivos) e a ID de registro do dispositivo a ser provisionada por meio do DPS.  |
| **DeviceConnectionString** | Uma cadeia de conexão de um dispositivo IoT Edge registrado em um hub IoT, entre aspas simples | **Necessário** para a instalação manual. Se você não fornecer uma cadeia de conexão nos parâmetros do script, você será solicitado a fornecer um durante a instalação. |
| **ScopeId** | Uma ID de escopo de uma instância do serviço de provisionamento de dispositivos associada ao Hub IoT. | **Necessário** para a instalação do DPS. Se você não fornecer uma ID de escopo nos parâmetros de script, você será solicitado a fornecer uma durante a instalação. |
| **RegistrationId** | Uma ID de registro gerada pelo seu dispositivo | **Necessário** para a instalação de DPS se estiver usando o TPM ou o atestado de chave simétrica. |
| **SymmetricKey** | A chave simétrica usada para provisionar a IoT Edge identidade do dispositivo ao usar o DPS | **Necessário** para a instalação de DPS se estiver usando o atestado de chave simétrica. |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres do Windows, IoT Edge usa o mecanismo de contêiner Moby incluído na instalação. Para contêineres do Linux, você precisa instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web são feitas. Use este campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI da imagem do agente de IoT Edge | Por padrão, uma nova instalação do IoT Edge usa a marca de rolagem mais recente para a imagem do agente de IoT Edge. Use esse parâmetro para definir uma marca específica para a versão da imagem ou para fornecer sua própria imagem do agente. Para obter mais informações, consulte [entender marcas de IOT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de Utilizador** | Nome de usuário do registro de contêiner | Use esse parâmetro somente se você definir o parâmetro-AgentImage para um contêiner em um registro privado. Forneça um nome de usuário com acesso ao registro. |
| **Palavra-passe** | Cadeia de senha segura | Use esse parâmetro somente se você definir o parâmetro-AgentImage para um contêiner em um registro privado. Forneça a senha para acessar o registro. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão. Para contêineres do Windows, um mecanismo de contêiner será incluído na instalação. Para contêineres do Linux, você precisa instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Acionista** | URL do proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web são feitas. Use este campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se esse parâmetro estiver incluído, o instalador verificará o diretório listado quanto aos arquivos MSI de tempo de execução do IoT Edge cab e do VC Runtime necessários para a instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você poderá instalar IoT Edge sem uma conexão com a Internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **RestartIfNeeded** | nenhuma | Esse sinalizador permite que o script de implantação reinicie o computador sem avisar, se necessário. |

### <a name="uninstall-iotedge"></a>Desinstalar-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Aplicação** | nenhuma | Esse sinalizador força a desinstalação caso a tentativa anterior de desinstalar não tenha sido bem-sucedida. 
| **RestartIfNeeded** | nenhuma | Esse sinalizador permite que o script de desinstalação reinicie o computador sem avisar, se necessário. |

## <a name="next-steps"></a>Passos seguintes

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, você pode [implantar IOT Edge módulos](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas para instalar o IoT Edge corretamente, confira a página de [solução de problemas](troubleshoot.md) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IOT Edge e o tempo de execução](how-to-update-iot-edge.md).

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
ms.openlocfilehash: e3f55f9be28a8b53f012e111e43ba1f495b1d585
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186474"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o Azure IoT Edge Runtime no Windows

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud.

Para saber mais sobre o tempo de funcionação do IoT Edge, consulte Compreender o tempo de funcionação do [Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista as etapas para instalar o Azure IoT Edge Runtime no seu sistema Windows x64 (AMD/Intel) usando contêineres do Windows.

> [!NOTE]
> Um problema conhecido do sistema operacional Windows impede a transição para os Estados de energia de suspensão e hibernação quando IoT Edge módulos (contêineres do Windows nano Server com isolamento de processo) estão em execução. Esse problema afeta a vida útil da bateria no dispositivo.
>
> Como sucinta, utilize o comando `Stop-Service iotedge` para parar qualquer módulo IoT Edge em funcionamento antes de utilizar estes estados de potência.

O uso de contêineres do Linux em sistemas Windows não é uma configuração de produção recomendada ou com suporte para Azure IoT Edge. No entanto, ele pode ser usado para fins de testes e de desenvolvimento. Para saber mais, consulte [Use IoT Edge no Windows para executar recipientes Linux](how-to-install-iot-edge-windows-with-linux.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte os [lançamentos do Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

## <a name="prerequisites"></a>Pré-requisitos

Use esta seção para examinar se o dispositivo Windows pode dar suporte a IoT Edge e prepará-lo para um mecanismo de contêiner antes da instalação.

### <a name="supported-windows-versions"></a>Versões suportadas do Windows

Para cenários de desenvolvimento e teste, Azure IoT Edge com contêineres do Windows podem ser instalados em qualquer versão do Windows 10 ou Windows Server 2019 (Build 17763) que ofereça suporte ao recurso de contêineres. Para obter informações sobre quais os sistemas operativos atualmente suportados para cenários de produção, consulte [os sistemas suportados pelo Azure IoT Edge.](support.md#operating-systems)

Os dispositivos IoT Core devem incluir o recurso opcional de contêineres IoT core-Windows para dar suporte ao tempo de execução de IoT Edge. Utilize o seguinte comando numa [sessão remota](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) da PowerShell para verificar se os recipientes do Windows são suportados no seu dispositivo:

```powershell
Get-Service vmcompute
```

Se o serviço estiver presente, deverá obter uma resposta bem sucedida com o estado de serviço listado como **execução**. Se o serviço vmcompute não for encontrado, o dispositivo não atenderá aos requisitos para IoT Edge. Entre em contato com seu provedor de hardware para perguntar sobre o suporte para esse recurso.

### <a name="prepare-for-a-container-engine"></a>Preparar para um mecanismo de contêiner

O Azure IoT Edge conta com um motor de contentor [compatível com OCI.](https://www.opencontainers.org/) Para cenários de produção, use o mecanismo Moby incluído no script de instalação para executar contêineres do Windows em seu dispositivo Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Instalar IoT Edge em um novo dispositivo

>[!NOTE]
>Pacotes de software do Azure IoT Edge são sujeita aos termos de licença localizados nos pacotes (no diretório de licença). Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

Um script do PowerShell baixa e instala o daemon de segurança Azure IoT Edge. Em seguida, o daemon de segurança inicia o primeiro de dois módulos de tempo de execução, o agente de IoT Edge, que habilita as implantações remotas de outros módulos.

>[!TIP]
>Para dispositivos IoT Core, é recomendável executar os comandos de instalação usando uma sessão RemotePowerShell. Para mais informações, consulte [A Utilização da PowerShell para windows ioT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Ao instalar o IoT Edge Runtime pela primeira vez em um dispositivo, você precisa provisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo de IoT Edge pode ser provisionado manualmente usando uma cadeia de conexão de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o DPS (serviço de provisionamento de dispositivos) para provisionar automaticamente os dispositivos, o que é útil quando você tem muitos dispositivos a serem configurados. Dependendo de sua escolha de aprovisionamento, escolha o script de instalação apropriado.

As seções a seguir descrevem os casos de uso comuns e os parâmetros para o script de instalação IoT Edge em um novo dispositivo.

### <a name="option-1-install-and-manually-provision"></a>Opção 1: Instalar e aprovisionar manualmente

Nesta primeira opção, fornece uma cadeia de **ligação** ao dispositivo gerada pelo IoT Hub para fornecer o dispositivo.

Este exemplo demonstra uma instalação manual com contêineres do Windows:

1. Se ainda não o fez, registe um novo dispositivo IoT Edge e recupere a **cadeia de ligação**do dispositivo . Copie a cadeia de conexão a ser usada posteriormente nesta seção. Você pode concluir esta etapa usando as seguintes ferramentas:

   * [Portal do Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [CLI do Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Executar powerShell como administrador.

   >[!NOTE]
   >Utilize uma sessão AMD64 da PowerShell para instalar o IoT Edge, não o PowerShell (x86). Se não tiver a certeza de que tipo de sessão está a usar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se a sua máquina Windows está numa versão suportada, liga a funcionalidade de contentores e, em seguida, descarrega o tempo de execução moby e o tempo de execução do IoT Edge. O padrão do comando é usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste ponto, os dispositivos IoT Core podem ser reiniciados automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar a reinicialização. Nesse caso, reinicie o dispositivo agora. Quando o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

5. O comando **Initialize-IoTEdge** configura o tempo de execução do IoT Edge na sua máquina. O comando não se aplica ao fornecimento manual com recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando solicitado, forneça a cadeia de conexão do dispositivo que você recuperou na etapa 1. A cadeia de conexão do dispositivo associa o dispositivo físico a uma ID do dispositivo no Hub IoT.

   A cadeia de ligação do dispositivo toma o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Utilize as medidas em Verificar a [instalação bem sucedida](#verify-successful-installation) para verificar o estado do IoT Edge no seu dispositivo.

Ao instalar e provisionar um dispositivo manualmente, você pode usar parâmetros adicionais para modificar a instalação, incluindo:

* Direcionar o tráfego para passar por um servidor proxy
* Aponte o instalador para um diretório offline
* Declare uma imagem de contêiner de agente específica e forneça credenciais se ele estiver em um registro privado

Para mais informações sobre estas opções de instalação, avance para saber sobre [todos os parâmetros](#all-installation-parameters)de instalação .

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: Instalar e aprovisionar automaticamente

Nessa segunda opção, você provisiona o dispositivo usando o serviço de provisionamento de dispositivos do Hub IoT. Forneça o ID de **âmbito** de uma instância do Serviço de Provisionamento de Dispositivos, juntamente com qualquer outra informação específica ao seu mecanismo de [atestado](../iot-dps/concepts-security.md#attestation-mechanism)preferido:

* [Crie e forre um dispositivo IoT Edge simulado com um TPM virtual no Windows](how-to-auto-provision-simulated-device-windows.md)
* [Criar e fornecer um dispositivo IoT Edge utilizando atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Ao instalar e provisionar um dispositivo automaticamente, você pode usar parâmetros adicionais para modificar a instalação, incluindo:

* Direcionar o tráfego para passar por um servidor proxy
* Aponte o instalador para um diretório offline
* Declare uma imagem de contêiner de agente específica e forneça credenciais se ele estiver em um registro privado

Para mais informações sobre estas opções de instalação, continue a ler este artigo ou salte para saber sobre [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Instalação de versão offline ou específica

Durante a instalação, dois arquivos são baixados:

* Microsoft Azure IoT Edge CAB, que contém o daemon de segurança do IoT Edge (iotedged), o mecanismo de contêiner do Moby e a CLI do Moby.
* Pacote C++ redistribuível visual (VC tempo de execução) MSI

Se o seu dispositivo estiver offline durante a instalação, ou se pretender instalar uma versão específica do IoT Edge, pode descarregar um ou ambos estes ficheiros antes do tempo para o dispositivo. Quando chegar a hora de instalar, aponte o script de instalação para o diretório que contém os ficheiros descarregados. O instalador verifica primeiro esse diretório, e depois só descarrega componentes que não são encontrados. Se todos os arquivos estiverem disponíveis offline, você poderá instalar o sem conexão com a Internet.

Para obter os mais recentes ficheiros de instalação IoT Edge juntamente com versões anteriores, consulte [lançamentos Do Edge Azure IoT.](https://github.com/Azure/azure-iotedge/releases)

Para instalar com componentes offline, utilize o parâmetro `-OfflineInstallationPath` como parte do comando Deploy-IoTEdge e forneça o caminho absoluto para o diretório de ficheiros. Por exemplo,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>O parâmetro `-OfflineInstallationPath` procura um ficheiro chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. Começando com a versão 1.0.9-rc4 do IoT Edge, existem dois ficheiros .cab disponíveis para utilização, um para dispositivos AMD64 e um para ARM32. Descarregue o ficheiro correto para o seu dispositivo e, em seguida, mude o nome do ficheiro para remover o sufixo de arquitetura.

O comando `Deploy-IoTEdge` instala os componentes IoT Edge e, em seguida, tem de continuar até ao comando `Initialize-IoTEdge` para fornecer o dispositivo com o seu ID e ligação do dispositivo IoT Hub. Ou executa o comando diretamente e fornece uma cadeia de ligação a partir do IoT Hub, ou utilize uma das ligações na secção anterior para aprender a fornecer automaticamente dispositivos com o Serviço de Provisionamento de Dispositivos.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

Também pode utilizar o parâmetro do caminho de instalação offline com o comando Update-IoTEdge.

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Verifique o estado do serviço IoT Edge. Ele deve estar listado como em execução.  

```powershell
Get-Service iotedge
```

Examine os registos do serviço dos últimos 5 minutos. Se terminar de instalar o tempo de funcionamento do IoT Edge, poderá ver uma lista de erros do momento entre executar **Deploy-IoTEdge** e **Initialize-IoTEdge**. Esses erros são esperados, pois o serviço está tentando iniciar antes de ser configurado.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Execute uma verificação automatizada para os erros de configuração e de rede mais comuns.

```powershell
iotedge check
```

Lista de módulos em execução. Depois de uma nova instalação, o único módulo que deve ver a correr é **o EdgeAgent**. Depois de implementar pela primeira vez [módulos IoT Edge,](how-to-deploy-modules-portal.md) o outro módulo de sistema, **edgeHub,** também começará no dispositivo.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gerenciar contêineres de módulo

O serviço de IoT Edge requer um mecanismo de contêiner em execução em seu dispositivo. Quando você implanta um módulo em um dispositivo, o tempo de execução de IoT Edge usa o mecanismo de contêiner para efetuar pull da imagem de contêiner de um registro na nuvem. O serviço de IoT Edge permite que você interaja com seus módulos e recupere logs, mas, às vezes, convém usar o mecanismo de contêiner para interagir com o próprio contêiner.

Para obter mais informações sobre os conceitos de módulos, consulte [módulos De Borda De IoT Do Min.](iot-edge-modules.md)

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

![moby_runtime uri em config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Para obter mais informações sobre comandos pode utilizar para interagir com recipientes e imagens em execução no seu dispositivo, consulte [as interfaces da linha de comando docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Desinstalar ioT edge

Se você quiser remover a instalação do IoT Edge do seu dispositivo Windows, use o comando a seguir em uma janela administrativa do PowerShell. Esse comando Remove o tempo de execução IoT Edge, juntamente com a configuração existente e os dados do mecanismo Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

O comando Uninstall-IoTEdge não funciona no Windows IoT Core. Para remover IoT Edge de dispositivos Windows IoT Core, você precisa reimplantar sua imagem do Windows IoT Core.

Para mais informações sobre opções de desinstalação, utilize o comando `Get-Help Uninstall-IoTEdge -full`.

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As seções anteriores introduziram cenários de instalação comuns com exemplos de como usar parâmetros para modificar o script de instalação. Esta seção fornece tabelas de referência dos parâmetros comuns usados para instalar, atualizar ou desinstalar o IoT Edge.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

O comando Deploy-IoTEdge baixa e implanta o daemon de segurança IoT Edge e suas dependências. O comando de implantação aceita esses parâmetros comuns, entre outros. Para a lista completa, utilize o comando `Get-Help Deploy-IoTEdge -full`.  

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Contentores** | **Janelas** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres do Windows, IoT Edge usa o mecanismo de contêiner Moby incluído na instalação. Para contêineres do Linux, você precisa instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Procuração** | URL do proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Caminho de Instalação Offline** | Caminho do diretório | Se esse parâmetro estiver incluído, o instalador verificará o diretório listado quanto aos arquivos MSI de tempo de execução do IoT Edge cab e do VC Runtime necessários para a instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você poderá instalar IoT Edge sem uma conexão com a Internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **InvocarWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web são feitas. Use este campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Reiniciar ifNeeded** | nenhuma | Esse sinalizador permite que o script de implantação reinicie o computador sem avisar, se necessário. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

O comando Initialize-IoTEdge configura IoT Edge com a cadeia de conexão do dispositivo e detalhes operacionais. Grande parte das informações geradas por esse comando é armazenada no arquivo iotedge\config.YAML. O comando de inicialização aceita esses parâmetros comuns, entre outros. Para a lista completa, utilize o comando `Get-Help Initialize-IoTEdge -full`.

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhuma | **Mudar o parâmetro**. Se nenhum tipo de provisionamento for especificado, manual será o valor padrão.<br><br>Declara que você fornecerá uma cadeia de conexão do dispositivo para provisionar o dispositivo manualmente |
| **Dps** | Nenhuma | **Mudar o parâmetro**. Se nenhum tipo de provisionamento for especificado, manual será o valor padrão.<br><br>Declara que você fornecerá uma ID de escopo do DPS (serviço de provisionamento de dispositivos) e a ID de registro do dispositivo a ser provisionada por meio do DPS.  |
| **String de conexão de dispositivos** | Uma cadeia de conexão de um dispositivo IoT Edge registrado em um hub IoT, entre aspas simples | **Necessário** para a instalação manual. Se você não fornecer uma cadeia de conexão nos parâmetros do script, você será solicitado a fornecer um durante a instalação. |
| **Scopeid** | Uma ID de escopo de uma instância do serviço de provisionamento de dispositivos associada ao Hub IoT. | **Necessário** para a instalação de DPS. Se você não fornecer uma ID de escopo nos parâmetros de script, você será solicitado a fornecer uma durante a instalação. |
| **RegistroId** | Uma ID de registro gerada pelo seu dispositivo | **Necessário** para a instalação de DPS se utilizar tPM ou atestado de chave simétrica. |
| **Chave Simétrica** | A chave simétrica usada para provisionar a IoT Edge identidade do dispositivo ao usar o DPS | **Necessário** para a instalação de DPS se utilizar o atestado da chave simétrica. |
| **Contentores** | **Janelas** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres do Windows, IoT Edge usa o mecanismo de contêiner Moby incluído na instalação. Para contêineres do Linux, você precisa instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **InvocarWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web são feitas. Use este campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI da imagem do agente de IoT Edge | Por padrão, uma nova instalação do IoT Edge usa a marca de rolagem mais recente para a imagem do agente de IoT Edge. Use esse parâmetro para definir uma marca específica para a versão da imagem ou para fornecer sua própria imagem do agente. Para mais informações, consulte [As tags De Borda IoT](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de Utilizador** | Nome de usuário do registro de contêiner | Use esse parâmetro somente se você definir o parâmetro-AgentImage para um contêiner em um registro privado. Forneça um nome de usuário com acesso ao registro. |
| **Palavra-passe** | Cadeia de senha segura | Use esse parâmetro somente se você definir o parâmetro-AgentImage para um contêiner em um registro privado. Forneça a senha para acessar o registro. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Contentores** | **Janelas** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão. Para contêineres do Windows, um mecanismo de contêiner será incluído na instalação. Para contêineres do Linux, você precisa instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Procuração** | URL do proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvocarWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web são feitas. Use este campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Caminho de Instalação Offline** | Caminho do diretório | Se esse parâmetro estiver incluído, o instalador verificará o diretório listado quanto aos arquivos MSI de tempo de execução do IoT Edge cab e do VC Runtime necessários para a instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você poderá instalar IoT Edge sem uma conexão com a Internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **Reiniciar ifNeeded** | nenhuma | Esse sinalizador permite que o script de implantação reinicie o computador sem avisar, se necessário. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Força** | nenhuma | Esse sinalizador força a desinstalação caso a tentativa anterior de desinstalar não tenha sido bem-sucedida.
| **Reiniciar ifNeeded** | nenhuma | Esse sinalizador permite que o script de desinstalação reinicie o computador sem avisar, se necessário. |

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge aprovisionado com o tempo de funcionar instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver com problemas em instalar o IoT Edge corretamente, consulte a página de resolução de [problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o daemon de segurança IoT Edge e o tempo](how-to-update-iot-edge.md)de execução .

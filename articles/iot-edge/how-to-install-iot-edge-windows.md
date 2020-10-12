---
title: Instale a borda Azure IoT no Windows / Microsoft Docs
description: Instruções de instalação do Azure IoT Edge no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: ba3e8b9d7649d56d1639f7f608d85a2da04ff74a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465563"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o runtime do Azure IoT Edge no Windows

O tempo de execução Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de funcionaamento pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grande como um servidor industrial. Quando um dispositivo é configurado com o runtime do IoT Edge, pode começar a implementar a lógica de negócio no mesmo partir da cloud.

Para saber mais sobre o tempo de execução do IoT Edge, consulte [o tempo de execução do Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para instalar o tempo de funcionamento do Azure IoT Edge no seu sistema Windows x64 (AMD/Intel) utilizando recipientes Windows.

> [!NOTE]
> Um problema conhecido do sistema operativo Windows impede a transição para o sono e hiberna os estados de potência quando os módulos IoT Edge (recipientes Windows Nano Server isolados) estão em funcionamento. Este problema afeta a vida útil da bateria no dispositivo.
>
> Como solução alternativa, utilize o comando `Stop-Service iotedge` para parar qualquer módulo IoT Edge em funcionamento antes de utilizar estes estados de alimentação.

A utilização de recipientes Linux em sistemas Windows não é uma configuração de produção recomendada ou suportada para a Azure IoT Edge. No entanto, pode ser utilizado para fins de desenvolvimento e teste. Para saber mais, consulte [use IoT Edge no Windows para executar recipientes Linux](how-to-install-iot-edge-windows-with-linux.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Pré-requisitos

Utilize esta secção para analisar se o seu dispositivo Windows pode suportar o IoT Edge e prepará-lo para um motor de contentores antes da instalação.

### <a name="supported-windows-versions"></a>Versões suportadas pelo Windows

O IoT Edge para windows requer a versão 1809/build 17763 do Windows, que é a mais recente [construção de suporte](https://docs.microsoft.com/windows/release-information/)a longo prazo do Windows. Para o suporte do Windows SKU, veja o que é suportado com base no facto de estar a preparar-se para cenários de produção ou cenários de desenvolvimento e teste:

* **Produção**: Para obter informações mais recentes sobre quais os sistemas operativos atualmente suportados para cenários de produção, consulte [os sistemas suportados Azure IoT Edge](support.md#operating-systems).
* **Desenvolvimento e teste**: Para cenários de desenvolvimento e teste, o Azure IoT Edge com recipientes Windows pode ser instalado em qualquer SKU (Pro, Enterprise, Server, etc.) do Windows build 17763 que suporta a funcionalidade de contentores.

Os dispositivos IoT Core devem incluir a funcionalidade opcional IoT Core Windows Containers para suportar o tempo de funcionaamento do IoT Edge. Utilize o seguinte comando numa [sessão remota do PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) para verificar se os recipientes windows são suportados no seu dispositivo:

```powershell
Get-Service vmcompute
```

Se o serviço estiver presente, deverá obter uma resposta bem sucedida com o estado de serviço listado como **em execução**. Se o `vmcompute` serviço não for encontrado, o seu dispositivo não satisfaz os requisitos para ioT Edge. Contacte o seu fornecedor de hardware para perguntar sobre o suporte a esta funcionalidade.

### <a name="prepare-for-a-container-engine"></a>Preparar para um motor de contentor

O Azure IoT Edge conta com um motor de contentor [compatível com OCI.](https://www.opencontainers.org/) Para cenários de produção, utilize o motor Moby incluído no script de instalação para executar recipientes Windows no seu dispositivo Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Instale ioT Edge num novo dispositivo

>[!NOTE]
>Os pacotes de software Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Por favor leia os termos da licença antes de usar o pacote. A sua instalação e utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize a embalagem.

Um script PowerShell descarrega e instala o daemon de segurança Azure IoT Edge. O daemon de segurança inicia então o primeiro de dois módulos de tempo de execução, o agente IoT Edge, que permite a implementação remota de outros módulos.

>[!TIP]
>Para dispositivos IoT Core, recomendamos que os comandos de instalação utilizem uma sessão RemotePowerShell. Para obter mais informações, consulte [utilizar o PowerShell para windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Quando instalar o tempo de funcionaamento do IoT Edge pela primeira vez num dispositivo, é necessário doar uma identidade do dispositivo a partir de um hub IoT. Um único dispositivo IoT Edge pode ser fornecido manualmente utilizando uma cadeia de ligação do dispositivo fornecida pelo IoT Hub. Ou, pode utilizar o Serviço de Provisionamento de Dispositivos (DPS) para provisões automáticas, o que é útil quando tem muitos dispositivos para configurar. Dependendo da sua escolha de provisionamento, escolha o roteiro de instalação apropriado.

As secções seguintes descrevem os casos e parâmetros de utilização comuns para o script de instalação IoT Edge num novo dispositivo.

### <a name="option-1-install-and-manually-provision"></a>Opção 1: Instalar e providenciar manualmente

Nesta primeira opção, fornece-se uma **cadeia de ligação** do dispositivo gerada pelo IoT Hub para fornecer o dispositivo.

Este exemplo demonstra uma instalação manual com recipientes Windows:

1. Se ainda não o fez, registe um novo dispositivo IoT Edge e recupere a **cadeia de ligação**do dispositivo . Copie o fio de ligação para utilizar mais tarde nesta secção. Pode completar este passo utilizando as seguintes ferramentas:

   * [Portal do Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [CLI do Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Execute o PowerShell como um administrador.

   >[!NOTE]
   >Utilize uma sessão AMD64 de PowerShell para instalar ioT Edge, não PowerShell (x86). Se não tiver a certeza de que tipo de sessão está a utilizar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se a sua máquina Do Windows está numa versão suportada, liga a funcionalidade de contentores e, em seguida, descarrega o tempo de execução moby e o tempo de execução IoT Edge. O comando não permite a utilização de recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server podem pedir-lhe para reiniciar. Em caso afirmativo, reinicie já o seu dispositivo. Assim que o seu dispositivo estiver pronto, volte a executar o PowerShell como administrador.

5. O comando **Initialize-IoTEdge** configura o tempo de funcionamento do IoT Edge na sua máquina. O comando não tem disposição manual com recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando solicitado, forneça a cadeia de ligação do dispositivo que recuperou no passo 1. A cadeia de ligação do dispositivo associa o dispositivo físico a um ID do dispositivo no IoT Hub.

   A cadeia de ligação do dispositivo assume o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Utilize os passos na [instalação de sucesso](#verify-successful-installation) para verificar o estado do IoT Edge no seu dispositivo.

Quando instalar e providenciar um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar a instalação, incluindo:

* Tráfego direto para passar por um servidor proxy
* Aponte o instalador para um diretório offline
* Declare uma imagem específica do contentor do agente, e forneça credenciais se estiver em um registo privado

Para obter mais informações sobre estas opções de instalação, avance para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: Instalar e provisão automática

Nesta segunda opção, você disponibiliza o dispositivo utilizando o Serviço de Provisionamento de Dispositivos IoT Hub. Fornecer o **ID** de âmbito a partir de uma instância do Serviço de Provisionamento de Dispositivos, juntamente com qualquer outra informação específica do seu mecanismo de [atestado](../iot-dps/concepts-security.md#attestation-mechanism)preferido:

* [Criar e providenciar um dispositivo IoT Edge simulado com um TPM virtual no Windows](how-to-auto-provision-simulated-device-windows.md)
* [Criar e providenciar um dispositivo IoT Edge simulado utilizando certificados X.509](how-to-auto-provision-x509-certs.md)
* [Criar e providenciar um dispositivo IoT Edge utilizando atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Quando instalar e providenciar um dispositivo automaticamente, pode utilizar parâmetros adicionais para modificar a instalação, incluindo:

* Tráfego direto para passar por um servidor proxy
* Aponte o instalador para um diretório offline
* Declare uma imagem específica do contentor do agente, e forneça credenciais se estiver em um registo privado

Para obter mais informações sobre estas opções de instalação, continue a ler este artigo ou salte para saber [sobre todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Instalação de versão offline ou específica

Durante a instalação, três ficheiros são descarregados:

* Um script PowerShell, que contém as instruções de instalação
* Cabine Microsoft Azure IoT Edge, que contém o daemon de segurança IoT Edge (iotedged), motor de contentores Moby, e Moby CLI
* Instalador de pacotes redistribuíveis Visual C++ (tempo de execução VC)

Se o seu dispositivo estiver offline durante a instalação ou se pretender instalar uma versão específica do IoT Edge, pode descarregar estes ficheiros antes do tempo para o dispositivo. Quando for a hora de instalar, aponte o script de instalação para o diretório que contém os ficheiros descarregados. O instalador verifica primeiro o diretório e, em seguida, apenas descarrega componentes que não são encontrados. Se todos os ficheiros estiverem disponíveis offline, pode instalar-se sem ligação à Internet.

Também pode utilizar o parâmetro do caminho de instalação offline para atualizar o IoT Edge. Para obter mais informações, consulte [atualizar o daemon de segurança IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

1. Para obter os mais recentes ficheiros de instalação IoT Edge juntamente com versões anteriores, consulte as [versões Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Encontre a versão que pretende instalar e descarregue os seguintes ficheiros da secção **Ativos** das notas de lançamento para o seu dispositivo IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab dos lançamentos 1.0.9 ou mais recentes, ou Microsoft-Azure-IoTEdge.cab dos lançamentos 1.0.8 ou mais antigos.

   Microsoft-Azure-IotEdge-arm32.cab também está disponível a partir de 1.0.9 apenas para fins de teste. O IoT Edge não é suportado atualmente em dispositivos Windows ARM32.

   É importante utilizar o script PowerShell a partir da mesma versão que o ficheiro .cabin que utiliza porque a funcionalidade muda para suportar as funcionalidades em cada versão.

3. Se o ficheiro .cab que descarregou tiver um sufixo de arquitetura, mude o nome do ficheiro para apenas **Microsoft-Azure-IoTEdge.cab**.

4. Opcionalmente, faça o download de um instalador para visual C++ redistribuível. Por exemplo, o script PowerShell utiliza esta versão: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Guarde o instalador na mesma pasta do seu dispositivo IoT que os ficheiros IoT Edge.

5. Para instalar com componentes offline, [dot origem](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) a cópia local do script PowerShell. Em seguida, use o `-OfflineInstallationPath` parâmetro como parte do comando e forneça o caminho absoluto para o `Deploy-IoTEdge` diretório de ficheiros. Por exemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   O comando de implantação utilizará todos os componentes encontrados no diretório de ficheiros local fornecido. Se faltar o ficheiro .cabin ou o instalador Visual C++, tentará descarregá-los.

6. Executar o `Initialize-IoTEdge` comando para a provisionar o seu dispositivo com uma identidade no Hub IoT. Ou fornece uma cadeia de ligação do dispositivo para o provisionamento manual, ou escolha um dos métodos descritos na secção [de fornecimento automática](#option-2-install-and-automatically-provision) anterior.

   Se o seu dispositivo reiniciar após a `Deploy-IoTEdge` execução, o ponto de origem do script PowerShell novamente antes de ser recorrido `Initialize-IoTEdge` .

Para obter mais informações sobre a opção de instalação offline, avance para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="verify-successful-installation"></a>Verifique a instalação bem sucedida

Verifique o estado do serviço IoT Edge. Deve estar listado como em execução.  

```powershell
Get-Service iotedge
```

Examine os registos de serviço dos últimos 5 minutos. Se acabou de instalar o tempo de execução IoT Edge, poderá ver uma lista de erros do tempo entre a execução **de Deploy-IoTEdge** e **Initialize-IoTEdge**. Estes erros são esperados, uma vez que o serviço está a tentar começar antes de ser configurado.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Executar a [ferramenta de resolução de problemas](troubleshoot.md#run-the-check-command) para verificar se há erros de configuração e rede mais comuns.

```powershell
iotedge check
```

Até que implante o seu primeiro módulo para IoT Edge no seu dispositivo, o módulo de sistema **$edgeHub** não será implantado no dispositivo. Como resultado, a verificação automatizada devolverá um erro para a `Edge Hub can bind to ports on host` verificação de conectividade. Este erro pode ser ignorado a menos que ocorra depois de ter implantado um módulo no dispositivo.

Finalmente, listar os módulos de execução:

```powershell
iotedge list
```

Após uma nova instalação, o único módulo que deve ver em execução é **edgeAgent**. Depois de implementar os [módulos IoT Edge](how-to-deploy-modules-portal.md) pela primeira vez, o outro módulo do sistema, **o EdgeHub,** também irá arrancar no dispositivo.

## <a name="manage-module-containers"></a>Gerir os recipientes dos módulos

O serviço IoT Edge requer um motor de contentores a funcionar no seu dispositivo. Quando se coloca um módulo num dispositivo, o tempo de funcionaamento do IoT Edge utiliza o motor do contentor para retirar a imagem do contentor de um registo na nuvem. O serviço IoT Edge permite interagir com os seus módulos e recuperar registos, mas por vezes pode querer utilizar o motor do recipiente para interagir com o próprio recipiente.

Para obter mais informações sobre os conceitos de módulos, consulte [os módulos Understand Azure IoT Edge](iot-edge-modules.md).

Se estiver a executar contentores Windows no seu dispositivo Windows IoT Edge, então a instalação IoT Edge incluiu o motor de contentores Moby. O motor Moby baseava-se nos mesmos padrões que o Docker, e foi projetado para funcionar em paralelo na mesma máquina que o Docker Desktop. Por essa razão, se queres atingir contentores geridos pelo motor Moby, tens de apontar especificamente esse motor em vez do Docker.

Por exemplo, para listar todas as imagens do Docker, utilize o seguinte comando:

```powershell
docker images
```

Para listar todas as imagens moby, modifique o mesmo comando com um ponteiro para o motor Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

O motor URI está listado na saída do script de instalação, ou pode encontrá-lo na secção de definições de tempo de funcionamento do contentor para o ficheiro config.yaml.

![moby_runtime uri em config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Para obter mais informações sobre comandos que pode utilizar para interagir com contentores e imagens em execução no seu dispositivo, consulte [as interfaces da linha de comando do Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Desinstalar ioT Edge

Se pretender remover a instalação IoT Edge do seu dispositivo Windows, utilize o seguinte comando a partir de uma janela administrativa do PowerShell. Este comando remove o tempo de funcionamento do IoT Edge, juntamente com a configuração existente e os dados do motor Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

O comando Uninstall-IoTEdge não funciona no Windows IoT Core. Para remover o IoT Edge dos dispositivos Windows IoT Core, é necessário recolocar a sua imagem Windows IoT Core.

Para obter mais informações sobre opções de desinstalação, utilize o comando `Get-Help Uninstall-IoTEdge -full` .

## <a name="verify-installation-script"></a>Verifique o script de instalação

Os comandos de instalação fornecidos neste artigo utilizam o Invoke-WebRequest cmdlet para solicitar o script de instalação de `aka.ms/iotedge-win` . Esta ligação aponta para o `IoTEdgeSecurityDaemon.ps1` script da mais recente versão [IoT Edge](https://github.com/Azure/azure-iotedge/releases). Também pode descarregar este script, ou uma versão do script a partir de uma versão específica, para executar os comandos de instalação no seu dispositivo IoT Edge.

O guião fornecido é assinado para aumentar a segurança. Pode verificar a assinatura descarregando o script para o seu dispositivo e executando o seguinte comando PowerShell:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

O estado de saída é **válido** se a assinatura for verificada.

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As secções anteriores introduziram cenários de instalação comuns com exemplos de como usar parâmetros para modificar o script de instalação. Esta secção fornece tabelas de referência dos parâmetros comuns utilizados para instalar, atualizar ou desinstalar o IoT Edge.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

O comando Deploy-IoTEdge descarrega e implementa o IoT Edge Security Daemon e as suas dependências. O comando de implantação aceita estes parâmetros comuns, entre outros. Para a lista completa, utilize o comando `Get-Help Deploy-IoTEdge -full` .  

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Contentores** | **Janelas** ou **Linux** | Se não for especificado nenhum sistema operativo de contentores, o Windows é o valor predefinido.<br><br>Para recipientes Windows, o IoT Edge utiliza o motor de contentores moby incluído na instalação. Para os recipientes Linux, é necessário instalar um motor de contentores antes de iniciar a instalação. |
| **Proxy** | URL de procuração | Inclua este parâmetro se o seu dispositivo precisar de passar por um servidor proxy para chegar à internet. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se este parâmetro for incluído, o instalador verificará o diretório listado para os ficheiros MSI de cabaça IoT Edge e VC runtime necessários para a instalação. Todos os ficheiros não encontrados no diretório são descarregados. Se ambos os ficheiros estiverem no diretório, pode instalar o IoT Edge sem uma ligação à Internet. Também pode utilizar este parâmetro para utilizar uma versão específica. |
| **Invocar WebRequestParameters** | Haxixe de parâmetros e valores | Durante a instalação, são feitos vários pedidos web. Utilize este campo para definir parâmetros para esses pedidos web. Este parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeed** | nenhuma | Esta bandeira permite que o script de implantação reinicie a máquina sem aviso, se necessário. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

O comando Initialize-IoTEdge configura o IoT Edge com a cadeia de ligação do dispositivo e detalhes operacionais. Grande parte da informação gerada por este comando é então armazenada no ficheiro iotedge\config.yaml. O comando de inicialização aceita estes parâmetros comuns, entre outros. Para a lista completa, utilize o comando `Get-Help Initialize-IoTEdge -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhum | **Parâmetro de comutação**. Se não for especificado nenhum tipo de provisionamento, o manual é o valor predefinido.<br><br>Declara que irá fornecer uma cadeia de ligação do dispositivo para fornecer o dispositivo manualmente |
| **DPS** | Nenhum | **Parâmetro de comutação**. Se não for especificado nenhum tipo de provisionamento, o manual é o valor predefinido.<br><br>Declara que irá fornecer um ID de âmbito do Serviço de Provisionamento de Dispositivos (DPS) e o ID de Registo do seu dispositivo através de DPS.  |
| **DeviceConnectionString** | Uma cadeia de ligação de um dispositivo IoT Edge registado num Hub IoT, em cotações únicas | **Requerido** para o provisionamento manual. Se não fornecer uma cadeia de ligação nos parâmetros do script, será solicitado um. |
| **ScopeId** | Uma identificação de âmbito a partir de uma instância do Serviço de Provisionamento de Dispositivos associado ao seu Hub IoT. | **Requerido** para o provisionamento de DPS. Se não fornecer uma identificação de âmbito nos parâmetros do script, será solicitado para um. |
| **RegistroD** | Um ID de registo gerado pelo seu dispositivo | **Requerido** provisões de DPS se utilizar atestado de teclas TPM ou simétricas. **Opcional** se utilizar o certificado X.509. |
| **X509IdentityCertificate** | O caminho URI para o certificado de identidade do dispositivo X.509 no dispositivo. | **Requerido** para o provisionamento de DPS se utilizar o atestado de certificado X.509. |
| **X509IdentityPrivateKey** | O caminho URI para a chave de certificado de identidade do dispositivo X.509 no dispositivo. | **Requerido** para o provisionamento de DPS se utilizar o atestado de certificado X.509. |
| **Chave Simétrica** | A chave simétrica utilizada para a disponibilização da identidade do dispositivo IoT Edge ao utilizar dPS | **Requerido** provisão de DPS se utilizar o atestado de chave simétrica. |
| **Contentores** | **Janelas** ou **Linux** | Se não for especificado nenhum sistema operativo de contentores, o Windows é o valor predefinido.<br><br>Para recipientes Windows, o IoT Edge utiliza o motor de contentores moby incluído na instalação. Para os recipientes Linux, é necessário instalar um motor de contentores antes de iniciar a instalação. |
| **Invocar WebRequestParameters** | Haxixe de parâmetros e valores | Durante a instalação, são feitos vários pedidos web. Utilize este campo para definir parâmetros para esses pedidos web. Este parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Imagem de Agente** | IoT Edge imagem agente URI | Por predefinição, uma nova instalação IoT Edge utiliza a mais recente etiqueta rolante para a imagem do agente IoT Edge. Utilize este parâmetro para definir uma etiqueta específica para a versão de imagem, ou para fornecer a sua própria imagem de agente. Para obter mais informações, consulte [as etiquetas IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de Utilizador** | Nome de utilizador do registo de contentores | Utilize este parâmetro apenas se definir o parâmetro -AgentImage num contentor num registo privado. Fornecer um nome de utilizador com acesso ao registo. |
| **Palavra-passe** | Cadeia de senha segura | Utilize este parâmetro apenas se definir o parâmetro -AgentImage num contentor num registo privado. Forneça a senha para aceder ao registo. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Contentores** | **Janelas** ou **Linux** | Se não for especificado nenhum sistema operativo de contentores, o Windows é o valor predefinido. Para os recipientes windows, um motor de contentores será incluído na instalação. Para os recipientes Linux, é necessário instalar um motor de contentores antes de iniciar a instalação. |
| **Proxy** | URL de procuração | Inclua este parâmetro se o seu dispositivo precisar de passar por um servidor proxy para chegar à internet. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Invocar WebRequestParameters** | Haxixe de parâmetros e valores | Durante a instalação, são feitos vários pedidos web. Utilize este campo para definir parâmetros para esses pedidos web. Este parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se este parâmetro for incluído, o instalador verificará o diretório listado para os ficheiros MSI de cabaça IoT Edge e VC runtime necessários para a instalação. Todos os ficheiros não encontrados no diretório são descarregados. Se ambos os ficheiros estiverem no diretório, pode instalar o IoT Edge sem uma ligação à Internet. Também pode utilizar este parâmetro para utilizar uma versão específica. |
| **RestartIfNeed** | nenhuma | Esta bandeira permite que o script de implantação reinicie a máquina sem aviso, se necessário. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Força** | nenhuma | Esta bandeira força a desinstalação no caso de a tentativa anterior de desinstalar não ter sido bem sucedida.
| **RestartIfNeed** | nenhuma | Esta bandeira permite que o script de desinstalar reinicie a máquina sem aviso, se necessário. |

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge alojotado com o tempo de funcionaamento instalado, pode [implantar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se tiver problemas em instalar o IoT Edge corretamente, consulte a página [de resolução de problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

---
title: Instale a borda Azure IoT para | windows Microsoft Docs
description: Instale a borda IoT Azure para recipientes Windows em dispositivos Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
ms.openlocfilehash: 7857f93e8c767f270041bb6bf041447786ce19ff
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633995"
---
# <a name="install-and-manage-azure-iot-edge-for-windows"></a>Instale e gere a borda Azure IoT para windows

O Azure IoT Edge para Windows funciona diretamente no seu dispositivo Windows anfitrião e utiliza contentores Windows para executar lógica de negócio no limite.

O tempo de execução Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de funcionaamento pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grande como um servidor industrial. Quando um dispositivo é configurado com o runtime do IoT Edge, pode começar a implementar a lógica de negócio no mesmo partir da cloud. Para saber mais, consulte [o tempo de execução Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

>[!NOTE]
>O Azure IoT Edge para Windows não será suportado a partir da versão 1.2.0 do Azure IoT Edge.
>
>Considere utilizar o novo método para executar ioT Edge em dispositivos Windows, Azure IoT Edge para Linux no Windows.

<!-- TODO: link to EFLOW-->

Existem dois passos para a criação de um dispositivo IoT Edge. O primeiro passo é instalar o tempo de funcionação e as suas dependências. O segundo passo é ligar o dispositivo à sua identidade na nuvem e configurar a autenticação com o IoT Hub.

Este artigo lista os passos para instalar o tempo de funcionamento do Azure IoT Edge em dispositivos Windows. Quando instalar o tempo de funcionação, tem a opção de utilizar recipientes Linux ou blocos. Atualmente, apenas os contentores windows no Windows são suportados para cenários de produção. Os contentores Linux no Windows são úteis para cenários de desenvolvimento e teste, especialmente se estiver a desenvolver num PC windows para se implantar em dispositivos Linux.

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo Windows

  IoT Edge com recipientes Windows requer a versão 1809/build 17762 do Windows, que é a mais recente [construção de suporte](/windows/release-information/)a longo prazo do Windows. Para cenários de desenvolvimento e teste, qualquer SKU (Pro, Enterprise, Server, etc.) que suporte a funcionalidade de contentores funcionará. No entanto, certifique-se de rever a lista de [sistemas suportados](support.md#operating-systems) antes de ir para a produção.

  IoT Edge com recipientes Linux pode funcionar em qualquer versão do Windows que satisfaça os [requisitos para o Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

* Suporte de contentores no dispositivo

  O Azure IoT Edge conta com um motor de contentor [compatível com OCI.](https://www.opencontainers.org/) Certifique-se de que o seu dispositivo pode suportar recipientes.

  Se estiver a instalar o IoT Edge numa máquina virtual, ative a virtualização aninhada e aloque pelo menos 2 GB de memória. Para o Hyper-V, as máquinas virtuais de geração 2 têm uma virtualização aninhada ativada por padrão. Para o VMware, existe um alternância para ativar a funcionalidade na sua máquina virtual.

  Se estiver a instalar o IoT Edge num dispositivo IoT Core, utilize o seguinte comando numa [sessão remota do PowerShell](/windows/iot-core/connect-your-device/powershell) para verificar se os recipientes do Windows são suportados no seu dispositivo:

  ```powershell
  Get-Service vmcompute
  ```

* Um [ID de dispositivo registado](how-to-register-device.md)

  Se registou o seu dispositivo com autenticação de chave simétrica, tenha a cadeia de ligação do dispositivo pronta.

  Se registou o seu dispositivo com a autenticação de certificado auto-assinado X.509, tenha pelo menos um dos certificados de identidade que usou para registar o dispositivo e a sua chave privada correspondente disponível no seu dispositivo.

## <a name="install-a-container-engine"></a>Instale um motor de contentores

O Azure IoT Edge conta com um tempo de funcionamento do recipiente compatível com OCI. Para cenários de produção, recomendamos que use o motor baseado em Moby. O motor Moby é o único motor de contentores oficialmente suportado com Azure IoT Edge. As imagens do contentor Estivador CE/EE são compatíveis com o tempo de execução do Moby.

Para cenários de produção, utilize o motor baseado em Moby que está incluído no script de instalação. Não há passos adicionais para instalar o motor.

Para ioT Edge com recipientes Linux, você precisa fornecer o seu próprio tempo de funcionação do recipiente. Instale [o Docker Desktop](https://docs.docker.com/docker-for-windows/install/) no seu dispositivo e configuure-o para utilizar [recipientes Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) antes de continuar.

## <a name="install-the-iot-edge-security-daemon"></a>Instale o daemon de segurança IoT Edge

>[!TIP]
>Para dispositivos IoT Core, recomendamos que os comandos de instalação utilizem uma sessão remota do PowerShell. Para obter mais informações, consulte [utilizar o PowerShell para windows IoT](/windows/iot-core/connect-your-device/powershell).

1. Execute o PowerShell como um administrador.

   Utilize uma sessão AMD64 de PowerShell, não PowerShell (x86). Se não tiver a certeza de que tipo de sessão está a utilizar, execute o seguinte comando:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Execute o comando [Deploy-IoTEdge,](reference-windows-scripts.md#deploy-iotedge) que executa as seguintes tarefas:

   * Verifique se a sua máquina do Windows se encontra numa versão suportada.
   * Liga a característica dos recipientes.
   * Descarrega o motor moby e o tempo de execução IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   O `Deploy-IoTEdge` comando não permite a utilização de recipientes Windows. Se quiser utilizar recipientes Linux, adicione o `ContainerOs` parâmetro:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Os dispositivos Windows 10 ou Windows Server podem levar-lhe a reiniciar. Em caso afirmativo, reinicie já o seu dispositivo.

Quando instalar o IoT Edge num dispositivo, pode utilizar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Aponte o instalador para um diretório local para instalação offline.

Para obter mais informações sobre estes parâmetros adicionais, consulte [os scripts PowerShell para IoT Edge no Windows](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Fornecimento do dispositivo com a sua identidade em nuvem

Agora que o motor de contentores e o tempo de funcionaamento do IoT Edge estão instalados no seu dispositivo, está pronto para o próximo passo, que é configurar o dispositivo com a sua identidade em nuvem e informações de autenticação.

Escolha a próxima secção com base no tipo de autenticação que pretende utilizar:

* [Opção 1: Autenticar com teclas simétricas](#option-1-authenticate-with-symmetric-keys)
* [Opção 2: Autenticar com certificados X.509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opção 1: Autenticar com teclas simétricas

Neste momento, o tempo de execução IoT Edge encontra-se instalado no seu dispositivo Windows, sendo necessário dotar o dispositivo com a sua identidade em nuvem e informações de autenticação.

Esta secção percorre os passos para providenciar um dispositivo com autenticação de chave simétrica. Devia ter registado o seu dispositivo no IoT Hub e recuperado a cadeia de ligação a partir das informações do dispositivo. Caso contrário, siga os passos no [Registo de um dispositivo IoT Edge no IoT Hub](how-to-register-device.md).

1. No dispositivo IoT Edge, executar o PowerShell como administrador.

2. Utilize o comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar o tempo de funcionamento do IoT Edge na sua máquina. O comando não tem disposição manual com recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Se estiver a utilizar recipientes Linux, adicione o `-ContainerOs` parâmetro à bandeira. Seja consistente com a opção de recipiente que escolheu com o `Deploy-IoTEdge` comando que executou anteriormente.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Se descarregou o script IoTEdgeSecurityDaemon.ps1 no seu dispositivo para instalação de versão offline ou específica, certifique-se de fazer referência à cópia local do script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando solicitado, forneça a cadeia de ligação do dispositivo que recuperou na secção anterior. A cadeia de ligação do dispositivo associa o dispositivo físico a um ID do dispositivo no IoT Hub e fornece informações de autenticação.

   A cadeia de ligação do dispositivo assume o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Quando fornece um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Declare uma imagem específica do recipiente edgeAgent, e forneça credenciais se estiver em um registo privado

Para obter mais informações sobre estes parâmetros adicionais, consulte [os scripts PowerShell para IoT Edge no Windows](reference-windows-scripts.md).

### <a name="option-2-authenticate-with-x509-certificates"></a>Opção 2: Autenticar com certificados X.509

Neste momento, o tempo de execução IoT Edge encontra-se instalado no seu dispositivo Windows, sendo necessário dotar o dispositivo com a sua identidade em nuvem e informações de autenticação.

Esta secção percorre os passos para providenciar um dispositivo com autenticação de certificado X.509. Deverá ter registado o seu dispositivo no IoT Hub, fornecendo impressões digitais que correspondam ao certificado e à chave privada localizado no seu dispositivo IoT Edge. Caso contrário, siga os passos no [Registo de um dispositivo IoT Edge no IoT Hub](how-to-register-device.md).

1. No dispositivo IoT Edge, executar o PowerShell como administrador.

2. Utilize o comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar o tempo de funcionamento do IoT Edge na sua máquina.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Se estiver a utilizar recipientes Linux, adicione o `-ContainerOs` parâmetro à bandeira. Seja consistente com a opção de recipiente que escolheu com o `Deploy-IoTEdge` comando que executou anteriormente.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Se descarregou o script IoTEdgeSecurityDaemon.ps1 no seu dispositivo para instalação de versão offline ou específica, certifique-se de fazer referência à cópia local do script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando lhe for pedido, forneça as seguintes informações:

   * **IotHubHostName**: Nome de anfitrião do hub IoT a que o dispositivo se ligará. Por exemplo, `{IoT hub name}.azure-devices.net`.
   * **DeviceId**: O ID que forneceu quando registou o dispositivo.
   * **X509IdentityCertificate**: Caminho absoluto para um certificado de identidade no dispositivo. Por exemplo, `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: Caminho absoluto para o ficheiro chave privado para o certificado de identidade fornecido. Por exemplo, `C:\path\identity_key.pem`.

Quando fornece um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Declare uma imagem específica do recipiente edgeAgent, e forneça credenciais se estiver em um registo privado

Para obter mais informações sobre estes parâmetros adicionais, consulte [os scripts PowerShell para IoT Edge no Windows](reference-windows-scripts.md).

## <a name="offline-or-specific-version-installation-optional"></a>Instalação de versão offline ou específica (opcional)

Os passos nesta secção são para cenários não abrangidos pelas etapas de instalação padrão. Isto pode incluir:

* Instale ioT Edge enquanto offline
* Instale uma versão de candidato de lançamento
* Instale uma versão diferente da mais recente

Durante a instalação, três ficheiros são descarregados:

* Um script PowerShell, que contém as instruções de instalação
* Cabine Microsoft Azure IoT Edge, que contém o daemon de segurança IoT Edge (iotedged), motor de contentores Moby, e Moby CLI
* Instalador de pacotes redistribuíveis Visual C++ (tempo de execução VC)

Se o seu dispositivo estiver offline durante a instalação ou se pretender instalar uma versão específica do IoT Edge, pode descarregar estes ficheiros antes do tempo para o dispositivo. Quando for a hora de instalar, aponte o script de instalação para o diretório que contém os ficheiros descarregados. O instalador verifica primeiro o diretório e, em seguida, apenas descarrega componentes que não são encontrados. Se todos os ficheiros estiverem disponíveis offline, pode instalar-se sem ligação à Internet.

1. Para obter os mais recentes ficheiros de instalação IoT Edge juntamente com versões anteriores, consulte as [versões Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Encontre a versão que pretende instalar e descarregue os seguintes ficheiros da secção **Ativos** das notas de lançamento para o seu dispositivo IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab dos lançamentos 1.0.9 ou mais recentes, ou Microsoft-Azure-IoTEdge.cab dos lançamentos 1.0.8 ou mais antigos.

   Microsoft-Azure-IotEdge-arm32.cab também está disponível a partir de 1.0.9 apenas para fins de teste. O IoT Edge não é suportado atualmente em dispositivos Windows ARM32.

   É importante utilizar o script PowerShell a partir da mesma versão que o ficheiro .cab que utiliza porque a funcionalidade muda para suportar as funcionalidades em cada versão.

3. Se o ficheiro .cab que descarregou tiver um sufixo de arquitetura, mude o nome do ficheiro para apenas **Microsoft-Azure-IoTEdge.cab**.

4. Opcionalmente, faça o download de um instalador para visual C++ redistribuível. Por exemplo, o script PowerShell utiliza esta versão: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Guarde o instalador na mesma pasta do seu dispositivo IoT que os ficheiros IoT Edge.

5. Para instalar com componentes offline, [dot origem](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) a cópia local do script PowerShell.

6. Executar o comando [Implementar-IoTEdge](reference-windows-scripts.md#deploy-iotedge) com o `-OfflineInstallationPath` parâmetro. Fornecer o caminho absoluto para o diretório de arquivos. Por exemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   O comando de implantação utilizará todos os componentes encontrados no diretório de ficheiros local fornecido. Se o ficheiro .cab ou o instalador Visual C++ estiver em falta, tentará descarregá-los.

## <a name="update-iot-edge"></a>Atualização IoT Edge

Use o `Update-IoTEdge` comando para atualizar o daemon de segurança. O script puxa automaticamente a versão mais recente do daemon de segurança.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Executar o comando Update-IoTEdge remove e atualiza o daemon de segurança do seu dispositivo, juntamente com as duas imagens do recipiente de tempo de execução. O ficheiro config.yaml é mantido no dispositivo, bem como dados do motor de contentores Moby (se estiver a utilizar recipientes Windows). Manter as informações de configuração significa que não tem de fornecer novamente informações sobre a cadeia de ligação ou serviço de fornecimento de dispositivos para o seu dispositivo durante o processo de atualização.

Se pretender atualizar para uma versão específica do daemon de segurança, encontre a versão que pretende direcionar a partir das [versões IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, descarregue o ficheiro **Microsoft-Azure-IoTEdge.cab.** Em seguida, use o `-OfflineInstallationPath` parâmetro para apontar para a localização do ficheiro local. Por exemplo:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>O `-OfflineInstallationPath` parâmetro procura um ficheiro chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. A partir da versão IoT Edge 1.0.9-rc4, existem dois ficheiros .cab disponíveis para utilização, um para dispositivos AMD64 e outro para ARM32. Faça o download do ficheiro correto para o seu dispositivo e, em seguida, mude o nome do ficheiro para remover o sufixo de arquitetura.

Se pretender atualizar um dispositivo offline, encontre a versão que pretende direcionar a partir das [versões Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, descarregue os ficheiros *IoTEdgeSecurityDaemon.ps1* e *Microsoft-Azure-IoTEdge.cab.* É importante utilizar o script PowerShell a partir da mesma versão que o ficheiro .cab que utiliza porque a funcionalidade muda para suportar as funcionalidades em cada versão.

Se o ficheiro .cab que descarregou tiver um sufixo de arquitetura, mude o nome do ficheiro para apenas **Microsoft-Azure-IoTEdge.cab**.

Para atualizar com componentes offline, [ponto fonte](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) da cópia local do script PowerShell. Em seguida, use o `-OfflineInstallationPath` parâmetro como parte do comando e forneça o caminho absoluto para o `Update-IoTEdge` diretório de ficheiros. Por exemplo,

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Para obter mais informações sobre opções de atualização, utilize o comando ou consulte o `Get-Help Update-IoTEdge -full` [script PowerShell para IoT Edge no Windows](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>Desinstalar ioT Edge

Se pretender remover a instalação IoT Edge do seu dispositivo, utilize os seguintes comandos.

Se pretender remover a instalação IoT Edge do seu dispositivo Windows, utilize o comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) a partir de uma janela administrativa powerShell. Este comando remove o tempo de funcionamento do IoT Edge, juntamente com a configuração existente e os dados do motor Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

O `Uninstall-IoTEdge` comando não funciona no Windows IoT Core. Para remover o IoT Edge, é necessário recolocar a sua imagem Do Windows IoT Core.

Para obter mais informações sobre opções de desinstalação, utilize o comando `Get-Help Uninstall-IoTEdge -full` .

## <a name="next-steps"></a>Passos seguintes

Continue a [implementar módulos IoT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos no seu dispositivo.

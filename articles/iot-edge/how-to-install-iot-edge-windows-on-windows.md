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
ms.openlocfilehash: c24389a1957f9e0cfb23e3bb5b8604c34e57a915
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609520"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Instale e gere a borda Azure IoT com recipientes Windows

O tempo de execução Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. Quando um dispositivo é configurado com o runtime do IoT Edge, pode começar a implementar a lógica de negócio no mesmo partir da cloud. Para saber mais, consulte [o tempo de execução Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Existem dois passos para a criação de um dispositivo IoT Edge. O primeiro passo é instalar o tempo de funcionação e as suas dependências. O segundo passo é ligar o dispositivo à sua identidade na nuvem e configurar a autenticação com o IoT Hub.

Este artigo lista os passos para instalar o tempo de execução Azure IoT Edge com recipientes Windows. Se procura utilizar recipientes Linux num dispositivo Windows, consulte o [Azure IoT Edge para Linux no](how-to-install-iot-edge-on-windows.md) artigo do Windows.

>[!NOTE]
>O Azure IoT Edge com recipientes Windows não será suportado a partir da versão 1.2 do Azure IoT Edge.
>
>Considere utilizar o novo método para executar ioT Edge em dispositivos Windows, [Azure IoT Edge para Linux no Windows](iot-edge-for-linux-on-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo Windows

  IoT Edge com recipientes Windows requer versão 1809/build 17763 do Windows, que é a mais recente [construção de suporte](/windows/release-information/)a longo prazo do Windows. Certifique-se de rever a lista de [sistemas suportados](support.md#operating-systems) para uma lista de SKUs suportados.

* Um [ID de dispositivo registado](how-to-register-device.md)

  Se registou o seu dispositivo com autenticação de chave simétrica, tenha a cadeia de ligação do dispositivo pronta.

  Se registou o seu dispositivo com a autenticação de certificado auto-assinado X.509, tenha pelo menos um dos certificados de identidade que usou para registar o dispositivo e a sua chave privada correspondente disponível no seu dispositivo.

## <a name="install-a-container-engine"></a>Instale um motor de contentores

Azure IoT Edge conta com um tempo de funcionamento de contentores compatíveis com OCI como [o Moby.](https://github.com/moby/moby) Um motor baseado em Moby que está incluído no script de instalação. Não há passos adicionais para instalar o motor.

## <a name="install-the-iot-edge-security-daemon"></a>Instale o daemon de segurança IoT Edge

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

3. Reinicie o dispositivo se solicitado.

Quando instalar o IoT Edge num dispositivo, pode utilizar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Aponte o instalador para um diretório local para instalação offline.

Para obter mais informações sobre estes parâmetros adicionais, consulte [scripts PowerShell para IoT Edge com recipientes Windows](reference-windows-scripts.md).

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

Para obter mais informações sobre estes parâmetros adicionais, consulte [scripts PowerShell para IoT Edge com recipientes Windows](reference-windows-scripts.md).

### <a name="option-2-authenticate-with-x509-certificates"></a>Opção 2: Autenticar com certificados X.509

Neste momento, o tempo de execução IoT Edge encontra-se instalado no seu dispositivo Windows, sendo necessário dotar o dispositivo com a sua identidade em nuvem e informações de autenticação.

Esta secção percorre os passos para providenciar um dispositivo com autenticação de certificado X.509. Deverá ter registado o seu dispositivo no IoT Hub, fornecendo impressões digitais que correspondam ao certificado e à chave privada localizado no seu dispositivo IoT Edge. Caso contrário, siga os passos no [Registo de um dispositivo IoT Edge no IoT Hub](how-to-register-device.md).

1. No dispositivo IoT Edge, executar o PowerShell como administrador.

2. Utilize o comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar o tempo de funcionamento do IoT Edge na sua máquina.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
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

Para obter mais informações sobre estes parâmetros adicionais, consulte [scripts PowerShell para IoT Edge com recipientes Windows](reference-windows-scripts.md).

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
   * Microsoft-Azure-IoTEdge-amd64.cab do canal de lançamento 1.1.

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
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Executar o comando Update-IoTEdge remove e atualiza o daemon de segurança do seu dispositivo, juntamente com as duas imagens do recipiente de tempo de execução. O ficheiro config.yaml é mantido no dispositivo, bem como dados do motor do contentor Moby. Manter as informações de configuração significa que não tem de fornecer novamente informações sobre a cadeia de ligação ou serviço de fornecimento de dispositivos para o seu dispositivo durante o processo de atualização.

Se pretender atualizar para uma versão específica do daemon de segurança, encontre a versão do canal de lançamento 1.1 que pretende direcionar a partir das [versões IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, descarregue o ficheiro **Microsoft-Azure-IoTEdge.cab.** Em seguida, use o `-OfflineInstallationPath` parâmetro para apontar para a localização do ficheiro local. Por exemplo:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>O `-OfflineInstallationPath` parâmetro procura um ficheiro chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. Mude o nome do ficheiro para remover o sufixo da arquitetura se tiver um.

Se pretender atualizar um dispositivo offline, encontre a versão que pretende direcionar a partir das [versões Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, descarregue os ficheiros *IoTEdgeSecurityDaemon.ps1* e *Microsoft-Azure-IoTEdge.cab.* É importante utilizar o script PowerShell a partir da mesma versão que o ficheiro .cab que utiliza porque a funcionalidade muda para suportar as funcionalidades em cada versão.

Se o ficheiro .cab que descarregou tiver um sufixo de arquitetura, mude o nome do ficheiro para apenas **Microsoft-Azure-IoTEdge.cab**.

Para atualizar com componentes offline, [ponto fonte](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) da cópia local do script PowerShell. Em seguida, use o `-OfflineInstallationPath` parâmetro como parte do comando e forneça o caminho absoluto para o `Update-IoTEdge` diretório de ficheiros. Por exemplo,

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Para obter mais informações sobre opções de atualização, utilize o comando `Get-Help Update-IoTEdge -full` ou consulte [scripts PowerShell para IoT Edge com recipientes Windows](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>Desinstalar ioT Edge

Se pretender remover a instalação IoT Edge do seu dispositivo, utilize os seguintes comandos.

Se pretender remover a instalação IoT Edge do seu dispositivo Windows, utilize o comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) a partir de uma janela administrativa powerShell. Este comando remove o tempo de funcionamento do IoT Edge, juntamente com a configuração existente e os dados do motor Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Para obter mais informações sobre opções de desinstalação, utilize o comando `Get-Help Uninstall-IoTEdge -full` .

## <a name="next-steps"></a>Passos seguintes

Continue a [implementar módulos IoT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos no seu dispositivo.

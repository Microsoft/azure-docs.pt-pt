---
title: Instalar o Azure IoT Edge no Windows | Documentos da Microsoft
description: Instruções de instalação do IoT Edge do Azure no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 8907ae61fb03b417a74eb32e1fd09aece75d5e2c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151726"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o runtime do Azure IoT Edge no Windows

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud. 

Para saber mais sobre o runtime do IoT Edge, veja [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Este artigo lista os passos para instalar o runtime do Azure IoT Edge no seu Windows x64 (AMD/Intel) sistema através de contentores do Windows.

> [!NOTE]
> Um problema conhecido de sistema operativo Windows impede que a transição para suspender e Hibernar Estados de energia quando módulos do IoT Edge (processo isolado contentores do Windows Nano Server) estão em execução. Este problema afeta a vida útil da bateria do dispositivo.
>
> Como solução, utilize o comando `Stop-Service iotedge` parar quaisquer módulos do IoT Edge em execução antes de utilizar estes Estados de energia. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

Através de contentores de Linux em sistemas Windows não é uma configuração de produção recomendada ou suportado para o Azure IoT Edge. No entanto, ele pode ser usado para fins de testes e de desenvolvimento. Para obter mais informações, consulte [utilize o IoT Edge no Windows para executar contentores do Linux](how-to-install-iot-edge-windows-with-linux.md).

Para obter informações sobre o que inclui a versão mais recente do IoT Edge, veja [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Pré-requisitos

Utilize esta secção para rever se o seu dispositivo Windows pode suportar IoT Edge e para o preparar para um mecanismo de contentor antes da instalação. 

### <a name="supported-windows-versions"></a>Versões suportadas do Windows

Para cenários de desenvolvimento e teste, o Azure IoT Edge com contentores do Windows pode ser instalado em qualquer versão do Windows 10 ou Windows Server de 2019 (compilação 17763) que suporta a funcionalidade de contentores. Para obter informações sobre os quais sistemas operacionais são atualmente suportados para cenários de produção, consulte [sistemas de suporte do Azure IoT Edge](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Preparar para um mecanismo de contentor 

O Azure IoT Edge se baseia numa [compatível com o OCI](https://www.opencontainers.org/) motor de contentor. Para cenários de produção, utilize o mecanismo de Moby incluído no script de instalação para executar contentores do Windows no seu dispositivo Windows. 

## <a name="install-iot-edge-on-a-new-device"></a>Instalar o IoT Edge num novo dispositivo

>[!NOTE]
>Pacotes de software do Azure IoT Edge são sujeita aos termos de licença localizados nos pacotes (no diretório de licença). Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

Um script do PowerShell transfere e instala o daemon de segurança do Azure IoT Edge. O daemon de segurança, em seguida, inicia o primeiro dos dois módulos de tempo de execução, o agente do IoT Edge, o que permite Implantações remotas de outros módulos. 

Quando instala o runtime do IoT Edge pela primeira vez num dispositivo, terá de aprovisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo IoT Edge pode ser aprovisionado manualmente usando uma cadeia de ligação do dispositivo fornecida pelo IoT Hub. Em alternativa, pode utilizar o serviço de aprovisionamento de dispositivos para aprovisionar automaticamente os dispositivos, que é útil quando tem o número de dispositivos para configurar. Dependendo de sua escolha de aprovisionamento, escolha o script de instalação apropriado. 

As secções seguintes descrevem os casos de utilização comuns e parâmetros para o script de instalação do IoT Edge num dispositivo novo. 

### <a name="option-1-install-and-manually-provision"></a>Opção 1: Instalação e manualmente a aprovisionar

Esta opção primeiro, fornecer uma **cadeia de ligação do dispositivo** gerado pelo IoT Hub para aprovisionar o dispositivo. 

Este exemplo demonstra uma instalação manual com contentores do Windows:

1. Se ainda não o fez, registe-se um novo dispositivo IoT Edge e obter o **cadeia de ligação do dispositivo**. Copie a cadeia de ligação para utilizar mais tarde nesta secção. Pode concluir este passo usando as ferramentas seguintes:

   * [Portal do Azure](how-to-register-device-portal.md)
   * [CLI do Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Execute o PowerShell como administrador.

   >[!NOTE]
   >Utilize uma sessão de AMD64 do PowerShell para instalar o PowerShell (x86) do IoT Edge. Se não tiver a certeza de que tipo de sessão que está a utilizar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O **Deploy IoTEdge** comando verifica se o seu computador Windows é uma versão suportada, ativa a funcionalidade de contentores e, em seguida, transfere o tempo de execução moby e o runtime do IoT Edge. Por predefinição através de contentores do Windows, o comando. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste momento, os dispositivos de IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server poderão pedir-lhe reiniciar. Se assim for, reinicie agora o seu dispositivo. Depois do dispositivo está pronto, execute novamente o PowerShell como administrador.

5. O **Initialize IoTEdge** comando configura o runtime do IoT Edge no seu computador. A comando assume a predefinição de aprovisionamento manual com contentores do Windows. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando lhe for pedido, forneça a cadeia de ligação do dispositivo que obteve no passo 1. A cadeia de ligação do dispositivo associa o dispositivo físico com um ID de dispositivo no IoT Hub. 

   A cadeia de ligação do dispositivo assume o formato seguinte e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Utilize os passos em [verificar o êxito da instalação](#verify-successful-installation) para verificar o estado do IoT Edge no dispositivo. 

Quando instalar e aprovisionar um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar a instalação, incluindo:
* Direcionar o tráfego passar por um servidor proxy
* O instalador de apontar para um diretório offline
* Declarar uma imagem de contentor do agente específico e forneça as credenciais, se está a ser um registo privado

Para obter mais informações sobre estas opções de instalação, avançar diretamente para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: Instalação automática e aprovisionamento

Essa segunda opção, aprovisionar o dispositivo com o serviço de aprovisionamento de dispositivo do IoT Hub. Forneça o **ID de âmbito** de uma instância do serviço aprovisionamento de dispositivos e o **ID de registo** do seu dispositivo.

O exemplo seguinte demonstra uma instalação automática com contentores do Windows:

1. Siga os passos em [criar e aprovisionar um dispositivo TPM IoT Edge simulado no Windows](how-to-auto-provision-simulated-device-windows.md) para configurar o serviço de aprovisionamento de dispositivos e obter seu **ID de âmbito**, simular um dispositivo TPM e recuperar seu **ID de registo**, em seguida, criar uma inscrição individual. Assim que o seu dispositivo é registado no IoT hub, prossiga com estes passos de instalação.  

   >[!TIP]
   >Mantenha a janela que está a executar o simulador de TPM aberta durante a instalação e teste. 

2. Execute o PowerShell como administrador.

   >[!NOTE]
   >Utilize uma sessão de AMD64 do PowerShell para instalar o PowerShell (x86) do IoT Edge. Se não tiver a certeza de que tipo de sessão que está a utilizar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O **Deploy IoTEdge** comando verifica se o seu computador Windows é uma versão suportada, ativa a funcionalidade de contentores e, em seguida, transfere o tempo de execução moby e o runtime do IoT Edge. Por predefinição através de contentores do Windows, o comando. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste momento, os dispositivos de IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server poderão pedir-lhe reiniciar. Se assim for, reinicie agora o seu dispositivo. Depois do dispositivo está pronto, execute novamente o PowerShell como administrador.

6. O **Initialize IoTEdge** comando configura o runtime do IoT Edge no seu computador. A comando assume a predefinição de aprovisionamento manual com contentores do Windows. Utilizar o `-Dps` sinalizador para utilizar o serviço de aprovisionamento de dispositivos em vez de aprovisionamento manual.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. Quando solicitado, forneça o ID de âmbito do seu serviço de aprovisionamento de dispositivos e o ID de registo do seu dispositivo, que o que deve ter obtido no passo 1.

8. Utilize os passos em [verificar o êxito da instalação](#verify-successful-installation) para verificar o estado do IoT Edge no dispositivo. 

Quando instalar e aprovisionar um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar a instalação, incluindo:
* Direcionar o tráfego passar por um servidor proxy
* O instalador de apontar para um diretório offline
* Declarar uma imagem de contentor do agente específico e forneça as credenciais, se está a ser um registo privado

Para obter mais informações sobre estas opções de instalação, continue a ler este artigo ou avançar para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-installation"></a>Instalação offline

Durante a instalação são transferidos dois arquivos: 
* Cab do Microsoft Azure IoT Edge, que contém o daemon de segurança de IoT Edge (iotedged), mecanismo de contentor do Moby e Moby CLI.
* Msi de (tempo de execução do VC) o pacote redistribuível do Visual C++

Pode transferir um ou ambos os arquivos antes do tempo para o dispositivo e depois apontar o script de instalação para o diretório que contém os ficheiros. O instalador verifica primeiro o diretório e, em seguida, transfere apenas os componentes que não forem encontrados. Se todos os arquivos estão disponíveis offline, pode instalar com sem ligação à internet. Também pode utilizar esta funcionalidade para instalar uma versão específica dos componentes.  

Para os ficheiros de instalação do IoT Edge mais recentes, juntamente com as versões anteriores, consulte [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Para instalar com componentes offline, utilize o `-OfflineInstallationPath` parâmetro como parte da Deploy-IoTEdge de comandos e forneça o caminho absoluto para o diretório de ficheiros. Por exemplo,

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Também pode utilizar o parâmetro de caminho de instalação offline com o comando Update-IoTEdge, apresentado neste artigo. 

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Verifique o estado do serviço IoT Edge. Ele deve estar listado como em execução.  

```powershell
Get-Service iotedge
```

Examine os registos do serviço dos últimos 5 minutos. Se acabado de instalar o runtime do IoT Edge, poderá ver uma lista de erros de tempo entre a execução **Deploy IoTEdge** e **Initialize IoTEdge**. Estes erros são esperados, como o serviço está a tentar iniciar antes de a ser configurado. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista de módulos em execução. Depois de uma nova instalação, o único módulo deve vir em execução seja **edgeAgent**. Depois de [implementar módulos IoT Edge](how-to-deploy-modules-portal.md), irá ver outras pessoas. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gerir contentores de módulo

O serviço de IoT Edge requer um mecanismo de contentor em execução no seu dispositivo. Quando implementar um módulo de um dispositivo, o runtime do IoT Edge utiliza o motor de contentor para extrair a imagem de contentor a partir de um registo na cloud. O serviço de IoT Edge permite-lhe interagir com seus módulos e recupere os registos, mas, às vezes, pode querer usar o mecanismo de contentor para interagir com o próprio contêiner. 

Para obter mais informações sobre os conceitos de módulo, consulte [módulos de compreender o Azure IoT Edge](iot-edge-modules.md). 

Se estiver a executar contentores do Windows no seu dispositivo Windows IoT Edge, em seguida, a instalação do IoT Edge incluído o mecanismo de contentor Moby. O mecanismo de Moby baseado nos mesmos padrões de Docker e foi concebido para ser executado em paralelo no mesmo computador como ambiente de trabalho do Docker. Por esse motivo, se desejar contêineres de destino geridos pelo motor Moby, terá de se destinam especificamente que o motor, em vez de Docker. 

Por exemplo, para listar todas as imagens do Docker, utilize o seguinte comando:

```powershell
docker images
```

Para listar todas as imagens de Moby, modifique o mesmo comando com um ponteiro para o mecanismo de Moby: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

O mecanismo de URI é listado no resultado do script de instalação, ou pode encontrá-lo na secção de definições de tempo de execução de contentor para o ficheiro de config.yaml. 

![uri de moby_runtime no config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Para obter mais informações sobre os comandos que pode utilizar para interagir com contentores e imagens em execução no seu dispositivo, consulte [interfaces de linha de comandos do Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Atualizar uma instalação existente

Se já instalou o runtime do IoT Edge num dispositivo antes e aprovisionou-o com uma identidade do IoT Hub, em seguida, pode atualizar o tempo de execução sem ter de reintroduzir as suas informações de dispositivo. 

Para obter mais informações, consulte [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

Este exemplo mostra uma instalação que aponta para um ficheiro de configuração existente e utiliza os contentores do Windows: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Quando atualiza o IoT Edge, pode utilizar parâmetros adicionais para modificar a atualização, incluindo:
* Direcionar o tráfego para passar por um servidor proxy, ou
* O instalador de apontar para um diretório offline 
* Reinício sem uma linha de comandos, se necessário

Não pode declarar uma imagem de contentor de agente do IoT Edge com parâmetros de script porque essa informação já está definida no ficheiro de configuração da instalação anterior. Se pretender modificar a imagem de contentor do agente, faça-o no ficheiro config.yaml. 

Para obter mais informações sobre estas opções de atualização, utilize o comando `Get-Help Update-IoTEdge -full` ou veja [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Desinstalar do IoT Edge

Se pretender remover a instalação do IoT Edge do seu dispositivo Windows, utilize o seguinte comando a partir de uma janela do PowerShell administrativa. Este comando remove o runtime do IoT Edge, juntamente com sua configuração existente e os dados de motor de Moby. 

```powershell
Uninstall-IoTEdge
```

O comando de desinstalação IoTEdge não funciona no Windows IoT Core. Para remover o IoT Edge de dispositivos do Windows IoT Core, terá de voltar a implementar a sua imagem do Windows IoT Core. 

Para obter mais informações sobre as opções de desinstalação, utilize o comando `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As secções anteriores introduziram os cenários de instalação comuns com exemplos de como utilizar os parâmetros para modificar o script de instalação. Esta secção fornece as tabelas de referência dos parâmetros comuns de utilizado para instalar, atualizar ou desinstalar o IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

O comando de implementar IoTEdge transfere e implementa o Daemon de segurança do IoT Edge e as respetivas dependências. O comando de implementação aceita estes parâmetros comuns, entre outros. Para obter a lista completa, utilize o comando `Get-Help Deploy-IoTEdge -full`.  

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se não for especificado nenhum sistema operacional de contentor, o Windows é o valor predefinido.<br><br>Para contentores do Windows, o IoT Edge utiliza o motor do contentor de moby incluído na instalação. Para contentores do Linux, tem de instalar um mecanismo de contentor antes de iniciar a instalação. |
| **Proxy** | URL de Proxy | Inclua este parâmetro se o seu dispositivo precisa passar por um servidor proxy para a internet. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se este parâmetro estiver incluído, o instalador irá verificar o diretório listado para o cab do IoT Edge e os arquivos de MSI de tempo de execução do VC necessários para a instalação. Todos os ficheiros não foi encontrados no diretório são transferidos. Se ambos os ficheiros estão no diretório, é possível instalar o IoT Edge sem uma ligação à internet. Também pode utilizar este parâmetro para utilizar uma versão específica. |
| **InvokeWebRequestParameters** | A tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da web são feitas. Utilize este campo para definir parâmetros para essas solicitações da web. Este parâmetro é útil para configurar as credenciais para os servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | nenhum | Este sinalizador permite que o script de implementação reiniciar a máquina sem pedir confirmação, se necessário. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

O comando Initialize IoTEdge configura o IoT Edge com a sua cadeia de ligação do dispositivo e os detalhes operacionais. Muitas das informações geradas por este comando, em seguida, é armazenada no ficheiro iotedge\config.yaml. O comando de inicialização aceita estes parâmetros comuns, entre outros. Para obter a lista completa, utilize o comando `Get-Help Initialize-IoTEdge -full`. 

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhuma | **Mudar o parâmetro**. Se não for especificado nenhum tipo de aprovisionamento manual é o valor predefinido.<br><br>Declara que irá fornecer uma cadeia de ligação do dispositivo para aprovisionar o dispositivo manualmente |
| **Dps** | Nenhuma | **Mudar o parâmetro**. Se não for especificado nenhum tipo de aprovisionamento manual é o valor predefinido.<br><br>Declara que irá fornecer um dispositivo de aprovisionamento DPS (serviço) ID de âmbito e o ID de registo do seu dispositivo para aprovisionar através de pontos de distribuição.  |
| **DeviceConnectionString** | Uma cadeia de ligação de um dispositivo IoT Edge registado num IoT Hub, aspas | **Necessário** para instalação manual. Se não fornecer uma cadeia de ligação nos parâmetros do script, será solicitado para um durante a instalação. |
| **ScopeId** | Um ID de âmbito de uma instância do serviço de aprovisionamento de dispositivos associados ao seu IoT Hub. | **Necessário** para a instalação de pontos de distribuição. Se não fornecer um ID de âmbito nos parâmetros do script, será solicitado para um durante a instalação. |
| **RegistrationId** | Um ID de registo gerado pelo seu dispositivo | **Necessário** para a instalação de pontos de distribuição. Se não fornecer um ID de registo nos parâmetros do script, será solicitado para um durante a instalação. |
| **ContainerOs** | **Windows** ou **Linux** | Se não for especificado nenhum sistema operacional de contentor, o Windows é o valor predefinido.<br><br>Para contentores do Windows, o IoT Edge utiliza o motor do contentor de moby incluído na instalação. Para contentores do Linux, tem de instalar um mecanismo de contentor antes de iniciar a instalação. |
| **InvokeWebRequestParameters** | A tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da web são feitas. Utilize este campo para definir parâmetros para essas solicitações da web. Este parâmetro é útil para configurar as credenciais para os servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI da imagem de agente a IoT Edge | Por predefinição, uma nova instalação do IoT Edge utiliza a etiqueta mais recente sem interrupção para a imagem de agente do IoT Edge. Utilize este parâmetro para definir uma etiqueta específica para a versão da imagem, ou para fornecer sua própria imagem de agente. Para obter mais informações, consulte [etiquetas de compreender o IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de Utilizador** | Nome de utilizador de registo de contentor | Utilize este parâmetro só se definir o parâmetro - AgentImage para um contentor num registo privado. Forneça um nome de utilizador com acesso ao registo. |
| **Palavra-passe** | Cadeia de palavra-passe segura | Utilize este parâmetro só se definir o parâmetro - AgentImage para um contentor num registo privado. Forneça a palavra-passe para aceder ao registo. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se não existir um contentor especificado do sistema operacional, o Windows é o valor predefinido. Para contentores do Windows, um mecanismo de contentor será incluído na instalação. Para contentores do Linux, tem de instalar um mecanismo de contentor antes de iniciar a instalação. |
| **Proxy** | URL de Proxy | Inclua este parâmetro se o seu dispositivo precisa passar por um servidor proxy para a internet. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | A tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da web são feitas. Utilize este campo para definir parâmetros para essas solicitações da web. Este parâmetro é útil para configurar as credenciais para os servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se este parâmetro estiver incluído, o instalador irá verificar o diretório listado para o cab do IoT Edge e os arquivos de MSI de tempo de execução do VC necessários para a instalação. Todos os ficheiros não foi encontrados no diretório são transferidos. Se ambos os ficheiros estão no diretório, é possível instalar o IoT Edge sem uma ligação à internet. Também pode utilizar este parâmetro para utilizar uma versão específica. |
| **RestartIfNeeded** | nenhum | Este sinalizador permite que o script de implementação reiniciar a máquina sem pedir confirmação, se necessário. |


### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Force** | nenhum | Este sinalizador força a desinstalação, no caso da anterior tentativa de desinstalação foi concluída com êxito. 
| **RestartIfNeeded** | nenhum | Este sinalizador permite que o script de desinstalação reiniciar a máquina sem pedir confirmação, se necessário. |


## <a name="next-steps"></a>Passos Seguintes

Agora que tiver um dispositivo IoT Edge aprovisionado com o tempo de execução instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver a ter problemas ao instalar corretamente o IoT Edge, veja a [resolução de problemas](troubleshoot.md) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, veja [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

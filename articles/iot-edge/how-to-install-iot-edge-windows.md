---
title: Instalar o Azure IoT Edge no Windows | Documentos da Microsoft
description: Instruções de instalação do IoT Edge do Azure no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 5f421c8949efae5a2488d5bf156a5d3571401bcc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996441"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o runtime do Azure IoT Edge no Windows

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud. 

Para saber mais sobre o runtime do IoT Edge, veja [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Este artigo lista os passos para instalar o runtime do Azure IoT Edge no seu Windows x64 (AMD/Intel) system. Suporte do Windows está atualmente em pré-visualização.

> [!NOTE]
> Através de contentores de Linux em sistemas Windows não é uma configuração de produção recomendada ou suportado para o Azure IoT Edge. No entanto, ele pode ser usado para fins de testes e de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Utilize esta secção para rever se o seu dispositivo Windows pode suportar IoT Edge e para o preparar para um mecanismo de contentor antes da instalação. 

### <a name="supported-windows-versions"></a>Versões suportadas do Windows

O Azure IoT Edge suporta versões diferentes do Windows, dependendo se estiver a executar contentores do Windows ou de contentores do Linux. 

A versão mais recente do Azure IoT Edge com contentores do Windows pode ser executado nas seguintes versões do Windows:
* Windows 10 ou IoT Core, com Outubro de 2018 update (compilação 17763)
* Windows Server 2019 (build 17763)

A versão mais recente do Azure IoT Edge com contentores do Linux pode ser executado nas seguintes versões do Windows: 
* Atualização de aniversário do Windows 10 (compilação 14393) ou mais recente
* Windows Server 2016 ou mais recente

Para obter mais informações sobre os quais sistemas operacionais são atualmente suportados, consulte [suporte do Azure IoT Edge](support.md#operating-systems). 

Para obter mais informações sobre o que inclui a versão mais recente do IoT Edge, veja [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

### <a name="prepare-for-a-container-engine"></a>Preparar para um mecanismo de contentor 

O Azure IoT Edge se baseia numa [compatível com o OCI](https://www.opencontainers.org/) motor de contentor. Para cenários de produção, utilize o mecanismo de Moby incluído no script de instalação para executar contentores do Windows no seu dispositivo Windows. Para o desenvolvimento e teste, pode executar contentores do Linux no seu dispositivo Windows, mas tem de instalar e configurar um mecanismo de contentor antes de instalar o IoT Edge. Para ambos os cenários, consulte as secções seguintes para pré-requisitos preparar o seu dispositivo. 

Se pretender instalar o IoT Edge numa máquina virtual, ativar a virtualização aninhada e atribuir, pelo menos, 2 GB de memória. Como ativar a virtualização aninhada é diferente consoante o hipervisor a sua utilização. Para o Hyper-V, máquinas virtuais de geração 2 aninha virtualização ativada por predefinição. Para VMWare, existe um botão de alternar para ativar a funcionalidade na sua máquina virtual. 

#### <a name="moby-engine-for-windows-containers"></a>Motor de Moby para contentores do Windows

Para dispositivos do Windows que executam o IoT Edge em cenários de produção, Moby é o mecanismo de contentor oficialmente suportada única. O script de instalação instala automaticamente o mecanismo de Moby no seu dispositivo antes de instalar o IoT Edge. Prepare o seu dispositivo, Ativando a funcionalidade de contentores. 

1. Na barra de início, procure **ou desativar funcionalidades do Windows ativar** e abrir o programa de painel de controle.
2. Localize e selecione **contentores**.
3. Selecione **OK**. 

#### <a name="docker-for-linux-containers"></a>Docker para contentores do Linux

Se estiver a utilizar o Windows para desenvolver e testar contentores para dispositivos de Linux, pode utilizar [Docker para Windows](https://www.docker.com/docker-windows) como seu mecanismo de contentor. Docker pode ser configurado para [utilizar contentores de Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). Tem de instalar o Docker e configurá-lo antes de instalar o IoT Edge. Contentores do Linux não são suportadas em dispositivos Windows em produção. 

Se o seu dispositivo IoT Edge é um computador Windows, verifique se cumprem os [requisitos de sistema](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) para Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instalar o IoT Edge num novo dispositivo

>[!NOTE]
>Pacotes de software do Azure IoT Edge são sujeita aos termos de licença localizados nos pacotes (no diretório de licença). Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

Um script do PowerShell transfere e instala o daemon de segurança do Azure IoT Edge. O daemon de segurança, em seguida, inicia o primeiro dos dois módulos de tempo de execução, o agente do IoT Edge, o que permite Implantações remotas de outros módulos. 

Quando instala o runtime do IoT Edge pela primeira vez num dispositivo, terá de aprovisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo IoT Edge pode ser aprovisionado manualmente através de uma cadeia de ligações de dispositivo fornecida pelo IoT Hub. Em alternativa, pode utilizar o serviço de aprovisionamento de dispositivos para aprovisionar automaticamente os dispositivos, que é útil quando tem o número de dispositivos para configurar. Dependendo de sua escolha de aprovisionamento, escolha o script de instalação apropriado. 

As secções seguintes descrevem os casos de utilização comuns e parâmetros para o script de instalação do IoT Edge num dispositivo novo. 

### <a name="option-1-install-and-manually-provision"></a>Opção 1: Instalação e manualmente a aprovisionar

Nesta primeira opção, forneça uma cadeia de ligação do dispositivo gerada pelo IoT Hub para aprovisionar o dispositivo. 

Siga os passos em [registar um novo dispositivo Azure IoT Edge](how-to-register-device-portal.md) para registar o seu dispositivo e obter a cadeia de ligação do dispositivo. 

Este exemplo mostra uma instalação manual com contentores do Windows:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

Quando instalar e aprovisionar um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar a instalação, incluindo:
* Direcionar o tráfego passar por um servidor proxy
* O instalador de apontar para um diretório offline
* Declarar uma imagem de contentor do agente específico e forneça as credenciais, se está a ser um registo privado
* Ignorar a instalação da CLI de Moby

Para obter mais informações sobre estas opções de instalação, continue a ler este artigo ou avançar para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: Instalação automática e aprovisionamento

Essa segunda opção, aprovisionar o dispositivo com o serviço de aprovisionamento de dispositivo do IoT Hub. Forneça o **ID de âmbito** de uma instância do serviço aprovisionamento de dispositivos e o **ID de registo** do seu dispositivo.

Siga os passos em [criar e aprovisionar um dispositivo de periferia de TPM simulado no Windows](how-to-auto-provision-simulated-device-windows.md) para configurar o serviço de aprovisionamento de dispositivos e obter seu **ID de âmbito**, simular um dispositivo TPM e recuperar seu  **ID de registo**, em seguida, criar uma inscrição individual. Depois do dispositivo estiver registado no IoT Hub, continue com a instalação.  

   >[!TIP]
   >Mantenha a janela que está a executar o simulador de TPM aberta durante a instalação e teste. 

O exemplo seguinte mostra uma instalação automática com contentores do Windows:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

Quando instalar e aprovisionar um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar a instalação, incluindo:
* Direcionar o tráfego passar por um servidor proxy
* O instalador de apontar para um diretório offline
* Declarar uma imagem de contentor do agente específico e forneça as credenciais, se está a ser um registo privado
* Ignorar a instalação da CLI de Moby

Para obter mais informações sobre estas opções de instalação, continue a ler este artigo ou avançar para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="update-an-existing-installation"></a>Atualizar uma instalação existente

Se já instalou o runtime do IoT Edge num dispositivo antes e aprovisionou-o com uma identidade do IoT Hub, em seguida, pode utilizar um script de instalação simplificada. O sinalizador `-ExistingConfig` declara que já existe um arquivo de configuração do IoT Edge no dispositivo. O ficheiro de configuração contém informações sobre as definições de identidade, bem como de certificados e de rede do dispositivo. Pode utilizar esta opção de instalação se o dispositivo foi originalmente aprovisionado manual ou automaticamente. 

Para obter mais informações, consulte [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

Este exemplo mostra uma instalação que aponta para um ficheiro de configuração existente e utiliza os contentores do Windows: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

Quando instala o IoT Edge de um ficheiro de configuração existente, pode utilizar parâmetros adicionais para modificar a instalação, incluindo:
* Direcionar o tráfego passar por um servidor proxy
* O instalador de apontar para um diretório offline, ou 
* Ignore a instalação da CLI de Moby. 

Não pode declarar uma imagem de contentor de agente do IoT Edge com parâmetros de script, porque essa informação já está definida no ficheiro de configuração da instalação anterior. Se pretender modificar a imagem de contentor do agente, faça-o no ficheiro config.yaml. 

Para obter mais informações sobre estas opções de instalação, continue a ler este artigo ou avançar para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-installation"></a>Instalação offline

Durante a instalação são transferidos quatro arquivos: 
* Zip de daemon (iotedgd) de segurança de IoT Edge 
* Zip do motor de Moby
* Zip Moby CLI
* Msi de (tempo de execução do VC) o pacote redistribuível do Visual C++

Pode transferir um ou todos esses arquivos antes do tempo para o dispositivo e depois apontar o script de instalação para o diretório que contém os ficheiros. O instalador verifica primeiro o diretório e, em seguida, transfere apenas os componentes que não forem encontrados. Se todos os quatro arquivos estão disponíveis offline, pode instalar com sem ligação à internet. Também pode utilizar esta funcionalidade para substituir as versões online de um ou mais componentes.  

Para os ficheiros de instalação mais recentes, juntamente com as versões anteriores, consulte [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)

Para instalar com componentes offline, utilize o `-OfflineInstallationPath` parâmetro e forneça o caminho absoluto para o diretório de ficheiros. Por exemplo,

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As secções anteriores introduziram os cenários de instalação comuns com exemplos de como utilizar os parâmetros para modificar o script de instalação. Esta seção fornece uma tabela de referência de parâmetros válidos, que pode utilizar para instalar o IoT Edge. Para obter mais informações, execute `get-help Install-SecurityDaemon -full` numa janela do PowerShell. 

Para instalar o IoT Edge com uma configuração existente, o comando de instalação pode utilizar estes parâmetros comuns: 

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhuma | **Mudar o parâmetro**. Cada instalação tem de ser declarada de qualquer manual, DPS, ou existingconfig.<br><br>Declara que irá fornecer uma cadeia de ligação do dispositivo para aprovisionar o dispositivo manualmente |
| **Dps** | Nenhuma | **Mudar o parâmetro**. Cada instalação tem de ser declarada de qualquer manual, DPS, ou existingconfig.<br><br>Declara que irá fornecer um dispositivo de aprovisionamento DPS (serviço) ID de âmbito e o ID de registo do seu dispositivo para aprovisionar através de pontos de distribuição.  |
| **ExistingConfig** | Nenhuma | **Mudar o parâmetro**. Cada instalação tem de ser declarada de qualquer manual, DPS, ou existingconfig.<br><br>Declara que já existe um ficheiro de config.yaml no dispositivo com as informações de aprovisionamento. |
| **DeviceConnectionString** | Uma cadeia de ligação de um dispositivo IoT Edge registado num IoT Hub, aspas | **Necessário** para instalação manual. Se não fornecer uma cadeia de ligação nos parâmetros do script, será solicitado para um durante a instalação. |
| **ScopeId** | Um ID de âmbito de uma instância do serviço de aprovisionamento de dispositivos associados ao seu IoT Hub. | **Necessário** para a instalação de pontos de distribuição. Se não fornecer um ID de âmbito nos parâmetros do script, será solicitado para um durante a instalação. |
| **RegistrationId** | Um ID de registo gerado pelo seu dispositivo | **Necessário** para a instalação de pontos de distribuição. Se não fornecer um ID de registo nos parâmetros do script, será solicitado para um durante a instalação. |
| **ContainerOs** | **Windows** ou **Linux** | Se não existir um contentor especificado do sistema operacional, o Linux é o valor predefinido. Para contentores do Windows, um mecanismo de contentor será incluído na instalação. Para contentores do Linux, tem de instalar um mecanismo de contentor antes de iniciar a instalação. Executar contentores do Linux no Windows é um cenário de desenvolvimento útil, mas não é suportada na produção. |
| **Proxy** | URL de Proxy | Inclua este parâmetro se o seu dispositivo precisa passar por um servidor proxy para a internet. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | A tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da web são feitas. Utilize este campo para definir parâmetros para essas solicitações da web. Este parâmetro é útil para configurar as credenciais para os servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se este parâmetro estiver incluído, o instalador irá verificar o diretório para o iotedged zip, zip do motor de Moby, zip Moby CLI e arquivos MSI de tempo de execução do VC necessários para a instalação. Se todos os ficheiros de quatro estão no diretório, pode instalar o IoT Edge ao mesmo tempo offline. Também pode utilizar este parâmetro para substituir a versão online de um componente específico. |
| **AgentImage** | URI da imagem de agente a IoT Edge | Por predefinição, uma nova instalação do IoT Edge utiliza a etiqueta mais recente sem interrupção para a imagem de agente do IoT Edge. Utilize este parâmetro para definir uma etiqueta específica para a versão da imagem, ou para fornecer sua própria imagem de agente. Para obter mais informações, consulte [etiquetas de compreender o IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de Utilizador** | Nome de utilizador de registo de contentor | Utilize este parâmetro só se definir o parâmetro - AgentImage para um contentor num registo privado. Forneça um nome de utilizador com acesso ao registo. |
| **Palavra-passe** | Cadeia de palavra-passe segura | Utilize este parâmetro só se definir o parâmetro - AgentImage para um contentor num registo privado. Forneça a palavra-passe para aceder ao registo. | 
| **SkipMobyCli** | Nenhuma | Apenas aplicável se - ContainerOS estiver definido como Windows. Não instale a CLI Moby (docker.exe) para $MobyInstallDirectory. |

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Pode verificar o estado do serviço IoT Edge por: 

```powershell
Get-Service iotedge
```

Examine os registos dos últimos 5 minutos através do serviço:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

E lista de módulos com a executar:

```powershell
iotedge list
```

Depois de uma nova instalação, o único módulo deve vir em execução seja **edgeAgent**. Depois de [implementar módulos IoT Edge](how-to-deploy-modules-portal.md), irá ver outras pessoas. 

## <a name="manage-module-containers"></a>Gerir contentores de módulo

O serviço de IoT Edge requer um mecanismo de contentor em execução no seu dispositivo. Quando implementar um módulo de um dispositivo, o runtime do IoT Edge utiliza o motor de contentor para extrair a imagem de contentor a partir de um registo na cloud. O serviço de IoT Edge permite-lhe interagir com seus módulos e recupere os registos, mas, às vezes, pode querer usar o mecanismo de contentor para interagir com o próprio contêiner. 

Para obter mais informações sobre os conceitos de módulo, consulte [módulos de compreender o Azure IoT Edge](iot-edge-modules.md). 

Se estiver a executar contentores do Windows no seu dispositivo Windows IoT Edge, em seguida, a instalação do IoT Edge incluído o mecanismo de contentor Moby. Se estiver a desenvolver contentores do Linux no seu computador de desenvolvimento do Windows, provavelmente está usando o ambiente de trabalho do Docker. O mecanismo de Moby baseado nos mesmos padrões de Docker e foi concebido para ser executado em paralelo no mesmo computador como ambiente de trabalho do Docker. Por esse motivo, se desejar contêineres de destino geridos pelo motor Moby, terá de se destinam especificamente que o motor, em vez de Docker. 

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

## <a name="uninstall-iot-edge"></a>Desinstalar do IoT Edge

Se pretender remover a instalação do IoT Edge do seu dispositivo Windows, utilize o seguinte comando a partir de uma janela do PowerShell administrativa. Este comando remove o runtime do IoT Edge, juntamente com sua configuração existente e os dados de motor de Moby. 

```PowerShell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

Se pretende reinstalar o IoT Edge no seu dispositivo, omitir os `-DeleteConfig` e `-DeleteMobyDataRoot` parâmetros, de modo a que pode reinstalar o daemon de segurança mais tarde com as informações de configuração existente. 

## <a name="next-steps"></a>Passos Seguintes

Agora que tiver um dispositivo IoT Edge aprovisionado com o tempo de execução instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver a ter problemas ao instalar corretamente o IoT Edge, veja a [resolução de problemas](troubleshoot.md) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, veja [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

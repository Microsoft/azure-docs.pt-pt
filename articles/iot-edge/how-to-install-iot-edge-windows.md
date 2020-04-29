---
title: Instale a borda Azure IoT no Windows [ Microsoft Docs
description: Instruções de instalação Azure IoT Edge no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: 61b382f1c286209a12d0be39a81e6817806d3251
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113464"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o runtime do Azure IoT Edge no Windows

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grandes como um servidor industrial. Uma vez configurado um dispositivo com o tempo de funcionamento do IoT Edge, pode começar a implementar a lógica do negócio a partir da nuvem.

Para saber mais sobre o tempo de funcionação do IoT Edge, consulte Compreender o tempo de funcionação do [Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para instalar o tempo de funcionação do Azure IoT Edge no seu sistema Windows x64 (AMD/Intel) utilizando recipientes Windows.

> [!NOTE]
> Um problema conhecido do sistema operativo Windows impede a transição para estados de energia do sono e hibernação quando os módulos IoT Edge (recipientes windows nano server isolados pelo processo) estão em execução. Este problema afeta a vida útil da bateria no dispositivo.
>
> Como sucinta, utilize `Stop-Service iotedge` o comando para parar qualquer módulo IoT Edge em funcionamento antes de utilizar estes estados de potência.

A utilização de recipientes Linux nos sistemas Windows não é uma configuração de produção recomendada ou suportada para o Azure IoT Edge. No entanto, pode ser utilizado para fins de desenvolvimento e teste. Para saber mais, consulte [Use IoT Edge no Windows para executar recipientes Linux](how-to-install-iot-edge-windows-with-linux.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte os [lançamentos do Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

## <a name="prerequisites"></a>Pré-requisitos

Utilize esta secção para analisar se o seu dispositivo Windows pode suportar o IoT Edge e prepará-lo para um motor de contentores antes da instalação.

### <a name="supported-windows-versions"></a>Versões suportadas do Windows

O IoT Edge para Windows requer a versão 1809/build 17763 do Windows, que é a mais recente construção de suporte a [longo prazo do Windows.](https://docs.microsoft.com/windows/release-information/) Para suporte ao Windows SKU, veja o que é suportado com base no facto de estar a preparar-se para cenários de produção ou desenvolvimento e cenários de teste:

* **Produção**: Para obter as últimas informações sobre quais os sistemas operativos atualmente suportados para cenários de produção, consulte [os sistemas suportados pelo Azure IoT Edge.](support.md#operating-systems)
* **Desenvolvimento e teste**: Para cenários de desenvolvimento e teste, o Azure IoT Edge com contentores Windows pode ser instalado em qualquer versão do Windows 10 ou Windows Server 2019 que suporte a funcionalidade de contentores.

Os dispositivos IoT Core devem incluir a funcionalidade opcional ioT Core Windows Containers para suportar o tempo de execução do IoT Edge. Utilize o seguinte comando numa [sessão remota](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) da PowerShell para verificar se os recipientes do Windows são suportados no seu dispositivo:

```powershell
Get-Service vmcompute
```

Se o serviço estiver presente, deverá obter uma resposta bem sucedida com o estado de serviço listado como **execução**. Se `vmcompute` o serviço não for encontrado, o seu dispositivo não preenche os requisitos para o IoT Edge. Contacte o seu fornecedor de hardware para perguntar sobre o suporte para esta funcionalidade.

### <a name="prepare-for-a-container-engine"></a>Prepare-se para um motor de contentores

O Azure IoT Edge conta com um motor de contentor [compatível com OCI.](https://www.opencontainers.org/) Para cenários de produção, utilize o motor Moby incluído no script de instalação para executar recipientes Windows no seu dispositivo Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Instale o IoT Edge num novo dispositivo

>[!NOTE]
>Os pacotes de software Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos da licença antes de utilizar o pacote. A sua instalação e utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize o pacote.

Um script PowerShell descarrega e instala o daemon de segurança Azure IoT Edge. O daemon de segurança inicia então o primeiro de dois módulos de tempo de execução, o agente IoT Edge, que permite a implantação remota de outros módulos.

>[!TIP]
>Para dispositivos IoT Core, recomendamos executar os comandos de instalação utilizando uma sessão RemotePowerShell. Para mais informações, consulte [A Utilização da PowerShell para windows ioT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Quando instalar o tempo de execução do IoT Edge pela primeira vez num dispositivo, é necessário fornecer o dispositivo com uma identidade a partir de um hub IoT. Um único dispositivo IoT Edge pode ser fornecido manualmente utilizando uma cadeia de ligação do dispositivo fornecida pelo IoT Hub. Ou, pode utilizar o Serviço de Fornecimento de Dispositivos (DPS) para fornecer automaticamente dispositivos, o que é útil quando tem muitos dispositivos para configurar. Dependendo da sua escolha de provisionamento, escolha o script de instalação apropriado.

As seguintes secções descrevem os casos e parâmetros de utilização comuns para o script de instalação IoT Edge num novo dispositivo.

### <a name="option-1-install-and-manually-provision"></a>Opção 1: Instalação e fornecimento manual

Nesta primeira opção, fornece uma cadeia de **ligação** ao dispositivo gerada pelo IoT Hub para fornecer o dispositivo.

Este exemplo demonstra uma instalação manual com recipientes Windows:

1. Se ainda não o fez, registe um novo dispositivo IoT Edge e recupere a **cadeia de ligação**do dispositivo . Copie a cadeia de ligação para utilizar mais tarde nesta secção. Pode completar este passo utilizando as seguintes ferramentas:

   * [Portal do Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [CLI do Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Execute o PowerShell como um administrador.

   >[!NOTE]
   >Utilize uma sessão AMD64 da PowerShell para instalar o IoT Edge, não o PowerShell (x86). Se não tiver a certeza de que tipo de sessão está a usar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se a sua máquina Windows está numa versão suportada, liga a funcionalidade de contentores e, em seguida, descarrega o tempo de execução moby e o tempo de execução do IoT Edge. O comando não se aplica à utilização de recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos do Windows 10 ou Do Windows Server podem pedir-lhe para reiniciar. Em caso afirmativo, reinicie o seu dispositivo agora. Uma vez que o seu dispositivo esteja pronto, execute a PowerShell como administrador novamente.

5. O comando **Initialize-IoTEdge** configura o tempo de execução do IoT Edge na sua máquina. O comando não se aplica ao fornecimento manual com recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando solicitado, forneça a cadeia de ligação do dispositivo que recuperou no passo 1. A cadeia de ligação do dispositivo associa o dispositivo físico a um ID do dispositivo no IoT Hub.

   A cadeia de ligação do dispositivo toma o seguinte formato e não deve incluir aspas:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Utilize as medidas em Verificar a [instalação bem sucedida](#verify-successful-installation) para verificar o estado do IoT Edge no seu dispositivo.

Quando instalar e fornecer um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar a instalação, incluindo:

* Tráfego direto para passar por um servidor proxy
* Aponte o instalador para um diretório offline
* Declare uma imagem específica do contentor do agente, e forneça credenciais se estiver em um registo privado

Para mais informações sobre estas opções de instalação, avance para saber sobre [todos os parâmetros](#all-installation-parameters)de instalação .

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: Instalar e fornecer automaticamente

Nesta segunda opção, disponibiliza o dispositivo utilizando o Serviço de Provisionamento de Dispositivos IoT Hub. Forneça o ID de **âmbito** de uma instância do Serviço de Provisionamento de Dispositivos, juntamente com qualquer outra informação específica ao seu mecanismo de [atestado](../iot-dps/concepts-security.md#attestation-mechanism)preferido:

* [Crie e forre um dispositivo IoT Edge simulado com um TPM virtual no Windows](how-to-auto-provision-simulated-device-windows.md)
* [Criar e fornecer um dispositivo IoT Edge simulado utilizando certificados X.509](how-to-auto-provision-x509-certs.md)
* [Criar e fornecer um dispositivo IoT Edge utilizando atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Quando instalar e fornecer um dispositivo automaticamente, pode utilizar parâmetros adicionais para modificar a instalação, incluindo:

* Tráfego direto para passar por um servidor proxy
* Aponte o instalador para um diretório offline
* Declare uma imagem específica do contentor do agente, e forneça credenciais se estiver em um registo privado

Para mais informações sobre estas opções de instalação, continue a ler este artigo ou salte para saber sobre [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Instalação de versão offline ou específica

Durante a instalação são descarregados três ficheiros:

* Um script PowerShell, que contém as instruções de instalação
* Cabine Microsoft Azure IoT Edge, que contém o daemon de segurança IoT Edge (iotedged), motor de contentorMo by e Moby CLI
* Instalador redistribuível Visual C++ (VC runtime)

Se o seu dispositivo estiver offline durante a instalação, ou se pretender instalar uma versão específica do IoT Edge, pode descarregar estes ficheiros com antecedência para o dispositivo. Quando chegar a hora de instalar, aponte o script de instalação para o diretório que contém os ficheiros descarregados. O instalador verifica primeiro esse diretório, e depois só descarrega componentes que não são encontrados. Se todos os ficheiros estiverem disponíveis offline, pode instalar sem ligação à Internet.

Também pode utilizar o parâmetro do caminho de instalação offline para atualizar o IoT Edge. Para mais informações, consulte [Atualizar o daemon de segurança IoT Edge e o tempo](how-to-update-iot-edge.md)de execução .

1. Para obter os mais recentes ficheiros de instalação IoT Edge juntamente com versões anteriores, consulte [lançamentos Do Edge Azure IoT.](https://github.com/Azure/azure-iotedge/releases)

2. Encontre a versão que pretende instalar e descarregue os seguintes ficheiros da secção **Assets** das notas de lançamento no seu dispositivo IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab a partir de lançamentos 1.0.9 ou mais recentes, ou Microsoft-Azure-IoTEdge.cab a partir dos lançamentos 1.0.8 ou mais velhos.

   O Microsoft-Azure-IotEdge-arm32.cab também está disponível a partir de 1.0.9 apenas para efeitos de teste. O IoT Edge não é atualmente suportado em dispositivos ARM32 do Windows.

   É importante utilizar o script PowerShell a partir do mesmo lançamento que o ficheiro .cab que utiliza porque a funcionalidade muda para suportar as funcionalidades em cada versão.

3. Se o ficheiro .cab que descarregou tiver um sufixo de arquitetura, mude o nome do ficheiro para apenas **Microsoft-Azure-IoTEdge.cab**.

4. Opcionalmente, baixe um instalador para Visual C++ redistribuível. Por exemplo, o script PowerShell utiliza esta versão: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Guarde o instalador na mesma pasta no seu dispositivo IoT que os ficheiros IoT Edge.

5. Para instalar com componentes offline, [faça o ponto de origem](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) da cópia local do script PowerShell. Em seguida, `-OfflineInstallationPath` use o parâmetro `Deploy-IoTEdge` como parte do comando e forneça o caminho absoluto para o diretório de ficheiros. Por exemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   O comando de implantação utilizará todos os componentes encontrados no diretório de ficheiros local fornecido. Se faltar o ficheiro .cab ou o instalador Visual C++, tentará descarregá-los.

6. Execute `Initialize-IoTEdge` o comando para fornecer o seu dispositivo com uma identidade no IoT Hub. Ou fornece uma cadeia de ligação do dispositivo para o fornecimento manual, ou escolha um dos métodos descritos na secção de [fornecimento automática](#option-2-install-and-automatically-provision) anterior.

   Se o seu dispositivo `Deploy-IoTEdge`recomeçar depois de executar, volte `Initialize-IoTEdge`a ligar o script PowerShell antes de ser reiniciado .

Para mais informações sobre a opção de instalação offline, avance para saber sobre [todos os parâmetros](#all-installation-parameters)de instalação .

## <a name="verify-successful-installation"></a>Verificar instalação bem sucedida

Verifique o estado do serviço IoT Edge. Deve ser listado como correndo.  

```powershell
Get-Service iotedge
```

Examine os registos de serviço dos últimos 5 minutos. Se terminar de instalar o tempo de funcionamento do IoT Edge, poderá ver uma lista de erros do momento entre executar **Deploy-IoTEdge** e **Initialize-IoTEdge**. Estes erros são esperados, uma vez que o serviço está a tentar começar antes de ser configurado.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Faça uma verificação automatizada para os erros de configuração e de rede mais comuns.

```powershell
iotedge check
```

Lista de módulos de execução. Depois de uma nova instalação, o único módulo que deve ver a correr é **o EdgeAgent**. Depois de implementar pela primeira vez [módulos IoT Edge,](how-to-deploy-modules-portal.md) o outro módulo de sistema, **edgeHub,** também começará no dispositivo.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gerir contentores de módulos

O serviço IoT Edge requer um motor de contentor a funcionar no seu dispositivo. Quando se implanta um módulo num dispositivo, o tempo de funcionamento do IoT Edge utiliza o motor do recipiente para retirar a imagem do recipiente de um registo na nuvem. O serviço IoT Edge permite interagir com os seus módulos e recuperar registos, mas às vezes pode querer utilizar o motor do recipiente para interagir com o próprio recipiente.

Para obter mais informações sobre os conceitos de módulos, consulte [módulos De Borda De IoT Do Min.](iot-edge-modules.md)

Se estiver a executar recipientes Windows no seu dispositivo Windows IoT Edge, a instalação IoT Edge incluiu o motor de contentorMoby. O motor Moby baseou-se nos mesmos padrões que o Docker, e foi concebido para funcionar paralelamente na mesma máquina que o Docker Desktop. Por essa razão, se quiser atingir contentores geridos pelo motor Moby, tem de direcionar especificamente esse motor em vez do Docker.

Por exemplo, para listar todas as imagens do Docker, use o seguinte comando:

```powershell
docker images
```

Para listar todas as imagens Moby, modifique o mesmo comando com um ponteiro para o motor Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

O motor URI está listado na saída do script de instalação, ou pode encontrá-lo na secção de definições de tempo de funcionamento do recipiente para o ficheiro config.yaml.

![moby_runtime uri em config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Para obter mais informações sobre comandos pode utilizar para interagir com recipientes e imagens em execução no seu dispositivo, consulte [as interfaces da linha de comando docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Desinstalar ioT edge

Se pretender remover a instalação IoT Edge do seu dispositivo Windows, utilize o seguinte comando a partir de uma janela administrativa da PowerShell. Este comando remove o tempo de execução do IoT Edge, juntamente com a sua configuração existente e os dados do motor Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

O comando Uninstall-IoTEdge não funciona no Núcleo IoT do Windows IoT. Para remover o IoT Edge dos dispositivos Core Windows IoT, é necessário reutilizar a sua imagem Core Windows IoT.

Para mais informações sobre opções `Get-Help Uninstall-IoTEdge -full`de desinstalação, utilize o comando .

## <a name="verify-installation-script"></a>Verificar o script de instalação

Os comandos de instalação fornecidos neste artigo utilizam o cmdlet `aka.ms/iotedge-win`Invoke-WebRequest para solicitar o script de instalação de . Este link aponta`IoTEdgeSecurityDaemon.ps1` para o script a partir do mais recente [lançamento do IoT Edge](https://github.com/Azure/azure-iotedge/releases). Também pode descarregar este script, ou uma versão do script a partir de um lançamento específico, para executar os comandos de instalação no seu dispositivo IoT Edge.

O guião fornecido é assinado para aumentar a segurança. Pode verificar a assinatura descarregando o script para o seu dispositivo e executando o seguinte comando PowerShell:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

O estado de saída é **válido** se a assinatura for verificada.

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As secções anteriores introduziram cenários comuns de instalação com exemplos de como utilizar parâmetros para modificar o script de instalação. Esta secção fornece tabelas de referência dos parâmetros comuns utilizados para instalar, atualizar ou desinstalar o IoT Edge.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

O comando Deploy-IoTEdge descarrega e implanta o IoT Edge Security Daemon e as suas dependências. O comando de implantação aceita estes parâmetros comuns, entre outros. Para a lista completa, `Get-Help Deploy-IoTEdge -full`utilize o comando .  

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Contentores** | **Janelas** ou **Linux** | Se não for especificado o sistema operativo do contentor, o Windows é o valor predefinido.<br><br>Para os recipientes Windows, o IoT Edge utiliza o motor de contentor moby incluído na instalação. Para os recipientes Linux, é necessário instalar um motor de contentor antes de iniciar a instalação. |
| **Proxy** | Proxy URL | Inclua este parâmetro se o seu dispositivo precisar de passar por um servidor proxy para chegar à internet. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Caminho de Instalação Offline** | Caminho do diretório | Se este parâmetro estiver incluído, o instalador verificará o diretório listado para os ficheiros MSI da cabina IoT Edge e VC Runtime MSI necessários para a instalação. Quaisquer ficheiros não encontrados no diretório são descarregados. Se ambos os ficheiros estiverem no diretório, pode instalar o IoT Edge sem uma ligação à Internet. Também pode utilizar este parâmetro para utilizar uma versão específica. |
| **InvocarWebRequestParameters** | Hashtable de parâmetros e valores | Durante a instalação, são feitos vários pedidos web. Utilize este campo para definir parâmetros para esses pedidos web. Este parâmetro é útil para configurar credenciais para servidores proxy. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Reiniciar ifNeeded** | nenhuma | Esta bandeira permite que o script de implantação reinicie a máquina sem solicitação, se necessário. |

### <a name="initialize-iotedge"></a>Inicializar-IoTEdge

O comando Initialize-IoTEdge configura ioT Edge com a corda de ligação do dispositivo e detalhes operacionais. Grande parte da informação gerada por este comando é então armazenada no ficheiro iotedge\config.yaml. O comando de inicialização aceita estes parâmetros comuns, entre outros. Para a lista completa, `Get-Help Initialize-IoTEdge -full`utilize o comando .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhuma | **Mudar o parâmetro**. Se não for especificado o tipo de provisionamento, o manual é o valor predefinido.<br><br>Declara que irá fornecer uma cadeia de ligação ao dispositivo para fornecer o dispositivo manualmente |
| **Dps** | Nenhuma | **Mudar o parâmetro**. Se não for especificado o tipo de provisionamento, o manual é o valor predefinido.<br><br>Declara que fornecerá um Id de âmbito do Serviço de Fornecimento de Dispositivos (DPS) e o ID de Registo do seu dispositivo para fornecer através do DPS.  |
| **String de conexão de dispositivos** | Uma cadeia de ligação de um dispositivo IoT Edge registado num Hub IoT, em cotações individuais | **Necessário** para o fornecimento manual. Se não fornecer uma corda de ligação nos parâmetros do script, será solicitado para um. |
| **Scopeid** | Um id de âmbito de uma instância do Serviço de Provisionamento de Dispositivos associado ao seu Hub IoT. | **Necessário** para o fornecimento de DPS. Se não fornecer uma identificação de âmbito nos parâmetros do script, será solicitado para um. |
| **RegistroId** | Um ID de registo gerado pelo seu dispositivo | **Necessário** para o fornecimento de DPS se utilizar TPM ou atestado de chave simétrica. **Opcional** se utilizar o atestado de certificado X.509. |
| **X509IdentityCertificate** | O caminho URI para o certificado de identidade do dispositivo X.509 no dispositivo. | **Necessário** para o fornecimento de DPS se utilizar o atestado x.509 do certificado. |
| **X509IdentityPrivateKey** | O caminho URI para a chave de certificado de identidade do dispositivo X.509 no dispositivo. | **Necessário** para o fornecimento de DPS se utilizar o atestado x.509 do certificado. |
| **Chave Simétrica** | A chave simétrica utilizada para fornecer a identidade do dispositivo IoT Edge ao utilizar DPS | **Necessário** para o fornecimento de DPS se utilizar o atestado da chave simétrica. |
| **Contentores** | **Janelas** ou **Linux** | Se não for especificado o sistema operativo do contentor, o Windows é o valor predefinido.<br><br>Para os recipientes Windows, o IoT Edge utiliza o motor de contentor moby incluído na instalação. Para os recipientes Linux, é necessário instalar um motor de contentor antes de iniciar a instalação. |
| **InvocarWebRequestParameters** | Hashtable de parâmetros e valores | Durante a instalação, são feitos vários pedidos web. Utilize este campo para definir parâmetros para esses pedidos web. Este parâmetro é útil para configurar credenciais para servidores proxy. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | IoT Edge agente imagem URI | Por predefinição, uma nova instalação IoT Edge utiliza a mais recente etiqueta de rolamento para a imagem do agente IoT Edge. Utilize este parâmetro para definir uma etiqueta específica para a versão de imagem ou para fornecer a sua própria imagem de agente. Para mais informações, consulte [As tags De Borda IoT](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de utilizador** | Nome de utilizador do registo de contentores | Utilize este parâmetro apenas se definir o parâmetro -AgentImage para um recipiente num registo privado. Forneça um nome de utilizador com acesso ao registo. |
| **Palavra-passe** | Cadeia de palavra-passe segura | Utilize este parâmetro apenas se definir o parâmetro -AgentImage para um recipiente num registo privado. Forneça a senha para aceder ao registo. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Contentores** | **Janelas** ou **Linux** | Se não for especificado o SISTEMA de contentores, o Windows é o valor predefinido. Para os recipientes Windows, um motor de contentor será incluído na instalação. Para os recipientes Linux, é necessário instalar um motor de contentor antes de iniciar a instalação. |
| **Proxy** | Proxy URL | Inclua este parâmetro se o seu dispositivo precisar de passar por um servidor proxy para chegar à internet. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvocarWebRequestParameters** | Hashtable de parâmetros e valores | Durante a instalação, são feitos vários pedidos web. Utilize este campo para definir parâmetros para esses pedidos web. Este parâmetro é útil para configurar credenciais para servidores proxy. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Caminho de Instalação Offline** | Caminho do diretório | Se este parâmetro estiver incluído, o instalador verificará o diretório listado para os ficheiros MSI da cabina IoT Edge e VC Runtime MSI necessários para a instalação. Quaisquer ficheiros não encontrados no diretório são descarregados. Se ambos os ficheiros estiverem no diretório, pode instalar o IoT Edge sem uma ligação à Internet. Também pode utilizar este parâmetro para utilizar uma versão específica. |
| **Reiniciar ifNeeded** | nenhuma | Esta bandeira permite que o script de implantação reinicie a máquina sem solicitação, se necessário. |

### <a name="uninstall-iotedge"></a>Desinstalar-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Força** | nenhuma | Esta bandeira obriga à desinstalação caso a tentativa anterior de desinstalar não tenha sido bem sucedida.
| **Reiniciar ifNeeded** | nenhuma | Esta bandeira permite que o script desinstalado reinicie a máquina sem solicitação, se necessário. |

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge aprovisionado com o tempo de funcionar instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver com problemas em instalar o IoT Edge corretamente, consulte a página de resolução de [problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o daemon de segurança IoT Edge e o tempo](how-to-update-iot-edge.md)de execução .

---
title: Instalar Azure IoT Edge / Microsoft Docs
description: Instruções de instalação do Azure IoT Edge em dispositivos Windows ou Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: a7794bcdfa4f82698fdc5875bc94dcf52b70166e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185101"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>Instale ou desinstale o tempo de execução do Azure IoT Edge

O tempo de execução Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de funcionaamento pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grande como um servidor industrial. Quando um dispositivo é configurado com o runtime do IoT Edge, pode começar a implementar a lógica de negócio no mesmo partir da cloud. Para saber mais, consulte [o tempo de execução Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Existem dois passos para a criação de um dispositivo IoT Edge. O primeiro passo é instalar o tempo de funcionação e as suas dependências, que são abrangidas por este artigo. O segundo passo é ligar o dispositivo à sua identidade na nuvem e configurar a autenticação com o IoT Hub. Esses passos estão nos próximos artigos.

Este artigo lista os passos para instalar o tempo de funcionamento do Azure IoT Edge em dispositivos Linux ou Windows. Para dispositivos Windows, tem uma escolha adicional de utilizar recipientes Linux ou recipientes Windows. Atualmente, os contentores windows no Windows são recomendados para cenários de produção. Os contentores Linux no Windows são úteis para cenários de desenvolvimento e teste, especialmente se estiver a desenvolver num PC windows para se implantar em dispositivos Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para obter as informações mais recentes sobre quais os sistemas operativos que são atualmente suportados para cenários de produção, consulte [os sistemas suportados Azure IoT Edge](support.md#operating-systems)

# <a name="linux"></a>[Linux](#tab/linux)

Tenha um dispositivo X64, ARM32 ou ARM64 Linux. A Microsoft fornece pacotes de instalação para sistemas operativos Ubuntu Server 16.04, Ubuntu Server 18.04 e Raspbian Stretch.

>[!NOTE]
>O suporte para dispositivos ARM64 está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Prepare o seu dispositivo para aceder aos pacotes de instalação da Microsoft.

1. Instale a configuração do repositório que corresponda ao sistema operativo do dispositivo.

   * **Servidor Ubuntu 16.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Servidor Ubuntu 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Estiramento de Framboesa Pi OS**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Copie a lista gerada para o diretório sources.list.d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Instale a chave pública Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Versão do Windows

IoT Edge com recipientes Windows requer a versão 1809/build 17762 do Windows, que é a mais recente [construção de suporte](/windows/release-information/)a longo prazo do Windows. Para cenários de desenvolvimento e teste, qualquer SKU (Pro, Enterprise, Server, etc.) que suporte a funcionalidade de contentores funcionará. No entanto, certifique-se de rever a lista de sistemas suportados antes de ir para a produção.

IoT Edge com recipientes Linux pode funcionar em qualquer versão do Windows que satisfaça os [requisitos para o Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

### <a name="container-engine-requirements"></a>Requisitos do motor do contentor

O Azure IoT Edge conta com um motor de contentor [compatível com OCI.](https://www.opencontainers.org/) Certifique-se de que o seu dispositivo pode suportar recipientes.

Se estiver a instalar o IoT Edge numa máquina virtual, ative a virtualização aninhada e aloque pelo menos 2 GB de memória. Para o Hyper-V, as máquinas virtuais de geração 2 têm uma virtualização aninhada ativada por padrão. Para o VMware, existe um alternância para ativar a funcionalidade na sua máquina virtual.

Se estiver a instalar o IoT Edge num dispositivo IoT Core, utilize o seguinte comando numa [sessão remota do PowerShell](/windows/iot-core/connect-your-device/powershell) para verificar se os recipientes do Windows são suportados no seu dispositivo:

```powershell
Get-Service vmcompute
```

---

Os pacotes de software Azure IoT Edge estão sujeitos aos termos de licença localizados em cada pacote `usr/share/doc/{package-name}` (ou `LICENSE` diretório). Leia os termos da licença antes de utilizar uma embalagem. A sua instalação e utilização de um pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize esse pacote.

## <a name="install-a-container-engine"></a>Instale um motor de contentores

O Azure IoT Edge conta com um tempo de funcionamento do recipiente compatível com OCI. Para cenários de produção, recomendamos que use o motor baseado em Moby. O motor Moby é o único motor de contentores oficialmente suportado com Azure IoT Edge. As imagens do contentor Estivador CE/EE são compatíveis com o tempo de execução do Moby.

# <a name="linux"></a>[Linux](#tab/linux)

Atualize as listas de pacotes no seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale o motor Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Se tiver erros ao instalar o motor de contentor moby, verifique o seu kernel Linux para compatibilidade moby. Alguns fabricantes de dispositivos incorporados enviam imagens de dispositivos que contêm núcleos Linux personalizados sem as características necessárias para a compatibilidade do motor do contentor. Executar o seguinte comando, que utiliza o [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) fornecido pela Moby, para verificar a sua configuração do kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Na saída do script, verifique se todos os itens `Generally Necessary` `Network Drivers` estão ativados. Se faltar funcionalidades, ative-as reconstruindo o seu núcleo a partir da fonte e selecionando os módulos associados para inclusão no núcleo apropriado .config. Da mesma forma, se estiver a utilizar um gerador de configuração de núcleo como `defconfig` ou , encontre e ative as `menuconfig` respetivas funcionalidades e reconstrua o seu núcleo em conformidade. Depois de ter implantado o seu kernel recém-modificado, volte a executar o script check-config para verificar se todas as funcionalidades necessárias foram ativadas com sucesso.

# <a name="windows"></a>[Windows](#tab/windows)

Para cenários de produção, utilize o motor baseado em Moby que está incluído no script de instalação. Não há passos adicionais para instalar o motor.

Para ioT Edge com recipientes Linux, você precisa fornecer o seu próprio tempo de funcionação do recipiente. Instale [o Docker Desktop](https://docs.docker.com/docker-for-windows/install/) no seu dispositivo e configuure-o para utilizar [recipientes Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) antes de continuar.

---

## <a name="install-the-iot-edge-security-daemon"></a>Instale o daemon de segurança IoT Edge

O daemon de segurança IoT Edge fornece e mantém os padrões de segurança no dispositivo IoT Edge. O daemon começa em cada bota e botas armadilhas do dispositivo iniciando o resto do tempo de execução IoT Edge.

Os passos nesta secção representam o processo típico de instalação da versão mais recente num dispositivo que tem ligação à Internet. Se precisar de instalar uma versão específica, como uma versão pré-lançamento, ou precisar de ser instalada offline, siga os passos [de instalação offline ou de versão específica](#offline-or-specific-version-installation) na secção seguinte.

# <a name="linux"></a>[Linux](#tab/linux)

Atualize as listas de pacotes no seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Verifique quais as versões do IoT Edge disponíveis.

   ```bash
   apt list -a iotedge
   ```

Se pretender instalar a versão mais recente do daemon de segurança, utilize o seguinte comando que também instala a versão mais recente do pacote **de sedos de libiothsm:**

   ```bash
   sudo apt-get install iotedge
   ```

Se pretender instalar uma versão específica do daemon de segurança, especifique a versão a partir da saída da lista apta. Especificar também a mesma versão para o pacote **de sedos de libiothsm,** que de outra forma iria instalar a sua versão mais recente. Por exemplo, o seguinte comando instala a versão mais recente da versão 1.0.8:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Se a versão que pretende instalar não estiver listada, siga os passos [de instalação offline ou de versão específica](#offline-or-specific-version-installation) na secção seguinte. Esta secção mostra-lhe como direcionar qualquer versão anterior do daemon de segurança IoT Edge ou lançar versões de candidatos.

# <a name="windows"></a>[Windows](#tab/windows)

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

---

Agora que o motor de contentores e o tempo de funcionaamento do IoT Edge estão instalados no seu dispositivo, está pronto para o próximo passo, que é registar o seu dispositivo com o IoT Hub e configurar o dispositivo com a sua identidade em nuvem e informações de autenticação.

Escolha o próximo artigo com base no tipo de autenticação que pretende utilizar:

* [A autenticação da chave simétrica](how-to-manual-provision-symmetric-key.md) é mais rápida para começar.
* [A autenticação do certificado X.509](how-to-manual-provision-x509.md) é mais segura para cenários de produção.

## <a name="offline-or-specific-version-installation"></a>Instalação de versão offline ou específica

Os passos nesta secção são para cenários não abrangidos pelas etapas de instalação padrão. Isto pode incluir:

* Instale ioT Edge enquanto offline
* Instale uma versão de candidato de lançamento
* No Windows, instale uma versão diferente da mais recente

# <a name="linux"></a>[Linux](#tab/linux)

Utilize os passos nesta secção se pretender instalar uma versão específica do tempo de execução Azure IoT Edge que não está disponível através `apt-get install` de . A lista de pacotes da Microsoft contém apenas um conjunto limitado de versões recentes e suas sub-versões, pelo que estes passos são para quem pretenda instalar uma versão mais antiga ou uma versão candidata ao lançamento.

Utilizando comandos curl, pode direcionar os ficheiros componentes diretamente do repositório IoT Edge GitHub.

<!-- TODO: Offline installation? -->

1. Navegue para as [versões Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)e encontre a versão de lançamento que pretende atingir.

2. Expandir a secção **Ativos** para esta versão.

3. Cada versão deve ter novos ficheiros para o daemon de segurança IoT Edge e para o hsmlib. Utilize os seguintes comandos para atualizar estes componentes.

   1. Encontre o ficheiro **libiothsm-std** que corresponde à arquitetura do seu dispositivo IoT Edge. Clique com o botão direito no link do ficheiro e copie o endereço de link.

   2. Utilize o link copiado no seguinte comando para instalar a versão do hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Encontre o ficheiro **iotedge** que corresponda à arquitetura do seu dispositivo IoT Edge. Clique com o botão direito no link do ficheiro e copie o endereço de link.

   4. Utilize o link copiado no seguinte comando para instalar a versão do daemon de segurança IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

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

---

Agora que o motor de contentores e o tempo de funcionaamento do IoT Edge estão instalados no seu dispositivo, está pronto para o próximo passo, que é [autenticar um dispositivo IoT Edge no IoT Hub](how-to-manual-provision-symmetric-key.md).

## <a name="uninstall-iot-edge"></a>Desinstalar ioT Edge

Se pretender remover a instalação IoT Edge do seu dispositivo, utilize os seguintes comandos.

# <a name="linux"></a>[Linux](#tab/linux)

Remova o runtime do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando o tempo de funcionaamento do IoT Edge é removido, quaisquer recipientes que tenha criado são parados mas ainda existem no seu dispositivo. Veja todos os recipientes para ver quais permanecem.

```bash
sudo docker ps -a
```

Elimine os recipientes do seu dispositivo, incluindo os dois recipientes de tempo de execução.

```bash
sudo docker rm -f <container name>
```

Por fim, retire o tempo de funcionaamento do recipiente do seu dispositivo.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

# <a name="windows"></a>[Windows](#tab/windows)

Se pretender remover a instalação IoT Edge do seu dispositivo Windows, utilize o comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) a partir de uma janela administrativa powerShell. Este comando remove o tempo de funcionamento do IoT Edge, juntamente com a configuração existente e os dados do motor Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

O `Uninstall-IoTEdge` comando não funciona no Windows IoT Core. Para remover o IoT Edge, é necessário recolocar a sua imagem Do Windows IoT Core.

Para obter mais informações sobre opções de desinstalação, utilize o comando `Get-Help Uninstall-IoTEdge -full` .

---

## <a name="next-steps"></a>Passos seguintes

Depois de instalar o tempo de funcionamento do IoT Edge, configuure o seu dispositivo para se ligar ao IoT Hub. Os seguintes artigos passam por registar um novo dispositivo na nuvem e, em seguida, fornecer ao dispositivo a sua identidade e informação de autenticação.

Escolha o próximo artigo com base no tipo de autenticação que pretende utilizar:

* **Chave simétrica**: Tanto o IoT Hub como o dispositivo IoT Edge têm uma cópia de uma chave segura. Quando o dispositivo se liga ao IoT Hub, verificam se as teclas coincidem. Este método de autenticação é mais rápido de começar, mas não tão seguro.

  [Configurar um dispositivo Azure IoT Edge com autenticação de chave simétrica](how-to-manual-provision-symmetric-key.md)

* **X.509 auto-assinado**: O dispositivo IoT Edge tem certificados de identidade X.509, e ioT Hub recebe a impressão digital dos certificados. Quando o dispositivo se liga ao IoT Hub, comparam o certificado com a sua impressão digital. Este método de autenticação é mais seguro e recomendado para cenários de produção.

  [Configurar um dispositivo Azure IoT Edge com autenticação de certificado X.509](how-to-manual-provision-x509.md)
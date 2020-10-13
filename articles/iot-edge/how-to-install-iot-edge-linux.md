---
title: Instale a borda Azure IoT no Linux Microsoft Docs
description: Instruções de instalação do Azure IoT Edge em dispositivos Linux que executam Ubuntu ou Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: kgremban
ms.openlocfilehash: 21fde76dc5791030a7afa280e00642119cbe464c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660034"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Instalar o runtime do Azure IoT Edge em sistemas Linux baseados em Debian

O tempo de execução Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de funcionaamento pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grande como um servidor industrial. Quando um dispositivo é configurado com o runtime do IoT Edge, pode começar a implementar a lógica de negócio no mesmo partir da cloud. Para saber mais, consulte [o tempo de execução Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para instalar o tempo de funcionamento do Azure IoT Edge num dispositivo Linux X64, ARM32 ou ARM64. Fornecemos pacotes de instalação para Ubuntu Server 16.04, Ubuntu Server 18.04 e Raspbian Stretch. Consulte os [sistemas suportados Azure IoT Edge](support.md#operating-systems) para obter uma lista de sistemas operativos e arquiteturas linux suportados.

> [!NOTE]
> Os pacotes nos repositórios de software Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/*nome do pacote).* Leia os termos da licença antes de utilizar a embalagem. A sua instalação e utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize a embalagem.

## <a name="install-container-runtime-and-iot-edge"></a>Instale o tempo de funcionação do contentor e o IoT Edge

Utilize as seguintes secções para instalar a versão mais recente do tempo de funcionamento do Azure IoT Edge no seu dispositivo.

>[!NOTE]
>O suporte para dispositivos ARM64 está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registar a chave da Microsoft e o feed de repositório de software

Prepare o seu dispositivo para a instalação de tempo de execução IoT Edge.

Instale a configuração do repositório. Escolha o comando **16.04** ou **18.04** que corresponde ao sistema operativo do seu dispositivo:

* **Servidor Ubuntu 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Servidor Ubuntu 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Estirpe raspbiana:**

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copie a lista gerada.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instale a chave pública do Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Instalar um runtime de contentor

O Azure IoT Edge conta com um tempo de funcionamento do recipiente [compatível com OCI.](https://www.opencontainers.org/) Para cenários de produção, recomendamos que use o motor [baseado em Moby](https://mobyproject.org/) fornecido abaixo. O motor Moby é o único motor de contentores oficialmente suportado com Azure IoT Edge. As imagens do contentor Estivador CE/EE são compatíveis com o tempo de execução do Moby.

Atualize as listas de pacotes no seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale o motor Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Instale a interface de linha de comando Moby (CLI). O CLI é útil para o desenvolvimento, mas opcional para implantações de produção.

   ```bash
   sudo apt-get install moby-cli
   ```

Se tiver erros ao instalar o tempo de funcionamento do contentor Moby, siga os passos para Verificar o [seu kernel Linux para compatibilidade moby](#verify-your-linux-kernel-for-moby-compatibility), fornecido mais tarde neste artigo.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instale o Azure IoT Edge Security Daemon

O **daemon de segurança IoT Edge** fornece e mantém os padrões de segurança no dispositivo IoT Edge. O daemon começa em cada bota e botas armadilhas do dispositivo iniciando o resto do tempo de execução IoT Edge.

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

Se a versão que pretende instalar não estiver listada, siga os passos no [tempo de funcionação da Instalação utilizando os ativos de lançamento](#install-runtime-using-release-assets). Esta secção mostra-lhe como direcionar qualquer versão anterior do daemon de segurança IoT Edge ou lançar versões de candidatos.

Uma vez instalado com sucesso o IoT `/etc/iotedge/` Edge, a saída solicita-lhe que atualize o ficheiro de configuração. Continue até à secção seguinte para completar o fornecimento do dispositivo.

## <a name="configure-the-security-daemon"></a>Configure o daemon de segurança

Configure o tempo de execução IoT Edge para ligar o seu dispositivo físico a uma identidade do dispositivo que existe num hub Azure IoT.

O daemon pode ser configurado utilizando o ficheiro de configuração em `/etc/iotedge/config.yaml` . O ficheiro está protegido por padrão por defeito, podendo precisar de permissões elevadas para editá-lo.

Um único dispositivo IoT Edge pode ser fornecido manualmente utilizando uma cadeia de ligações do dispositivo fornecida pelo IoT Hub. Ou, pode utilizar o Serviço de Provisionamento de Dispositivos para provisões automáticas, o que é útil quando tem muitos dispositivos para o fornecimento. Dependendo da sua escolha de provisionamento, escolha o roteiro de instalação apropriado.

### <a name="option-1-manual-provisioning"></a>Opção 1: Provisionamento manual

Para fornecer manualmente um dispositivo, é necessário fornecê-lo com uma [cadeia de ligação](how-to-register-device.md#register-in-the-azure-portal) do dispositivo que pode criar registando um novo dispositivo no seu hub IoT.

Abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Encontre as configurações de provisionamento do ficheiro e descompromete a secção **de configuração manual de provisionamento.** Atualize o valor de **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge. Certifique-se de que quaisquer outras secções de provisionamento são comentadas. Certifique-se de que o **provisionamento: a** linha não tem espaço branco anterior e que os itens aninhados são recortados por dois espaços.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Para colar o conteúdo da prancheta em Nano `Shift+Right Click` ou premir `Shift+Insert` .

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Depois de introduzir as informações de provisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: Provisionamento automático

Os dispositivos IoT Edge podem ser automaticamente a provisionados utilizando o Serviço de Provisionamento de [Dispositivos Azure IoT Hub (DPS)](../iot-dps/index.yml). Atualmente, o IoT Edge suporta três mecanismos de atestado ao utilizar o fornecimento automático, mas os seus requisitos de hardware podem ter impacto nas suas escolhas. Por exemplo, os dispositivos Raspberry Pi não vêm com um chip de Módulo de Plataforma Fidedigna (TPM) por padrão. Para obter mais informações, veja os artigos seguintes:

* [Criar e providenciar um dispositivo IoT Edge com um TPM virtual num Linux VM](how-to-auto-provision-simulated-device-linux.md)
* [Criar e providenciar um dispositivo IoT Edge utilizando certificados X.509](how-to-auto-provision-x509-certs.md)
* [Criar e providenciar um dispositivo IoT Edge utilizando atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Esses artigos passam pela criação de inscrições em DPS, e gerando os certificados ou chaves adequados para o atestado. Independentemente do mecanismo de atestação escolhido, as informações de provisionamento são adicionadas ao ficheiro de configuração IoT Edge no seu dispositivo IoT Edge.

Abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Encontre as configurações de provisionamento do ficheiro e descompromete a secção adequada para o seu mecanismo de atestação. Certifique-se de que quaisquer outras secções de provisionamento são comentadas. O **provisionamento:** a linha não deve ter espaço branco anterior, e os itens aninhados devem ser recortados por dois espaços. Atualize o valor de **scope_id** com o valor da sua instância do Serviço de Provisionamento de Dispositivos IoT Hub e forneça os valores adequados para os campos de atestado.

Atestado TPM:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

Atestado X.509:

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Atestado de chave simétrica:

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

Para colar o conteúdo da prancheta em Nano `Shift+Right Click` ou premir `Shift+Insert` .

Guarde e feche o ficheiro. `CTRL + X`, `Y`, `Enter`

Depois de introduzir as informações de provisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifique a instalação bem sucedida

Se utilizar os passos **de configuração manual** na secção anterior, o tempo de execução IoT Edge deve ser a provisionado e executado com sucesso no seu dispositivo. Se utilizou os passos **de configuração automática,** então precisa de completar alguns passos adicionais para que o tempo de execução possa registar o seu dispositivo com o seu hub IoT em seu nome. Para os próximos passos, consulte [Criar e providenciar um dispositivo TPM IoT Edge simulado numa máquina virtual Linux.](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)

Pode verificar o estado do IoT Edge Daemon:

```bash
systemctl status iotedge
```

Examine os registos daemon:

```bash
journalctl -u iotedge --no-pager --no-full
```

Executar a [ferramenta de resolução de problemas](troubleshoot.md#run-the-check-command) para verificar se há erros de configuração e rede mais comuns:

```bash
sudo iotedge check
```

Até que implante o seu primeiro módulo para IoT Edge no seu dispositivo, o módulo de sistema **$edgeHub** não será implantado no dispositivo. Como resultado, a verificação automatizada devolverá um erro para a `Edge Hub can bind to ports on host` verificação de conectividade. Este erro pode ser ignorado a menos que ocorra depois de ter implantado um módulo no dispositivo.

Finalmente, listar os módulos de execução:

```bash
sudo iotedge list
```

Depois de instalar o IoT Edge no seu dispositivo, o único módulo que deve ver em funcionamento é **edgeAgent**. Assim que criar a sua primeira implantação, o outro módulo do sistema **$edgeHub** também irá iniciar-se no dispositivo. Para obter mais informações, consulte [os módulos IoT Edge.](how-to-deploy-modules-portal.md)

## <a name="tips-and-troubleshooting"></a>Sugestões e resolução de problemas

Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de instalar o tempo de funcionamento, inscreva-se na sua máquina e volte a entrar para atualizar automaticamente as suas permissões. Até lá, use **sudo** na frente de qualquer `iotedge` comando.

Nos dispositivos com restrições de recursos, recomenda-se vivamente que desempeciu a variável do ambiente *OptimizeForPerformance* para *falso,* de acordo com as instruções do [guia de resolução de problemas](troubleshoot.md).

Se o seu dispositivo não conseguir ligar-se ao IoT Hub e a sua rede tiver um servidor proxy, siga os passos no [Configure o seu dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifique o seu núcleo Linux para compatibilidade moby

Muitos fabricantes de dispositivos incorporados enviam imagens de dispositivos que contêm kernels Linux personalizados sem as funcionalidades necessárias para a compatibilidade do tempo de funcionamento do contentor. Se encontrar problemas durante a instalação do tempo de funcionamento recomendado do contentor Moby, poderá ser capaz de resolver problemas na sua configuração do kernel Linux utilizando o script [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) do [repositório](https://github.com/moby/moby)oficial do Moby GitHub . Executar os seguintes comandos no dispositivo para verificar a configuração do seu núcleo:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Este comando fornece uma saída detalhada que contém o estado das funcionalidades de kernel que são usadas pelo tempo de execução moby. Você vai querer garantir que todos os itens sob `Generally Necessary`  `Network Drivers` estão habilitados para garantir que o seu kernel é totalmente compatível com o tempo de execução Moby.  Se tiver identificado alguma característica em falta, ative-as reconstruindo o seu núcleo a partir da fonte e selecionando os módulos associados para inclusão no núcleo apropriado .config.  Da mesma forma, se estiver a utilizar um gerador de configuração de núcleo como `defconfig` ou , encontre e ative as `menuconfig` respetivas funcionalidades e reconstrua o seu núcleo em conformidade.  Depois de ter implantado o seu kernel recém-modificado, volte a executar o script check-config para verificar se todas as funcionalidades necessárias foram ativadas com sucesso.

## <a name="install-runtime-using-release-assets"></a>Instale o tempo de execução utilizando os ativos de libertação

Utilize os passos nesta secção se pretender instalar uma versão específica do tempo de execução Azure IoT Edge que não está disponível através `apt-get install` de . A lista de pacotes da Microsoft contém apenas um conjunto limitado de versões recentes e suas sub-versões, pelo que estes passos são para quem pretenda instalar uma versão mais antiga ou uma versão candidata ao lançamento.

Utilizando comandos curl, pode direcionar os ficheiros componentes diretamente do repositório IoT Edge GitHub. Utilize os seguintes passos para instalar o libiothsm e o daemon de segurança IoT Edge.

1. Prepare o seu dispositivo com um motor de contentores instalado. Se não tiver um motor de contentor, siga os passos para registar o repositório da Microsoft e instale o Moby na secção de tempo de funcionação do [contentor Install e IoT Edge](#install-container-runtime-and-iot-edge) deste artigo.

2. Navegue para as [versões Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)e encontre a versão de lançamento que pretende atingir.

3. Expandir a secção **Ativos** para esta versão.

4. Cada versão deve ter novos ficheiros para o daemon de segurança IoT Edge e para o hsmlib. Utilize os seguintes comandos para atualizar estes componentes.

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

Uma vez instalado com sucesso o IoT `/etc/iotedge` Edge, a saída solicita-lhe que atualize o ficheiro de configuração. Siga os passos na secção [Configurar a](#configure-the-security-daemon) secção de segurança para completar o provisionamento do dispositivo.

## <a name="uninstall-iot-edge"></a>Desinstalar ioT Edge

Se pretender remover a instalação IoT Edge do seu dispositivo Linux, utilize os seguintes comandos da linha de comando.

Remova o runtime do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando o tempo de funcionaamento do IoT Edge é removido, os recipientes que criou são parados mas ainda existem no seu dispositivo. Veja todos os recipientes para ver quais permanecem.

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

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge alojotado com o tempo de funcionaamento instalado, pode [implantar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se tiver problemas com a instalação correta do tempo de funcionamento do IoT Edge, consulte a página [de resolução de problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

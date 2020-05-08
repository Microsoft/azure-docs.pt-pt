---
title: Instale a borda Azure IoT no Linux / Microsoft Docs
description: Instruções de instalação azure IoT Edge em dispositivos Linux que executam Ubuntu ou Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: 947f224426b3a70c39cbf94ee888c5c353b3993b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857330"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Instalar o runtime do Azure IoT Edge em sistemas Linux baseados em Debian

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grandes como um servidor industrial. Uma vez configurado um dispositivo com o tempo de funcionamento do IoT Edge, pode começar a implementar a lógica do negócio a partir da nuvem. Para saber mais, consulte O tempo de funcionação do [Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para instalar o tempo de execução Do Limite Azure IoT num dispositivo X64, ARM32 ou ARM64 Linux. Fornecemos pacotes de instalação para Ubuntu Server 16.04, Ubuntu Server 18.04 e Raspbian Stretch. Consulte os [sistemas suportados pelo Azure IoT Edge](support.md#operating-systems) para obter uma lista de sistemas operativos linux suportados e arquiteturas.

> [!NOTE]
> Os pacotes nos repositórios de software Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/*nome de pacote).* Leia os termos da licença antes de utilizar o pacote. A sua instalação e utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize o pacote.

## <a name="install-the-latest-runtime-version"></a>Instale a versão mais recente do tempo de execução

Utilize as seguintes secções para instalar a versão mais recente do tempo de execução do Azure IoT Edge no seu dispositivo.

>[!NOTE]
>O suporte para dispositivos ARM64 está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registe a chave da Microsoft e o feed de repositório de software

Prepare o seu dispositivo para a instalação de tempo de execução IoT Edge.

Instale a configuração do repositório. Escolha o comando **16.04** ou **18.04** que corresponda ao sistema operativo do dispositivo:

* **Ubuntu Server 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04:**

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch:**

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copie a lista gerada.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instale a chave pública GPG da Microsoft

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Instale o tempo de execução do recipiente

O Azure IoT Edge baseia-se num tempo de execução compatível com o [OCI.](https://www.opencontainers.org/) Para cenários de produção, recomendamos que utilize o motor [baseado em Moby](https://mobyproject.org/) fornecido abaixo. O motor Moby é o único motor de contentor suportado oficialmente com O Edge Azure IoT. As imagens do contentor Docker CE/EE são compatíveis com o tempo de execução do Moby.

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

Se tiver erros ao instalar o tempo de execução do contentor Moby, siga os passos para verificar o [seu núcleo Linux para compatibilidade moby](#verify-your-linux-kernel-for-moby-compatibility), fornecido mais tarde neste artigo.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instale o Daemon de Segurança de Borda Azure IoT

O **daemon de segurança IoT Edge** fornece e mantém padrões de segurança no dispositivo IoT Edge. O daemon começa em cada bota e arranca o dispositivo iniciando o resto do tempo de execução ioT Edge.

O comando de instalação também instala a versão padrão do **libiotose** se ainda não estiver presente.

Atualize as listas de pacotes no seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale o daemon de segurança. A embalagem está `/etc/iotedge/`instalada a .

   ```bash
   sudo apt-get install iotedge
   ```

Assim que o IoT Edge for instalado com sucesso, a saída irá levar-lhe a atualizar o ficheiro de configuração. Siga os passos na secção Configurar a secção [da daemon de segurança](#configure-the-security-daemon) para completar o fornecimento do dispositivo.

## <a name="install-a-specific-runtime-version"></a>Instale uma versão específica do tempo de execução

Se pretender instalar uma versão específica do tempo de funcionamento do Moby e do Azure IoT Edge em vez de utilizar as versões mais recentes, pode direcionar os ficheiros dos componentes diretamente do repositório IoT Edge GitHub. Utilize os seguintes passos para colocar todos os componentes IoT Edge no seu dispositivo: o motor Moby e o CLI, o libiotosmo e, finalmente, o daemon de segurança IoT Edge. Passe para a secção seguinte, [Configure o daemon](#configure-the-security-daemon)de segurança, se não quiser mudar para uma versão específica do tempo de execução.

1. Navegue para os [lançamentos do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)e encontre a versão de lançamento que pretende atingir.

2. Expanda a secção **Ativos** para esta versão.

3. Pode ou não haver atualizações do motor Moby em qualquer desbloqueio. Se vir ficheiros que comecem com **moby-engine** e **moby-cli,** utilize os seguintes comandos para atualizar esses componentes. Se não vir nenhum ficheiro Moby, volte a ver os ativos de libertação mais antigos até encontrar a versão mais recente.

   1. Encontre o ficheiro **moby-engine** que corresponde à arquitetura do seu dispositivo IoT Edge. Clique no link do ficheiro e copie o endereço de link.

   2. Utilize o link copiado no seguinte comando para instalar aquela versão do motor Moby:

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Encontre o ficheiro **moby-cli** que corresponde à arquitetura do seu dispositivo IoT Edge. O Moby CLI é um componente opcional, mas pode ser útil durante o desenvolvimento. Clique no link do ficheiro e copie o endereço de link.

   4. Utilize o link copiado no seguinte comando para instalar aquela versão do Moby CLI:

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Cada lançamento deve ter novos ficheiros para o daemon de segurança IoT Edge e o hsmlib. Utilize os seguintes comandos para atualizar esses componentes.

   1. Encontre o ficheiro **libiothsm-std** que corresponde à arquitetura do seu dispositivo IoT Edge. Clique no link do ficheiro e copie o endereço de link.

   2. Utilize o link copiado no seguinte comando para instalar a versão do hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Encontre o ficheiro **iotedge** que combina com a arquitetura do seu dispositivo IoT Edge. Clique no link do ficheiro e copie o endereço de link.

   4. Utilize o link copiado no seguinte comando para instalar a versão do daemon de segurança IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Assim que o IoT Edge for instalado com sucesso, a saída irá levar-lhe a atualizar o ficheiro de configuração. Siga os passos na secção seguinte para completar o fornecimento do dispositivo.

## <a name="configure-the-security-daemon"></a>Configure o daemon de segurança

Configure o tempo de execução do IoT Edge para ligar o seu dispositivo físico a uma identidade de dispositivo que existe num hub Azure IoT.

O daemon pode ser configurado `/etc/iotedge/config.yaml`utilizando o ficheiro de configuração em . O ficheiro está protegido por predefinição, pode precisar de permissões elevadas para editá-lo.

Um único dispositivo IoT Edge pode ser fornecido manualmente utilizando uma cadeia de ligações de dispositivo fornecida pelo IoT Hub. Ou, pode utilizar o Serviço de Fornecimento de Dispositivos para fornecer automaticamente dispositivos, o que é útil quando tem muitos dispositivos para fornecer. Dependendo da sua escolha de provisionamento, escolha o script de instalação apropriado.

### <a name="option-1-manual-provisioning"></a>Opção 1: Disposição manual

Para fornecer manualmente um dispositivo, é necessário fornecê-lo com uma cadeia de [ligação](how-to-register-device.md#register-in-the-azure-portal) ao dispositivo que pode criar registando um novo dispositivo no seu hub IoT.

Abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Encontre as configurações de fornecimento do ficheiro e descodere a secção de configuração de **fornecimento manual.** Atualize o valor do **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge. Certifique-se de que quaisquer outras secções de fornecimento são comentadas. Certifique-se de que o **fornecimento:** a linha não tem espaço branco anterior e que os itens aninhados são retalhados por dois espaços.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Para colar o conteúdo da `Shift+Right Click` prancheta em Nano ou premir `Shift+Insert`.

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Depois de introduzir as informações de provisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: Fornecimento automático

Os dispositivos IoT Edge podem ser automaticamente aprovisionados utilizando o Serviço de Provisionamento de [Dispositivos Hub Azure IoT (DPS)](../iot-dps/index.yml). Atualmente, o IoT Edge suporta dois mecanismos de atestado ao utilizar o fornecimento automático, mas os seus requisitos de hardware podem ter impacto nas suas escolhas. Por exemplo, os dispositivos Raspberry Pi não vêm com um chip De Módulo de Plataforma Fidedigna (TPM) por padrão. Para obter mais informações, veja os artigos seguintes:

* [Crie e forre um dispositivo IoT Edge com um TPM virtual em um VM Linux](how-to-auto-provision-simulated-device-linux.md)
* [Criar e fornecer um dispositivo IoT Edge utilizando certificados X.509](how-to-auto-provision-x509-certs.md)
* [Criar e fornecer um dispositivo IoT Edge utilizando atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Esses artigos passam por você através da criação de inscrições em DPS, e gerando os certificados ou chaves adequados para o atestado. Independentemente do mecanismo de atestado que escolher, a informação de fornecimento é adicionada ao ficheiro de configuração IoT Edge no seu dispositivo IoT Edge.

Abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Encontre as configurações de fornecimento do ficheiro e não comente a secção adequada para o seu mecanismo de atestado. Certifique-se de que quaisquer outras secções de fornecimento são comentadas. O **fornecimento:** a linha não deve ter espaço branco anterior, e os itens aninhados devem ser reiniciados por dois espaços. Atualize o valor da **scope_id** com o valor da sua instância de Serviço de Provisionamento de Dispositivos IoT Hub e forneça os valores adequados para os campos de atestado.

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

X.509 atestado:

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

Atestação de chaves simétricas:

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

Para colar o conteúdo da `Shift+Right Click` prancheta em Nano ou premir `Shift+Insert`.

Guarde e feche o ficheiro. `CTRL + X`, `Y`, `Enter`

Depois de introduzir as informações de provisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificar instalação bem sucedida

Se utilizou os passos de **configuração manual** na secção anterior, o tempo de funcionamento do IoT Edge deve ser aprovisionado e executado com sucesso no seu dispositivo. Se utilizou os passos de **configuração automática,** então precisa de completar alguns passos adicionais para que o tempo de funcionamento possa registar o seu dispositivo com o seu hub IoT em seu nome. Para os próximos passos, consulte [Criar e fornecer um dispositivo TPM IoT Edge simulado numa máquina virtual Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Pode verificar o estado do Daemon IoT Edge:

```bash
systemctl status iotedge
```

Examine os registos daemon:

```bash
journalctl -u iotedge --no-pager --no-full
```

Executar a ferramenta de resolução de [problemas](troubleshoot.md#run-the-check-command) para verificar se há erros de configuração e rede mais comuns:

```bash
sudo iotedge check
```

Até que implemente o seu primeiro módulo para IoT Edge no seu dispositivo, o módulo **de $edgeHub** do sistema não será implantado no dispositivo. Como resultado, a verificação automatizada `Edge Hub can bind to ports on host` devolverá um erro para a verificação de conectividade. Este erro pode ser ignorado a menos que ocorra após a implementação de um módulo para o dispositivo.

Finalmente, lista ruminar módulos de execução:

```bash
sudo iotedge list
```

Depois de instalar o IoT Edge no seu dispositivo, o único módulo que deve ver a correr é **o EdgeAgent**. Assim que criar a sua primeira implementação, o outro módulo de sistema **$edgeHub** também começará no dispositivo. Para mais informações, consulte a implementação de [módulos IoT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Sugestões e resolução de problemas

Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de instalar o tempo de funcionamento, assine a sua máquina e volte a ligar as suas permissões automaticamente. Até lá, use **o** `iotedge` sudo na frente de qualquer comando.

Em dispositivos limitados de recursos, é altamente recomendável que este ajuste a variável ambiente *OptimizeForPerformance* para *falso* de acordo com as instruções no guia de resolução de [problemas](troubleshoot.md).

Se a sua rede que tem um servidor proxy, siga os passos no [Configure o seu dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifique o seu kernel Linux para obter compatibilidade com Moby

Muitos fabricantes de dispositivos incorporados enviam imagens de dispositivos que contêm núcleos linux personalizados sem as funcionalidades necessárias para a compatibilidade do tempo de execução do recipiente. Se encontrar problemas durante a instalação do tempo de funcionamento recomendado do recipiente Moby, poderá ser capaz de resolver problemas com a configuração do kernel Linux utilizando o script [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) do [repositório oficial Moby GitHub](https://github.com/moby/moby). Execute os seguintes comandos no dispositivo para verificar a sua configuração de kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Este comando fornece uma saída detalhada que contém o estado das características do núcleo que são usadas pelo tempo de execução de Moby. Você deve garantir que todos os `Generally Necessary` `Network Drivers` itens em baixo e estão habilitados para garantir que o seu núcleo é totalmente compatível com o tempo de funcionação de Moby.  Se tiver identificado alguma característica em falta, ative-as reconstruindo o seu núcleo a partir da fonte e selecionando os módulos associados para inclusão no kernel apropriado .config.  Da mesma forma, se estiver a `defconfig` utilizar `menuconfig`um gerador de configuração de núcleos como ou , encontre e ative as respetivas funcionalidades e reconstrua o seu núcleo em conformidade.  Depois de ter implantado o seu núcleo recém-modificado, volte a executar o script check-config para verificar se todas as funcionalidades necessárias foram ativadas com sucesso.

## <a name="uninstall-iot-edge"></a>Desinstalar ioT edge

Se pretender remover a instalação IoT Edge do seu dispositivo Linux, utilize os seguintes comandos da linha de comando.

Remova o runtime do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando o tempo de funcionação do IoT Edge é removido, os recipientes que criou são parados, mas ainda existem no seu dispositivo. Veja todos os recipientes para ver quais permanecem.

```bash
sudo docker ps -a
```

Elimine os recipientes do seu dispositivo, incluindo os dois recipientes de tempo de execução.

```bash
sudo docker rm -f <container name>
```

Por fim, retire o tempo de execução do recipiente do seu dispositivo.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge aprovisionado com o tempo de funcionar instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver a ter problemas com o tempo de funcionamento do IoT Edge a instalar corretamente, consulte a página de resolução de [problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o daemon de segurança IoT Edge e o tempo](how-to-update-iot-edge.md)de execução .

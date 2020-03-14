---
title: Instalar o Azure IoT Edge no Linux | Documentos da Microsoft
description: Instruções de instalação azure IoT Edge em dispositivos Linux que executam Ubuntu ou Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.openlocfilehash: af53dea76670be500e7be20063487e3e4a2177b6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79285087"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Instale o tempo de execução do Azure IoT Edge nos sistemas Linux baseados em Debian

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud. Para saber mais, consulte O tempo de funcionação do [Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para instalar o tempo de execução Do Limite Azure IoT num dispositivo X64, ARM32 ou ARM64 Linux. Fornecemos pacotes de instalação para Ubuntu Server 16.04, Ubuntu Server 18.04 e Raspbian Stretch. Consulte os [sistemas suportados pelo Azure IoT Edge](support.md#operating-systems) para obter uma lista de sistemas operativos linux suportados e arquiteturas.

> [!NOTE]
> Os pacotes nos repositórios de software Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/*nome de pacote).* Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

## <a name="install-the-latest-runtime-version"></a>Instale a versão mais recente do tempo de execução

Utilize as seguintes secções para instalar a versão mais recente do tempo de execução do Azure IoT Edge no seu dispositivo.

>[!NOTE]
>O suporte para dispositivos ARM64 está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registar o feed de repositório de chave e o software Microsoft

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

### <a name="install-the-container-runtime"></a>Instalar o runtime de contentor

O Azure IoT Edge baseia-se num tempo de execução compatível com o [OCI.](https://www.opencontainers.org/) Para cenários de produção, recomendamos que utilize o motor [baseado em Moby](https://mobyproject.org/) fornecido abaixo. O motor Moby é o único motor de contentor suportado oficialmente com O Edge Azure IoT. Imagens de contentor do docker CE/EE são compatíveis com o tempo de execução Moby.

Atualize as listas de pacotes no seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale o motor de Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Instale a interface de linha de comandos (CLI) de Moby. A CLI é útil para desenvolvimento mas opcional para implementações de produção.

   ```bash
   sudo apt-get install moby-cli
   ```

Se tiver erros ao instalar o tempo de execução do contentor Moby, siga os passos para verificar o [seu núcleo Linux para compatibilidade moby](#verify-your-linux-kernel-for-moby-compatibility), fornecido mais tarde neste artigo.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instale o Daemon de segurança do Azure IoT Edge

O **daemon de segurança IoT Edge** fornece e mantém padrões de segurança no dispositivo IoT Edge. O daemon começa em cada inicialização e inicializa o dispositivo ao iniciar o restante do runtime do IoT Edge.

O comando de instalação também instala a versão padrão do **libiotose** se ainda não estiver presente.

Atualize as listas de pacotes no seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale o daemon de segurança. A embalagem é instalada em `/etc/iotedge/`.

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

Configure o runtime do IoT Edge para ligar o dispositivo físico com uma identidade de dispositivo que existe num hub IoT do Azure.

O daemon pode ser configurado utilizando o ficheiro de configuração em `/etc/iotedge/config.yaml`. O ficheiro é protegida contra escrita por padrão, poderá ter permissões elevadas para editá-lo.

Um único dispositivo IoT Edge pode ser aprovisionado manualmente através de uma cadeia de ligações de dispositivo fornecida pelo IoT Hub. Em alternativa, pode utilizar o serviço de aprovisionamento de dispositivos para aprovisionar automaticamente os dispositivos, que é útil quando tem o número de dispositivos para aprovisionar. Dependendo de sua escolha de aprovisionamento, escolha o script de instalação apropriado.

### <a name="option-1-manual-provisioning"></a>Opção 1: De aprovisionamento Manual

Para fornecer manualmente um dispositivo, é necessário fornecê-lo com uma cadeia de [ligação](how-to-register-device.md#register-in-the-azure-portal) ao dispositivo que pode criar registando um novo dispositivo no seu hub IoT.

Abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Encontre as configurações de fornecimento do ficheiro e descodere a secção de configuração de **fornecimento manual.** Atualize o valor do **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge. Certifique-se de que quaisquer outras secções de fornecimento são comentadas. Certifique-se de que o **fornecimento:** a linha não tem espaço branco anterior e que os itens aninhados são retalhados por dois espaços.

   ```yaml
   # Manual provisioning configuration
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   attestation:
   #     method: "tpm"
   #     registration_id: "{registration_id}"
   ```

Para colar o conteúdo da prancheta em Nano `Shift+Right Click` ou pressione `Shift+Insert`.

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Após introduzir as informações de aprovisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: Aprovisionamento automático

Para fornecer automaticamente um dispositivo, crie o Serviço de Provisionamento de [Dispositivos e recupere o ID](how-to-auto-provision-simulated-device-linux.md)de registo do dispositivo . Existem uma série de mecanismos de atestado suportados pelo IoT Edge ao utilizar o fornecimento automático, mas os seus requisitos de hardware também impactam as suas escolhas. Por exemplo, os dispositivos Raspberry Pi não vêm com um chip De Módulo de Plataforma Fidedigna (TPM) por padrão.

Abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Encontre as configurações de fornecimento do ficheiro e não comente a secção adequada para o seu mecanismo de atestado. Ao utilizar o atestado TPM, por exemplo, atualize os valores de **scope_id** e **registration_id** com os valores do seu serviço de provisionamento de dispositivos IoT Hub e do seu dispositivo IoT Edge com TPM, respectivamente. Certifique-se de que o **fornecimento:** a linha não tem espaço branco anterior e que os itens aninhados são retalhados por dois espaços.

   ```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
   ```

Para colar o conteúdo da prancheta em Nano `Shift+Right Click` ou pressione `Shift+Insert`.

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Após introduzir as informações de aprovisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Se utilizou os passos de **configuração manual** na secção anterior, o tempo de funcionamento do IoT Edge deve ser aprovisionado e executado com sucesso no seu dispositivo. Se utilizou os passos de **configuração automática,** então precisa de completar alguns passos adicionais para que o tempo de funcionamento possa registar o seu dispositivo com o seu hub IoT em seu nome. Para os próximos passos, consulte [Criar e fornecer um dispositivo TPM IoT Edge simulado numa máquina virtual Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Pode verificar o estado do Daemon IoT Edge:

```bash
systemctl status iotedge
```

Examine os registos daemon:

```bash
journalctl -u iotedge --no-pager --no-full
```

Faça uma verificação automatizada para os erros de configuração e de rede mais comuns:

```bash
sudo iotedge check
```

Até que implemente o seu primeiro módulo para IoT Edge no seu dispositivo, o módulo **de $edgeHub** do sistema não será implantado no dispositivo. Como resultado, a verificação automatizada devolverá um erro para a verificação de conectividade `Edge Hub can bind to ports on host`. Este erro pode ser ignorado a menos que ocorra após a implementação de um módulo para o dispositivo.

Finalmente, lista ruminar módulos de execução:

```bash
sudo iotedge list
```

Depois de instalar o IoT Edge no seu dispositivo, o único módulo que deve ver a correr é **o EdgeAgent**. Assim que criar a sua primeira implementação, o outro módulo de sistema **$edgeHub** também começará no dispositivo. Para mais informações, consulte a implementação de [módulos IoT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Sugestões e resolução de problemas

Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de instalar o tempo de execução, inicie sessão fora do seu computador e inicie sessão novamente para atualizar automaticamente as suas permissões. Até lá, use **o sudo** na frente de qualquer `iotedge` comandos.

Em dispositivos limitados de recursos, é altamente recomendável que este ajuste a variável ambiente *OptimizeForPerformance* para *falso* de acordo com as instruções no guia de resolução de [problemas](troubleshoot.md).

Se a sua rede que tem um servidor proxy, siga os passos no [Configure o seu dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifique o seu kernel Linux para obter compatibilidade com Moby

Muitos fabricantes de dispositivos incorporados enviam imagens de dispositivos que contêm núcleos linux personalizados sem as funcionalidades necessárias para a compatibilidade do tempo de execução do recipiente. Se encontrar problemas durante a instalação do tempo de funcionamento recomendado do recipiente Moby, poderá ser capaz de resolver problemas com a configuração do kernel Linux utilizando o script [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) do [repositório oficial Moby GitHub](https://github.com/moby/moby). Execute os seguintes comandos no dispositivo para verificar a sua configuração de kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Este comando fornece uma saída detalhada que contém o estado das características do núcleo que são usadas pelo tempo de execução de Moby. Deverá certificar-se de que todos os itens sob `Generally Necessary` e `Network Drivers` estão habilitados para garantir que o seu núcleo é totalmente compatível com o tempo de funcionação de Moby.  Se tiver identificado alguma característica em falta, ative-as reconstruindo o seu núcleo a partir da fonte e selecionando os módulos associados para inclusão no kernel apropriado .config.  Da mesma forma, se estiver a utilizar um gerador de configuração de kernel como defconfig ou menuconfig, encontre e ative as respetivas funcionalidades e reconstrua o seu núcleo em conformidade.  Depois de ter implantado o seu núcleo recém-modificado, volte a executar o script check-config para verificar se todas as funcionalidades necessárias foram ativadas com sucesso.

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

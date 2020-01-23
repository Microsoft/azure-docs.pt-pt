---
title: Instalar o Azure IoT Edge no Linux | Documentos da Microsoft
description: Azure IoT Edge instruções de instalação em dispositivos Linux que executam o Ubuntu ou o Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.openlocfilehash: 4a8725e3ba7be2dc572798d1397e098046a4b352
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510231"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Instalar o tempo de execução de Azure IoT Edge em sistemas Linux baseados em Debian

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud. Para saber mais, confira [entender o tempo de execução de Azure IOT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o Azure IoT Edge Runtime em um dispositivo com x64, ARM32 ou ARM64 Linux. Os pacotes de instalação são fornecidos para o Ubuntu Server 16, 4, o Ubuntu Server 18, 4 e o Stretch Raspbian. Consulte [Azure IOT Edge sistemas com suporte](support.md#operating-systems) para obter uma lista de arquiteturas e sistemas operacionais Linux com suporte.

>[!NOTE]
>O suporte para dispositivos ARM64 está em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Pacotes em repositórios de software do Linux estão sujeita aos termos de licença localizados em cada pacote (/ usr/partilhar/doc/*nome do pacote*). Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

## <a name="install-the-latest-runtime-version"></a>Instalar a versão mais recente do tempo de execução

Use as seções a seguir para instalar a versão mais recente do Azure IoT Edge Runtime em seu dispositivo.

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registar o feed de repositório de chave e o software Microsoft

Prepare seu dispositivo para a instalação do IoT Edge Runtime.

Instale a configuração do repositório. Escolha o comando **16, 4** ou **18, 4** que corresponde ao sistema operacional do seu dispositivo:

* **Ubuntu Server 16, 4**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18, 4**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copie a lista gerada.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instalar a chave pública do Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Instalar o runtime de contentor

Azure IoT Edge se baseia em um tempo de execução de contêiner [compatível com o OCI](https://www.opencontainers.org/) . Para cenários de produção, recomendamos que você use o mecanismo [baseado em Moby](https://mobyproject.org/) fornecido abaixo. É o mecanismo de contentor único oficialmente suportado com o Azure IoT Edge. Imagens de contentor do docker CE/EE são compatíveis com o tempo de execução Moby.

Execute a atualização de apt.

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

Se você encontrar erros ao instalar o tempo de execução do contêiner Moby, siga as etapas para [verificar a compatibilidade do kernel do Linux para Moby](#verify-your-linux-kernel-for-moby-compatibility), fornecida mais adiante neste artigo.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instale o Daemon de segurança do Azure IoT Edge

O **daemon de segurança do IOT Edge** fornece e mantém os padrões de segurança no dispositivo IOT Edge. O daemon começa em cada inicialização e inicializa o dispositivo ao iniciar o restante do runtime do IoT Edge.

O comando de instalação também instala a versão padrão do **libiothsm** , se ainda não estiver presente.

Execute a atualização de apt.

   ```bash
   sudo apt-get update
   ```

Instale o daemon de segurança. O pacote está instalado em `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Quando IoT Edge for instalado com êxito, a saída solicitará que você atualize o arquivo de configuração. Siga as etapas na seção [Configurar o daemon de segurança](#configure-the-security-daemon) para concluir o provisionamento do seu dispositivo.

## <a name="install-a-specific-runtime-version"></a>Instalar uma versão de tempo de execução específica

Se você quiser instalar uma versão específica do Moby e o tempo de execução de Azure IoT Edge em vez de usar as versões mais recentes, você poderá direcionar os arquivos de componente diretamente do repositório GitHub IoT Edge. Use as etapas a seguir para obter todos os componentes de IoT Edge em seu dispositivo: o mecanismo Moby e a CLI, o libiothsm e, por fim, o daemon de segurança do IoT Edge. Pule para a próxima seção, [Configure o daemon de segurança](#configure-the-security-daemon), se você não quiser alterar para uma versão de tempo de execução específica.

1. Navegue até o [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases)e localize a versão de lançamento que você deseja direcionar. 

2. Expanda a seção **ativos** para essa versão.

3. Pode ou não haver atualizações para o mecanismo Moby em qualquer versão fornecida. Se você vir arquivos que começam com **Moby-Engine** e **Moby-CLI**, use os comandos a seguir para atualizar esses componentes. Se você não vir nenhum arquivo Moby, volte pelos ativos de lançamento mais antigos até encontrar a versão mais recente. 

   1. Localize o arquivo **Moby-Engine** que corresponde à arquitetura do dispositivo de IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   2. Use o link copiado no comando a seguir para instalar essa versão do mecanismo Moby: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Localize o arquivo **Moby-CLI** que corresponde à arquitetura do dispositivo IOT Edge. A CLI do Moby é um componente opcional, mas pode ser útil durante o desenvolvimento. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link. 

   4. Use o link copiado no comando a seguir para instalar essa versão da CLI do Moby: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Cada versão deve ter novos arquivos para o daemon de segurança IoT Edge e o hsmlib. Use os comandos a seguir para atualizar esses componentes. 

   1. Localize o arquivo **libiothsm-STD** que corresponde à arquitetura do dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link. 

   2. Use o link copiado no comando a seguir para instalar essa versão do hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Localize o arquivo **iotedge** que corresponde à arquitetura do dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link. 

   4. Use o link copiado no comando a seguir para instalar essa versão do daemon de segurança IoT Edge. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Quando IoT Edge for instalado com êxito, a saída solicitará que você atualize o arquivo de configuração. Siga as etapas na próxima seção para concluir o provisionamento do seu dispositivo. 

## <a name="configure-the-security-daemon"></a>Configurar o daemon de segurança

Configure o runtime do IoT Edge para ligar o dispositivo físico com uma identidade de dispositivo que existe num hub IoT do Azure.

O daemon de pode ser configurado com o ficheiro de configuração em `/etc/iotedge/config.yaml`. O ficheiro é protegida contra escrita por padrão, poderá ter permissões elevadas para editá-lo.

Um único dispositivo IoT Edge pode ser aprovisionado manualmente através de uma cadeia de ligações de dispositivo fornecida pelo IoT Hub. Em alternativa, pode utilizar o serviço de aprovisionamento de dispositivos para aprovisionar automaticamente os dispositivos, que é útil quando tem o número de dispositivos para aprovisionar. Dependendo de sua escolha de aprovisionamento, escolha o script de instalação apropriado.

### <a name="option-1-manual-provisioning"></a>Opção 1: De aprovisionamento Manual

Para aprovisionar manualmente um dispositivo, tem de fornecê-lo com uma [cadeia de ligação do dispositivo](how-to-register-device.md#register-in-the-azure-portal) que pode criar ao registar um novo dispositivo no IoT hub.

Abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize as configurações de provisionamento do arquivo e remova a marca de comentário da seção **configuração de provisionamento manual** . Atualize o valor de **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge. Verifique se qualquer outra seção de provisionamento foi comentada. Verifique se o **provisionamento:** linha não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços.

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

Para colar o conteúdo da área de transferência no nano `Shift+Right Click` ou pressione `Shift+Insert`.

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Após introduzir as informações de aprovisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: Aprovisionamento automático

Para aprovisionar automaticamente um dispositivo [configurar o serviço aprovisionamento de dispositivos e obter o seu ID de registo do dispositivo](how-to-auto-provision-simulated-device-linux.md). Há vários mecanismos de atestado com suporte pelo IoT Edge ao usar o provisionamento automático, mas os requisitos de hardware também afetam suas escolhas. Por exemplo, os dispositivos Raspberry Pi não vêm com um chip Trusted Platform Module (TPM) por padrão.

Abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize as configurações de provisionamento do arquivo e remova a marca de comentário da seção apropriada para seu mecanismo de atestado. Ao usar o atestado do TPM, por exemplo, atualize os valores de **scope_id** e **registration_id** com os valores do serviço de provisionamento de dispositivos do Hub IOT e seu dispositivo de IOT Edge com o TPM, respectivamente. Verifique se o **provisionamento:** linha não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços.

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

Para colar o conteúdo da área de transferência no nano `Shift+Right Click` ou pressione `Shift+Insert`.

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Após introduzir as informações de aprovisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Se tiver utilizado o **configuração manual** passos na secção anterior, o runtime do IoT Edge deve estar em execução no seu dispositivo e aprovisionada com êxito. Se tiver utilizado o **a configuração automática** passos, tem de concluir alguns passos adicionais para que o tempo de execução pode registar o seu dispositivo com o seu hub IoT em seu nome. Para as próximas etapas, consulte [criar e provisionar um dispositivo IOT Edge de TPM simulado em uma máquina virtual do Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Você pode verificar o status do daemon de IoT Edge:

```bash
systemctl status iotedge
```

Examinar os logs do daemon:

```bash
journalctl -u iotedge --no-pager --no-full
```

Execute uma verificação automatizada para os erros mais comuns de configuração e rede: 

```bash
sudo iotedge check
```

Até que você implante seu primeiro módulo para IoT Edge em seu dispositivo, o módulo do sistema **$edgeHub** não será implantado no dispositivo. Como resultado, a verificação automática retornará um erro para a verificação de conectividade de `Edge Hub can bind to ports on host`. Esse erro pode ser ignorado, a menos que ocorra após a implantação de um módulo no dispositivo.

Por fim, liste módulos em execução:

```bash
sudo iotedge list
```

Depois de instalar IoT Edge em seu dispositivo, o único módulo que você deve ver em execução é **edgeAgent**. Depois de criar sua primeira implantação, o outro módulo do sistema **$edgeHub** será iniciado no dispositivo também. Para obter mais informações, consulte [implantar módulos de IOT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Sugestões e resolução de problemas

Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de instalar o tempo de execução, inicie sessão fora do seu computador e inicie sessão novamente para atualizar automaticamente as suas permissões. Até lá, utilize **sudo** à frente de qualquer `iotedge` os comandos.

Em dispositivos de restrição de recursos, é altamente recomendável que defina os *OptimizeForPerformance* variável de ambiente para *false* de acordo com as instruções no [guia de resolução de problemas ](troubleshoot.md).

Se a sua rede que tenha um servidor proxy, siga os passos em [configurar o seu dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifique a compatibilidade do kernel do Linux para Moby

Muitos fabricantes de dispositivos incorporados enviam imagens de dispositivos que contêm kernels Linux personalizados sem os recursos necessários para a compatibilidade do tempo de execução do contêiner. Se você encontrar problemas ao instalar o tempo de execução do contêiner Moby recomendado, poderá ser capaz de solucionar problemas de configuração do kernel do Linux usando o script [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) do [repositório GitHub](https://github.com/moby/moby)oficial do Moby. Execute os seguintes comandos no dispositivo para verificar a configuração do kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Isso fornecerá uma saída detalhada que contém o status dos recursos de kernel usados pelo tempo de execução Moby. Você desejará garantir que todos os itens em `Generally Necessary` e `Network Drivers` estejam habilitados para garantir que o kernel seja totalmente compatível com o tempo de execução do Moby.  Se você tiver identificado quaisquer recursos ausentes, habilite-os recriando o kernel da origem e selecionando os módulos associados para inclusão no kernel. config apropriado.  Da mesma forma, se você estiver usando um gerador de configuração de kernel como defconfig ou menuconfig, localize e habilite os respectivos recursos e recompile o kernel de acordo.  Depois de implantar o kernel recém-modificado, execute o script check-config novamente para verificar se todos os recursos necessários foram habilitados com êxito.


## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Se você quiser remover a instalação do IoT Edge do seu dispositivo Linux, use os comandos a seguir na linha de comando.

Remova o runtime do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando o tempo de execução de IoT Edge é removido, o contêiner que ele criou é interrompido, mas ainda existe em seu dispositivo. Exiba todos os contêineres para ver quais permanecem.

```bash
sudo docker ps -a
```

Exclua os contêineres do seu dispositivo, incluindo os dois contêineres de tempo de execução.

```bash
sudo docker rm -f <container name>
```

Por fim, remova o tempo de execução do contêiner do seu dispositivo.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Passos seguintes

Agora que tiver um dispositivo IoT Edge aprovisionado com o tempo de execução instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do IoT Edge sendo instalado corretamente, confira a página de [solução de problemas](troubleshoot.md) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IOT Edge e o tempo de execução](how-to-update-iot-edge.md).

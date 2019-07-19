---
title: Instalar o Azure IoT Edge no Linux ARM32 | Microsoft Docs
description: Azure IoT Edge instruções de instalação no Linux em dispositivos ARM32, como um Raspberry PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.openlocfilehash: 72349597a2831a64cb11ff66b30e6948f34cc921
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224701"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalar Azure IoT Edge tempo de execução no Linux (ARM32v7/armhf)

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud. 

Para saber mais sobre como funciona o runtime do IoT Edge e que componentes são incluídos, veja [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o Azure IoT Edge Runtime em um dispositivo Linux ARM32v7/armhf IoT Edge. Por exemplo, essas etapas funcionariam para dispositivos Raspberry Pi. Para obter uma lista de sistemas operacionais ARM32 com suporte, consulte [Azure IOT Edge sistemas com suporte](support.md#operating-systems). 

>[!NOTE]
>Pacotes em repositórios de software do Linux estão sujeita aos termos de licença localizados em cada pacote (/ usr/partilhar/doc/*nome do pacote*). Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

## <a name="install-the-latest-version"></a>Instalar a versão mais recente

Use as seções a seguir para instalar a versão mais recente do serviço de Azure IoT Edge em seus dispositivos ARM do Linux. 

### <a name="install-the-container-runtime"></a>Instalar o runtime de contentor

Azure IoT Edge se baseia em um tempo de execução de contêiner [compatível com o OCI](https://www.opencontainers.org/) . Para cenários de produção, é altamente recomendado que utilize o [com base em Moby](https://mobyproject.org/) motor fornecido abaixo. É o mecanismo de contentor único oficialmente suportado com o Azure IoT Edge. As imagens de contêiner do Docker CE/EE são compatíveis com o tempo de execução baseado em Moby.

Os comandos a seguir instalam o mecanismo baseado em Moby e a CLI (interface de linha de comando). A CLI é útil para desenvolvimento mas opcional para implementações de produção.

```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f
```

### <a name="install-the-iot-edge-security-daemon"></a>Instalar o daemon de segurança do IoT Edge

O **daemon de segurança do IOT Edge** fornece e mantém os padrões de segurança no dispositivo IOT Edge. O daemon começa em cada inicialização e inicializa o dispositivo ao iniciar o restante do runtime do IoT Edge. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

Quando IoT Edge for instalado com êxito, a saída solicitará que você atualize o arquivo de configuração. Siga as etapas na seção [Configurar o daemon de segurança Azure IOT Edge](#configure-the-azure-iot-edge-security-daemon) para concluir o provisionamento do seu dispositivo. 

## <a name="install-a-specific-version"></a>Instalar uma versão específica

Se você quiser instalar uma versão específica do Azure IoT Edge, poderá direcionar os arquivos de componente diretamente do repositório IoT Edge GitHub. Use os mesmos `curl` comandos listados nas seções anteriores para obter todos os componentes de IOT Edge em seu dispositivo: o mecanismo Moby e a CLI, o libiothsm e, finalmente, o daemon de segurança IOT Edge. A única diferença é que você substitui as URLs de **aka.ms** por links que apontam diretamente para a versão de cada componente que você deseja usar.

Navegue até o [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases)e localize a versão de lançamento que você deseja direcionar. Expanda a seção **ativos** para a versão e escolha os arquivos que correspondem à arquitetura do dispositivo IOT Edge. Cada versão de IoT Edge contém arquivos **iotedge** e **libiothsm** . Nem todas as versões incluem **Moby-Engine** ou **Moby-CLI**. Se você ainda não tiver o mecanismo de contêiner do Moby instalado, examine as versões mais antigas até encontrar uma que inclua os componentes do Moby. 

Quando IoT Edge for instalado com êxito, a saída solicitará que você atualize o arquivo de configuração. Siga as etapas na próxima seção para concluir o provisionamento do seu dispositivo. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurar o daemon de segurança do Azure IoT Edge

Configure o runtime do IoT Edge para ligar o dispositivo físico com uma identidade de dispositivo que existe num hub IoT do Azure. 

O daemon de pode ser configurado com o ficheiro de configuração em `/etc/iotedge/config.yaml`. O arquivo é protegido por gravação por padrão, portanto, talvez você precise de permissões elevadas para editá-lo.

Um único dispositivo IoT Edge pode ser aprovisionado manualmente através de uma cadeia de ligações de dispositivo fornecida pelo IoT Hub. Em alternativa, pode utilizar o serviço de aprovisionamento de dispositivos para aprovisionar automaticamente os dispositivos, que é útil quando tem o número de dispositivos para aprovisionar. Dependendo de sua escolha de aprovisionamento, escolha o script de instalação apropriado. 

### <a name="option-1-manual-provisioning"></a>Opção 1: Provisionamento manual

Para provisionar manualmente um dispositivo, você precisa fornecê-lo com uma [cadeia de conexão de dispositivo](how-to-register-device-portal.md) que você pode criar registrando um novo dispositivo IOT Edge no Hub IOT.

Abra o ficheiro de configuração. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize as configurações de provisionamento do arquivo e remova a marca de comentário da seção **configuração de provisionamento manual** . Atualize o valor de **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge. Verifique se qualquer outra seção de provisionamento foi comentada.

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

Guarde e feche o ficheiro. 

`CTRL + X`, `Y`, `Enter`

Após introduzir as informações de aprovisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: Provisionamento automático

Para aprovisionar automaticamente um dispositivo [configurar o serviço aprovisionamento de dispositivos e obter o seu ID de registo do dispositivo](how-to-auto-provision-simulated-device-linux.md). Há vários mecanismos de atestado com suporte pelo IoT Edge ao usar o provisionamento automático, mas os requisitos de hardware também afetam suas escolhas. Por exemplo, os dispositivos Raspberry Pi não vêm com um chip Trusted Platform Module (TPM) por padrão.

Abra o ficheiro de configuração. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize as configurações de provisionamento do arquivo e remova a marca de comentário da seção apropriada para seu mecanismo de atestado. Ao usar o atestado de TPM, por exemplo, atualize os valores de **scope_id** e **registration_id** com os valores de seu serviço de provisionamento de dispositivos no Hub IoT e seu dispositivo IOT Edge com TPM, respectivamente.

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

Guarde e feche o ficheiro. 

`CTRL + X`, `Y`, `Enter`

Após introduzir as informações de aprovisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Se tiver utilizado o **configuração manual** passos na secção anterior, o runtime do IoT Edge deve estar em execução no seu dispositivo e aprovisionada com êxito. Ou, se você usou as etapas de **configuração automática** , precisará concluir algumas etapas adicionais para que o tempo de execução possa registrar seu dispositivo com o Hub IOT em seu nome. Para as próximas etapas, consulte [criar e provisionar um dispositivo IOT Edge de TPM simulado em uma máquina virtual do Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Pode verificar o estado do IoT Edge Daemon:

```bash
systemctl status iotedge
```

Examine os registos de daemon com:

```bash
journalctl -u iotedge --no-pager --no-full
```

E lista de módulos com a executar:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de instalar o tempo de execução, inicie sessão fora do seu computador e inicie sessão novamente para atualizar automaticamente as suas permissões. Até lá, utilize **sudo** à frente de qualquer `iotedge` os comandos.

Em dispositivos de restrição de recursos, é altamente recomendável que defina os *OptimizeForPerformance* variável de ambiente para *false* de acordo com as instruções no [guia de resolução de problemas ](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Se a sua rede que tenha um servidor proxy, siga os passos em [configurar o seu dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

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

## <a name="next-steps"></a>Passos Seguintes

Agora que tiver um dispositivo IoT Edge aprovisionado com o tempo de execução instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do IoT Edge sendo instalado corretamente, consulte a página de [solução de problemas](troubleshoot.md#stability-issues-on-resource-constrained-devices) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IOT Edge e o tempo de execução](how-to-update-iot-edge.md).

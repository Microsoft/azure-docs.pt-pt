---
title: Instalar o Azure IoT Edge no Linux ARM32 | Documentos da Microsoft
description: Azure instruções de instalação de IoT Edge no Linux em dispositivos de ARM32, como um Raspberry PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kgremban
ms.openlocfilehash: ad7e34110b0c6d047eb7454b7fac9f8c10df8be2
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316104"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalar o runtime do Azure IoT Edge no Linux (ARM32v7/armhf)

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud. 

Para saber mais sobre como funciona o runtime do IoT Edge e que componentes são incluídos, veja [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Este artigo lista os passos para instalar o runtime do Azure IoT Edge num dispositivo Linux ARM32v7/armhf IoT Edge. Por exemplo, estes passos funcionaria para dispositivos de Raspberry Pi. Para obter uma lista dos sistemas de operativos ARM32 suportados, consulte [suporte do Azure IoT Edge](support.md#operating-systems). 

>[!NOTE]
>Pacotes em repositórios de software do Linux estão sujeita aos termos de licença localizados em cada pacote (/ usr/partilhar/doc/*nome do pacote*). Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

## <a name="install-the-container-runtime"></a>Instalar o runtime de contentor

O Azure IoT Edge se baseia numa [compatível com o OCI](https://www.opencontainers.org/) tempo de execução do contentor. Para cenários de produção, é altamente recomendado que utilize o [com base em Moby](https://mobyproject.org/) motor fornecido abaixo. É o mecanismo de contentor único oficialmente suportado com o Azure IoT Edge. Imagens de contentor do docker CE/EE são compatíveis com o tempo de execução com base em Moby.

Os seguintes comandos de instalar o motor baseado em Moby e a interface de linha de comandos (CLI). A CLI é útil para desenvolvimento mas opcional para implementações de produção.

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

## <a name="install-the-iot-edge-security-daemon"></a>Instale o Daemon de segurança de IoT Edge

O **daemon de segurança de IoT Edge** fornece e mantém as normas de segurança no dispositivo IoT Edge. O daemon começa em cada inicialização e inicializa o dispositivo ao iniciar o restante do runtime do IoT Edge. 


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

## <a name="connect-your-device-to-an-iot-hub"></a>Ligar o seu dispositivo para um hub IoT 

Configure o runtime do IoT Edge para ligar o dispositivo físico com uma identidade de dispositivo que existe num hub IoT do Azure. 

O daemon de pode ser configurado com o ficheiro de configuração em `/etc/iotedge/config.yaml`. O ficheiro é protegida contra escrita por predefinição, pelo que necessita de permissões elevadas para editá-lo.

Um único dispositivo IoT Edge pode ser aprovisionado manualmente através de uma cadeia de ligações de dispositivo fornecida pelo IoT Hub. Em alternativa, pode utilizar o serviço de aprovisionamento de dispositivos para aprovisionar automaticamente os dispositivos, que é útil quando tem o número de dispositivos para aprovisionar. Dependendo de sua escolha de aprovisionamento, escolha o script de instalação apropriado. 

### <a name="option-1-manual-provisioning"></a>Opção 1: Serviço de aprovisionamento manual

Para aprovisionar manualmente um dispositivo, tem de fornecê-lo com uma [cadeia de ligação do dispositivo](how-to-register-device-portal.md) que pode criar ao registar um novo dispositivo IoT Edge no seu hub IoT.

Abra o ficheiro de configuração. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize a seção de aprovisionamento do ficheiro e anule os comentários da **manual** modo de aprovisionamento. Atualize o valor de **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# provisioning: 
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   registration_id: "{registration_id}"
```

Guarde e feche o ficheiro. 

`CTRL + X`, `Y`, `Enter`

Após introduzir as informações de aprovisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: Aprovisionamento automático

Para aprovisionar automaticamente um dispositivo [configurar o serviço aprovisionamento de dispositivos e obter o seu ID de registo do dispositivo](how-to-auto-provision-simulated-device-linux.md). Aprovisionamento automático só funciona com dispositivos que tenham um chip Trusted Platform Module (TPM). Por exemplo, dispositivos de Raspberry Pi vem com TPM por predefinição. 

Abra o ficheiro de configuração. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize a seção de aprovisionamento do ficheiro e anule os comentários da **dps** modo de aprovisionamento. Atualize os valores **scope_id** e **registration_id** com os valores do seu serviço de aprovisionamento de dispositivos do IoT Hub e o seu dispositivo IoT Edge com o TPM. 

```yaml
# provisioning:
#   source: "manual"
#   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
provisioning: 
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "{scope_id}"
  registration_id: "{registration_id}"
```

Guarde e feche o ficheiro. 

`CTRL + X`, `Y`, `Enter`

Após introduzir as informações de aprovisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Se tiver utilizado o **configuração manual** passos na secção anterior, o runtime do IoT Edge deve estar em execução no seu dispositivo e aprovisionada com êxito. Ou, se tiver utilizado o **a configuração automática** passos, tem de concluir alguns passos adicionais para que o tempo de execução pode registar o seu dispositivo com o seu hub IoT em seu nome. Para os passos seguintes, veja [criar e aprovisionar um dispositivo TPM IoT Edge simulado numa máquina virtual Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

## <a name="uninstall-iot-edge"></a>Desinstalar do IoT Edge

Se pretender remover a instalação do IoT Edge do seu dispositivo de Linux, utilize os seguintes comandos na linha de comando. 

Remova o runtime do IoT Edge. 

```bash
sudo apt-get remove --purge iotedge
```

Quando o tempo de execução do IoT Edge é removido, o contentor que ele criou são parados, mas ainda existem no seu dispositivo. Ver todos os contentores para ver quais os permanecem. 

```bash
sudo docker ps -a
```

Elimine os contentores do seu dispositivo, incluindo os dois contentores de tempo de execução. 

```bash
sudo docker rm -f <container name>
```

Por fim, remova o tempo de execução do contentor do seu dispositivo. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Passos Seguintes

Agora que tiver um dispositivo IoT Edge aprovisionado com o tempo de execução instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver a ter problemas com o runtime do IoT Edge corretamente a instalação, consulte a [resolução de problemas](troubleshoot.md#stability-issues-on-resource-constrained-devices) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, veja [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

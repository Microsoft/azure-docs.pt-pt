---
title: Instalar o Azure IoT Edge no Linux | Documentos da Microsoft
description: Instruções de instalação do Azure IoT Edge em dispositivos de AMD64 do Linux com o Ubuntu
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 398b984f4d97005fdc4d749f3fe072423cc5bbd7
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309303"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalar o runtime do Azure IoT Edge no Linux (x64)

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud.

Para obter mais informações, consulte [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Este artigo lista os passos para instalar o runtime do Azure IoT Edge no seu Linux x64 (Intel/AMD) dispositivo IoT Edge. Consulte a [suporte do Azure IoT Edge](support.md#operating-systems) para obter uma lista dos sistemas de operativos AMD64 suportados.

> [!NOTE]
> Pacotes em repositórios de software do Linux estão sujeita aos termos de licença localizados em cada pacote (/ usr/partilhar/doc/*nome do pacote*). Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Registar o feed de repositório de chave e o software Microsoft

Prepare o seu dispositivo para o IoT Edge instalação de tempo de execução.


Instale a configuração do repositório. Substitua **\<versão\>** com **16.04** ou **18.04** conforme adequado para a sua versão do Ubuntu.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/<release>/prod.list > ./microsoft-prod.list
   ```

Copie a lista gerada.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instalar a chave pública da Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-the-container-runtime"></a>Instalar o runtime de contentor

O Azure IoT Edge se baseia numa [compatível com o OCI](https://www.opencontainers.org/) tempo de execução do contentor. Para cenários de produção, recomendamos que utilize o [com base em Moby](https://mobyproject.org/) motor fornecido abaixo. É o mecanismo de contentor único oficialmente suportado com o Azure IoT Edge. Imagens de contentor do docker CE/EE são compatíveis com o tempo de execução Moby.

Execute atualização apt.

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

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instale o Daemon de segurança do Azure IoT Edge

O **daemon de segurança de IoT Edge** fornece e mantém as normas de segurança no dispositivo IoT Edge. O daemon começa em cada inicialização e inicializa o dispositivo ao iniciar o restante do runtime do IoT Edge.

O comando de instalação também instala a versão padrão dos **iothsmlib** se ainda não estiver presente.

Execute atualização apt.

   ```bash
   sudo apt-get update
   ```

Instale o daemon de segurança. O pacote está instalado em `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurar o Daemon de segurança do Azure IoT Edge

Configure o runtime do IoT Edge para ligar o dispositivo físico com uma identidade de dispositivo que existe num hub IoT do Azure.

O daemon de pode ser configurado com o ficheiro de configuração em `/etc/iotedge/config.yaml`. O ficheiro é protegida contra escrita por padrão, poderá ter permissões elevadas para editá-lo.

Um único dispositivo IoT Edge pode ser aprovisionado manualmente através de uma cadeia de ligações de dispositivo fornecida pelo IoT Hub. Em alternativa, pode utilizar o serviço de aprovisionamento de dispositivos para aprovisionar automaticamente os dispositivos, que é útil quando tem o número de dispositivos para aprovisionar. Dependendo de sua escolha de aprovisionamento, escolha o script de instalação apropriado.

### <a name="option-1-manual-provisioning"></a>Opção 1: Serviço de aprovisionamento manual

Para aprovisionar manualmente um dispositivo, tem de fornecê-lo com uma [cadeia de ligação do dispositivo](how-to-register-device-portal.md) que pode criar ao registar um novo dispositivo no IoT hub.

Abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize a seção de aprovisionamento do ficheiro. Anule os comentários da **manual** modo de aprovisionamento e certificar-se de que o modo de aprovisionamento de pontos de distribuição é comentado. Atualize o valor de **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge.

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

Localize a seção de aprovisionamento do ficheiro. Anule os comentários da **dps** aprovisionamento modo e comente a seção manual. Atualize os valores **scope_id** e **registration_id** com os valores do seu serviço de aprovisionamento de dispositivos do IoT Hub e o seu dispositivo IoT Edge com o TPM.

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

Se tiver utilizado o **configuração manual** passos na secção anterior, o runtime do IoT Edge deve estar em execução no seu dispositivo e aprovisionada com êxito. Se tiver utilizado o **a configuração automática** passos, tem de concluir alguns passos adicionais para que o tempo de execução pode registar o seu dispositivo com o seu hub IoT em seu nome. Para os passos seguintes, veja [criar e aprovisionar um dispositivo TPM IoT Edge simulado numa máquina virtual Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

Em dispositivos de restrição de recursos, é altamente recomendável que defina os *OptimizeForPerformance* variável de ambiente para *false* de acordo com as instruções no [guia de resolução de problemas ](troubleshoot.md).

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

Se estiver a ter problemas com o runtime do IoT Edge corretamente a instalação, consulte a [resolução de problemas](troubleshoot.md) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, veja [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

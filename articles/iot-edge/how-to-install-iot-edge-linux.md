---
title: Instalar o Azure IoT Edge no Linux | Documentos da Microsoft
description: Instruções de instalação do Azure IoT Edge em dispositivos de AMD64 do Linux com o Ubuntu
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: bbab0d8d0947c18cf8e6c178d12fdbd7b335d2b6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485904"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalar o runtime do Azure IoT Edge no Linux (x64)

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud.

Para obter mais informações, consulte [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Este artigo lista os passos para instalar o runtime do Azure IoT Edge no seu Ubuntu Linux x64 (Intel/AMD) dispositivo IoT Edge. Consulte a [sistemas de suporte do Azure IoT Edge](support.md#operating-systems) para obter uma lista dos sistemas de operativos AMD64 suportados.

> [!NOTE]
> Pacotes em repositórios de software do Linux estão sujeita aos termos de licença localizados em cada pacote (/ usr/partilhar/doc/*nome do pacote*). Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

## <a name="install-the-latest-version"></a>Instalar a versão mais recente

Utilize as secções seguintes para instalar a versão mais recente do serviço do Azure IoT Edge em seus dispositivos. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registar o feed de repositório de chave e o software Microsoft

Prepare o seu dispositivo para o IoT Edge instalação de tempo de execução.


Instale a configuração do repositório. Escolher entre o **16.04** ou **18.04** trecho de código, conforme adequado para a sua versão do Ubuntu:

> [!NOTE]
> Certifique-se de que escolher o trecho de código na caixa de código correto para a sua versão do Ubuntu.

* Para **Ubuntu 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* Para **Ubuntu 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
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

### <a name="install-the-container-runtime"></a>Instalar o runtime de contentor

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

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifique se o kernel do Linux para compatibilidade Moby

Muitos fabricantes de dispositivos embedded enviar imagens de dispositivo que contêm personalizados kernels de Linux que podem estar em falta funcionalidades necessárias para compatibilidade de tempo de execução do contentor. Se tiver problemas ao instalar o recomendado [Moby](https://github.com/moby/moby) tempo de execução do contentor, talvez consiga resolver problemas de sua através de configuração de kernel do Linux a [verificação-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) script fornecido no oficial [repositório do Moby Github](https://github.com/moby/moby) executando os seguintes comandos no dispositivo.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Isso fornecerá uma saída detalhada que contém o estado das funcionalidades do kernel que são utilizados pelo tempo de execução Moby. Vai querer garantir que todos os itens em `Generally Necessary` e `Network Drivers` estão ativadas para se certificar de que o kernel é totalmente compatível com o tempo de execução Moby.  Se identificou os recursos em falta, poderá ativá-las reconstruir o kernel de origem e selecionando os módulos associados para inclusão no. config de kernel apropriado.  Da mesma forma, se estiver a utilizar um gerador de configuração de kernel como defconfig ou menuconfig, terá de localizar e ativar as respetivas funcionalidades e recriar o kernel em conformidade.  Assim que tiver implementado o kernel modificado mais recentemente, execute o script de configuração de verificação novamente para verificar que os recursos identificados foram ativados com êxito.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instale o Daemon de segurança do Azure IoT Edge

O **daemon de segurança de IoT Edge** fornece e mantém as normas de segurança no dispositivo IoT Edge. O daemon começa em cada inicialização e inicializa o dispositivo ao iniciar o restante do runtime do IoT Edge.

O comando de instalação também instala a versão padrão dos **libiothsm** se ainda não estiver presente.

Execute atualização apt.

   ```bash
   sudo apt-get update
   ```

Instale o daemon de segurança. O pacote está instalado em `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Assim que o IoT Edge foi instalado com êxito, a saída irá solicitar-lhe para atualizar o ficheiro de configuração. Siga os passos a [configurar o daemon de segurança do Azure IoT Edge](#configure-the-azure-iot-edge-security-daemon) secção para concluir o aprovisionamento do seu dispositivo. 

## <a name="install-a-specific-version"></a>Instalar uma versão específica

Se pretender instalar uma versão específica do Azure IoT Edge, pode filtrar os ficheiros de componente diretamente a partir do repositório do GitHub do IoT Edge. Utilize os passos seguintes para obter todo o IoT Edge componentes em seu dispositivo: o motor de Moby e CLI, o libiothsm e, finalmente, o daemon de segurança de IoT Edge.

1. Navegue para o [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)e encontrar a versão de lançamento de destino. 

2. Expanda a **ativos** secção para essa versão.

3. Pode existir ou não ser atualizações para o mecanismo de Moby em qualquer determinada versão. Se vir que os ficheiros começados por **mecanismo de moby** e **moby-cli**, utilize os seguintes comandos para atualizar esses componentes. Se não vir quaisquer ficheiros Moby e ainda não tiver Moby instalada no seu dispositivo, retroceder os ativos da versão mais antigos até que encontrá-los. 

   1. Encontrar o **mecanismo de moby** ficheiro que corresponde à arquitetura do seu dispositivo IoT Edge. Faça duplo clique no link de arquivo e copie o endereço de link.

   2. Utilize a ligação de copiado no comando seguinte para instalar essa versão do motor Moby: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Encontrar o **moby-cli** ficheiro que corresponde à arquitetura do seu dispositivo IoT Edge. A CLI Moby é um componente opcional, mas pode ser útil durante o desenvolvimento. Faça duplo clique no link de arquivo e copie o endereço de link. 

   4. Utilize a ligação de copiado no comando seguinte para instalar essa versão da Moby CLI: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Cada versão deve ter ficheiros novos para o daemon de segurança de IoT Edge e o hsmlib. Utilize os seguintes comandos para atualizar esses componentes. 

   1. Encontrar o **libiothsm std** ficheiro que corresponde à arquitetura do seu dispositivo IoT Edge. Faça duplo clique no link de arquivo e copie o endereço de link. 

   2. Utilize a ligação de copiado no comando seguinte para instalar essa versão do hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Encontrar o **iotedge** ficheiro que corresponde à arquitetura do seu dispositivo IoT Edge. Faça duplo clique no link de arquivo e copie o endereço de link. 

   4. Utilize a ligação copiada no comando seguinte para instalar essa versão do daemon de segurança de IoT Edge. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Assim que o IoT Edge foi instalado com êxito, a saída irá solicitar-lhe para atualizar o ficheiro de configuração. Siga os passos na secção seguinte para concluir o aprovisionamento do seu dispositivo. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurar o daemon de segurança do Azure IoT Edge

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

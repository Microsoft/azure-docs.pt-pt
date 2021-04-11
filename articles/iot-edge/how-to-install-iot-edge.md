---
title: Instale | de borda Azure IoT Microsoft Docs
description: Instruções de instalação do Azure IoT Edge em dispositivos Windows ou Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: kgremban
ms.openlocfilehash: a98eed61904b580988fe34302999f3ec6a24ac9e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640914"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Instale ou desinstale a borda Azure IoT para o Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

O tempo de execução Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de funcionaamento pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grande como um servidor industrial. Quando um dispositivo é configurado com o runtime do IoT Edge, pode começar a implementar a lógica de negócio no mesmo partir da cloud. Para saber mais, consulte [o tempo de execução Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para instalar o tempo de funcionamento do Azure IoT Edge em dispositivos Linux.

## <a name="prerequisites"></a>Pré-requisitos

* Um [ID de dispositivo registado](how-to-register-device.md)

  Se registou o seu dispositivo com autenticação de chave simétrica, tenha a cadeia de ligação do dispositivo pronta.

  Se registou o seu dispositivo com a autenticação de certificado auto-assinado X.509, tenha pelo menos um dos certificados de identidade que usou para registar o dispositivo e a sua chave privada correspondente disponível no seu dispositivo.

* Um dispositivo Linux

  Tenha um dispositivo X64, ARM32 ou ARM64 Linux. A Microsoft fornece pacotes de instalação para sistemas operativos Ubuntu Server 18.04 e Raspberry Pi OS Stretch.

  Para obter as informações mais recentes sobre quais os sistemas operativos que são atualmente suportados para cenários de produção, consulte [os sistemas suportados Azure IoT Edge](support.md#operating-systems)

  >[!NOTE]
  >O suporte para dispositivos ARM64 está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Prepare o seu dispositivo para aceder aos pacotes de instalação da Microsoft.

  Instale a configuração do repositório que corresponda ao sistema operativo do dispositivo.

  * **Servidor Ubuntu 18.04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Estiramento de Framboesa Pi OS**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Copie a lista gerada para o diretório sources.list.d.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Instale a chave pública Microsoft GPG.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Os pacotes de software Azure IoT Edge estão sujeitos aos termos de licença localizados em cada pacote `usr/share/doc/{package-name}` (ou `LICENSE` diretório). Leia os termos da licença antes de utilizar uma embalagem. A sua instalação e utilização de um pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize esse pacote.

## <a name="install-a-container-engine"></a>Instale um motor de contentores

O Azure IoT Edge conta com um tempo de funcionamento do recipiente compatível com OCI. Para cenários de produção, recomendei-lhe que usasse o motor Moby. O motor Moby é o único motor de contentores oficialmente suportado com Azure IoT Edge. As imagens do contentor Estivador CE/EE são compatíveis com o tempo de execução do Moby.

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

## <a name="install-iot-edge"></a>Instalar borda IoT

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

O daemon de segurança IoT Edge fornece e mantém os padrões de segurança no dispositivo IoT Edge. O daemon começa em cada bota e botas armadilhas do dispositivo iniciando o resto do tempo de execução IoT Edge.

Os passos nesta secção representam o processo típico de instalação da versão mais recente num dispositivo que tem ligação à Internet. Se precisar de instalar uma versão específica, como uma versão pré-lançamento, ou precisar de ser instalada offline, siga os passos de [instalação offline ou de versão específica](#offline-or-specific-version-installation-optional) mais tarde neste artigo.

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

Ou, se quiser instalar uma versão específica do daemon de segurança, especifique a versão a partir da saída da lista apta. Especificar também a mesma versão para o pacote **de sedos de libiothsm,** que de outra forma iria instalar a sua versão mais recente. Por exemplo, o seguinte comando instala a versão mais recente da versão 1.0.10:

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

Se a versão que pretende instalar não estiver listada, siga os passos [de instalação offline ou de versão específica](#offline-or-specific-version-installation-optional) mais tarde neste artigo. Esta secção mostra-lhe como direcionar qualquer versão anterior do daemon de segurança IoT Edge ou lançar versões de candidatos.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

O serviço IoT Edge fornece e mantém padrões de segurança no dispositivo IoT Edge. O serviço começa em todas as botas e botas do dispositivo iniciando o resto do tempo de execução IoT Edge.

O serviço de identidade IoT foi introduzido juntamente com a versão 1.2 do IoT Edge. Este serviço trata do fornecimento e gestão de identidade para ioT Edge e para outros componentes do dispositivo que precisam de comunicar com o IoT Hub.

Os passos nesta secção representam o processo típico de instalação da versão mais recente num dispositivo que tem ligação à Internet. Se precisar de instalar uma versão específica, como uma versão pré-lançamento, ou precisar de ser instalada offline, siga os passos de [instalação offline ou de versão específica](#offline-or-specific-version-installation-optional) mais tarde neste artigo.

>[!NOTE]
>Os passos desta secção mostram-lhe como instalar a versão 1.2 do IoT Edge, que está atualmente em pré-visualização pública. Se estiver à procura dos passos para instalar a versão mais recente geralmente disponível do IoT Edge, veja a versão [1.1 (LTS)](?view=iotedge-2018-06&preserve-view=true) deste artigo.
>
>Se já tem um dispositivo IoT Edge a executar uma versão mais antiga e quer atualizar para 1.2, utilize os passos em [Atualizar o daemon de segurança IoT Edge e o tempo de execução](how-to-update-iot-edge.md). A versão 1.2 é suficientemente diferente das versões anteriores do IoT Edge que são necessárias medidas específicas para atualizar.

Atualize as listas de pacotes no seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Verifique quais as versões do IoT Edge disponíveis.

   ```bash
   apt list -a aziot-edge
   ```

Se pretender instalar a versão mais recente do IoT Edge, utilize o seguinte comando que também instala a versão mais recente do pacote de serviço de identidade:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- commenting out for public preview. reintroduce at GA

Or, if you want to install a specific version of IoT Edge and the identity service, specify the versions from the apt list output. Specify the same versions for both services.. For example, the following command installs the most recent version of the 1.2 release:

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

-->

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Fornecimento do dispositivo com a sua identidade em nuvem

Agora que o motor de contentores e o tempo de funcionaamento do IoT Edge estão instalados no seu dispositivo, está pronto para o próximo passo, que é configurar o dispositivo com a sua identidade em nuvem e informações de autenticação.

Escolha a próxima secção com base no tipo de autenticação que pretende utilizar:

* [Opção 1: Autenticar com teclas simétricas](#option-1-authenticate-with-symmetric-keys)
* [Opção 2: Autenticar com certificados X.509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opção 1: Autenticar com teclas simétricas

Neste momento, o tempo de execução IoT Edge está instalado no seu dispositivo Linux, e é necessário doar ao dispositivo a sua identidade em nuvem e informações de autenticação.

Esta secção percorre os passos para providenciar um dispositivo com autenticação de chave simétrica. Devia ter registado o seu dispositivo no IoT Hub e recuperado a cadeia de ligação a partir das informações do dispositivo. Caso contrário, siga os passos no [Registo de um dispositivo IoT Edge no IoT Hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

No dispositivo IoT Edge, abra o ficheiro de configuração.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Encontre as configurações de provisionamento do ficheiro e descompromete **a configuração manual de provisionamento utilizando uma secção de cadeia de ligação,** se ainda não estiver descomprimida.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Atualize o valor de **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge. Certifique-se de que quaisquer outras secções de provisionamento são comentadas. Certifique-se de que o **provisionamento: a** linha não tem espaço branco anterior e que os itens aninhados são recortados por dois espaços.

Para colar o conteúdo da prancheta em Nano `Shift+Right Click` ou premir `Shift+Insert` .

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Depois de introduzir as informações de provisionamento no ficheiro de configuração, reinicie o daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Crie o ficheiro de configuração para o seu dispositivo com base num ficheiro de modelo que é fornecido como parte da instalação IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

No dispositivo IoT Edge, abra o ficheiro de configuração.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Encontre a secção **de Provisionamento** do ficheiro e descompromete o provisionamento manual com linhas de fio de ligação.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Atualize o valor de **connection_string** com a cadeia de ligação do seu dispositivo IoT Edge.

Para colar o conteúdo da prancheta em Nano `Shift+Right Click` ou premir `Shift+Insert` .

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Depois de introduzir as informações de provisionamento no ficheiro de configuração, aplique as suas alterações:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>Opção 2: Autenticar com certificados X.509

Neste momento, o tempo de execução IoT Edge está instalado no seu dispositivo Linux, e é necessário doar ao dispositivo a sua identidade em nuvem e informações de autenticação.

Esta secção percorre os passos para providenciar um dispositivo com autenticação de certificado X.509. Deverá ter registado o seu dispositivo no IoT Hub, fornecendo impressões digitais que correspondam ao certificado e à chave privada localizado no seu dispositivo IoT Edge. Caso contrário, siga os passos no [Registo de um dispositivo IoT Edge no IoT Hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

No dispositivo IoT Edge, abra o ficheiro de configuração.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Encontre a secção de configurações de provisionamento do ficheiro e desacompromete **a configuração manual de provisionamento utilizando uma secção de certificado de identidade X.509.** Certifique-se de que quaisquer outras secções de provisionamento são comentadas. Certifique-se de que o **provisionamento: a** linha não tem espaço branco anterior e que os itens aninhados são recortados por dois espaços.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Atualizar os seguintes campos:

* **iothub_hostname**: Nome de anfitrião do hub IoT a que o dispositivo se ligará. Por exemplo, `{IoT hub name}.azure-devices.net`.
* **device_id:** O ID que forneceu quando registou o dispositivo.
* **identity_cert**: URI a um certificado de identidade no dispositivo. Por exemplo, `file:///path/identity_certificate.pem`.
* **identity_pk**: URI ao ficheiro de chave privada para o certificado de identidade fornecido. Por exemplo, `file:///path/identity_key.pem`.

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Depois de introduzir as informações de provisionamento no ficheiro de configuração, reinicie o daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Crie o ficheiro de configuração para o seu dispositivo com base num ficheiro de modelo que é fornecido como parte da instalação IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

No dispositivo IoT Edge, abra o ficheiro de configuração.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Encontre a secção de **Provisionamento** do ficheiro e descodi as linhas para o provisionamento manual com certificado de identidade X.509. Certifique-se de que quaisquer outras secções de provisionamento são comentadas.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Atualizar os seguintes campos:

* **iothub_hostname**: Nome de anfitrião do hub IoT a que o dispositivo se ligará. Por exemplo, `{IoT hub name}.azure-devices.net`.
* **device_id:** O ID que forneceu quando registou o dispositivo.
* **identity_cert**: URI a um certificado de identidade no dispositivo, por exemplo: `file:///path/identity_certificate.pem` . Ou, dinamicamente, emite o certificado utilizando a EST ou uma autoridade de certificados locais.
* **identity_pk**: URI ao ficheiro-chave privado para o certificado de identidade fornecido, por exemplo: `file:///path/identity_key.pem` . Ou, forneça um PKCS#11 URI e, em seguida, forneça as suas informações de configuração na secção **PKCS#11** mais tarde no ficheiro config.

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Depois de introduzir as informações de provisionamento no ficheiro de configuração, aplique as suas alterações:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Verifique a configuração bem sucedida

Verifique se o tempo de funcionaamento foi instalado com sucesso e configurado no seu dispositivo IoT Edge.

>[!TIP]
>Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de terminar sessão do seu computador e iniciar sessão novamente pela primeira vez depois de instalar o runtime do IoT Edge, as suas permissões são atualizadas automaticamente. Até lá, use `sudo` na frente dos comandos.

Verifique se o serviço de sistema IoT Edge está em funcionamento.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

Uma resposta de estado bem sucedida é `Ok` .

::: moniker-end

Se precisar de resolver problemas relacionados com o serviço, obtenha os registos do serviço.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Utilize a `check` ferramenta para verificar a configuração e o estado de ligação do dispositivo.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Utilize sempre `sudo` para executar a ferramenta de verificação, mesmo depois de as suas permissões serem atualizadas. A ferramenta necessita de privilégios elevados para aceder ao ficheiro config para verificar o estado da configuração.

Veja todos os módulos em execução no seu dispositivo IoT Edge. Quando o serviço começar pela primeira vez, só deverá ver o módulo **edgeAgent** a funcionar. O módulo EdgeAgent funciona por predefinição e ajuda a instalar e iniciar quaisquer módulos adicionais que implemente no seu dispositivo.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Instalação de versão offline ou específica (opcional)

Os passos nesta secção são para cenários não abrangidos pelas etapas de instalação padrão. Isto pode incluir:

* Instale ioT Edge enquanto offline
* Instale uma versão de candidato de lançamento

Utilize os passos nesta secção se pretender instalar uma versão específica do tempo de execução Azure IoT Edge que não está disponível através `apt-get install` de . A lista de pacotes da Microsoft contém apenas um conjunto limitado de versões recentes e suas sub-versões, pelo que estes passos são para quem pretenda instalar uma versão mais antiga ou uma versão candidata ao lançamento.

Utilizando comandos curl, pode direcionar os ficheiros componentes diretamente do repositório IoT Edge GitHub.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Navegue para as [versões Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)e encontre a versão de lançamento que pretende atingir.

2. Expandir a secção **Ativos** para esta versão.

3. Cada versão deve ter novos ficheiros para o daemon de segurança IoT Edge e para o hsmlib. Se vai instalar o IoT Edge num dispositivo offline, descarregue estes ficheiros antes do tempo. Caso contrário, utilize os seguintes comandos para atualizar esses componentes.

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

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Se o seu dispositivo estiver atualmente a executar a versão 1.1 ou mais antiga do IoT Edge, desinstale as embalagens **iotedge** e **libiothsm-std** antes de seguir os passos nesta secção. Para mais informações, consulte [atualização de 1.0 ou 1.1 a 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Navegue para as [versões Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)e encontre a versão de lançamento que pretende atingir.

2. Expandir a secção **Ativos** para esta versão.

3. Cada lançamento deve ter novos ficheiros para ioT Edge e o serviço de identidade. Se vai instalar o IoT Edge num dispositivo offline, descarregue estes ficheiros antes do tempo. Caso contrário, utilize os seguintes comandos para atualizar esses componentes.

   1. Encontre o ficheiro **de serviço de identidade aziot** que corresponde à arquitetura do seu dispositivo IoT Edge. Clique com o botão direito no link do ficheiro e copie o endereço de link.

   2. Utilize o link copiado no seguinte comando para instalar a versão do serviço de identidade:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. Encontre o ficheiro **de borda aziot** que corresponde à arquitetura do seu dispositivo IoT Edge. Clique com o botão direito no link do ficheiro e copie o endereço de link.

   4. Utilize o link copiado no seguinte comando para instalar a versão do IoT Edge.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Agora que o motor de contentores e o tempo de funcionaamento do IoT Edge estão instalados no seu dispositivo, está pronto para o próximo passo, que é [providenciar o dispositivo com a sua identidade em nuvem](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Desinstalar ioT Edge

Se pretender remover a instalação IoT Edge do seu dispositivo, utilize os seguintes comandos.

Remova o runtime do IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Utilize a `--purge` bandeira se pretender eliminar todos os ficheiros associados ao IoT Edge, incluindo os seus ficheiros de configuração. Deixe esta bandeira de fora se pretender reinstalar o IoT Edge e utilizar as mesmas informações de configuração no futuro.

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

## <a name="next-steps"></a>Passos seguintes

Continue a [implementar módulos IoT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos no seu dispositivo.

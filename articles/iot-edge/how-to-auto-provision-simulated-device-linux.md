---
title: Dispositivo de provisão com um TPM virtual no Linux VM - Azure IoT Edge
description: Utilize um TPM simulado num Linux VM para testar o Serviço de Provisionamento de Dispositivos Azure para Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0583852f0be590eb1c6a4b53047f94b3ea0fbaa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447821"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Criar e providenciar um dispositivo IoT Edge com um TPM no Linux

Este artigo mostra como testar o provisionamento automático num dispositivo Linux IoT Edge utilizando um Módulo de Plataforma Fidedigna (TPM). Pode automaticamente a provisionar dispositivos Azure IoT Edge com o [Serviço de Provisionamento de Dispositivos](../iot-dps/index.yml). Se não estiver familiarizado com o processo de provisão automática, reveja a visão geral do [provisionamento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

As tarefas são as seguintes:

1. Crie uma máquina virtual Linux (VM) em Hiper-V com um Módulo de Plataforma Fidedigna simulada (TPM) para segurança de hardware.
1. Criar uma instância do Serviço de Provisionamento de Dispositivos IoT Hub (DPS).
1. Crie uma inscrição individual para o dispositivo.
1. Instale o tempo de funcionaamento do IoT Edge e ligue o dispositivo ao IoT Hub.

> [!TIP]
> Este artigo descreve como testar o fornecimento de DPS usando um simulador TPM, mas grande parte dele se aplica a hardware TPM físico, como o [Infineon OPTIGA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), um Azure Certified for IoT.
>
> Se estiver a utilizar um dispositivo físico, pode adiantar-se à [informação de fornecimento de informações de um dispositivo físico](#retrieve-provisioning-information-from-a-physical-device) neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma máquina de desenvolvimento Windows com [Hiper-V ativada](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Este artigo utiliza o Windows 10 com um Ubuntu Server VM.
* Um hub IoT ativo.

> [!NOTE]
> TPM 2.0 é necessário ao utilizar atestado de TPM com DPS e só pode ser usado para criar inscrições individuais, não de grupo.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Criar uma máquina virtual Linux com um TPM virtual

Nesta secção, cria-se uma nova máquina virtual Linux no Hyper-V. Configura esta máquina virtual com um TPM simulado para testar como o provisionamento automático funciona com ioT Edge.

### <a name="create-a-virtual-switch"></a>Criar um comutador virtual

Um interruptor virtual permite que a sua máquina virtual se conecte a uma rede física.

1. Abra o Hyper-V Manager na sua máquina Windows.

2. No menu **Ações,** selecione **Virtual Switch Manager**.

3. Escolha um interruptor virtual **externo** e, em seguida, selecione **Criar Switch Virtual**.

4. Dê um nome ao seu novo interruptor virtual, por exemplo **EdgeSwitch**. Certifique-se de que o tipo de ligação está definido para **rede externa**e, em seguida, selecione **Ok**.

5. Um pop-up avisa-o de que a conectividade da rede pode ser interrompida. Selecione **Sim** para continuar.

Se vir erros ao criar o novo interruptor virtual, certifique-se de que nenhum outro interruptor está a utilizar o adaptador ethernet e que nenhum outro interruptor usa o mesmo nome.

### <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Descarregue um ficheiro de imagem em disco para utilizar para a sua máquina virtual e guarde-o localmente. Por exemplo, [o servidor Ubuntu 18.04](http://releases.ubuntu.com/18.04/). Para obter informações sobre sistemas operativos suportados para dispositivos IoT Edge, consulte [os sistemas suportados Azure IoT Edge](support.md).

2. No Hyper-V Manager novamente, selecione **Action**  >  **New**  >  **Virtual Machine** no menu **Ações.**

3. Complete o **Novo Assistente de Máquinas Virtuais** com as seguintes configurações específicas:

   1. **Especificar Geração**: Selecione **Geração 2**. As máquinas virtuais de geração 2 têm uma virtualização aninhada, que é necessária para executar ioT Edge numa máquina virtual.
   2. **Configurar rede**: Desconfige o valor da **Ligação** ao interruptor virtual que criou na secção anterior.
   3. **Opções de instalação**: Selecione **Instale um sistema operativo a partir de um ficheiro de imagem inicializável** e navegue no ficheiro de imagem do disco que guardou localmente.

4. Selecione **Terminar** no assistente para criar a máquina virtual.

Pode levar alguns minutos para criar o novo VM.

### <a name="enable-virtual-tpm"></a>Ativar TPM virtual

Assim que o seu VM for criado, abra as suas definições para ativar o módulo de plataforma de confiança virtual (TPM) que lhe permite abastecer automaticamente o dispositivo.

1. No Hyper-V Manager, clique à direita no VM e selecione **Definições**.

2. Navegar para a **Segurança.**

3. Desmarque **Ativar a bota segura**.

4. Verifique **o módulo de plataforma fidedigna.**

5. Clique em **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Inicie a máquina virtual e recolha dados de TPM

Na máquina virtual, construa uma ferramenta que pode utilizar para recuperar a **chave de ID** de Registo e **Endosso**do dispositivo.

1. No Hyper-V Manager, inicie o seu VM e ligue-se a ele.

1. Siga as indicações dentro da máquina virtual para terminar o processo de instalação e reiniciar a máquina.

1. Inscreva-se no seu VM e siga os passos na [Configuração de um ambiente de desenvolvimento Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar e construir o dispositivo Azure IoT SDK para C.

   >[!TIP]
   >No decorrer deste artigo, irá copiar e colar na máquina virtual, o que não é fácil através da aplicação de conexão Hyper-V Manager. É melhor ligar-se à máquina virtual através do Hyper-V Manager uma vez para recuperar o seu endereço IP. Primeiro correr `sudo apt install net-tools` e `hostname -I` depois. Em seguida, pode utilizar o endereço IP para ligar através do SSH: `ssh <username>@<ipaddress>` .

1. Executar os seguintes comandos para construir a ferramenta SDK que recupera o seu dispositivo fornecendo informações a partir do TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. A janela de saída exibe o **ID** de registo do dispositivo e a **tecla de endosso**. Copie estes valores para utilização posterior quando criar uma inscrição individual para o seu dispositivo.

Assim que tiver o seu ID de registo e chave de endosso, continue para a secção [Configurar o Serviço de Provisionamento de Dispositivos IoT Hub](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Recuperar informações de fornecimento de um dispositivo físico

Se estiver a utilizar um dispositivo IoT Edge físico em vez de um VM, construa uma ferramenta que possa utilizar para recuperar as informações de fornecimento do dispositivo.

1. Siga os passos na [Configuração de um ambiente de desenvolvimento Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar e construir o dispositivo Azure IoT SDK para C.

1. Executar os seguintes comandos para construir a ferramenta SDK que recupera o seu dispositivo fornecendo informações a partir do dispositivo TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copiar os valores para **identificação de registo** e **chave de endosso.** Utiliza estes valores para criar uma inscrição individual para o seu dispositivo em DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de provisionamento de dispositivos IoT Hub

Crie uma nova instância do serviço de provisionamento de dispositivos IoT Hub em Azure e ligue-o ao seu hub IoT. Pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de ter o Serviço de Provisionamento de Dispositivos em funcionamento, copie o valor do **ID Scope** na página de visão geral. Utilize este valor quando configurar o tempo de execução IoT Edge.

## <a name="create-a-dps-enrollment"></a>Criar uma inscrição de DPS

Recupere as informações de fornecimento da sua máquina virtual e use-as para criar uma inscrição individual no Serviço de Provisionamento de Dispositivos.

Quando cria uma inscrição em DPS, tem a oportunidade de declarar um **Estado Gémeo de Dispositivo Inicial.** No twin do dispositivo, pode definir tags para agrupar dispositivos por qualquer métrica que necessite na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas.](how-to-deploy-at-scale.md)

> [!TIP]
> No CLI Azure, pode criar uma [inscrição](/cli/azure/ext/azure-iot/iot/dps/enrollment) e utilizar a bandeira **ativada por arestas** para especificar que um dispositivo é um dispositivo IoT Edge.

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Serviço de Provisionamento de Dispositivos IoT Hub.

2. Em **Definições**, **selecione Gerir as inscrições**.

3. **Selecione Adicionar inscrição individual** e, em seguida, completar os seguintes passos para configurar a inscrição:  

   1. Para **mecanismo**, selecione **TPM**.

   2. Forneça a **chave de endosso** e **identificação de registo** que copiou da sua máquina virtual.

      > [!TIP]
      > Se estiver a utilizar um dispositivo TPM físico, tem de determinar a **tecla Endorsement**, que é única em cada chip TPM e é obtida do fabricante de chips TPM associado a ele. Pode obter um **ID de registo** único para o seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.

   3. Forneça uma identificação para o seu dispositivo, se quiser. Se não fornecer uma identificação do dispositivo, o ID de registo é usado.

   4. Selecione **True** para declarar que esta máquina virtual é um dispositivo IoT Edge.

   5. Escolha o IoT Hub ligado ao qual pretende ligar o seu dispositivo ou selecione **Link para o novo Hub IoT**. Pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de atribuição selecionada.

   6. Adicione um valor de etiqueta ao **Estado Gémeo do Dispositivo Inicial,** se quiser. Pode utilizar tags para direcionar grupos de dispositivos para a implementação do módulo. Para obter mais informações, consulte [os módulos IoT Edge em escala.](how-to-deploy-at-scale.md)

   7. Selecione **Guardar**.

Agora que existe uma inscrição para este dispositivo, o tempo de execução IoT Edge pode automaticamente abastecer o dispositivo durante a instalação.

## <a name="install-the-iot-edge-runtime"></a>Instale o tempo de execução IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Os seus componentes funcionam em contentores e permitem-lhe colocar recipientes adicionais no dispositivo para que possa executar código na borda. Instale o tempo de funcionação do IoT Edge na sua máquina virtual.

Conheça o seu **ID Scope DPS** e o **ID de registo** do dispositivo antes de iniciar o artigo que corresponde ao tipo de dispositivo. Se instalou o servidor Ubuntu exemplo, utilize as instruções **x64.** Certifique-se de configurar o tempo de funcionamento IoT Edge para o provisionamento automático e não manual.

Quando chegar ao passo para configurar o daemon de segurança, certifique-se e escolha [a Opção 2 De Provisionamento Automático](how-to-install-iot-edge-linux.md#option-2-automatic-provisioning) e configuração para atestado TPM.

[Instale o tempo de execução Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Dar acesso a IoT Edge ao TPM

O tempo de execução IoT Edge precisa de aceder ao TPM para abastecer automaticamente o seu dispositivo.

Pode dar acesso ao tempo de funcionamento do IoT Edge, sobrepondo as definições sistematadas para que o `iotedge` serviço tenha privilégios de raiz. Se não quiser elevar os privilégios de serviço, também pode usar os seguintes passos para fornecer manualmente acesso a TPM.

1. Encontre o caminho do ficheiro para o módulo de hardware TPM no seu dispositivo e guarde-o como uma variável local.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Crie uma nova regra que dará ao IoT Edge acesso ao tempo de execução tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Abra o arquivo de regras.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copie as seguintes informações de acesso para o ficheiro de regras.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. Guarde e saia do ficheiro.

6. Desencadeie o sistema udev para avaliar a nova regra.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Verifique se a regra foi aplicada com sucesso.

   ```bash
   ls -l /dev/tpm0
   ```

   A saída bem sucedida aparece da seguinte forma:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se não vir que foram aplicadas as permissões corretas, tente reiniciar a sua máquina para refrescar o udev.

## <a name="restart-the-iot-edge-runtime"></a>Reiniciar o tempo de execução IoT Edge

Reinicie o tempo de funcionamento do IoT Edge de modo a que detete todas as alterações de configuração que fez no dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

Verifique se o tempo de funcionamento do IoT Edge está a decorrer.

   ```bash
   sudo systemctl status iotedge
   ```

Se vir erros de provisão, pode ser que as alterações de configuração ainda não tenham feito efeito. Tente reiniciar o daemon IoT Edge novamente.

   ```bash
   sudo systemctl daemon-reload
   ```

Ou tente reiniciar a sua máquina virtual para ver se as alterações fazem efeito num novo começo.

## <a name="verify-successful-installation"></a>Verifique a instalação bem sucedida

Se o tempo de funcionaamento tiver começado com sucesso, pode entrar no seu IoT Hub e ver se o seu novo dispositivo foi automaticamente a provisionado. Agora o seu dispositivo está pronto para executar módulos IoT Edge.

Verifique o estado do IoT Edge Daemon.

```cmd/sh
systemctl status iotedge
```

Examine os registos do daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Listar módulos de execução.

```cmd/sh
iotedge list
```

Pode verificar se foi utilizada a inscrição individual que criou no Serviço de Provisionamento de Dispositivos. Navegue para a sua instância de Serviço de Provisionamento de Dispositivos no portal Azure. Abra os detalhes das inscrições para a inscrição individual que criou. Note que o estado da inscrição é **atribuído** e o ID do dispositivo está listado.

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição DPS permite definir o ID do dispositivo e as etiquetas gémeas do dispositivo ao mesmo tempo que fornece o novo dispositivo. Pode utilizar esses valores para direcionar dispositivos individuais ou grupos de dispositivos utilizando a gestão automática do dispositivo. Aprenda a [implementar e monitorizar os módulos IoT Edge em escala utilizando o portal Azure](how-to-deploy-at-scale.md) ou utilizando o [Azure CLI](how-to-deploy-cli-at-scale.md).

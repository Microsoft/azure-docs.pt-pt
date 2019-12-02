---
title: Provisionar dispositivo com um TPM virtual na VM Linux-Azure IoT Edge
description: Usar um TPM simulado em uma VM do Linux para testar o serviço de provisionamento de dispositivos do Azure para Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8f664a2c503367410507ccba3bc9078d34acbe17
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666346"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Criar e provisionar um dispositivo IoT Edge com um TPM virtual em uma máquina virtual Linux

Azure IoT Edge dispositivos podem ser provisionados automaticamente usando o [serviço de provisionamento de dispositivos](../iot-dps/index.yml). Se você não estiver familiarizado com o processo de provisionamento automático, examine os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra como testar o provisionamento automático em um dispositivo IoT Edge simulado com as seguintes etapas:

* Crie uma VM (máquina virtual) do Linux no Hyper-V com um Trusted Platform Module simulado (TPM) para segurança de hardware.
* Crie uma instância do DPS (serviço de provisionamento de dispositivos) do Hub IoT.
* Criar um registro individual para o dispositivo
* Instalar o IoT Edge Runtime e conectar o dispositivo ao Hub IoT

> [!NOTE]
> O TPM 2,0 é necessário ao usar o atestado de TPM com o DPS e só pode ser usado para criar registros individuais, não de grupo.

> [!TIP]
> Este artigo descreve como testar o provisionamento de DPS usando um simulador de TPM, mas grande parte dele se aplica ao hardware físico do TPM, como o [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), um dispositivo certificado pelo Azure para IOT.
>
> Se você estiver usando um dispositivo físico, poderá pular para a seção [recuperar informações de provisionamento de um dispositivo físico](#retrieve-provisioning-information-from-a-physical-device) neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Um computador de desenvolvimento do Windows com o [Hyper-V habilitado](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Este artigo usa o Windows 10 executando uma VM do servidor Ubuntu.
* Um hub IoT ativo.
* Se você estiver usando um TPM simulado, o [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 ou posterior com a carga de [trabalho ' desenvolvimento de desktop com C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Criar uma máquina virtual Linux com um TPM virtual

Nesta seção, você criará uma nova máquina virtual do Linux no Hyper-V. Você configurou essa máquina virtual com um TPM simulado para que possa usá-la para testar como o provisionamento automático funciona com o IoT Edge. 

### <a name="create-a-virtual-switch"></a>Criar um comutador virtual

Um comutador virtual permite que sua máquina virtual se conecte a uma rede física.

1. Abra o Gerenciador do Hyper-V no computador com Windows. 

2. No menu **ações** , selecione **Gerenciador de comutador virtual**. 

3. Escolha um comutador virtual **externo** e, em seguida, selecione **criar comutador virtual**. 

4. Dê um nome a seu novo comutador virtual, por exemplo, **EdgeSwitch**. Verifique se o tipo de conexão está definido como **rede externa**e, em seguida, selecione **OK**.

5. Um pop-up avisa que a conectividade de rede pode ser interrompida. Selecione **Sim** para continuar. 

Se você vir erros ao criar o novo comutador virtual, certifique-se de que nenhuma outra opção esteja usando o adaptador Ethernet e que nenhuma outra opção use o mesmo nome. 

### <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Baixe um arquivo de imagem de disco para usar em sua máquina virtual e salve-o localmente. Por exemplo, [servidor Ubuntu](https://www.ubuntu.com/download/server). 

2. No Gerenciador do Hyper-V novamente, selecione **novo** > **máquina virtual** no menu **ações** .

3. Conclua o **Assistente de nova máquina virtual** com as seguintes configurações específicas:

   1. **Especificar geração**: selecione **geração 2**. As máquinas virtuais de geração 2 têm virtualização aninhada habilitada, que é necessária para executar IoT Edge em uma máquina virtual.
   2. **Configurar rede**: defina o valor de **conexão** com o comutador virtual que você criou na seção anterior. 
   3. **Opções de instalação**: selecione **instalar um sistema operacional de um arquivo de imagem inicializável** e navegue até o arquivo de imagem de disco que você salvou localmente.

4. Selecione **concluir** no Assistente para criar a máquina virtual.

Pode levar alguns minutos para criar a nova VM. 

### <a name="enable-virtual-tpm"></a>Habilitar TPM virtual

Depois que a VM for criada, abra suas configurações para habilitar o TPM (Trusted Platform Module) que permite que você provisione automaticamente o dispositivo.

1. Selecione a máquina virtual e, em seguida, abra suas **configurações**.

2. Navegue até **segurança**. 

3. Desmarque **habilitar inicialização segura**.

4. Marque **habilitar Trusted Platform Module**. 

5. Clique em **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Iniciar a máquina virtual e coletar dados do TPM

Na máquina virtual, crie uma ferramenta que você possa usar para recuperar a **ID de registro** e a **chave de endosso**do dispositivo.

1. Inicie sua máquina virtual e conecte-se a ela.

1. Siga os prompts na máquina virtual para concluir o processo de instalação e reinicializar o computador.

1. Entre em sua VM e siga as etapas em [configurar um ambiente de desenvolvimento do Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar e criar o SDK do dispositivo IOT do Azure para C.

   >[!TIP]
   >No decorrer deste artigo, você copiará e colará a partir da máquina virtual, o que não é fácil por meio do aplicativo de conexão do Gerenciador do Hyper-V. Talvez você queira se conectar à máquina virtual por meio do Gerenciador do Hyper-V uma vez para recuperar seu endereço IP: `ifconfig`. Em seguida, você pode usar o endereço IP para se conectar por meio de SSH: `ssh <username>@<ipaddress>`.

1. Execute os comandos a seguir para criar a ferramenta SDK que recupera as informações de provisionamento do dispositivo do simulador TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Em uma janela de comando, navegue até o diretório `azure-iot-sdk-c` e execute o simulador de TPM. O simulador escuta através de um socket nas portas 2321 e 2322. Não feche esta janela de comando; Você precisará manter este simulador em execução.

   No diretório `azure-iot-sdk-c`, execute o seguinte comando para iniciar o simulador:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Usando o Visual Studio, abra a solução gerada no diretório `cmake` chamado `azure_iot_sdks.sln`e Compile-a usando o comando **Compilar solução** no menu **Compilar** .

1. No painel **Explorador de Soluções** do Visual Studio, navegue para a pasta **Aprovisionar\_Ferramentas**. Clique com o botão direito do rato no projeto **tpm_device_provision** e selecione **Configurar como Projeto de Arranque**.

1. Execute a solução usando um dos comandos **Iniciar** no menu **depurar** . A janela saída exibe a **ID de registro** do simulador do TPM e a **chave de endosso**, que você deve copiar para uso posterior ao criar um registro individual para seu dispositivo no você pode fechar esta janela (com a ID de registro e a chave de endosso), mas deixar a janela do simulador do TPM em execução.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Recuperar informações de provisionamento de um dispositivo físico

Em seu dispositivo, crie uma ferramenta que você possa usar para recuperar as informações de provisionamento do dispositivo.

1. Siga as etapas em [configurar um ambiente de desenvolvimento do Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar e criar o SDK do dispositivo IOT do Azure para C.

1. Execute os comandos a seguir para criar a ferramenta SDK que recupera as informações de provisionamento do dispositivo do dispositivo TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copie os valores para **ID de registro** e **chave de endosso**. Você usa esses valores para criar um registro individual para seu dispositivo no DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de provisionamento de dispositivos no Hub IoT

Crie uma nova instância do serviço de provisionamento de dispositivos do Hub IoT no Azure e vincule-o ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IOT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o serviço de provisionamento de dispositivos, copie o valor do **escopo da ID** da página Visão geral. Você usa esse valor quando configura o tempo de execução de IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Criar um registro de DPS

Recupere as informações de provisionamento de sua máquina virtual e use-as para criar um registro individual no serviço de provisionamento de dispositivos. 

Ao criar um registro no DPS, você tem a oportunidade de declarar um **estado inicial**de um dispositivo. No dispositivo, você pode definir marcas para agrupar dispositivos por qualquer métrica necessária em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md). 

1. Na [portal do Azure](https://portal.azure.com), navegue até sua instância do serviço de provisionamento de dispositivos do Hub IOT. 

2. Em **configurações**, selecione **gerenciar registros**. 

3. Selecione **adicionar registro individual** e conclua as seguintes etapas para configurar o registro:  

   1. Para **mecanismo**, selecione **TPM**. 

   2. Forneça a **chave de endosso** e a **ID de registro** que você copiou de sua máquina virtual.

      > [!TIP]
      > Se você estiver usando um dispositivo TPM físico, você precisará determinar a **chave de endosso**, que é exclusiva de cada chip TPM e é obtida do fabricante do chip TPM associado a ela. Você pode derivar uma **ID de registro** exclusiva para seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.

   3. Selecione **true** para declarar que esta máquina virtual é um dispositivo IOT Edge. 

   4. Escolha o **Hub IOT** vinculado ao qual você deseja conectar o dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada. 

   5. Forneça uma ID para seu dispositivo, se desejar. Você pode usar IDs de dispositivo para direcionar um dispositivo individual para implantação de módulo. Se você não fornecer uma ID de dispositivo, a ID de registro será usada.

   6. Se desejar, adicione um valor de marca ao **estado inicial do dispositivo** . Você pode usar marcas para direcionar grupos de dispositivos para implantação de módulo. Por exemplo: 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Selecione **Guardar**. 

Agora que um registro existe para esse dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação. 

## <a name="install-the-iot-edge-runtime"></a>Instalar o IoT Edge Runtime

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Seus componentes são executados em contêineres e permitem que você implante contêineres adicionais no dispositivo para que você possa executar o código na borda. Instale o IoT Edge Runtime em sua máquina virtual. 

Conheça o **escopo da ID** de DPS e a **ID de registro** do dispositivo antes de iniciar o artigo que corresponde ao seu tipo de dispositivo. Se você instalou o servidor Ubuntu de exemplo, use as instruções **x64** . Certifique-se de configurar o tempo de execução de IoT Edge para o provisionamento automático, não manual. 

[Instalar o tempo de execução de Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Conceder IoT Edge acesso ao TPM

Para que o tempo de execução de IoT Edge provisione automaticamente seu dispositivo, ele precisa acessar o TPM. 

Você pode conceder acesso de TPM ao tempo de execução de IoT Edge substituindo as configurações do sistema para que o serviço **iotedge** tenha privilégios de raiz. Se você não quiser elevar os privilégios de serviço, também poderá usar as etapas a seguir para fornecer manualmente o acesso ao TPM. 

1. Localize o caminho do arquivo para o módulo de hardware do TPM em seu dispositivo e salve-o como uma variável local. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Crie uma nova regra que fornecerá ao IoT Edge o acesso de tempo de execução ao tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Abra o arquivo de regras. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copie as informações de acesso a seguir no arquivo de regras. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Salve e saia do arquivo. 

6. Dispare o sistema udev para avaliar a nova regra. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Verifique se a regra foi aplicada com êxito.

   ```bash
   ls -l /dev/tpm0
   ```

   A saída bem-sucedida é semelhante ao seguinte:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se você não vir que as permissões corretas foram aplicadas, tente reinicializar o computador para atualizar o udev. 

## <a name="restart-the-iot-edge-runtime"></a>Reiniciar o tempo de execução IoT Edge

Reinicie o tempo de execução do IoT Edge para que ele escolha todas as alterações de configuração feitas no dispositivo. 

   ```bash
   sudo systemctl restart iotedge
   ```

Verifique se o tempo de execução do IoT Edge está em execução. 

   ```bash
   sudo systemctl status iotedge
   ```

Se você vir erros de provisionamento, pode ser que as alterações de configuração ainda não tenham efeito. Tente reiniciar o daemon de IoT Edge novamente. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Ou então, tente reiniciar sua máquina virtual para ver se as alterações entram em vigor em um novo início. 

## <a name="verify-successful-installation"></a>Verificar a instalação bem-sucedida

Se o tempo de execução for iniciado com êxito, você poderá entrar no Hub IoT e ver se o novo dispositivo foi provisionado automaticamente. Agora seu dispositivo está pronto para executar módulos IoT Edge. 

Verifique o status do daemon de IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os logs do daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Listar módulos em execução.

```cmd/sh
iotedge list
```

Você pode verificar se o registro individual criado no serviço de provisionamento de dispositivos foi usado. Navegue até a instância do serviço de provisionamento de dispositivos no portal do Azure. Abra os detalhes de registro para o registro individual que você criou. Observe que o status do registro é **atribuído** e a ID do dispositivo é listada. 

## <a name="next-steps"></a>Passos seguintes

O processo de registro do serviço de provisionamento de dispositivos permite definir a ID do dispositivo e as marcas de configuração do dispositivo ao mesmo tempo que você provisiona o novo dispositivo. Você pode usar esses valores para direcionar dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivos. Saiba como [implantar e monitorar módulos IOT Edge em escala usando o portal do Azure](how-to-deploy-monitor.md) ou [usando CLI do Azure](how-to-deploy-monitor-cli.md).

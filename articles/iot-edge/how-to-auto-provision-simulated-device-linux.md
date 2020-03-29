---
title: Dispositivo de fornecimento com um TPM virtual em Linux VM - Azure IoT Edge
description: Utilize um TPM simulado num VM Linux para testar o Serviço de Provisionamento de Dispositivos Azure para o Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511064"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Crie e forre um dispositivo IoT Edge com um TPM virtual numa máquina virtual Linux

Os dispositivos Azure IoT Edge podem ser automaticamente aprovisionados utilizando o Serviço de Provisionamento de [Dispositivos](../iot-dps/index.yml). Se não estiver familiarizado com o processo de fornecimento automático, reveja os [conceitos de fornecimento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra-lhe como testar o fornecimento automático num dispositivo IoT Edge simulado com os seguintes passos:

* Crie uma máquina virtual Linux (VM) em Hyper-V com um módulo de plataforma fidedigno simulado (TPM) para segurança de hardware.
* Crie uma instância do Serviço de Provisionamento de Dispositivos IoT Hub (DPS).
* Criar uma inscrição individual para o dispositivo
* Instale o tempo de execução do IoT Edge e ligue o dispositivo ao IoT Hub

> [!TIP]
> Este artigo descreve como testar o fornecimento de DPS utilizando um simulador TPM, mas grande parte dele aplica-se a hardware tpm físico, como o [Infineon&trade; OPTIGA TPM,](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)um Dispositivo Azure Certified for IoT.
>
> Se estiver a utilizar um dispositivo físico, pode avançar para o [fornecimento de informações do Recuperado](#retrieve-provisioning-information-from-a-physical-device) a partir de uma secção de dispositivo físico neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma máquina de desenvolvimento do Windows com [hiper-V ativada](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Este artigo utiliza o Windows 10 executando um Ubuntu Server VM.
* Um hub ativo.
* Se utilizar um TPM simulado, [o Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 ou mais tarde com a carga de trabalho ['Desktop com C++'ativada.](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)

> [!NOTE]
> TPM 2.0 é necessário quando se utiliza atestado TPM com DPS e só pode ser usado para criar matrículas individuais, não em grupo.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Crie uma máquina virtual Linux com um TPM virtual

Nesta secção, cria-se uma nova máquina virtual Linux no Hyper-V. Configurou esta máquina virtual com um TPM simulado para que possa usá-la para testar como o fornecimento automático funciona com o IoT Edge.

### <a name="create-a-virtual-switch"></a>Criar um comutador virtual

Um interruptor virtual permite que a sua máquina virtual se conectem a uma rede física.

1. Abra o Gestor de Hiper-V na sua máquina Windows.

2. No menu **Ações,** selecione **Virtual Switch Manager**.

3. Escolha um interruptor virtual **Externo** e, em seguida, selecione **Create Virtual Switch**.

4. Dê ao seu novo interruptor virtual um nome, por **exemplo, EdgeSwitch**. Certifique-se de que o tipo de ligação está definido para **a rede Externa**e, em seguida, selecione **Ok**.

5. Um pop-up avisa-o de que a conectividade da rede pode ser interrompida. Selecione **Sim** para continuar.

Se vir erros ao criar o novo interruptor virtual, certifique-se de que nenhum outro interruptor está a utilizar o adaptador ethernet e que nenhum outro interruptor usa o mesmo nome.

### <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Descarregue um ficheiro de imagem em disco para usar para a sua máquina virtual e guarde-o localmente. Por exemplo, [servidor Ubuntu](https://www.ubuntu.com/download/server).

2. No Hiper-V Manager novamente, selecione **New** > **Virtual Machine** no menu **Ações.**

3. Complete o **Novo Assistente de Máquina Virtual** com as seguintes configurações específicas:

   1. **Especificar Geração**: Selecione **Geração 2**. As máquinas virtuais da Geração 2 têm uma virtualização aninhada, que é necessária para executar IoT Edge numa máquina virtual.
   2. **Configurar a rede**: Desloque o valor da **Ligação** ao interruptor virtual que criou na secção anterior.
   3. **Opções de instalação**: Selecione Instale um sistema operativo a partir de um ficheiro de **imagem sabotável** e navegue até ao ficheiro de imagem do disco que guardou localmente.

4. Selecione **Terminar** no assistente para criar a máquina virtual.

Pode levar alguns minutos para criar o novo VM.

### <a name="enable-virtual-tpm"></a>Ativar TPM virtual

Assim que o VM for criado, abra as suas definições para ativar o módulo de plataforma de confiança virtual (TPM) que permite fornecer automaticamente o dispositivo.

1. Selecione a máquina virtual e abra as **definições**.

2. Navegue para **a Segurança.**

3. Descontrolar **a bota segura de ativação**.

4. Verifique **ativar módulo de plataforma fidedigno**.

5. Clique em **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Inicie a máquina virtual e recolha dados do TPM

Na máquina virtual, construa uma ferramenta que possa utilizar para recuperar o ID de **Registo** e **a chave de averbamento**do dispositivo .

1. Ligue a sua máquina virtual e ligue-a.

1. Siga as instruções dentro da máquina virtual para terminar o processo de instalação e reiniciar a máquina.

1. Inscreva-se no seu VM e, em seguida, siga os passos em [Configurar um ambiente](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) de desenvolvimento Linux para instalar e construir o dispositivo Azure IoT SDK para C.

   >[!TIP]
   >No decorrer deste artigo, você copiará e colará da máquina virtual, o que não é fácil através da aplicação de ligação Hyper-V Manager. Pode querer ligar-se à máquina virtual através do Hyper-V `ifconfig`Manager uma vez para recuperar o seu endereço IP: . Em seguida, pode utilizar o endereço IP `ssh <username>@<ipaddress>`para ligar através do SSH: .

1. Execute os seguintes comandos para construir a ferramenta SDK que recupere as informações de fornecimento do dispositivo do simulador TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. A partir de uma `azure-iot-sdk-c` janela de comando, navegue até ao diretório e execute o simulador TPM. O simulador escuta através de um socket nas portas 2321 e 2322. Não feche esta janela de comando; terá de manter este simulador a funcionar.

   A `azure-iot-sdk-c` partir do diretório, execute o seguinte comando para iniciar o simulador:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Utilizando o Visual Studio, abra `cmake` a solução gerada no diretório nomeado `azure_iot_sdks.sln`, e construa-a utilizando o comando build **solution** no menu **Build.**

1. No painel **Explorador de Soluções** do Visual Studio, navegue para a pasta **Aprovisionar\_Ferramentas**. Clique com o botão direito do rato no projeto **tpm_device_provision** e selecione **Configurar como Projeto de Arranque**.

1. Executar a solução utilizando qualquer um dos comandos **Iniciar** no menu **Debug.** A janela de saída apresenta o ID de **registo** do simulador TPM e a **chave de averbamento,** que deverá copiar para utilização mais tarde quando criar uma inscrição individual para o seu dispositivo em Pode fechar esta janela (com id de registo e chave de averbamento), mas deixar a janela do simulador TPM em funcionamento.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Recuperar informações de fornecimento de um dispositivo físico

No seu dispositivo, construa uma ferramenta que possa utilizar para recuperar as informações de fornecimento do dispositivo.

1. Siga os passos em [Configurar um ambiente](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) de desenvolvimento Linux para instalar e construir o dispositivo Azure IoT SDK para C.

1. Execute os seguintes comandos para construir a ferramenta SDK que recupere as informações de fornecimento do dispositivo do dispositivo TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copie os valores para **identificação de registo** e **chave de averbamento**. Utiliza estes valores para criar uma inscrição individual para o seu dispositivo em DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de provisionamento de dispositivos IoT Hub

Crie uma nova instância do Serviço de Provisionamento de Dispositivos IoT Hub em Azure e ligue-o ao seu hub IoT. Pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de ter o Serviço de Provisionamento de Dispositivos em funcionamento, copie o valor do **ID Scope** a partir da página de visão geral. Utiliza este valor quando configura o tempo de execução do IoT Edge.

## <a name="create-a-dps-enrollment"></a>Criar uma inscrição dPS

Recupere as informações de provisionamento da sua máquina virtual e use-as para criar uma inscrição individual no Serviço de Provisionamento de Dispositivos.

Ao criar uma inscrição em DPS, tem a oportunidade de declarar um **Dispositivo Inicial Twin State**. No dispositivo twin, pode definir etiquetas para agrupar dispositivos por qualquer métrica que necessite na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas](how-to-deploy-monitor.md).

1. No [portal Azure,](https://portal.azure.com)navegue para a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

2. Em **Definições,** **selecione Gerir as matrículas**.

3. **Selecione Adicionar inscrição individual** e, em seguida, completar os seguintes passos para configurar a inscrição:  

   1. Para **mecanismos,** selecione **TPM**.

   2. Forneça a chave de **averbamento** e **o ID** de registo que copiou da sua máquina virtual.

      > [!TIP]
      > Se estiver a utilizar um dispositivo TPM físico, tem de determinar a **chave De aval,** que é única em cada chip TPM e é obtida junto do fabricante de chips TPM associado ao mesmo. Pode obter um ID de **registo** único para o seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.

   3. Selecione **True** para declarar que esta máquina virtual é um dispositivo IoT Edge.

   4. Escolha o **IoT Hub** ligado ao qual pretende ligar o seu dispositivo. Pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de atribuição selecionada.

   5. Forneça uma identificação para o seu dispositivo, se quiser. Pode utilizar iDs do dispositivo para direcionar um dispositivo individual para a implementação do módulo. Se não fornecer uma identificação do dispositivo, o ID de registo é utilizado.

   6. Adicione um valor de etiqueta ao **Dispositivo Inicial Twin State,** se quiser. Pode utilizar etiquetas para grupos-alvo de dispositivos para a implementação de módulos. Por exemplo:

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

Agora que existe uma inscrição para este dispositivo, o tempo de funcionamento do IoT Edge pode fornecer automaticamente o dispositivo durante a instalação.

## <a name="install-the-iot-edge-runtime"></a>Instale o tempo de execução do IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Os seus componentes funcionam em contentores e permitem-lhe colocar recipientes adicionais no dispositivo para que possa executar código na borda. Instale o tempo de execução do IoT Edge na sua máquina virtual.

Conheça o seu **DPS** **ID Scope** e o id de registo do dispositivo antes de iniciar o artigo que corresponde ao tipo do seu dispositivo. Se instalou o exemplo do servidor Ubuntu, utilize as instruções **x64.** Certifique-se de configurar o tempo de funcionamento do IoT Edge para o fornecimento automático, não manual.

[Instale o tempo de execução do Azure IoT Edge em Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Dê acesso ioT Edge ao TPM

Para que o tempo de funcionamento do IoT Edge aprovisione automaticamente o seu dispositivo, necessita de acesso ao TPM.

Pode dar acesso ao tempo de funcionação do IoT Edge, ultrapassando as definições sistematas, de modo a que o serviço **iotedge** tenha privilégios de raiz. Se não quiser elevar os privilégios de serviço, também pode usar os seguintes passos para fornecer manualmente acesso tPM.

1. Encontre o caminho de ficheiro para o módulo de hardware TPM no seu dispositivo e guarde-o como uma variável local.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Crie uma nova regra que dará ao IoT Edge acesso ao tempo de corrida ao TPM0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Abra o arquivo de regras.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copie as seguintes informações de acesso no ficheiro de regras.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Salve e saia do ficheiro.

6. Desencadear o sistema udev para avaliar a nova regra.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Verifique se a regra foi aplicada com sucesso.

   ```bash
   ls -l /dev/tpm0
   ```

   A saída bem sucedida parece ser a seguinte:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se não vir que foram aplicadas as permissões corretas, tente reiniciar a sua máquina para atualizar o udev.

## <a name="restart-the-iot-edge-runtime"></a>Reinicie o tempo de execução da Borda IoT

Reinicie o tempo de funcionamento do IoT Edge de modo a captar todas as alterações de configuração que fez no dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

Verifique se o tempo de funcionamento do IoT Edge está a decorrer.

   ```bash
   sudo systemctl status iotedge
   ```

Se vir erros de provisionamento, pode ser que as alterações de configuração ainda não tenham feito efeito. Tente reiniciar o daemon IoT Edge novamente.

   ```bash
   sudo systemctl daemon-reload
   ```

Ou, tente reiniciar a sua máquina virtual para ver se as alterações têm efeito num novo começo.

## <a name="verify-successful-installation"></a>Verificar instalação bem sucedida

Se o tempo de funcionar começar com sucesso, pode entrar no seu IoT Hub e ver se o seu novo dispositivo foi automaticamente aprovisionado. Agora o seu dispositivo está pronto para executar módulos IoT Edge.

Verifique o estado do IoT Edge Daemon.

```cmd/sh
systemctl status iotedge
```

Examine os registos de daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista de módulos de execução.

```cmd/sh
iotedge list
```

Pode verificar se foi utilizada a inscrição individual que criou no Serviço de Provisionamento de Dispositivos. Navegue para a sua instância de Serviço de Provisionamento de Dispositivos no portal Azure. Abra os detalhes das inscrições para a inscrição individual que criou. Note que o estado da inscrição está **atribuído** e o ID do dispositivo está listado.

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do Serviço de Provisionamento de Dispositivos permite-lhe definir as etiquetas duplas de identificação do dispositivo e dispositivo ao mesmo tempo que disponibiliza o novo dispositivo. Pode utilizar esses valores para direcionar dispositivos ou grupos individuais de dispositivos utilizando a gestão automática do dispositivo. Aprenda a [implantar e monitorizar os módulos IoT Edge em escala utilizando o portal Azure](how-to-deploy-monitor.md) ou utilizando o [Azure CLI](how-to-deploy-monitor-cli.md).

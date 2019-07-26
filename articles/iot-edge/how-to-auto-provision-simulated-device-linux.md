---
title: Provisionar automaticamente dispositivos Linux com Azure IoT Edge de DPS | Microsoft Docs
description: Utilizar um TPM simulado numa VM do Linux para testar o serviço de aprovisionamento de dispositivos do Azure para o Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b48455b6ea9c1cd74e94c10d8f9f938c20512c02
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414572"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Criar e provisionar um dispositivo IoT Edge com um TPM virtual em uma máquina virtual Linux

Azure IoT Edge dispositivos podem ser provisionados automaticamente usando o [serviço de provisionamento de dispositivos](../iot-dps/index.yml). Se não estiver familiarizado com o processo de autoprovisioning, reveja os [autoprovisioning conceitos](../iot-dps/concepts-auto-provisioning.md) antes de continuar. 

Este artigo mostra como testar o provisionamento automático em um dispositivo IoT Edge simulado com as seguintes etapas: 

* Crie uma máquina virtual (VM) do Linux no Hyper-V com um simulado Trusted Platform Module (TPM) para a segurança de hardware.
* Crie uma instância do IoT Hub dispositivo aprovisionamento DPS (serviço).
* Criar uma inscrição individual para o dispositivo
* Instalar o runtime do IoT Edge e ligar o dispositivo ao IoT Hub

Os passos neste artigo destinam-se para fins de teste.

## <a name="prerequisites"></a>Pré-requisitos

* Uma máquina de desenvolvimento do Windows com [Hyper-V ativada](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Este artigo utiliza o Windows 10 com uma VM do Ubuntu Server. 
* Um IoT Hub Active Directory. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Criar uma máquina virtual Linux com um TPM virtual

Nesta seção, você criará uma nova máquina virtual do Linux no Hyper-V. Você configurou essa máquina virtual com um TPM simulado para que possa usá-la para testar como o provisionamento automático funciona com o IoT Edge. 

### <a name="create-a-virtual-switch"></a>Criar um comutador virtual

Um comutador virtual permite que sua máquina virtual ligar a uma rede física.

1. Abra o Gerenciador do Hyper-V no computador com Windows. 

2. Na **ações** menu, selecione **Gestor de comutadores virtuais**. 

3. Escolher uma **externo** virtual mudar, em seguida, selecione **criar comutador Virtual**. 

4. O novo comutador virtual dê um nome ao, por exemplo **EdgeSwitch**. Certifique-se de que o tipo de ligação está definido como **rede externa**, em seguida, selecione **Ok**.

5. Um pop-up avisa-o de que a conectividade de rede pode ser interrompida. Selecione **Sim** para continuar. 

Se vir um erro ao criar o novo comutador virtual, certifique-se de que não existem outros comutadores estão a utilizar o adaptador de ethernet, e que não existem outros comutadores, utilize o mesmo nome. 

### <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Transferir um ficheiro de imagem de disco a utilizar para a máquina virtual e guarde-o localmente. Por exemplo, [Ubuntu server](https://www.ubuntu.com/download/server). 

2. No Gerenciador do Hyper-V novamente, selecione **nova** > **máquina virtual** no menu **ações** .

3. Concluir o **Assistente de Nova Máquina Virtual** com as seguintes configurações específicas:

   1. **Especificar geração**: Selecione **geração 2**. As máquinas virtuais de geração 2 têm virtualização aninhada habilitada, que é necessária para executar IoT Edge em uma máquina virtual.
   2. **Configurar rede**: Defina o valor de **conexão** com o comutador virtual que você criou na seção anterior. 
   3. **Opções de instalação**: Selecione **instalar um sistema operacional de um arquivo de imagem inicializável** e navegue até o arquivo de imagem de disco que você salvou localmente.

4. Selecione **concluir** no Assistente para criar a máquina virtual.

Pode demorar alguns minutos para criar a nova VM. 

### <a name="enable-virtual-tpm"></a>Ativar o virtual TPM

Depois que a VM for criada, abra suas configurações para habilitar o TPM (Trusted Platform Module) que lhe permite provisionar o dispositivo. 

1. Selecione a máquina virtual e, em seguida, abra suas **configurações**.

2. Navegue para **segurança**. 

3. Desmarque **ativar o arranque seguro**.

4. Verifique **Enable Trusted Platform Module**. 

5. Clique em **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Iniciar a máquina virtual e recolher dados TPM

Na máquina virtual, criar uma ferramenta de SDK de C que pode usar para recuperar do dispositivo **ID de registo** e **chave de endossamento**. 

1. Inicie sua máquina virtual e conecte-se a ela.

2. Siga os prompts na máquina virtual para concluir o processo de instalação e reinicializar o computador. 

3. Entre em sua VM e siga as etapas em [configurar um ambiente de desenvolvimento do Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar e criar o SDK do dispositivo IOT do Azure para C. 

   >[!TIP]
   >No decorrer deste artigo, você copiará e colará a partir da máquina virtual, o que não é fácil por meio do aplicativo de conexão do Gerenciador do Hyper-V. Talvez você queira se conectar à máquina virtual por meio do Gerenciador do Hyper-V uma vez para recuperar `ifconfig`seu endereço IP:. Em seguida, você pode usar o endereço IP para se conectar por `ssh <username>@<ipaddress>`meio de SSH:.

4. Execute os seguintes comandos para criar uma ferramenta do SDK de C que obtém as informações de aprovisionamento de dispositivo. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```
   >[!TIP]
   >Se você estiver testando com o simulador TPM, precisará colocar um parâmetro `-Duse_tpm_simulator:BOOL=ON` extra para habilitá-lo. O comando completo será `cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..`.

5. Copie os valores de **ID de registo** e **chave de endossamento**. Utilize estes valores para criar uma inscrição individual para o seu dispositivo no DPS. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de aprovisionamento de dispositivos do IoT Hub

Criar uma nova instância do serviço de aprovisionamento de dispositivos do IoT Hub no Azure e ligá-lo ao seu hub IoT. Pode seguir as instruções em [configurar o Hub IoT DPS](../iot-dps/quick-setup-auto-provision.md).

Depois de ter a execução do serviço aprovisionamento de dispositivos, copie o valor da **âmbito do ID** da página de descrição geral. Utilize este valor quando configurar o runtime do IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Criar uma inscrição de DPS

Obter as informações de aprovisionamento da sua máquina virtual e usá-lo para criar uma inscrição individual no serviço aprovisionamento de dispositivos. 

Quando criar uma inscrição em pontos de distribuição, terá a oportunidade para declarar uma **inicial estado do dispositivo duplo**. No dispositivo duplo, pode definir etiquetas para agrupar dispositivos com qualquer métrica que terá na sua solução, como o tipo de dispositivo, localização, ambiente ou região. Essas marcas são usadas para criar [implementações automáticas](how-to-deploy-monitor.md). 

1. Na [portal do Azure](https://portal.azure.com), navegue até sua instância do serviço de provisionamento de dispositivos do Hub IOT. 

2. Sob **configurações**, selecione **gerir inscrições**. 

3. Selecione **adicionar inscrição individual** , em seguida, conclua os seguintes passos para configurar a inscrição:  

   1. Para **mecanismo**, selecione **TPM**. 
   
   2. Forneça a **chave de endosso** e a **ID de registro** que você copiou de sua máquina virtual.
   
   3. Selecione **true** para declarar que esta máquina virtual é um dispositivo IOT Edge. 
   
   4. Escolha associada **IoT Hub** que pretende ligar o seu dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada. 
   
   5. Forneça um ID para o seu dispositivo se desejar. Pode usar as identificações de dispositivo para um dispositivo individual para implementação do módulo de destino. Se você não fornecer uma ID de dispositivo, a ID de registro será usada.
   
   6. Adicionar um valor de etiqueta para o **inicial estado do dispositivo duplo** se desejar. Pode utilizar etiquetas para grupos de dispositivos de destino para implementação do módulo. Por exemplo: 

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

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Seus componentes executadas em contentores e permitem-lhe implementar contentores adicionais no dispositivo para que pode executar o código na periferia. Instale o runtime do IoT Edge na sua máquina virtual. 

Saber o DPS **âmbito do ID** e o dispositivo **ID de registo** antes de iniciar o artigo que corresponda ao seu tipo de dispositivo. Se tiver instalado o servidor Ubuntu de exemplo, utilize o **x64** instruções. Certifique-se configurar o runtime do IoT Edge para aprovisionamento automático e não manual. 

[Instalar o tempo de execução de Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Conceder acesso de IoT Edge para o TPM

Para o runtime do IoT Edge aprovisionar automaticamente o seu dispositivo, ele tem acesso para o TPM. 

Pode conceder acesso TPM para o runtime do IoT Edge ao substituir as definições de systemd para que o **iotedge** serviço tem privilégios de raiz. Se não quiser elevar os privilégios de serviço, também pode utilizar os seguintes passos para fornecer manualmente o acesso TPM. 

1. Localizar o caminho de ficheiro para o módulo de hardware TPM no seu dispositivo e guarde-o como uma variável local. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Crie uma nova regra que vai dar acesso de tempo de execução o IoT Edge para tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Abra o ficheiro de regras. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copie as seguintes informações de acesso para o ficheiro de regras. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Guardar e sair o ficheiro. 

6. Acione o sistema de udev para avaliar a nova regra. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Certifique-se de que a regra foi aplicada com êxito.

   ```bash
   ls -l /dev/tpm0
   ```

   Saída bem-sucedido é semelhante ao seguinte:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se não vir a que foram aplicadas as permissões corretas, tente reiniciar o computador para atualizar o udev. 

8. Abra o runtime do IoT Edge substitui ficheiros. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Adicione o seguinte código para estabelecer uma variável de ambiente de TPM.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

10. Guardar e sair o ficheiro.

11. Certifique-se de que a substituição foi concluída com êxito.

    ```bash
    sudo systemctl cat iotedge.service
    ```

    Saída com êxito apresenta a **iotedge** predefinido variáveis de serviço e, em seguida, mostra a variável de ambiente que defina **override.conf**. 

12. Volte a carregar as definições.

    ```bash
    sudo systemctl daemon-reload
    ```

## <a name="restart-the-iot-edge-runtime"></a>Reiniciar o runtime do IoT Edge

Reinicie o runtime do IoT Edge, para que ele seleciona todas as alterações de configuração feitas no dispositivo. 

   ```bash
   sudo systemctl restart iotedge
   ```

Certifique-se de que o runtime do IoT Edge está em execução. 

   ```bash
   sudo systemctl status iotedge
   ```

Se vir erros de aprovisionamento, pode ser que as alterações de configuração ainda não foram aplicadas ainda. Tente reiniciar o daemon de IoT Edge novamente. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Em alternativa, tente reiniciar a máquina virtual para ver se as alterações entrem em vigor no começar do zero. 

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Se o tempo de execução foi iniciado com êxito, pode entrar no seu IoT Hub e ver que o seu dispositivo novo foi automaticamente aprovisionado. O dispositivo está agora pronto para executar os módulos do IoT Edge. 

Verificar o estado de que o Daemon do IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os registos de daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista de módulos em execução.

```cmd/sh
iotedge list
```

Você pode verificar se o registro individual criado no serviço de provisionamento de dispositivos foi usado. Navegue até a instância do serviço de provisionamento de dispositivos no portal do Azure. Abra os detalhes de registro para o registro individual que você criou. Observe que o status do registro é **atribuído** e a ID do dispositivo é listada. 

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do serviço aprovisionamento de dispositivos permite-lhe definir o ID de dispositivo e etiquetas do dispositivo duplo ao mesmo tempo, como aprovisionar o novo dispositivo. Pode utilizar esses valores para dispositivos individuais ou grupos de dispositivos com a gestão de dispositivos automático de destino. Saiba como [implementar e monitorizar módulos em dimensionar no portal do Azure do IoT Edge](how-to-deploy-monitor.md) ou [com a CLI do Azure](how-to-deploy-monitor-cli.md).

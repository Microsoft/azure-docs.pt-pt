---
title: Instalar um servidor de destino mestre do Linux para failback em um site local | Microsoft Docs
description: Saiba como configurar um servidor de destino mestre do Linux para failback para um site local durante a recuperação de desastre de VMs VMware no Azure usando Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 062ed5e408317e95b36d6d0dfa395311ed4afe7f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261422"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalar um servidor de destino mestre do Linux para failback
Depois de fazer failover de suas máquinas virtuais para o Azure, você pode executar failback das máquinas virtuais para o site local. Para fazer failback, você precisa proteger novamente a máquina virtual do Azure para o site local. Para esse processo, você precisa de um servidor de destino mestre local para receber o tráfego. 

Se sua máquina virtual protegida for uma máquina virtual do Windows, você precisará de um destino mestre do Windows. Para uma máquina virtual do Linux, você precisa de um destino mestre do Linux. Leia as etapas a seguir para saber como criar e instalar um destino mestre do Linux.

> [!IMPORTANT]
> A partir do lançamento do servidor de destino mestre do 9.10.0, o servidor de destino mestre mais recente só pode ser instalado em um servidor Ubuntu 16, 4. Novas instalações não são permitidas em servidores CentOS 6.6. No entanto, você pode continuar a atualizar seus servidores de destino mestre antigos usando a versão 9.10.0.
> Não há suporte para o servidor de destino mestre no LVM.

## <a name="overview"></a>Descrição geral
Este artigo fornece instruções sobre como instalar um destino mestre do Linux.

Poste comentários ou perguntas no final deste artigo ou no [Fórum dos serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos

* Para escolher o host no qual o destino mestre será implantado, determine se o failback vai ser para uma máquina virtual local existente ou para uma nova máquina virtual. 
    * Para uma máquina virtual existente, o host do destino mestre deve ter acesso aos armazenamentos de dados da máquina virtual.
    * Se a máquina virtual local não existir (no caso de recuperação de local alternativo), a máquina virtual de failback será criada no mesmo host que o destino mestre. Você pode escolher qualquer host ESXi para instalar o destino mestre.
* O destino mestre deve estar em uma rede que possa se comunicar com o servidor de processo e com o servidor de configuração.
* A versão do destino mestre deve ser igual ou anterior às versões do servidor de processo e do servidor de configuração. Por exemplo, se a versão do servidor de configuração for 9,4, a versão do destino mestre poderá ser 9,4 ou 9,3, mas não 9,5.
* O destino mestre só pode ser uma máquina virtual VMware e não um servidor físico.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Diretrizes de dimensionamento para criar o servidor de destino mestre

Crie o destino mestre de acordo com as seguintes diretrizes de dimensionamento:
- **RAM**: 6 GB ou mais
- **Tamanho do disco do so**: 100 GB ou mais (para instalar o sistema operacional)
- **Tamanho de disco adicional para a unidade de retenção**: 1 TB
- **Núcleos de CPU**: 4 núcleos ou mais

Há suporte para os seguintes kernels do Ubuntu.


|Série kernel  |Suporte para até  |
|---------|---------|
|4.4      |4.4.0-81-Generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>Implantar o servidor de destino mestre

### <a name="install-ubuntu-16042-minimal"></a>Instalar o Ubuntu 16.04.2 Minimal mínimo

Execute as etapas a seguir para instalar o sistema operacional Ubuntu 16.04.2 minimal de 64 bits.

1.   Vá para o [link de download](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), escolha o espelho mais próximo e baixe um ISO 16.04.2 minimal de mínimo de 64 bits.
Mantenha um ISO 16.04.2 Minimal mínimo de 64 bits na unidade de DVD e inicie o sistema.

1.  Selecione **Inglês** como seu idioma preferido e, em seguida, selecione **Enter**.
    
    ![Selecione uma Linguagem](./media/vmware-azure-install-linux-master-target/image1.png)
1. Selecione **instalar servidor Ubuntu**e, em seguida, selecione **Enter**.

    ![Selecione instalar servidor Ubuntu](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Selecione **Inglês** como seu idioma preferido e, em seguida, selecione **Enter**.

    ![Selecione inglês como seu idioma preferido](./media/vmware-azure-install-linux-master-target/image3.png)

1. Selecione a opção apropriada na lista de opções de **fuso horário** e, em seguida, selecione **Enter**.

    ![Selecione o fuso horário correto](./media/vmware-azure-install-linux-master-target/image4.png)

1. Selecione **não** (a opção padrão) e, em seguida, selecione **Enter**.

     ![Configurar o teclado](./media/vmware-azure-install-linux-master-target/image5.png)
1. Selecione **Inglês (EUA)** como o país/região de origem do teclado e, em seguida, selecione **Enter**.

1. Selecione **Inglês (EUA)** como o layout do teclado e, em seguida, selecione **Enter**.

1. Digite o nome do host para o servidor na caixa **nome do host** e selecione **continuar**.

1. Para criar uma conta de usuário, insira o nome de usuário e, em seguida, selecione **continuar**.

      ![Criar uma conta de usuário](./media/vmware-azure-install-linux-master-target/image9.png)

1. Insira a senha para a nova conta de usuário e selecione **continuar**.

1.  Confirme a senha para o novo usuário e selecione **continuar**.

    ![Confirmar as senhas](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Na próxima seleção para criptografar seu diretório base, selecione **não** (a opção padrão) e, em seguida, selecione **Enter**.

1. Se o fuso horário exibido estiver correto, selecione **Sim** (a opção padrão) e, em seguida, selecione **Enter**. Para reconfigurar o fuso horário, selecione **não**.

1. Nas opções do método de particionamento, selecione **guiado – usar o disco inteiro**e, em seguida, selecione **Enter**.

     ![Selecione a opção de método de particionamento](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Selecione o disco apropriado nas opções **selecionar disco para partição** e, em seguida, selecione **Enter**.

    ![Selecionar o disco](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Selecione **Sim** para gravar as alterações no disco e, em seguida, selecione **Enter**.

    ![Selecione a opção padrão](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Em Configurar seleção de proxy, selecione a opção padrão, selecione **continuar**e, em seguida, selecione **Enter**.
     
     ![Selecione como gerenciar atualizações](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Selecione **nenhuma** opção de atualizações automáticas na seleção para gerenciar atualizações no seu sistema e selecione **Enter**.

     ![Selecione como gerenciar atualizações](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Como o servidor de destino mestre de Azure Site Recovery requer uma versão muito específica do Ubuntu, você precisa garantir que as atualizações de kernel estejam desabilitadas para a máquina virtual. Se eles estiverem habilitados, qualquer atualização regular fará com que o servidor de destino mestre não funcione corretamente. Certifique-se de selecionar a opção **sem atualizações automáticas** .

1.  Selecione opções padrão. Se você quiser o openSSH para conexão SSH, selecione a opção de **servidor OpenSSH** e, em seguida, selecione **continuar**.

    ![Selecionar software](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Na seleção para instalar o carregador de inicialização GRUB, selecione **Sim**e, em seguida, selecione **Enter**.
     
    ![Instalador de inicialização GRUB](./media/vmware-azure-install-linux-master-target/image20.png)


1. Selecione o dispositivo apropriado para a instalação do carregador de inicialização (preferencialmente **/dev/sda**) e, em seguida, selecione **Enter**.
     
    ![Selecione o dispositivo apropriado](./media/vmware-azure-install-linux-master-target/image21.png)

1. Selecione **continuar**e, em seguida, selecione **Enter** para concluir a instalação.

    ![Concluir a instalação](./media/vmware-azure-install-linux-master-target/image22.png)

1. Após a conclusão da instalação, entre na VM com as novas credenciais do usuário. (Consulte a **etapa 10** para obter mais informações.)

1. Use as etapas descritas na captura de tela a seguir para definir a senha do usuário raiz. Em seguida, entre como usuário raiz.

    ![Definir a senha do usuário raiz](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Configurar o computador como um servidor de destino mestre

Para obter a ID de cada disco rígido SCSI em uma máquina virtual Linux, o **disco. O parâmetro EnableUUID = TRUE** precisa ser habilitado. Para habilitar esse parâmetro, execute as seguintes etapas:

1. Desligue sua máquina virtual.

2. Clique com o botão direito do mouse na entrada da máquina virtual no painel esquerdo e selecione **Editar configurações**.

3. Selecione a guia **Opções** .

4. No painel esquerdo, selecione geral **avançado** > e, em seguida, selecione o botão **parâmetros de configuração** na parte inferior direita da tela.

    ![Abrir parâmetro de configuração](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    A opção **parâmetros de configuração** não está disponível quando o computador está em execução. Para tornar essa guia ativa, desligue a máquina virtual.

5. Veja se uma linha com o **disco. EnableUUID** já existe.

   - Se o valor existir e for definido como **false**, altere o valor para **true**. (Os valores não diferenciam maiúsculas de minúsculas.)

   - Se o valor existir e estiver definido como **true**, selecione **Cancelar**.

   - Se o valor não existir, selecione **Adicionar linha**.

   - Na coluna nome, adicione **disco. EnableUUID**e, em seguida, defina o valor como **true**.

     ![Verificando se o disco. EnableUUID já existe](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Desabilitar atualizações de kernel

Azure Site Recovery servidor de destino mestre requer uma versão específica do Ubuntu, verifique se as atualizações de kernel estão desabilitadas para a máquina virtual. Se as atualizações de kernel estiverem habilitadas, isso poderá fazer com que o servidor de destino mestre não funcione corretamente.

#### <a name="download-and-install-additional-packages"></a>Baixar e instalar pacotes adicionais

> [!NOTE]
> Verifique se você tem conectividade com a Internet para baixar e instalar pacotes adicionais. Se você não tiver conectividade com a Internet, precisará encontrar esses pacotes deb manualmente e instalá-los.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Obter o instalador para a instalação

Se o destino mestre tiver conectividade com a Internet, você poderá usar as etapas a seguir para baixar o instalador. Caso contrário, você pode copiar o instalador do servidor de processo e instalá-lo.

#### <a name="download-the-master-target-installation-packages"></a>Baixar os pacotes de instalação de destino mestre

[Baixe os bits de instalação de destino mestre do Linux mais recentes](https://aka.ms/latestlinuxmobsvc).

Para baixá-lo usando o Linux, digite:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Certifique-se de baixar e descompactar o instalador em seu diretório base. Se você descompactar o **/usr/local**, a instalação falhará.


#### <a name="access-the-installer-from-the-process-server"></a>Acessar o instalador do servidor de processo

1. No servidor de processo, vá para **c:\Arquivos de programas (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Copie o arquivo do instalador necessário do servidor de processo e salve-o como **latestlinuxmobsvc. tar. gz** em seu diretório base.


### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizadas

Para aplicar alterações de configuração personalizadas, use as seguintes etapas:


1. Execute o comando a seguir para descompactar o binário.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Captura de tela do comando a ser executado](./media/vmware-azure-install-linux-master-target/image16.png)

2. Execute o comando a seguir para conceder permissão.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Execute o comando a seguir para executar o script.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Execute o script apenas uma vez no servidor. Em seguida, desligue o servidor. Reinicie o servidor depois de adicionar um disco, conforme descrito na próxima seção.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adicionar um disco de retenção à máquina virtual de destino mestre do Linux

Use as seguintes etapas para criar um disco de retenção:

1. Anexe um novo disco de 1 TB à máquina virtual de destino mestre do Linux e, em seguida, inicie o computador.

2. Use o comando **vários caminhos-ll** para saber a ID de vários caminhos do disco de retenção: **vários caminhos-ll**

    ![ID de vários caminhos](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formate a unidade e, em seguida, crie um sistema de arquivos na nova unidade: **mkfs\<. ext4/dev/mapper/a ID de vários caminhos do disco de retenção >** .
    
    ![Sistema de ficheiros](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Depois de criar o sistema de arquivos, monte o disco de retenção.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Crie a entrada **fstab** para montar a unidade de retenção toda vez que o sistema for iniciado.
    
    `vi /etc/fstab`
    
    Selecione **Inserir** para começar a editar o arquivo. Crie uma nova linha e, em seguida, insira o texto a seguir. Edite a ID de vários caminhos de disco com base na ID de vários caminhos destacados do comando anterior.

    **ID\<de vários caminhos do/dev/mapper/Retention disks >/mnt/Retention ext4 RW 0 0**

    Selecione **ESC**e digite **: Wq** (gravar e sair) para fechar a janela do editor.

### <a name="install-the-master-target"></a>Instalar o destino mestre

> [!IMPORTANT]
> A versão do servidor de destino mestre deve ser igual ou anterior às versões do servidor de processo e do servidor de configuração. Se essa condição não for atendida, proteger novamente terá êxito, mas a replicação falhará.


> [!NOTE]
> Antes de instalar o servidor de destino mestre, verifique se o arquivo **/etc/hosts** na máquina virtual contém entradas que mapeiam o nome do host local para os endereços IP que estão associados a todos os adaptadores de rede.

1. Copie a frase secreta de **C:\ProgramData\Microsoft Azure site Recovery\private\connection.passphrase** no servidor de configuração. Em seguida, salve-o como **passphrase. txt** no mesmo diretório local executando o seguinte comando:

    `echo <passphrase> >passphrase.txt`

    Exemplo: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Anote o endereço IP do servidor de configuração. Execute o comando a seguir para instalar o servidor de destino mestre e registrar o servidor no servidor de configuração.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemplo: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Aguarde até que o script seja concluído. Se o destino mestre for registrado com êxito, o destino mestre será listado na página **site Recovery infraestrutura** do Portal.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalar o destino mestre usando a instalação interativa

1. Execute o comando a seguir para instalar o destino mestre. Para a função de agente, escolha **destino mestre**.

    ```
    ./install
    ```

2. Escolha o local padrão para instalação e, em seguida, selecione **Enter** para continuar.

    ![Escolhendo um local padrão para a instalação do destino mestre](./media/vmware-azure-install-linux-master-target/image17.png)

Após a conclusão da instalação, registre o servidor de configuração usando a linha de comando.

1. Anote o endereço IP do servidor de configuração. Você precisará dela na próxima etapa.

2. Execute o comando a seguir para instalar o servidor de destino mestre e registrar o servidor no servidor de configuração.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Exemplo: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Aguarde até que o script seja concluído. Se o destino mestre for registrado com êxito, o destino mestre será listado na página **site Recovery infraestrutura** do Portal.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Instalar ferramentas do VMware/Open-VM-Tools no servidor de destino mestre

Você precisa instalar as ferramentas do VMware ou as ferramentas de VM abertas no destino mestre para que ele possa descobrir os armazenamentos de dados. Se as ferramentas não estiverem instaladas, a tela proteger novamente não será listada nos armazenamentos de dados. Após a instalação das ferramentas do VMware, você precisa reiniciar o.

### <a name="upgrade-the-master-target-server"></a>Atualizar o servidor de destino mestre

Execute o instalador. Ele detecta automaticamente que o agente está instalado no destino mestre. Para atualizar, selecione **Y**.  Após a conclusão da instalação, verifique a versão do destino mestre instalada usando o seguinte comando:

`cat /usr/local/.vx_version`


Você verá que o campo **versão** fornece o número de versão do destino mestre.

## <a name="common-issues"></a>Problemas comuns

* Certifique-se de não ativar o Storage vMotion em nenhum componente de gerenciamento, como um destino mestre. Se o destino mestre for movido após uma nova proteção bem-sucedida, os discos de máquina virtual (VMDKs) não poderão ser desanexados. Nesse caso, o failback falha.

* O destino mestre não deve ter nenhum instantâneo na máquina virtual. Se houver instantâneos, o failback falhará.

* Devido a algumas configurações personalizadas de NIC, a interface de rede é desabilitada durante a inicialização e o agente de destino mestre não pode ser inicializado. Verifique se as propriedades a seguir estão definidas corretamente. Verifique essas propriedades no/etc/sysconfig/network-scripts/ifcfg-ETH * do arquivo do cartão Ethernet.
    * BOOTPROTO=dhcp
    * ONBOOT = Sim


## <a name="next-steps"></a>Passos Seguintes
Depois que a instalação e o registro do destino mestre forem concluídos, você poderá ver o destino mestre exibido na seção **destino mestre** em **site Recovery infraestrutura**, na visão geral do servidor de configuração.

Agora você pode continuar com a nova [proteção](vmware-azure-reprotect.md), seguida pelo failback.


---
title: Instalar um servidor de destino principal do Linux para reativação pós-falha para um site no local | Documentos da Microsoft
description: Saiba como configurar um servidor de destino principal do Linux para reativação pós-falha para um site no local durante a recuperação após desastre de VMs de VMware para o Azure com o Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 98718709038d7fd753e5eb3d45c130085c5accd9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099057"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalar um servidor de destino principal do Linux para reativação pós-falha
Depois de efetuar a ativação pós-falha de máquinas virtuais para o Azure, pode efetuar a reativação pós-falha as máquinas virtuais para o site no local. Para efetuar a reativação pós-falha, terá de voltar a proteger a máquina virtual do Azure para o site no local. Para que este processo, terá de um servidor de destino mestre no local para receber o tráfego. 

Se a sua máquina virtual protegida é uma máquina virtual do Windows, em seguida, tem um destino principal do Windows. Para uma máquina virtual do Linux, tem um destino principal do Linux. Leia os seguintes passos para saber como criar e instalar um destino principal do Linux.

> [!IMPORTANT]
> Começando com versão do 9.10.0 servidor de destino mestre, o servidor de destino principal mais recente pode ser instalado apenas num servidor de Ubuntu 16.04. As novas instalações não são permitidas em servidores de CentOS6.6. No entanto, pode continuar a atualizar seus servidores de destino mestra antiga, utilizando o 9.10.0 versão.
> Servidor de destino principal no LVM não é suportada.

## <a name="overview"></a>Descrição geral
Este artigo fornece instruções sobre como instalar um destino principal do Linux.

Publique comentários ou perguntas no final deste artigo ou no [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos

* Para escolher o anfitrião no qual pretenda implementar destino principal, determine se a reativação pós-falha vai ser para uma máquina de virtual no local existente ou para uma nova máquina virtual. 
    * Para uma máquina virtual existente, o anfitrião de destino principal deve ter acesso a arquivos de dados da máquina virtual.
    * Se a máquina de virtual no local não existe (em caso de recuperação alternativo de localização), a máquina virtual de reativação pós-falha é criada no mesmo anfitrião, como o destino principal. Pode escolher qualquer anfitrião ESXi para instalar o destino principal.
* O destino principal deve estar numa rede que possa comunicar com o servidor de processos e o servidor de configuração.
* A versão de destino principal tem de ser igual ou mais cedo do que as versões do servidor de processos e o servidor de configuração. Por exemplo, se a versão do servidor de configuração for 9.4, a versão de destino principal pode ser 9.4 ou 9.3 mas não 9,5.
* O destino principal só pode ser uma máquina virtual VMware e não um servidor físico.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Orientações de dimensionamento para criar o servidor de destino mestre

Crie o destino principal em conformidade com as seguintes diretrizes de dimensionamento:
- **RAM**: 6 GB ou mais
- **Tamanho do disco de SO**: 100 GB ou mais (para instalar o sistema operacional)
- **Tamanho de disco adicional para a unidade de retenção**: 1 TB
- **Núcleos de CPU**: 4 núcleos ou mais

São suportados os seguintes kernels suportados do Ubuntu.


|Série de kernel  |Suporta até  |
|---------|---------|
|4.4      |4.4.0-81-generic         |
|4.8      |4.8.0-56-generic         |
|4.10     |4.10.0-24-generic        |


## <a name="deploy-the-master-target-server"></a>Implementar o servidor de destino mestre

### <a name="install-ubuntu-16042-minimal"></a>Instalar o Ubuntu 16.04.2 mínimo

Realize os seguintes passos para instalar o sistema de operativo de 64 bits do Ubuntu 16.04.2.

1.   Vá para o [ligação de transferência](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), escolha o mais próximo espelho e Baixe o ISO de 64 bits mínimo um Ubuntu 16.04.2.
Mantenha um ISO de 64 bits mínimo do Ubuntu 16.04.2 na unidade de DVD e iniciar o sistema.

1.  Selecione **inglês** como sua linguagem preferida e, em seguida, selecione **Enter**.
    
    ![Selecione uma Linguagem](./media/vmware-azure-install-linux-master-target/image1.png)
1. Selecione **instalar o servidor Ubuntu**e, em seguida, selecione **Enter**.

    ![Select Install Ubuntu Server](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Selecione **inglês** como sua linguagem preferida e, em seguida, selecione **Enter**.

    ![Selecione o inglês como sua linguagem preferida](./media/vmware-azure-install-linux-master-target/image3.png)

1. Selecione a opção adequada a partir da **fuso horário** lista de opções e, em seguida, selecione **Enter**.

    ![Selecione o fuso horário correto](./media/vmware-azure-install-linux-master-target/image4.png)

1. Selecione **não** (a opção predefinida) e, em seguida, selecione **Enter**.

     ![Configurar o teclado](./media/vmware-azure-install-linux-master-target/image5.png)
1. Selecione **inglês (EUA)** como o país de origem para o teclado e, em seguida, selecione **Enter**.

1. Selecione **inglês (EUA)** como o layout de teclado e, em seguida, selecione **Enter**.

1. Introduza o nome de anfitrião para o seu servidor na **Hostname** caixa e, em seguida, selecione **continuar**.

1. Para criar uma conta de utilizador, introduza o nome de utilizador e, em seguida, selecione **continuar**.

      ![Criar uma conta de utilizador](./media/vmware-azure-install-linux-master-target/image9.png)

1. Introduza a palavra-passe para a nova conta de utilizador e, em seguida, selecione **continuar**.

1.  Confirme a palavra-passe para o novo utilizador e, em seguida, selecione **continuar**.

    ![Confirmar as palavras-passe](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Na próxima seleção para encriptar o seu diretório raiz, selecione **não** (a opção predefinida) e, em seguida, selecione **Enter**.

1. Se o fuso horário que é apresentado está correto, selecione **Sim** (a opção predefinida) e, em seguida, selecione **Enter**. Para reconfigurar o seu fuso horário, selecione **não**.

1. Entre as opções do método de criação de partições, selecione **guiada - utilizar o disco inteiro**e, em seguida, selecione **Enter**.

     ![Selecione a opção de método de criação de partições](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Selecione o disco adequado partir da **disco selecione a partição** opções e, em seguida, selecione **Enter**.

    ![Selecione o disco](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Selecione **Sim** escrever as alterações para o disco e, em seguida, selecione **Enter**.

    ![Selecione a opção predefinida](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Na seleção de proxy de configurar, selecione a opção predefinida, selecione **continuar**e, em seguida, selecione **Enter**.
     
     ![Selecione a forma de gerir atualizações](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Selecione **sem as atualizações automáticas** opção na seleção para o gerenciamento de atualizações no seu sistema e, em seguida, selecione **Enter**.

     ![Selecione a forma de gerir atualizações](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Como o servidor de destino principal do Azure Site Recovery requer uma versão bastante específica do Ubuntu, terá de se certificar de que o kernel atualizações estão desativadas para a máquina virtual. Se eles estiverem ativados, as atualizações regulares com que o servidor de destino mestre funcione incorretamente. Certifique-se de que seleciona os **sem as atualizações automáticas** opção.

1.  Selecione as opções predefinidas. Se pretender o openSSH para ligação de SSH, selecione o **servidor OpenSSH** opção e, em seguida, selecione **continuar**.

    ![Selecione o software](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. A seleção para instalar o carregador de inicialização GRUB, selecione **Sim**e, em seguida, selecione **Enter**.
     
    ![Instalador de arranque do GRUB](./media/vmware-azure-install-linux-master-target/image20.png)


1. Selecione o dispositivo apropriado para a instalação do carregador de arranque (preferencialmente **/desenvolvimento/sda**) e, em seguida, selecione **Enter**.
     
    ![Selecione o dispositivo apropriado](./media/vmware-azure-install-linux-master-target/image21.png)

1. Selecione **continuar**e, em seguida, selecione **Enter** para concluir a instalação.

    ![Concluir a instalação](./media/vmware-azure-install-linux-master-target/image22.png)

1. Após concluir a instalação, inicie sessão para a VM com as novas credenciais de utilizador. (Consulte a **passo 10** para obter mais informações.)

1. Utilize os passos descritos na seguinte captura de ecrã para definir palavra-passe de utilizador de raiz. Em seguida, inicie sessão como utilizador raiz.

    ![Definir palavra-passe de utilizador de raiz](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Configure a máquina como um servidor de destino mestre

Para obter o ID para cada disco de rígido SCSI na máquina virtual do Linux, o **disco. EnableUUID = TRUE** parâmetro tem de ser ativada. Para ativar este parâmetro, siga os passos seguintes:

1. Encerre a máquina virtual.

2. Faça duplo clique na entrada para a máquina virtual no painel esquerdo e, em seguida, selecione **editar definições de**.

3. Selecione o **opções** separador.

4. No painel esquerdo, selecione **avançadas** > **geral**e, em seguida, selecione o **parâmetros de configuração** botão na parte inferior direito da tela.

    ![Parâmetro de configuração aberta](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    O **parâmetros de configuração** opção não está disponível quando a máquina está em execução. Para tornar este separador ativa, encerre a máquina virtual.

5. Ver se uma linha com **disco. EnableUUID** já existe.

   - Se o valor existe e está definido como **False**, altere o valor para **verdadeiro**. (Os valores não diferenciam maiúsculas de minúsculas.)

   - Se o valor existe e está definido como **True**, selecione **Cancelar**.

   - Se o valor não existir, selecione **Adicionar linha**.

   - Na coluna nome, adicionar **disco. EnableUUID**e, em seguida, defina o valor **TRUE**.

     ![Bancária se o disco. EnableUUID já existe](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Desativar as atualizações de kernel

Servidor de destino principal do Azure Site Recovery requer uma versão específica do Ubuntu, certifique-se de que as atualizações de kernel estão desativadas para a máquina virtual. Se as atualizações de kernel estiverem ativadas, pode fazer com que o servidor de destino mestre funcione incorretamente.

#### <a name="download-and-install-additional-packages"></a>Transferir e instalar pacotes adicionais

> [!NOTE]
> Certifique-se de que tem ligação à Internet para transferir e instalar pacotes adicionais. Se não tiver conectividade à Internet, terá de encontrar esses pacotes Deb e instalá-los manualmente.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Obter o instalador para a configuração

Se o destino principal tem conectividade à Internet, pode utilizar os seguintes passos para transferir o instalador. Caso contrário, pode copiar o instalador do servidor de processos e, em seguida, instalá-lo.

#### <a name="download-the-master-target-installation-packages"></a>Transfira os pacotes de instalação de destino mestre

[Baixe os mais recentes bits de instalação de destino principal do Linux](https://aka.ms/latestlinuxmobsvc).

Para transferi-la com o Linux, escreva:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Certifique-se de que baixe e Descompacte o instalador no seu diretório raiz. Se descompactá-lo para **/usr/Local**, a instalação falhará.


#### <a name="access-the-installer-from-the-process-server"></a>O instalador do servidor de processos de acesso

1. No servidor de processos, aceda a **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Copie o ficheiro de instalador necessárias do servidor de processos e guarde-o como **latestlinuxmobsvc.tar.gz** no seu diretório raiz.


### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizada

Para aplicar alterações de configuração personalizada, utilize os seguintes passos:


1. Execute o seguinte comando para untar o binário.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Captura de ecrã do comando para executar](./media/vmware-azure-install-linux-master-target/image16.png)

2. Execute o seguinte comando para conceder permissão.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Execute o seguinte comando para executar o script.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Execute o script apenas uma vez no servidor. Em seguida, encerre o servidor. Reinicie o servidor depois de adicionar um disco, conforme descrito na secção seguinte.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adicionar um disco de retenção para a máquina de virtual de destino principal do Linux

Utilize os seguintes passos para criar um disco de retenção:

1. Anexar um disco novo de 1 TB para a máquina de virtual de destino principal do Linux e, em seguida, inicie a máquina.

2. Utilize o **Multipath i -ll** comandos para obter o ID de Multipath i do disco de retenção: **Multipath i -ll**

    ![ID de Multipath i](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formatar o disco e, em seguida, criar um sistema de ficheiros na nova unidade: **mkfs.ext4 /dev/mapeador/< id multipath do disco de retenção >**.
    
    ![Sistema de ficheiros](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Depois de criar o sistema de ficheiros, Monte o disco de retenção.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Criar a **fstab** entrada para montar a unidade de retenção, sempre que o sistema é iniciado.
    
    `vi /etc/fstab`
    
    Selecione **inserir** para começar a editar o ficheiro. Criar uma nova linha e, em seguida, insira o seguinte texto. Edite o ID de Multipath i do disco com base no ID de multipath realçado do comando anterior.

     **/Dev/mapeador/ <Retention disks multipath id> /mnt/rw do ext4 do retenção 0 0**

    Selecione **Esc**e, em seguida, escreva **: wq** (escrever e sair) para fechar a janela do editor.

### <a name="install-the-master-target"></a>Instalar o destino principal

> [!IMPORTANT]
> A versão do servidor de destino mestre tem de ser igual ou mais cedo do que as versões do servidor de processos e o servidor de configuração. Se esta condição não for cumprida, voltar a proteger for concluída com êxito, mas falha de replicação.


> [!NOTE]
> Antes de instalar o servidor de destino mestre, verifique se o **/etc/anfitriões** arquivo na máquina virtual contém entradas que mapeiam o nome do anfitrião local para os endereços IP que estão associados a todos os adaptadores de rede.

1. Copie a frase de acesso de **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** no servidor de configuração. Em seguida, guarde-o como **passphrase** no mesmo diretório local, executando o seguinte comando:

    `echo <passphrase> >passphrase.txt`

    Exemplo: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Tome nota do endereço IP do servidor de configuração. Execute o seguinte comando para instalar o servidor de destino mestre e registar o servidor com o servidor de configuração.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemplo: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Aguarde até que o script termina. Se o destino principal registra com êxito, o destino principal está listado na **infraestrutura do Site Recovery** página do portal.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalar o destino principal com a instalação interativa

1. Execute o seguinte comando para instalar o destino principal. Para a função de agente, escolher **destino mestre**.

    ```
    ./install
    ```

2. Escolha a localização predefinida para a instalação e, em seguida, selecione **Enter** para continuar.

    ![Escolher um local padrão para a instalação de destino mestre](./media/vmware-azure-install-linux-master-target/image17.png)

Após concluir a instalação, registe o servidor de configuração através da linha de comando.

1. Tenha em atenção o endereço IP do servidor de configuração. Precisa no próximo passo.

2. Execute o seguinte comando para instalar o servidor de destino mestre e registar o servidor com o servidor de configuração.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Exemplo: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Aguarde até que o script termina. Se o destino principal está registado com êxito, o destino principal está listado na **infraestrutura do Site Recovery** página do portal.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Instalar as ferramentas do VMware / aberto--ferramentas da vm no servidor de destino mestre

Tem de instalar as ferramentas do VMware ou ferramentas da vm aberto no destino mestre para que ele pode detetar os arquivos de dados. Se não estão instaladas as ferramentas, a tela de voltar a proteger não estiver listada nos arquivos de dados. Após a instalação das ferramentas do VMware, terá de reiniciar.

### <a name="upgrade-the-master-target-server"></a>Atualizar o servidor de destino mestre

Execute o instalador. Deteta automaticamente que o agente está instalado no destino mestre. Para atualizar, selecione **Y**.  Depois da configuração foi concluída, verifique a versão de destino principal instalado com o seguinte comando:

`cat /usr/local/.vx_version`


Verá que o **versão** campo indica o número de versão de destino mestre.

## <a name="common-issues"></a>Problemas comuns

* Certifique-se de que não ative vMotion de armazenamento em qualquer componente de gestão, como um destino principal. Se o destino principal move-se depois de uma nova proteção com êxito, não não possível desligar os máquina virtual de discos (VMDKs). Neste caso, a reativação pós-falha falha.

* O destino principal não deve ter quaisquer instantâneos na máquina virtual. Se existirem instantâneos, falha a reativação pós-falha.

* Devido a algumas configurações de NIC personalizadas, a interface de rede está desativada durante o arranque e não é possível inicializar o agente de destino mestre. Certifique-se de que as seguintes propriedades estão corretamente definidas. Verifique estas propriedades no Ethernet cartão /etc/sysconfig/network-scripts/ifcfg do arquivo-eth *.
    * BOOTPROTO=dhcp
    * ONBOOT=yes


## <a name="next-steps"></a>Passos Seguintes
Após concluir a instalação e registo de destino mestre, pode ver o destino principal são apresentadas na **destino mestre** secção **infraestrutura do Site Recovery**, na configuração do Descrição geral do servidor.

Agora, pode avançar com [nova proteção](vmware-azure-reprotect.md), seguido de reativação pós-falha.


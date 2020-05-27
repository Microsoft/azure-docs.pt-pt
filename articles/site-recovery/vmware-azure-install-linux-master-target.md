---
title: Instale um servidor-alvo principal para falha de Linux VM com recuperação do site Azure
description: Aprenda a configurar um servidor-alvo principal do Linux para falhar num site no local durante a recuperação de vMware VMware vMs para Azure usando a Recuperação do Site Azure.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 9ab4db53086046ff831fe91d003599841aa8148c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829788"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalar um servidor de destino principal do Linux para reativação pós-falha
Depois de falhar as suas máquinas virtuais para o Azure, pode falhar as máquinas virtuais no local. Para falhar, é necessário reproteger a máquina virtual de Azure para o local no local. Para este processo, precisa de um servidor-alvo principal no local para receber o tráfego. 

Se a sua máquina virtual protegida for uma máquina virtual do Windows, então precisa de um alvo principal do Windows. Para uma máquina virtual Linux, precisas de um alvo principal do Linux. Leia os seguintes passos para aprender a criar e instalar um alvo principal do Linux.

> [!IMPORTANT]
> Começando com o lançamento do servidor alvo principal 9.10.0, o mais recente servidor de alvo principal só pode ser instalado num servidor Ubuntu 16.04. Não são permitidas novas instalações nos servidores CentOS6.6. No entanto, pode continuar a atualizar os seus antigos servidores-alvo principal utilizando a versão 9.10.0.
> O servidor-alvo principal no LVM não é suportado.

## <a name="overview"></a>Descrição geral
Este artigo fornece instruções para como instalar um alvo principal do Linux.

Publique comentários ou perguntas no final deste artigo ou no [Microsoft Q&Uma página de perguntas para os Serviços de Recuperação do Azure](https://docs.microsoft.com/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Pré-requisitos

* Para escolher o hospedeiro para implantar o alvo principal, determine se o failback será para uma máquina virtual existente no local ou para uma nova máquina virtual. 
    * Para uma máquina virtual existente, o anfitrião do alvo principal deve ter acesso às lojas de dados da máquina virtual.
    * Se a máquina virtual no local não existir (em caso de Recuperação de Localização Alternativa), a máquina virtual de failback é criada no mesmo hospedeiro que o alvo principal. Pode escolher qualquer hospedeiro ESXi para instalar o alvo principal.
* O alvo principal deve estar numa rede que possa comunicar com o servidor de processo e com o servidor de configuração.
* A versão do alvo principal deve ser igual ou mais cedo do que as versões do servidor de processoe do servidor de configuração. Por exemplo, se a versão do servidor de configuração for de 9.4, a versão do alvo principal pode ser de 9.4 ou 9.3, mas não 9.5.
* O alvo principal só pode ser uma máquina virtual VMware e não um servidor físico.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Dimensionamento de diretrizes para criar o servidor alvo principal

Criar o alvo principal de acordo com as seguintes diretrizes de dimensionamento:
- **RAM**: 6 GB ou mais
- **Tamanho do disco OS**: 100 GB ou mais (para instalar os O)
- **Tamanho adicional do disco para unidade de retenção**: 1 TB
- **Núcleos CPU**: 4 núcleos ou mais

Os seguintes núcleos Ubuntu são apoiados.


|Série Kernel  |Apoio até  |
|---------|---------|
|4.4      |4.4.0-81-genérico         |
|4.8      |4.8.0-56-genérico         |
|4.10     |4.10.0-24-genérico        |


## <a name="deploy-the-master-target-server"></a>Implementar o servidor alvo principal

### <a name="install-ubuntu-16042-minimal"></a>Instale Ubuntu 16.04.2 Mínimo

Tome as seguintes etapas para instalar o sistema operativo Ubuntu 16.04.2 64 bits.

1.   Vá ao link de [descarregamento](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), escolha o espelho mais próximo e baixe um Ubuntu 16.04.2 mínimo de 64 bits ISO.
Mantenha um Ubuntu 16.04.2 iso mínimo de 64 bits na unidade de DVD e inicie o sistema.

1.  Selecione **o inglês** como o seu idioma preferido e, em seguida, selecione **Enter**.
    
    ![Escolha um idioma](./media/vmware-azure-install-linux-master-target/image1.png)
1. **Selecione Instalar o Servidor Ubuntu**e, em seguida, selecione **Enter**.

    ![Selecione Instalar o Servidor Ubuntu](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Selecione **o inglês** como o seu idioma preferido e, em seguida, selecione **Enter**.

    ![Selecione inglês como o seu idioma preferido](./media/vmware-azure-install-linux-master-target/image3.png)

1. Selecione a opção adequada na lista de opções do **Fuso Horário** e, em seguida, selecione **Enter**.

    ![Selecione o fuso horário correto](./media/vmware-azure-install-linux-master-target/image4.png)

1. Selecione **Não** (a opção predefinido) e, em seguida, selecione **Enter**.

     ![Configure o teclado](./media/vmware-azure-install-linux-master-target/image5.png)
1. Selecione **inglês (EUA)** como país/região de origem para o teclado e, em seguida, selecione **Enter**.

1. Selecione **inglês (EUA)** como o layout do teclado e, em seguida, selecione **Enter**.

1. Introduza o nome de anfitrião para o seu servidor na caixa **'Hostname'** e, em seguida, selecione **Continuar**.

1. Para criar uma conta de utilizador, introduza o nome de utilizador e, em seguida, selecione **Continuar**.

      ![Criar uma conta de utilizador](./media/vmware-azure-install-linux-master-target/image9.png)

1. Introduza a palavra-passe para a nova conta de utilizador e, em seguida, selecione **Continuar**.

1.  Confirme a palavra-passe para o novo utilizador e, em seguida, selecione **Continuar**.

    ![Confirme as palavras-passe](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Na próxima seleção para encriptar o seu diretório inicial, selecione **Não** (a opção predefinida) e, em seguida, selecione **Enter**.

1. Se o fuso horário visualizado estiver correto, selecione **Sim** (a opção predefinido) e, em seguida, selecione **Enter**. Para reconfigurar o seu fuso horário, selecione **Nº**.

1. A partir das opções do método de partição, selecione **Guiado - use o disco inteiro,** e, em seguida, selecione **Enter**.

     ![Selecione a opção de método de partição](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Selecione o disco apropriado do disco Select para as opções **de partição** e, em seguida, selecione **Enter**.

    ![Selecione o disco](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Selecione **Sim** para escrever as alterações no disco e, em seguida, selecione **Enter**.

    ![Selecione a opção por defeito](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Na seleção de proxy configurar, selecione a opção predefinida, selecione **Continuar**, e, em seguida, selecione **Enter**.
     
     ![Selecione como gerir upgrades](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Selecione Nenhuma opção de **atualizações automáticas** na seleção para gerir atualizações no seu sistema e, em seguida, selecione **Enter**.

     ![Selecione como gerir upgrades](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Uma vez que o servidor-alvo principal de recuperação do site Azure requer uma versão muito específica do Ubuntu, é necessário garantir que as atualizações do kernel são desativadas para a máquina virtual. Se estiverem ativadas, quaisquer atualizações regulares fazem com que o servidor alvo principal esteja avariado. Certifique-se de que seleciona a opção **De atualizações automáticas.**

1.  Selecione opções predefinidas. Se pretender abrir o SSSH para a ligação SSH, selecione a opção de **servidor OpenSSH** e, em seguida, selecione **Continuar**.

    ![Selecione software](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Na seleção para instalar o carregador de botas GRUB, selecione **Sim,** e, em seguida, **selecione Enter**.
     
    ![Instalador de botas GRUB](./media/vmware-azure-install-linux-master-target/image20.png)


1. Selecione o dispositivo apropriado para a instalação do carregador de arranque (de preferência **/dev/sda**), e, em seguida, selecione **Enter**.
     
    ![Selecione o dispositivo apropriado](./media/vmware-azure-install-linux-master-target/image21.png)

1. Selecione **Continuar**e, em seguida, selecione **Entrar** para terminar a instalação.

    ![Termine a instalação](./media/vmware-azure-install-linux-master-target/image22.png)

1. Depois de concluída a instalação, inscreva-se no VM com as novas credenciais de utilizador. (Consulte o **Passo 10** para mais informações.)

1. Utilize os passos descritos na seguinte imagem para definir a palavra-passe do utilizador ROOT. Em seguida, inscreva-se como utilizador ROOT.

    ![Definir a palavra-passe do utilizador ROOT](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Configure a máquina como um servidor alvo principal

Para obter o ID para cada disco rígido SCSI numa máquina virtual Linux, o **disco. O parâmetro ENABLEUUID = TRUE** precisa de ser ativado. Para ativar este parâmetro, tome os seguintes passos:

1. Desligue a sua máquina virtual.

2. Clique à direita na entrada da máquina virtual no painel esquerdo e, em seguida, selecione Definições de **Edição**.

3. Selecione o separador **Opções.**

4. No painel esquerdo, selecione **Advanced**  >  **General**e, em seguida, selecione o botão Parâmetros de **Configuração** na parte inferior-direita do ecrã.

    ![Parâmetro de configuração aberta](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    A opção Parâmetros de **Configuração** não está disponível quando a máquina está em funcionamento. Para tornar este separador ativo, desligue a máquina virtual.

5. Veja se uma linha com **disco. O EnableUUID** já existe.

   - Se o valor existir e estiver definido para **Falso,** altere o valor para **True**. (Os valores não são sensíveis aos casos.)

   - Se o valor existir e estiver definido para **True,** **selecione Cancelar**.

   - Se o valor não existir, selecione **Adicionar Linha**.

   - Na coluna de nomes, adicione **o disco. EnableUUID,** e, em seguida, definir o valor para **TRUE**.

     ![A verificar se o disco. EnableUUID já existe](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Desativar as atualizações do kernel

O servidor-alvo principal de recuperação do site Azure requer uma versão específica do Ubuntu, certifique-se de que as atualizações do kernel são desativadas para a máquina virtual. Se as atualizações do kernel estiverem ativadas, pode causar o mau funcionamento do servidor alvo principal.

#### <a name="download-and-install-additional-packages"></a>Descarregue e instale pacotes adicionais

> [!NOTE]
> Certifique-se de que tem conectividade com a Internet para descarregar e instalar pacotes adicionais. Se não tiver conectividade com a Internet, tem de encontrar manualmente estes pacotes Deb e instalá-los.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Obtenha o instalador para configuração

Se o seu alvo principal tiver conectividade com a Internet, pode utilizar os seguintes passos para descarregar o instalador. Caso contrário, pode copiar o instalador a partir do servidor de processos e, em seguida, instalá-lo.

#### <a name="download-the-master-target-installation-packages"></a>Descarregue os pacotes de instalação de alvo principal

[Descarregue os mais recentes bits de instalação de alvo principal do Linux.](https://aka.ms/latestlinuxmobsvc)

Para descarregá-lo usando Linux, escreva:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Certifique-se de que descarrega e desaperta o instalador no seu diretório inicial. Se desapertar para **/usr/Local,** então a instalação falha.


#### <a name="access-the-installer-from-the-process-server"></a>Aceda ao instalador a partir do servidor de processos

1. No servidor de processos, vá a **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repositório**.

2. Copie o ficheiro instalador necessário do servidor de processos e guarde-o como **latestlinuxmobsvc.tar.gz** no seu diretório inicial.


### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizadas

Para aplicar alterações de configuração personalizadas, utilize os seguintes passos como utilizador ROOT:

1. Executar o seguinte comando para desartar o binário.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Screenshot do comando para correr](./media/vmware-azure-install-linux-master-target/image16.png)

2. Executar o seguinte comando para dar permissão.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Para executar o script, execute o seguinte comando.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Execute o script apenas uma vez no servidor. Em seguida, desligue o servidor. Reinicie o servidor depois de adicionar um disco, conforme descrito na secção seguinte.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adicione um disco de retenção à máquina virtual do alvo principal do Linux

Utilize os seguintes passos para criar um disco de retenção:

1. Fixe um novo disco de 1 TB à máquina virtual do alvo principal do Linux e, em seguida, ligue a máquina.

2. Utilize o comando **multipath -ll** para aprender a identificação multipata do disco de retenção: **multipath -ll**

    ![Multipath ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. Forforte a unidade e, em seguida, crie um sistema de ficheiros na nova unidade: **mkfs.ext4 /dev/mapper/ \< Id multipata do disco **de retenção>.
    
    ![Sistema de ficheiros](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Depois de criar o sistema de ficheiros, monte o disco de retenção.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Crie a entrada **de fstab** para montar a unidade de retenção sempre que o sistema começa.
    
    `vi /etc/fstab`
    
    Selecione **Inserir** para começar a editar o ficheiro. Crie uma nova linha e, em seguida, insira o seguinte texto. Editar o ID multipata do disco com base no ID multipata realçado do comando anterior.

    **/dev/mapper/ \< Retenção de discos multipatia id> /mnt/retenção ext4 rw 0 0 0**

    Selecione **Esc**, e, em seguida, **escreva :wq** (escreva e desista) para fechar a janela do editor.

### <a name="install-the-master-target"></a>Instale o alvo principal

> [!IMPORTANT]
> A versão do servidor alvo principal deve ser igual ou mais cedo do que as versões do servidor de processo e do servidor de configuração. Se esta condição não for satisfeita, a reprotecção tem sucesso, mas a replicação falha.


> [!NOTE]
> Antes de instalar o servidor alvo principal, verifique se o ficheiro **/etc/anfitriões** na máquina virtual contém entradas que mapeiam o nome de anfitrião local para os endereços IP que estão associados a todos os adaptadores de rede.

1. Copie a frase de passe de **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** no servidor de configuração. Em seguida, guarde-o como **passphrase.txt** no mesmo diretório local executando o seguinte comando:

    `echo <passphrase> >passphrase.txt`

    Exemplo: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Anote o endereço IP do servidor de configuração. Executar o seguinte comando para instalar o servidor alvo principal e registar o servidor com o servidor de configuração.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemplo: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Espere até o guião terminar. Se o alvo principal se registar com sucesso, o alvo principal está listado na página de Infraestrutura de **Recuperação** do Site do portal.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instale o alvo principal utilizando a instalação interativa

1. Executar o seguinte comando para instalar o alvo principal. Para o papel de agente, escolha o **alvo principal.**

    ```
    ./install
    ```

2. Escolha a localização predefinida para a instalação e, em seguida, selecione **Entrar** para continuar.

    ![Escolher um local padrão para a instalação do alvo principal](./media/vmware-azure-install-linux-master-target/image17.png)

Depois de concluída a instalação, registe o servidor de configuração utilizando a linha de comando.

1. Note o endereço IP do servidor de configuração. Precisa no próximo passo.

2. Executar o seguinte comando para instalar o servidor alvo principal e registar o servidor com o servidor de configuração.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Exemplo: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Espere até o guião terminar. Se o alvo principal for registado com sucesso, o alvo principal está listado na página de Infraestrutura de **Recuperação** do Site do portal.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Instale ferramentas VMware / ferramentas abertas de vm no servidor alvo principal

É necessário instalar ferramentas VMware ou ferramentas de vm abertas no alvo principal para que possa descobrir as lojas de dados. Se as ferramentas não forem instaladas, o ecrã de reproteção não está listado nas lojas de dados. Após a instalação das ferramentas VMware, é necessário reiniciar.

### <a name="upgrade-the-master-target-server"></a>Atualizar o servidor alvo principal

Execute o instalador. Deteta automaticamente que o agente está instalado no alvo principal. Para atualizar, selecione **Y**.  Depois de concluída a configuração, verifique a versão do alvo principal instalada utilizando o seguinte comando:

`cat /usr/local/.vx_version`


Verá que o campo **Versão** dá o número de versão do alvo principal.

## <a name="common-issues"></a>Problemas comuns

* Certifique-se de que não liga o Armazenamento vMotion em quaisquer componentes de gestão, tais como um alvo principal. Se o alvo principal se mover após uma reprotecção bem sucedida, os discos de máquina virtual (VMDKs) não podem ser separados. Neste caso, o failback falha.

* O alvo principal não deve ter fotografias na máquina virtual. Se houver instantâneos, o failback falha.

* Devido a algumas configurações de NIC personalizadas, a interface de rede é desativada durante o arranque, e o agente-alvo principal não pode inicializar. Certifique-se de que as seguintes propriedades estão corretamente definidas. Verifique estas propriedades no ficheiro do cartão Ethernet /etc/sysconfig/network-scripts/ifcfg-eth*.
    * BOOTPROTO=dhcp
    * ONBOOT=sim


## <a name="next-steps"></a>Passos seguintes
Após a instalação e registo do alvo principal ter terminado, pode ver o alvo principal aparecer na secção principal de **alvo** na Infraestrutura de Recuperação do **Local,** sob a visão geral do servidor de configuração.

Agora pode proceder à [reproteção,](vmware-azure-reprotect.md)seguida de recuo.


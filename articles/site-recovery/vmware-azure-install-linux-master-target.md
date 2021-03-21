---
title: Instale um servidor-alvo principal para o recuo do Linux VM com a recuperação do site Azure
description: Aprenda a configurar um servidor-alvo principal do Linux para falhar no local durante a recuperação de VMware VMs para Azure usando a Recuperação do Site Azure.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: mayg
ms.openlocfilehash: 9e1008f7acbfe0685b7a171176c7dc54592d1491
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019247"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalar um servidor de destino principal do Linux para reativação pós-falha
Depois de falhar as suas máquinas virtuais para o Azure, pode falhar de volta as máquinas virtuais para o local. Para falhar, é necessário reprotegir a máquina virtual de Azure para o local. Para este processo, precisa de um servidor-alvo principal no local para receber o tráfego. 

Se a sua máquina virtual protegida é uma máquina virtual Windows, então precisa de um alvo principal do Windows. Para uma máquina virtual Linux, precisa de um alvo principal do Linux. Leia os seguintes passos para aprender a criar e instalar um alvo principal do Linux.

> [!IMPORTANT]
> A partir do lançamento do servidor-alvo principal 9.10.0, o mais recente servidor-alvo principal só pode ser instalado num servidor Ubuntu 16.04. Não são permitidas novas instalações nos servidores CentOS6.6. No entanto, pode continuar a atualizar os seus antigos servidores-alvo principal utilizando a versão 9.10.0.
> O servidor-alvo principal na LVM não é suportado.

## <a name="overview"></a>Descrição geral
Este artigo fornece instruções sobre como instalar um alvo principal linux.

Publique comentários ou perguntas no final deste artigo ou na página de perguntas do [Microsoft Q&Uma página de perguntas para serviços de recuperação do Azure](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Pré-requisitos

* Para escolher o anfitrião para implementar o alvo principal, determine se o failback será para uma máquina virtual existente no local ou para uma nova máquina virtual. 
    * Para uma máquina virtual existente, o anfitrião do alvo principal deve ter acesso às lojas de dados da máquina virtual.
    * Se a máquina virtual no local não existir (em caso de Recuperação alternativa de localização), a máquina virtual de recuo é criada no mesmo anfitrião que o alvo principal. Pode escolher qualquer anfitrião ESXi para instalar o alvo principal.
* O alvo principal deve estar numa rede que possa comunicar com o servidor de processo e o servidor de configuração.
* A versão do alvo principal deve ser igual ou mais cedo às versões do servidor de processo e do servidor de configuração. Por exemplo, se a versão do servidor de configuração for 9.4, a versão do alvo principal pode ser 9.4 ou 9.3, mas não 9.5.
* O alvo principal só pode ser uma máquina virtual VMware e não um servidor físico.

> [!NOTE]
> Certifique-se de que não liga o Storage vMotion em componentes de gestão, como um alvo principal. Se o alvo principal se mover após uma reproteção bem sucedida, os discos de máquina virtuais (VMDKs) não podem ser separados. Neste caso, o falhanço falha.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Diretrizes de dimensionamento para criar o servidor principal alvo

Crie o alvo principal de acordo com as seguintes diretrizes de dimensionamento:
- **RAM**: 6 GB ou mais
- **Tamanho do disco de SO**: 100 GB ou mais (para instalar o SO)
- **Tamanho adicional do disco para unidade de retenção**: 1 TB
- **Núcleos do CPU**: 4 núcleos ou mais
- **Kernel**: 4.16.*

## <a name="deploy-the-master-target-server"></a>Implementar o servidor-alvo principal

### <a name="install-ubuntu-16042-minimal"></a>Instalar Ubuntu 16.04.2 Mínimo

Tome as seguintes medidas para instalar o sistema operativo Ubuntu 16.04.2 de 64 bits.

1.   Vá ao [link de descarregamento](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), escolha o espelho mais próximo e descarregue um Ubuntu 16.04.2 mínimo de 64 bits ISO.
Mantenha um Ubuntu 16.04.2 iso mínimo de 64 bits na unidade de DVD e inicie o sistema.

1.  Selecione **o inglês** como o seu idioma preferido e, em seguida, selecione **Enter**.
    
    ![Escolha um idioma](./media/vmware-azure-install-linux-master-target/image1.png)
1. Selecione **instalar o Servidor Ubuntu** e, em seguida, selecione **Enter**.

    ![Selecione instalar o servidor Ubuntu](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Selecione **o inglês** como o seu idioma preferido e, em seguida, selecione **Enter**.

    ![Selecione o inglês como o seu idioma preferido](./media/vmware-azure-install-linux-master-target/image3.png)

1. Selecione a opção adequada na lista de opções **do Fuso Horário** e, em seguida, selecione **Enter**.

    ![Selecione o fuso horário correto](./media/vmware-azure-install-linux-master-target/image4.png)

1. Selecione **Não** (a opção predefinitiva) e, em seguida, selecione **Enter**.

     ![Configure o teclado](./media/vmware-azure-install-linux-master-target/image5.png)
1. Selecione **Inglês (EUA)** como país/região de origem para o teclado e, em seguida, selecione **Enter**.

1. Selecione **Inglês (EUA)** como o layout do teclado e, em seguida, selecione **Enter**.

1. Introduza o nome de anfitrião para o seu servidor na caixa **de nome anfitrião** e, em seguida, selecione **Continue**.

1. Para criar uma conta de utilizador, insira o nome de utilizador e, em seguida, **selecione Continue**.

      ![Criar uma conta de utilizador](./media/vmware-azure-install-linux-master-target/image9.png)

1. Introduza a palavra-passe para a nova conta de utilizador e, em seguida, **selecione Continue**.

1.  Confirme a palavra-passe para o novo utilizador e, em seguida, **selecione Continue**.

    ![Confirme as palavras-passe](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Na próxima seleção para encriptar o diretório de casa, selecione **Nº** (a opção padrão) e, em seguida, **selecione Enter**.

1. Se o fuso horário apresentado estiver correto, selecione **Sim** (a opção padrão) e, em seguida, selecione **Enter**. Para reconfigurar o seu fuso horário, selecione **Nº**.

1. A partir das opções do método de partição, **selecione Guided - use o disco inteiro** e, em seguida, selecione **Enter**.

     ![Selecione a opção método de partição](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Selecione o disco apropriado a partir do disco Select para as opções **de partição** e, em seguida, selecione **Enter**.

    ![Selecione o disco](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Selecione **Sim** para escrever as alterações no disco e, em seguida, selecione **Enter**.

    ![Selecione a opção predefinitiva](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Na seleção de procuração configurada, selecione a opção predefinida, selecione **Continue** e, em seguida, selecione **Enter**.
     
     ![Screenshot que mostra onde selecionar Continue e, em seguida, selecione Enter.](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Selecione Nenhuma opção **de atualizações automáticas** na seleção para gerir atualizações no seu sistema e, em seguida, selecione **Enter**.

     ![Selecione como gerir upgrades](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Uma vez que o servidor-alvo principal de recuperação do site Azure requer uma versão muito específica do Ubuntu, é necessário garantir que as atualizações do kernel são desativadas para a máquina virtual. Se estiverem ativadas, quaisquer atualizações regulares fazem com que o servidor principal do alvo avarie. Certifique-se de que seleciona a opção **"Não atualizações automáticas".**

1.  Selecione as opções predefinitivas. Se pretender abrir OSSH para ligação SSH, selecione a opção **do servidor OpenSSH** e, em seguida, selecione **Continue**.

    ![Selecione software](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Na seleção para instalar o carregador de arranque GRUB, selecione **Sim** e, em seguida, selecione **Enter**.
     
    ![Instalador de botas GRUB](./media/vmware-azure-install-linux-master-target/image20.png)


1. Selecione o dispositivo apropriado para a instalação do carregador de arranque (de preferência **/dev/sda**), e, em seguida, selecione **Enter**.
     
    ![Selecione o dispositivo apropriado](./media/vmware-azure-install-linux-master-target/image21.png)

1. **Selecione Continue** e, em seguida, selecione **Enter** para terminar a instalação.

    ![Termine a instalação](./media/vmware-azure-install-linux-master-target/image22.png)

1. Depois de concluída a instalação, inscreva-se no VM com as novas credenciais de utilizador. (Consulte o **passo 10** para obter mais informações.)

1. Utilize os passos descritos na imagem seguinte para definir a palavra-passe do utilizador ROOT. Em seguida, inscreva-se como utilizador ROOT.

    ![Desafine a palavra-passe do utilizador ROOT](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Configure a máquina como um servidor alvo principal

Para obter o ID de cada disco rígido SCSI numa máquina virtual Linux, o **disco. EnableUUID = O** parâmetro TRUE tem de ser ativado. Para ativar este parâmetro, tome os seguintes passos:

1. Desligue a sua máquina virtual.

2. Clique à direita na entrada da máquina virtual no painel esquerdo e, em seguida, selecione **Editar Definições**.

3. Selecione o separador **Opções.**

4. No painel esquerdo, selecione **Advanced**  >  **General** e, em seguida, selecione o botão **Parâmetros** de Configuração na parte inferior direita do ecrã.

    ![Parâmetro de configuração aberto](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    A opção **Parâmetros de Configuração** não está disponível quando a máquina está a funcionar. Para tornar este separador ativo, desligue a máquina virtual.

5. Veja se uma linha com **disco. EnableUUID** já existe.

   - Se o valor existir e estiver definido como **Falso,** altere o valor para **Verdadeiro**. (Os valores não são sensíveis a casos.)

   - Se o valor existir e estiver definido para **True,** **selecione Cancelar**.

   - Se o valor não existir, **selecione Add Row**.

   - Na coluna de nomes, adicione **o disco. EnableUUID**, e, em seguida, definir o valor para **TRUE**.

     ![A verificar se o disco. EnableUUID já existe](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Desativar as atualizações do núcleo

O servidor-alvo principal de recuperação do site Azure requer uma versão específica do Ubuntu, garantindo que as atualizações do kernel são desativadas para a máquina virtual. Se as atualizações do kernel estiverem ativadas, pode causar o mau funcionamento do servidor principal do alvo.

#### <a name="download-and-install-additional-packages"></a>Descarregue e instale pacotes adicionais

> [!NOTE]
> Certifique-se de que tem conectividade com a Internet para descarregar e instalar pacotes adicionais. Se não tiver conectividade com a Internet, tem de encontrar manualmente estes pacotes Deb e instalá-los.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Obtenha o instalador para configuração

Se o seu alvo principal tiver conectividade com a Internet, pode utilizar os seguintes passos para descarregar o instalador. Caso contrário, pode copiar o instalador do servidor de processo e instalá-lo.

#### <a name="download-the-master-target-installation-packages"></a>Descarregue os pacotes de instalação do alvo principal

[Descarregue os mais recentes bits de instalação do linux principal target](https://aka.ms/latestlinuxmobsvc).

Para descarregá-lo usando Linux, escreva:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Certifique-se de que descarrega e desaperte o instalador no seu diretório de casa. Se desapertar para **/usr/Local,** então a instalação falha.


#### <a name="access-the-installer-from-the-process-server"></a>Aceda ao instalador a partir do servidor de processo

1. No servidor de processos, aceda a **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repositório**.

2. Copie o ficheiro instalador necessário do servidor de processo e guarde-o como **mais recente .tar.gz** no seu diretório de origem.


### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizadas

Para aplicar alterações de configuração personalizadas, utilize os seguintes passos como utilizador ROOT:

1. Executar o seguinte comando para desatar o binário.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Screenshot do comando para correr](./media/vmware-azure-install-linux-master-target/image16.png)

2. Executar o seguinte comando para dar permissão.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Para executar o script, execute o seguinte comando.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Executar o script apenas uma vez no servidor. Em seguida, desligue o servidor. Reinicie o servidor depois de adicionar um disco, conforme descrito na secção seguinte.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adicione um disco de retenção à máquina virtual alvo linux

Utilize os seguintes passos para criar um disco de retenção:

1. Fixe um novo disco de 1-TB à máquina virtual principal do Linux e, em seguida, ligue a máquina.

2. Use o comando **multipata -ll** para aprender o ID multipata do disco de retenção: **multipata -ll**

    ![ID multipata](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formatar a unidade e, em seguida, criar um sistema de ficheiros na nova unidade: **mkfs.ext4 /dev/mapper/ \<Retention disk's multipath id>**.
    
    ![Sistema de ficheiros](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Depois de criar o sistema de ficheiros, monte o disco de retenção.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Crie a entrada **fstab** para montar a unidade de retenção sempre que o sistema for iniciado.
    
    `vi /etc/fstab`
    
    **Selecione Inserção** para começar a editar o ficheiro. Crie uma nova linha e, em seguida, insira o seguinte texto. Editar o ID multipata do disco com base no ID multipata realçado do comando anterior.

    **/dev/mapper/ \<Retention disks multipath id> /mnt/retenção ext4 rw 0 0**

    Selecione **Esc**, e, em seguida, **escreva :wq** (escreva e desista) para fechar a janela do editor.

### <a name="install-the-master-target"></a>Instale o alvo principal

> [!IMPORTANT]
> A versão do servidor alvo principal deve ser igual ou mais cedo às versões do servidor de processo e do servidor de configuração. Se esta condição não for cumprida, a reproteta terá sucesso, mas a replicação falha.


> [!NOTE]
> Antes de instalar o servidor alvo principal, verifique se o ficheiro **/etc/anfitriões** na máquina virtual contém entradas que mapeiam o nome de anfitrião local para os endereços IP que estão associados a todos os adaptadores de rede.

1. Executar o seguinte comando para instalar o alvo principal.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    ```

2. Copie a frase de passagem de **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** no servidor de configuração. Em seguida, guarde-o como **passphrase.txt** no mesmo diretório local, executando o seguinte comando:

    `echo <passphrase> >passphrase.txt`

    Exemplo: 

    `echo itUx70I47uxDuUVY >passphrase.txt`
    

3. Note o endereço IP do servidor de configuração. Executar o seguinte comando para registar o servidor com o servidor de configuração.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemplo: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Espera até o guião acabar. Se o alvo principal se registar com sucesso, o alvo principal está listado na página de **Infraestrutura** de Recuperação do Local do portal.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instale o alvo principal utilizando a instalação interativa

1. Executar o seguinte comando para instalar o alvo principal. Para o papel de agente, escolha **o alvo principal**.

    ```
    ./install
    ```

2. Escolha a localização predefinitiva para a instalação e, em seguida, selecione **Enter** para continuar.

    ![Escolher um local padrão para a instalação do alvo principal](./media/vmware-azure-install-linux-master-target/image17.png)

Depois de concluída a instalação, registe o servidor de configuração utilizando a linha de comando.

1. Note o endereço IP do servidor de configuração. Precisa no próximo passo.

2. Executar o seguinte comando para registar o servidor com o servidor de configuração.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh
    ```

     Espera até o guião acabar. Se o alvo principal for registado com sucesso, o alvo principal está listado na página de **Infraestrutura** de Recuperação do Local do portal.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Instale ferramentas VMware / open-vm-tools no servidor alvo principal

É necessário instalar ferramentas VMware ou ferramentas open-vm no alvo principal para que possa descobrir as lojas de dados. Se as ferramentas não forem instaladas, o ecrã reprotetor não está listado nas lojas de dados. Após a instalação das ferramentas VMware, é necessário reiniciar.

### <a name="upgrade-the-master-target-server"></a>Atualize o servidor alvo principal

Execute o instalador. Deteta automaticamente que o agente está instalado no alvo principal. Para atualizar, selecione **Y**.  Depois de concluída a configuração, verifique a versão do alvo principal instalado utilizando o seguinte comando:

`cat /usr/local/.vx_version`


Verá que o campo **Versão** dá o número de versão do alvo principal.

## <a name="common-issues"></a>Problemas comuns

* Certifique-se de que não liga o Storage vMotion em componentes de gestão, como um alvo principal. Se o alvo principal se mover após uma reproteção bem sucedida, os discos de máquina virtuais (VMDKs) não podem ser separados. Neste caso, o falhanço falha.

* O alvo principal não deve ter imagens instantâneas na máquina virtual. Se houver instantâneos, falha.

* Devido a algumas configurações personalizadas de NIC, a interface de rede é desativada durante o arranque, e o agente-alvo principal não pode inicializar. Certifique-se de que as seguintes propriedades estão corretamente definidas. Verifique estas propriedades no ficheiro do cartão Ethernet /etc/rede/interfaces.
    * auto eth0
    * iface eth0 inet dhcp <br>

    Reiniciar o serviço de rede utilizando o seguinte comando: <br>

`sudo systemctl restart networking`


## <a name="next-steps"></a>Passos seguintes
Após a instalação e registo do alvo principal ter terminado, pode ver o alvo principal aparecer na secção **alvo principal** na **Infraestrutura de Recuperação** do Local, sob a visão geral do servidor de configuração.

Pode agora proceder com [reproteção,](vmware-azure-reprotect.md)seguida de recuo.


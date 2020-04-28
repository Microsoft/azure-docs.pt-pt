---
title: Criar um laboratório de hacking ético com serviços de laboratório azure [ Microsoft Docs
description: Aprenda a montar um laboratório usando os Serviços de Laboratório Azure para ensinar hacking ético.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74133174"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Criar um laboratório para ensinar aulas de hacking ético 
Este artigo mostra-lhe como criar uma classe que se foca no lado forense da pirataria ética. Os testes de penetração, uma prática usada pela comunidade de hacking ético, ocorre quando alguém tenta aceder ao sistema ou à rede para demonstrar vulnerabilidades que um intruso malicioso pode explorar. 

Numa aula de hacking ético, os alunos podem aprender técnicas modernas para se defenderem contra vulnerabilidades. Cada aluno recebe uma máquina virtual do Windows Server que tem duas máquinas virtuais aninhadas – uma máquina virtual com imagem [Metasploitável3](https://github.com/rapid7/metasploitable3) e outra máquina com imagem [Kali Linux.](https://www.kali.org/) A máquina virtual Metasploitável é usada para explorar propósitos e a máquina virtual Kali fornece acesso às ferramentas necessárias para executar tarefas forenses.

Este artigo tem duas secções principais. A primeira secção cobre como criar o laboratório de sala de aula. A segunda secção cobre como criar a máquina do modelo com virtualização aninhada ativada e com as ferramentas e imagens necessárias. Neste caso, uma imagem metasploitável e uma imagem Kali Linux numa máquina que tem Hyper-V habilitado a hospedar as imagens.

## <a name="lab-configuration"></a>Configuração do laboratório
Para montar este laboratório, precisa de uma assinatura Azure para começar. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que obtém uma subscrição Azure, pode criar uma nova conta de laboratório nos Serviços de Laboratório Azure ou usar uma conta existente. Consulte o seguinte tutorial para criar uma nova conta de laboratório: [Tutorial para configurar uma conta](tutorial-setup-lab-account.md)de laboratório .

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e, em seguida, aplique as seguintes definições:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- | 
| Médio (Virtualização Aninhada) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Máquina de modelo 

Depois de criada a máquina do modelo, ligue a máquina e ligue-a para completar as três principais tarefas seguintes. 
 
1. Instale a máquina para virtualização aninhada. Permite que todas as funcionalidades apropriadas do windows, como o Hyper-V, e configura o networking para que as imagens Hyper-V possam comunicar entre si e com a internet.
2. Configura risa a imagem [kali](https://www.kali.org/) Linux. Kali é uma distribuição Linux que inclui ferramentas para testes de penetração e auditoria de segurança.
3. Configurar a imagem metasploitável. Para este exemplo, será utilizada a imagem [Metasploitável3.](https://github.com/rapid7/metasploitable3) Esta imagem é criada para ter propositadamente vulnerabilidades de segurança.

Um script que automatiza as tarefas acima descritas está disponível nos [Scripts de Hacking Ético dos Serviços de Laboratório.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)

### <a name="prepare-template-machine-for-nested-virtualization"></a>Prepare a máquina de modelo para a virtualização aninhada
Siga as instruções [deste artigo](how-to-enable-nested-virtualization-template-vm.md) para preparar o seu modelo de máquina virtual para virtualização aninhada. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Configurar uma máquina virtual aninhada com imagem Kali Linux
Kali é uma distribuição Linux que inclui ferramentas para testes de penetração e auditoria de segurança.

1. Descarregue [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)a imagem de .  
    1. Baixe o **Kali Linux Hyper-V 64 Bit** para Hyper-V.
    1. Extraio o ficheiro .7z.  Se ainda não tiver 7 zip, [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)descarregue-o a partir de . Lembre-se da localização da pasta extraída, pois precisará dela mais tarde.
2. Abra **o Gestor de Hiper-V** a partir de Ferramentas Administrativas.
1. Selecione **Action**, e, em seguida, selecione **Import Virtual Machine**. 
1. Na página **localizar pastas** do assistente da **Máquina Virtual importada,** escolha a localização da pasta extraída que detém a imagem Kali Linux.

    ![Localizar o diálogo da pasta](../media/class-type-ethical-hacking/locate-folder.png)
1. Na página **Select Virtual Machine,** selecione a imagem Kali Linux.  Neste caso, a imagem é **kali-linux-2019.3-hyperv**.

    ![Selecione imagem Kali](../media/class-type-ethical-hacking/select-kali-image.png)
1.  Na página **Escolha do Tipo de Importação,** selecione Copiar a máquina virtual **(criar um novo ID único)**.

    ![Escolha o tipo de importação](../media/class-type-ethical-hacking/choose-import-type.png)
1. Aceite as definições predefinidas em **Escolher Pastas para Ficheiros de Máquinas Virtuais** e escolha pastas para armazenar páginas **de Discos Rígidos Virtuais** e, em seguida, selecione **Next**.
1. Na página **Connect Network,** escolha **LabServicesSwitch** criado anteriormente no **Modelo de Preparação para a Virtualização Anestesada** deste artigo e, em seguida, selecione **Next**.

    ![Conecte página de rede](../media/class-type-ethical-hacking/connect-network.png)
1. Selecione **Terminar** na página **Resumo.** Aguarde até que as operações de cópia e importação estejam concluídas. A máquina virtual Kali Linux já estará disponível em Hyper-V.
1. De **Hyper-V Manager,** escolha **Action** -> **Start**e, em seguida, escolha **Action** -> **Connect** para ligar à máquina virtual.  
12. O nome de `root` utilizador predefinido é e a palavra-passe é `toor`. 

    > [!NOTE]
    > Se precisar de desbloquear a imagem, prima a tecla CTRL e arraste o rato para cima.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Criar um VM aninhado com imagem metasploitável  
A imagem Rapid7 Metasploitable é uma imagem propositadamente configurada com vulnerabilidades de segurança. Vais usar esta imagem para testar e encontrar problemas. As seguintes instruções mostram-lhe como usar uma imagem metasploitável pré-criada. No entanto, se for necessária uma versão mais [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)recente da imagem metasploitável, consulte .

1. Navegar [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)para. Preencha o formulário para descarregar a imagem e selecione o botão **Enviar.**
1. Selecione o botão **Download Metasploitable Now.**
1. Quando o ficheiro zip for descarregado, extraa o ficheiro zip e lembre-se da localização.
1. Converta o ficheiro vmdk extraído num ficheiro Vhdx para que possa utilizar com Hyper-V. Para tal, abra a PowerShell com privilégios administrativos e navegue até à pasta onde reside o ficheiro vmdk, e siga estas instruções:
    1. Descarregue o Conversor de [Máquinavirtual da Microsoft](https://www.microsoft.com/download/details.aspx?id=42497)e execute mvmc_setup ficheiro.msi quando solicitado.
    1. Importe o módulo PowerShell.  A localização predefinida na qual o módulo está instalado é C:\Program Files\Microsoft Virtual Machine Converter\

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Converta o vmdk num ficheiro vhd que pode ser usado pela Hyper-V. Esta operação pode demorar vários minutos.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Copie os metásplitos recém-criados.vhdx para C:\Utilizadores\Public\Documents\Hyper-V\Virtual Hard Disks\. 
1. Crie uma nova máquina virtual Hyper-V.
    1. Open **Hyper-V Manager**.
    1. Escolha **ação** -> **nova** -> máquina**virtual**.
    1. Na página Antes de **New Virtual Machine Wizard** **Começar,** clique em **Seguinte**.
    1. Na página **'Especificar Nome e Localização',** introduza **metasploitável** para o **nome**, e selecione **Next**.

        ![Novo assistente de imagem VM](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Na página **'Especificar Geração',** aceite as predefinições e selecione **Next**.
    1. Na página De atribuição de **memória,** introduza **512 MB** para a memória de **arranque**, e selecione **Seguinte**. 

        ![Atribuir página de memória](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. Na página de Configuração de **Rede,** deixe a ligação **como não ligada**. Vai sabotar o adaptador de rede mais tarde.
    1. Na página **Connect Virtual Hard Disk,** selecione **Utilize um disco rígido virtual existente**. Navegue no local para o ficheiro **metasploitable.vhdx** criado no passo anterior e selecione **Next**. 

        ![Conecte página de disco de rede virtual](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Na página **de Completar a Nova Máquina Virtual Assistente** e selecionar **Acabamento**.
    1. Assim que a máquina virtual for criada, selecione-a no Gestor de Hiper-V. Não ligue a máquina ainda.  
    1. Escolha**as Definições** **de Ação** -> .
    1. Nas **Definições para um diálogo metasploitável,** selecione **Adicionar Hardware**. 
    1. Selecione **Legacy Network Adapter**, e selecione **Adicionar**.

        ![Página adaptador de rede](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. Na página do Adaptador de **Rede Legacy,** selecione **LabServicesSwitch** para a definição **de Switch Virtual** e selecione **OK**. LabServicesSwitch foi criado ao preparar a máquina de modelo para Hyper-V na secção **de preparação para a virtualização anuminária.**

        ![Página adaptador legacy Network](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. A imagem metasploitável está agora pronta a ser utilizada. De **Hyper-V Manager,** escolha **Action** -> **Start**e, em seguida, escolha **Action** -> **Connect** para ligar à máquina virtual.  O nome de utilizador predefinido é **msfadmin** e a palavra-passe é **msfadmin**. 


O modelo está agora atualizado e tem imagens necessárias para uma aula de testes de penetração de hacking ético, uma imagem com ferramentas para fazer o teste de penetração e outra imagem com vulnerabilidades de segurança para descobrir. A imagem do modelo pode agora ser publicada para a classe. Selecione o botão **Publicar** na página do modelo para publicar o modelo no laboratório.
  

## <a name="cost"></a>Custo  
Se quiser estimar o custo deste laboratório, pode usar o seguinte exemplo: 
 
Para uma turma de 25 alunos com 20 horas de horário de aulas programada e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria: 

25 alunos * (20 + 10) horas * 55 Unidades de Laboratório * 0,01 USD por hora = 412,50 USD. 

Para obter mais informações sobre preços, consulte o [Preço dos Serviços do Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo acompanhou-o pelas escadas para criar um laboratório para a aula de hacking ético. Inclui passos para configurar a virtualização aninhada para criar duas máquinas virtuais dentro da máquina virtual hospedeira para testes de penetração.

## <a name="next-steps"></a>Passos seguintes
Os próximos passos são comuns à criação de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição por e-mail para estudantes.](how-to-configure-student-usage.md#send-invitations-to-users) 


---
title: Criar um laboratório de Hacking Ético com serviços de laboratório Azure Microsoft Docs
description: Aprenda a criar um laboratório usando os Serviços Azure Lab para ensinar hacking ético.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ae0d57223edb68d1bed4ad64a005dd33da019dd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631686"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Crie um laboratório para ensinar aulas de hacking ético 
Este artigo mostra-lhe como criar uma aula que se centre no lado forense da pirataria ética. Os testes de penetração, uma prática usada pela comunidade de hacking ético, ocorre quando alguém tenta aceder ao sistema ou rede para demonstrar vulnerabilidades que um intruso malicioso pode explorar. 

Numa aula de hacking ético, os alunos podem aprender técnicas modernas para se defenderem contra vulnerabilidades. Cada aluno recebe uma máquina virtual de anfitrião do Windows Server que tem duas máquinas virtuais aninhadas – uma máquina virtual com imagem [Metaploitável3](https://github.com/rapid7/metasploitable3) e outra máquina com imagem [Kali Linux.](https://www.kali.org/) A máquina virtual metasploitável é usada para explorar propósitos e a máquina virtual Kali fornece acesso às ferramentas necessárias para executar tarefas forenses.

Este artigo tem duas secções principais. A primeira secção cobre como criar o laboratório de sala de aula. A segunda secção abrange como criar a máquina de modelo com virtualização aninhada ativada e com as ferramentas e imagens necessárias. Neste caso, uma imagem metapável e uma imagem de Kali Linux numa máquina que tem Hiper-V habilitado a acolher as imagens.

## <a name="lab-configuration"></a>Configuração de laboratório
Para montar este laboratório, precisa de uma assinatura Azure para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que tiver uma subscrição do Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab ou utilizar uma conta existente. Consulte o seguinte tutorial para a criação de uma nova conta de laboratório: [Tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e, em seguida, aplique as seguintes definições:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- | 
| Médio (Virtualização Aninhada) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Máquina de modelo 

Após a criação da máquina do modelo, inicie a máquina e ligue-a para completar as três tarefas principais seguintes. 
 
1. Configurar a máquina para a virtualização aninhada. Permite que todas as funcionalidades de janelas apropriadas, como o Hyper-V, e configura a rede para que as imagens Hiper-V possam comunicar entre si e com a internet.
2. Prepara a imagem do [Kali](https://www.kali.org/) Linux. Kali é uma distribuição Linux que inclui ferramentas para testes de penetração e auditoria de segurança.
3. Configurar a imagem metaperitável. Para este exemplo, a imagem [Metasploitável3](https://github.com/rapid7/metasploitable3) será utilizada. Esta imagem é criada para ter vulnerabilidades de segurança propositadamente.

Um script que automatiza as tarefas descritas acima está disponível nos [Scripts de Hacking Éticos dos Serviços de Laboratório.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)

### <a name="prepare-template-machine-for-nested-virtualization"></a>Preparar máquina de modelo para virtualização aninhada
Siga as instruções [deste artigo](how-to-enable-nested-virtualization-template-vm.md) para preparar a máquina virtual do seu modelo para a virtualização aninhada. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Configurar uma máquina virtual aninhada com a Imagem Kali Linux
Kali é uma distribuição Linux que inclui ferramentas para testes de penetração e auditoria de segurança.

1. Descarregue a imagem a partir de [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/) .  
    1. Descarregue o **Kali Linux Hyper-V 64 Bit** para Hyper-V.
    1. Extraia o ficheiro .7z.  Se ainda não tem 7 zip, descarregue-o a partir de [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) . Lembre-se da localização da pasta extraída, pois vai precisar dela mais tarde.
2. Abrir **Hyper-V Manager** a partir de Ferramentas Administrativas.
1. Selecione **Ação**e, em seguida, **selecione Import Virtual Machine**. 
1. Na página **de Definição** de Localizar o assistente de **máquina virtual de importação,** escolha a localização da pasta extraída que detém a imagem Kali Linux.

    ![Localizar o diálogo do pasta](./media/class-type-ethical-hacking/locate-folder.png)
1. Na página **'Selecionar Máquina Virtual',** selecione a imagem Kali Linux.  Neste caso, a imagem é **kali-linux-2019.3-hyperv**.

    ![Selecione a imagem de Kali](./media/class-type-ethical-hacking/select-kali-image.png)
1.  Na página **Escolha Importar Tipo,** selecione **Copiar a máquina virtual (criar um novo ID único)**.

    ![Escolha o tipo de importação](./media/class-type-ethical-hacking/choose-import-type.png)
1. Aceite as definições predefinidos em **Escolher Pastas para ficheiros de máquinas virtuais** e escolha pastas para armazenar páginas **de discos rígidos virtuais** e, em seguida, selecione **Seguinte**.
1. Na página **'Rede De Ligação',** escolha **LabServicesSwitch** criado anteriormente na secção **de preparação para virtualização aninhada** deste artigo e, em seguida, selecione **Next**.

    ![Conecte a página da rede](./media/class-type-ethical-hacking/connect-network.png)
1. **Selecione Terminar** na página **Resumo.** Aguarde até que as operações de cópia e importação estejam concluídas. A máquina virtual Kali Linux estará agora disponível no Hyper-V.
1. Do **Hyper-V Manager,** escolha **Action**  ->  **Start**e, em seguida, escolha **Action**  ->  **Connect** para ligar à máquina virtual.  
12. O nome de utilizador predefinido é `root` e a palavra-passe é `toor` . 

    > [!NOTE]
    > Se precisar de desbloquear a imagem, pressione a tecla CTRL e arraste o rato para cima.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Configurar um VM aninhado com imagem metaplotiável  
A imagem Metaplodável Rapid7 é uma imagem configurada propositadamente com vulnerabilidades de segurança. Vais usar esta imagem para testar e encontrar problemas. As seguintes instruções mostram-lhe como utilizar uma imagem metaperiável pré-criada. No entanto, se for necessária uma versão mais recente da imagem metapertilizável, ver [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) .

1. Descarregue a imagem metaperitável.
    1. Navegue para [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html) . Preencha o formulário para descarregar a imagem e selecione o botão **Enviar.**
    2. Selecione o botão **Baixar Metaploitável Agora.**
    3. Quando o ficheiro zip for descarregado, extraia o ficheiro zip e lembre-se da localização do ficheiro Metasploitable.vmdk.
1. Converta o ficheiro vmdk extraído num ficheiro vhdx para que possa utilizar o ficheiro vhdx com Hyper-V. Existem várias ferramentas disponíveis para converter imagens VMware em imagens Hyper-V e vice-versa.  Vamos usar o [StarWind V2V Converter.](https://www.starwindsoftware.com/starwind-v2v-converter)  Para baixar, consulte a [página de download do StarWind V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Iniciar **o StarWind V2V Converter**.
    1. Na **localização selecionada da imagem para converter página,** escolha **o ficheiro Local**.  Selecione **Seguinte**.
    1. Na página de **imagem 'Fonte',** navegue para e selecione o Metasploitable.vmdk extraído no passo anterior para a definição do **nome 'Ficheiro'.**  Selecione **Seguinte**.
    1. Na **localização selecionada da imagem de destino,** escolha **o ficheiro Local.**  Selecione **Seguinte**.
    1. Na página do **formato de imagem de destino Select,** escolha **VHD/VHDX**.  Selecione **Seguinte**.
    1. Na **opção Selecione para a página de formato de imagem VHD/VHDX,** escolha **a imagem de cultivo VHDX**.  Selecione **Seguinte**.
    1. Na página de nome do **ficheiro de destino Select,** aceite o nome do ficheiro predefinido.  Selecione **Converter**.
    1. Na página **de Conversão,** aguarde que a imagem seja convertida.  Este processo pode demorar vários minutos.  **Selecione Acabamento** quando a conversão estiver concluída.
1. Crie uma nova máquina virtual Hyper-V.
    1. Gestor **de Hiper-V aberto.**
    1. Escolha **a ação**  ->  **nova**máquina  ->  **virtual**.
    1. Na página **antes de começar** o novo assistente de máquina **virtual,** selecione **Seguinte**.
    1. Na página **'Indicar o Nome e a Localização',** **insira o metaploitível** para o **nome**e selecione **Seguinte**.

        ![Novo assistente de imagem VM](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Na página **'Especificar Geração',** aceite as predefinições e selecione **Seguinte**.
    1. Na página **'Memória' 'Atribuir',** insira **512 MB** para a **memória de arranque**e selecione **Seguinte**. 

        ![Atribuir página de memória](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. Na página **de configuração de rede,** deixe a ligação como **Não Conectada**. Irá configurar o adaptador de rede mais tarde.
    1. Na página **'Ligar** o Disco Rígido Virtual', **selecione Utilize um disco rígido virtual existente.** Navegue para a localização do ficheiro **metasploitável.vhdx** criado no passo anterior e selecione **Next**. 

        ![Ligue a página de disco de rede virtual](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Na **página de Completar a nova** página de Assistente de Máquina Virtual e selecione **Terminar**.
    1. Uma vez criada a máquina virtual, selecione-a no Hyper-V Manager. Não ligue a máquina ainda.  
    1. Escolha **Action**  ->  **definições de ação**.
    1. Nas **Definições para diálogo metaperiável** para, selecione **Adicionar Hardware**. 
    1. Selecione **o adaptador de rede Legacy**e selecione **Adicionar**.

        ![Página de adaptador de rede](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. Na página **do adaptador de rede Legacy,** selecione **LabServicesSwitch** para a definição **de Switch Virtual** e selecione **OK**. LabServicesSwitch foi criado ao preparar a máquina de modelo para Hiper-V na secção **de preparação para a virtualização aninhada.**

        ![Página de adaptador de rede legacy](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. A imagem metapertilizável está agora pronta a ser utilizada. Do **Hyper-V Manager,** escolha **Action**  ->  **Start**e, em seguida, escolha **Action**  ->  **Connect** para ligar à máquina virtual.  O nome de utilizador predefinido é **msfadmin** e a palavra-passe é **msfadmin**. 


O modelo está agora atualizado e tem imagens necessárias para uma aula de testes de penetração de hacking ético, uma imagem com ferramentas para fazer o teste de penetração e outra imagem com vulnerabilidades de segurança para descobrir. A imagem do modelo pode agora ser publicada na classe. Selecione o botão **Publicar** na página do modelo para publicar o modelo para o laboratório.
  

## <a name="cost"></a>Cost  
Se quiser estimar o custo deste laboratório, pode usar o seguinte exemplo: 
 
Para uma turma de 25 alunos com 20 horas de horário de aula programado e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria: 

25 alunos * (20 + 10) horas * 55 Unidades de Laboratório * 0,01 USD por hora = 412,50 USD. 

Para obter mais informações sobre preços, consulte [os preços dos serviços do Azure Lab.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Conclusão
Este artigo acompanhou-o através dos degraus para criar um laboratório para a aula de hacking ético. Inclui passos para configurar a virtualização aninhada para criar duas máquinas virtuais dentro da máquina virtual do hospedeiro para testes de penetração.

## <a name="next-steps"></a>Passos seguintes
Os próximos passos são comuns para a criação de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição de e-mail para estudantes.](how-to-configure-student-usage.md#send-invitations-to-users) 


---
title: Ligue-se a um Linux VM em Azure Lab Services | Microsoft Docs
description: Aprenda a usar o ambiente de trabalho remoto para máquinas virtuais Linux em um laboratório em Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85443422"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Ligue-se a máquinas virtuais Linux em um laboratório de sala de aula de Azure Lab Services
Este artigo mostra como os alunos podem ligar-se a uma máquina virtual Linux (VM) num laboratório utilizando:
- Terminal SSH (protocolo de concha seguro)
- GUI (interface gráfica do utilizador) ambiente de trabalho remoto

> [!IMPORTANT] 
> O SSH é configurado automaticamente para que tanto os alunos como o instrutor possam entrar em LM linux sem qualquer configuração adicional. No entanto, se os alunos precisarem de se ligar a um ambiente de trabalho remoto GUI, o instrutor poderá ter de fazer uma configuração adicional.  Para mais informações, consulte [Ativar o ambiente de trabalho remoto para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>Ligue-se ao aluno VM usando SSH

1. Quando um aluno entra diretamente no portal Labs ( ou usando um link de `https://labs.azure.com` registo ), é exibido um azulejo para cada laboratório a que o aluno tem `https://labs.azure.com/register/<registrationCode>` acesso. 
   
1. No azulejo, alternar o botão para ligar o VM se estiver em estado de paragem. 

2. Selecione **Ligar**. Vê duas opções para ligar ao VM: **SSH** e **RDP**.

    ![Estudante VM - opções de conexão](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Selecione a opção **SSH** e verá a caixa de diálogo **de máquina virtual:**  

    ![Cadeia de conexão SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Clique no botão **Copiar** ao lado da caixa de texto para copiar as informações de ligação SSH à área de transferência. 

5. Guarde as informações de ligação SSH, como no bloco de texto, para que possa utilizar esta informação de ligação no passo seguinte.

6. A partir de um terminal SSH (como [putty),](https://www.putty.org/)ligue-se ao seu VM.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Ligue-se ao VM do aluno utilizando o ambiente de trabalho remoto GUI
O instrutor pode optar por configurar VMs para que os alunos também possam ligar-se através de um ambiente de trabalho remoto GUI.  Neste caso, os alunos precisam de saber pelo seu instrutor se devem ligar-se aos seus VMs utilizando a aplicação do **cliente Microsoft Remote Desktop (RDP)** ou **X2Go.**  Ambas as aplicações permitem que um aluno se conecte remotamente ao seu VM e exiba o ambiente de trabalho gráfico Linux no seu computador local.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Ligue-se ao VM do aluno utilizando o Microsoft Remote Desktop (RDP)
Os estudantes podem usar o Microsoft Remote Desktop (RDP) para se conectarem aos seus VMs Linux depois de o seu instrutor configurar o seu laboratório com pacotes RDP e GUI para um ambiente de ambiente de trabalho gráfico Linux (como MATE, XFCE, e assim por diante). Aqui estão os passos para ligar: 

1. No azulejo para o seu VM, certifique-se de que o VM está a funcionar e clique em **Connect**. Vê duas opções para ligar ao VM: **SSH** e **RDP**.

    ![Estudante VM - opções de conexão](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Selecione a opção **RDP.**  Quando o ficheiro RDP for descarregado na sua máquina, guarde-o para o seu VM.

3. Se estiver a ligar-se a partir de um computador Windows, normalmente, o cliente do Microsoft Remote Desktop (RDP) já está instalado e configurado.  Como resultado, tudo o que precisa fazer é clicar no ficheiro RDP para abri-lo e iniciar a sessão remota.

    Em vez disso, se estiver a ligar-se a partir de um Mac ou chromebook, consulte os seguintes passos:
   - [Ligue-se a um VM utilizando RDP num Mac](connect-virtual-machine-mac-remote-desktop.md).
   - [Ligue-se a um VM utilizando RDP num Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).  

### <a name="connect-to-the-student-vm-using-x2go"></a>Ligue-se ao aluno VM usando X2Go
Os alunos podem usar o X2Go para se conectarem aos seus VMs Linux depois de o seu instrutor configurar o seu laboratório com o X2Go e os pacotes GUI para um ambiente de desktop gráfico Linux (como MATE, XFCE, e assim por diante).

Os alunos precisam de descobrir pelo seu instrutor que ambiente de trabalho gráfico Linux o seu instrutor instalou.  Esta informação é necessária nos próximos passos para se conectar usando o cliente X2Go.

1. Instale o [cliente X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) no seu computador local.

1. Siga as instruções da [primeira secção](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) para copiar as informações de ligação SSH para o seu VM.  Precisa desta informação para se conectar usando o cliente X2Go.

1. Assim que tiver a informação de ligação SSH, abra o cliente X2Go e selecione **Session**  >  **New Session**.
   ![X2Go cria nova sessão](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. Introduza os valores no painel **'Preferências de Sessão'** com base nas informações de ligação SSH.  Por exemplo, as suas informações de ligação serão semelhantes a esta:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    Utilizando este exemplo, são introduzidos os seguintes valores:

   - **Nome da sessão** - Especifique um nome, como o nome do seu VM.
   - **Anfitrião** - O ID do seu VM; por exemplo, **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** . .
   - **Início de Sessão** - O nome de utilizador para o seu VM; por exemplo, **estudante.**
   - **Porta SSH** - A porta única atribuída ao seu VM; por exemplo, **12345**.
   - **Tipo de sessão** - Selecione o ambiente de trabalho gráfico Linux que o seu instrutor configura o seu VM.  Precisa obter esta informação do seu instrutor.

    Finalmente, clique **em OK** para criar a sessão.

    ![Preferências de sessão X2Go](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Clique na sua sessão no painel direito.

    ![X2Go inicia nova sessão](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Se for solicitado com uma mensagem semelhante a esta, selecione **sim** para continuar a inserir a sua palavra-passe: **A autenticidade do anfitrião '[:12345' não pode ser `00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com` estabelecida.  A impressão digital principal do ECDSA é SHA256:00000000000000000000000000000000000000000000000000000000000.Tem a certeza de que pretende continuar a ligar (sim/não)?**

2. Quando solicitado, insira a sua palavra-passe e clique **em OK**.  Estará agora ligado remotamente ao ambiente de trabalho GUI do seu VM.

## <a name="next-steps"></a>Passos seguintes
Para aprender a ativar a funcionalidade de ligação ao ambiente de trabalho remoto para Os VMs Do Linux num laboratório de sala de aula, consulte [Ativar o ambiente de trabalho remoto para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md). 


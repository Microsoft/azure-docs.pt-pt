---
title: Ligue-se a um Linux VM em Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a usar o ambiente de trabalho remoto para máquinas virtuais Linux num laboratório em Serviços de Laboratório Azure.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 0658360c093fb1928421f8f1746ed57c4cd347a4
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700098"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Ligue-se a máquinas virtuais Linux em um laboratório de sala de aula de Serviços de Laboratório Azure
Este artigo mostra como os alunos podem ligar-se a uma máquina virtual Linux (VM) num laboratório usando:
- Terminal SSH (protocolo de concha segura)
- Ambiente de trabalho remoto GUI (interface gráfica do utilizador)

> [!IMPORTANT] 
> O SSH é configurado automaticamente para que tanto os alunos como o instrutor possam sSH em VMs Linux sem qualquer configuração adicional. No entanto, se os alunos precisarem de se ligar à utilização de um ambiente de trabalho remoto GUI, o instrutor poderá ter de fazer uma configuração adicional.  Para mais detalhes, consulte [Ativar o ambiente de trabalho remoto para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>Ligue-se ao VM do aluno usando SSH

1. Quando um aluno entra diretamente no portal labs ( ou através de uma ligação de `https://labs.azure.com` registo), é apresentado um azulejo para cada laboratório a que o aluno tem `https://labs.azure.com/register/<registrationCode>` acesso. 
   
1. No azulejo, altere o botão para iniciar o VM se estiver em estado de paragem. 

2. Selecione **Ligar**. Você vê duas opções para ligar ao VM: **SSH** e **RDP**.

    ![Student VM - opções de conexão](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Selecione a opção **SSH** e verá o Connect à sua caixa de diálogo virtual da **máquina:**  

    ![Cadeia de ligação SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Clique no botão **Copiar** ao lado da caixa de texto para copiar as informações de ligação SSH à área de redação. 

5. Guarde as informações de ligação SSH, como na almofada de texto, para que possa utilizar esta informação de ligação no próximo passo.

6. A partir de um terminal SSH (como [putty),](https://www.putty.org/)ligue-se ao seu VM.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Ligue-se ao vM do aluno usando o ambiente de trabalho remoto GUI
O instrutor pode optar por configurar VMs para que os alunos também possam ligar-se através de um ambiente de trabalho remoto GUI.  Neste caso, os alunos precisam de saber pelo seu instrutor se devem ligar-se aos seus VMs utilizando a aplicação do cliente **Microsoft Remote Desktop (RDP)** ou **X2Go.**  Ambas as aplicações permitem que um aluno se conectem remotamente ao seu VM e exibam o ambiente de trabalho gráfico linux no seu computador local.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Ligue-se ao VM do aluno utilizando o Microsoft Remote Desktop (RDP)
Os estudantes podem usar o Microsoft Remote Desktop (RDP) para se conectarem aos seus VMs Linux depois de o seu instrutor configurar o seu laboratório com pacotes RDP e GUI para um ambiente de trabalho gráfico Linux (como MATE, XFCE, etc.). Aqui estão os passos para ligar: 

1. No azulejo do seu VM, certifique-se de que o VM está a funcionar e clique em **Connect**. Você vê duas opções para ligar ao VM: **SSH** e **RDP**.

    ![Student VM - opções de conexão](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Selecione a opção **RDP.**  Quando o ficheiro RDP for descarregado na sua máquina, guarde-o para o seu VM.

3. Se estiver a ligar-se a partir de um computador Windows, normalmente, o cliente do Microsoft Remote Desktop (RDP) já está instalado e configurado.  Como resultado, tudo o que precisa fazer é clicar no ficheiro RDP para abri-lo e iniciar a sessão remota.

    Em vez disso, se estiver a ligar-se a partir de um Mac ou Chromebook, consulte os seguintes passos:
   - [Ligue-se a um VM utilizando RDP num Mac](connect-virtual-machine-mac-remote-desktop.md).
   - [Ligue-se a um VM utilizando RDP num Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).  

### <a name="connect-to-the-student-vm-using-x2go"></a>Ligue-se ao estudante VM usando X2Go
Os alunos podem usar o X2Go para se conectarem aos seus VMs Linux depois de o seu instrutor configurar o seu laboratório com x2Go e os pacotes GUI para um ambiente de ambiente de trabalho gráfico Linux (como MATE, XFCE, etc.

Os alunos precisam de descobrir pelo seu instrutor que o ambiente de trabalho gráfico do Linux instalou o seu instrutor.  Esta informação é necessária nos próximos passos para se conectar usando o cliente X2Go.

1. Instale o [cliente X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) no seu computador local.

1. Siga as instruções na [primeira secção](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) para copiar as informações de ligação SSH para o seu VM.  Precisa desta informação para se conectar usando o cliente X2Go.

1. Assim que tiver as informações de ligação SSH, abra o cliente X2Go e selecione **Session**  >  **New Session**.
   ![X2Go cria nova sessão](../media/how-to-use-classroom-lab/x2go-new-session.png)

1. Introduza os valores no painel **de preferências** da sessão com base nas informações de ligação SSH.  Por exemplo, as suas informações de ligação serão semelhantes a esta:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    Utilizando este exemplo, são introduzidos os seguintes valores:

   - Nome da **sessão** - Especifique um nome, como o nome do seu VM.
   - **Anfitrião** - A identificação do seu VM; por exemplo, **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Login** - O nome de utilizador do seu VM; por exemplo, **estudante.**
   - **Porta SSH** - A porta única atribuída ao seu VM; por exemplo, **12345**.
   - Tipo de **sessão** - Selecione o ambiente de trabalho gráfico linux que o seu instrutor configurou o seu VM.  Precisa de obter esta informação do seu instrutor.

    Por fim, clique em **OK** para criar a sessão.

    ![Preferências de sessão x2Go](../media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Clique na sua sessão no painel da direita.

    ![X2Go inicia nova sessão](../media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Se for solicitado com uma mensagem semelhante a esta, selecione **sim** para continuar a introduzir a sua palavra-passe: **A autenticidade do hospedeiro `00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com` '[ [:12345' não pode ser estabelecida.  A impressão digital chave da ECDSA é** SHA256:000000000000000000000000000000000000000000000000.Tem a certeza de que quer continuar a ligar-se (sim/não)?

2. Quando solicitado, introduza a sua palavra-passe e clique em **OK**.  Agora estará remotamente ligado ao ambiente de ambiente de trabalho GUI da sua VM.

## <a name="next-steps"></a>Passos seguintes
Para aprender a ativar a função de ligação remota para os VMs linux num laboratório de sala de aula, consulte [O ambiente de trabalho remoto Enable para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md). 


---
title: Ativar o ambiente de trabalho remoto gráfico para o Linux nos Serviços Azure Lab ; Microsoft Docs
description: Saiba como permitir o ambiente de trabalho remoto para máquinas virtuais Linux em um laboratório em Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 34c940fec388bb0e79ab5e1db9be6d52fb223873
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647958"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Ativar o ambiente de trabalho remoto gráfico para máquinas virtuais Linux nos Serviços Azure Lab
Este artigo mostra-lhe como fazer as seguintes tarefas:

- Ative sessões de desktop remotas gráficas para um Linux VM
- Como ligar-se a um VM Linux utilizando clientes de ambiente de trabalho remoto RDP (Remote Desktop Protocol) ou X2Go

## <a name="set-up-graphical-remote-desktop-solution"></a>Configurar a solução de ambiente de trabalho remoto gráfico
Quando um laboratório é criado a partir de uma imagem **Linux,** o acesso **SSH** (Secure Shell) é automaticamente configurado para que o instrutor possa ligar-se ao VM do modelo a partir da linha de comando utilizando SSH.  Da mesma forma, quando o modelo VM é publicado, os estudantes também podem ligar-se aos seus VMs usando SSH.

Para ligar a um Linux VM utilizando um **GUI** (interface gráfica do utilizador), recomendamos a utilização de **RDP** ou **X2Go**.  Ambas as opções requerem que o instrutor faça alguma configuração adicional no modelo VM:

### <a name="rdp-setup"></a>Configuração RDP
Para utilizar o PDR, o instrutor deve:
  - Ativar a ligação remota do ambiente de trabalho; isto é especificamente necessário para abrir a porta do VM para RDP.
  - Instale o servidor de ambiente de trabalho remoto RDP.
  - Instale um ambiente de ambiente de trabalho gráfico Linux (como MATE, XFCE, e assim por diante).

### <a name="x2go-setup"></a>Configuração X2Go
Para utilizar o X2Go, o instrutor deve:
- Instale o servidor de ambiente de trabalho remoto X2Go.
- Instale um ambiente de ambiente de trabalho gráfico Linux (como MATE, XFCE, e assim por diante).

X2Go usa a mesma porta que já está ativada para SSH.  Como resultado, não é necessária nenhuma configuração extra para abrir uma porta no VM para X2Go.

> [!NOTE]
> Em alguns casos, como com ubuntu LTS 18.04, X2Go proporciona um melhor desempenho.  Se utilizar RDP e notar a latência ao interagir com o ambiente de trabalho gráfico, considere experimentar x2Go uma vez que pode melhorar o desempenho.

> [!IMPORTANT]
>  Algumas imagens do mercado já possuem um ambiente de ambiente de trabalho gráfico e um servidor de ambiente de trabalho remoto instalado.  Por exemplo, a [Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) já tem O Servidor [XFCE e X2Go instalado e configurado para aceitar ligações ao cliente.](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go)

## <a name="enable-remote-desktop-connection-for-rdp"></a>Ativar a ligação remota de ambiente de trabalho para RDP

Este passo só é necessário para ligar usando RDP.  Se em vez disso planeia utilizar o X2Go, pode saltar para a secção seguinte uma vez que x2Go utiliza a porta SSH.

1.  Durante a criação do laboratório, o instrutor tem a opção de ativar a **conexão de ambiente de trabalho remoto.**  O instrutor deve **permitir** que esta opção abra a porta no Linux VM que é necessário para uma sessão de ambiente de trabalho remoto RDP.  Caso contrário, se esta opção for deixada **desativada,** apenas a porta para SSH é aberta.
  
    ![Screenshot que mostra a janela "Novo laboratório" com a opção "Ativar a ligação remota do ambiente de trabalho".](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. Na caixa de mensagem de **ligação ao ambiente de trabalho remoto ativada,** selecione **Continue com o Ambiente de Trabalho Remoto**. 

    ![Ativar a ligação remota do ambiente de trabalho para uma imagem Linux](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Instalar RDP ou X2Go

Após a criação do laboratório, o instrutor precisa de garantir que um ambiente de ambiente de trabalho gráfico e servidor de ambiente de trabalho remoto são instalados no modelo VM.  Os instrutores devem primeiro ligar-se ao modelo VM utilizando o SSH para instalar as embalagens para:
- Ou o servidor de ambiente de trabalho remoto RDP ou X2Go.
- Um ambiente de trabalho gráfico, como MATE, XFCE, etc.

Depois de configurado, o instrutor pode ligar-se ao VM do modelo utilizando o cliente **Microsoft Remote Desktop (RDP)** ou o cliente **X2Go.**

Siga os passos abaixo para configurar o modelo VM:

1. Se vir **o modelo De personalizar** na barra de ferramentas, selecione-o. Em seguida, **selecione Continue** na caixa de diálogo **do modelo Desativar.** Esta ação inicia o modelo VM.  

    ![Modelo de personalização](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Após o início do VM do modelo, pode selecionar **o modelo De Ligar** e, em seguida, ligar através de **SSH** na barra de ferramentas. 

    ![Conecte-se ao modelo via RDP após a criação do laboratório](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Veja o seguinte Ligar à sua caixa de diálogo **de máquina virtual.** Selecione o botão **Copiar** ao lado da caixa de texto para copiá-lo para a área de transferência. Guarde as informações de ligação SSH. Utilize esta informação de ligação a partir de um terminal SSH (como [o Putty)](https://www.putty.org/)para ligar à máquina virtual.
 
    ![Cadeia de conexão SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Instale RDP ou X2Go juntamente com o ambiente de trabalho gráfico à sua escolha.  Consulte as seguintes instruções:
    - [Instalar e configurar RDP](../virtual-machines/linux/use-remote-desktop.md)
    - [Instalar e configurar x2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Ligue ao modelo VM através do GUI

Após a configuração do modelo VM, o instrutor pode ligar-se através do GUI utilizando o cliente **Microsoft Remote Desktop (RDP)** ou o cliente **X2Go.**  O cliente que utiliza depende se RDP ou X2Go estiverem configurados como o servidor de ambiente de trabalho remoto no modelo VM.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Cliente do Microsoft Remote Desktop (RDP)

O cliente do Microsoft Remote Desktop (RDP) é utilizado para ligar a um VM de modelo que tem RDP configurado.  O cliente remote desktop pode ser usado em Windows, Chromebooks, Macs e muito mais.  Consulte o artigo sobre [clientes remote desktop](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) para mais detalhes.

Siga os passos abaixo com base no tipo de computador utilizado para ligar ao modelo VM:

- Windows
  1. Clique em **Ligar para o modelo** na barra de ferramentas do seu laboratório e selecione Connect via **RDP** para ligar ao modelo VM. 
  1. Guarde o ficheiro RDP e utilize-o para ligar ao modelo VM utilizando o cliente Remote Desktop. 
  1. Normalmente, o cliente remote desktop já está instalado e configurado no Windows.  Como resultado, tudo o que precisa fazer é clicar no ficheiro RDP para abri-lo e iniciar a sessão remota.

- Mac
  1. Clique em **Ligar para o modelo** na barra de ferramentas do seu laboratório e, em seguida, selecione Connect via **RDP** para guardar o ficheiro RDP.  
  1. Em seguida, consulte o artigo ["Ligar a um VM" utilizando RDP num Mac](connect-virtual-machine-mac-remote-desktop.md).

- Livro cromado
  1. Clique em **Ligar para o modelo** na barra de ferramentas do seu laboratório e, em seguida, selecione Connect via **RDP** para guardar o ficheiro RDP.  
  1. Em seguida, consulte o artigo ["Ligar a um VM" utilizando RDP num Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>Cliente X2Go

O cliente X2Go é utilizado para ligar a um modelo VM que tem X2Go configurado.  Utilizando as informações de ligação SSH do modelo VM, siga os passos no artigo de como [ligar a um VM utilizando X2Go](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go).

## <a name="next-steps"></a>Próximos passos
Depois de um instrutor configurar RDP ou X2Go no seu modelo VM e publicar, os alunos podem ligar-se aos seus VMs através do ambiente de trabalho remoto gui ou SSH.

Para obter mais informações, veja:
 - [Ligar a uma VM do Linux](how-to-use-remote-desktop-linux-student.md)
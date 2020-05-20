---
title: Ativar o ambiente de trabalho remoto gráfico para o Linux nos Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a ativar o ambiente de trabalho remoto para máquinas virtuais Linux num laboratório em Serviços de Laboratório Azure.
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
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: cc59cd40701957591faba3fb91c2596bc92e21e3
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701751"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Ativar o ambiente de trabalho remoto gráfico para máquinas virtuais Linux nos Serviços de Laboratório Azure
Este artigo mostra-lhe como fazer as seguintes tarefas:

- Ativar sessões gráficas remotas de desktop para um VM Linux
- Como ligar a um VM Linux utilizando clientes RDP (Remote Desktop Protocol) ou X2Go remote desktop

## <a name="set-up-graphical-remote-desktop-solution"></a>Configurar uma solução gráfica remota de ambiente de trabalho
Quando um laboratório é criado a partir de uma imagem **linux,** o acesso **SSH** (Secure Shell) é configurado automaticamente para que o instrutor possa ligar-se ao modelo VM a partir da linha de comando usando SSH.  Da mesma forma, quando o modelo VM é publicado, os alunos também podem ligar-se aos seus VMs usando SSH.

Para ligar a um VM Linux utilizando um **GUI** (interface gráfica do utilizador), recomendamos a utilização de **RDP** ou **X2Go**.  Ambas as opções requerem que o instrutor faça alguma configuração adicional no modelo VM:

### <a name="rdp-setup"></a>Configuração RDP
Para utilizar o PDR, o instrutor deve:
  - Ativar a ligação remota de ambiente de trabalho; isto é especificamente necessário para abrir o porto do VM para rdp.
  - Instale o servidor de ambiente de trabalho remoto RDP.
  - Instale um ambiente de trabalho gráfico Linux (como MATE, XFCE, etc.).

### <a name="x2go-setup"></a>Configuração X2Go
Para utilizar o X2Go, o instrutor deve:
- Instale o servidor de ambiente de trabalho remoto X2Go.
- Instale um ambiente de trabalho gráfico Linux (como MATE, XFCE, etc.).

X2Go utiliza a mesma porta que já está ativada para SSH.  Como resultado, não é necessária nenhuma configuração extra para abrir uma porta no VM para X2Go.

> [!NOTE]
> Em alguns casos, como com Ubuntu LTS 18.04, x2Go proporciona um melhor desempenho.  Se utilizar RDP e notar latência ao interagir com o ambiente de ambiente de trabalho gráfico, considere experimentar x2Go, uma vez que pode melhorar o desempenho.

> [!IMPORTANT]
>  Algumas imagens do mercado já têm um ambiente de trabalho gráfico e servidor de ambiente de trabalho remoto instalado.  Por exemplo, a Máquina Virtual de Ciência de [Dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) já tem [O Servidor XFCE e X2Go instalado e configurado para aceitar ligações](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro#x2go)ao cliente .

## <a name="enable-remote-desktop-connection-for-rdp"></a>Ativar a ligação remota para o ambiente de trabalho para RDP

Este passo só é necessário para ligar utilizando rdp.  Se em vez disso planeia utilizar o X2Go, pode saltar para a secção seguinte, uma vez que x2Go utiliza a porta SSH.

1.  Durante a criação do laboratório, o instrutor tem a opção de ativar a **ligação remota**de ambiente de trabalho .  O instrutor deve **ativar** esta opção de abrir a porta no VM Linux que é necessário para uma sessão de ambiente de trabalho remoto RDP.  Caso contrário, se esta opção for deixada **desativada,** apenas a porta para SSH é aberta.
  
    ![Ativar a ligação remota para o ambiente de trabalho para uma imagem linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. Na caixa de mensagens de ligação à **secretária de trabalho remota ativa,** selecione Continue com o Ambiente de Trabalho **Remoto**. 

    ![Ativar a ligação remota para o ambiente de trabalho para uma imagem linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Instalar RDP ou X2Go

Após a criação do laboratório, o instrutor precisa de garantir que um ambiente de trabalho gráfico e um servidor de ambiente de trabalho remoto são instalados no modelo VM.  Os instrutores devem primeiro ligar-se ao modelo VM utilizando SSH para instalar as embalagens para:
- Quer o servidor de ambiente de trabalho remoto RDP ou X2Go.
- Um ambiente de trabalho gráfico, como MATE, XFCE, etc.

Depois de configurado, o instrutor pode ligar-se ao modelo VM utilizando o cliente **Microsoft Remote Desktop (RDP)** ou o cliente **X2Go.**

Siga os passos abaixo para configurar o modelo VM:

1. Se vir o **modelo Personalizar** na barra de ferramentas, selecione-o. Em seguida, selecione **Continuar** na caixa de diálogo do **modelo Personalizar.** Esta ação inicia o modelo VM.  

    ![Modelo de personalizar](../media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Depois do vM do modelo ter começado, pode selecionar o **modelo De Ligar** e, em seguida, ligar através do **SSH** na barra de ferramentas. 

    ![Ligue-se ao modelo via RDP após a criação do laboratório](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Veja o seguinte Ligar à sua caixa de diálogo **virtual.** Selecione o botão **Copiar** ao lado da caixa de texto para copiá-lo para a área de recortes. Guarde as informações de ligação SSH. Utilize esta informação de ligação a partir de um terminal SSH (como [putty)](https://www.putty.org/)para ligar à máquina virtual.
 
    ![Cadeia de ligação SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Instale RDP ou X2Go juntamente com o ambiente de trabalho gráfico à sua escolha.  Consulte as seguintes instruções:
    - [Instalar e configurar RDP](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)
    - [Instalar e configurar X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Ligue-se ao modelo VM através do GUI

Após a configuração do modelo VM, o instrutor pode ligar-se através do GUI utilizando o cliente **Microsoft Remote Desktop (RDP)** ou o cliente **X2Go.**  O cliente que utiliza depende se rdP ou X2Go estiver configurado como o servidor de ambiente de trabalho remoto no modelo VM.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Cliente do Microsoft Remote Desktop (RDP)

O cliente microsoft Remote Desktop (RDP) é usado para ligar a um Modelo VM que tem RDP configurado.  O cliente Remote Desktop pode ser utilizado em Windows, Chromebooks, Macs e muito mais.  Consulte o artigo sobre [clientes do Remote Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) para mais detalhes.

Siga os passos abaixo com base no tipo de computador utilizado para ligar ao modelo VM:

- Windows
  1. Clique **em Ligar ao modelo** na barra de ferramentas do seu laboratório e selecione Ligar via **RDP** para ligar ao modelo VM. 
  1. Guarde o ficheiro RDP e use-o para se ligar ao modelo VM utilizando o cliente Remote Desktop. 
  1. Normalmente, o cliente Remote Desktop já está instalado e configurado no Windows.  Como resultado, tudo o que precisa fazer é clicar no ficheiro RDP para abri-lo e iniciar a sessão remota.

- Mac
  1. Clique em **Ligar para o modelo** na barra de ferramentas do seu laboratório e, em seguida, selecione Ligar via **RDP** para guardar o ficheiro RDP.  
  1. Em seguida, consulte o artigo como-fazer [Connect to a VM usando RDP em um Mac](connect-virtual-machine-mac-remote-desktop.md).

- Chromebook
  1. Clique em **Ligar para o modelo** na barra de ferramentas do seu laboratório e, em seguida, selecione Ligar via **RDP** para guardar o ficheiro RDP.  
  1. Em seguida, consulte o artigo como-fazer [Connect to a VM usando RDP num Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>Cliente X2Go

O cliente X2Go é usado para ligar a um Modelo VM que tem X2Go configurado.  Utilizando as informações de ligação SSH do modelo VM, siga os passos no artigo Como ligar [a um VM usando X2Go](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go).

## <a name="next-steps"></a>Passos seguintes
Depois de um instrutor configurar rdp ou X2Go no seu modelo VM e publicar, os alunos podem ligar-se aos seus VMs através do ambiente de trabalho remoto GUI ou SSH.

Para obter mais informações, consulte:
 - [Ligue-se a um Linux VM](how-to-use-remote-desktop-linux-student.md)

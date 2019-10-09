---
title: Configurar um laboratório de script do shell do Linux com o Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar o script de Shell no Linux.
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
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 69fe370029cef391b8ee483ffdee5c588df41d58
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030666"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Configurar um laboratório para ensinar scripts de Shell no Linux
Este artigo mostra como configurar um laboratório para ensinar o script de Shell no Linux. O script é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de exemplo, a classe abrange scripts de bash tradicionais e scripts aprimorados. Scripts aprimorados são scripts que combinam comandos de bash e Ruby. Essa abordagem permite que o Ruby transmita dados e bash comandos para interagir com o Shell. 

Os alunos que tomam essas classes de script obtêm uma máquina virtual Linux para aprender as noções básicas do Linux e também se familiarizarem com os scripts do shell bash. A máquina virtual Linux vem com o acesso à área de trabalho remota habilitado e com os editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de ter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services ou usar uma conta de laboratório existente. Consulte o tutorial a seguir para criar uma nova conta de laboratório: [Tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).

Depois de criar a conta de laboratório, habilite as seguintes configurações na conta do laboratório: 

| Configuração de conta do laboratório | Instruções |
| ----------- | ------------ |  
| Imagens do Marketplace | Habilite a imagem do [Ubuntu Server 18, 4 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer1804LTS) para uso em sua conta de laboratório. Para obter mais informações, consulte [especificar imagens do Marketplace disponíveis para criadores de laboratório](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators) | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e aplicar as seguintes configurações:

| Configurações do laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da VM (máquina virtual) | Pequeno  |
| Imagem da VM | [Ubuntu Server 18, 4 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer1804LTS) |
| Habilitar conexão de área de trabalho remota | Desabilitar. <p>Habilitar essa configuração permitirá que professores e alunos se conectem às suas VMs usando a área de trabalho remota (RDP). Para obter mais informações, consulte [habilitar a área de trabalho remota para máquinas virtuais Linux em um laboratório no Azure Lab Services](how-to-enable-remote-desktop-linux.md#teachers-connecting-to-the-template-vm-using-rdp). </p>|


## <a name="install-desktop-and-xrdp"></a>Instalar o desktop e o xrdp
A imagem do [Ubuntu Server 18, 4 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer1804LTS) não tem o servidor de área de trabalho remota instalado por padrão. Siga as instruções no artigo [instalar e configurar área de trabalho remota para se conectar a uma VM do Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md) para instalar os pacotes necessários no computador de modelo para se conectar via protocolo de área de trabalho remota.

## <a name="install-ruby"></a>Instalar o Ruby
O Ruby é uma linguagem dinâmica de software livre que pode ser combinada com scripts bash. Esta seção mostra como usar `apt-get` para instalar a versão mais recente do [Ruby](https://www.ruby-lang.org/).

1. Instale as atualizações executando os seguintes comandos:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Instale o [Ruby](https://www.ruby-lang.org/).  O Ruby é uma linguagem dinâmica de software livre que pode ser combinada com scripts bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalar ferramentas de desenvolvimento
Esta seção mostra como instalar alguns editores de texto. Gedit é o editor de texto padrão para o ambiente de desktop GNOME. Ele foi projetado como um editor de texto de uso geral. Visual Studio Code é um editor de texto que inclui suporte para depuração e integração de controle do código-fonte.

> [!NOTE]
> Há vários editores de texto diferentes disponíveis. Visual Studio Code e gedit são apenas dois exemplos.

1. Instale o [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Instale o [Visual Studio Code](https://code.visualstudio.com/).  O Visual Studio Code pode ser instalado usando o snap Store.  Para obter opções de instalação alternativas, consulte [Visual Studio Code downloads alternativos](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    O modelo agora está atualizado e tem a linguagem de programação e as ferramentas de desenvolvimento necessárias para concluir o laboratório. A imagem de modelo agora pode ser publicada no laboratório. Selecione o botão **publicar** na página modelo para publicar o modelo no laboratório.  

## <a name="cost"></a>Custo 
Se você quiser estimar o custo deste laboratório, poderá usar o exemplo a seguir:
 
Para uma classe de 25 alunos com 20 horas de tempo de classe agendada e 10 horas de cota para a casa ou as atribuições, o preço do laboratório seria: 

25 alunos * (20 + 10) horas * 20 unidades de laboratório * 0, 1 USD por hora = 150 USD

Para obter mais informações sobre os preços, encontre o seguinte documento: [Preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo descreveu as etapas para criar um laboratório para classes de script. Embora este artigo se concentre na configuração das ferramentas de script do Ruby no computador Linux, a mesma configuração pode ser usada para outras classes de script, como Python no Linux.

## <a name="next-steps"></a>Passos seguintes
As próximas etapas são comuns à configuração de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](tutorial-setup-classroom-lab.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de registro de email para alunos](tutorial-setup-classroom-lab.md#send-an-email-with-the-registration-link). 






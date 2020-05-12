---
title: Crie um laboratório de scripts linux com serviços de laboratório azure [ Microsoft Docs
description: Aprenda a montar um laboratório para ensinar scripts de concha sintetmente no Linux.
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
ms.openlocfilehash: 1199bca9e2ab7c85ccf5c90879fe5141a55a7bb5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115069"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Criar um laboratório para ensinar o script de concha sintetmente em Linux
Este artigo mostra-te como criar um laboratório para ensinar guiões de conchas no Linux. O scripting é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de amostragem, a classe abrange scripts tradicionais de bash e scripts melhorados. Scripts melhorados são scripts que combinam comandos de bash e Ruby. Esta abordagem permite que ruby passe dados ao redor e esmaga comandos para interagir com a concha. 

Os alunos que têm estas aulas de scripting recebem uma máquina virtual Linux para aprender o básico do Linux, e também se familiarizarem com o script da concha da bash. A máquina virtual Linux vem com acesso remoto ao ambiente de trabalho ativado e com editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

## <a name="lab-configuration"></a>Configuração do laboratório
Para montar este laboratório, precisa de uma assinatura Azure para começar. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Uma vez que tenha uma subscrição Azure, pode criar uma nova conta de laboratório nos Serviços de Laboratório Azure ou usar uma conta de laboratório existente. Consulte o seguinte tutorial para criar uma nova conta de laboratório: [Tutorial para Configurar uma Conta lab.](tutorial-setup-lab-account.md)

Depois de criar a conta de laboratório, ative as seguintes definições na conta de laboratório: 

| Definição de conta de laboratório | Instruções |
| ----------- | ------------ |  
| Imagens do mercado | Ative a imagem do Ubuntu Server 18.04 LTS para utilização na sua conta de laboratório. Para obter mais informações, consulte [Especifique as imagens do Marketplace disponíveis para os criadores de laboratório](specify-marketplace-images.md). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e aplique as seguintes definições:

| Configurações de laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da máquina virtual (VM) | Pequeno  |
| Imagem VM | Ubuntu Server 18.04 LTS |
| Ativar a ligação remota de ambiente de trabalho | Ativar. <p>Permitir esta definição permitirá que professores e alunos se conectem aos seus VMs utilizando o ambiente de trabalho remoto (RDP). Para mais informações, consulte [Enable remote desktop para máquinas virtuais Linux num laboratório em Serviços de Laboratório Azure](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm). </p>|


## <a name="install-desktop-and-xrdp"></a>Instale desktop e xrdp
A imagem Ubuntu Server 18.04 LTS não tem um servidor de ambiente de trabalho remoto instalado por padrão. Siga as instruções no Ambiente de [Trabalho Remoto de Instalação e configure para ligar a um Artigo do Linux VM em Azure](../../virtual-machines/linux/use-remote-desktop.md) para instalar as embalagens necessárias na máquina do modelo para se ligar através de um protocolo de ambiente de trabalho remoto.

## <a name="install-ruby"></a>Instalar o Ruby
Ruby é uma linguagem dinâmica de código aberto que pode ser combinada com scripts de bash. Esta secção mostra como usar `apt-get` para instalar a versão mais recente da [Ruby.](https://www.ruby-lang.org/)

1. Instale atualizações executando os seguintes comandos:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Instale [ruby](https://www.ruby-lang.org/).  Ruby é uma linguagem dinâmica de código aberto que pode ser combinada com scripts de bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalar ferramentas de desenvolvimento
Esta secção mostra-lhe como instalar alguns editores de texto. Gedit é o editor de texto padrão para o ambiente de ambiente de trabalho do gnomo. É projetado como um editor de texto de propósito geral. Visual Studio Code é um editor de texto que inclui suporte para depuração e integração de controlo de fontes.

> [!NOTE]
> Existem vários editores de texto diferentes disponíveis. Visual Studio Code e gedit são apenas dois exemplos.

1. Instale [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Instale o Código do [Estúdio Visual](https://code.visualstudio.com/).  O código Visual Studio pode ser instalado utilizando a Snap Store.  Para opções de instalação [alternativas, consulte transferências alternativas do Visual Studio Code](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    O modelo está agora atualizado e tem tanto a linguagem de programação como as ferramentas de desenvolvimento necessárias para completar o laboratório. A imagem do modelo pode agora ser publicada para o laboratório. Selecione o botão **Publicar** na página do modelo para publicar o modelo no laboratório.  

## <a name="cost"></a>Custo 
Se quiser estimar o custo deste laboratório, pode usar o seguinte exemplo:
 
Para uma turma de 25 alunos com 20 horas de horário de aulas programada e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria: 

25 alunos * (20 + 10) horas * 20 Unidades de Laboratório * 0,01 USD por hora = 150 USD

Para mais informações sobre os preços pode ser encontrada no seguinte documento: Preços dos [Serviços do Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo acompanhou-te pelos degraus para criar um laboratório para aulas de guionismo. Enquanto este artigo se focava em configurar ferramentas de script Ruby na máquina Linux, a mesma configuração pode ser usada para outras aulas de script, como Python no Linux.

## <a name="next-steps"></a>Passos seguintes
Os próximos passos são comuns à criação de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição por e-mail para estudantes.](how-to-configure-student-usage.md#send-invitations-to-users) 






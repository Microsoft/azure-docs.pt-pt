---
title: Crie um laboratório de scripts de conchas Linux com a Azure Lab Services | Microsoft Docs
description: Aprende a criar um laboratório para ensinar a escrever em Linux.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ba14f2fb5263367014b57741c78d6e509df044b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444969"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Crie um laboratório para ensinar scripts de conchas em Linux
Este artigo mostra-lhe como criar um laboratório para ensinar scripts de conchas no Linux. A scripting é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de amostra, a classe cobre scripts tradicionais de bash e scripts melhorados. Scripts melhorados são scripts que combinam comandos de bash e Ruby. Esta abordagem permite à Ruby passar dados e bater comandos para interagir com a concha. 

Os alunos que tomam estas aulas de scripts recebem uma máquina virtual Linux para aprender o básico do Linux, e também se familiarizar com o script de conchas de bash. A máquina virtual Linux vem com acesso remoto ao ambiente de trabalho ativado e com editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

## <a name="lab-configuration"></a>Configuração de laboratório
Para montar este laboratório, precisa de uma assinatura Azure para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Uma vez que tenha uma subscrição do Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab ou usar uma conta de laboratório existente. Consulte o seguinte tutorial para a criação de uma nova conta de laboratório: [Tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).

Depois de criar a conta de laboratório, ative as seguintes definições na conta do laboratório: 

| Definição de conta de laboratório | Instruções |
| ----------- | ------------ |  
| Imagens do mercado | Ative a imagem Ubuntu Server 18.04 LTS para utilização na sua conta de laboratório. Para obter mais informações, consulte [as imagens do Mercado de Especificação disponíveis para os criadores de laboratório.](specify-marketplace-images.md) | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e aplique as seguintes definições:

| Configurações de laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da máquina virtual (VM) | Pequeno  |
| Imagem VM | Ubuntu Server 18.04 LTS|
| Ativar a ligação remota do ambiente de trabalho | Ativar. <p>Ativar esta definição permitirá que professores e alunos se conectem aos seus VMs utilizando o ambiente de trabalho remoto (PDR). Para obter mais informações, consulte [Ative o ambiente de trabalho remoto para máquinas virtuais Linux em laboratório em Azure Lab Services](how-to-enable-remote-desktop-linux.md). </p>|

## <a name="install-desktop-and-rdp"></a>Instalar desktop e RDP
A imagem Ubuntu Server 18.04 LTS não tem o servidor de ambiente de trabalho remoto RDP instalado por predefinição. Siga as instruções no [Ambiente de Trabalho Remoto de Instalação e configuure-se para ligar a um Linux VM em Azure](../virtual-machines/linux/use-remote-desktop.md) para instalar as embalagens necessárias na máquina do modelo para se ligar através do protocolo de ambiente de trabalho remoto (RDP).

## <a name="install-ruby"></a>Instalar o Ruby
Ruby é uma linguagem dinâmica de código aberto que pode ser combinada com scripts de bash. Esta secção mostra como usar `apt-get` para instalar a versão mais recente do [Ruby.](https://www.ruby-lang.org/)

1. Instale atualizações executando os seguintes comandos:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Instale [o Rubi.](https://www.ruby-lang.org/)  Ruby é uma linguagem dinâmica de código aberto que pode ser combinada com scripts de bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalar ferramentas de desenvolvimento
Esta secção mostra como instalar um par de editores de texto. Gedit é o editor de texto padrão para o ambiente de ambiente de trabalho gnomo. É projetado como um editor de texto de propósito geral. Visual Studio Code é um editor de texto que inclui suporte para depurar e integração de controlo de fontes.

> [!NOTE]
> Existem vários editores de texto diferentes disponíveis. Visual Studio Code e gedit são apenas dois exemplos.

1. Instale [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Instale [o Código do Estúdio Visual](https://code.visualstudio.com/).  O código visual do Estúdio pode ser instalado utilizando a Snap Store.  Para opções de instalação [alternativas, consulte as transferências alternativas do Código Do Estúdio Visual](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    O modelo é agora atualizado e tem tanto a linguagem de programação como as ferramentas de desenvolvimento necessárias para completar o laboratório. A imagem do modelo pode agora ser publicada no laboratório. Selecione o botão **Publicar** na página do modelo para publicar o modelo para o laboratório.  

## <a name="cost"></a>Custo 
Se quiser estimar o custo deste laboratório, pode usar o seguinte exemplo:
 
Para uma turma de 25 alunos com 20 horas de horário de aula programado e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria: 

25 alunos * (20 + 10) horas * 20 Unidades de Laboratório * 0,01 USD por hora = 150 USD

Para obter mais informações sobre o preço pode ser consultado no seguinte documento: [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo acompanhou-te pelos degraus para criar um laboratório para aulas de guiões. Enquanto este artigo se focou na configuração de ferramentas de script ruby na máquina Linux, a mesma configuração pode ser usada para outras aulas de scripts como Python em Linux.

## <a name="next-steps"></a>Passos seguintes
Os próximos passos são comuns para a criação de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição de e-mail para estudantes.](how-to-configure-student-usage.md#send-invitations-to-users) 






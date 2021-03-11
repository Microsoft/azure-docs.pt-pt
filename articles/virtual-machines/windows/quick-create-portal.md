---
title: Quickstart - Criar um Windows VM no portal Azure
description: Neste início rápido, vai aprender a utilizar o portal do Azure para criar uma máquina virtual do Windows
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5e52fab081a94ad58e91c629f4092ae889d38e7a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560927"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Início Rápido: Criar uma máquina virtual do Windows no portal do Azure

As máquinas virtuais do Azure (VMs) podem ser criadas através do portal do Azure. Este método fornece uma interface de utilizador baseada no browser para criar VMs e os respetivos recursos associados. Este quickstart mostra-lhe como usar o portal Azure para implementar uma máquina virtual (VM) em Azure que executa o Windows Server 2019. Para ver a VM em ação, estabeleça o RDP para a VM e instale o servidor Web IIS.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Digite **máquinas virtuais** na pesquisa.
1. Em **Serviços**, selecione **Máquinas Virtuais.**
1. Na página **'Máquinas Virtuais',** selecione **Adicionar**. 
1. No separador **Noções básicas**, em **Detalhes do projeto**, certifique-se de que está selecionada a subscrição correta e, em seguida, selecione **Criar novo** no grupo de recursos. Digite *myResourceGroup* para o nome. 

    ![Screenshot da secção de detalhes do Projeto mostrando onde seleciona a subscrição do Azure e o grupo de recursos para a máquina virtual](./media/quick-create-portal/project-details.png)

1. Em **Caso de exemplo,** digite *myVM* para o **nome da máquina virtual** e escolha Os EUA *Orientais* para a sua **Região**, e, em seguida, escolha *o Centro de Dados do Windows Server 2019* para a **imagem**. Mantenha as restantes predefinições inalteradas.

    ![Screenshot da secção de detalhes da Instância onde fornece um nome para a máquina virtual e seleciona a sua região, imagem e tamanho](./media/quick-create-portal/instance-details.png)

1. Em **Conta de administrador**, forneça um nome de utilizador, como *utilizadordoazure*, e uma palavra-passe. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Screenshot da secção de conta do administrador onde fornece o nome de utilizador e senha do administrador](./media/quick-create-portal/administrator-account.png)

1. De acordo com **as regras de entrada**, escolha Permitir portas **selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP (80)** a partir do drop-down.

    ![Screenshot da secção de regras da porta de entrada onde seleciona quais as ligações de entrada das portas permitidas](./media/quick-create-portal/inbound-port-rules.png)

1. Mantenha as restantes predefinições e, em seguida, selecione o botão **Rever + criar** na parte inferior da página.

    ![Screenshot mostrando o Revisão e criar botão na parte inferior da página](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Crie uma ligação de ambiente de trabalho remoto para a máquina virtual. Estas instruções indicam como ligar à VM a partir de um computador Windows. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) a partir da Mac App Store.

1. Selecione o botão **'Ligar'** na página geral para a sua máquina virtual. 

    ![Screenshot da página geral da máquina que mostra a localização do botão de ligação](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Na página **'Ligar à máquina virtual',** mantenha as opções predefinidos para ligar por endereço IP, sobre a porta 3389, e clique em **Descarregar ficheiro RDP**.

2. Abra o ficheiro RDP transferido e clique em **Ligar** quando lhe for pedido. 

3. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Digite o nome de utilizador como nome **de** \\ *utilizador* local, introduza a palavra-passe que criou para a máquina virtual e, em seguida, clique em **OK**.

4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para criar a ligação.

## <a name="install-web-server"></a>Instalar o servidor Web

Para ver a VM em ação, instale o servidor Web do IIS. Abra uma janela do PowerShell na VM e execute o comando seguinte:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Quando terminar, feche a ligação RDP à VM.


## <a name="view-the-iis-welcome-page"></a>Ver a página de boas-vindas do IIS

No portal, selecione o VM e na visão geral do VM, utilize o botão **Click para copiar** à direita do endereço IP para copiá-lo e colá-lo num separador de navegador. A página de boas-vindas do IIS por defeito será aberta, e deve ser assim:

![Screenshot do site padrão do IIS em um navegador](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. 

Selecione o grupo de recursos para a máquina virtual e, em seguida, **selecione Delete**. Confirme o nome do grupo de recursos para terminar a eliminação dos recursos.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma máquina virtual simples, abriu uma porta de rede para o tráfego Web e instalou um servidor Web básico. Para saber mais sobre as máquinas virtuais do Azure, continue com o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](./tutorial-manage-vm.md)

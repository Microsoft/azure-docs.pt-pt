---
title: Quickstart - Criar um VM Windows no portal Azure
description: Neste início rápido, vai aprender a utilizar o portal do Azure para criar uma máquina virtual do Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 002d374f5be606688121ef4a3952383567c43e85
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240045"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Início Rápido: Criar uma máquina virtual do Windows no portal do Azure

As máquinas virtuais do Azure (VMs) podem ser criadas através do portal do Azure. Este método fornece uma interface de utilizador baseada no browser para criar VMs e os respetivos recursos associados. Este quickstart mostra-lhe como usar o portal Azure para implementar uma máquina virtual (VM) no Azure que executa o Windows Server 2019. Para ver a VM em ação, estabeleça o RDP para a VM e instale o servidor Web IIS.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Digite **máquinas virtuais** na procura.
1. Em **Serviços,** selecione **máquinas Virtuais**.
1. Na página das **máquinas Virtuais,** selecione **Adicionar**. 
1. No separador **Noções básicas**, em **Detalhes do projeto**, certifique-se de que está selecionada a subscrição correta e, em seguida, selecione **Criar novo** no grupo de recursos. Digite *o myResourceGroup* para o nome. 

    ![Criar um novo grupo de recursos para a VM](./media/quick-create-portal/project-details.png)

1. Em **detalhes de Instância**, *escreva myVM* para o nome da máquina **virtual** e escolha Os EUA *Orientais* para a sua **Região**, e, em seguida, escolha o *Datacenter Windows Server 2019* para a **Imagem**. Mantenha as restantes predefinições inalteradas.

    ![Secção de detalhes da instância](./media/quick-create-portal/instance-details.png)

1. Em **Conta de administrador**, forneça um nome de utilizador, como *utilizadordoazure*, e uma palavra-passe. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Introduza o seu nome de utilizador e palavra-passe](./media/quick-create-portal/administrator-account.png)

1. De acordo com as regras da **porta de entrada,** escolha **permitir portas selecionadas** e, em seguida, selecionar **RDP (3389)** e **HTTP (80)** a partir da queda.

    ![Abrir portas para RDP e HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Mantenha as restantes predefinições e, em seguida, selecione o botão **Rever + criar** na parte inferior da página.

    ![Rever e criar](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Crie uma ligação de ambiente de trabalho remoto para a máquina virtual. Estas instruções indicam como ligar à VM a partir de um computador Windows. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) a partir da Mac App Store.

1. Clique no botão **Ligar** na página de visão geral para a sua máquina virtual. 

    ![Ligar a uma VM do Azure a partir do portal](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Na página **Connect to virtual machine,** mantenha as opções predefinidas para ligar por endereço IP, através da porta 3389, e clique em **Download FICHEIRO RDP**.

2. Abra o ficheiro RDP transferido e clique em **Ligar** quando lhe for pedido. 

3. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Escreva o nome de utilizador como **localhost**\\*nome de utilizador*, introduza a palavra-passe que criou para a máquina virtual e clique em **OK**.

4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para criar a ligação.

## <a name="install-web-server"></a>Instalar o servidor Web

Para ver a VM em ação, instale o servidor Web do IIS. Abra uma janela do PowerShell na VM e execute o comando seguinte:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Quando terminar, feche a ligação RDP para a VM.


## <a name="view-the-iis-welcome-page"></a>Ver a página de boas-vindas do IIS

No portal, selecione o VM e na visão geral do VM, utilize o botão **Click para copiar** à direita do endereço IP para copiá-lo e colar num separador de navegador. A página de boas-vindas padrão do IIS será aberta, e deve ser assim:

![Site predefinido do IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. 

Selecione o grupo de recursos para a máquina virtual e, em seguida, selecione **Eliminar**. Confirme o nome do grupo de recursos para terminar a adesão dos recursos.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou uma máquina virtual simples, abriu uma porta de rede para o tráfego Web e instalou um servidor Web básico. Para saber mais sobre as máquinas virtuais do Azure, continue com o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](./tutorial-manage-vm.md)
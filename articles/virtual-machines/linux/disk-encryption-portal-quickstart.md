---
title: Criar e criptografar uma VM do Linux com o portal do Azure
description: Neste guia de início rápido, você aprende a usar o portal do Azure para criar e criptografar uma máquina virtual do Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 0249867a6af16505a692e7090639807ed3d239c9
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031078"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Início rápido: Criar e criptografar uma máquina virtual com o portal do Azure

As máquinas virtuais do Azure (VMs) podem ser criadas através do portal do Azure. O portal do Azure é uma interface de utilizador baseada no browser para criar VMs e os respetivos recursos associados. Neste guia de início rápido, você usará o portal do Azure para implantar uma VM (máquina virtual) do Linux que executa o Ubuntu 18, 4 LTS, criará um cofre de chaves para o armazenamento de chaves de criptografia e criptografará a VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

1. Selecione a opção **Criar um recurso** no canto superior esquerdo do portal do Azure
1. Na caixa Pesquisar, introduza **Key Vault**.
1. Na lista de resultados, selecione **Key Vault**.
1. Na seção Key Vault, selecione **criar**.
1. Na tela **criar cofre de chaves** , escolha um nome exclusivo para o novo cofre de chaves.

    > [!Important]
    > Cada Key Vault deve ter um nome exclusivo. O exemplo a seguir cria um Key Vault chamado *myADEKV*, mas você deve nomear algo diferente.

1. Selecione uma **assinatura**.
1.  Em **grupo de recursos**, selecione **criar novo**. No pop-up, escreva *myResourceGroup* no nome do grupo de recursos e, em seguida, selecione **OK**. 

    ![Tela de criação do grupo de recursos](./media/disk-encryption/portal-qs-keyvaultcreation.png)

1. No menu suspenso **local** , escolha **leste dos EUA**.
1. Deixe as outras opções com os valores predefinidos.
1. Selecione "políticas de acesso", que levará você para uma nova tela.
1. Marque a caixa de seleção ao lado de "habilitar o acesso a Azure Disk Encryption para criptografia de volume.

    ![Tela de criação de resourcegroup](./media/disk-encryption/portal-qs-keyvault-enable-encryption.png)

1. Na parte inferior da tela políticas de acesso, clique em "revisar + criar".
1. Após a revisão, clique em "criar".

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.

1. Na nova página, em popular, selecione **Ubuntu Server 18, 4 LTS**.
1. Na guia **noções básicas** , em **detalhes do projeto**, verifique se a assinatura correta está selecionada.
1. Para **grupo de recursos**, selecione o grupo de recursos que você criou ao tornar o cofre de chaves acima (por exemplo, **MyResource**Group)
1. Para **nome da máquina virtual** , insira *MyVM*e escolha 
1. Para **região**, selecione a mesma região que você usou ao tornar o cofre de chaves acima (por exemplo, **leste dos EUA**).
1. Verifique se o **tamanho** é *Standard D2s v3*.
1. Em **conta de administrador**, selecione **senha**. Insira um nome de usuário e uma senha.
    tela de criação do ![ResourceGroup @ no__t-1
1. Selecione a guia "gerenciamento" e verifique se você tem uma conta de armazenamento de diagnóstico. Se você não tiver contas de armazenamento, selecione "criar nova", dê um nome à sua nova conta e selecione "OK" @no__t tela de criação de 0ResourceGroup @ no__t-1
1. Clique em "revisar + criar".
1. Na página **Criar uma máquina virtual**, pode ver os detalhes sobre a VM que está prestes a criar. Quando estiver pronto, selecione **Criar**.

Irá demorar alguns minutos até a VM ser implementada. Quando a implementação estiver concluída, avance para a secção seguinte.

## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual

1. Quando a implantação da VM for concluída, selecione **ir para o recurso**.
1. Na barra lateral esquerda, selecione **discos**.
1. Na tela discos, selecione **criptografia**. 

    ![seleção de discos e criptografia](./media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na tela criptografia, em **discos para criptografar**, escolha **sistema operacional e discos de dados**.
1. Em **configurações de criptografia**, clique em "selecionar um cofre de chaves e uma chave para criptografia".
1. Na barra lateral direita, selecione o nome do cofre de chaves que você criou anteriormente como o valor para * Key Vault * * e clique em **selecionar**.

    ![seleção de discos e criptografia](./media/disk-encryption/portal-qs-encrypt-vm-screen.png)
1. Na parte superior da tela criptografia, clique em "salvar". Um pop-up avisará que a VM será reinicializada. Clique em **Sim**.


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos para a máquina virtual, selecione Excluir e confirme o nome do grupo de recursos a ser excluído.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um Key Vault que foi habilitado para chaves de criptografia, criou uma máquina virtual e habilitou a máquina virtual para criptografia.  

> [!div class="nextstepaction"]
> [Visão geral de Azure Disk Encryption](disk-encryption-overview.md)
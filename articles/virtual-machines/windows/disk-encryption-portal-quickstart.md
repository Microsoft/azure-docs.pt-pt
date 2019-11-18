---
title: Criar e criptografar uma VM do Windows com o portal do Azure
description: Neste guia de início rápido, você aprende a usar o portal do Azure para criar e criptografar uma máquina virtual do Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 2518de3ad5d0f583cd4962796e4adda5751351df
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151016"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Início rápido: criar e criptografar uma máquina virtual do Windows com o portal do Azure

As máquinas virtuais do Azure (VMs) podem ser criadas através do portal do Azure. O portal do Azure é uma interface de utilizador baseada no browser para criar VMs e os respetivos recursos associados. Neste guia de início rápido, você usará o portal do Azure para implantar uma VM (máquina virtual) do Windows que executa o Ubuntu 18, 4 LTS, criará um cofre de chaves para o armazenamento de chaves de criptografia e criptografará a VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na nova página, em popular, selecione **Windows Server 2016 datacenter**.
1. Na guia noções básicas, em detalhes do projeto, verifique se a assinatura correta está selecionada e, em seguida, escolha **criar novo grupo de recursos**. Insira *Myresourceobject* como o nome.
1. Para **nome da máquina virtual**, insira *MyVM*.
1. Para **região**, selecione a mesma região que você usou ao tornar o cofre de chaves acima (por exemplo, *leste dos EUA*).
1. Verifique se o **tamanho** é *Standard D2s v3*.
1. Em **conta de administrador**, selecione **senha**. Insira um nome de usuário e uma senha.
    ![tela de criação de resourcegroup](../media/disk-encryption/portal-qs-windows-vm-creation.png)
1. Selecione a guia "gerenciamento" e verifique se você tem uma conta de armazenamento de diagnóstico. Se você não tiver contas de armazenamento, selecione "criar nova", dê um nome à sua nova conta e selecione "OK" ![tela de criação de resourcegroup](../media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Clique em "revisar + criar".
1. Na página **Criar uma máquina virtual**, pode ver os detalhes sobre a VM que está prestes a criar. Quando estiver pronto, selecione **Criar**.

Irá demorar alguns minutos até a VM ser implementada. Quando a implementação estiver concluída, avance para a secção seguinte.

## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual

1. Quando a implantação da VM for concluída, selecione **ir para o recurso**.
1. Na barra lateral esquerda, selecione **discos**.
1. Na tela discos, selecione **criptografia**. 

    ![seleção de discos e criptografia](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na tela criptografia, em **discos para criptografar**, escolha **sistema operacional e discos de dados**.
1. Em **configurações de criptografia**, escolha **selecionar um cofre de chaves e uma chave para criptografia**.
1. Na tela **selecionar chave de Azure Key Vault** , selecione **criar novo**.

    ![seleção de discos e criptografia](../media/disk-encryption/portal-qs-keyvault-create.png)

1. Na tela **criar cofre de chaves** , verifique se o grupo de recursos é o mesmo usado para criar a VM.
1. Dê um nome ao seu cofre de chaves.  Cada cofre de chaves no Azure deve ter um nome exclusivo.
1. Na guia **políticas de acesso** , marque a caixa **Azure Disk Encryption para criptografia de volume** .

    ![seleção de discos e criptografia](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Selecione **Rever + criar**.  
1. Depois que o cofre de chaves passar na validação, selecione **criar**. Isso retornará à tela **selecionar chave de Azure Key Vault** .
1. Deixe o campo de **chave** em branco e escolha **selecionar**.
1. Na parte superior da tela criptografia, clique em **salvar**. Um pop-up avisará que a VM será reinicializada. Clique em **Sim**.


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos para a máquina virtual, selecione Excluir e confirme o nome do grupo de recursos a ser excluído.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um Key Vault que foi habilitado para chaves de criptografia, criou uma máquina virtual e habilitou a máquina virtual para criptografia.  

> [!div class="nextstepaction"]
> [Visão geral de Azure Disk Encryption](disk-encryption-overview.md)

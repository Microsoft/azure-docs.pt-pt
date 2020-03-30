---
title: Criar e encriptar uma VM do Linux com o portal do Azure
description: Neste arranque rápido, aprende-se a usar o portal Azure para criar e encriptar uma máquina virtual Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 9f09f28dc63e7f061946a66beb59bd4c62be70aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970561"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Quickstart: Criar e encriptar uma máquina virtual com o portal Azure

As máquinas virtuais do Azure (VMs) podem ser criadas através do portal do Azure. O portal do Azure é uma interface de utilizador baseada no browser para criar VMs e os respetivos recursos associados. Neste arranque rápido, utilizará o portal Azure para implementar uma máquina virtual Linux (VM) que executa ubuntu 18.04 LTS, criar ábreos chave para o armazenamento de chaves de encriptação e encriptar o VM.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na página Nova, em Popular, selecione **Ubuntu Server 18.04 LTS**.
1. No separador **Basics,** sob os detalhes do **Projeto,** certifique-se de que a subscrição correta é selecionada.
1. Para **o grupo Recursos,** selecione o grupo de recursos que criou ao fazer o seu cofre-chave acima (por exemplo, **myResourceGroup**).
1. Para o nome da **máquina virtual,** introduza *MyVM*.
1. Para **a Região,** selecione a mesma região que usou ao fazer o seu cofre-chave acima (por exemplo, **Leste dos EUA).**
1. Certifique-se de que o **tamanho** é *Standard D2s v3*.
1. Na **conta do Administrador,** selecione **Password**. Introduza um nome de utilizador e uma palavra-passe.
    ![Tela de criação do Grupo de Recursos](./media/disk-encryption/portal-qs-vm-creation.png)
1. Selecione o separador "Gestão" e verifique se tem uma Conta de Armazenamento de Diagnósticos. Se não tiver contas de armazenamento, selecione "Create New", dê ![um nome à sua nova conta e selecione o ecrã de criação do Grupo de Recursos "Ok"](./media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Clique em "Rever + Criar".
1. Na página **Criar uma máquina virtual**, pode ver os detalhes sobre a VM que está prestes a criar. Quando estiver pronto, selecione **Criar**.

Irá demorar alguns minutos até a VM ser implementada. Quando a implementação estiver concluída, avance para a secção seguinte.

## <a name="encrypt-the-virtual-machine"></a>Criptografe a máquina virtual

1. Quando a implementação vm estiver completa, selecione **Ir para o recurso**.
1. Na barra lateral esquerda, selecione **Discos**.
1. No ecrã dos Discos, selecione **Encriptação**. 

    ![disquetes e seleção de encriptação](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. No ecrã de encriptação, sob **os Discos para encriptar,** escolha **OS e discos**de dados .
1. Nas definições de **encriptação,** escolha **Selecione um cofre de chave e uma chave para encriptação**.
1. Na tecla Select a partir do ecrã **do Cofre de Chaves Azure,** selecione Criar **Novo**.

    ![disquetes e seleção de encriptação](../media/disk-encryption/portal-qs-keyvault-create.png)

1. No ecrã do **cofre de teclas Create,** certifique-se de que o Grupo de Recursos é o mesmo que o que usou para criar o VM.
1. Dê um nome ao cofre da chave.  Todos os cofres chave em Azure devem ter um nome único.
1. No separador Políticas de **Acesso,** verifique a encriptação do **disco Azure para obter** uma caixa de encriptação de volume.

    ![disquetes e seleção de encriptação](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Selecione **Rever + criar**.  
1. Depois de o cofre da chave ter passado a validação, selecione **Criar**. Isto irá devolvê-lo à tecla Select a partir do ecrã **Do cofre de chaves Azure.**
1. Deixe o campo **chave** em branco e escolha **Selecionar**.
1. Na parte superior do ecrã de encriptação, clique em **Guardar**. Um pop-up vai avisá-lo que o VM vai reiniciar. Clique **sim**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione Eliminar e confirme o nome do grupo de recursos a eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um Key Vault que estava habilitado para chaves de encriptação, criou uma máquina virtual e ativou a máquina virtual para encriptação.  

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco azure](disk-encryption-overview.md)

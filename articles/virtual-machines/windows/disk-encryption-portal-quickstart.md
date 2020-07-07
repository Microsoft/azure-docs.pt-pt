---
title: Criar e encriptar uma VM do Windows com o portal do Azure
description: Neste arranque rápido, aprende-se a usar o portal Azure para criar e encriptar uma máquina virtual do Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 1327a2c621eca1cfadcf776ecd62f0899651f0bc
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85807932"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Quickstart: Criar e encriptar uma máquina virtual do Windows com o portal Azure

As máquinas virtuais do Azure (VMs) podem ser criadas através do portal do Azure. O portal do Azure é uma interface de utilizador baseada no browser para criar VMs e os respetivos recursos associados. Neste arranque rápido utilizará o portal Azure para implementar uma máquina virtual Windows, criar um cofre chave para o armazenamento de chaves de encriptação e encriptar o VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na página Nova, em Popular, selecione **O Centro de Dados do Windows Server 2016**.
1. No separador Basics, nos detalhes do Projeto, certifique-se de que a subscrição correta é selecionada e, em seguida, opte por **criar um novo grupo de recursos**. Insira *o myResourceGroup* como o nome.
1. Para **o nome da máquina virtual,** insira o *MyVM*.
1. Para **a Região**, selecione a mesma região que usou ao fazer o seu cofre-chave acima (por exemplo, *Leste dos EUA).*
1. Certifique-se de que o **tamanho** é *Standard D2s v3*.
1. Na **conta do Administrador**, selecione **Password**. Insira um nome de utilizador e uma senha.

    :::image type="content" source="../media/disk-encryption/portal-qs-windows-vm-creation.png" alt-text="Ecrã de criação do Grupo de Recursos":::

    > [!WARNING]
    > O separador "Discos" apresenta um campo "Tipo de Encriptação" nas **opções de Disco**. Este campo é utilizado para especificar opções de encriptação para [Discos Geridos](managed-disks-overview.md) + CMK, e não para Encriptação do Disco Azure. 
    >
    > Para evitar confusões, sugerimos que ignore completamente o separador *Discos* enquanto completa este tutorial. 

1. Selecione o separador "Gestão" e verifique se tem uma Conta de Armazenamento de Diagnóstico. Se não tiver contas de armazenamento, selecione "Create New", dê um nome à sua nova conta e selecione "Ok"

    :::image type="content" source="../media/disk-encryption/portal-qs-vm-creation-storage.png" alt-text="Ecrã de criação do Grupo de Recursos":::

1. Clique em "Review + Create".
1. Na página **Criar uma máquina virtual**, pode ver os detalhes sobre a VM que está prestes a criar. Quando estiver pronto, selecione **Criar**.

Irá demorar alguns minutos até a VM ser implementada. Quando a implementação estiver concluída, avance para a secção seguinte.

## <a name="encrypt-the-virtual-machine"></a>Criptografe a máquina virtual

1. Quando a implementação de VM estiver concluída, selecione **Ir para o recurso**.
1. Na barra lateral esquerda, selecione **Discos**.
1. No ecrã discos, selecione **Encriptação**. 

    :::image type="content" source="../media/disk-encryption/portal-qs-disks-to-encryption.png" alt-text="discos e seleção de encriptação":::

1. No ecrã de encriptação, em **Discos para encriptar,** escolha **os discos de OS e de dados**.
1. Nas **definições de encriptação,** escolha **Selecionar um cofre de tecla e chave para encriptação**.
1. Na **tecla Select a partir do ecrã Azure Key Vault,** selecione **Create New**.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="discos e seleção de encriptação":::

1. No ecrã do **cofre da chave Criar,** certifique-se de que o Grupo de Recursos é o mesmo que o utilizado para criar o VM.
1. Dê um nome ao seu cofre de chaves.  Cada cofre-chave em Azure deve ter um nome único.
1. No separador **Políticas de Acesso,** verifique a encriptação do **disco Azure para obter a caixa de encriptação de volume.**

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-enable.png" alt-text="discos e seleção de encriptação":::

1. Selecione **Rever + criar**.  
1. Depois de passar a validação do cofre da chave, **selecione Criar**. Isto irá devolvê-lo à tecla Select do ecrã **Azure Key Vault.**
1. Deixe o campo **chave** em branco e escolha **Selecione**.
1. No topo do ecrã de encriptação, clique em **Guardar**. Um pop-up irá avisá-lo que o VM vai reiniciar. Clique **em Sim**.


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione Eliminar e confirme o nome do grupo de recursos a eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um Key Vault que permitia chaves de encriptação, criou uma máquina virtual e permitiu a encriptação da máquina virtual.  

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco Azure](disk-encryption-overview.md)

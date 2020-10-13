---
title: Criar e encriptar uma VM do Linux com o portal do Azure
description: Neste arranque rápido, aprende-se a usar o portal Azure para criar e encriptar uma máquina virtual Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 2a5588940d4efacc08d89b72bde4433d725a4994
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978412"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Quickstart: Criar e encriptar uma máquina virtual com o portal Azure

As máquinas virtuais do Azure (VMs) podem ser criadas através do portal do Azure. O portal do Azure é uma interface de utilizador baseada no browser para criar VMs e os respetivos recursos associados. Neste arranque rápido utilizará o portal Azure para implantar uma máquina virtual Linux (VM) que executa ubuntu 18.04 LTS, criará um cofre chave para o armazenamento de chaves de encriptação e criptografe o VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na página Nova, em Popular, selecione **Ubuntu Server 18.04 LTS**.
1. No separador Basics, nos detalhes do Projeto, verifique se a subscrição correta está selecionada.
1. Para "Grupo de Recursos", **selecione Criar novos**. Insira *o myResourceGroup* como o nome e selecione **Ok**.
1. Para **o nome da máquina virtual,** insira o *MyVM*.
1. Para **a Região**, selecione *(EUA) Leste DOS EUA.*
1. Certifique-se de que o **tamanho** é *Standard D2s v3*.
1. Na **conta do Administrador**, selecione *Palavra-passe* como **tipo de autenticação**. Insira um nome de utilizador e uma senha.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-linux-vm-creation.png" alt-text="Tela de criação Linux VM&quot;:::

    > [!WARNING]
    > O separador &quot;Discos&quot; apresenta um campo &quot;Tipo de Encriptação&quot; nas **opções de Disco**. Este campo é utilizado para especificar opções de encriptação para [Discos Geridos](../managed-disks-overview.md) + CMK, **e não** para Encriptação do Disco Azure.
    >
    > Para evitar confusões, sugerimos que ignore completamente o separador *Discos* enquanto completa este tutorial.

1. Selecione o separador &quot;Gestão&quot; e verifique se tem uma Conta de Armazenamento de Diagnóstico. Se não tiver contas de armazenamento, selecione *Create New,* nomeie a sua conta de armazenamento *myStorageAccount*e selecione &quot;Ok"

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="Tela de criação Linux VM&quot;:::

    > [!WARNING]
    > O separador &quot;Discos&quot; apresenta um campo &quot;Tipo de Encriptação&quot; nas **opções de Disco**. Este campo é utilizado para especificar opções de encriptação para [Discos Geridos](../managed-disks-overview.md) + CMK, **e não** para Encriptação do Disco Azure.
    >
    > Para evitar confusões, sugerimos que ignore completamente o separador *Discos* enquanto completa este tutorial.

1. Selecione o separador &quot;Gestão&quot; e verifique se tem uma Conta de Armazenamento de Diagnóstico. Se não tiver contas de armazenamento, selecione *Create New,* nomeie a sua conta de armazenamento *myStorageAccount*e selecione &quot;Ok":::

1. Clique em "Review + Create".
1. Na página **Criar uma máquina virtual**, pode ver os detalhes sobre a VM que está prestes a criar. Quando estiver pronto, selecione **Criar**.

Irá demorar alguns minutos até a VM ser implementada. Quando a implementação estiver concluída, avance para a secção seguinte.

## <a name="encrypt-the-virtual-machine"></a>Criptografe a máquina virtual

1. Quando a implementação de VM estiver concluída, selecione **Ir para o recurso**.
1. Na barra lateral esquerda, selecione **Discos**.
1. Na barra superior, selecione **Definições Adicionais** .
1. Nas **definições de encriptação,**  >  **os discos para encriptar,** selecione **OS e discos de dados**.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="Tela de criação Linux VM&quot;:::

    > [!WARNING]
    > O separador &quot;Discos&quot; apresenta um campo &quot;Tipo de Encriptação&quot; nas **opções de Disco**. Este campo é utilizado para especificar opções de encriptação para [Discos Geridos](../managed-disks-overview.md) + CMK, **e não** para Encriptação do Disco Azure.
    >
    > Para evitar confusões, sugerimos que ignore completamente o separador *Discos* enquanto completa este tutorial.

1. Selecione o separador &quot;Gestão&quot; e verifique se tem uma Conta de Armazenamento de Diagnóstico. Se não tiver contas de armazenamento, selecione *Create New,* nomeie a sua conta de armazenamento *myStorageAccount*e selecione &quot;Ok":::

1. Nas **definições de encriptação,** escolha **Selecionar um cofre de tecla e chave para encriptação**.
1. Na **tecla Select a partir do ecrã Azure Key Vault,** selecione **Create New**.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Tela de criação Linux VM&quot;:::

    > [!WARNING]
    > O separador &quot;Discos&quot; apresenta um campo &quot;Tipo de Encriptação&quot; nas **opções de Disco**. Este campo é utilizado para especificar opções de encriptação para [Discos Geridos](../managed-disks-overview.md) + CMK, **e não** para Encriptação do Disco Azure.
    >
    > Para evitar confusões, sugerimos que ignore completamente o separador *Discos* enquanto completa este tutorial.

1. Selecione o separador &quot;Gestão&quot; e verifique se tem uma Conta de Armazenamento de Diagnóstico. Se não tiver contas de armazenamento, selecione *Create New,* nomeie a sua conta de armazenamento *myStorageAccount*e selecione &quot;Ok":::

1. À esquerda do **cofre e tecla chave,** selecione **Click para selecionar uma tecla**.
1. Na **tecla Select a partir do Cofre da Chave Azure,** sob o campo **Key Vault,** selecione **Criar novos**.
1. No ecrã do **cofre de teclas Create,** certifique-se de que o Grupo de Recursos é *o myResourceGroup*e dê um nome ao cofre da sua chave.  Cada cofre-chave em Azure deve ter um nome único.
1. No separador **Políticas de Acesso,** verifique a encriptação do **disco Azure para obter a caixa de encriptação de volume.**

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="Tela de criação Linux VM&quot;:::

    > [!WARNING]
    > O separador &quot;Discos&quot; apresenta um campo &quot;Tipo de Encriptação&quot; nas **opções de Disco**. Este campo é utilizado para especificar opções de encriptação para [Discos Geridos](../managed-disks-overview.md) + CMK, **e não** para Encriptação do Disco Azure.
    >
    > Para evitar confusões, sugerimos que ignore completamente o separador *Discos* enquanto completa este tutorial.

1. Selecione o separador &quot;Gestão&quot; e verifique se tem uma Conta de Armazenamento de Diagnóstico. Se não tiver contas de armazenamento, selecione *Create New,* nomeie a sua conta de armazenamento *myStorageAccount*e selecione &quot;Ok":::

1. Selecione **Rever + criar**.  
1. Depois de passar a validação do cofre da chave, **selecione Criar**. Isto irá devolvê-lo à tecla Select do ecrã **Azure Key Vault.**
1. Deixe o campo **chave** em branco e escolha **Selecione**.
1. No topo do ecrã de encriptação, clique em **Guardar**. Um pop-up irá avisá-lo que o VM vai reiniciar. Clique em **Sim**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione Eliminar e confirme o nome do grupo de recursos a eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um Key Vault que foi ativado para chaves de encriptação, criou uma máquina virtual e permitiu a encriptação da máquina virtual.  

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco Azure](disk-encryption-overview.md)
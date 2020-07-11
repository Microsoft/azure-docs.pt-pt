---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8956d4f5b2243cab433fcb3abaf2e71da8f8c772
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229419"
---
A configuração de chaves geridas pelo cliente para os seus discos exigirá que crie recursos numa determinada encomenda, se o fizer pela primeira vez. Primeiro, terás de criar e montar um Cofre de Chaves Azure.

## <a name="set-up-your-azure-key-vault"></a>Configurar o seu Cofre de Chaves Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Procure e selecione **Cofres-chave.**

    [![server-side-encryption-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > O cofre da chave Azure, o conjunto de encriptação do disco, o VM, os discos e os instantâneos devem estar todos na mesma região e subscrição para que a implementação tenha sucesso.

1. **Selecione +Adicionar** para criar um novo Cofre de Chaves.
1. Criar um novo grupo de recursos.
1. Insira um nome de cofre chave, selecione uma região e selecione um nível de preços.
1. Selecione **Rever + Criar,** verificar as suas escolhas e, em seguida, selecione **Criar**.

    ![Screenshot da experiência de criação do Azure Key Vault. Mostrando os valores particulares que cria](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Assim que o cofre terminar de ser acionado, selecione-o.
1. Selecione **Teclas** em **Definições**.
1. **Selecione Gerar/Importar.**

    ![Screenshot do painel de configurações de recursos do Cofre de Chaves. Mostra o botão de gerar/importar dentro das definições.](media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Deixe o **tipo de chave** definido para O Tamanho da Chave **RSA** e **RSA** definido para **2048**.
1. Preencha as restantes seleções como quiser e, em seguida, **selecione Criar**.

    ![Screenshot da criação de uma lâmina chave que aparece uma vez que o botão gerar/importar é selecionado](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Configurar o seu conjunto de encriptação de disco

1. Procure por **Conjuntos de Encriptação de Discos** e selecione-o.
1. No conjunto de **encriptação** do disco seleciona a lâmina **+Adicionar**.

    ![Screenshot do ecrã principal do portal de encriptação do disco. Realçar o botão Adicionar](media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Selecione o seu grupo de recursos, nomeie o seu conjunto de encriptação e selecione a mesma região que o cofre de chaves.
1. Selecione **cofre e chave chave**.
1. Selecione o cofre e a chave chave que criou anteriormente, bem como a versão.
1. **Seleção de imprensa**.
1. Selecione **Rever + Criar** e, em seguida, **Criar**.

    ![Screenshot da lâmina de criação de encriptação do disco. Mostrando a subscrição, grupo de recursos, nome de conjunto de encriptação de disco, região e chave do seletor de chaves + chave.](media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-blade-key.png)

1. Abra o conjunto de encriptação do disco assim que terminar de criar e selecione o alerta que aparece.

    ![Screenshot do alerta popup: "Para associar um disco, imagem ou instantâneo com um conjunto de encriptação de disco, deve conceder permissões ao cofre de chaves". Selecione este alerta para continuar](media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

Duas notificações devem aparecer e ter sucesso. Ao fazê-lo, irá utilizar o conjunto de encriptação do disco com o seu cofre de chaves.

![Screenshot de permissão bem sucedida e atribuição de função para o seu cofre de chaves.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

## <a name="deploy-a-vm"></a>Implementar uma VM

Agora que criou e montou o cofre de chaves e o conjunto de encriptação do disco, pode implementar um VM usando a encriptação.
O processo de implementação de VM é semelhante ao processo de implementação padrão, as únicas diferenças são que você precisa implementar o VM na mesma região que os seus outros recursos e você opta por usar uma chave gerida pelo cliente.

1. Procure por **Máquinas Virtuais** e selecione **+ Adicionar** para criar um VM.
1. No separador **Basic,** selecione a mesma região que o seu conjunto de encriptação de disco e Azure Key Vault.
1. Preencha os outros valores no separador **Básico** como quiser.

    ![Screenshot da experiência de criação de VM, com o valor da região em destaque.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. No **separador Discos,** selecione **Encriptação em repouso com uma chave gerida pelo cliente**.
1. Selecione o conjunto de encriptação do disco no conjunto de encriptação do **disco.**
1. Faça as restantes seleções como quiser.

    ![Screenshot da experiência de criação de VM, a lâmina dos discos. Com o conjunto de encriptação do disco em destaque.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Ativar num disco existente

> [!CAUTION]
> Ativar a encriptação do disco em quaisquer discos ligados a um VM exigirá que pare o VM.
    
1. Navegue para um VM que está na mesma região que um dos seus conjuntos de encriptação de disco.
1. Abra o VM e **selecione Stop**.

    ![Screenshot da sobreposição principal para o seu exemplo VM. Com o botão Stop em destaque](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Depois de o VM ter terminado de parar, selecione **Discos** e, em seguida, selecione o disco que pretende encriptar.

    ![Screenshot do seu exemplo VM, com a lâmina dos Discos aberta. O disco SO é realçado, como um disco de exemplo para você selecionar.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Selecione **Encriptação** e selecione **Encriptação em repouso com uma chave gerida pelo cliente** e, em seguida, selecione o conjunto de encriptação do disco na lista de drop-down.
1. Selecione **Guardar**.

    ![Screenshot do seu disco DE EXEMPLO. A lâmina de encriptação está aberta, a encriptação em repouso com uma chave gerida pelo cliente é selecionada, bem como o seu exemplo Azure Key Vault. Depois de estois, o botão de poupança é selecionado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Repita este processo para quaisquer outros discos ligados ao VM que pretende encriptar.
1. Quando os discos terminarem de mudar para teclas geridas pelo cliente, se não houver outros discos anexos que queira encriptar, poderá iniciar o seu VM.

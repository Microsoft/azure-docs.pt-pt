---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3ba5d74aa245fbcd9d43f2b4398387d7f59e202c
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299524"
---
### <a name="portal"></a>vários inquilinos

A configuração de chaves geridas pelo cliente para os seus discos exigirá que crie recursos numa determinada encomenda, caso o esteja a fazer pela primeira vez. Primeiro, terás de criar e montar um Cofre de Chave Azure.

#### <a name="setting-up-your-azure-key-vault"></a>Configurar o seu cofre de chaves Azure

1. Assine no [portal Azure](https://portal.azure.com/) e procure o Cofre chave
1. Procure e selecione **Cofres chave**.

    [![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > O seu cofre de chaves Azure, conjunto de encriptação de discos, VM, discos e instantâneos devem estar todos na mesma região e a subscrição para a implementação tiver sucesso.

1. Selecione **+Adicionar** para criar um novo Cofre chave.
1. Criar um novo grupo de recursos
1. Introduza um nome chave do cofre, selecione uma região e selecione um nível de preços.
1. Selecione **Rever + Criar,** verificar as suas escolhas e, em seguida, selecione **Criar**.

    ![Screenshot da experiência de criação do Cofre Chave Azure. Mostrando os valores particulares que cria](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Assim que o cofre terminar de ser implantado, selecione-o.
1. Selecione **Teclas** em **Definições**.
1. Selecione **Generate/Import**

    ![Screenshot do painel de definições de recursos do Cofre chave. Mostra o botão de geração/importação dentro das definições.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Deixe ambos os tipos de **chave** definidos para **RSA** e **RSA Tamanho** de chave definido para **2080**.
1. Preencha as restantes seleções conforme quiser e, em seguida, selecione **Criar**.

    ![Screenshot da criação de uma lâmina chave que aparece uma vez que gera/importa botão é selecionado](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Configuração do seu conjunto de encriptação de disco

Para criar e configurar conjuntos de encriptação de disco, deve utilizar o seguinte link: https://aka.ms/diskencryptionsets. Se estiver nas regiões do Governo Microsoft Azure, deve utilizar este link: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff). A criação de conjuntos de encriptação de disco ainda não está disponível no portal global Azure.

1. Abra o link de encriptação do disco adequado para a sua região:

    Regiões públicas: [https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Regiões do Governo de Azure: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Selecione **+Adicionar**.

    ![Screenshot do ecrã principal do portal de encriptação do disco. Realçando o botão Adicionar](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Selecione o seu grupo de recursos, nomeie o seu conjunto de encriptação e selecione a mesma região que o seu cofre chave.
1. Selecione **cofre chave e chave**.
1. Selecione o cofre chave e a chave que criou anteriormente, bem como a versão.
1. Pressione **Selecione**.
1. Selecione **Rever + Criar** e, em seguida, **Criar**.

    ![Screenshot da lâmina de criação de encriptação do disco. Mostrando a subscrição, grupo de recursos, nome do conjunto de encriptação do disco, região e cofre chave + seletor de chaves.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Abra o conjunto de encriptação do disco assim que terminar de criar e selecione o alerta que aparece.

    ![Screenshot do alerta popup: "Para associar um disco, imagem ou instantâneo com um conjunto de encriptação de disco, você deve conceder permissões para o cofre chave". Selecione este alerta para continuar](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Duas notificações devem aparecer e ter sucesso. Isto permitir-lhe-á utilizar o conjunto de encriptação do disco com o seu cofre chave.

![Screenshot de permissão bem sucedida e atribuição de papéis para o seu cofre chave.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Implementar uma VM

Agora que criou e montou o seu cofre de chaves e o conjunto de encriptação do disco, pode implementar um VM usando a encriptação.
O processo de implementação de VM é semelhante ao processo de implementação padrão, as únicas diferenças são que você precisa implementar o VM na mesma região que os seus outros recursos e você opta por usar uma chave gerida pelo cliente.

1. Abra o link de encriptação do disco adequado para a sua região:

    Regiões públicas: [https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Regiões do Governo de Azure: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. Procure **por Máquinas Virtuais** e selecione **+ Adicione** para criar um VM.
1. No separador **Basic,** selecione a mesma região que o seu conjunto de encriptação de disco e o Cofre chave Azure.
1. Preencha os outros valores no separador **Basic** como quiser.

    ![Screenshot da experiência de criação vm, com o valor da região em destaque.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. No separador **Disks,** selecione **Encriptação em repouso com uma chave gerida pelo cliente**.
1. Selecione o conjunto de encriptação do disco no conjunto de **encriptação do disco.**
1. Faça as restantes seleções como quiser.

    ![Screenshot da experiência de criação vm, a lâmina dos discos. Com o conjunto de encriptação do disco em destaque.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Ativar num disco existente

Para gerir e configurar a encriptação do disco nos seus discos existentes, deve utilizar o seguinte link: https://aka.ms/diskencryptionsets. Ativar as chaves geridas pelo cliente nos discos existentes ainda não está disponível no portal Azure global.

> [!CAUTION]
> Permitir a encriptação do disco em quaisquer discos ligados a um VM exigirá que pare o VM.

1. Abra o link de encriptação do disco adequado para a sua região:

    Regiões públicas: [https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Regiões do Governo de Azure: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Navegue para um VM que esteja na mesma região que um dos seus conjuntos de encriptação de discos.
1. Abra o VM e selecione **Stop**.

    ![Screenshot da sobreposição principal para o seu vm exemplo. Com o botão Stop em destaque](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Depois de o VM ter terminado de parar, selecione **Discos** e, em seguida, selecione o disco que pretende encriptar.

    ![Screenshot do seu exemplo VM, com a lâmina dos Discos aberta. O disco OS é realçado, como um disco de exemplo para selecionar.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Selecione **encriptação** e selecione **encriptação em repouso com uma chave gerida pelo cliente** e, em seguida, selecione o conjunto de encriptação do disco na lista de drop-down.
1. Selecione **Guardar**.

    ![Screenshot do seu disco de exemplo SO. A lâmina de encriptação está aberta, a encriptação em repouso com uma chave gerida pelo cliente é selecionada, bem como o seu exemplo Azure Key Vault. Depois de efazer as seleções, o botão de salvamento é selecionado.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Repita este processo para quaisquer outros discos ligados ao VM que queira encriptar.
1. Quando os seus discos terminarem de mudar para chaves geridas pelo cliente, se não houver outros discos anexados que queira encriptar, poderá iniciar o seu VM.
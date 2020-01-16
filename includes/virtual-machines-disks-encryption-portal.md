---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 662b2792a2e09603425b1988138326799334f323
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973387"
---
### <a name="portal"></a>Portal

A configuração de chaves gerenciadas pelo cliente para seus discos exigirá que você crie recursos em uma ordem específica, se estiver fazendo isso pela primeira vez. Primeiro, será necessário criar e configurar um Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Configurando sua Azure Key Vault

1. Entre no [portal do Azure](https://portal.azure.com/) e procure Key Vault
1. Procure e selecione **cofres de chaves**.

    [![SSE-Key-Vault-portal-Search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Seu cofre de chaves do Azure, conjunto de criptografia de disco, VM, discos e instantâneos devem estar na mesma região e assinatura para que a implantação tenha sucesso.

1. Selecione **+ Adicionar** para criar um novo Key Vault.
1. Criar um novo grupo de recursos
1. Insira um nome de Key Vault, selecione uma região e selecione um tipo de preço.
1. Selecione **revisão + criar**, verifique suas opções e, em seguida, selecione **criar**.

    ![SSE-Create-a-Key-Vault. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Depois que o cofre de chaves concluir a implantação, selecione-o.
1. Selecione **chaves** em **configurações**.
1. Selecionar **gerar/importar**

    ![SSE-Key-Vault-Generate-Settings. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Deixe o **tipo de chave** definido como **RSA** e o **tamanho da chave RSA** definido como **2080**.
1. Preencha as seleções restantes como desejar e, em seguida, selecione **criar**.

    ![SSE-Create-a-Key-generate. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Configurando o conjunto de criptografia de disco

Para criar e configurar conjuntos de criptografia de disco, você deve usar o seguinte link: https://aka.ms/diskencryptionsets. A criação do conjunto de criptografia de disco ainda não está disponível no portal do Azure global.

1. Abra o [link conjuntos de criptografia de disco](https://aka.ms/diskencryptionsets).
1. Selecione **+ Adicionar**.

    ![SSE-Create-Disk-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Selecione seu grupo de recursos, nomeie o conjunto de criptografia e selecione a mesma região que o cofre de chaves.
1. Selecione o **cofre de chaves e a chave**.
1. Selecione o cofre de chaves e a chave que você criou anteriormente, bem como a versão.
1. Pressione **selecionar**.
1. Selecione **revisar + criar** e **criar**.

    ![SSE-Disk-ENC-Set-Blade-Key. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Abra o conjunto de criptografia de disco depois de concluir a criação e selecione o alerta que aparece.

    ![SSE-Disk-ENC-Alert-Fix. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Duas notificações devem ser exibidas e bem sucedidos. Isso permitirá que você use o conjunto de criptografia de disco com o cofre de chaves.

![Disk-ENC-Notification-Success. png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Implementar uma VM

Agora que você criou e configurou o cofre de chaves e o conjunto de criptografia de disco, você pode implantar uma VM usando a criptografia.
O processo de implantação de VM é semelhante ao processo de implantação padrão, as únicas diferenças são que você precisa implantar a VM na mesma região que seus outros recursos e optar por usar uma chave gerenciada pelo cliente.

1. Pesquise **máquinas virtuais** e selecione **+ Adicionar** para criar uma VM.
1. Na guia **básico** , selecione a mesma região que o conjunto de criptografia de disco e Azure Key Vault.
1. Preencha os outros valores na guia **básico** como desejar.

    ![SSE-Create-a-VM-Region. png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Na guia **discos** , selecione **criptografia em repouso com uma chave gerenciada pelo cliente**.
1. Selecione o conjunto de criptografia de disco na lista suspensa **conjunto de criptografia de disco** .
1. Faça as seleções restantes como desejar.

    ![SSE-Create-VM-Select-CMK-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Habilitar em um disco existente

Para gerenciar e configurar a criptografia de disco em seus discos existentes, você deve usar o seguinte link: https://aka.ms/diskencryptionsets. Habilitar chaves gerenciadas pelo cliente em discos existentes ainda não está disponível no portal do Azure global.

> [!CAUTION]
> Habilitar a criptografia de disco em qualquer disco anexado a uma VM exigirá que você interrompa a VM.

1. Navegue até uma VM que está na mesma região que um de seus conjuntos de criptografia de disco.
1. Abra a VM e selecione **parar**.

    ![sse-stop-VM-to-encrypt-disk. png](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Após a interrupção da VM, selecione **discos** e, em seguida, selecione o disco que você deseja criptografar.

    ![SSE-existing-Disk-SELECT. png](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Selecione **criptografia** e selecione **criptografia em repouso com uma chave gerenciada pelo cliente** e, em seguida, selecione o conjunto de criptografia de disco na lista suspensa.
1. Selecione **Guardar**.

    ![SSE-Encrypt-existing-Disk-Customer-Managed-Key. png](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Repita esse processo para todos os outros discos anexados à VM que você gostaria de criptografar.
1. Quando os discos terminarem de alternar para chaves gerenciadas pelo cliente, se não houver nenhum outro disco anexado que você queira criptografar, você poderá iniciar sua VM.
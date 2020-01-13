---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a799339f2780c2bc372c39120a6e20b34d907326
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912767"
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
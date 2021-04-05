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
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "88815475"
---
A configuração de chaves geridas pelo cliente para os seus discos exigirá que crie recursos numa determinada encomenda, se o fizer pela primeira vez. Primeiro, terás de criar e montar um Cofre de Chaves Azure.

## <a name="set-up-your-azure-key-vault"></a>Configurar o seu Cofre de Chaves Azure

1. Inicie sessão no [Portal do Azure](https://aka.ms/diskencryptionupdates).
1. Procure e selecione **Cofres-chave.**

    [![Screenshot do portal Azure com a caixa de diálogo de pesquisa expandida.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > O cofre da chave Azure, o conjunto de encriptação do disco, o VM, os discos e os instantâneos devem estar todos na mesma região e subscrição para que a implementação tenha sucesso.

1. **Selecione +Adicionar** para criar um novo Cofre de Chaves.
1. Criar um novo grupo de recursos.
1. Insira um nome de cofre chave, selecione uma região e selecione um nível de preços.

    > [!NOTE]
    > Ao criar a instância Key Vault, deve ativar a proteção de eliminação e purga suave. A eliminação suave garante que o Cofre de Chaves detém uma chave eliminada durante um determinado período de retenção (padrão de 90 dias). A proteção da purga garante que uma chave eliminada não pode ser eliminada permanentemente até que o período de retenção caduque. Estas definições protegem-no de perder dados devido à eliminação acidental. Estas definições são obrigatórias quando se utiliza um Cofre-Chave para encriptar discos geridos.

1. Selecione **Rever + Criar,** verificar as suas escolhas e, em seguida, selecione **Criar**.

    ![Screenshot da experiência de criação do Azure Key Vault. Mostrando os valores particulares que cria](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Assim que o cofre terminar de ser acionado, selecione-o.
1. Selecione **Teclas** em **Definições**.
1. **Selecione Gerar/Importar.**

    ![Screenshot do painel de configurações de recursos do Cofre de Chaves. Mostra o botão de gerar/importar dentro das definições.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Deixe o **tipo de chave** definido para O Tamanho da Chave **RSA** e **RSA** definido para **2048**.
1. Preencha as restantes seleções como quiser e, em seguida, **selecione Criar**.

    ![Screenshot da criação de uma lâmina chave que aparece uma vez que o botão gerar/importar é selecionado](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Configurar o seu conjunto de encriptação de disco

1. Procure por **Conjuntos de Encriptação de Discos** e selecione-o.
1. No conjunto de **encriptação** do disco seleciona a lâmina **+Adicionar**.

    ![Screenshot do ecrã principal do portal de encriptação do disco. Realçar o botão Adicionar](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Selecione o seu grupo de recursos, nomeie o seu conjunto de encriptação e selecione a mesma região que o cofre de chaves.
1. Para **o tipo de encriptação** **selecione Encriptação em repouso com uma chave gerida pelo cliente**.

    > [!NOTE]
    > Uma vez criado um conjunto de encriptação de disco com um determinado tipo de encriptação, este não pode ser alterado. Se quiser utilizar um tipo de encriptação diferente, tem de criar um novo conjunto de encriptação de disco.

1. Selecione **Clique para selecionar uma tecla**.
1. Selecione o cofre e a chave chave que criou anteriormente, bem como a versão.
1. **Seleção de imprensa**.
1. Selecione **Rever + Criar** e, em seguida, clique em **Criar**.

    ![Screenshot da lâmina de criação de encriptação do disco. Mostrando a subscrição, grupo de recursos, nome de conjunto de encriptação de disco, região e chave do seletor de chaves + chave.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. Abra o conjunto de encriptação do disco assim que terminar de criar e selecione o alerta que aparece.

    ![Screenshot do alerta popup: "Para associar um disco, imagem ou instantâneo com um conjunto de encriptação de disco, deve conceder permissões ao cofre de chaves". Selecione este alerta para continuar](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    Duas notificações devem aparecer e ter sucesso. Isto permite-lhe utilizar o conjunto de encriptação do disco com o seu cofre de chaves.

    ![Screenshot de permissão bem sucedida e atribuição de função para o seu cofre de chaves.](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)
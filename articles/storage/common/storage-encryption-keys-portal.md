---
title: Utilize o portal Azure para configurar chaves geridas pelo cliente
titleSuffix: Azure Storage
description: Saiba como usar o portal Azure para configurar chaves geridas pelo cliente com o Cofre de Chaves Azure para encriptação de Armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 192e58b101b824ca0cc0c732e02647838be6dc35
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456486"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configure as chaves geridas pelo cliente com o Cofre chave Azure utilizando o portal Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Cofre chave Azure com chaves geridas pelo cliente usando o [portal Azure](https://portal.azure.com/). Para aprender a criar um cofre chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault utilizando o portal Azure](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

A utilização de chaves geridas pelo cliente com encriptação de armazenamento Azure requer que duas propriedades sejam definidas no cofre da chave, **Soft Delete** e **Não Expurgar**. Estas propriedades não são ativadas por padrão, mas podem ser ativadas usando powerShell ou Azure CLI em um cofre de chave novo ou existente.

Para aprender a ativar estas propriedades num cofre chave existente, consulte as secções intituladas **Enableing soft-delete** e **Enableing Purge Protection** num dos seguintes artigos:

- [Como utilizar soft-delete com powerShell](../../key-vault/general/soft-delete-powershell.md).
- Como utilizar o [soft-delete com o CLI](../../key-vault/general/soft-delete-cli.md).

Apenas as teclas RSA e RSA-HSM de 2048 são suportadas com encriptação de Armazenamento Azure. Para mais informações sobre as chaves, consulte **as chaves key vault** em [chaves, segredos e certificados do Cofre chave Azure.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue até à sua conta de armazenamento.
1. Na lâmina **Definições** para a conta de armazenamento, clique em **Encriptação**. Selecione a opção **Chaves Geridas** pelo Cliente, como mostra a seguinte imagem.

    ![Imagem do portal mostrando opção de encriptação](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de ativar as chaves geridas pelo cliente, terá a oportunidade de especificar uma chave para associar a conta de armazenamento.

### <a name="specify-a-key-as-a-uri"></a>Especifique uma chave como URI

Para especificar uma chave como URI, siga estes passos:

1. Para localizar a chave URI no portal Azure, navegue até ao seu cofre chave e selecione a definição de **Keys.** Selecione a tecla desejada e, em seguida, clique na tecla para visualizar as suas versões. Selecione uma versão chave para visualizar as definições para esta versão.
1. Copie o valor do campo **chave identificador,** que fornece o URI.

    ![Screenshot mostrando chave do cofre chave URI](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. Nas definições **de Encriptação** para a sua conta de armazenamento, escolha a **opção Enter key URI.**
1. Colhe o URI que copiou no campo **Key URI.**

   ![Screenshot mostrando como entrar na chave URI](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Especifique a subscrição que contém o cofre da chave.
1. Guarde as alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especifique uma chave de um cofre chave

Para especificar uma chave de um cofre chave, certifique-se primeiro de que tem um cofre chave que contém uma chave. Para especificar uma chave de um cofre chave, siga estes passos:

1. Escolha o Select a partir da opção **Key Vault.**
1. Selecione o cofre da chave que contém a chave que pretende utilizar.
1. Selecione a chave do cofre da chave.

   ![Screenshot mostrando opção chave gerida pelo cliente](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Guarde as alterações.

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, atualize a conta de armazenamento para utilizar a nova versão. Siga estes passos.

1. Navegue na sua conta de armazenamento e exiba as definições **de Encriptação.**
1. Introduza o URI para a nova versão chave. Alternadamente, pode selecionar o cofre da chave e a chave novamente para atualizar a versão.
1. Guarde as alterações.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação de Armazenamento Azure, siga estes passos:

1. Navegue na sua conta de armazenamento e exiba as definições **de Encriptação.**
1. Introduza o URI para a nova chave. Alternadamente, pode selecionar o cofre da chave e escolher uma nova chave.
1. Guarde as alterações.

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as chaves geridas pelo cliente, a sua conta de armazenamento é novamente encriptada com chaves geridas pela Microsoft. Para desativar as chaves geridas pelo cliente, siga estes passos:

1. Navegue na sua conta de armazenamento e exiba as definições **de Encriptação.**
1. Desmarque a caixa de verificação ao lado da definição **da sua própria tecla.**

## <a name="next-steps"></a>Passos seguintes

- [Encriptação azure storage para dados em repouso](storage-service-encryption.md)
- [O que é o Cofre chave Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

---
title: Utilize o portal Azure para configurar chaves geridas pelo cliente
titleSuffix: Azure Storage
description: Aprenda a usar o portal Azure para configurar chaves geridas pelo cliente com cofre de chave Azure para encriptação de armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 30ef840e254355e7db34c626f482d86246e6a738
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84805392"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configure as chaves geridas pelo cliente com o Azure Key Vault utilizando o portal Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Cofre de Chaves Azure com teclas geridas pelo cliente utilizando o [portal Azure](https://portal.azure.com/). Para aprender a criar um cofre-chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault using the Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

A utilização de chaves geridas pelo cliente com encriptação de armazenamento Azure requer que duas propriedades sejam definidas no cofre da chave, **Soft Delete** e Não **Purga .** Estas propriedades não são ativadas por padrão, mas podem ser ativadas usando o PowerShell ou o Azure CLI num cofre de chaves novo ou existente.

Para saber como ativar estas propriedades num cofre de chaves existente, consulte as secções intituladas **Permitir a eliminação suave** e permitir a **proteção contra purgas** num dos seguintes artigos:

- [Como utilizar o soft-delete com PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Como utilizar o soft-delete com CLI](../../key-vault/general/soft-delete-cli.md).

Apenas as teclas RSA e RSA-HSM de 2048 são suportadas com encriptação de Armazenamento Azure. Para obter mais informações sobre as chaves, consulte **as chaves do Cofre chave** em chaves [Azure Key Vault, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue até à sua conta de armazenamento.
1. Na lâmina **Definições** para a conta de armazenamento, clique em **Encriptação**. Selecione a opção **Chaves Geridas pelo Cliente,** como mostra a seguinte imagem.

    ![Screenshot do portal mostrando opção de encriptação](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de ativar as chaves geridas pelo cliente, terá a oportunidade de especificar uma chave para associar à conta de armazenamento.

### <a name="specify-a-key-as-a-uri"></a>Especifique uma chave como um URI

Para especificar uma chave como URI, siga estes passos:

1. Para localizar a chave URI no portal Azure, navegue até ao cofre da chave e selecione a definição **de Chaves.** Selecione a tecla desejada e, em seguida, clique na chave para visualizar as suas versões. Selecione uma versão chave para visualizar as definições para esta versão.
1. Copie o valor do campo **identificador chave,** que fornece o URI.

    ![Screenshot mostrando chave de cofre chave URI](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. Nas definições **de encriptação** para a sua conta de armazenamento, escolha a opção **URI chave introduzir.**
1. Cole o URI que copiou para o campo **Key URI.**

   ![Screenshot mostrando como entrar na chave URI](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Especifique a subscrição que contém o cofre de chaves.
1. Guarde as alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especifique uma chave de um cofre chave

Para especificar uma chave de um cofre de chaves, certifique-se primeiro de que tem um cofre chave que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estes passos:

1. Escolha a **opção Selecionar a partir da** opção Key Vault.
1. Selecione o cofre de chaves que contém a chave que pretende utilizar.
1. Selecione a chave do cofre da chave.

   ![Screenshot mostrando a opção chave gerida pelo cliente](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Guarde as alterações.

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, atualize a conta de armazenamento para utilizar a nova versão. Siga estes passos.

1. Navegue para a sua conta de armazenamento e apresente as definições **de Encriptação.**
1. Introduza o URI para a nova versão chave. Em alternativa, pode selecionar o cofre da chave e a chave novamente para atualizar a versão.
1. Guarde as alterações.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação de armazenamento Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento e apresente as definições **de Encriptação.**
1. Insira o URI para a nova chave. Alternadamente, pode selecionar o cofre da chave e escolher uma nova chave.
1. Guarde as alterações.

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as teclas geridas pelo cliente, a sua conta de armazenamento é novamente encriptada com as teclas geridas pela Microsoft. Para desativar as chaves geridas pelo cliente, siga estes passos:

1. Navegue para a sua conta de armazenamento e apresente as definições **de Encriptação.**
1. Desmarcar a caixa de verificação ao lado da definição **de teclas.**

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [O que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

---
title: Configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure do portal do Azure
description: Saiba como usar o portal do Azure para configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure. As chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 74ca6f15baeeb9fe8adad4bda80e313a4b4cf03a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376232"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure do portal do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o [portal do Azure](https://portal.azure.com/). Para saber como criar um cofre de chaves usando o portal do Azure, consulte [início rápido: definir e recuperar um segredo de Azure Key Vault usando o portal do Azure](../../key-vault/quick-create-portal.md). 

> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com a criptografia de armazenamento do Azure requer que duas propriedades sejam definidas no cofre de chaves, **exclusão reversível** e **não sejam limpas**. Essas propriedades não são habilitadas por padrão. Para habilitar essas propriedades, use o PowerShell ou o CLI do Azure.
> Somente chaves RSA e tamanho de chave 2048 têm suporte.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até à sua conta de armazenamento.
1. Na folha **configurações** da conta de armazenamento, clique em **criptografia**. Selecione a opção **usar sua própria chave** , conforme mostrado na figura a seguir.

    ![Captura de tela do portal mostrando a opção de criptografia](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de habilitar as chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave a ser associada à conta de armazenamento.

### <a name="specify-a-key-as-a-uri"></a>Especifique uma chave como um URI

Para especificar uma chave como um URI, siga estas etapas:

1. Para localizar o URI de chave no portal do Azure, navegue até o cofre de chaves e selecione a configuração **chaves** . Selecione a chave desejada e clique na chave para exibir suas configurações. Copie o valor do campo **identificador de chave** , que fornece o URI.

    ![Captura de tela mostrando o URI da chave do Key Vault](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Nas configurações de **criptografia** para sua conta de armazenamento, escolha a opção **inserir URI de chave** .
1. No campo **URI da chave** , ESPECIFIQUE o URI.

   ![Captura de tela mostrando como inserir o URI da chave](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Para especificar uma chave de um cofre de chaves, primeiro verifique se você tem um cofre de chaves que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **selecionar de Key Vault** .
2. Escolha o cofre de chaves que contém a chave que você deseja usar.
3. Escolha a chave no cofre de chaves.

   ![Captura de tela mostrando a opção de chave gerenciada pelo cliente](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Ao criar uma nova versão de uma chave, você precisará atualizar a conta de armazenamento para usar a nova versão. Siga estes passos.

1. Navegue até sua conta de armazenamento e exiba as configurações de **criptografia** .
1. Especifique o URI para a nova versão de chave. Como alternativa, você pode selecionar o cofre de chaves e a chave novamente para atualizar a versão.

## <a name="next-steps"></a>Passos seguintes

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?

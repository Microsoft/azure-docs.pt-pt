---
title: Utilize o portal Azure para configurar chaves geridas pelo cliente
titleSuffix: Azure Storage
description: Aprenda a usar o portal Azure para configurar chaves geridas pelo cliente com cofre de chave Azure para encriptação de armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/31/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a506f59d3f2d331e4c7680565f3c110b9cd12956
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799179"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurar as chaves geridas pelo cliente com o Azure Key Vault através do portal do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Cofre de Chaves Azure com teclas geridas pelo cliente utilizando o [portal Azure](https://portal.azure.com/). Para aprender a criar um cofre-chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault using the Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

A utilização de chaves geridas pelo cliente com encriptação de armazenamento Azure requer que duas propriedades sejam definidas no cofre da chave, **Soft Delete** e Não **Purga .** Estas propriedades não são ativadas por padrão, mas podem ser ativadas usando o PowerShell ou o Azure CLI num cofre de chaves novo ou existente.

Para saber como ativar estas propriedades num cofre de chaves existente, consulte as secções intituladas **Permitir a eliminação suave** e permitir a **proteção contra purgas** num dos seguintes artigos:

- [Como utilizar o soft-delete com PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Como utilizar o soft-delete com CLI](../../key-vault/general/soft-delete-cli.md).

A encriptação de armazenamento Azure suporta chaves RSA e RSA-HSM dos tamanhos 2048, 3072 e 4096. Para obter mais informações sobre as chaves, consulte **as chaves do Cofre chave** em chaves [Azure Key Vault, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue até à sua conta de armazenamento.
1. Na lâmina **Definições** para a conta de armazenamento, clique em **Encriptação**. Selecione a opção **Chaves Geridas pelo Cliente,** como mostra a seguinte imagem.

    ![Screenshot do portal mostrando opção de encriptação](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de ativar as chaves geridas pelo cliente, terá a oportunidade de especificar uma chave para associar à conta de armazenamento. Também pode indicar se o Azure Storage deve atualizar automaticamente a chave gerida pelo cliente para a versão mais recente ou se irá atualizar manualmente a versão chave.

### <a name="specify-a-key-from-a-key-vault"></a>Especifique uma chave de um cofre chave

Quando seleciona uma chave gerida pelo cliente a partir de um cofre de teclas, a atualização automática da versão chave está ativada. Para gerir manualmente a versão chave, especifique a chave URI e inclua a versão chave. Para obter mais informações, consulte [Especificar uma chave como URI](#specify-a-key-as-a-uri).

Para especificar uma chave de um cofre de chaves, siga estes passos:

1. Escolha a **opção Selecionar a partir da** opção Key Vault.
1. **Selecione um cofre e uma chave de teclas**.
1. Selecione o cofre de chaves que contém a chave que pretende utilizar.
1. Selecione a chave do cofre da chave.

   ![Screenshot mostrando como selecionar cofre e chave chave](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Guarde as alterações.

### <a name="specify-a-key-as-a-uri"></a>Especifique uma chave como um URI

O Azure Storage pode atualizar automaticamente a chave gerida pelo cliente que é usada para encriptação para usar a versão chave mais recente. Quando a tecla gerida pelo cliente for rodada no Cofre da Chave Azure, o Azure Storage começará automaticamente a utilizar a versão mais recente da chave para encriptação.

> [!NOTE]
> Para rodar uma chave, crie uma nova versão da chave no Cofre da Chave Azure. O Azure Storage não lida com a rotação da chave no Cofre da Chave Azure, pelo que terá de rodar a chave manualmente ou criar uma função para a rodar num horário.

Quando especificar a chave URI, omita a versão chave do URI para permitir a atualização automática para a versão chave mais recente. Se incluir a versão chave na chave URI, então a atualização automática não está ativada, e você mesmo deve gerir a versão chave. Para obter mais informações sobre a atualização da versão chave, consulte [atualização manual da versão chave](#manually-update-the-key-version).

Para especificar uma chave como URI, siga estes passos:

1. Para localizar a chave URI no portal Azure, navegue até ao cofre da chave e selecione a definição **de Chaves.** Selecione a tecla desejada e, em seguida, clique na chave para visualizar as suas versões. Selecione uma versão chave para visualizar as definições para esta versão.
1. Copie o valor do campo **identificador chave,** que fornece o URI.

    ![Screenshot mostrando chave de cofre chave URI](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. Nas definições da **chave de encriptação** para a sua conta de armazenamento, escolha a opção **URI da chave entrar.**
1. Cole o URI que copiou para o campo **Key URI.** Omita a versão chave do URI para permitir a atualização automática da versão chave.

   ![Screenshot mostrando como entrar na chave URI](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Especifique a subscrição que contém o cofre de chaves.
1. Guarde as alterações.

Depois de ter especificado a chave, o portal Azure indica se a atualização automática da versão chave está ativada e exibe a versão chave atualmente em uso para encriptação.

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="Screenshot mostrando atualização automática da versão chave ativada":::

## <a name="manually-update-the-key-version"></a>Atualizar manualmente a versão chave

Por padrão, quando cria uma nova versão de uma chave gerida pelo cliente no Key Vault, o Azure Storage utiliza automaticamente a nova versão para encriptação com teclas geridas pelo cliente, conforme descrito nas secções anteriores. Se optar por gerir a versão chave, terá de atualizar a versão chave que está associada à conta de armazenamento sempre que criar uma nova versão da chave.

Para atualizar a conta de armazenamento para utilizar a nova versão chave, siga estes passos:

1. Navegue para a sua conta de armazenamento e apresente as definições **de Encriptação.**
1. Introduza o URI para a nova versão da chave. Em alternativa, pode selecionar o cofre da chave e a chave novamente para atualizar a versão.
1. Guarde as alterações.

## <a name="switch-to-a-different-key"></a>Mude para uma chave diferente

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

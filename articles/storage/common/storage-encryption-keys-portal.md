---
title: Configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure no portal do Azure
description: Saiba como utilizar o portal do Azure para configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure. Chaves geridas pelo cliente permitem-lhe criar, girar, desativar e revogar os controlos de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: baabc5a8e1d063cb51a3edea3a7218591e85aa1a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154158"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure no portal do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves geridas pelo cliente, utilizando o [portal do Azure](https://portal.azure.com/). Para saber como criar um cofre de chaves com o portal do Azure, veja [início rápido: Definir e obter um segredo no Azure Key Vault com o portal do Azure](../../key-vault/quick-create-portal.md). 


> [!IMPORTANT]
> Utilizar chaves geridas pelo cliente com encriptação de armazenamento do Azure requer que o Cofre de chaves têm duas propriedades necessárias configuradas, **eliminação de forma recuperável** e **fazer não remover**. Estas propriedades estão ativadas por predefinição, quando cria um novo cofre de chaves no portal do Azure. No entanto, se precisar de ativar estas propriedades no Cofre de chaves existente, tem de utilizar o PowerShell ou a CLI do Azure.

## <a name="enable-customer-managed-keys"></a>Ativar as chaves geridas pelo cliente

Para ativar as chaves geridas pelo cliente no portal do Azure, siga estes passos:

1. Navegue até à sua conta de armazenamento.
1. Sobre o **definições** painel para a conta de armazenamento, clique em **encriptação**. Selecione o **sua própria chave** opção, conforme mostrado na figura a seguir.

    ![Opção de encriptação do portal de captura de ecrã a mostrar](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Especifique uma chave

Depois de ativar as chaves geridas pelo cliente, terá a oportunidade de especificar uma chave para associar a conta de armazenamento.

### <a name="specify-a-key-as-a-uri"></a>Especifique uma chave como um URI

Para especificar uma chave como um URI, siga estes passos:

1. Para localizar o URI da chave no portal do Azure, navegue para o seu Cofre de chaves e selecione o **chaves** definição. Selecione a chave pretendida e, em seguida, clicar na tecla para ver as respetivas definições. Copie o valor do **identificador de chave** campo, que fornece o URI.

    ![URI da chave captura de ecrã que mostra Cofre de chaves](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Na **encriptação** definições da sua conta de armazenamento, escolha a **URI da chave de Enter** opção.
1. Na **URI da chave** campo, especifique o URI.

   ![Captura de ecrã que mostra como inserir o URI da chave](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Especifique uma chave de um cofre de chaves

Para especificar uma chave de um cofre de chaves, certifique-se de que tem um cofre de chaves que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estes passos:

1. Escolha o **selecione do Key Vault** opção.
2. Escolha o Cofre de chaves que contém a chave que pretende utilizar.
3. Escolha a chave de Cofre de chaves.

   ![Captura de ecrã que mostra a opção de chave gerida pelo cliente](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Atualizar a versão da chave

Quando cria uma nova versão de uma chave, terá de atualizar a conta de armazenamento a utilizar a nova versão. Siga estes passos.

1. Navegue até à sua conta de armazenamento e exibir o **Encryption** definições.
1. Especifique o URI para a nova versão da chave. Em alternativa, pode selecionar o Cofre de chaves e a chave novamente para atualizar a versão.

## <a name="next-steps"></a>Passos Seguintes

- [Encriptação de armazenamento do Azure para dados Inativos](storage-service-encryption.md)
- [O que é o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?

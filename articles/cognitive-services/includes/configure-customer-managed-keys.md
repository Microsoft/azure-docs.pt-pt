---
title: Utilize o portal Azure para configurar chaves geridas pelo cliente
titleSuffix: Cognitive Services
description: Aprenda a usar o portal Azure para configurar chaves geridas pelo cliente com o Azure Key Vault. As teclas geridas pelo cliente permitem criar, rodar, desativar e revogar os controlos de acesso.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 63cfe7968ec88ed75dfe23e8a3d34ac2649f6776
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84307836"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves geridas pelo cliente com o Azure Key Vault

Tem de utilizar o Cofre da Chave Azure para armazenar chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O recurso de Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Azure Key Vault, veja [o que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Quando um novo recurso de Serviços Cognitivos é criado, é sempre encriptado usando teclas geridas pela Microsoft. Não é possível ativar chaves geridas pelo cliente no momento em que o recurso é criado. As chaves geridas pelo cliente são armazenadas no Cofre da Chave Azure, e o cofre-chave deve ser aprovisionado com políticas de acesso que concedem permissões-chave à identidade gerida que está associada ao recurso Serviços Cognitivos. A identidade gerida só está disponível após a criação do recurso utilizando o Nível de Preços necessário para a CMK.

Ativar as chaves geridas pelo cliente também permitirá um sistema de [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)atribuída , uma característica do Azure AD. Uma vez ativada a identidade gerida do sistema, este recurso será registado no Azure Ative Directory. Após a sua inscrição, a identidade gerida terá acesso ao Cofre-Chave selecionado durante a configuração da chave gerida pelo cliente. 

> [!IMPORTANT]
> Se desativar as identidades geridas do sistema, o acesso ao cofre da chave será removido e quaisquer dados encriptados com as teclas do cliente deixarão de estar acessíveis. Quaisquer funcionalidades dependentes destes dados deixarão de funcionar.

> [!IMPORTANT]
> As identidades geridas não suportam atualmente cenários de diretórios cruzados. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o recurso de um diretório AD Azure para outro, a identidade gerida associada ao recurso não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a transferência de uma subscrição entre diretórios AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

A utilização de chaves geridas pelo cliente requer que sejam definidas duas propriedades no cofre da chave, **Soft Delete** e **Não Purpuria**. Estas propriedades não são ativadas por padrão, mas podem ser ativadas usando o PowerShell ou o Azure CLI num cofre de chaves novo ou existente.

> [!IMPORTANT]
> Se não tiver as propriedades **Soft Delete** e **Não Purgar** ativadas e eliminar a sua chave, não poderá recuperar os dados no seu recurso Serviço Cognitivo.

Para saber como ativar estas propriedades num cofre de chaves existente, consulte as secções intituladas **Permitir a eliminação suave** e permitir a **proteção contra purgas** num dos seguintes artigos:

- [Como utilizar o soft-delete com PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Como utilizar o soft-delete com CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Apenas as teclas RSA do tamanho 2048 são suportadas com encriptação de Armazenamento Azure. Para obter mais informações sobre as chaves, consulte **as chaves do Cofre chave** em chaves [Azure Key Vault, segredos e certificados](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys-for-your-resource"></a>Ativar as chaves geridas pelo cliente para o seu recurso

Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue para o seu recurso de Serviços Cognitivos.
1. Na lâmina Definições para o seu recurso **Serviços** Cognitivos, clique em **Encriptação**. Selecione a opção **Chaves Geridas pelo Cliente,** como mostra a seguinte figura.

    ![Screenshot mostrando como selecionar Chaves Geridas pelo Cliente](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de ativar as chaves geridas pelo cliente, terá a oportunidade de especificar uma chave para associar ao recurso Serviços Cognitivos.

### <a name="specify-a-key-as-a-uri"></a>Especifique uma chave como um URI

Para especificar uma chave como URI, siga estes passos:

1. Para localizar a chave URI no portal Azure, navegue até ao cofre da chave e selecione a definição **de Chaves.** Selecione a tecla desejada e, em seguida, clique na chave para visualizar as suas versões. Selecione uma versão chave para visualizar as definições para esta versão.
1. Copie o valor do campo **identificador chave,** que fornece o URI.

    ![Screenshot mostrando chave de cofre chave URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nas definições **de encriptação** para a sua conta de armazenamento, escolha a opção **URI chave introduzir.**
1. Cole o URI que copiou para o campo **Key URI.**

   ![Screenshot mostrando como entrar na chave URI](../media/cognitive-services-encryption/ssecmk2.png)

1. Especifique a subscrição que contém o cofre de chaves.
1. Guarde as alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especifique uma chave de um cofre chave

Para especificar uma chave de um cofre de chaves, certifique-se primeiro de que tem um cofre chave que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estes passos:

1. Escolha a **opção Selecionar a partir da** opção Key Vault.
1. Selecione o cofre de chaves que contém a chave que pretende utilizar.
1. Selecione a chave do cofre da chave.

   ![Screenshot mostrando a opção chave gerida pelo cliente](../media/cognitive-services-encryption/ssecmk3.png)

1. Guarde as alterações.

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, atualize o recurso Serviços Cognitivos para utilizar a nova versão. Siga estes passos:

1. Navegue para o seu recurso de Serviços Cognitivos e exiba as definições **de Encriptação.**
1. Introduza o URI para a nova versão chave. Em alternativa, pode selecionar o cofre da chave e a chave novamente para atualizar a versão.
1. Guarde as alterações.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação, siga estes passos:

1. Navegue para o seu recurso de Serviços Cognitivos e exiba as definições **de Encriptação.**
1. Insira o URI para a nova chave. Alternadamente, pode selecionar o cofre da chave e escolher uma nova chave.
1. Guarde as alterações.

## <a name="rotate-customer-managed-keys"></a>Rode as teclas geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a tecla estiver rodada, deve atualizar o recurso Serviços Cognitivos para utilizar o novo URI da chave. Para saber como atualizar o recurso para utilizar uma nova versão da chave no portal Azure, consulte [a versão chave](#update-the-key-version).

Rodar a tecla não desencadeia a reencriminação de dados no recurso. Não é necessária nenhuma outra ação por parte do utilizador.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). A revogação do acesso bloqueia eficazmente o acesso a todos os dados do recurso Serviços Cognitivos, uma vez que a chave de encriptação é inacessível pelos Serviços Cognitivos.

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as teclas geridas pelo cliente, o seu recurso De Serviços Cognitivos é então encriptado com teclas geridas pela Microsoft. Para desativar as chaves geridas pelo cliente, siga estes passos:

1. Navegue para o seu recurso de Serviços Cognitivos e exiba as definições **de Encriptação.**
1. Desmarcar a caixa de verificação ao lado da definição **de teclas.**
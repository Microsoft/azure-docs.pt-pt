---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372196"
---
### <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

Você deve usar o Cofre chave Azure para armazenar as suas chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O recurso Dos Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Um novo recurso dos Serviços Cognitivos é sempre encriptado utilizando chaves geridas pela Microsoft. Não é possível ativar as chaves geridas pelo cliente no momento em que o recurso é criado. As chaves geridas pelo cliente são armazenadas no Cofre chave Azure, e o cofre chave deve ser aprovisionado com políticas de acesso que concedem permissões-chave para a identidade gerida que está associada ao recurso dos Serviços Cognitivos. A identidade gerida só está disponível após a criação do recurso utilizando o Nível de Preços para A CMK.

Para aprender a usar chaves geridas pelo cliente com o Cofre chave Azure para encriptação de Serviços Cognitivos, consulte:

- [Configure chaves geridas pelo cliente com chave vault para encriptação de serviços cognitivos do portal Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Ativar as chaves geridas pelo cliente também permitirá um sistema de identidade gerida atribuída, uma funcionalidade da Azure AD. Uma vez ativada a identidade gerida do sistema atribuída, este recurso será registado no Azure Ative Directory. Depois de registada, a identidade gerida terá acesso ao Cofre chave selecionado durante a configuração da chave gerida pelo cliente. Pode saber mais sobre [identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Se desativar as identidades geridas atribuídas ao sistema, o acesso ao cofre da chave será removido e quaisquer dados encriptados com as teclas do cliente deixarão de estar acessíveis. Quaisquer funcionalidades dependentes destes dados deixarão de funcionar.

> [!IMPORTANT]
> As identidades geridas não suportam atualmente cenários transversais. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída sob as capas. Se posteriormente deslocar a subscrição, o grupo de recursos ou o recurso de um diretório Azure AD para outro, a identidade gerida associada ao recurso não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a Transferência de uma subscrição entre diretórios da AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves geridas pelo cliente no Cofre de Chaves Azure

Para ativar as chaves geridas pelo cliente, tem de utilizar um Cofre de Chave Azure para armazenar as suas chaves. Deve ativar as propriedades **Soft Delete** e **Não purgar** as propriedades no cofre da chave.

Apenas as chaves RSA do tamanho 2048 são suportadas com encriptação dos Serviços Cognitivos. Para mais informações sobre as chaves, consulte **as chaves key vault** em [chaves, segredos e certificados do Cofre chave Azure.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)

### <a name="rotate-customer-managed-keys"></a>Rode as chaves geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a chave estiver rodada, deve atualizar o recurso dos Serviços Cognitivos para utilizar a nova chave URI. Para aprender a atualizar o recurso para utilizar uma nova versão da chave no portal Azure, consulte a secção intitulada **Atualizar a versão chave** em Chaves geridas pelo cliente para [serviços cognitivos](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)através do portal Azure .

Rodar a tecla não desencadeia a reencriptação de dados no recurso. Não são necessárias mais medidas por parte do utilizador.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize o PowerShell ou o Azure CLI. Para mais informações, consulte [o Cofre de Chaves Azure PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou o Cofre de Chaves [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso bloqueia efetivamente o acesso a todos os dados do recurso Serviços Cognitivos, uma vez que a chave de encriptação é inacessível pelos Serviços Cognitivos.



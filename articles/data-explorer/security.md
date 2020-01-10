---
title: Proteger clusters de Data Explorer do Azure no Azure
description: Saiba mais sobre como proteger clusters no Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: cc16a40b0ea53d433a5a6c592f3b9ea364ef9089
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725835"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Proteger clusters de Data Explorer do Azure no Azure

Este artigo fornece uma introdução à segurança no Azure Data Explorer para ajudá-lo a proteger seus dados e recursos na nuvem e atender às necessidades de segurança de sua empresa. É importante manter seus clusters seguros. A proteção de seus clusters inclui um ou mais recursos do Azure que incluem acesso seguro e armazenamento. Este artigo fornece informações para ajudá-lo a manter seu cluster seguro.

## <a name="managed-identities-for-azure-resources"></a>Identidades geridas para os recursos do Azure

Um desafio comum ao criar aplicativos em nuvem é o gerenciamento de credenciais em seu código para autenticação em serviços de nuvem. Manter essas credenciais protegidas é uma tarefa importante. As credenciais não devem ser armazenadas em estações de trabalho de desenvolvedor ou verificadas no controle do código-fonte. O Azure Key Vault oferece uma forma de armazenar credenciais, segredos e outras chaves em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los.

O recurso identidades gerenciadas do Azure Active Directory (Azure AD) para recursos do Azure resolve esse problema. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar essa identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código. Para obter mais informações sobre esse serviço, consulte a página Visão geral [de identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) .

## <a name="data-encryption"></a>Encriptação de Dados

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) ajuda a proteger e proteger seus dados para atender aos compromissos de segurança e conformidade da organização. Ele fornece criptografia de volume para o sistema operacional e discos de dados das máquinas virtuais do cluster. O Azure Disk Encryption também se integra ao [Azure Key Vault](/azure/key-vault/), que nos permite controlar e gerenciar as chaves e os segredos de criptografia de disco e garantir que todos os dados nos discos de VM sejam criptografados. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com Azure Key Vault

Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para usar na criptografia de dados. Você pode gerenciar a criptografia de seus dados no nível de armazenamento com suas próprias chaves. Uma chave gerenciada pelo cliente é usada para proteger e controlar o acesso à chave de criptografia raiz, que é usada para criptografar e descriptografar todos os dados. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

Use Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar uma API de Azure Key Vault para gerar chaves. O cluster Data Explorer do Azure e o Azure Key Vault devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](/azure/key-vault/key-vault-overview). Para obter uma explicação detalhada sobre chaves gerenciadas pelo cliente, consulte [chaves gerenciadas pelo cliente com Azure Key Vault](/azure/storage/common/storage-service-encryption)

> [!Note]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Para configurar chaves gerenciadas pelo cliente no portal do Azure, você precisa configurar uma identidade gerenciada do **SystemAssigned** para o cluster.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar chaves gerenciadas pelo cliente em um cluster, use um Azure Key Vault para armazenar suas chaves. Você deve habilitar a **exclusão reversível** e **não limpar** as propriedades no cofre de chaves. O cofre de chaves deve estar localizado na mesma assinatura que o cluster. O Azure Data Explorer usa identidades gerenciadas para recursos do Azure para autenticar no cofre de chaves para operações de criptografia e descriptografia. Identidades gerenciadas não dão suporte a cenários entre diretórios.

#### <a name="rotate-customer-managed-keys"></a>Girar chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente em Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar o cluster para usar o novo URI de chave. A rotação da chave não aciona a nova criptografia dos dados no cluster. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou CLI do Azure. Para obter mais informações, consulte [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) ou [Azure Key Vault CLI](/cli/azure/keyvault). Revogar o acesso bloqueia o acesso a todos os dados no nível de armazenamento do cluster, já que a chave de criptografia é, consequentemente, inacessível pelo Azure Data Explorer.

> [!Note]
> Quando o Azure Data Explorer identifica que o acesso a uma chave gerenciada pelo cliente é revogado, ele suspende automaticamente o cluster para excluir todos os dados armazenados em cache. Depois que o acesso à chave é retornado, o cluster precisa ser retomado manualmente.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Usando o [RBAC (controle de acesso baseado em função)](/azure/role-based-access-control/overview), você pode separar as tarefas dentro de sua equipe e conceder apenas o acesso necessário aos usuários do cluster. Em vez de fornecer a todos permissões irrestritas no cluster, você pode permitir apenas determinadas ações. Você pode configurar o [controle de acesso para os bancos de dados](/azure/data-explorer/manage-database-permissions) no [portal do Azure](/azure/role-based-access-control/role-assignments-portal), usando o [CLI do Azure](/azure/role-based-access-control/role-assignments-cli)ou [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Passos seguintes

* [Configurar identidades gerenciadas para o cluster de Data Explorer do Azure](managed-identities.md)
* [Proteja seu cluster no portal de data Explorer do Azure](manage-cluster-security.md) habilitando a criptografia em repouso.
* [Configurar chaves gerenciadas pelo cliente usando o modelo de Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Configurar chaves gerenciadas pelo cliente usando oC#](customer-managed-keys-csharp.md)


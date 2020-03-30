---
title: Clusters Secure Azure Data Explorer em Azure
description: Saiba como proteger clusters no Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77373363"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Clusters Secure Azure Data Explorer em Azure

Este artigo fornece uma introdução à segurança no Azure Data Explorer para ajudá-lo a proteger os seus dados e recursos na nuvem e a atender às necessidades de segurança do seu negócio. É importante manter os seus aglomerados seguros. A fixação dos seus clusters inclui uma ou mais funcionalidades Azure que incluem acesso e armazenamento seguros. Este artigo fornece informações para ajudá-lo a manter o seu cluster seguro.

## <a name="managed-identities-for-azure-resources"></a>Identidades geridas para os recursos do Azure

Um desafio comum ao construir aplicações em nuvem é a gestão de credenciais no seu código para autenticação a serviços na nuvem. Manter essas credenciais protegidas é uma tarefa importante. As credenciais não devem ser armazenadas em postos de trabalho de desenvolvimento ou verificadas no controlo de origem. O Azure Key Vault oferece uma forma de armazenar credenciais, segredos e outras chaves em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los.

O Azure Ative Directory (Azure AD) geriu identidades para recursos Azure que resolve maquetes resolve este problema. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar essa identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código. Para obter mais informações sobre este serviço, consulte [as identidades geridas para](/azure/active-directory/managed-identities-azure-resources/overview) a página geral dos recursos do Azure.

## <a name="data-encryption"></a>Encriptação de dados

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[A Encriptação do Disco Azure](/azure/security/azure-security-disk-encryption-overview) ajuda a proteger e salvaguardar os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. Fornece encriptação de volume para o SISTEMA e discos de dados das máquinas virtuais do seu cluster. A Encriptação azure Disk também se integra com [o Azure Key Vault,](/azure/key-vault/)que nos permite controlar e gerir as chaves e segredos de encriptação do disco, e garantir que todos os dados nos discos VM estão encriptados. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves geridas pelo cliente com cofre de chaves Azure

Por padrão, os dados são encriptados com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação de dados. Pode gerir a encriptação dos seus dados ao nível de armazenamento com as suas próprias chaves. Uma chave gerida pelo cliente é usada para proteger e controlar o acesso à chave de encriptação de raiz, que é usada para encriptar e desencriptar todos os dados. As chaves geridas pelo cliente oferecem uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus dados.

Utilize o Cofre de Chaves Azure para armazenar as chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar um Cofre de Chave Azure para gerar chaves. O cluster Azure Data Explorer e o Azure Key Vault devem estar na mesma região, mas podem estar em diferentes subscrições. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](/azure/key-vault/key-vault-overview) Para obter uma explicação detalhada sobre as chaves geridas pelo cliente, consulte [as chaves geridas pelo Cliente com](/azure/storage/common/storage-service-encryption)o Cofre de Chaves Azure . Configure as chaves geridas pelo cliente no seu cluster Azure Data Explorer utilizando [C#](/azure/data-explorer/customer-managed-keys-csharp) ou o modelo de Gestor de [Recursos Azure](/azure/data-explorer/customer-managed-keys-resource-manager)

> [!Note]
> As chaves geridas pelo cliente baseiam-se em identidades geridas para os recursos Azure, uma característica do Azure Ative Directory (Azure AD). Para configurar as chaves geridas pelo cliente no portal Azure, é necessário configurar uma identidade gerida pelo **SystemAs para** o seu cluster, conforme detalhado em [identidades geridas pela Configuração para o seu cluster Azure Data Explorer](/azure/data-explorer/managed-identities).

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves geridas pelo cliente no Cofre de Chaves Azure

Para ativar as chaves geridas pelo cliente num cluster, utilize um Cofre de Chave Azure para armazenar as suas chaves. Deve ativar as propriedades **Soft Delete** e **Não purgar** as propriedades no cofre da chave. O cofre-chave deve estar localizado na mesma subscrição que o cluster. O Azure Data Explorer utiliza identidades geridas para os recursos do Azure autenticarem o cofre chave para operações de encriptação e desencriptação. Identidades geridas não suportam cenários transversais.

#### <a name="rotate-customer-managed-keys"></a>Rode as chaves geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a tecla estiver rodada, tem de atualizar o cluster para utilizar a nova tecla URI. Rodar a tecla não despoleta a reencriptação de dados no cluster. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize o PowerShell ou o Azure CLI. Para mais informações, consulte [o Cofre de Chaves Azure PowerShell](/powershell/module/az.keyvault/) ou o Cofre de Chaves [Azure CLI](/cli/azure/keyvault). Revogar o acesso bloqueia o acesso a todos os dados no nível de armazenamento do cluster, uma vez que a chave de encriptação é consequentemente inacessível pelo Azure Data Explorer.

> [!Note]
> Quando o Azure Data Explorer identificar que o acesso a uma chave gerida pelo cliente é revogado, suspenderá automaticamente o cluster para eliminar quaisquer dados em cache. Uma vez devolvido o acesso à chave, o cluster tem de ser retomado manualmente.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Utilizando [o controlo de acesso baseado em funções (RBAC),](/azure/role-based-access-control/overview)pode segregar tarefas dentro da sua equipa e conceder apenas o acesso necessário aos utilizadores do cluster. Em vez de dar permissões sem restrições a todos no cluster, só pode permitir certas ações. Pode configurar o controlo de [acesso para as bases](/azure/data-explorer/manage-database-permissions) de dados do portal [Azure,](/azure/role-based-access-control/role-assignments-portal)utilizando o [Azure CLI](/azure/role-based-access-control/role-assignments-cli)ou [o Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Passos seguintes

* [Proteja o seu cluster no Azure Data Explorer - Portal,](manage-cluster-security.md) permitindo a encriptação em repouso.
* [Configure identidades geridas para o seu cluster Azure Data Explorer](managed-identities.md)
* [Configure as chaves geridas pelo cliente utilizando o modelo do Gestor de Recursos Azure](customer-managed-keys-resource-manager.md)
* [Configure as chaves geridas pelo cliente utilizando C #](customer-managed-keys-csharp.md)


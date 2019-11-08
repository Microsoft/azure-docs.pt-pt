---
title: Gerenciar configurações de conta de armazenamento no portal do Azure-armazenamento do Azure | Microsoft Docs
description: Saiba como gerenciar as configurações de conta de armazenamento no portal do Azure, incluindo a configuração de configurações de controle de acesso, regenerando chaves de acesso da conta, alterando a camada de acesso ou modificando o tipo de replicação usado pela conta. Além disso, saiba como excluir uma conta de armazenamento no Portal.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 3c01cc870b20c8256b215eb700548e6cd69ad0d5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749014"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Gerenciar configurações de conta de armazenamento no portal do Azure

Uma variedade de configurações para sua conta de armazenamento está disponível no [portal do Azure](https://portal.azure.com). Este artigo descreve algumas dessas configurações e como usá-las.

## <a name="access-control"></a>Controlo de acesso

O armazenamento do Azure dá suporte à autorização com Azure Active Directory para armazenamento de BLOBs e armazenamento de fila por meio do controle de acesso baseado em função (RBAC). Para obter mais informações sobre autorização com o Azure AD, consulte [autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md).

As configurações de **controle de acesso** na portal do Azure oferecem uma maneira simples de atribuir funções RBAC a usuários, grupos, entidades de serviço e identidades gerenciadas. Para obter mais informações sobre como atribuir funções RBAC, consulte [gerenciar direitos de acesso a BLOB e dados de fila com o RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>Etiquetas

O armazenamento do Azure dá suporte a marcas de Azure Resource Manager para organizar os recursos do Azure com uma taxonomia personalizada. Você pode aplicar marcas às suas contas de armazenamento para poder agrupá-las em sua assinatura de uma maneira lógica.

Para contas de armazenamento, um nome de marca é limitado a 128 caracteres e um valor de marca é limitado a 256 caracteres.

Para obter mais informações, consulte [usar marcas para organizar os recursos do Azure](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Chaves de acesso

Quando você cria uma conta de armazenamento, o Azure gera chaves de acesso da conta de armazenamento de 2 512 bits. Essas chaves podem ser usadas para autorizar o acesso à sua conta de armazenamento por meio de uma chave compartilhada. Você pode girar e regenerar as chaves sem interrupções para seus aplicativos e a Microsoft recomenda que você faça isso regularmente.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Exibir chaves de conta e cadeia de conexão

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

A Microsoft recomenda que você gere novamente suas chaves de acesso periodicamente para ajudar a manter sua conta de armazenamento segura. Duas chaves de acesso são atribuídas para que você possa girar suas chaves. Ao girar suas chaves, você garante que seu aplicativo Mantenha o acesso ao armazenamento do Azure durante todo o processo. 

> [!WARNING]
> A regeneração de suas chaves de acesso pode afetar qualquer aplicativo ou serviço do Azure que seja dependente da chave da conta de armazenamento. Todos os clientes que usam a chave de conta para acessar a conta de armazenamento devem ser atualizados para usar a nova chave, incluindo serviços de mídia, nuvem, aplicativos móveis e de desktop e aplicativos de interface gráfica do usuário para armazenamento do Azure, como [Gerenciador de armazenamento do Azure ](https://azure.microsoft.com/features/storage-explorer/).

Siga este processo para girar suas chaves de conta de armazenamento:

1. Atualize as cadeias de conexão no código do aplicativo para usar a chave secundária.
2. Volte a gerar a chave de acesso primária para a sua conta do Storage. Na folha **chaves de acesso** na portal do Azure, clique em **regenerar key1**e, em seguida, clique em **Sim** para confirmar que deseja gerar uma nova chave.
3. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.
4. Volte a gerar a chave de acesso secundária da mesma forma.

## <a name="account-configuration"></a>Configuração da conta

Depois de criar uma conta de armazenamento, você pode modificar sua configuração. Por exemplo, você pode alterar a forma como os dados são replicados ou alterar a camada de acesso da conta de quente para fria. Na [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento e, em seguida, localize e clique em **configuração** em **configurações** para exibir e/ou alterar a configuração da conta.

Alterar a configuração da conta de armazenamento pode resultar em custos adicionais. Para obter mais detalhes, consulte a página de [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

Para remover uma conta de armazenamento que já não está a utilizar, navegue até à mesma no [Portal do Azure](https://portal.azure.com) e clique em **Eliminar**. A eliminação de uma conta do Storage elimina toda a conta, incluindo todos os dados na mesma.

> [!WARNING]
> Não é possível restaurar uma conta do Storage eliminada ou obter os conteúdos que esta continha antes da eliminação. Certifique-se de que faz uma cópia de segurança de tudo o que pretende guardar antes de eliminar a conta. Isto também se aplica a quaisquer recursos na conta – depois de eliminar um blob, tabela, fila ou ficheiro, este é eliminado permanentemente.
> 

Se tentar eliminar uma conta de armazenamento associada a uma máquina virtual do Azure, poderá receber um erro informando-o de que a conta de armazenamento ainda está em utilização. Para ajudar a resolver este erro, veja [Resolver erros ao eliminar as contas de armazenamento](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da conta de armazenamento do Azure](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-quickstart-create-account.md)

---
title: Gerir definições de conta de armazenamento no portal do Azure - armazenamento do Azure | Documentos da Microsoft
description: Saiba como gerir as definições de conta de armazenamento no portal do Azure, incluindo a configuração de acesso definições de controlo, regenerar chaves de acesso de conta, a alteração da camada de acesso ou modificar o tipo de replicação utilizado pela conta do. Além disso, saiba como eliminar uma conta de armazenamento no portal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 66bdc4bd1e17347419a6eccd7c9532db17b33001
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303484"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Gerir definições de conta de armazenamento no portal do Azure

Está disponível numa variedade de definições para a sua conta de armazenamento a [portal do Azure](https://portal.azure.com). Este artigo descreve algumas destas definições e como utilizá-los.

## <a name="access-control"></a>Controlo de acesso

O armazenamento do Azure suporta a autorização com o Azure Active Directory para o armazenamento de BLOBs e armazenamento de filas através do controlo de acesso baseado em funções (RBAC). Para obter mais informações sobre a autorização com o Azure AD, consulte [autorizar o acesso ao Azure os blobs e filas com o Azure Active Directory](storage-auth-aad.md).

O **controlo de acesso** definições no portal do Azure oferecem uma forma simples para atribuir funções RBAC a utilizadores, grupos, os principais de serviço e identidades geridas. Para obter mais informações sobre a atribuição de funções RBAC, veja [gerir direitos de acesso a dados de BLOBs e filas com RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>Tags

O armazenamento do Azure suporta etiquetas do Azure Resource Manager para organizar recursos do Azure com uma taxonomia personalizado. Pode aplicar etiquetas a suas contas de armazenamento, de modo a que pode agrupá-los na sua subscrição de maneira lógica.

Para contas de armazenamento, um nome de etiqueta está limitado a 128 carateres e um valor de etiqueta está limitado a 256 carateres.

Para obter mais informações, consulte [utilizar etiquetas para organizar os recursos do Azure](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Chaves de acesso

Quando cria uma conta de armazenamento, o Azure gera duas chaves de acesso de conta de armazenamento de 512 bits. Essas chaves podem ser utilizadas para autorizar o acesso à sua conta de armazenamento por meio da chave partilhada. Pode girar e regenerar as chaves sem interrupção para seus aplicativos e a Microsoft recomenda que fizer isso regularmente.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Ver as chaves de conta e a cadeia de ligação

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Voltar a gerar chaves de acesso

A Microsoft recomenda que regenerar as chaves de acesso periodicamente para ajudar a manter segura a sua conta de armazenamento. Duas chaves de acesso são atribuídas para que pode girar as suas chaves. Ao rodar as chaves, garante que seu aplicativo mantém o acesso ao armazenamento do Azure em todo o processo. 

> [!WARNING]
> A regenerar as chaves de acesso pode afetar quaisquer aplicações ou serviços do Azure que são dependentes na chave de conta de armazenamento. Todos os clientes que utilizam a chave de conta para aceder à conta de armazenamento tem de ser atualizados para utilizar a nova chave, incluindo serviços de multimédia na cloud, ambiente de trabalho e aplicativos móveis e aplicativos de interface gráfica do usuário para o armazenamento do Azure, tais como [Azure Explorador de armazenamento](https://azure.microsoft.com/features/storage-explorer/).

Siga este processo para rodar as chaves de conta de armazenamento:

1. Atualize as cadeias de ligação no código da aplicação para utilizar a chave secundária.
2. Volte a gerar a chave de acesso primária para a sua conta do Storage. Sobre o **chaves de acesso** painel no portal do Azure, clique em **voltar a gerar chave1**e, em seguida, clique em **Sim** para confirmar que pretende gerar uma nova chave.
3. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.
4. Volte a gerar a chave de acesso secundária da mesma forma.

## <a name="account-configuration"></a>Configuração da conta

Depois de criar uma conta de armazenamento, pode modificar a respetiva configuração. Por exemplo, pode alterar como os dados são replicados ou alterar a camada de acesso da conta de acesso frequente para esporádico. Na [portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento, em seguida, localize e clique em **configuração** sob **definições** para ver e/ou alterar a configuração de conta.

Alterar a configuração de conta de armazenamento pode resultar em custos adicionais. Para obter mais detalhes, consulte a [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) página.

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

Para remover uma conta de armazenamento que já não está a utilizar, navegue até à mesma no [Portal do Azure](https://portal.azure.com) e clique em **Eliminar**. A eliminação de uma conta do Storage elimina toda a conta, incluindo todos os dados na mesma.

> [!WARNING]
> Não é possível restaurar uma conta do Storage eliminada ou obter os conteúdos que esta continha antes da eliminação. Certifique-se de que faz uma cópia de segurança de tudo o que pretende guardar antes de eliminar a conta. Isto também se aplica a quaisquer recursos na conta – depois de eliminar um blob, tabela, fila ou ficheiro, este é eliminado permanentemente.
> 

Se tentar eliminar uma conta de armazenamento associada a uma máquina virtual do Azure, poderá receber um erro informando-o de que a conta de armazenamento ainda está em utilização. Para ajudar a resolver este erro, veja [Resolver erros ao eliminar as contas de armazenamento](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral da conta de armazenamento do Azure](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-quickstart-create-account.md)

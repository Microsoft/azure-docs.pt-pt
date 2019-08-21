---
title: Autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory | Microsoft Docs
description: Autorize o acesso a BLOBs e filas do Azure usando Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/02/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2607e09e6b8428c2aacc8ee7c6be42f54284a1ce
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641097"
---
# <a name="authorize-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory

O armazenamento do Azure dá suporte ao uso de Azure Active Directory (AD) para autorizar solicitações de armazenamento de BLOBs e filas. Com o Azure AD, você pode usar o RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança, que pode ser um usuário, grupo ou entidade de serviço de aplicativo. A entidade de segurança é autenticada pelo AD do Azure para retornar um token 2,0 do OAuth. O token pode ser usado para autorizar uma solicitação para acessar um recurso no armazenamento de BLOBs ou de filas.

A autorização de usuários ou aplicativos que usam um token 2,0 do OAuth retornado pelo AD do Azure fornece segurança superior e facilidade de uso sobre autorização de chave compartilhada e SAS (assinaturas de acesso compartilhado). Com o Azure AD, não é necessário armazenar a chave de acesso da conta com seu código e arriscar as vulnerabilidades de segurança potenciais. Embora você possa continuar a usar a autorização de chave compartilhada com seus aplicativos, usar o Azure AD evita a necessidade de armazenar a chave de acesso da conta com seu código. Você também pode continuar a usar SAS (assinaturas de acesso compartilhado) para conceder acesso refinado aos recursos em sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar com a revogação de uma SAS comprometida. A Microsoft recomenda usar a autorização do Azure AD com seus aplicativos de armazenamento do Azure quando possível.

A autorização com o Azure AD está disponível para todas as contas de armazenamento de BLOBs e de uso geral em todas as regiões públicas e nuvens nacionais. Somente contas de armazenamento criadas com o modelo de implantação Azure Resource Manager dão suporte à autorização do Azure AD. Não há suporte para a autorização com o Azure AD no armazenamento de tabelas do Azure.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Visão geral do Azure AD para BLOBs e filas

Quando uma entidade de segurança (um usuário, grupo ou aplicativo) tenta acessar um recurso de BLOB ou fila, a solicitação deve ser autorizada, a menos que seja um blob disponível para acesso anônimo. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. Primeiro, a identidade da entidade de segurança é autenticada e um token OAuth 2,0 é retornado. Em seguida, o token é passado como parte de uma solicitação para o BLOB ou serviço Fila e usado pelo serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um aplicativo solicite um token de acesso OAuth 2,0 em tempo de execução. Se um aplicativo estiver sendo executado de dentro de uma entidade do Azure, como uma VM do Azure, um conjunto de dimensionamento de máquinas virtuais ou um aplicativo Azure Functions, ele poderá usar uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para acessar BLOBs ou filas. Para saber como autorizar solicitações feitas por uma identidade gerenciada para o blob do Azure ou serviço Fila, consulte autorizar o [acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md).

A etapa de autorização requer que uma ou mais funções RBAC sejam atribuídas à entidade de segurança. O armazenamento do Azure fornece funções RBAC que abrangem conjuntos comuns de permissões para dados de BLOB e fila. As funções atribuídas a uma entidade de segurança determinam as permissões que o principal terá. Para saber mais sobre como atribuir funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).

Aplicativos nativos e aplicativos Web que fazem solicitações para o blob do Azure ou serviço Fila também podem autorizar o acesso com o Azure AD. Para saber como solicitar um token de acesso e usá-lo para autorizar solicitações de BLOB ou dados de fila, consulte autorizar o [acesso ao armazenamento do Azure com o Azure AD de um aplicativo de armazenamento do Azure](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Atribuindo funções RBAC para direitos de acesso

O Azure Active Directory (AD do Azure) autoriza os direitos de acesso a recursos protegidos por meio [do RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções RBAC internas que abrangem conjuntos comuns de permissões usadas para acessar dados de BLOB e de fila. Você também pode definir funções personalizadas para acesso a dados de BLOB e de fila.

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível da assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Funções RBAC internas para BLOBs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para saber como atribuir uma função de RBAC interna a uma entidade de segurança, consulte um dos seguintes artigos:

- [Grant access to Azure blob and queue data with RBAC in the Azure portal](storage-auth-aad-rbac-portal.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC no portal do Azure)
- [Grant access to Azure blob and queue data with RBAC using Azure CLI](storage-auth-aad-rbac-cli.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através da CLI do Azure)
- [Grant access to Azure blob and queue data with RBAC using PowerShell](storage-auth-aad-rbac-powershell.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através do PowerShell)

Para obter mais informações sobre como as funções internas são definidas para o armazenamento do Azure, consulte [entender as definições de função](../../role-based-access-control/role-definitions.md#management-and-data-operations). Para obter informações sobre como criar funções RBAC personalizadas, consulte [criar funções personalizadas para o controle de acesso baseado em função do Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Permissões de acesso para operações de dados

Para obter detalhes sobre as permissões necessárias para chamar operações de BLOB ou serviço Fila específicas, consulte [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Escopo de recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Acessar dados com uma conta do Azure AD

O acesso a dados de BLOB ou de fila por meio do portal do Azure, PowerShell ou CLI do Azure pode ser autorizado usando a conta do Azure AD do usuário ou usando as chaves de acesso da conta (autorização de chave compartilhada).

### <a name="data-access-from-the-azure-portal"></a>Acesso a dados do portal do Azure

O portal do Azure pode usar sua conta do Azure AD ou as chaves de acesso da conta para acessar dados de BLOB e de fila em uma conta de armazenamento do Azure. O esquema de autorização que o portal do Azure usa depende das funções RBAC atribuídas a você.

Quando você tenta acessar dados de BLOB ou de fila, a portal do Azure primeiro verifica se você recebeu uma função de RBAC com **Microsoft. Storage/storageAccounts/listkeys/Action**. Se você tiver recebido uma função com essa ação, o portal do Azure usará a chave de conta para acessar dados de BLOB e de fila por meio da autorização de chave compartilhada. Se você não tiver recebido uma função com essa ação, a portal do Azure tentará acessar os dados usando sua conta do Azure AD.

Para acessar dados de BLOB ou de fila do portal do Azure usando sua conta do Azure AD, você precisa de permissões para acessar dados de BLOB e de fila e também precisa de permissões para navegar pelos recursos da conta de armazenamento no portal do Azure. As funções internas fornecidas pelo armazenamento do Azure concedem acesso aos recursos de BLOB e fila, mas não concedem permissões aos recursos da conta de armazenamento. Por esse motivo, o acesso ao portal também requer a atribuição de uma função de Azure Resource Manager, como a função [leitor](../../role-based-access-control/built-in-roles.md#reader) , no escopo do nível da conta de armazenamento ou superior. A função **leitor** concede as permissões mais restritas, mas outra função de Azure Resource Manager que concede acesso aos recursos de gerenciamento da conta de armazenamento também é aceitável. Para saber mais sobre como atribuir permissões a usuários para acesso a dados no portal do Azure com uma conta do Azure AD, consulte [conceder acesso ao blob do Azure e dados de fila com RBAC no portal do Azure](storage-auth-aad-rbac-portal.md).

O portal do Azure indica qual esquema de autorização está em uso quando você navega para um contêiner ou uma fila. Para obter mais informações sobre o acesso a dados no portal, consulte [usar o portal do Azure para acessar dados de BLOB ou fila](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Acesso a dados do PowerShell ou CLI do Azure

O CLI do Azure e o PowerShell dão suporte à entrada com as credenciais do Azure AD. Depois de entrar, sua sessão é executada sob essas credenciais. Para saber mais, confira [executar comandos do CLI do Azure ou do PowerShell com as credenciais do Azure ad para acessar dados de BLOB ou fila](storage-auth-aad-script.md).

## <a name="azure-ad-authorization-over-smb-for-azure-files"></a>Autorização do Azure AD sobre SMB para arquivos do Azure

Os arquivos do Azure dão suporte à autorização com o Azure AD sobre SMB somente para VMs ingressadas no domínio (versão prévia). Para saber mais sobre como usar o Azure AD sobre SMB para arquivos do Azure, consulte [visão geral da autorização de Azure Active Directory sobre SMB para arquivos do Azure (versão prévia)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Passos seguintes

- [Autorizar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md)
- [Autorizar com Azure Active Directory de um aplicativo para acesso a BLOBs e filas](storage-auth-aad-app.md)
- [Suporte do armazenamento do Azure para Azure Active Directory controle de acesso baseado em disponibilidade geral](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

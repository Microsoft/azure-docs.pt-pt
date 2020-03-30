---
title: Autorizar o acesso a blobs e filas usando o Diretório Ativo
titleSuffix: Azure Storage
description: Autorize o acesso a blobs e filas Azure utilizando o Diretório Ativo Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255369"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autorizar acesso a blobs e filas utilizando o Diretório Ativo Azure

O Azure Storage suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos de armazenamento blob e fila. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um diretor de segurança, que pode ser um utilizador, grupo ou diretor de serviço de aplicação. O diretor de segurança é autenticado pela Azure AD para devolver um token OAuth 2.0. O símbolo pode então ser usado para autorizar um pedido contra o armazenamento blob ou fila.

Autorizar pedidos contra o Armazenamento Azure com a AD Azure proporciona uma segurança e facilidade de utilização superiores em relação à autorização da Chave Partilhada. A Microsoft recomenda a utilização da autorização da AD Azure com as suas aplicações blob e fila, quando possível, para minimizar potenciais vulnerabilidades de segurança inerentes à Chave Partilhada.

A autorização com a Azure AD está disponível para todas as contas de armazenamento de fins gerais e blob em todas as regiões públicas e nuvens nacionais. Apenas contas de armazenamento criadas com o modelo de implementação do Gestor de Recursos Azure suportam a autorização da Azure AD.

O armazenamento blob também suporta a criação de assinaturas de acesso partilhado (SAS) que são assinadas com credenciais Azure AD. Para obter mais informações, consulte [Grant acesso limitado a dados com assinaturas de acesso partilhado.](storage-sas-overview.md)

O Azure Files suporta a autorização com AD (pré-visualização) ou Azure AD DS (GA) sobre SMB apenas para VMs de domínio. Para saber sobre a utilização de AD (pré-visualização) ou Azure AD DS (GA) sobre SMB para Ficheiros Azure, consulte o suporte de [autenticação baseado na identidade do Azure Files para acesso](../files/storage-files-active-directory-overview.md)a SMB .

A autorização com a Azure AD não é suportada para armazenamento de mesa Azure. Utilize a Chave Partilhada para autorizar pedidos de armazenamento de mesa.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Visão geral da AD Azure para bolhas e filas

Quando um diretor de segurança (utilizador, grupo ou aplicação) tenta aceder a um recurso blob ou fila, o pedido deve ser autorizado, a menos que se seja uma bolha disponível para acesso anónimo. Com a AD Azure, o acesso a um recurso é um processo em duas etapas. Primeiro, a identidade do diretor de segurança é autenticada e um símbolo OAuth 2.0 é devolvido. Em seguida, o símbolo é passado como parte de um pedido ao serviço Blob ou Fila e utilizado pelo serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um pedido de pedido de pedido de pedido de acesso OAuth 2.0 seja aceso a tempo de execução. Se uma aplicação estiver a funcionar dentro de uma entidade Azure, como um Azure VM, um conjunto de escala de máquina virtual ou uma aplicação De Funções Azure, pode usar uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para aceder a bolhas ou filas. Para saber autorizar pedidos feitos por uma identidade gerida para o serviço Azure Blob ou Queue, consulte [Autorizar o acesso a blobs e filas com o Azure Ative Directory e identidades geridas para a Azure Resources.](storage-auth-aad-msi.md)

O passo de autorização requer que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. O Azure Storage fornece funções RBAC que englobam conjuntos comuns de permissões para dados de blob e fila. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para saber mais sobre a atribuição de funções RBAC para armazenamento Azure, consulte Gerir os direitos de acesso aos dados de [armazenamento com rBAC](storage-auth-aad-rbac.md).

Aplicações nativas e aplicações web que fazem pedidos ao serviço Azure Blob ou Fila também podem autorizar o acesso com a Azure AD. Para saber como solicitar um token de acesso e usá-lo para autorizar pedidos de dados blob ou fila, consulte [Autorizar o acesso ao Armazenamento Azure com a AD Azure a partir de uma aplicação](storage-auth-aad-app.md)de Armazenamento Azure .

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções rBAC para direitos de acesso

O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos garantidos através do [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). O Azure Storage define um conjunto de funções RBAC incorporadas que englobam conjuntos comuns de permissões usadas para aceder a dados blob e fila. Também pode definir papéis personalizados para acesso a dados de blob e fila.

Quando uma função RBAC é atribuída a um diretor de segurança da AD Azure, o Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser consultado ao nível da subscrição, do grupo de recursos, da conta de armazenamento ou de um recipiente ou fila individual. Um diretor de segurança da AD Azure pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma [identidade gerida para os recursos Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Funções RBAC incorporadas para bolhas e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para aprender a atribuir um papel RBAC incorporado a um diretor de segurança, consulte um dos seguintes artigos:

- [Grant access to Azure blob and queue data with RBAC in the Azure portal](storage-auth-aad-rbac-portal.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC no portal do Azure)
- [Grant access to Azure blob and queue data with RBAC using Azure CLI](storage-auth-aad-rbac-cli.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através da CLI do Azure)
- [Grant access to Azure blob and queue data with RBAC using PowerShell](storage-auth-aad-rbac-powershell.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através do PowerShell)

Para obter mais informações sobre como as funções incorporadas são definidas para o Armazenamento Azure, consulte [compreender as definições](../../role-based-access-control/role-definitions.md#management-and-data-operations)de papéis . Para obter informações sobre a criação de funções RBAC personalizadas, consulte [Criar papéis personalizados para o Controlo de Acesso baseado em papel Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Permissões de acesso para operações de dados

Para obter informações sobre as permissões necessárias para ligar para operações específicas de serviço blob ou fila, consulte [Permissões para ligar](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)para operações de dados blob e fila .

## <a name="resource-scope"></a>Âmbito de recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Dados de acesso com uma conta Azure AD

O acesso a dados blob ou de fila através do portal Azure, PowerShell ou Azure CLI pode ser autorizado através da conta AD Azure do utilizador ou utilizando as chaves de acesso à conta (autorização de chave partilhada).

### <a name="data-access-from-the-azure-portal"></a>Acesso de dados a partir do portal Azure

O portal Azure pode utilizar a sua conta Azure AD ou as chaves de acesso à conta para aceder a dados blob e fila numa conta de armazenamento Azure. O esquema de autorização que o portal Azure utiliza depende das funções RBAC que lhe são atribuídas.

Quando tenta aceder a dados blob ou de fila, o portal Azure verifica primeiro se lhe foi atribuída uma função RBAC com **a Microsoft.StorageAccounts/listkeys/action**. Se lhe foi atribuída uma função com esta ação, então o portal Azure utiliza a chave da conta para aceder a dados blob e de fila através da autorização Shared Key. Se não lhe foi atribuído um papel com esta ação, então o portal Azure tenta aceder a dados através da sua conta AD Azure.

Para aceder a dados blob ou de fila do portal Azure utilizando a sua conta Azure AD, precisa de permissões para aceder a dados blob e fila, e também precisa de permissões para navegar através dos recursos da conta de armazenamento no portal Azure. As funções incorporadas fornecidas pelo Azure Storage concedem acesso a recursos blob e fila, mas não concedem permissões aos recursos da conta de armazenamento. Por esta razão, o acesso ao portal requer também a atribuição de uma função de Gestor de Recursos Azure, como a função [Reader,](../../role-based-access-control/built-in-roles.md#reader) ao nível da conta de armazenamento ou superior. O papel **do Leitor** concede as permissões mais restritas, mas outra função do Gestor de Recursos Azure que concede acesso aos recursos de gestão de contas de armazenamento também é aceitável. Para saber mais sobre como atribuir permissões aos utilizadores para acesso a dados no portal Azure com uma conta Azure AD, consulte o [Acesso ao Acesso ao Azure blob e aos dados de fila com rBAC no portal Azure.](storage-auth-aad-rbac-portal.md)

O portal Azure indica que esquema de autorização está a ser utilizado quando navega para um contentor ou fila. Para obter mais informações sobre o acesso de dados no portal, consulte [Utilize o portal Azure para aceder a dados de blob ou fila.](storage-access-blobs-queues-portal.md)

### <a name="data-access-from-powershell-or-azure-cli"></a>Acesso a dados da PowerShell ou Do ClI Azure

Suporte Azure CLI e PowerShell assinando com credenciais Azure AD. Depois de assinar, a sua sessão passa por baixo dessas credenciais. Para saber mais, consulte [comandos Run Azure CLI ou PowerShell com credenciais Azure AD para aceder a dados blob ou fila](authorize-active-directory-powershell.md).

## <a name="next-steps"></a>Passos seguintes

- [Autorizar acesso a blobs e filas com diretório ativo azure e identidades geridas para os Recursos Azure](storage-auth-aad-msi.md)
- [Autorizar com o Azure Ative Directory a partir de um pedido de acesso a blobs e filas](storage-auth-aad-app.md)
- [Suporte de armazenamento Azure para controlo de acesso baseado em Diretório Ativo Azure geralmente disponível](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

---
title: Autorizar o acesso a bolhas e filas utilizando o Ative Directory
titleSuffix: Azure Storage
description: Autorizar o acesso a blobs e filas Azure utilizando o Azure Ative Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e4e64a022a8cb071eb1b3447995c1e91b910fe2d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519206"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autorizar o acesso a bolhas e filas utilizando o Azure Ative Directory

O Azure Storage suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos para o armazenamento de Blob e Queue. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um principal de segurança, que pode ser um utilizador, grupo ou diretor de serviço de aplicação. O principal de segurança é autenticado pela Azure AD para devolver um token OAuth 2.0. O símbolo pode então ser usado para autorizar um pedido contra o armazenamento de Blob ou Queue.

Autorizar pedidos contra o Azure Storage com Azure AD proporciona uma segurança superior e facilidade de utilização sobre a autorização da Chave Partilhada. A Microsoft recomenda a utilização da autorização AD AZure com as suas aplicações blob e queue, quando possível, para minimizar potenciais vulnerabilidades de segurança inerentes à Chave Partilhada.

A autorização com Azure AD está disponível para todas as contas de armazenamento de fins gerais e blob em todas as regiões públicas e nuvens nacionais. Apenas as contas de armazenamento criadas com o modelo de implementação do Azure Resource Manager suportam a autorização Azure AD.

O armazenamento blob suporta ainda a criação de assinaturas de acesso partilhado (SAS) que são assinadas com credenciais AD AZure. Para obter mais informações, consulte [Grant acesso limitado a dados com assinaturas de acesso partilhado.](storage-sas-overview.md)

A Azure Files suporta a autorização com AD (pré-visualização) ou Azure AD DS (GA) sobre SMB apenas para VMs ligados ao domínio. Para saber mais sobre a utilização de AD (pré-visualização) ou Azure AD DS (GA) sobre SMB para Ficheiros Azure, consulte [o suporte de autenticação baseado em identidade dos Ficheiros Azure para acesso SMB](../files/storage-files-active-directory-overview.md).

A autorização com Azure AD não é suportada para o armazenamento da tabela Azure. Utilize a Chave Partilhada para autorizar pedidos de armazenamento de mesa.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Visão geral do AD Azure para bolhas e filas

Quando um principal de segurança (um utilizador, grupo ou aplicação) tenta aceder a um recurso de bolha ou fila, o pedido deve ser autorizado, a menos que seja uma bolha disponível para acesso anónimo. Com o Azure AD, o acesso a um recurso é um processo em duas etapas. Primeiro, a identidade do diretor de segurança é autenticada e um token OAuth 2.0 é devolvido. Em seguida, o token é passado como parte de um pedido para o serviço Blob ou Queue e usado pelo serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um pedido de pedido de acesso OAuth 2.0 seja token no tempo de execução. Se uma aplicação estiver a funcionar a partir de uma entidade Azure, como um Azure VM, um conjunto de escala de máquina virtual ou uma aplicação Azure Functions, pode usar uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para aceder a bolhas ou filas. Para saber como autorizar pedidos feitos por identidade gerida ao serviço Azure Blob ou Queue, consulte [o Acesso autorizado a bolhas e filas com o Azure Ative Directory e identidades geridas para a Azure Resources](storage-auth-aad-msi.md).

O passo de autorização requer que uma ou mais funções do RBAC sejam atribuídas ao diretor de segurança. O Azure Storage fornece funções RBAC que englobam conjuntos comuns de permissões para dados de bolhas e filas. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para saber mais sobre a atribuição de funções de RBAC para armazenamento Azure, consulte [Gerir os direitos de acesso aos dados de armazenamento com o RBAC.](storage-auth-aad-rbac.md)

As aplicações nativas e aplicações web que fazem pedidos para o serviço Azure Blob ou Queue também podem autorizar o acesso com a Azure AD. Para obter como solicitar um token de acesso e usá-lo para autorizar pedidos de dados de bolhas ou filas, consulte [Acesso autorizado ao Azure Storage com Azure AD a partir de uma aplicação de Armazenamento Azure](storage-auth-aad-app.md).

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções do RBAC para direitos de acesso

O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através [do controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). O Azure Storage define um conjunto de funções RBAC incorporadas que englobam conjuntos comuns de permissões usadas para aceder a dados de bolhas e filas. Também pode definir funções personalizadas para acesso a dados de blob e fila.

Quando um papel rbac é atribuído a um diretor de segurança Azure, Azure concede acesso a esses recursos para esse chefe de segurança. O acesso pode ser alargado ao nível da subscrição, do grupo de recursos, da conta de armazenamento ou de um contentor ou fila individual. Um diretor de segurança Azure AD pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma [identidade gerida para os recursos da Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Papéis RBAC incorporados para bolhas e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para aprender a atribuir um papel RBAC incorporado a um diretor de segurança, consulte um dos seguintes artigos:

- [Grant access to Azure blob and queue data with RBAC in the Azure portal](storage-auth-aad-rbac-portal.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC no portal do Azure)
- [Grant access to Azure blob and queue data with RBAC using Azure CLI](storage-auth-aad-rbac-cli.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através da CLI do Azure)
- [Grant access to Azure blob and queue data with RBAC using PowerShell](storage-auth-aad-rbac-powershell.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através do PowerShell)

Para obter mais informações sobre como as funções incorporadas são definidas para o Armazenamento Azure, consulte [definições de funções de entender](../../role-based-access-control/role-definitions.md#management-and-data-operations). Para obter informações sobre a criação de funções RBAC personalizadas, consulte [criar funções personalizadas para o Controlo de Acesso Baseado em Funções Azure.](../../role-based-access-control/custom-roles.md)

### <a name="access-permissions-for-data-operations"></a>Permissões de acesso para operações de dados

Para obter mais informações sobre as permissões necessárias para ligar para operações específicas de serviço Blob ou Queue, consulte [permissões para ligar para operações de dados de blob e fila](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Âmbito do recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Aceder a dados com uma conta AD Azure

O acesso aos dados blob ou de fila através do portal Azure, PowerShell ou Azure CLI pode ser autorizado através da utilização da conta Azure AD do utilizador ou através da utilização das teclas de acesso à conta (autorização de Chave Partilhada).

### <a name="data-access-from-the-azure-portal"></a>Acesso a dados a partir do portal Azure

O portal Azure pode utilizar a sua conta Azure AD ou as chaves de acesso à conta para aceder a dados de blob e fila numa conta de armazenamento Azure. Que esquema de autorização que o portal Azure utiliza depende das funções RBAC que lhe são atribuídas.

Quando tenta aceder a dados de blob ou de fila, o portal Azure verifica primeiro se lhe foi atribuída uma função RBAC com **microsoft.Storage/storageAcounts/listkeys/action**. Se lhe foi atribuído um papel com esta ação, então o portal Azure utiliza a chave de conta para aceder a dados de blob e fila através da autorização da Chave Partilhada. Se não lhe foi atribuído um papel com esta ação, então o portal Azure tenta aceder aos dados utilizando a sua conta Azure AD.

Para aceder a dados de blob ou fila a partir do portal Azure utilizando a sua conta Azure AD, precisa de permissões para aceder a dados de blob e fila, e também precisa de permissões para navegar através dos recursos da conta de armazenamento no portal Azure. As funções incorporadas fornecidas pela Azure Storage concedem acesso a recursos blob e fila, mas não concedem permissões aos recursos da conta de armazenamento. Por esta razão, o acesso ao portal também requer a atribuição de uma função de Gestor de Recursos Azure, como a função [Reader,](../../role-based-access-control/built-in-roles.md#reader) alargada ao nível da conta de armazenamento ou superior. A função **Reader** concede as permissões mais restritas, mas outra função de Gestor de Recursos Azure que concede acesso aos recursos de gestão de conta de armazenamento também é aceitável. Para saber mais sobre como atribuir permissões aos utilizadores para acesso a dados no portal Azure com uma conta AZure AD, consulte [o Acesso ao Grant para blob Azure e dados de fila com o RBAC no portal Azure.](storage-auth-aad-rbac-portal.md)

O portal Azure indica qual o sistema de autorização que está a ser utilizado quando navega para um contentor ou fila. Para obter mais informações sobre o acesso aos dados no portal, consulte [utilizar o portal Azure para aceder aos dados de blob ou fila](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Acesso de dados a partir de PowerShell ou Azure CLI

Suporte Azure CLI e PowerShell assinando com credenciais AZure AD. Depois de assinar, a sua sessão passa por baixo dessas credenciais. Para saber mais, consulte os [comandos Run Azure CLI ou PowerShell com credenciais AD AD para aceder a dados de blob ou fila](authorize-active-directory-powershell.md).

## <a name="next-steps"></a>Próximos passos

- [Autorizar o acesso a blobs e filas com o Azure Ative Directory e identidades geridas para a Azure Resources](storage-auth-aad-msi.md)
- [Autorizar com o Azure Ative Directory a partir de um pedido de acesso a bolhas e filas](storage-auth-aad-app.md)
- [Suporte de armazenamento Azure para o controlo de acesso baseado no Azure Ative, geralmente disponível](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

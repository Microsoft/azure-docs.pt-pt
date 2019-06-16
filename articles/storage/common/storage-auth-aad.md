---
title: Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory | Documentos da Microsoft
description: Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 66051bd0f8be349f748c72218d538bba273be8f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147256"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory

O armazenamento do Azure suporta a autenticação e autorização com o Azure Active Directory (AD) para os serviços de BLOBs e filas. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder acesso a utilizadores, grupos ou principais de serviço de aplicações. 

Autenticação de utilizadores ou aplicações que utilizam as credenciais do Azure AD fornece segurança superior e a facilidade de utilização ao longo de outros meios de autorização. Enquanto pode continuar a utilizar a autorização de chave partilhada com as suas aplicações, utilizar o Azure AD evita a necessidade de armazenar a chave de acesso da conta com o seu código. Também pode continuar a utilizar assinaturas de acesso partilhado (SAS) para conceder um acesso otimizado aos recursos na sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerir os tokens de SAS ou se preocupar sobre revogar uma SAS comprometida. A Microsoft recomenda utilizar autenticação do Azure AD para as suas aplicações de armazenamento do Azure, sempre que possível.

Autenticação e autorização com credenciais do Azure AD está disponível para todos os fins gerais e de contas do Blob storage em todas as regiões públicas e clouds nacionais. Apenas as contas de armazenamento criada com o suporte de modelo de implementação Azure Resource Manager autorização do Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Descrição geral do Azure AD para blobs e filas

Quando uma entidade de segurança (um utilizador, grupo ou aplicação) tenta aceder a um recurso de BLOBs ou filas, o pedido tem de estar autorizado, a menos que seja um blob disponível para acesso anónimo. Com o Azure AD, o acesso a um recurso é um processo de dois passos. Em primeiro lugar, a identidade da entidade de segurança é autenticada e é devolvido um token de OAuth 2.0. Em seguida, o token é passado como parte de um pedido para o serviço de BLOBs ou filas e utilizado pelo serviço para autorizar o acesso para o recurso especificado.

A etapa de autenticação requer que um aplicativo solicitar um token de acesso de OAuth 2.0 em tempo de execução. Se um aplicativo estiver em execução a partir de dentro de uma entidade do Azure como uma VM do Azure, um conjunto de dimensionamento de máquina virtual ou uma aplicação de funções do Azure, pode utilizar um [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para aceder a blobs ou filas. Para saber como autorizar os pedidos efetuados por uma identidade gerida para o serviço de Blobs do Azure ou filas, veja [autenticar o acesso a blobs e filas com o Azure Active Directory e de identidades geridas para recursos do Azure](storage-auth-aad-msi.md).

O passo de autorização requer que uma ou mais funções do RBAC ser atribuída a entidade de segurança. O armazenamento do Azure fornece funções RBAC que abrangem conjuntos comuns de permissões para os dados de BLOBs e filas. As funções que são atribuídas a uma entidade de segurança determinam as permissões que terão o principal. Para saber mais sobre a atribuição de funções RBAC do armazenamento do Azure, veja [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).

Aplicativos nativos e aplicativos da web que efetuam pedidos para o serviço de Blobs do Azure ou a fila, também podem autenticar com o Azure AD. Para saber como solicitar um token de acesso e utilizá-lo para autorizar os pedidos de dados de BLOBs ou filas, veja [autenticar com o Azure AD a partir de uma aplicação de armazenamento do Azure](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Atribuir funções de RBAC para direitos de acesso

Azure Active Directory (Azure AD) autoriza direitos de acesso a recursos protegidos por meio [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões utilizadas para aceder a dados de BLOBs e filas. Também pode definir funções personalizadas para o acesso aos dados de BLOBs e filas.

Quando uma função RBAC é atribuída a um principal de segurança do Azure AD, o Azure concede acesso a esses recursos para aquela entidade de segurança. Acesso pode ser confinado ao nível da subscrição, o grupo de recursos, a conta de armazenamento, ou um contentor individual ou fila. Um principal de segurança do Azure AD pode ser um utilizador, um grupo, um principal de serviço de aplicações, ou uma [identidade de recursos do Azure gerida](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Funções incorporadas do RBAC para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para saber como atribuir uma função RBAC incorporada a uma entidade de segurança, consulte um dos seguintes artigos:

- [Conceder acesso a dados BLOBs e filas do Azure com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso a dados de BLOBs e filas do Azure com o RBAC com a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de BLOBs e filas do Azure com o RBAC com o PowerShell](storage-auth-aad-rbac-powershell.md)

Para obter mais informações sobre como as funções incorporadas são definidas para o armazenamento do Azure, consulte [compreender as definições de função](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview). Para obter informações sobre como criar funções RBAC personalizadas, consulte [criar funções personalizadas para controlo de acesso](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Permissões de acesso para operações de dados

Para obter detalhes sobre as permissões necessárias para chamar operações de serviço de BLOBs ou filas específicas, consulte [permissões para chamar BLOBs e filas de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Âmbito de recursos

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Aceder a dados com uma conta do Azure AD

Acesso a dados de BLOBs ou filas através do portal do Azure, PowerShell, ou a CLI do Azure pode ser autorizada utilizando a conta de utilizador do Azure AD ou com as chaves de acesso de conta (autenticação de chave partilhada).

### <a name="data-access-from-the-azure-portal"></a>Acesso a dados no portal do Azure

O portal do Azure pode utilizar a sua conta do Azure AD ou as chaves de acesso de conta para aceder aos dados de BLOBs e filas numa conta de armazenamento do Azure. A esquema de autorização do portal do Azure utiliza depende as funções RBAC que estão atribuídas ao utilizador.

Quando tentar aceder aos dados de BLOBs ou filas, o portal do Azure verifica primeiro se lhe tenha sido atribuída uma função RBAC com **Microsoft.Storage/storageAccounts/listkeys/action**. Se lhe tenha sido atribuída uma função com esta ação, o portal do Azure utiliza a chave da conta para aceder aos dados de BLOBs e filas por meio de autorização de chave partilhada. Se não tiver sido atribuído uma função com esta ação, em seguida, o portal do Azure tenta aceder a dados com a sua conta do Azure AD.

Para aceder a BLOBs ou filas dados a partir do portal do Azure com a sua conta do Azure AD, precisa de permissões para aceder aos dados de BLOBs e filas e também precisa de permissões para navegar pelos recursos da conta de armazenamento no portal do Azure. As funções incorporadas fornecidas pelo armazenamento do Azure concedem acesso a recursos de BLOBs e filas, mas eles não concedem permissões para recursos da conta de armazenamento. Por esse motivo, acesso ao portal também requer a atribuição de uma função do Azure Resource Manager, tais como o [leitor](../../role-based-access-control/built-in-roles.md#reader) função, como escopo o nível de conta de armazenamento ou superior. O **leitor** função concede as permissões mais restritas, mas outra função do Azure Resource Manager que concede acesso a recursos de gerenciamento de conta de armazenamento também é aceitável. Para saber mais sobre como atribuir permissões a utilizadores para acesso a dados no portal do Azure com uma conta do Azure AD, veja [conceder acesso a dados BLOBs e filas do Azure com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md).

O portal do Azure indica qual esquema de autorização está em utilização ao navegar para um contentor ou uma fila. Para obter mais informações sobre o acesso a dados no portal, consulte [utilizar o portal do Azure para aceder a dados de BLOBs ou filas](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Acesso a dados do PowerShell ou da CLI do Azure

CLI do Azure e o PowerShell suportam início de sessão com credenciais do Azure AD. Depois de iniciar sessão, a sessão é executado sob essas credenciais. Para obter mais informações, consulte [comandos de executar o Azure CLI ou o PowerShell com credenciais do Azure AD para aceder aos dados de BLOBs ou filas](storage-auth-aad-script.md).

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Autenticação do Azure do AD através de SMB para ficheiros do Azure

Os ficheiros do Azure suporta a autenticação com o Azure AD através de SMB para associados a um domínio apenas VMs (pré-visualização). Para saber mais sobre como utilizar o Azure AD através de SMB para ficheiros do Azure, veja [autenticação de descrição geral do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Passos Seguintes

- [Autenticar o acesso a blobs e filas com o Azure Active Directory e de identidades geridas para recursos do Azure](storage-auth-aad-msi.md)
- [Authenticate with Azure Active Directory from an application for access to blobs and queues](storage-auth-aad-app.md) (Autenticar com o Azure Active Directory a partir de uma aplicação para aceder a blobs e filas)
- [Suporte de armazenamento do Azure para o Azure Active Directory com base em disponibilidade geral do controlo de acesso](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

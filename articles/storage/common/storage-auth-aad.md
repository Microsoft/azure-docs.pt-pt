---
title: Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory | Documentos da Microsoft
description: Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ff543b7275ab05a83b1be1d156cbc6059a3b5430
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369902"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory

O armazenamento do Azure suporta a autenticação e autorização com o Azure Active Directory (AD) para os serviços de BLOBs e filas. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder acesso a utilizadores, grupos ou principais de serviço de aplicações. 

Autenticação de utilizadores ou aplicações que utilizam as credenciais do Azure AD fornece segurança superior e a facilidade de utilização ao longo de outros meios de autorização. Enquanto pode continuar a utilizar a autorização de chave partilhada com as suas aplicações, utilizar o Azure AD evita a necessidade de armazenar a chave de acesso da conta com o seu código. Também pode continuar a utilizar assinaturas de acesso partilhado (SAS) para conceder um acesso otimizado aos recursos na sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerir os tokens de SAS ou se preocupar sobre revogar uma SAS comprometida. A Microsoft recomenda utilizar autenticação do Azure AD para as suas aplicações de armazenamento do Azure, sempre que possível.

Autenticação e autorização com credenciais do Azure AD está disponível para todos os fins gerais v2, para fins gerais v1 e contas de armazenamento de BLOBs em todas as regiões públicas. Apenas as contas de armazenamento criada com o suporte de modelo de implementação Azure Resource Manager autorização do Azure AD.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Descrição geral do Azure AD para blobs e filas

É a primeira etapa no uso de integração do Azure AD com o armazenamento do Azure atribuir funções RBAC para dados de armazenamento para o seu principal de serviço (um utilizador, grupo ou principal de serviço de aplicação) ou identidades geridas para recursos do Azure. Funções RBAC abrangem comuns conjuntos de permissões para contentores e filas. Para saber mais sobre a atribuição de funções RBAC do armazenamento do Azure, veja [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).

Para utilizar o Azure AD para autorizar o acesso aos recursos de armazenamento nas suas aplicações, terá de solicitar um token de acesso de OAuth 2.0 a partir do código. Para saber como solicitar um token de acesso e utilizá-lo para autorizar os pedidos ao armazenamento do Azure, veja [autenticar com o Azure AD a partir de uma aplicação de armazenamento do Azure](storage-auth-aad-app.md). Se estiver a utilizar uma identidade gerida, veja [Authenticate acesso para blobs e filas com o Azure managed identidades para recursos do Azure](storage-auth-aad-msi.md).

CLI do Azure e o PowerShell suportam agora iniciar sessão com uma identidade do Azure AD. Depois de iniciar sessão com uma identidade do Azure AD, a sessão é executado sob essa identidade. Para obter mais informações, consulte [utilizar uma identidade do Azure AD para aceder ao armazenamento do Azure com a CLI ou o PowerShell](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções do RBAC para blobs e filas

Azure Active Directory (Azure AD) autoriza direitos de acesso a recursos protegidos por meio [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões utilizados para aceder aos contentores ou filas. 

Quando uma função RBAC é atribuída a um principal de segurança do Azure AD, o Azure concede acesso a esses recursos para aquela entidade de segurança. Acesso pode ser confinado ao nível da subscrição, o grupo de recursos, a conta de armazenamento, ou um contentor individual ou fila. Um principal de segurança do Azure AD pode ser um utilizador, um grupo, um principal de serviço de aplicações, ou uma [identidade de recursos do Azure gerida](../../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para saber como atribuir uma função incorporada no portal do Azure, veja [conceder acesso a contentores do Azure e filas com RBAC no portal do Azure](storage-auth-aad-rbac.md).

### <a name="access-permissions-granted-by-rbac-roles"></a>Permissões de acesso concedidas por funções RBAC 

Para obter detalhes sobre as permissões necessárias para chamar operações de armazenamento do Azure, consulte [permissões para chamar as operações REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>Passos Seguintes

- [Conceder acesso a contentores do Azure e filas com RBAC no portal do Azure](storage-auth-aad-rbac.md)
- [Authenticate with Azure Active Directory from an application for access to blobs and queues](storage-auth-aad-app.md) (Autenticar com o Azure Active Directory a partir de uma aplicação para aceder a blobs e filas)
- [Autenticar o acesso a blobs e filas com identidades geridas para recursos do Azure](storage-auth-aad-msi.md)
- [Utilizar uma identidade do Azure AD para aceder ao armazenamento do Azure com a CLI ou o PowerShell](storage-auth-aad-script.md)
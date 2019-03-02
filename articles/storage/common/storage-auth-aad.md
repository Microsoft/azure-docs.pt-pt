---
title: Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory (pré-visualização) | Documentos da Microsoft
description: Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory (pré-visualização).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3a8ee51656b5521916e68ee34edda91c34ea0c35
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216046"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory (pré-visualização)

O armazenamento do Azure suporta a autenticação e autorização com o Azure Active Directory (AD) para os serviços de BLOBs e filas. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder acesso a utilizadores, grupos ou principais de serviço de aplicações. 

Autenticação de utilizadores ou aplicações que utilizam as credenciais do Azure AD fornece segurança superior e a facilidade de utilização ao longo de outros meios de autorização. Enquanto pode continuar a utilizar a autorização de chave partilhada com as suas aplicações, utilizar o Azure AD evita a necessidade de armazenar a chave de acesso da conta com o seu código. Também pode continuar a utilizar assinaturas de acesso partilhado (SAS) para conceder um acesso otimizado aos recursos na sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerir os tokens de SAS ou se preocupar sobre revogar uma SAS comprometida. A Microsoft recomenda utilizar autenticação do Azure AD para as suas aplicações de armazenamento do Azure, sempre que possível.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>Sobre a pré-visualização

Tenha em atenção os seguintes pontos relativamente a pré-visualização:

- Integração do Azure AD está disponível para os serviços de BLOBs e filas apenas na pré-visualização.
- Integração do Azure AD está disponível para contas de armazenamento de BLOBs, GPv1 e GPv2 em todas as regiões públicas. 
- São suportadas apenas as contas de armazenamento criadas com o modelo de implementação do Resource Manager. 
- Suporte para obter informações de identidade do autor da chamada no registo de análise de armazenamento do Azure estará disponível brevemente.
- O Azure AD autorização de acesso a recursos em contas de armazenamento standard é atualmente suportada. Autorização de acesso para blobs de páginas em contas de armazenamento premium será suportada em breve.
- O armazenamento do Azure suporta funções do RBAC incorporadas e personalizadas. Pode atribuir funções no âmbito da subscrição, o grupo de recursos, a conta de armazenamento, ou um contentor individual ou fila.
- As bibliotecas de cliente de armazenamento do Azure que suportam atualmente a integração do Azure AD incluem:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [BLOBs, filas e ficheiros](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>Descrição geral do Azure AD para o armazenamento

É a primeira etapa no uso de integração do Azure AD com o armazenamento do Azure atribuir funções RBAC para dados de armazenamento para o seu principal de serviço (um utilizador, grupo ou principal de serviço de aplicação) ou identidades geridas para recursos do Azure. Funções RBAC abrangem comuns conjuntos de permissões para contentores e filas. Para saber mais sobre a atribuição de funções RBAC do armazenamento do Azure, veja [gerir direitos de acesso aos dados de armazenamento com o RBAC (pré-visualização)](storage-auth-aad-rbac.md).

Para utilizar o Azure AD para autorizar o acesso aos recursos de armazenamento nas suas aplicações, terá de solicitar um token de acesso de OAuth 2.0 a partir do código. Para saber como solicitar um token de acesso e utilizá-lo para autorizar os pedidos ao armazenamento do Azure, veja [autenticar com o Azure AD a partir de uma aplicação de armazenamento do Azure (pré-visualização)](storage-auth-aad-app.md). Se estiver a utilizar uma identidade gerida, veja [Authenticate acesso para blobs e filas com o Azure managed identidades para recursos do Azure (pré-visualização)](storage-auth-aad-msi.md).

CLI do Azure e o PowerShell suportam agora iniciar sessão com uma identidade do Azure AD. Depois de iniciar sessão com uma identidade do Azure AD, a sessão é executado sob essa identidade. Para obter mais informações, consulte [utilizar uma identidade do Azure AD para aceder ao armazenamento do Azure com a CLI ou o PowerShell (pré-visualização)](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções do RBAC para blobs e filas

Azure Active Directory (Azure AD) autoriza direitos de acesso a recursos protegidos por meio [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões utilizados para aceder aos contentores ou filas. 

Quando uma função RBAC é atribuída a um principal de segurança do Azure AD, o Azure concede acesso a esses recursos para aquela entidade de segurança. Acesso pode ser confinado ao nível da subscrição, o grupo de recursos, a conta de armazenamento, ou um contentor individual ou fila. Um principal de segurança do Azure AD pode ser um utilizador, um grupo, um principal de serviço de aplicações, ou uma [identidade de recursos do Azure gerida](../../active-directory/managed-identities-azure-resources/overview.md). 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para saber como atribuir uma função incorporada no portal do Azure, veja [conceder acesso a contentores do Azure e filas com RBAC no portal do Azure (pré-visualização)](storage-auth-aad-rbac.md).

### <a name="access-permissions-granted-by-rbac-roles"></a>Permissões de acesso concedidas por funções RBAC 

A tabela seguinte resume os direitos de acesso concedidos por funções incorporadas para diferentes níveis de âmbito:

|Âmbito|Proprietário de dados de blob|Contribuinte de dados de blob|Leitor de dados de blob|Contribuinte de dados de fila|Leitor de dados de fila|
|---|---|---|---|---|---|
|Nível de assinatura|Gestão de controlo a todos os contentores e blobs na subscrição de acesso de acesso de leitura/escrita e POSIX|Acesso de leitura/escrita a todos os contentores e blobs na subscrição| Acesso de leitura a todos os contentores e blobs na subscrição|Acesso de leitura/gravação para todas as filas na subscrição|Acesso de leitura para todas as filas na subscrição|
|Ao nível do grupo de recursos|Gestão de controlo a todos os contentores e blobs, no grupo de recursos de acesso de acesso de leitura/escrita e POSIX|Acesso de leitura/escrita a todos os contentores e blobs, no grupo de recursos|Acesso de leitura a todos os contentores e blobs, no grupo de recursos|Acesso de leitura/gravação para todas as filas no grupo de recursos|Acesso de leitura para todas as filas no grupo de recursos|
|Nível de conta de armazenamento|Gestão de controlo a todos os contentores e blobs na conta de armazenamento de acesso de acesso de leitura/escrita e POSIX|Acesso de leitura/escrita a todos os contentores e blobs na conta de armazenamento|Acesso de leitura a todos os contentores e blobs na conta de armazenamento|Acesso de leitura/gravação para todas as filas na conta de armazenamento|Acesso de leitura para todas as filas na conta de armazenamento|
|Nível de contêiner/fila|Gestão de controlo para o contentor especificado e respetivos blobs de acesso POSIX e acesso de leitura/escrita.|Acesso de leitura/gravação para o contentor especificado e respetivos blobs|Acesso de leitura para o contentor especificado e respetivos blobs|Acesso de leitura/escrita para a fila especificada|Acesso de leitura para a fila especificada|

Para obter detalhes sobre as permissões necessárias para chamar operações de armazenamento do Azure, consulte [permissões para chamar as operações REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a integração do Azure AD para os Blobs do Azure e filas de mensagens em fila, consulte a postagem no armazenamento do Azure team blog [a autenticação de pré-visualização do Azure AD para o armazenamento do Azure a anunciar](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).

---
title: Assegurar os principais do serviço no Azure Ative Directory
description: Encontre, avalie e proteja os diretores de serviço.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bab8e8c6dfb944e496c636d53217e63175be9fbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587853"
---
# <a name="securing-service-principals"></a>Assegurar os principais de serviços

Um diretor de [serviço](../develop/app-objects-and-service-principals.md) Azure Ative (Azure AD) é a representação local de um objeto de aplicação num único inquilino ou diretório.  ‎It functions as the identity of the application instance. Os diretores de serviço definem quem pode aceder à aplicação e quais os recursos a que a aplicação pode aceder. Um principal serviço é criado em cada inquilino onde a aplicação é usada e faz referência ao objeto de aplicação globalmente único. O inquilino assegura a inscrição do diretor de serviço e o acesso aos recursos.  

### <a name="tenant-service-principal-relationships"></a>Relações principais de serviço de inquilino
Um pedido de inquilino único tem apenas um principiante de serviço no seu inquilino de casa. Uma aplicação web de vários inquilinos ou API requer um principal serviço em cada inquilino. Um principal serviço é criado quando um utilizador desse inquilino consentiu com a utilização da aplicação ou da API. Este consentimento cria uma relação entre a aplicação multi-inquilino e os seus principais serviços associados.

Um pedido de multi-inquilinos é asseado em um único inquilino e é projetado para ter casos em outros inquilinos. A maioria das aplicações de software-as-a-service (SaaS) são projetadas para o multi-arrendamento. Utilize os princípios de serviço para garantir a postura de segurança certa para a aplicação e os seus utilizadores em casos de utilização de inquilinos individuais e multi-inquilinos.

## <a name="applicationid-and-objectid"></a>ApplicationID e ObjectID

Uma determinada instância de aplicação tem duas propriedades distintas: o ApplicationID (também conhecido como ClientID) e o ObjectID.

> [!NOTE] 
> Pode descobrir que os termos de aplicação e de serviço são utilizados intercambiavelmente quando se referem a uma aplicação no contexto de tarefas relacionadas com a autenticação. No entanto, são duas representações diferentes de aplicações no Azure AD.
 

O ApplicationID representa a aplicação global e é o mesmo para todos os casos de candidatura entre inquilinos. O ObjectID é um valor único para um objeto de aplicação e representa o principal de serviço. Tal como acontece com utilizadores, grupos e outros recursos, o ObjectID ajuda a identificar de forma única uma instância de aplicação em Azure AD.

Para obter informações mais detalhadas sobre este tema, consulte [a relação principal de aplicação e serviço.](../develop/app-objects-and-service-principals.md)

Também pode criar uma aplicação e o seu principal objeto de serviço (ObjectID) num inquilino usando Azure PowerShell, Azure CLI, Microsoft Graph, o portal Azure, e outras ferramentas. 

![Screen shot mostrando um novo registo de aplicação, com os campos de ID de aplicação e ID de objeto realçados.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Existem dois mecanismos de autenticação utilizando os principais serviços — certificados de cliente e segredos de cliente. 

![ Imagem de ecrã da página da Nova App mostrando as áreas de certificados e segredos dos clientes destacadas.](./media/securing-service-accounts/secure-principal-certificates.png)

Os certificados são mais seguros: use certificados de cliente, se possível. Ao contrário dos segredos dos clientes, os certificados de cliente não podem ser incorporados acidentalmente em código. Utilize o Cofre de Chaves Azure para gestão de certificados e segredos sempre que possível para encriptar os seguintes ativos utilizando chaves protegidas por módulos de segurança de hardware:

* chaves de autenticação

* chaves de conta de armazenamento

* chaves de encriptação de dados

* Ficheiros .pfx

* senhas 

Para obter mais informações sobre o Cofre da Chave Azure e como usá-lo para certificação e gestão secreta, consulte [About Azure Key Vault](../../key-vault/general/overview.md) e Atribuir uma política de acesso ao Cofre de Chaves utilizando o portal [Azure](../../key-vault/general/assign-access-policy-portal.md). 

 ### <a name="challenges-and-mitigations"></a>Desafios e mitigações
A tabela a seguir apresenta mitigações aos desafios que poderá encontrar ao utilizar os princípios de serviço.


| Desafios| Mitigações |
| - | - |
| Comentários de acesso a diretores de serviços atribuídos a funções privilegiadas.| Esta funcionalidade encontra-se em pré-visualização, e ainda não está disponível de forma bastante disponível. |
| Comentários sobre o acesso dos principais do serviço| Verificação manual da lista de controlo de acesso dos recursos utilizando o portal Azure. |
| Diretores de serviço autorizados| Quando criar contas de serviço de automação ou ou principais de serviço, forneça apenas as permissões necessárias para a tarefa. Avalie os principais serviços existentes para ver se consegue reduzir privilégios. |
|Identificar alterações às credenciais ou métodos de autenticação dos principais de serviço |Utilize o livro de relatórios de operações sensíveis, que pode ajudar a mitigar este problema. [Veja a explicação neste post de blog.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)|

## <a name="find-accounts-using-service-principals"></a>Encontrar contas usando os principais do serviço
Executar os seguintes comandos para encontrar contas usando os principais de serviço.

Utilizar a CLI do Azure


`az ad sp list`

Com o PowerShell

`Get-AzureADServicePrincipal -All:$true` 


Para mais informações consulte [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal)

## <a name="assess-service-principal-security"></a>Avaliar a segurança principal do serviço

Para avaliar a segurança dos seus diretores de serviço, certifique-se de que avalia privilégios e armazenamento credencial.

Atenuar potenciais desafios usando as seguintes informações.
|Desafios | Mitigações|
| - | - |
| Detete o utilizador que consentiu numa app multi-inquilino, e detete subsídios de consentimento ilícitos a uma app multi-inquilina | Execute o seguinte PowerShell para encontrar aplicativos multi-inquilinos.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>Desativar o consentimento do utilizador. <br>Permitir o consentimento do utilizador de editores verificados, para permissões selecionadas (recomendadas) <br> Utilize acesso condicional para bloquear os princípios de serviço de locais não fided oses. Configurá-los no contexto do utilizador e os seus tokens devem ser utilizados para ativar o principal de serviço.|
|Uso de um segredo partilhado codificado num script usando um diretor de serviço.|Use um certificado ou cofre de chaves Azure.|
|Rastreando quem está usando o certificado ou o segredo| Monitorize as entradas do diretor de serviço utilizando os registos de inscrição AZure AD.|
Não é possível gerir a inscrição dos diretores de serviço com acesso condicional.| Monitorize os logins utilizando os registos de inscrição Azure AD
| O papel padrão do Azure RBAC é o Contribuinte. |Avaliar as necessidades e aplicar o papel com o mínimo possível de permissões para satisfazer essa necessidade.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>Passar de uma conta de utilizador para um principal de serviço  
‎If you are using an Azure user account as a service principal, evaluate if you can move to a [Managed Identity](../../app-service/overview-managed-identity.md?tabs=dotnet) or a service principal. Se não puder utilizar uma identidade gerida, fornece um principal de serviço que tenha permissões e âmbito suficientes para executar as tarefas necessárias. Pode criar um principal de serviço [registando uma aplicação,](../develop/howto-create-service-principal-portal.md)ou com [o PowerShell.](../develop/howto-authenticate-service-principal-powershell.md)

When using Microsoft Graph, check the documentation of the specific API, [like in this example](/powershell/azure/create-azure-service-principal-azureps), ‎and make sure the permission type for application is showing as supported.

## <a name="next-steps"></a>Passos seguintes

**Para saber mais sobre os diretores de serviço:**

[Criar um principal de serviço](../develop/howto-create-service-principal-portal.md)

 [Iniciar inscrições principais do serviço](../reports-monitoring/concept-sign-ins.md#sign-ins-report)

**Para saber mais sobre a segurança de contas de serviço:**

[Introdução às contas de serviço da Azure](service-accounts-introduction-azure.md)

[Assegurar identidades geridas](service-accounts-managed-identities.md)

[Contas de serviço Azure governando](service-accounts-governing-azure.md)

[Introdução às contas de serviço no local](service-accounts-on-premises.md)
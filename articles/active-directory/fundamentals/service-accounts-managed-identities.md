---
title: Assegurar identidades geridas no Azure Ative Directory
description: Explicação de como encontrar, avaliar e aumentar a segurança das identidades geridas.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32ba630d5e2f8e3e581f394af2ee687a971d3a7a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693283"
---
# <a name="securing-managed-identities"></a>Assegurar identidades geridas

Os desenvolvedores são frequentemente desafiados pela gestão de segredos e credenciais usados para garantir a comunicação entre diferentes serviços. As identidades geridas são identidades seguras do Azure Ative Directory (Azure AD) criadas para fornecer identidades para os recursos do Azure.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Benefícios da utilização de identidades geridas para recursos Azure

Seguem-se os benefícios da utilização de identidades geridas:

* Não precisas de gerir credenciais. Com identidades geridas, as credenciais são totalmente geridas, rotativas e protegidas por Azure. As identidades são automaticamente fornecidas e eliminadas com recursos Azure. As identidades geridas permitem aos recursos da Azure comunicar com todos os serviços que suportam a autenticação Azure AD.

* Ninguém (incluindo qualquer administrador global) tem acesso às credenciais, pelo que não pode ser acidentalmente vazado por, por exemplo, ser incluído no código.

## <a name="when-to-use-managed-identities"></a>Quando usar identidades geridas?

As identidades geridas são mais utilizadas para comunicações entre serviços que suportam a autenticação AZure AD. 

Um sistema de origem solicita acesso a um serviço de alvo. Qualquer recurso Azure pode ser um sistema de origem. Por exemplo, um Azure VM, instância Azure Function e Azure App Services casos suportam identidades geridas.

[!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>Como funciona a autenticação e autorização

Com identidades geridas, o sistema de origem pode obter um símbolo da Azure AD sem que o proprietário da fonte tenha de gerir credenciais. Azure gere as credenciais. O token obtido pelo sistema de origem é apresentado ao sistema alvo para autenticação. 

O sistema alvo precisa de autenticar (identificar) e autorizar o sistema de origem antes de permitir o acesso. Quando o serviço alvo suporta a autenticação baseada em Azure AD, aceita um token de acesso emitido pela Azure AD. 

O Azure tem um avião de controlo e um avião de dados. No plano de controlo, cria-se recursos, e no plano de dados acede-los. Por exemplo, cria-se uma base de dados Cosmos no plano de controlo, mas consulta-a no plano de dados.

Uma vez que o sistema alvo aceite o símbolo para a autenticação, pode suportar diferentes mecanismos de autorização para o seu plano de controlo e plano de dados.

Todas as operações do avião de controlo da Azure são geridas pelo [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) e utilizam [o Azure Role Based Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview). No plano de dados, cada sistema alvo tem o seu próprio mecanismo de autorização. O Azure Storage suporta o Azure RBAC no plano de dados. Por exemplo, as aplicações que utilizam o Azure App Services podem ler dados do Azure Storage, e as aplicações que usam o Serviço Azure Kubernetes podem ler segredos armazenados no Cofre da Chave Azure.

Para obter mais informações sobre os aviões de controlo e dados, consulte [as operações do avião de controlo e do plano de dados - Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/control-plane-and-data-plane).

Todos os serviços da Azure acabarão por suportar identidades geridas. Para mais informações, consulte [Serviços que suportem identidades geridas para recursos Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

##  

## <a name="types-of-managed-identities"></a>Tipos de identidades geridas

Existem dois tipos de identidades geridas - atribuídas pelo sistema e atribuídas pelo utilizador.

A identidade gerida atribuída pelo sistema tem as seguintes propriedades:

* Têm uma relação 1:1 com o recurso Azure. Por exemplo, há uma identidade gerida única associada a cada VM.

* Estão ligados ao ciclo de vida dos recursos do Azure. Quando o recurso é eliminado, a identidade gerida associada ao seu é automaticamente eliminada, eliminando o risco associado a contas órfãs. 

As identidades geridas atribuídas pelo utilizador têm as seguintes propriedades:

* O ciclo de vida destas identidades é independente de um recurso Azure, e você deve gerir o ciclo de vida. Quando o recurso Azure é eliminado, a identidade gerida atribuída ao utilizador atribuída não é automaticamente eliminada para si.

* Uma única identidade gerida atribuída pelo utilizador pode ser atribuída a recursos Azure zero ou mais.

* Podem ser criados com antecedência e depois atribuídos a um recurso.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Encontre diretores de serviços de identidade geridos em Azure AD

Existem várias formas de encontrar identidades geridas:

* Utilização da página Aplicações empresariais no portal Azure

* Usando o Gráfico da Microsoft

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

1. Em Azure AD, selecione a aplicação Enterprise.

2. Selecione o filtro para "Identidades Geridas" 

   ![Imagem de todas as aplicações no ecrã com o dropdown tipo aplicação destacando "Identidades Geridas".](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Usando o Gráfico da Microsoft

Pode obter uma lista de todas as identidades geridas no seu inquilino com o seguinte pedido GET ao Microsoft Graph:

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

Pode filtrar estes pedidos. Para mais informações, consulte a documentação do Gráfico do [serviço GETPrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-get?view=graph-rest-1.0&tabs=http).

## <a name="assess-the-security-of-managed-identities"></a>Avaliar a segurança das identidades geridas 

Pode avaliar a segurança das identidades geridas das seguintes formas:

* Examine os privilégios e certifique-se de que o modelo menos privilegiado é selecionado. Utilize o cmdlet PowerShell seguinte para obter as permissões atribuídas às suas identidades geridas.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Certifique-se de que a identidade gerida não faz parte de nenhum grupo privilegiado, como um grupo de administradores.  
‎You can do this by enumerating the members of your highly privileged groups with PowerShell.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Certifique-se de que sabe a que recursos a identidade gerida está a aceder.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell)

## <a name="move-to-managed-identities"></a>Mover-se para identidades geridas

Se estiver a utilizar um titular de serviço ou uma conta de utilizador Azure AD, avalie se pode, em vez disso, utilizar uma conseguida para eliminar a necessidade de proteger, rodar e gerir credenciais. 

## <a name="next-steps"></a>Passos seguintes

**Para obter informações sobre a criação de identidades geridas, consulte:** 

[Criar uma identidade gerida atribuída pelo utilizador.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) 

[Ativar um sistema atribuído identidade gerida durante a criação de recursos](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

[Permitir a identidade gerida atribuída pelo sistema num recurso existente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Para obter mais informações sobre contas de serviço consulte:**

[Introdução às contas de serviço do Azure Ative Directory](service-accounts-introduction-azure.md)

[Assegurar os principais de serviços](service-accounts-principal.md)

[Contas de serviço Azure governando](service-accounts-governing-azure.md)

[Introdução às contas de serviço no local](service-accounts-on-poremises.md)

 

 

 

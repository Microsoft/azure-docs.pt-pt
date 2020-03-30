---
title: Permissões de função de administrador personalizado disponíveis - Azure AD / Microsoft Docs
description: Permissões de funções de administrador personalizado para delegar a gestão de identidade.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025154"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Subtipos e permissões de registo de candidaturas no Diretório Ativo do Azure

Este artigo contém as permissões de registo de aplicações atualmente disponíveis para definições de papéis personalizados no Azure Ative Directory (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Permissões para gestão de aplicações de diretório único

Ao escolher as permissões para o seu papel personalizado, tem a opção de conceder acesso para gerir apenas aplicações de diretório único. As aplicações de diretório único só estão disponíveis para utilizadores da organização Azure AD onde a aplicação está registada. As aplicações de diretório único são definidas como tendo tipos de **conta suportados** definidos para "Contas apenas neste diretório organizacional". No Gráfico API, as aplicações de um único diretório têm a propriedade signInAudience definida para "AzureADMyOrg".

Para conceder acesso a penas para gerir aplicações de diretório único, utilize as permissões abaixo com as **aplicações subtipo.myOrganization**. Por exemplo, microsoft.directy/applications.myOrganization/basic/update.

Consulte a visão geral das [funções personalizadas](roles-custom-overview.md) para obter uma explicação do que significam os termos gerais subtipo, permissão e conjunto de propriedades. As seguintes informações são específicas dos registos de candidaturas.

### <a name="create-and-delete"></a>Criar e excluir

Existem duas permissões disponíveis para a concessão da capacidade de criar registos de candidaturas, cada uma com comportamentos diferentes:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directy/applications/createAsOwner

A atribuição desta permissão resulta na adição do criador como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado selevai supor-se com a quota de 250 objetos criados pelo criador.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directy/applications/create

A atribuição desta permissão resulta na não adição do criador como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado não contará com a quota de 250 objetos criados pelo criador. Utilize esta permissão cuidadosamente, pois não há nada que impeça o designado de criar registos de aplicações até que a quota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, esta permissão tem precedência.

Se ambas as permissões forem atribuídas, a permissão /criar terá precedência. Embora a permissão /createAsOwner não adicione automaticamente o criador como primeiro proprietário, os proprietários podem ser especificados durante a criação do registo da aplicação ao utilizar em APIs gráficos ou cmdlets PowerShell.

Criar permissões que garantam o acesso ao novo comando de **registo.**

[Estas permissões concedem acesso ao novo comando do portal de registo](./media/roles-create-custom/new-custom-role.png)

Existem duas permissões disponíveis para a concessão da capacidade de eliminar registos de aplicações:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directy/applications/delete

Concede a possibilidade de eliminar registos de aplicações independentemente do subtipo; isto é, tanto pedidos de inquilino único como multi-inquilinos.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directy/applications.myOrganization/delete

Concede a possibilidade de eliminar registos de aplicações restritos aos acessíveis apenas a contas na sua organização ou aplicações de inquilino único (subtipo myOrganization).

![Estas permissões concedem acesso ao comando de registo de aplicações Delete](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Ao atribuir uma função que contenha criar permissões, a atribuição de funções deve ser feita no âmbito do diretório. Uma autorização de criação atribuída a um âmbito de recurso não concede a capacidade de criar registos de aplicações.

### <a name="read"></a>Leitura

Todos os utilizadores membros da organização podem ler as informações de registo de aplicações por padrão. No entanto, os utilizadores convidados e os diretores de serviços de aplicação não podem. Se pretender atribuir uma função a um utilizador ou aplicação de hóspedes, deve incluir as permissões de leitura adequadas.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directy/applications/allProperties/read

Capacidade de ler todas as propriedades de aplicações de inquilino único e multi-inquilinofora de imóveis que não podem ser lidos em qualquer situação como credenciais.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directy/applications.myOrganization/allProperties/read

Concede as mesmas permissões que microsoft.directy/applications/allProperties/read, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.diretório/aplicações/proprietários/ler

Concede a possibilidade de ler propriedades de proprietários em pedidos de inquilino único e multi-inquilinos. Concede acesso a todos os campos na página dos proprietários de registos de candidaturas:

![Isto permite o acesso à página de proprietários de registo de aplicações](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.diretório/aplicações/standard/read

Concede acesso à leitura de propriedades de registo de aplicações padrão. Isto inclui propriedades em todas as páginas de inscrição de candidaturas.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directy/applications.myOrganization/standard/read

Concede as mesmas permissões que microsoft.diretório/aplicações/standard/read, mas para aplicações de um único inquilino.

### <a name="update"></a>Atualizar

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directy/applications/allProperties/update

Capacidade de atualizar todas as propriedades em aplicações de diretório único e multidiretório.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directy/applications.myOrganization/allProperties/update

Concede as mesmas permissões que microsoft.directy/applications/allProperties/update, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directy/applications/audience/update

Capacidade de atualizar a propriedade do tipo de conta suportada (signInAudience) em aplicações de diretório único e multi-directórios.

![Esta permissão permite o acesso à propriedade do tipo de conta suportada pelo registo de aplicações na página de autenticação](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directy/applications.myOrganization/audience/update

Concede as mesmas permissões que microsoft.directy/applications/audience/update, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directy/applications/autenticação/atualização

Capacidade de atualizar o URL de resposta, URL de inscrição, fluxo implícito e propriedades de domínio de editor em aplicações de inquilino único e multi-inquilinos. Concede acesso a todos os campos da página de autenticação do registo de candidaturas, exceto os tipos de conta suportada:

![Concede acesso à autenticação do registo de aplicações, mas não suportado por tipos de conta](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directy/applications.myOrganization/autenticação/atualização

Concede as mesmas permissões que microsoft.diretório/aplicações/autenticação/atualização, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.diretório/aplicações/básico/atualização

Capacidade de atualizar o nome, logotipo, URL de página inicial, termos de URL de serviço e propriedades url de declaração de privacidade em aplicações de inquilino único e multi-inquilinos. Concede acesso a todos os campos da página de marca de registo de aplicações:

![Esta permissão permite o acesso à página de marca de registo de aplicações](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directy/applications.myOrganization/basic/update

Concede as mesmas permissões que microsoft.diretório/aplicações/basic/update, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directy/applications/credentials/update

Capacidade de atualizar os certificados e propriedades de segredos de cliente em aplicações de inquilino único e multi-inquilinos. Concede acesso a todos os campos dos certificados de inscrição de aplicação & página de segredos:

![Esta permissão permite o acesso aos certificados de registo da aplicação & página de segredos](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directy/applications.myOrganization/credentials/update

Concede as mesmas permissões que microsoft.diretório/aplicações/credenciais/atualização, mas apenas para aplicações de diretório único.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.diretório/aplicações/proprietários/atualização

Capacidade de atualizar a propriedade do proprietário em inquilino único e multi-inquilino. Concede acesso a todos os campos na página dos proprietários de registos de candidaturas:

![Isto permite o acesso à página de proprietários de registo de aplicações](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directy/applications.myOrganization/owners/update

Concede as mesmas permissões que microsoft.diretório/aplicações/proprietários/atualização, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directy/applications/permissions/update

Capacidade de atualizar as permissões delegadas, permissões de candidatura, pedidos de clientes autorizados, permissões necessárias e concessão de propriedades de consentimento em pedidos de inquilino único e multi-inquilinos. Não concede a capacidade de realizar o consentimento. Concede acesso a todos os campos no registo de candidaturas Permissões API e expõe uma página da API:

![Isto permite o acesso à página de permissões de registo da aplicação API](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Esta permissão permite acesso ao registo da aplicação Expor uma página da API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directy/applications.myOrganization/permissions/update

Concede as mesmas permissões que microsoft.diretório/aplicações/permissões/atualização, mas apenas para aplicações de inquilino único.

## <a name="required-license-plan"></a>Plano de licença obrigatório

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos seguintes

- Crie funções personalizadas utilizando [o portal Azure, Azure AD PowerShell e Graph API](roles-create-custom.md)
- [Ver as atribuições para um papel personalizado](roles-view-assignments.md)

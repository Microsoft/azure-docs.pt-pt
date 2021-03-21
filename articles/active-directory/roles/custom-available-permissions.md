---
title: Permissões de funções personalizadas para registo de aplicações - Azure AD | Microsoft Docs
description: Delegar permissões de função de administrador personalizado para gerir registos de aplicações.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af2c1b912afbcf44cefbfb021c592836dbde5b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466291"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Permissões de registo de aplicativos para funções personalizadas no Azure Ative Directory

Este artigo contém as permissões de registo de aplicações atualmente disponíveis para definições de funções personalizadas no Azure Ative Directory (Azure AD).

## <a name="permissions-for-managing-single-tenant-applications"></a>Permissões para a gestão de aplicações de inquilino único

Ao escolher as permissões para o seu papel personalizado, tem a opção de conceder acesso para gerir apenas aplicações de inquilino único. As aplicações de inquilino único estão disponíveis apenas para utilizadores da organização Azure AD onde a aplicação está registada. as aplicações de inquilino único são definidas como tendo **tipos de conta suportado** definidos apenas para "Contas neste diretório organizacional.". Na API do Gráfico, as aplicações de inquilino único têm o signInAudience propriedade definida para "AzureADMyOrg".

Para conceder acesso a apenas pedidos de inquilino único, utilize as permissões abaixo com as aplicações do **subtipo.myOrganization**. Por exemplo, microsoft.directy/applications.myOrganization/basic/update.

Consulte a visão geral das [funções personalizadas](custom-overview.md) para obter uma explicação do que os termos gerais do subtipo, permissão e conjunto de propriedade significam. As seguintes informações são específicas dos registos de pedidos.

### <a name="create-and-delete"></a>Criar e eliminar

Existem duas permissões disponíveis para a concessão da capacidade de criar registos de candidaturas, cada uma com comportamentos diferentes:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.diretório/aplicações/createAsOwner

A atribuição desta permissão resulta na adição do criador como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado contará com a quota de objetos criado pelo criador.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.diretório/aplicações/criar

A atribuição desta permissão faz com que o criador não seja adicionado como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado não contará com a quota de objetos criado pelo criador. Use esta permissão cuidadosamente, porque não há nada que impeça o cessionário de criar registos de aplicações até que a quota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, esta permissão tem precedência.

Se ambas as permissões forem atribuídas, a permissão /criar terá precedência. Embora a permissão /createAsOwner não adicione automaticamente o criador como o primeiro proprietário, os proprietários podem ser especificados durante a criação do registo da aplicação quando utilizarem APIs gráficos ou cmdlets PowerShell.

Criar permissões concedem acesso ao Novo comando **de registo.**

[Estas permissões concedem acesso ao comando do Novo Portal de Registos](./media/custom-available-permissions/new-custom-role.png)

Existem duas permissões disponíveis para a concessão da possibilidade de apagar registos de aplicações:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.diretório/aplicações/delete

Concede a possibilidade de apagar registos de aplicações independentemente do subtipo; isto é, tanto aplicações de inquilino único como multi-inquilinos.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.diretório/aplicações.myOrganization/delete

Concede a possibilidade de apagar registos de aplicações restritos àqueles que são acessíveis apenas a contas na sua organização ou aplicações de inquilino único (subtipo myOrganization).

![Estas permissões concedem acesso ao comando de registo de aplicações Delete](./media/custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Ao atribuir uma função que contenha permissões de criação, a atribuição de funções deve ser feita no âmbito do diretório. Uma permissão de criação atribuída num âmbito de recursos não concede a capacidade de criar registos de aplicações.

### <a name="read"></a>Ler

Todos os utilizadores membros da organização podem ler as informações de registo de aplicações por padrão. No entanto, os utilizadores convidados e os diretores do serviço de aplicações não podem. Se pretende atribuir uma função a um utilizador ou aplicação de hóspedes, deve incluir as permissões de leitura apropriadas.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.diretório/aplicações/allProperties/read

Capacidade de ler todos os imóveis de aplicações de inquilino único e multi-inquilinos fora dos imóveis que não podem ser lidos em qualquer situação como credenciais.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.diretório/aplicações.myOrganization/allProperties/read

Concede as mesmas permissões que microsoft.diretório/aplicações/allProperties/read, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.diretório/aplicações/proprietários/ler

Concede a capacidade de ler propriedade dos proprietários em aplicações individuais de inquilinos e multi-inquilinos. Concede acesso a todos os campos na página dos proprietários do registo de candidaturas:

![Esta permissão dá acesso à página dos proprietários de registo de aplicações](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.diretório/aplicações/standard/read

Concede acesso a propriedades de registo de aplicações padrão. Isto inclui propriedades em todas as páginas de registo de candidaturas.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.diretório/aplicações.myOrganization/standard/read

Concede as mesmas permissões que microsoft.diretório/aplicações/standard/read, mas apenas para aplicações de inquilino único.

### <a name="update"></a>Atualizar

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.diretório/aplicações/allProperties/update

Capacidade de atualizar todos os imóveis em aplicações de inquilino único e multi-inquilinos.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.diretório/aplicações.myOrganization/allProperties/update

Concede as mesmas permissões que microsoft.diretório/aplicações/allProperties/update, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.diretório/aplicações/audiência/atualização

Capacidade de atualizar o tipo de conta suportada (signInAudience) em aplicações de inquilino único e multi-inquilinos.

![Esta permissão concede acesso a propriedade de tipo de conta suportada por registo de aplicações na página de autenticação](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.diretório/aplicações.myOrganization/audience/update

Concede as mesmas permissões que microsoft.diretório/aplicações/audience/update, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.diretório/aplicações/autenticação/atualização

Capacidade de atualizar o URL de resposta, URL de assinatura, fluxo implícito e propriedades de domínio de editor em aplicações de inquilino único e multi-inquilino. Concede acesso a todos os campos da página de autenticação do registo de pedidos, exceto tipos de conta suportados:

![Concede acesso à autenticação de registo de aplicações mas não suporta tipos de conta](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.diretório/aplicações.myOrganização/autenticação/atualização

Concede as mesmas permissões que microsoft.diretório/aplicações/autenticação/atualização, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.diretório/aplicações/básico/atualização

Capacidade de atualizar o nome, logotipo, URL de página inicial, termos de URL de serviço e propriedades URL de declaração de privacidade em aplicações de inquilino único e multi-inquilino. Concede acesso a todos os campos na página de marca de registo de candidaturas:

![Esta permissão dá acesso à página de marca de registo de aplicações](./media/custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.diretório/aplicações.myOrganization/basic/update

Concede as mesmas permissões que microsoft.diretório/aplicações/basic/update, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.diretório/aplicações/credenciais/atualização

Capacidade de atualizar os certificados e propriedades de segredos do cliente em aplicações de inquilino único e multi-inquilinos. Concede acesso a todos os campos nos certificados de registo de pedidos & página de segredos:

![Esta permissão concede acesso aos certificados de registo de aplicações & página de segredos](./media/custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.diretório/aplicações.myOrganization/credentials/update

Concede as mesmas permissões que microsoft.diretório/aplicações/credenciais/atualização, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.diretório/aplicações/proprietários/atualização

Capacidade de atualizar a propriedade do proprietário em inquilino único e multi-inquilino. Concede acesso a todos os campos na página dos proprietários do registo de candidaturas:

![Esta permissão dá acesso à página dos proprietários de registo de aplicações](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.diretório/aplicações.myOrganization/owners/update

Concede as mesmas permissões que microsoft.diretório/aplicações/proprietários/atualização, mas apenas para aplicações de inquilino único.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.diretório/aplicações/permissões/atualização

Capacidade de atualizar as permissões delegadas, permissões de pedidos, pedidos de cliente autorizados, permissões necessárias e conceder propriedades de consentimento em aplicações de inquilino único e multi-inquilinos. Não concede a capacidade de realizar o consentimento. Concede acesso a todos os campos nas permissões de registo de pedidos API e exponha uma página de API:

![Esta permissão concede acesso à página de permissões de registo de aplicações API](./media/custom-available-permissions/app-registration-api-permissions.png)

![Esta permissão concede acesso ao registo da aplicação Expor uma página da API](./media/custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.diretório/aplicações.myOrganization/permissions/update

Concede as mesmas permissões que microsoft.diretório/aplicações/permissões/atualização, mas apenas para aplicações de inquilino único.

## <a name="required-license-plan"></a>Plano de licença exigido

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos seguintes

- Crie funções personalizadas utilizando [o portal Azure, Azure AD PowerShell e Graph API](custom-create.md)
- [Listar atribuições de função](view-assignments.md)

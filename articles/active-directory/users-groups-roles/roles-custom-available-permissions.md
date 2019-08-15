---
title: Permissões de função de administrador personalizado para gerenciamento de registro de aplicativo-Azure Active Directory | Microsoft Docs
description: Permissões de função de administrador personalizado para delegar o gerenciamento de identidade.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f31c5928273973a9089ae9ef1fd184cdb78bbb
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033390"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Subtipos de registro de aplicativo e permissões no Azure Active Directory

Este artigo contém as permissões de registro de aplicativo disponíveis no momento para definições de função personalizadas no Azure Active Directory (Azure AD).

## <a name="single-tenant-v-multi-tenant-permissions"></a>Único locatário v. permissões de multilocatário

Permissões de função personalizada diferem para aplicativos de locatário único e de vários locatários. Os aplicativos de locatário único estão disponíveis somente para usuários na organização do Azure AD em que o aplicativo está registrado. Os aplicativos multilocatário estão disponíveis para todas as organizações do Azure AD. Os aplicativos de locatário único são definidos como tendo os **tipos de conta com suporte** definidos como "contas neste diretório organizacional somente". Na API do Graph, os aplicativos de locatário único têm a propriedade signInAudience definida como "AzureADMyOrg".

## <a name="application-registration-subtypes-and-permissions"></a>Subtipos de registro de aplicativo e permissões

Consulte a [visão geral das funções personalizadas](roles-custom-overview.md) para obter uma explicação do que significa o subtipo de termos gerais, a permissão e o conjunto de propriedades. As informações a seguir são específicas para os registros do aplicativo.

### <a name="subtypes"></a>Subtipos

Há apenas um subtipo de registro de aplicativo-Applications. MyOrganization. Por exemplo, Microsoft. Directory/Applications. MyOrganization/Basic/Update. Esse subtipo é definido na página de **autenticação** para um registro de aplicativo específico e corresponde à definição da propriedade signInAudience como "AzureADMyOrg" usando API do Graph ou PowerShell. O subtipo restringe a permissão a registros de aplicativo marcados como acessíveis somente por contas em sua organização (aplicativos de locatário único).

Você pode usar a permissão restrita para conceder permissões de leitura ou de gerenciamento a aplicativos internos somente sem conceder permissões de leitura ou de gerenciamento a aplicativos acessíveis por contas em outras organizações.

Há aplicativos. MyOrganization de todas as permissões de leitura e atualização, bem como a permissão DELETE. Não há nenhum aplicativo. a versão MyOrganization de Create neste momento. Permissões padrão (por exemplo, Microsoft. Directory/Applications/Basic/Update) concedem permissões de leitura ou gerenciamento para todos os tipos de registro de aplicativo.

![Declarar um aplicativo de locatário único ou aplicativo multilocatário](./media/roles-custom-available-permissions/supported-account-types.png)

Os detalhes das seguintes permissões para a visualização de funções personalizadas estão listados em [permissões de função personalizada disponíveis no Azure Active Directory](roles-custom-available-permissions.md).

### <a name="create-and-delete"></a>Criar e excluir

Há duas permissões disponíveis para conceder a capacidade de criar registros de aplicativo, cada um com comportamento diferente:

- **Microsoft. Directory/Applications/createAsOwner**: A atribuição dessa permissão faz com que o criador seja adicionado como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado será contabilizado na cota de objetos criados do 250 do criador.
- **Microsoft. Directory/applicationPolicies/Create**: A atribuição dessa permissão faz com que o criador não seja adicionado como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado não será contabilizado na cota de objetos criados do 250 do criador. Use essa permissão com cuidado, pois não há nada impedindo que o destinatário crie registros de aplicativo até que a cota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, essa permissão terá precedência.

Se ambas as permissões forem atribuídas, a permissão/CREATE terá precedência. Embora a permissão/createAsOwner não adicione automaticamente o criador como o primeiro proprietário, os proprietários podem ser especificados durante a criação do registro do aplicativo ao usar as APIs do Graph ou os cmdlets do PowerShell.

Criar permissões concedem acesso ao **novo** comando de registro.

[Essas permissões concedem acesso ao novo comando do portal de registro](./media/roles-create-custom/new-custom-role.png)

Há duas permissões disponíveis para conceder a capacidade de excluir registros de aplicativo:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Concede a capacidade de excluir registros de aplicativo, independentemente do subtipo; ou seja, aplicativos de locatário único e multilocatário.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. myorganizacionai/Delete

Concede a capacidade de excluir registros de aplicativo restritos àqueles que são acessíveis somente para contas em sua organização ou aplicativos de locatário único (subtipo MyOrganization).

![Essas permissões concedem acesso ao comando excluir aplicativo de registro](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Ao atribuir uma função que contém permissões de criação, a atribuição de função deve ser feita no escopo do diretório. Uma permissão de criação atribuída a um escopo de recurso não concede a capacidade de criar registros de aplicativo.

### <a name="read"></a>Leitura

Todos os usuários Membros na organização podem ler informações de registro do aplicativo por padrão. No entanto, os usuários convidados e as entidades de serviço do aplicativo não podem. Se você planeja atribuir uma função a um usuário ou aplicativo convidado, deve incluir as permissões de leitura apropriadas.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/Applications/myproperties/Read

Capacidade de ler todas as propriedades de aplicativos de locatário único e multilocatário fora de propriedades confidenciais, como credenciais.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. myorganizacionai/myproperties/Read

Concede as mesmas permissões que Microsoft. Directory/Applications/myproperties/Read, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsstandardread-grants-access-to-all-fields-on-the-application-registration-branding-page"></a>Microsoft. Directory/Applications/Standard/Read: Concede acesso a todos os campos na página de identidade visual do registro do aplicativo

![Essa permissão concede acesso à página de identidade visual do registro do aplicativo](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. myorganizacional/Standard/Read

Concede as mesmas permissões que Microsoft. Directory/Applications/Standard/Read, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/Applications/Owners/Read

Concede a capacidade de ler a propriedade Owners em aplicativos de locatário único e multilocatário. Concede acesso a todos os campos na página proprietários de registro do aplicativo:

![Essas permissões concedem acesso à página de proprietários de registro do aplicativo](./media/roles-custom-available-permissions/app-registration-owners.png)

Concede acesso às seguintes propriedades na entidade do aplicativo:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppCreatedDateTime
- AppData
- appId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

### <a name="update"></a>Atualizar

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/myproperties/Update

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. MyOrganization/Propriedade/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/myproperties/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Concede acesso a todos os campos na página de autenticação de registro do aplicativo:

![Essa permissão concede acesso à página de autenticação de registro do aplicativo](./media/roles-custom-available-permissions/supported-account-types.png)

Concede acesso às seguintes propriedades no recurso do aplicativo:

- AvailableToOtherTenants
- SignInAudience

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. MyOrganization/audiência/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/Audience/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Capacidade de atualizar a URL de resposta, a URL de saída, o fluxo implícito e as propriedades de domínio do Publicador em aplicativos de locatário único e multilocatário. Concede acesso a todos os campos na página de autenticação de registro do aplicativo, exceto tipos de conta com suporte:

![Concede acesso à autenticação de registro de aplicativo, mas aos tipos de conta sem suporte](./media/roles-custom-available-permissions/supported-account-types.png)

 Concede acesso às seguintes propriedades no recurso do aplicativo:

- AcceptMappedClaims
- AccessTokenAcceptedVersion
- Suplementos
- GroupMembershipClaims
- IsDeviceOnlyAuthSupported
- OAuth2LegacyUrlPathMatching
- OauthOidcResponsePolicyBitmap
- OptionalClaims
- OrgRestrictions
- PublicClient
- UseCustomTokenSigningKey

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. MyOrganization/autenticação/atualização

Concede as mesmas permissões que Microsoft. Directory/Applications/Authentication/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft. Directory/Applications/Basic/Update

Capacidade de atualizar o nome, o logotipo, a URL da Home Page, a URL dos termos de serviço e as propriedades de URL da política de privacidade em aplicativos de locatário único e multilocatário. Concede acesso a todos os campos na página de identidade visual do registro do aplicativo:

![Essa permissão concede acesso à página de identidade visual do registro do aplicativo](./media/roles-custom-available-permissions/app-registration-branding.png)

Concede acesso às seguintes propriedades no recurso do aplicativo:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppData
- appId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. MyOrganization/Basic/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/Basic/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Capacidade de atualizar as propriedades de certificados e segredos de cliente em aplicativos de locatário único e multilocatário. Concede acesso a todos os campos na página certificados de registro de aplicativo & segredos:

![Essa permissão concede acesso à página de certificados de registro de aplicativo & segredos](./media/roles-custom-available-permissions/app-registration-secrets.png)

Concede acesso às seguintes propriedades no recurso do aplicativo:
- AsymmetricKey
- EncryptedSecretKey
- Descrição da
- SharedKeyReference
- TokenEncryptionKeyId

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. MyOrganization/Credentials/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/Credentials/Update, mas somente para aplicativos de diretório único.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Capacidade de atualizar a propriedade de proprietário em um locatário único e multilocatário. Concede acesso a todos os campos na página proprietários de registro do aplicativo:

![Essas permissões concedem acesso à página de proprietários de registro do aplicativo](./media/roles-custom-available-permissions/app-registration-owners.png)

Concede acesso às seguintes propriedades no recurso do aplicativo:
- Proprietários

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. MyOrganization/Owners/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/Owners/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>Microsoft. Directory/Applications/Permissions/Update

Capacidade de atualizar as permissões delegadas, permissões de aplicativo, aplicativos cliente autorizados, permissões necessárias e conceder Propriedades de consentimento em aplicativos de locatário único e multilocatário. Não concede a capacidade de executar o consentimento. Concede acesso a todos os campos nas permissões da API de registro de aplicativo e expõe uma página de API:

![Essas permissões concedem acesso à página de permissões da API de registro do aplicativo](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Essa permissão concede acesso ao registro do aplicativo expor uma página de API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

Concede acesso às seguintes propriedades no recurso do aplicativo:

- AppIdentifierUri
- Elegibilidade
- PreAuthorizedApplications
- RecordConsentConditions
- RequiredResourceAccess

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. MyOrganization/Permissions/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/Permissions/Update, mas somente para aplicativos de locatário único.

## <a name="required-license-plan"></a>Plano de licença necessário

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos Seguintes

- Criar funções personalizadas usando [o portal do Azure, o PowerShell do Azure AD e o API do Graph](roles-create-custom.md)
- [Exibir as atribuições para uma função personalizada](roles-view-assignments.md)

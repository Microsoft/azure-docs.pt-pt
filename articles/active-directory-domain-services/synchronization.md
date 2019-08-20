---
title: 'Azure Active Directory Domain Services: Sincronização em domínios gerenciados | Microsoft Docs'
description: Entender a sincronização em um domínio gerenciado Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 1c52ac967d241f31d96988fa5ead8b4e049f6f4c
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617108"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronização em um domínio gerenciado Azure AD Domain Services
O diagrama a seguir ilustra como a sincronização funciona em Azure AD Domain Services domínios gerenciados.

![Sincronização no Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronização do seu diretório local para seu locatário do Azure AD
Azure AD Connect sincronização é usada para sincronizar contas de usuário, associações de grupo e hashes de credenciais para seu locatário do Azure AD. Os atributos de contas de usuário, como o UPN e o SID local (identificador de segurança), são sincronizados. Se você usar Azure AD Domain Services, os hashes de credenciais herdados necessários para a autenticação NTLM e Kerberos também serão sincronizados com o locatário do Azure AD.

Se você configurar o Write-back, as alterações que ocorrerem em seu diretório do Azure AD serão sincronizadas de volta para sua Active Directory local. Por exemplo, se você alterar sua senha usando o gerenciamento de senhas de autoatendimento do Azure AD, a senha alterada será atualizada no domínio do AD local.

> [!NOTE]
> Sempre use a versão mais recente do Azure AD Connect para garantir que você tenha correções para todos os bugs conhecidos.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronização do seu locatário do Azure AD para seu domínio gerenciado
As contas de usuário, as associações de grupo e os hashes de credenciais são sincronizados do seu locatário do Azure AD para seu Azure AD Domain Services domínio gerenciado. Esse processo de sincronização é automático. Você não precisa configurar, monitorar ou gerenciar esse processo de sincronização. A sincronização inicial pode levar de algumas horas a alguns dias, dependendo do número de objetos no seu diretório do Azure AD. Após a conclusão da sincronização inicial, levará cerca de 20-30 minutos para que as alterações feitas no Azure AD sejam atualizadas em seu domínio gerenciado. Esse intervalo de sincronização se aplica a alterações de senha ou alterações em atributos feitos no Azure AD.

O processo de sincronização também é unidirecional/unidirecional por natureza. O domínio gerenciado é amplamente somente leitura, exceto para UOs personalizadas que você criar. Portanto, você não pode fazer alterações em atributos de usuário, senhas de usuário ou associações de grupo no domínio gerenciado. Como resultado, não há nenhuma sincronização reversa de alterações do seu domínio gerenciado de volta para seu locatário do Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização de um ambiente local de várias florestas
Muitas organizações têm uma infraestrutura de identidade local bastante complexa que consiste em várias florestas de contas. O Azure AD Connect dá suporte à sincronização de usuários, grupos e hashes de credenciais de ambientes de várias florestas para seu locatário do Azure AD.

Em contraste, seu locatário do Azure AD é um namespace muito mais simples e simples. Para permitir que os usuários acessem aplicativos protegidos pelo Azure AD de maneira confiável, resolva conflitos de UPN em contas de usuário em diferentes florestas. Seu Azure AD Domain Services domínio gerenciado tem uma aparência próxima do seu locatário do Azure AD. Você verá uma estrutura de UO simples em seu domínio gerenciado. Todas as contas de usuário e grupos são armazenados no contêiner ' usuários do AADDC ', apesar de serem sincronizados de diferentes domínios ou florestas locais. Você pode ter configurado uma estrutura de UO hierárquica local. O domínio gerenciado ainda tem uma estrutura de UO simples.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusões-o que não está sincronizado com o domínio gerenciado
Os seguintes objetos ou atributos não são sincronizados com seu locatário do Azure AD ou com seu domínio gerenciado:

* **Atributos excluídos:** Você pode optar por excluir determinados atributos da sincronização para o locatário do Azure AD do seu domínio local usando Azure AD Connect. Esses atributos excluídos não estão disponíveis em seu domínio gerenciado.
* **Políticas de Grupo:** As políticas de grupo configuradas no seu domínio local não são sincronizadas com o domínio gerenciado.
* **Compartilhamento de SYSVOL:** Da mesma forma, o conteúdo do compartilhamento SYSVOL no seu domínio local não é sincronizado com o domínio gerenciado.
* **Objetos de computador:** Objetos de computador para computadores ingressados em seu domínio local não são sincronizados com o domínio gerenciado. Esses computadores não têm uma relação de confiança com seu domínio gerenciado e pertencem somente ao seu domínio local. Em seu domínio gerenciado, você encontra objetos de computador somente para computadores que ingressaram explicitamente no domínio gerenciado.
* **Atributos de SidHistory para usuários e grupos:** O usuário primário e os SIDs do grupo primário do seu domínio local são sincronizados com o domínio gerenciado. No entanto, os atributos SidHistory existentes para usuários e grupos não são sincronizados do seu domínio local para o domínio gerenciado.
* **Estruturas de UO (unidades organizacionais):** As unidades organizacionais definidas no seu domínio local não são sincronizadas com o domínio gerenciado. Há duas UOs internas em seu domínio gerenciado. Por padrão, o domínio gerenciado tem uma estrutura de UO simples. No entanto, você pode optar por [criar uma UO personalizada em seu domínio gerenciado](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Como os atributos específicos são sincronizados com o domínio gerenciado
A tabela a seguir lista alguns atributos comuns e descreve como eles são sincronizados com o domínio gerenciado.

| Atributo em seu domínio gerenciado | Source | Notas |
|:--- |:--- |:--- |
| UPN |Atributo UPN do usuário no seu locatário do Azure AD |O atributo UPN do seu locatário do Azure AD é sincronizado como está em seu domínio gerenciado. Portanto, a maneira mais confiável de entrar em seu domínio gerenciado é usar seu UPN. |
| SAMAccountName |Atributo mailNickname do usuário em seu locatário do Azure AD ou gerado automaticamente |O atributo SAMAccountName é originado do atributo mailNickname em seu locatário do Azure AD. Se várias contas de usuário tiverem o mesmo atributo mailNickname, o SAMAccountName será gerado automaticamente. Se o prefixo de mailNickname ou UPN do usuário tiver mais de 20 caracteres, o SAMAccountName será gerado automaticamente para atender ao limite de 20 caracteres em atributos SAMAccountName. |
| Palavras-passe |Senha do usuário do seu locatário do Azure AD |Os hashes de credenciais necessários para a autenticação NTLM ou Kerberos (também chamadas de credenciais complementares) são sincronizados do seu locatário do Azure AD. Se o seu locatário do Azure AD for um locatário sincronizado, essas credenciais serão originadas do seu domínio local. |
| SID do grupo/usuário primário |Gerado automaticamente |O SID principal para contas de usuário/grupo é gerado automaticamente em seu domínio gerenciado. Esse atributo não corresponde ao SID do grupo/usuário primário do objeto em seu domínio do AD local. Essa incompatibilidade ocorre porque o domínio gerenciado tem um namespace SID diferente do seu domínio local. |
| Histórico de SID para usuários e grupos |SID de grupo e usuário primário local |O atributo SidHistory para usuários e grupos em seu domínio gerenciado é definido para corresponder ao SID de grupo ou usuário primário correspondente no seu domínio local. Esse recurso ajuda a tornar mais fácil a migração de aplicativos locais para o domínio gerenciado, pois você não precisa de recursos de Nova ACL. |

> [!NOTE]
> **Entre no domínio gerenciado usando o formato UPN:** O atributo SAMAccountName pode ser gerado automaticamente para algumas contas de usuário em seu domínio gerenciado. Se vários usuários tiverem o mesmo atributo mailNickname ou os usuários tiverem prefixos UPN muito longos, o SAMAccountName para esses usuários poderá ser gerado automaticamente. Portanto, o formato SAMAccountName (por exemplo, ' CONTOSO\dee ') nem sempre é uma maneira confiável de entrar no domínio. O SAMAccountName gerado automaticamente pelos usuários pode ser diferente do prefixo UPN. Use o formato UPN (por exemplo, 'dee@contoso.com') para entrar no domínio gerenciado de forma confiável.

### <a name="attribute-mapping-for-user-accounts"></a>Mapeamento de atributo para contas de usuário
A tabela a seguir ilustra como os atributos específicos para objetos de usuário em seu locatário do Azure AD são sincronizados com os atributos correspondentes em seu domínio gerenciado.

| Atributo de usuário em seu locatário do Azure AD | Atributo de usuário em seu domínio gerenciado |
|:--- |:--- |
| accountEnabled |userAccountControl (define ou limpa o bit ACCOUNT_DISABLED) |
| city |l |
| país |Co |
| Departamento |Departamento |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |título |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (às vezes pode ser gerado automaticamente) |
| móvel |móvel |
| ObjectID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (define ou limpa o bit DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |St |
| streetAddress |streetAddress |
| Apelido |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapeamento de atributo para grupos
A tabela a seguir ilustra como os atributos específicos para objetos de grupo no seu locatário do Azure AD são sincronizados com os atributos correspondentes em seu domínio gerenciado.

| Atributo de grupo em seu locatário do Azure AD | Atributo de grupo em seu domínio gerenciado |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (às vezes pode ser gerado automaticamente) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| ObjectID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Considerações de segurança e sincronização de hash de senha
Quando você habilita Azure AD Domain Services, seu diretório do AD do Azure gera e armazena os hashes de senha no NTLM & formatos compatíveis com Kerberos. 

Para contas de usuário de nuvem existentes, como o Azure AD nunca armazena suas senhas de texto não criptografado, esses hashes não podem ser gerados automaticamente. Portanto, a Microsoft exige [que os usuários de nuvem redefinam/alterem suas senhas](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que seus hashes de senha sejam gerados e armazenados no Azure AD. Para qualquer conta de usuário de nuvem criada no Azure AD depois de habilitar a Azure AD Domain Services, os hashes de senha são gerados e armazenados nos formatos compatíveis com NTLM e Kerberos. 

Para contas de usuário sincronizadas do AD local usando Azure AD Connect sincronização, você precisa [configurar Azure ad Connect para sincronizar hashes de senha nos formatos compatíveis com NTLM e Kerberos](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Os hashes de senha compatíveis com NTLM e Kerberos são sempre armazenados de forma criptografada no Azure AD. Esses hashes são criptografados de modo que somente Azure AD Domain Services tenha acesso às chaves de descriptografia. Nenhum outro serviço ou componente no Azure AD tem acesso às chaves de descriptografia. As chaves de criptografia são exclusivas por locatário do Azure AD. Azure AD Domain Services sincroniza os hashes de senha nos controladores de domínio para seu domínio gerenciado. Esses hashes de senha são armazenados e protegidos nesses controladores de domínio semelhantes a como as senhas são armazenadas e protegidas nos controladores de domínio do AD do Windows Server. Os discos para esses controladores de domínio gerenciados são criptografados em repouso.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objetos que não são sincronizados com o locatário do Azure AD por meio do seu domínio gerenciado
Conforme descrito em uma seção anterior deste artigo, não há sincronização do seu domínio gerenciado de volta para seu locatário do Azure AD. Você pode optar por [criar uma UO (unidade organizacional) personalizada](create-ou.md) em seu domínio gerenciado. Além disso, você pode criar outras UOs, usuários, grupos ou contas de serviço dentro dessas UOs personalizadas. Nenhum dos objetos criados em UOs personalizadas é sincronizado de volta para seu locatário do Azure AD. Esses objetos estão disponíveis para uso somente dentro de seu domínio gerenciado. Portanto, esses objetos não são visíveis usando os cmdlets do PowerShell do Azure AD, o Azure AD API do Graph ou usando a interface do usuário de gerenciamento do Azure AD.

## <a name="related-content"></a>Conteúdo relacionado
* [Cenários de implantação-Azure AD Domain Services](scenarios.md)
* [Considerações de rede para Azure AD Domain Services](network-considerations.md)
* [Introdução ao Azure AD Domain Services](tutorial-create-instance.md)

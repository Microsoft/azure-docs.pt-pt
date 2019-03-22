---
title: 'Azure Active Directory Domain Services: Sincronização em domínios geridos | Documentos da Microsoft'
description: Compreender a sincronização num domínio gerido do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: b5544be37015368173f3f8eb39288b7a97ae11c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085002"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronização num domínio gerido do Azure AD Domain Services
O diagrama seguinte ilustra como funciona a sincronização no Azure AD Domain Services domínios geridos.

![Sincronização no Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronização do diretório no local com o seu inquilino do Azure AD
Sincronização do Azure AD Connect é utilizada para sincronizar contas de utilizador, associações de grupo e hashes de credencial com o seu inquilino do Azure AD. Atributos de utilizador de contas, como o UPN e o SID (identificador de segurança) são sincronizados no local. Se utilizar o Azure AD Domain Services, os hashes de credencial herdados necessários para autenticação NTLM e Kerberos também são sincronizados com o seu inquilino do Azure AD.

Se configurar a repetição de escrita, alterações que ocorrem no diretório do Azure AD são sincronizadas com o Active Directory no local. Por exemplo, se alterar a palavra-passe com a gestão de palavras-passe self-service do Azure AD, a palavra-passe alterada é atualizado no seu local domínio do AD.

> [!NOTE]
> Utilize sempre a versão mais recente do Azure AD Connect para garantir que tenha correções para todos os bugs conhecidos.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronização a partir do seu inquilino do Azure AD ao seu domínio gerido
Contas de utilizador, as associações de grupo e hashes de credenciais são sincronizados a partir do seu inquilino do Azure AD ao seu domínio gerido do Azure AD Domain Services. Este processo de sincronização é automático. Não é necessário configurar, monitorizar ou gere este processo de sincronização. A sincronização inicial poderá demorar algumas horas para alguns dias, dependendo do número de objetos no diretório do Azure AD. Depois de concluída a sincronização inicial, demora cerca de 20 a 30 minutos para as alterações efetuadas no Azure AD para ser atualizado no seu domínio gerido. Este intervalo de sincronização aplica-se às alterações de palavra-passe ou alterações aos atributos feitos no Azure AD.

O processo de sincronização também é uma-way/unidirecionais por natureza. O domínio gerido está amplamente só de leitura, exceto para quaisquer UOs personalizadas que cria. Por conseguinte, não pode efetuar alterações a atributos de utilizador, palavras-passe de utilizador ou associações de grupo no domínio gerido. Como resultado, não há nenhuma sincronização inversa de alterações do seu domínio gerido para o inquilino do Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização de um ambiente de várias florestas no local
Muitas organizações têm uma infraestrutura de identidade bastante complexa no local, consistindo em várias florestas de contas. Azure AD Connect suporta a sincronização de utilizadores, grupos e hashes de credenciais de ambientes multi-floresta ao seu inquilino do Azure AD.

Por outro lado, o inquilino do Azure AD é muito espaço de nomes mais simples e simples. Para permitir aos utilizadores aceder com fiabilidade aos aplicativos a serem protegidos pelo Azure AD, resolva conflitos UPN em contas de utilizador em florestas diferentes. Sua ursos de domínio gerido do Azure AD Domain Services fechar bastante semelhante ao inquilino do Azure AD. Verá uma estrutura de UO simples no seu domínio gerido. Todas as contas de utilizador e grupos são armazenados no contentor "Utilizadores do aad DC", apesar de que está a ser sincronizados a partir de diferentes domínios no local ou florestas. Poderá ter configurado uma UO hierárquica estruturar no local. O domínio gerido ainda tem uma estrutura de UO simples simple.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusões - o que não está sincronizado com o seu domínio gerido
Os seguintes objetos ou atributos não estão sincronizados com o seu inquilino do Azure AD ou ao seu domínio gerido:

* **Atributos excluídos:** Pode optar por excluir determinados atributos a sincronizar com o inquilino do Azure AD do seu domínio no local com o Azure AD Connect. Esses atributos excluídos não estão disponíveis no seu domínio gerido.
* **Políticas de grupo:** Políticas de grupo configuradas no seu domínio no local não estão sincronizadas com o seu domínio gerido.
* **Partilha de SYSVOL:** Da mesma forma, os conteúdos da partilha Sysvol no seu domínio no local não estão sincronizados com o seu domínio gerido.
* **Objetos de computador:** Objetos de computador para computadores associados ao seu domínio no local não estão sincronizados com o seu domínio gerido. Estes computadores não têm uma relação de confiança com o seu domínio gerido e pertencem ao seu domínio no local apenas. No seu domínio gerido, encontrar os objetos de computador apenas para computadores que tê explicitamente associado a um domínio para o domínio gerido.
* **Atributos de SidHistory para utilizadores e grupos:** O utilizador primário e o grupo primário SIDs do seu domínio no local são sincronizados com o seu domínio gerido. No entanto, os atributos de SidHistory existentes para utilizadores e grupos não são sincronizados do seu domínio no local seu domínio gerido.
* **Estruturas de unidades (UO) da organização:** Não sincronizar a unidades organizacionais definidas no seu domínio no local seu domínio gerido. Existem duas UOs incorporadas no seu domínio gerido. Por predefinição, o seu domínio gerido tem uma estrutura de UO simples. No entanto pode optar por [criar uma UO personalizada no seu domínio gerido](active-directory-ds-admin-guide-create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Como atributos específicos são sincronizados com o seu domínio gerido
A tabela seguinte lista alguns atributos comuns e descreve como eles são sincronizados com o seu domínio gerido.

| Atributo no seu domínio gerido | Origem | Notas |
|:--- |:--- |:--- |
| UPN |Atributo UPN do utilizador no inquilino do Azure AD |O atributo UPN no seu inquilino do Azure AD é sincronizado como está ao seu domínio gerido. Por conseguinte, a forma mais fiável para iniciar sessão no seu domínio gerido está a utilizar o UPN. |
| SAMAccountName |MailNickname do usuário de atributo no seu inquilino do Azure AD ou gerado automaticamente |O atributo SAMAccountName é obtido a partir do atributo mailNickname no seu inquilino do Azure AD. Se várias contas de utilizador tem o mesmo atributo mailNickname, SAMAccountName é gerado automaticamente. Se mailNickname ou prefixo UPN do utilizador tiver mais de 20 carateres, SAMAccountName é gerado automaticamente para satisfazer o limite de 20 carateres nos atributos de SAMAccountName. |
| Palavras-passe |Senha de usuário do seu inquilino do Azure AD |Os hashes de credenciais necessários para a autenticação NTLM ou Kerberos (também chamada de credenciais suplementares) são sincronizados a partir do seu inquilino do Azure AD. Se o seu inquilino do Azure AD é um inquilino sincronizado, estas credenciais são originadas de seu domínio no local. |
| Utilizador/SID primário do grupo |Gerado automaticamente |O SID primário para contas de utilizador/grupo é gerado automaticamente no seu domínio gerido. Este atributo não coincide com o utilizador/SID primário do grupo do objeto no seu local de domínio do AD. Este erro de correspondência é porque o domínio gerido com um espaço de nomes de SID diferente do que o seu domínio no local. |
| Histórico de SIDs para utilizadores e grupos |Utilizador primário no local e o SID do grupo |O atributo de SidHistory para utilizadores e grupos no seu domínio gerido está definido para corresponder o utilizador primário correspondente ou o grupo SID no seu domínio no local. Esta funcionalidade ajuda a facilitar a migração lift-and-shift de aplicações no local para o domínio gerido, uma vez que não é necessário a recursos de re-ACL. |

> [!NOTE]
> **Inicie sessão no domínio gerido com o formato UPN:** O atributo SAMAccountName pode ser gerado automaticamente para algumas contas de utilizador no seu domínio gerido. Se vários usuários têm o mesmo atributo mailNickname ou os utilizadores têm prefixos UPN demasiado longos, SAMAccountName para estes utilizadores pode ser geradas automaticamente. Por conseguinte, o formato de SAMAccountName (por exemplo, "CONTOSO100\joeuser') nem sempre é uma forma fiável para iniciar sessão domínio. SAMAccountName de gerado automaticamente dos utilizadores pode ser diferente do seu prefixo UPN. Utilize o formato UPN (por exemplo, "joeuser@contoso100.com") para iniciar sessão no domínio gerido com confiança.

### <a name="attribute-mapping-for-user-accounts"></a>Mapeamento do atributo para contas de utilizador
A tabela seguinte ilustra os atributos como específicos para os objetos no inquilino do Azure AD são sincronizados com atributos correspondentes no seu domínio gerido do utilizador.

| Atributo de utilizador no inquilino do Azure AD | Atributo de utilizador no seu domínio gerido |
|:--- |:--- |
| accountEnabled |userAccountControl (define ou limpa ACCOUNT_DISABLED bit) |
| city |l |
| País |Co |
| Departamento |Departamento |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |título |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (pode por vezes, ser gerado automaticamente) |
| móvel |móvel |
| ObjectId |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (define ou limpa DONT_EXPIRE_PASSWORD bit) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |St |
| streetAddress |streetAddress |
| Apelido |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapeamento de atributo para grupos
A tabela seguinte ilustra os atributos como específicos para o grupo de objetos no inquilino do Azure AD são sincronizados com atributos correspondentes no seu domínio gerido.

| Atributo de grupo no seu inquilino do Azure AD | Atributo de grupo no seu domínio gerido |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (pode por vezes, ser gerado automaticamente) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| ObjectId |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Considerações de segurança e de sincronização de hash de palavra-passe
Quando ativar os serviços de domínio do Azure AD, o diretório do Azure AD gera e armazena hashes de palavra-passe em formatos compatíveis, NTLM e Kerberos. 

Para contas de utilizador de cloud existentes, uma vez que o Azure AD nunca armazena as palavras-passe de texto não encriptado, estes hashes não podem ser gerados automaticamente. Por conseguinte, a Microsoft requer [-os utilizadores da nuvem para repor as palavras-passe/alteração de](active-directory-ds-getting-started-password-sync.md) para que os hashes de palavra-passe ser gerado e armazenado no Azure AD. Para qualquer conta de utilizador na cloud criada no Azure AD depois de ativar o Azure AD Domain Services, os hashes de palavra-passe são gerados e armazenados nos formatos compatíveis NTLM e Kerberos. 

Para contas de utilizador sincronizado a partir do AD com o Azure AD Connect Sync, no local terá [configurar o Azure AD Connect para sincronizar os hashes de palavra-passe nos formatos compatíveis NTLM e Kerberos](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Os hashes de palavra-passe compatível NTLM e Kerberos são sempre armazenados de forma encriptada no Azure AD. Estes hashes são encriptados de forma a que apenas os serviços de domínio do Azure AD tem acesso às chaves de desencriptação. Nenhum outro serviço ou componente no Azure AD tem acesso às chaves de desencriptação. As chaves de encriptação são único inquilino do AD pelo Azure. O Azure AD Domain Services sincroniza os hashes de palavra-passe para os controladores de domínio para o seu domínio gerido. Estes hashes de palavra-passe são armazenados e protegidos nestes controladores de domínio semelhantes a como palavras-passe estão armazenadas e protegidas em controladores de domínio do Windows Server AD. Os discos para estes controladores de domínio geridos são encriptados em inatividade.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objetos que não estão sincronizados com o seu inquilino do Azure AD do seu domínio gerido
Conforme descrito numa seção anterior deste artigo, não há nenhuma sincronização a partir do seu domínio gerido para o inquilino do Azure AD. Pode optar por [criar uma unidade organizacional personalizado (UO)](active-directory-ds-admin-guide-create-ou.md) no seu domínio gerido. Além disso, pode criar outras UOs, utilizadores, grupos ou contas de serviço nesses UOs personalizadas. Nenhum dos objetos criados dentro de UOs personalizadas são sincronizados de volta para o inquilino do Azure AD. Esses objetos estão disponíveis para utilização apenas dentro de seu domínio gerido. Por conseguinte, esses objetos não são visíveis utilizando cmdlets do PowerShell do Azure AD, Azure AD Graph API ou utilizando a IU de gestão do Azure AD.

## <a name="related-content"></a>Conteúdo relacionado
* [Recursos - dos serviços de domínio do Azure AD](active-directory-ds-features.md)
* [Cenários de implementação - Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Considerações de redes para o Azure AD Domain Services](active-directory-ds-networking.md)
* [Introdução ao Azure AD Domain Services](active-directory-ds-getting-started.md)

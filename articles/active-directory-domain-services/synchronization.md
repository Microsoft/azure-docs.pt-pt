---
title: Como funciona a sincronização nos Serviços de Domínio Azure AD Microsoft Docs
description: Saiba como funciona o processo de sincronização para objetos e credenciais de um inquilino AD AZure ou no local Ative Directory Domain Services ambiente para um domínio gerido por Azure Ative Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 10eec1527fb0ac5109822da398642613219771f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039845"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>Como os objetos e credenciais são sincronizados num domínio gerido por Serviços de Domínio do Diretório Ativo Azure

Objetos e credenciais num domínio gerido por Azure Ative Directory Domain Services (Azure AD DS) podem ser criados localmente dentro do domínio, ou sincronizados a partir de um inquilino do Azure Ative Directory (Azure AD). Quando implementa pela primeira vez O AD DS, uma sincronização automática de sentido único é configurada e começou a replicar os objetos do Azure AD. Esta sincronização unidirecionais continua a ser executada em segundo plano para manter o domínio gerido Azure AD DS atualizado com quaisquer alterações a partir de Azure AD. Não ocorre sincronização desde Azure AD DS até Azure AD.

Num ambiente híbrido, objetos e credenciais de um domínio AD DS no local podem ser sincronizados até Azure AD usando Azure AD Connect. Uma vez que esses objetos são sincronizados com sucesso para Azure AD, a sincronização automática de fundo torna esses objetos e credenciais disponíveis para aplicações usando o domínio gerido.

O seguinte diagrama ilustra como funciona a sincronização entre a Azure AD DS, Azure AD e um ambiente opcional no local AD DS:

![Visão geral da sincronização para um domínio gerido por serviços de domínio AD AD Azure](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Sincronização de Azure AD a Azure AD DS

As contas de utilizador, os membros do grupo e as hashes credenciais são sincronizadas de uma forma de Azure AD a Azure AD DS. Este processo de sincronização é automático. Não é necessário configurar, monitorizar ou gerir este processo de sincronização. A sincronização inicial pode demorar algumas horas a alguns dias, dependendo do número de objetos no diretório AD Azure. Após a sincronização inicial estar concluída, as alterações que são feitas em AD Azure, tais como alterações de palavras-passe ou atributos, são automaticamente sincronizadas para Azure AD DS.

Quando um utilizador é criado em Azure AD, não são sincronizados com Azure AD DS até que alterem a sua palavra-passe em Azure AD. Este processo de alteração de palavra-passe faz com que os hashes de palavra-passe para a autenticação Kerberos e NTLM sejam gerados e armazenados em Azure AD. Os hashes de palavra-passe são necessários para autenticar com sucesso um utilizador em Azure AD DS.

O processo de sincronização é uma forma/unidirecional por design. Não há sincronização inversa das alterações do Azure AD DS de volta para Azure AD. Um domínio gerido é em grande parte apenas leitura, exceto para OUs personalizado que você pode criar. Não é possível esprodução de alterações nos atributos do utilizador, palavras-passe do utilizador ou membros do grupo dentro de um domínio gerido.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Atribuir sincronização e mapeamento ao Azure AD DS

A tabela que se segue lista alguns atributos comuns e como são sincronizados com Azure AD DS.

| Atributo em Azure AD DS | Origem | Notas |
|:--- |:--- |:--- |
| UPN | Atributo *UPN* do utilizador no inquilino AD AZure | O atributo UPN do inquilino AD AD É sincronizado como é para Azure AD DS. A forma mais fiável de entrar num domínio gerido é usar a UPN. |
| SAMAccountName | E-mail do utilizador *Atributo De nome de* utilizador no inquilino AD AZure ou autogerido | O atributo *SAMAccountName* é obtido a partir do *atributo MailNickname* no inquilino AZure AD. Se várias contas de utilizador tiverem o mesmo *atributo de correio,* o *NOME SAMAccount* é autogerido. Se o *prefixo do utilizador por correio Nickname* ou *UPN* for superior a 20 caracteres, o *SAMAccountName* é autogerido para cumprir o limite de 20 caracteres nos atributos *SAMAccountName.* |
| Palavras-passe | Palavra-passe do utilizador do inquilino AZure AD | As hashes de senha legacy necessárias para a autenticação NTLM ou Kerberos são sincronizadas pelo inquilino Azure AD. Se o inquilino AZURE AD estiver configurado para sincronização híbrida utilizando o Azure AD Connect, estes hashes de palavra-passe são provenientes do ambiente AD DS no local. |
| UTILIZADOR/grupo PRIMÁRIO SID | Autogerado | O SID primário para contas de utilizador/grupo é autogerido em Azure AD DS. Este atributo não corresponde ao SID do utilizador/grupo primário do objeto num ambiente AD DS no local. Esta incompatibilidade é porque o domínio gerido tem um espaço de nome SID diferente do domínio AD DS no local. |
| Histórico sid para utilizadores e grupos | No local, o utilizador primário e o grupo SID do grupo SID | O atributo *SidHistory* para utilizadores e grupos em Azure AD DS está definido para corresponder ao utilizador primário ou grupo SID correspondente num ambiente AD DS no local. Esta funcionalidade ajuda a facilitar o levantamento e a mudança de aplicações no local para Azure AD DS, uma vez que não precisa de re-recursos ACL. |

> [!TIP]
> **Inscreva-se no domínio gerido utilizando o formato UPN** O atributo *SAMAccountName,* por `AADDSCONTOSO\driley` exemplo, pode ser gerado automaticamente para algumas contas de utilizador num domínio gerido. O *SAMAccountName* gerado automaticamente pode diferir do seu prefixo UPN, pelo que nem sempre é uma forma fiável de iniciar scontabilidade.
>
> Por exemplo, se vários utilizadores tiverem o mesmo *atributo de correio Ou* os utilizadores utilizadores tiverem prefixos UPN demasiado longos, o *SAMAccountName* para estes utilizadores pode ser gerado automaticamente. Utilize o formato UPN, `driley@aaddscontoso.com` como, para iniciar súm numa área gerida.

### <a name="attribute-mapping-for-user-accounts"></a>Mapeamento de atributos para contas de utilizador

A tabela a seguir ilustra como os atributos específicos para objetos do utilizador em Azure AD são sincronizados com os atributos correspondentes em Azure AD DS.

| Atributo do utilizador em Azure AD | Atributo do utilizador em Azure AD DS |
|:--- |:--- |
| accountEnabled |userAccountControl (define ou limpa a ACCOUNT_DISABLED bit) |
| city |l |
| país |co |
| departamento |departamento |
| displayName |displayName |
| empregadodid |employeeId |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| nomeDado |nomeDado |
| jobTitle |título |
| correio |correio |
| mailSemame |msds-AzureADMailNickname |
| mailSemame |SAMAccountName (às vezes pode ser autogerado) |
| gestor |gestor |
| dispositivo móvel |dispositivo móvel |
| objectid |msds-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolícias |userAccountControl (define ou limpa a DONT_EXPIRE_PASSWORD bit) |
| físicoDeliveryOfficeName |físicoDeliveryOfficeName |
| código postal |código postal |
| preferiuLanguage |preferiuLanguage |
| proxyAddresses | proxyAddresses |
| state |SC |
| streetAddress |streetAddress |
| surname |sn |
| número de telefone |número de telefone |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapeamento de atributos para grupos

A tabela a seguir ilustra como os atributos específicos para objetos de grupo em Azure AD são sincronizados com os atributos correspondentes em Azure AD DS.

| Atributo de grupo em Azure AD | Atributo de grupo em Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (às vezes pode ser autogerado) |
| correio |correio |
| mailSemame |msds-AzureADMailNickname |
| objectid |msds-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| proxyAddresses | proxyAddresses |
| securityEnabled |tipodeGrupo |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Sincronização de AD DS nas instalações para Azure AD e Azure AD DS

O Azure AD Connect é utilizado para sincronizar contas de utilizadores, membros do grupo e hashes credenciais de um ambiente AD DS no local para Azure AD. Os atributos de contas de utilizador, tais como o UPN e o identificador de segurança no local (SID) são sincronizados. Para iniciar sação usando Azure AD DS, as hashes de senha legado necessárias para a autenticação NTLM e Kerberos também são sincronizadas para Azure AD.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio gerido para sincronizar objetos de volta ao AZure AD.

Se configurar o write-back, as alterações a partir do Azure AD são sincronizadas de volta ao ambiente AD DS no local. Por exemplo, se um utilizador alterar a sua palavra-passe utilizando a gestão da palavra-passe de autosserviço Azure AD, a palavra-passe é atualizada no ambiente AD DS no local.

> [!NOTE]
> Utilize sempre a versão mais recente do Azure AD Connect para garantir que tem correções para todos os bugs conhecidos.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização a partir de um ambiente multi-floresta nas instalações

Muitas organizações têm um ambiente DS AD bastante complexo que inclui múltiplas florestas. O Azure AD Connect suporta utilizadores sincronizados, grupos e hashes credenciais de ambientes multi-florestais para Azure AD.

A AZure AD tem um espaço de nome muito mais simples e plano. Para permitir aos utilizadores acederem de forma fiável a aplicações protegidas pela Azure AD, resolva conflitos de UPN em todas as contas de utilizadores em diferentes florestas. Os domínios geridos utilizam uma estrutura plana da U, semelhante à Azure AD. Todas as contas e grupos de utilizadores são armazenados no contentor *AADDC Users,* apesar de serem sincronizados de diferentes domínios ou florestas no local, mesmo que tenha configurado uma estrutura hierárquica da OU no local. O domínio gerido achata quaisquer estruturas hierárquicas da O.

Como previamente detalhado, não há sincronização do Azure AD DS de volta ao Azure AD. Pode [criar uma Unidade Organizacional Personalizada (OU)](create-ou.md) em Azure AD DS e, em seguida, utilizadores, grupos ou contas de serviço dentro dessas OUs personalizadas. Nenhum dos objetos criados em OUs personalizados é sincronizado de volta ao Azure AD. Estes objetos estão disponíveis apenas dentro do domínio gerido, e não são visíveis usando cmdlets Azure AD PowerShell, Microsoft Graph API, ou usando o UI de gestão Azure.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>O que não é sincronizado com Azure AD DS

Os seguintes objetos ou atributos não são sincronizados de um ambiente AD DS no local para Azure AD ou Azure AD DS:

* **Atributos excluídos:** Pode optar por excluir certos atributos da sincronização para Azure AD de um ambiente AD DS no local utilizando o Azure AD Connect. Estes atributos excluídos não estão então disponíveis em Azure AD DS.
* **Políticas de Grupo:** As Políticas de Grupo configuradas num ambiente AD DS no local não são sincronizadas com a Azure AD DS.
* **Pasta Sysvol:** O conteúdo da pasta *Sysvol* num ambiente AD DS no local não está sincronizado com o Azure AD DS.
* **Objetos de computador:** Os objetos de computador para computadores unidos a um ambiente AD DS no local não são sincronizados com Azure AD DS. Estes computadores não têm uma relação de confiança com o domínio gerido e apenas pertencem ao ambiente AD DS no local. Em Azure AD DS, apenas são mostrados objetos de computador para computadores que se uniram explicitamente ao domínio gerido.
* **Atributos SidHistory para utilizadores e grupos:** Os SIDs do utilizador primário e do grupo primário de um ambiente AD DS no local são sincronizados com Azure AD DS. No entanto, os *atributos SidHistory existentes* para utilizadores e grupos não são sincronizados do ambiente AD DS no local para Azure AD DS.
* **Estruturas das Unidades de Organização (OU):** As Unidades Organizacionais definidas num ambiente AD DS no local não sincronizam com a Azure AD DS. Existem duas OUs incorporadas em Azure AD DS - uma para utilizadores e outra para computadores. O domínio gerido tem uma estrutura plana de U. Pode optar por [criar um OU personalizado no seu domínio gerido.](create-ou.md)

## <a name="password-hash-synchronization-and-security-considerations"></a>Sincronização de hash de palavra-passe e considerações de segurança

Quando ativa o Azure AD DS, são necessárias hashes de senha para autenticação NTLM + Kerberos. O AZure AD não armazena palavras-passe de texto claro, pelo que estes hashes não podem ser gerados automaticamente para as contas de utilizador existentes. Uma vez gerados e armazenados, os hashes de palavra-passe compatíveis com NTLM e Kerberos são sempre armazenados de forma encriptada em Azure AD.

As chaves de encriptação são únicas para cada inquilino AZure AD. Estes hashes são encriptados de tal forma que apenas Azure AD DS tem acesso às chaves de desencriptação. Nenhum outro serviço ou componente no Azure AD tem acesso às teclas de desencriptação.

Os hashes de palavra-passe legacy são então sincronizados a partir do Azure AD nos controladores de domínio para um domínio gerido. Os discos para estes controladores de domínio geridos em Azure AD DS são encriptados em repouso. Estes hashes de palavra-passe são armazenados e protegidos nestes controladores de domínio semelhantes à forma como as palavras-passe são armazenadas e protegidas num ambiente AD DS no local.

Para ambientes Azure AD apenas na nuvem, [os utilizadores devem redefinir/alterar a sua palavra-passe](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que as hashes de palavra-passe necessárias sejam geradas e armazenadas em Azure AD. Para qualquer conta de utilizador em nuvem criada em Azure AD após permitir serviços de domínio Azure AD, os hashes de palavra-passe são gerados e armazenados nos formatos compatíveis NTLM e Kerberos. Todas as contas de utilizador em nuvem devem alterar a sua palavra-passe antes de serem sincronizadas para Azure AD DS.

Para contas híbridas de utilizadores sincronizadas a partir do ambiente AD DS no local utilizando o Azure AD Connect, é necessário [configurar o Azure AD Connect para sincronizar hashes de palavra-passe nos formatos compatíveis com NTLM e Kerberos.](tutorial-configure-password-hash-sync.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as especificidades da sincronização da palavra-passe, consulte [como funciona a sincronização de hash de palavra-passe com o Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Para começar com a Azure AD DS, [crie um domínio gerido](tutorial-create-instance.md).

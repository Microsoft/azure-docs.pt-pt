---
title: Como funciona a sincronização nos Serviços de Domínio Da AD Azure [ Microsoft Docs
description: Saiba como funciona o processo de sincronização para objetos e credenciais de um inquilino da AD Azure ou no local do ambiente ative directory Domain Services para um domínio gerido pelo Azure Ative Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 38ed48df4d681543cc30daccf46b98635d973b89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639900"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Como objetos e credenciais são sincronizados num domínio gerido pelo Azure AD Domain Services

Objetos e credenciais num domínio gerido pelo Azure Ative Directory Services (AD DS) podem ser criados localmente dentro do domínio, ou sincronizados a partir de um inquilino do Azure Ative Directory (Azure AD). Quando se implanta o Azure AD DS, configura-se uma sincronização automática de ida e única e começou a replicar os objetos a partir de Azure AD. Esta sincronização de sentido único continua a ser executada em segundo plano para manter o Domínio Gerido azure AD DS atualizado com quaisquer alterações a partir de Azure AD. Não ocorre sincronização desde o Azure AD DS até à AD Azure.

Num ambiente híbrido, objetos e credenciais de um domínio AD DS no local podem ser sincronizados para Azure AD Connect usando Azure AD Connect. Uma vez que esses objetos são sincronizados com sucesso para AD Azure, a sincronização automática de fundo então coloca esses objetos e credenciais disponíveis para aplicações usando o domínio gerido azure AD DS.

O diagrama que se segue ilustra como funciona a sincronização entre o Azure AD DS, o Azure AD, e um ambiente AD DS opcional no local:

![Visão geral de sincronização para um domínio gerido pelos Serviços de Domínio Da Azure AD](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Sincronização de Azure AD para Azure AD DS

As contas de utilizador, membros do grupo e hashes credenciais são sincronizadas de uma forma de Azure AD para Azure AD DS. Este processo de sincronização é automático. Não é necessário configurar, monitorizar ou gerir este processo de sincronização. A sincronização inicial pode demorar algumas horas a alguns dias, dependendo do número de objetos no diretório Azure AD. Após a sincronização inicial estar completa, as alterações que são feitas em AD Azure, tais como alterações de password ou atributo, são automaticamente sincronizadas para O DS Azure.

Quando um utilizador é criado em AD Azure, não é sincronizado com O DS Azure até que altere a sua palavra-passe em Azure AD. Este processo de alteração de palavra-passe faz com que as hashes de senha para a autenticação Kerberos e NTLM sejam geradas e armazenadas em Azure AD. As hashes de senha são necessárias para autenticar com sucesso um utilizador em Azure AD DS.

O processo de sincronização é de uma forma/unidirecional por design. Não há sincronização inversa das mudanças do Azure AD DS de volta ao Azure AD. Um domínio gerido por AD DS Azure é em grande parte apenas lido, exceto para OUs personalizado que você pode criar. Não é possível efetuar alterações nos atributos do utilizador, palavras-passe do utilizador ou membros do grupo dentro de um domínio gerido pelo Azure AD DS.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Atributo sincronização e mapeamento a Azure AD DS

A tabela seguinte lista alguns atributos comuns e como são sincronizados com O DS Azure.

| Atributo em DS AD Azure | Origem | Notas |
|:--- |:--- |:--- |
| UPN | Atributo *UPN* do utilizador no inquilino da AD Azure | O atributo da UPN do inquilino da AD Azure é sincronizado como é para Azure AD DS. A forma mais fiável de iniciar sessão num domínio gerido pelo Azure AD DS é a utilização da UPN. |
| Nome de conta sam | *Atributo* de apelido de utilizador em inquilino da AD Azure ou autogerado | O atributo *SAMAccountName* é obtido a partir do *atributo mailNickname* no inquilino Azure AD. Se várias contas de utilizador tiverem o mesmo *atributo de apelido* de correio, o *SAMAccountName* é autogerado. Se o *prefixo* de correio do utilizador Nickname ou *UPN* for superior a 20 caracteres, o *SAMAccountName* é autogerado para cumprir o limite de 20 caracteres nos atributos *SAMAccountName.* |
| Palavras-passe | Senha do utilizador do inquilino da AD Azure | As hashes de senha legacy necessárias para a autenticação NTLM ou Kerberos são sincronizadas do inquilino Azure AD. Se o inquilino da AD Azure estiver configurado para sincronização híbrida utilizando o Azure AD Connect, estas hashes de senha são provenientes do ambiente AD DS no local. |
| Sid do utilizador/grupo primário | Autogerado | O SID primário para contas de utilizador/grupo é autogerado em Azure AD DS. Este atributo não corresponde ao sid do principal utilizador/grupo do objeto num ambiente AD DS no local. Esta incompatibilidade deve-se ao facto de o domínio gerido pelo Azure AD DS ter um espaço de nome SID diferente do domínio AD DS no local. |
| Histórico sid para utilizadores e grupos | No local, o utilizador primário e o grupo SID | O atributo *SidHistory* para utilizadores e grupos em Azure AD DS está definido para corresponder ao utilizador primário correspondente ou grupo SID em um ambiente AD DS no local. Esta funcionalidade ajuda a tornar mais fácil o levantamento e a mudança de aplicações no local para o Azure AD DS, uma vez que não precisa de re-ACL recursos. |

> [!TIP]
> **Inscreva-se no domínio gerido utilizando o formato UPN** O atributo *SAMAccountName,* como, por exemplo, `AADDSCONTOSO\driley`pode ser gerado automaticamente para algumas contas de utilizador num domínio gerido pelo Azure AD DS. O *SAMAccountName* gerado automaticamente pelos utilizadores pode diferir do prefixo UPN, pelo que nem sempre é uma forma fiável de iniciar sessão.
>
> Por exemplo, se vários utilizadores tiverem o mesmo *atributo de mailNickname* ou utilizadores tiverem prefixos UPN excessivamente longos, o *Nome SAMAccountName* para estes utilizadores pode ser gerado automaticamente. Utilize o formato UPN, como, por exemplo, `driley@aaddscontoso.com`para iniciar sessão fiavelmente num domínio gerido pelo Azure AD DS.

### <a name="attribute-mapping-for-user-accounts"></a>Mapeamento de atributos para contas de utilizador

A tabela que se segue ilustra como os atributos específicos para os objetos de utilizador em AD Azure são sincronizados com os atributos correspondentes no Azure ADDS.

| Atributo do utilizador em Azure AD | Atributo do utilizador em Azure AD DS |
|:--- |:--- |
| contaEnabled |userAccountControl (define ou limpa a ACCOUNT_DISABLED bit) |
| city |l |
| país |co |
| departamento |departamento |
| displayName |displayName |
| facsimilePhoneNumber |facsimilePhoneNumber |
| nomeDado |nomeDado |
| empregoTítulo |título |
| correio |correio |
| mailApelido |msDS-AzureADMailNickname |
| mailApelido |SAMAccountName (pode por vezes ser autogerado) |
| dispositivo móvel |dispositivo móvel |
| objectide |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolíticas |userAccountControl (define ou limpa a DONT_EXPIRE_PASSWORD bit) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Código postal |Código postal |
| língua preferida |língua preferida |
| state |SC |
| streetAddress |streetAddress |
| surname |sn |
| telefoneNúmero |telefoneNúmero |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapeamento de atributos para grupos

A tabela que se segue ilustra como os atributos específicos para objetos de grupo em Azure AD são sincronizados com os atributos correspondentes no Azure ADDS.

| Atributo de grupo em Azure AD | Atributo de grupo em Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (pode por vezes ser autogerado) |
| correio |correio |
| mailApelido |msDS-AzureADMailNickname |
| objectide |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| segurançaHabilitado |tipodeGrupo |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Sincronização de AD DS no local para Azure AD e Azure AD DS

O Azure AD Connect é utilizado para sincronizar contas de utilizadores, membros do grupo e hashes credenciais de um ambiente AD DS no local para a AD Azure. Os atributos das contas de utilizador, tais como o UPN e o identificador de segurança no local (SID) são sincronizados. Para iniciar a utilização do Azure AD DS, as hashes de senha antigas necessárias para a autenticação NTLM e Kerberos também são sincronizadas para a AD Azure.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio gerido pelo Azure AD DS para sincronizar objetos de volta ao Azure AD.

Se configurar a redação, as alterações do Azure AD são sincronizadas de volta ao ambiente AD DS no local. Por exemplo, se um utilizador alterar a sua palavra-passe utilizando a gestão de passwords auto-service Daa Azure, a palavra-passe é atualizada de volta no ambiente AD DS no local.

> [!NOTE]
> Utilize sempre a versão mais recente do Azure AD Connect para garantir que tem correções para todos os bugs conhecidos.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização de um ambiente multiflorestal no local

Muitas organizações têm um ambiente bastante complexo no local, a DS, que inclui várias florestas. O Azure AD Connect suporta utilizadores, grupos e hashes credenciais sincronizados, desde ambientes multiflorestais até AD Azure.

A Azure AD tem um espaço de nome muito mais simples e plano. Para permitir aos utilizadores acederem de forma fiável a aplicações protegidas pela Azure AD, resolva os conflitos da UPN entre as contas dos utilizadores em diferentes florestas. Os domínios geridos pela Azure AD DS utilizam uma estrutura de Ou plana, semelhante à Azure AD. Todas as contas e grupos de utilizadores estão armazenados no contentor *aaddc utilizadores,* apesar de estarem sincronizados de diferentes domínios ou florestas no local, mesmo que tenha configurado uma estrutura hierárquica de Ou no local. O domínio gerido pela AD DS Azure achata quaisquer estruturas hierárquicas da U.

Como anteriormente detalhado, não há sincronização do Azure AD DS de volta ao Azure AD. Pode [criar uma Unidade Organizacional Personalizada (OU)](create-ou.md) em DS AD Azure e, em seguida, utilizadores, grupos ou contas de serviço dentro dessas OUs personalizadas. Nenhum dos objetos criados em OUs personalizados é sincronizado de volta ao Azure AD. Estes objetos estão disponíveis apenas dentro do domínio gerido pelo Azure AD DS, e não são visíveis usando cmdlets De PowerShell Azure AD, Microsoft Graph API ou usando a UI de gestão de AD Azure.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>O que não é sincronizado com O DS Azure

Os seguintes objetos ou atributos não são sincronizados de um ambiente AD DS no local para Azure AD ou Azure AD DS:

* **Atributos excluídos:** Pode optar por excluir certos atributos da sincronização para a AD Azure de um ambiente AD DS no local utilizando o Azure AD Connect. Estes atributos excluídos não estão então disponíveis no Azure AD DS.
* **Políticas de grupo:** As Políticas de Grupo configuradas num ambiente AD DS no local não são sincronizadas com O DS Azure.
* **Pasta Sysvol:** O conteúdo da pasta *Sysvol* num ambiente AD DS no local não é sincronizado para o Azure AD DS.
* **Objetos de computador:** Os objetos informáticos para computadores ligados a um ambiente AD DS no local não são sincronizados com O DS Azure. Estes computadores não têm uma relação de confiança com o domínio gerido pela AD DS azure e pertencem apenas ao ambiente AD DS no local. No Azure AD DS, apenas são mostrados objetos de computador para computadores que tenham ligado explicitamente ao domínio gerido.
* **SidHistory atribui para utilizadores e grupos:** Os SIDs do principal utilizador e do grupo primário a partir de um ambiente AD DS no local são sincronizados para AD DS Azure. No entanto, os atributos existentes do *SidHistory* para utilizadores e grupos não são sincronizados do ambiente AD DS no local para O DS Azure.
* **Estruturas de Unidades de Organização (OU):** Unidades organizacionais definidas num ambiente AD DS no local não sincronizam com O DS Azure. Existem duas OUs incorporadas em DS AD Azure - uma para utilizadores e outra para computadores. O domínio gerido pela Azure AD DS tem uma estrutura de Ou plana. Pode optar por [criar um OU personalizado no seu domínio gerido.](create-ou.md)

## <a name="password-hash-synchronization-and-security-considerations"></a>Sincronização de hash de palavra-passe e considerações de segurança

Quando ativa o Azure AD DS, são necessárias hashes de senha seletiva para a autenticação NTLM + Kerberos. A Azure AD não armazena senhas de texto claro, pelo que estes hashes não podem ser gerados automaticamente para as contas de utilizador existentes. Uma vez gerados e armazenados, os hashes de senha compatíveis NTLM e Kerberos são sempre armazenados de forma encriptada em Azure AD.

As chaves de encriptação são únicas para cada inquilino da AD Azure. Estes hashes são encriptados de tal forma que apenas o Azure AD DS tem acesso às teclas de desencriptação. Nenhum outro serviço ou componente em Azure AD tem acesso às teclas de desencriptação.

As hashes de senha legacy são então sincronizadas a partir de Azure AD para os controladores de domínio para um domínio gerido por AD DS Azure. Os discos para estes controladores de domínio geridos em DS AD Azure são encriptados em repouso. Estes hashes de palavra-passe são armazenados e protegidos nestes controladores de domínio semelhantes à forma como as palavras-passe são armazenadas e protegidas num ambiente AD DS no local.

Para ambientes AD Azure apenas na nuvem, [os utilizadores devem redefinir/alterar a sua palavra-passe](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que as hashes de senha necessárias sejam geradas e armazenadas em Azure AD. Para qualquer conta de utilizador na nuvem criada em Azure AD após ativar os Serviços de Domínio Azure AD, as hashes de senha são geradas e armazenadas nos formatos compatíveis NTLM e Kerberos. Todas as contas de utilizadores na nuvem devem alterar a sua palavra-passe antes de serem sincronizadas com o Azure AD DS.

Para contas híbridas de utilizador sincronizadas a partir do ambiente AD DS no local utilizando o Azure AD Connect, é necessário configurar o [Azure AD Connect para sincronizar hashes de palavra-passe nos formatos compatíveis NTLM e Kerberos.](tutorial-configure-password-hash-sync.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as especificidades da sincronização de passwords, consulte como funciona a sincronização de hash de [palavra-passe com o Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Para começar com o Azure AD DS, [crie um domínio gerido.](tutorial-create-instance.md)

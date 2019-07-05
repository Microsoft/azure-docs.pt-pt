---
title: Vincular utilizando o LDAP seguro (LDAPS) a um domínio gerido do Azure AD Domain Services | Documentos da Microsoft
description: Ligar a um domínio gerido do Azure AD Domain Services usando o LDAP seguro (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: df0b3d27eec478280a33be831a2431eccdf05a74
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483373"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Ligar a um domínio gerido do Azure AD Domain Services usando o LDAP seguro (LDAPS)

## <a name="before-you-begin"></a>Antes de começar
Concluída [Tarefa 4 - configurar o DNS para aceder ao domínio gerido a partir da internet](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Tarefa 5: Ligar ao domínio gerido através de LDAP usando LDP.exe
Pode usar a ferramenta de LDP.exe, que está incluída no pacote de ferramentas de administração remota do servidor para vincular e procurar em LDAP.

Em primeiro lugar, abra o LDP e ligue ao domínio gerido. Clique em **conexão** e clique em **Connect...**  no menu. Especifique o nome de domínio DNS do domínio gerido. Especifique a porta a utilizar para ligações. Para conexões LDAP, utilize a porta 389. Para ligações de LDAPS, utilize a porta 636. Clique em **OK** botão para ligar ao domínio gerido.

Em seguida, vincule ao domínio gerido. Clique em **conexão** e clique em **vincular...**  no menu. Forneça as credenciais de uma conta de utilizador que pertencem ao grupo "Administradores do AAD DC".

> [!IMPORTANT]
> Os utilizadores (e contas de serviço) não é possível efetuar une simples de LDAP se tiver desativado a sincronização de hash de palavra-passe NTLM na sua instância do Azure AD Domain Services.  Para obter mais informações sobre como desabilitar a sincronização de hash de palavra-passe NTLM, leia [proteger o seu domínio gerido do Azure AD DOmain Services](secure-your-domain.md).
>
>

Selecione **View**e, em seguida, selecione **árvore** no menu. Deixe o campo Base DN em branco e clique em OK. Navegue para o contentor que pretende pesquisar, o contentor com o botão direito e selecione Search.

> [!TIP]
> - Os utilizadores e grupos sincronizados a partir do Azure AD são armazenados na **utilizadores do aad DC** unidade organizacional. O caminho de pesquisa para esta unidade organizacional se parece com ```OU=AADDC Users,DC=CONTOSO100,DC=COM```.
> - Contas de computador para computadores associados ao domínio gerido são armazenadas no **computadores do aad DC** unidade organizacional. O caminho de pesquisa para esta unidade organizacional se parece com ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```.
>
>

Obter mais informações - [Noções básicas de consulta LDAP](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Tarefa 6: Bloquear o acesso de secure LDAP ao seu domínio gerido através da internet
> [!NOTE]
> Se não tiver ativado o acesso LDAPS para o domínio gerido através da internet, ignore esta tarefa de configuração.
>
>

Antes de começar esta tarefa, complete os passos descritos em [tarefa 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Quando ativar o acesso LDAPS através da internet ao seu domínio gerido, cria uma ameaça de segurança. O domínio gerido está acessível a partir da internet, a porta utilizada para o LDAP seguro (ou seja, a porta 636). Pode optar por restringir o acesso ao domínio gerido para os endereços IP conhecidos específicos. Criar um grupo de segurança de rede (NSG) e associá-la com a sub-rede onde ativou o Azure AD Domain Services.

O exemplo de NSG na seguinte tabela bloqueia o acesso de secure LDAP pela internet. As regras de NSG permitem o acesso de secure LDAP entrado através da porta TCP 636 apenas a partir de um conjunto especificado de endereços IP. A regra predefinida do "DenyAll" aplica-se a todos os outros tráfegos de entrada da internet. A regra NSG para permitir o acesso LDAPS através da internet a partir de endereços IP especificados tem prioridade sobre a regra de DenyAll NSG.

![Exemplo de NSG para proteger o acesso LDAPS através da internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Obter mais informações** - [grupos de segurança de rede](../virtual-network/security-overview.md).


## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](create-instance.md)
* [Gerir um domínio do Azure AD Domain Services](manage-domain.md)
* [Noções básicas de consulta LDAP](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)
* [Gerir política de grupo para o Azure AD Domain Services](manage-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/security-overview.md)
* [Criar um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Passo seguinte
[Resolver problemas de secure LDAP no domínio gerido](tshoot-ldaps.md)

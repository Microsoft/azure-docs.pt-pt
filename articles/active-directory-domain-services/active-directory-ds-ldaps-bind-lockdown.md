---
title: Vincular utilizando o LDAP seguro (LDAPS) a um domínio gerido do Azure AD Domain Services | Documentos da Microsoft
description: Ligar a um domínio gerido do Azure AD Domain Services usando o LDAP seguro (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: b5ddc44f0011cc90bbfe2a1bdd32e45d44f4b16a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39460192"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Ligar a um domínio gerido do Azure AD Domain Services usando o LDAP seguro (LDAPS)

## <a name="before-you-begin"></a>Antes de começar
Concluída [Tarefa 4 - configurar o DNS para aceder ao domínio gerido a partir da internet](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Tarefa 5: Ligar ao domínio gerido através de LDAP usando LDP.exe
Pode usar a ferramenta de LDP.exe, que está incluída no pacote de ferramentas de administração remota do servidor para vincular e procurar em LDAP.

Em primeiro lugar, abra o LDP e ligue ao domínio gerido. Clique em **conexão** e clique em **Connect...**  no menu. Especifique o nome de domínio DNS do domínio gerido. Especifique a porta a utilizar para ligações. Para conexões LDAP, utilize a porta 389. Para ligações de LDAPS, utilize a porta 636. Clique em **OK** botão para ligar ao domínio gerido.

Em seguida, vincule ao domínio gerido. Clique em **conexão** e clique em **vincular...**  no menu. Forneça as credenciais de uma conta de utilizador que pertencem ao grupo "Administradores do AAD DC".

Selecione **View**e, em seguida, selecione **árvore** no menu. Deixe o campo Base DN em branco e clique em OK. Navegue para o contentor que pretende pesquisar, o contentor com o botão direito e selecione Search.

> [!TIP]
> - Os utilizadores e grupos sincronizados a partir do Azure AD são armazenados na **utilizadores do aad DC** contentor. O caminho de pesquisa para esse contêiner é semelhante a ```CN=AADDC\ Users,DC=CONTOSO100,DC=COM```.
> - Contas de computador para computadores associados ao domínio gerido são armazenadas no **computadores do aad DC** contentor. O caminho de pesquisa para esse contêiner é semelhante a ```CN=AADDC\ Computers,DC=CONTOSO100,DC=COM```.
>
>

Obter mais informações - [Noções básicas de consulta LDAP](https://technet.microsoft.com/library/aa996205.aspx)


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
* [Azure AD Domain Services - guia de introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Noções básicas de consulta LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Administrar política de grupo num domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/security-overview.md)
* [Criar um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Passo seguinte
[Resolver problemas de secure LDAP no domínio gerido](active-directory-ds-ldaps-troubleshoot.md)

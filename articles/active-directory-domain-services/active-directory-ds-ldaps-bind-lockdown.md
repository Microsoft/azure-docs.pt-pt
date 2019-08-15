---
title: Associar usando LDAP Seguro (LDAPs) a um domínio gerenciado Azure AD Domain Services | Microsoft Docs
description: Associar a um Azure AD Domain Services domínio gerenciado usando LDAP seguro (LDAPs)
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
ms.openlocfilehash: cbc5bee0f4cc59f59af6e3f57219279cd8fcb030
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988571"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Associar a um Azure AD Domain Services domínio gerenciado usando LDAP seguro (LDAPs)

## <a name="before-you-begin"></a>Antes de começar
Concluir [tarefa 4-configurar o DNS para acessar o domínio gerenciado pela Internet](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Tarefa 5: Associar ao domínio gerenciado por LDAP usando o LDP. exe
Você pode usar a ferramenta LDP. exe, que está incluída no pacote de ferramentas de administração de servidor remoto para associar e Pesquisar por LDAP.

Primeiro, abra o LDP e conecte-se ao domínio gerenciado. Clique em **conexão** e clique em **conectar...** no menu. Especifique o nome de domínio DNS do domínio gerenciado. Especifique a porta a ser usada para conexões. Para conexões LDAP, use a porta 389. Para conexões LDAPs, use a porta 636. Clique no botão **OK** para se conectar ao domínio gerenciado.

Em seguida, associe-se ao domínio gerenciado. Clique em **conexão** e clique em **associar...** no menu. Forneça as credenciais de uma conta de usuário que pertença ao grupo ' Administradores do controlador de domínio do AAD '.

> [!IMPORTANT]
> Os usuários (e as contas de serviço) não poderão executar associações LDAP simples se você tiver desabilitado a sincronização de hash de senha NTLM em sua instância de Azure AD Domain Services.  Para obter mais informações sobre como desabilitar a sincronização de hash de senha NTLM, leia [proteger seu Azure AD Domain Services domínio gerenciado](secure-your-domain.md).
>
>

Selecione **Exibir**e, em seguida, **árvore** no menu. Deixe o campo base DN em branco e clique em OK. Navegue até o contêiner que você deseja pesquisar, clique com o botão direito do mouse no contêiner e selecione Pesquisar.

> [!TIP]
> - Os usuários e grupos sincronizados do Azure AD são armazenados na unidade organizacional **usuários do AADDC** . O caminho de pesquisa para essa unidade organizacional é ```OU=AADDC Users,DC=CONTOSO100,DC=COM```semelhante a.
> - As contas de computador para computadores ingressados no domínio gerenciado são armazenadas na unidade organizacional **computadores AADDC** . O caminho de pesquisa para essa unidade organizacional é ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```semelhante a.
>
>

Mais informações- [noções básicas de consulta LDAP](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Tarefa 6: Bloquear o acesso LDAP seguro ao domínio gerenciado pela Internet
> [!NOTE]
> Se você não tiver habilitado o acesso LDAPs ao domínio gerenciado pela Internet, ignore esta tarefa de configuração.
>
>

Antes de iniciar esta tarefa, conclua as etapas descritas na [tarefa 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Quando você habilita o acesso LDAPs pela Internet para seu domínio gerenciado, ele cria uma ameaça de segurança. O domínio gerenciado pode ser acessado pela Internet na porta usada para LDAP seguro (ou seja, a porta 636). Você pode optar por restringir o acesso ao domínio gerenciado para endereços IP conhecidos específicos. Crie um NSG (grupo de segurança de rede) e associe-o à sub-rede em que você habilitou Azure AD Domain Services.

O NSG de exemplo na tabela a seguir bloqueia o acesso LDAP seguro pela Internet. As regras no NSG permitem acesso LDAP seguro de entrada pela porta TCP 636 somente de um conjunto especificado de endereços IP. A regra ' DenyAll ' padrão se aplica a todos os outros tráfegos de entrada da Internet. A regra NSG para permitir o acesso LDAPs pela Internet de endereços IP especificados tem uma prioridade mais alta do que a regra NSG DenyAll.

![Exemplo de NSG para proteger o acesso LDAPs pela Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Mais informações** - [grupos de segurança de rede](../virtual-network/security-overview.md).


## <a name="related-content"></a>Conteúdo relacionado
* [Guia de Introdução de Azure AD Domain Services](create-instance.md)
* [Gerenciar um domínio de Azure AD Domain Services](manage-domain.md)
* [Noções básicas de consulta LDAP](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)
* [Gerenciar Política de Grupo para Azure AD Domain Services](manage-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/security-overview.md)
* [Criar um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Passo seguinte
[Solucionar problemas de LDAP seguro em um domínio gerenciado](tshoot-ldaps.md)

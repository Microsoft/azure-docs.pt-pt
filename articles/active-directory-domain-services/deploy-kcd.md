---
title: 'Azure Active Directory Domain Services: Habilitar a delegação restrita de Kerberos | Microsoft Docs'
description: Habilitar a delegação restrita de Kerberos em Azure Active Directory Domain Services domínios gerenciados
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: f234eaea0d4df3859ef9458ea334f1b7616add34
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612942"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Configurar a KCD (delegação restrita de Kerberos) em um domínio gerenciado
Muitos aplicativos precisam acessar recursos no contexto do usuário. Active Directory dá suporte a um mecanismo chamado delegação de Kerberos, que habilita esse caso de uso. Além disso, você pode restringir a delegação para que apenas recursos específicos possam ser acessados no contexto do usuário. Azure AD Domain Services domínios gerenciados são diferentes dos domínios de Active Directory tradicionais, pois eles são bloqueados com mais segurança.

Este artigo mostra como configurar a delegação restrita de Kerberos em um Azure AD Domain Services domínio gerenciado.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Delegação restrita de Kerberos (KCD)
A delegação de Kerberos permite que uma conta represente outra entidade de segurança (como um usuário) para acessar recursos. Considere um aplicativo Web que acessa uma API Web de back-end no contexto de um usuário. Neste exemplo, o aplicativo Web (em execução no contexto de uma conta de serviço ou uma conta de computador/máquina) representa o usuário ao acessar o recurso (API Web de back-end). A delegação de Kerberos é insegura, pois não restringe os recursos que a conta representando pode acessar no contexto do usuário.

A delegação restrita de Kerberos (KCD) restringe os serviços/recursos aos quais o servidor especificado pode agir em nome de um usuário. O KCD tradicional exige privilégios de administrador de domínio para configurar uma conta de domínio para um serviço e restringe a conta a um único domínio.

O KCD tradicional também tem alguns problemas associados a ele. Em sistemas operacionais anteriores, se o administrador de domínio configurou o KCD baseado em conta para o serviço, o administrador de serviços não tinha uma maneira útil de saber quais serviços de front-end eram delegados aos serviços de recursos que eles possuíam. E qualquer serviço de front-end que pode delegar a um serviço de recurso representou um possível ponto de ataque. Se um servidor que hospedava um serviço de front-end foi comprometido e foi configurado para delegar aos serviços de recursos, os serviços de recursos também poderiam ser comprometidos.

> [!NOTE]
> Em um domínio gerenciado Azure AD Domain Services, você não tem privilégios de administrador de domínio. Portanto, as **KCD tradicionais baseadas em contas não podem ser configuradas em um domínio gerenciado**. Use KCD com base em recursos conforme descrito neste artigo. Esse mecanismo também é mais seguro.
>
>

## <a name="resource-based-kcd"></a>KCD com base em recursos
Do Windows Server 2012 em diante, os administradores de serviço têm a capacidade de configurar a delegação restrita para seu serviço. Nesse modelo, o administrador de serviços de back-end pode permitir ou negar serviços de front-end específicos do uso do KCD. Esse modelo é conhecido como **KCD baseada em recursos**.

O KCD baseado em recursos é configurado usando o PowerShell. Você usa os `Set-ADComputer` cmdlets ou `Set-ADUser` , dependendo se a conta de representação é uma conta de computador ou conta de serviço/conta de usuário.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Configurar KCD com base em recursos para uma conta de computador em um domínio gerenciado
Suponha que você tenha um aplicativo Web em execução no computador ' contoso-webapp.contoso.com '. Ele precisa acessar o recurso (uma API da Web em execução em ' contoso-api.contoso.com ') no contexto de usuários do domínio. Veja como você configuraria o KCD baseado em recursos para este cenário:

1. [Crie uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar essa UO personalizada para usuários dentro do domínio gerenciado.
2. Ingresse ambas as máquinas virtuais (a que executa o aplicativo Web e a que está executando a API da Web) para o domínio gerenciado. Crie essas contas de computador na UO personalizada.
3. Agora, configure KCD com base em recursos usando o seguinte comando do PowerShell:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> As contas de computador para o aplicativo Web e a API da Web precisam estar em uma UO personalizada em que você tenha permissões para configurar KCD com base em recursos. Você não pode configurar KCD com base em recursos para uma conta de computador no contêiner ' AAD DC Computers ' interno.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Configurar KCD com base em recursos para uma conta de usuário em um domínio gerenciado
Suponha que você tenha um aplicativo Web em execução como uma conta de serviço ' appsvc ' e precise acessar o recurso (uma API Web executada como uma conta de serviço-' backendsvc ') no contexto de usuários do domínio. Veja como você configuraria o KCD baseado em recursos para esse cenário.

1. [Crie uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar essa UO personalizada para usuários dentro do domínio gerenciado.
2. Ingresse a máquina virtual que executa a API/recurso de back-end da Web no domínio gerenciado. Crie sua conta de computador na UO personalizada.
3. Crie a conta de serviço (por exemplo, ' appsvc ') usada para executar o aplicativo Web dentro da UO personalizada.
4. Agora, configure KCD com base em recursos usando o seguinte comando do PowerShell:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> A conta de computador para a API Web de back-end e a conta de serviço precisam estar em uma UO personalizada em que você tenha permissões para configurar o KCD baseado em recursos. Você não pode configurar KCD com base em recursos para uma conta de computador no contêiner interno ' AAD DC Computers ' ou para contas de usuário no contêiner ' AAD DC users ' interno. Portanto, você não pode usar contas de usuário sincronizadas do Azure AD para configurar o KCD baseado em recursos.
>

## <a name="related-content"></a>Conteúdo relacionado
* [Guia de Introdução de Azure AD Domain Services](tutorial-create-instance.md)
* [Visão geral da delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx)

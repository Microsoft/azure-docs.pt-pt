---
title: 'Azure Active Directory Domain Services: Ativar a delegação restringida de kerberos | Documentos da Microsoft'
description: Ativar a delegação restringida de kerberos em domínios geridos do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
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
ms.author: mstephen
ms.openlocfilehash: 287aea990664cf01caf83e1871acf69e48210226
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246168"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Configurar a delegação restringida de Kerberos (KCD) num domínio gerido
Muitos aplicativos precisam de aceder a recursos no contexto do utilizador. Active Directory suporta um mecanismo chamado delegação de Kerberos, que permite que este caso de utilização. Além disso, pode restringir a delegação para que apenas os recursos específicos podem ser acedidos no contexto do utilizador. Domínios de geridos de serviços de domínio do AD do Azure são diferentes das tradicionais domínios do Active Directory, uma vez que eles com mais segurança são bloqueados.

Este artigo mostra-lhe como configurar a delegação restringida de Kerberos num domínio gerido do Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Delegação restringida de Kerberos (KCD)
Delegação de Kerberos que permite que uma conta para representar outra entidade de segurança (por exemplo, um utilizador) para aceder aos recursos. Considere uma aplicação web que acede a uma API de web de back-end no contexto de um utilizador. Neste exemplo, a aplicação web (em execução no contexto de uma conta de serviço ou uma conta de computador/máquina) representa o utilizador ao aceder o recursos (back-end web API). Delegação de Kerberos é insegura, uma vez que ele não restringe os recursos que a conta impersonating pode aceder no contexto do utilizador.

Delegação restringida de Kerberos (KCD) restringe os serviços/recursos aos quais o servidor especificado pode agir em nome de um utilizador. KCD tradicional requer privilégios de administrador de domínio para configurar uma conta de domínio para um serviço e restringe a conta para um único domínio.

KCD tradicional também tem alguns problemas associados à mesma. Nos sistemas operativos anteriores, se o administrador de domínio configurada com base em conta o KCD para o serviço, o administrador de serviços teve uma maneira útil de saber quais os serviços front-end delegados aos serviços de recurso detinham. E qualquer serviço front-end que poderia delegar a um serviço de recursos representado um ponto de ataque potencial. Se um servidor que alojava um serviço de front-end fosse comprometido e que foi configurado para delegar a serviços de recursos, os serviços de recursos também podiam ser comprometidos.

> [!NOTE]
> Num domínio gerido do Azure AD Domain Services, não tem privilégios de administrador de domínio. Por conseguinte, **tradicional KCD com base na conta não é possível configurar um domínio gerido**. Utilize o KCD baseada em recursos, conforme descrito neste artigo. Esse mecanismo também é mais seguro.
>
>

## <a name="resource-based-kcd"></a>KCD baseada em recursos
Do Windows Server 2012 e superior, os administradores de serviços ganha a capacidade para configurar a delegação restrita para o seu serviço. Nesse modelo, o administrador de serviços de back-end pode permitir ou negar serviços front-end específicos da utilização de KCD. Esse modelo é conhecido como **baseada em recursos KCD**.

Baseada em recursos KCD está configurado com o PowerShell. Utilizar o `Set-ADComputer` ou `Set-ADUser` cmdlets, dependendo da conta impersonating é uma conta de computador ou uma conta de serviço/conta de utilizador.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Configurar o KCD baseada em recursos para uma conta de computador num domínio gerido
Suponha que tem uma aplicação web em execução no computador ' contoso100-webapp.contoso100.com'. Ela precisa acessar o recurso (uma API web em execução no ' contoso100-api.contoso100.com') no contexto de utilizadores de domínio. Eis como poderia configurar KCD baseada em recursos para este cenário:

1. [Criar uma UO personalizada](create-ou.md). Pode delegar permissões para gerir essa UO personalizado aos utilizadores no domínio gerido.
2. Junte-se ambas as máquinas virtuais (está sendo executada a aplicação web e está em execução a API web) para o domínio gerido. Crie estas contas de computador na UO personalizada.
3. Agora, configure o KCD baseada em recursos com o seguinte comando do PowerShell:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> As contas de computador para a aplicação web e a API web tem de ser numa UO em que tem permissões para configurar o KCD baseada em recursos personalizada. Não é possível configurar o KCD baseada em recursos para uma conta de computador no contentor "Computadores de controlador de domínio do AAD" incorporado.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Configurar o KCD baseada em recursos para uma conta de utilizador num domínio gerido
Suponha que tem uma aplicação web em execução como uma conta de serviço "appsvc" e que precisa aceder ao recurso (uma API web em execução como uma conta de serviço - 'backendsvc') no contexto de utilizadores de domínio. Aqui está como poderia configurar KCD baseada em recursos para este cenário.

1. [Criar uma UO personalizada](create-ou.md). Pode delegar permissões para gerir essa UO personalizado aos utilizadores no domínio gerido.
2. Junte-se a máquina virtual com o API/recurso da web de back-end para o domínio gerido. Crie a sua conta de computador na UO personalizada.
3. Crie a conta de serviço (por exemplo, "appsvc') utilizada para executar a aplicação web na UO personalizada.
4. Agora, configure o KCD baseada em recursos com o seguinte comando do PowerShell:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> A conta de computador para a API da web de back-end e a necessidade de conta de serviço para estar numa UO personalizada onde não tem permissões para configurar o KCD baseada em recursos. Não é possível configurar o KCD baseada em recursos para uma conta de computador no contentor "Computadores de controlador de domínio do AAD" incorporado ou para contas de utilizador no contentor "Utilizadores do AAD DC" incorporado. Portanto, não é possível utilizar contas de utilizador sincronizadas a partir do Azure AD para configurar o KCD baseada em recursos.
>

## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](create-instance.md)
* [Descrição geral da delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx)

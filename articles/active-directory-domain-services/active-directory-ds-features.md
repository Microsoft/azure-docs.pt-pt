---
title: 'Azure Active Directory Domain Services: Recursos | Microsoft Docs'
description: Recursos do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: e0f38ed8367f076c9f9c4da77440bb3e9603d514
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234199"
---
# <a name="features-provided-by-azure-active-directory-domain-services"></a>Recursos fornecidos pelo Azure Active Directory Domain Services

Os recursos a seguir estão disponíveis em Azure AD Domain Services domínios gerenciados.

* **Experiência de implantação simples:** Você pode habilitar Azure AD Domain Services para seu diretório do AD do Azure usando apenas alguns cliques. O domínio gerenciado inclui contas de usuário somente em nuvem e contas de usuário sincronizadas de um diretório local.
* **Suporte para ingresso no domínio:** Você pode facilmente ingressar computadores no domínio na rede virtual do Azure em que seu domínio gerenciado está disponível. A experiência de ingresso no domínio em sistemas operacionais cliente e servidor do Windows funciona diretamente em relação a domínios atendidos por Azure AD Domain Services. Você também pode usar as ferramentas de junção de domínio automatizadas em relação a esses domínios.
* **Uma instância de domínio por diretório do Azure AD:** Você pode criar um único domínio de Active Directory para cada diretório do Azure AD.
* **Criar domínios com nomes personalizados:** Você pode criar domínios com nomes personalizados (por exemplo, ' contoso100.com ') usando Azure AD Domain Services. Você pode usar nomes de domínio verificados ou não verificados. Opcionalmente, você também pode criar um domínio com o sufixo de domínio interno (ou seja, ' *. onmicrosoft.com ') oferecido pelo seu diretório do Azure AD.
* **Integrado ao Azure AD:** Você não precisa configurar ou gerenciar a replicação para Azure AD Domain Services. As contas de usuário, as associações de grupo e as credenciais de usuário (senhas) do seu diretório do Azure AD ficam automaticamente disponíveis no Azure AD Domain Services. Novos usuários, grupos ou alterações de atributos do seu locatário do Azure AD ou seu diretório local são sincronizados automaticamente com o Azure AD Domain Services.
* **Autenticação NTLM e Kerberos:** Com suporte para autenticação NTLM e Kerberos, você pode implantar aplicativos que dependem da autenticação integrada do Windows.
* **Use suas credenciais/senhas corporativas:** As senhas para usuários em seu locatário do Azure AD funcionam com Azure AD Domain Services. Os usuários podem usar suas credenciais corporativas para computadores de ingresso no domínio, entrar interativamente ou pela área de trabalho remota e autenticar no domínio gerenciado.
* **LDAP BIND & suporte de leitura LDAP:** Você pode usar aplicativos que dependem de associações LDAP para autenticar usuários em domínios atendidos pelo Azure AD Domain Services. Além disso, os aplicativos que usam operações de leitura LDAP para consultar os atributos de usuário/computador do diretório também podem trabalhar em Azure AD Domain Services.
* **LDAP Seguro (LDAPs):** Você pode habilitar o acesso ao diretório sobre LDAPS seguros (LDAPs). Por padrão, o acesso LDAP Seguro está disponível na rede virtual. No entanto, opcionalmente, você também pode habilitar o acesso LDAP seguro pela Internet.
* **Política de grupo:** Você pode usar um único GPO interno para os contêineres usuários e computadores para impor a conformidade com as políticas de segurança necessárias para contas de usuário e computadores ingressados no domínio. Você também pode criar seus próprios GPOs personalizados e atribuí-los a unidades organizacionais personalizadas para [gerenciar a diretiva de grupo](manage-group-policy.md).
* **Gerenciar DNS:** Os membros do grupo ' Administradores do AAD DC ' podem gerenciar o DNS para seu domínio gerenciado usando ferramentas de administração de DNS familiares, como o snap-in MMC de administração do DNS.
* **Criar UOs (unidades organizacionais) personalizadas:** Os membros do grupo ' Administradores do AAD DC ' podem criar UOs personalizadas no domínio gerenciado. Esses usuários recebem privilégios administrativos totais em UOs personalizadas, para que possam adicionar/remover contas de serviço, computadores, grupos, etc. dentro dessas UOs personalizadas.
* **Disponível em muitas regiões globais do Azure:** Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.
* **Alta disponibilidade:** O Azure AD Domain Services oferece alta disponibilidade para seu domínio. Esse recurso oferece a garantia de maior tempo de atividade de serviço e resiliência a falhas. O monitoramento de integridade interno oferece correção automatizada de falhas, girando novas instâncias para substituir instâncias com falha e fornecer serviço contínuo para seu domínio.
* **Proteção de bloqueio de conta do AD:** As contas de usuários serão bloqueadas por 30 minutos se cinco senhas inválidas forem usadas em 2 minutos. As contas são desbloqueadas automaticamente após 30 minutos.
* **Use ferramentas de gerenciamento familiares:** Você pode usar ferramentas de gerenciamento de Active Directory do Windows Server conhecidas, como o Centro Administrativo do Active Directory ou o Active Directory PowerShell para administrar domínios gerenciados.

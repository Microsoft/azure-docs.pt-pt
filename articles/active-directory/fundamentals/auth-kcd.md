---
title: Kerberos constrangido delegação com diretório ativo Azure
description: Orientação arquitetónica para alcançar este padrão de autenticação
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77f90cd7aa8d972226a8f134eaa7b3abfe7bea66
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114462"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Autenticação do Windows - Kerberos constrangido delegação com Diretório Ativo Azure

A Delegação Restrita kerberos (KCD) fornece uma delegação restrita entre recursos e baseia-se em nomes de princípios de serviço. Exige que os administradores de domínios criem as delegações e se limitem a um único domínio. O KCD baseado em recursos é frequentemente usado como uma forma de fornecer a autenticação Kerberos para uma aplicação web que tem utilizadores em vários domínios dentro de uma floresta de Diretório Ativo.

O Azure Ative Directory Application Proxy pode fornecer um único sign-on (SSO) e acesso remoto a aplicações baseadas em KCD que requerem um bilhete Kerberos para acesso e Delegação Restrita Kerberos (KCD).

Ativa sSO nas suas aplicações KCD no local que utilizam a Autenticação Integrada do Windows (IWA), dando permissão aos conectores Proxy da Aplicação para personificar os utilizadores no Ative Directory. O conector Proxy da Aplicação utiliza esta permissão para enviar e receber fichas em nome dos utilizadores.

## <a name="use-when"></a>Utilizar quando

É necessário fornecer acesso remoto, proteger com pré-autenticação e fornecer SSO para aplicações IWA no local.

![Diagrama de arquitetura](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador:** Acede a aplicação legado servida pela Application Proxy.

* **Web browser**: O componente com o que o utilizador interage para aceder ao URL externo da aplicação.

* **Azure AD**: Autentica o utilizador. 

* **Serviço de procuração de aplicação**: Atua como procuração inversa para enviar o pedido do utilizador para a aplicação no local. Fica em Azure AD. O Application Proxy também pode impor quaisquer políticas de acesso condicional.

* **Conector Proxy de aplicação**: Instalado no local nos servidores do Windows para fornecer conectividade à aplicação. Devolve a resposta ao Azure AD. Realiza a negociação do KCD com o Ative Directory, fazendo-se passar pelo utilizador para obter um sinal kerberos para a aplicação.

* **Diretório Ativo**: Envia o token Kerberos para a aplicação ao conector Application Proxy.

* **Aplicações legados**: Aplicações que recebem pedidos de utilizador da Application Proxy. As aplicações antigas devolvem a resposta ao conector Application Proxy.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Implementar autenticação do Windows (KCD) com Azure AD

* [Delegação Restrita Kerberos para início de sessão único nas suas aplicações com o Proxy de Aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd) 

* [Adicione um pedido de acesso remoto no local através do Application Proxy in Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 

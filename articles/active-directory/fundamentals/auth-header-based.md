---
title: Autenticação baseada em cabeçalho com Diretório Ativo Azure
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
ms.openlocfilehash: 4f364e4e14dd1b7c60cb81f06051d9dedd94396a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114457"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Autenticação baseada em cabeçalho com Diretório Ativo Azure

As aplicações antigas geralmente utilizam a autenticação baseada no cabeçalho. Neste cenário, um utilizador (ou autor de mensagens) autentica-se numa solução de identidade intermediária. A solução intermediária autentica o utilizador e propaga os cabeçalhos necessários do Protocolo de Transferência de Hipertexto (HTTP) para o serviço web de destino. O Azure Ative Directory (AD) suporta este padrão através do seu serviço Application Proxy e integrações com outras soluções de controlador de rede. 

Na nossa solução, o Application Proxy fornece acesso remoto à aplicação, autentica o utilizador e passa os cabeçalhos exigidos pela aplicação. 

## <a name="use-when"></a>Utilizar quando

Os utilizadores remotos precisam de uma única sintenção (SSO) para aplicações no local que exijam autenticação baseada em cabeçalho.

![Autenticação baseada em cabeçalho de imagem arquitetónica](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador:** Acede a aplicações antigas servidas pela Application Proxy.

* **Web browser**: O componente com o que o utilizador interage para aceder ao URL externo da aplicação.

* **Azure AD**: Autentica o utilizador. 

* **Serviço de procuração de aplicação**: Atua como procuração inversa para enviar o pedido do utilizador para a aplicação no local. Reside em Azure AD e também pode impor quaisquer políticas de acesso condicional.

* **Conector Proxy de aplicação**: Instalados no local nos servidores do Windows para fornecer conectividade às aplicações. Só usa ligações de saída. Devolve a resposta ao Azure AD.

* **Aplicações legados**: Aplicações que recebem pedidos de utilizador da Application Proxy. A aplicação legacy recebe os cabeçalhos HTTP necessários para configurar uma sessão e devolver uma resposta. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Implementar autenticação baseada em cabeçalho com Azure AD

* [Adicione uma aplicação no local para acesso remoto através de Aplicação Proxy em Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)  

* [Autenticação baseada em cabeçalho para início de sessão único com o Proxy de Aplicações e PingAccess](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-ping-access) 

* [Proteger aplicações legadas com controladores e redes de entrega de aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)

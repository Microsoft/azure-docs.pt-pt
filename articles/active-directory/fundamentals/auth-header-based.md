---
title: Autenticação baseada em cabeçalho com Diretório Ativo Azure
description: Orientação arquitetónica para a obtenção de autenticação baseada em cabeçalho com diretório Azure Ative.
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
ms.openlocfilehash: bb54410fb314376b68d7297a3452f0990762343d
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577163"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Autenticação baseada em cabeçalho com Diretório Ativo Azure

As aplicações antigas geralmente utilizam a autenticação baseada no cabeçalho. Neste cenário, um utilizador (ou autor de mensagens) autentica-se numa solução de identidade intermediária. A solução intermediária autentica o utilizador e propaga os cabeçalhos necessários do Protocolo de Transferência de Hipertexto (HTTP) para o serviço web de destino. O Azure Ative Directory (AD) suporta este padrão através do seu serviço Application Proxy e integrações com outras soluções de controlador de rede. 

Na nossa solução, o Application Proxy fornece acesso remoto à aplicação, autentica o utilizador e passa os cabeçalhos exigidos pela aplicação. 

## <a name="use-when"></a>Utilizar quando

Os utilizadores remotos precisam de uma única sintenção (SSO) para aplicações no local que exijam autenticação baseada em cabeçalho.

![Autenticação baseada em cabeçalho de imagem arquitetónica](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador:** Acede a aplicações antigas servidas pela Application Proxy.

* **Web browser** : O componente com o que o utilizador interage para aceder ao URL externo da aplicação.

* **Azure AD** : Autentica o utilizador. 

* **Serviço de procuração de aplicação** : Atua como procuração inversa para enviar o pedido do utilizador para a aplicação no local. Reside em Azure AD e também pode impor quaisquer políticas de acesso condicional.

* **Conector Proxy de aplicação** : Instalados no local nos servidores do Windows para fornecer conectividade às aplicações. Só usa ligações de saída. Devolve a resposta ao Azure AD.

* **Aplicações legados** : Aplicações que recebem pedidos de utilizador da Application Proxy. A aplicação legacy recebe os cabeçalhos HTTP necessários para configurar uma sessão e devolver uma resposta. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Implementar autenticação baseada em cabeçalho com Azure AD

* [Adicione uma aplicação no local para acesso remoto através de Aplicação Proxy em Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)  

* [Autenticação baseada em cabeçalho para início de sessão único com o Proxy de Aplicações e PingAccess](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-ping-access) 

* [Proteger aplicações legadas com controladores e redes de entrega de aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)

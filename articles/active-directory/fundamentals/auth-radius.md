---
title: Autenticação RADIUS com Diretório Ativo Azure
description: Orientação arquitetónica para a obtenção da autenticação RADIUS com o Diretório Ativo Azure.
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
ms.openlocfilehash: 7ec8062eb864c877b0f3659ca2dd8f103e935071
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836789"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Autenticação RADIUS com Diretório Ativo Azure

Serviço de Acesso ao Utilizador de Autenticação Remota (RADIUS) é um protocolo de rede que assegura uma rede, permitindo a autenticação centralizada e a autorização dos utilizadores com acesso a marcação. Muitas aplicações ainda dependem do protocolo RADIUS para autenticar os utilizadores.

O Microsoft Windows Server tem uma função chamada Network Policy Server (NPS), que pode funcionar como um servidor RADIUS e suportar a autenticação RADIUS.

O Azure Ative Directory (Azure AD) permite a autenticação de vários fatores com sistemas baseados em RADIUS. Se um cliente quiser aplicar a autenticação multi-factor Azure AD a qualquer uma das cargas de trabalho de RADIUS anteriormente mencionadas, pode instalar a extensão NPS de autenticação multi-factor Azure AD no seu servidor NPS do Windows. 

O servidor NPS do Windows autentica as credenciais de um utilizador contra o Ative Directory e, em seguida, envia o pedido de autenticação multi-factor para o Azure. O utilizador recebe então um desafio no seu autenticador móvel. Uma vez bem sucedido, a aplicação do cliente é permitida a ligação ao serviço. 

## <a name="use-when"></a>Utilize se: 

É necessário adicionar autenticação multi-factor a aplicações como
* uma Rede Privada Virtual (VPN)
* Acesso Wi-Fi
* Gateway remoto de desktop (RDG)
* Infraestrutura virtual de ambiente de trabalho (VDI)
* Quaisquer outros que dependam do protocolo RADIUS para autenticar os utilizadores no serviço. 

> [!NOTE]
> Em vez de confiar no RADIUS e na extensão NPS de autenticação multi-factor Azure AD para aplicar a autenticação multi-factor Azure AD às cargas de trabalho VPN, recomendamos que atualize as suas VPN's para SAML e federa diretamente a sua VPN com Azure AD. Isto dá à sua VPN toda a extensão da proteção AZure AD, incluindo acesso condicional, autenticação multi-factor, conformidade do dispositivo e proteção de identidade.

![diagrama arquitetónico](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>Componentes do sistema 

* **Pedido de cliente (cliente VPN)**: Envia pedido de autenticação ao cliente RADIUS.

* **RADIUS cliente**: Converte os pedidos da aplicação do cliente e envia-os para o servidor RADIUS que tem a extensão NPS instalada.

* **RADIUS servidor**: Conecta-se com o Ative Directory para efetuar a autenticação primária para o pedido RADIUS. Após o sucesso, passa o pedido para a extensão NPS de autenticação multi-factor Azure AD.

* **Extensão NPS**: Desencadeia um pedido de autenticação multi-factor Azure AD para uma autenticação secundária. Se for bem sucedido, a extensão NPS completa o pedido de autenticação fornecendo ao servidor RADIUS fichas de segurança que incluem a reclamação de autenticação multi-factor, emitida pelo Serviço de Token de Segurança da Azure.

* **Autenticação multi-factor Azure AD**: Comunica com a Azure AD para recuperar os dados do utilizador e efetua uma autenticação secundária utilizando um método de verificação configurado pelo utilizador.

## <a name="implement-radius-with-azure-ad"></a>Implementar RADIUS com Azure AD 

* [Fornecer capacidades de autenticação multi-factor Azure AD utilizando NPS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension) 

* [Configure a extensão NPS de autenticação multi-factor Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-advanced) 

* [VPN com Autenticação Multi-Factor Azure AD usando a extensão NPS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-vpn) 

  
‎ 

 

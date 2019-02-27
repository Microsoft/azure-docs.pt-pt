---
title: O Azure AD Connect e Federação | Documentos da Microsoft
description: Esta página é o local central de toda a documentação sobre operações do AD FS que utilizam o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67ae5d2661371c256f753d05eb496d2cd53a0017
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869552"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e a federação
O Azure Active Directory (Azure AD) Connect permite que configura a Federação com locais serviços de Federação do Active Directory (AD FS) e o Azure AD. Com o Federação início de sessão, pode permitir aos utilizadores iniciar sessão para serviços do Azure baseada no AD com as palavras-passe no local – e, embora na rede da empresa, sem ter de introduzir as palavras-passe novamente. Ao utilizar a opção de federação com o AD FS, pode implementar uma nova instalação do AD FS ou pode especificar uma instalação existente num farm do Windows Server 2012 R2.

Este tópico é a página para obter informações sobre funcionalidades relacionadas com a Federação para o Azure AD Connect. Ele apresenta uma lista de links para todos os tópicos relacionados. Para obter ligações para o Azure AD Connect, consulte [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: tópicos de Federação
| Tópico | O que abrange e quando lê-lo |
|:--- |:--- |
| **Opções de início de sessão de utilizador do Azure AD Connect** | |
| [Compreender as opções de início de sessão do utilizador](plan-connect-user-signin.md) |Saiba mais sobre as várias opções de início de sessão do utilizador e como elas afetam a experiência de utilizador de início de sessão do Azure. |
| **Instalar o AD FS com o Azure AD Connect** | |
| [Pré-requisitos](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Consulte os pré-requisitos para uma instalação do AD FS com êxito através do Azure AD Connect. |
| [Configurar um farm do AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Instale um novo farm do AD FS utilizando o Azure AD Connect. |
| [Federar com o Azure AD com o ID de início de sessão alternativo](how-to-connect-fed-management.md#alternateid) | Configurar a Federação com o ID de início de sessão alternativo  |
| **Modificar a configuração do AD FS** | |
| [Repare a confiança](how-to-connect-fed-management.md#repairthetrust) |Reparar a confiança atual no local do AD FS e o Office 365/Azure. |
| [Adicionar um novo servidor do AD FS](how-to-connect-fed-management.md#addadfsserver) |Expanda um farm do AD FS com um servidor do AD FS adicional após a instalação inicial. |
| [Adicionar um novo servidor do WAP do AD FS](how-to-connect-fed-management.md#addwapserver) |Expanda um farm do AD FS com um servidor de Proxy de aplicações Web (WAP) adicionais após a instalação inicial. |
| [Adicionar um novo domínio federado](how-to-connect-fed-management.md#addfeddomain) |Adicione outro domínio Federado com o Azure AD. |
| [Atualizar o certificado SSL](how-to-connect-fed-ssl-update.md)| Atualize o certificado SSL para um farm do AD FS. |
| [Renovar certificados de Federação para o Office 365 e o Azure AD](how-to-connect-fed-o365-certs.md)|Renove o certificado do Office 365 com o Azure AD.|
| **Outra configuração de Federação** | |
| [Federar várias instâncias do Azure AD com uma instância única do AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federar vários Azure AD com o farm do AD FS único| 
| [Adicionar uma ilustração/logótipo de empresa personalizado](how-to-connect-fed-management.md#customlogo) |Modificar a experiência de início de sessão, especificando o logotipo personalizado que é apresentado na página de início de sessão do AD FS. |
| [Adicione uma descrição de início de sessão](how-to-connect-fed-management.md#addsignindescription) |Altere a descrição de início de sessão na página de início de sessão do AD FS. |
| [Modificar regras de afirmações do AD FS](how-to-connect-fed-management.md#modclaims) |Modificar ou adicionar regras de afirmações do AD FS que correspondem à configuração de sincronização do Azure AD Connect. |


## <a name="additional-resources"></a>Recursos adicionais
* [Federar dois do Azure AD com AD FS único](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Implementação do AD FS no Azure](how-to-connect-fed-azure-adfs.md)
* [Implementação de FS AD geográficos cruzados de elevada disponibilidade no Azure com o Gestor de tráfego do Azure](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

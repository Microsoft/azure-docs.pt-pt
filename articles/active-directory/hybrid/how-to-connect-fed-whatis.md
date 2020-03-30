---
title: Azure AD Connect e federação Microsoft Docs
description: Esta página é a localização central para toda a documentação relativa às operações AD FS que utilizam o Azure AD Connect.
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
ms.openlocfilehash: 0518c58abf156c718ee083ffadb0ef8e0a590252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331531"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e a federação
O Azure Ative Directory (Azure AD) Connect permite configurar a federação com serviços da Federação de Diretório Ativo (AD FS) e Azure AD. Com o início de sessão da federação, pode permitir que os utilizadores acedam aos serviços baseados em AD Azure com as suas palavras-passe no local —- e, enquanto estão na rede corporativa, sem terem de voltar a introduzir as suas palavras-passe. Ao utilizar a opção da federação com AD FS, pode implementar uma nova instalação de AD FS, ou pode especificar uma instalação existente numa quinta do Windows Server 2012 R2.

Este tema é a casa para informações sobre funcionalidades relacionadas com a federação para o Azure AD Connect. Ele lista links para todos os tópicos relacionados. Para ligações ao Azure AD Connect, consulte [Integrando as suas identidades no local com o Diretório Ativo Azure](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: temas da federação
| Tópico | O que cobre e quando lê-lo |
|:--- |:--- |
| **Opções de entrada de utilizador azure AD Connect** | |
| [Compreender as opções de inscrição do utilizador](plan-connect-user-signin.md) |Saiba mais sobre várias opções de entrada no utilizador e como afetam a experiência de utilizador de entrada do Azure. |
| **Instale AD FS utilizando o Azure AD Connect** | |
| [Pré-requisitos](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Consulte os pré-requisitos para uma instalação AD FS bem sucedida via Azure AD Connect. |
| [Configure uma fazenda AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Instale uma nova quinta AD FS utilizando o Azure AD Connect. |
| [Federato com Azure AD usando identificação de login alternativo](how-to-connect-fed-management.md#alternateid) | Configure federação usando id de login alternativo  |
| **Modificar a configuração AD FS** | |
| [Reparar a confiança](how-to-connect-fed-management.md#repairthetrust) |Repare a confiança atual entre as Instalações AD FS e Office 365/Azure. |
| [Adicione um novo servidor AD FS](how-to-connect-fed-management.md#addadfsserver) |Expanda uma quinta AD FS com um servidor AD FS adicional após a instalação inicial. |
| [Adicione um novo servidor AD FS WAP](how-to-connect-fed-management.md#addwapserver) |Expanda uma quinta AD FS com um servidor adicional de Proxy de Aplicação Web (WAP) após a instalação inicial. |
| [Adicione um novo domínio federado](how-to-connect-fed-management.md#addfeddomain) |Adicione outro domínio a ser federado com Azure AD. |
| [Atualizar o certificado TLS/SSL](how-to-connect-fed-ssl-update.md)| Atualize o certificado TLS/SSL para uma exploração aD FS. |
| [Renovar certificados da federação para o Office 365 e Azure AD](how-to-connect-fed-o365-certs.md)|Renove o certificado O365 com a Azure AD.|
| **Outra configuração da federação** | |
| [Federar várias instâncias do Azure AD com uma instância única do AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federate multi-AD Azure com única fazenda AD FS| 
| [Adicione um logotipo/ilustração personalizado da empresa](how-to-connect-fed-management.md#customlogo) |Modifique a experiência de login especificando o logótipo personalizado que é mostrado na página de login AD FS. |
| [Adicione uma descrição de sessão](how-to-connect-fed-management.md#addsignindescription) |Altere a descrição de inscrição na página de inscrição da AD FS. |
| [Modificar as regras de reclamação da AD FS](how-to-connect-fed-management.md#modclaims) |Modifique ou adicione regras de reclamação em AD FS que correspondam à configuração de sincronização de Ligação AD Azur. |


## <a name="additional-resources"></a>Recursos adicionais
* [Federating dois Azure AD com aD FS single](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Implementação do AD FS no Azure](how-to-connect-fed-azure-adfs.md)
* [Implantação de AD FS cross-geographic de alta disponibilidade em Azure com Gestor de Tráfego Azure](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

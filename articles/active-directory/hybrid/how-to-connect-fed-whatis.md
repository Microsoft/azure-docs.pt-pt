---
title: Azure AD Connect e federação | Microsoft Docs
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
ms.topic: how-to
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a97142e0c512f4f95235ad08c94c852906d3efd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92095861"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e a federação
O Azure Ative Directory (Azure AD) Connect permite-lhe configurar a federação com os serviços da Federação De Diretório Ativo (AD FS) e Azure AD. Com o sign-in da federação, pode permitir que os utilizadores inscrevam-se nos serviços baseados em Azure com as suas senhas no local -- e, enquanto estiverem na rede corporativa, sem terem de introduzir novamente as suas palavras-passe. Ao utilizar a opção da federação com AD FS, pode implementar uma nova instalação de AD FS, ou pode especificar uma instalação existente numa quinta do Windows Server 2012 R2.

Este tópico é a casa para informações sobre funcionalidades relacionadas com a federação para Azure AD Connect. Lista ligações a todos os tópicos relacionados. Para obter links para Azure AD Connect, consulte [integrar as suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: tópicos da federação
| Tópico | O que cobre e quando lê-lo |
|:--- |:--- |
| **Azure AD Connect opções de inscrição do utilizador** | |
| [Compreender as opções de inscrição do utilizador](plan-connect-user-signin.md) |Saiba mais sobre várias opções de inscrição do utilizador e como afetam a experiência do utilizador de iniciar súmus. |
| **Instale O FS AD utilizando o Azure AD Connect** | |
| [Pré-requisitos](how-to-connect-install-custom.md#ad-fs-configuration-prerequisites) |Consulte os pré-requisitos para uma instalação AD FS bem sucedida via Azure AD Connect. |
| [Configure uma fazenda AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Instale uma nova quinta AD FS utilizando o Azure AD Connect. |
| [Federate com AD AZure usando ID de login alternativo](how-to-connect-fed-management.md#alternateid) | Federação de configuração usando iD de login alternativo  |
| **Modificar a configuração AD FS** | |
| [Reparar a confiança](how-to-connect-fed-management.md#repairthetrust) |Repare a confiança atual entre os AD FS e Microsoft 365/Azure. |
| [Adicione um novo servidor AD FS](how-to-connect-fed-management.md#addadfsserver) |Expanda uma quinta AD FS com um servidor AD FS adicional após a instalação inicial. |
| [Adicione um novo servidor AD FS WAP](how-to-connect-fed-management.md#addwapserver) |Expanda uma quinta AD FS com um servidor adicional de Proxy (WAP) de aplicação web após a instalação inicial. |
| [Adicione um novo domínio federado](how-to-connect-fed-management.md#addfeddomain) |Adicione outro domínio a ser federado com Azure AD. |
| [Atualizar o certificado TLS/SSL](how-to-connect-fed-ssl-update.md)| Atualize o certificado TLS/SSL para uma exploração AD FS. |
| [Renovar certificados da federação para Microsoft 365 e AZure AD](how-to-connect-fed-o365-certs.md)|Renove o seu certificado O365 com Azure AD.|
| **Outra configuração da federação** | |
| [Federar várias instâncias do Azure AD com uma instância única do AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federate múltiplos AD AZure com uma única fazenda AD FS| 
| [Adicione um logotipo/ilustração personalizado da empresa](how-to-connect-fed-management.md#customlogo) |Modifique a experiência de início de sposição especificando o logótipo personalizado que é mostrado na página de início de sposição da AD FS. |
| [Adicione uma descrição de inscrição](how-to-connect-fed-management.md#addsignindescription) |Altere a descrição de inscrição na página de sind.FS. |
| [Modificar as regras de reclamação da AD FS](how-to-connect-fed-management.md#modclaims) |Modifique ou adicione regras de reclamação em AD FS que correspondam à configuração de sincronização Azure AD Connect. |


## <a name="additional-resources"></a>Recursos adicionais
* [Federando dois AD Azure com um único AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Implementação do AD FS no Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)
* [Implantação de AD FS de alta disponibilidade em Azure com Azure Traffic Manager](/windows-server/identity/ad-fs/deployment/active-directory-adfs-in-azure-with-azure-traffic-manager)
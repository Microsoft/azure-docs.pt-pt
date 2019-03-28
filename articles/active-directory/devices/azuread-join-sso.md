---
title: Como funciona o SSO para recursos no local no Azure AD dispositivos associados ao | Documentos da Microsoft
description: Saiba como configurar dispositivos associados ao Azure Active Directory híbrido.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45941de6a90a5824ebc1e5d31b18b68f5fd9d493
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520552"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Como funciona o SSO para recursos no local no Azure AD dispositivos associados

Provavelmente não é uma surpresa que um dispositivo associado ao Azure Active Directory (Azure AD) dá-lhe uma experiência de início de sessão único (SSO) para aplicações de cloud do seu inquilino. Se o ambiente tiver um diretório de Active Directory no local (AD), pode estender a experiência SSO nestes dispositivos ao mesmo.

Este artigo explica como isso funciona.

## <a name="how-it-works"></a>Como funciona 

Uma vez que precisa se lembrar de apenas um nome de utilizador único e a palavra-passe, o SSO simplifica o acesso aos seus recursos e melhora a segurança do seu ambiente. Com um dispositivo associado ao Azure AD, os utilizadores já tem uma experiência SSO para aplicações na cloud no seu ambiente. Se o ambiente tiver um Azure AD e no local AD, que provavelmente pretende expandir o âmbito da sua experiência SSO para aplicações de linha de negócio (LOB) no local, partilhas de ficheiros e impressoras.  


Dispositivos do Azure AD associado ao não tem conhecimento sobre o ambiente ambiente AD porque estes não são associados a ele. No entanto, pode fornecer informações adicionais sobre o ambiente AD para estes dispositivos com o Azure AD Connect.
Um ambiente que tem ambos, um Azure AD e no local AD, também é conhecido tem o ambiente híbrido. Se tiver um ambiente híbrido, é provável que já tem o Azure AD Connect, implementado para sincronizar as informações de identidade no local para a cloud. Como parte do processo de sincronização, o Azure AD Connect sincroniza informações de domínio no local para o Azure AD. Quando um utilizador inicia sessão com um Azure AD associado a um dispositivo num ambiente híbrido:

1. O Azure AD envia que o nome do domínio no local do utilizador é membro de volta para o dispositivo. 

2. O serviço de autoridade (LSA) de segurança local permite a autenticação Kerberos no dispositivo.

Durante uma tentativa de acesso a um recurso no domínio de no local do usuário, o dispositivo:

1. Usa as informações de domínio para localizar um controlador de domínio (DC). 

2. Envia no local as credenciais de utilizador e informações de domínio para o controlador de domínio localizado para obter o utilizador autenticado.

3. Recebe um Kerberos [concessão de permissões (TGT)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets) que é utilizado para aceder aos recursos associados ao AD.

Todas as aplicações que estão configuradas para **autenticação integrada do Windows** facilmente obter SSO quando um utilizador tenta aceder aos mesmos.  

Windows Hello para empresas requer configuração adicional para ativar o SSO no local de um dispositivo associado ao Azure AD. Para obter mais informações, consulte [configurar o Azure AD associado a um dispositivos para o local início de sessão único em utilizar o Windows Hello para empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>O que obtém

Com o SSO, num Azure AD associado a um dispositivo que pode: 

- Aceder a um caminho UNC num servidor de membro do AD

- Aceder a um servidor de web de membro AD configurado para a segurança integrada do Windows 



Se pretender gerir o ambiente AD a partir de um dispositivo Windows, instale o [remota Server Administration Tools para Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=45520).

Pode utilizar:

- O Active Directory utilizadores e computadores (ADUC) snap-in para administrar todos os objetos do AD. No entanto, tem de especificar o domínio que deseja se conectar manualmente.

- Snap-in do DHCP para administrar um servidor DHCP associado ao AD. No entanto, terá de especificar o nome do servidor DHCP ou o endereço.

 
## <a name="what-you-should-know"></a>O que deve saber

Poderá ter de ajustar sua [filtragem baseada em domínio](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) no Azure AD Connect, para assegurar que os dados sobre os domínios necessários estão sincronizados.

Aplicações e recursos que dependem do Active Directory, autenticação de máquina não funcionam porque dispositivos associados ao Azure AD não tem um objeto de computador no AD. 

Não é possível partilhar ficheiros com outros utilizadores num dispositivo associado ao AD do Azure.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte [o que é a gestão de dispositivos no Azure Active Directory?](overview.md) 

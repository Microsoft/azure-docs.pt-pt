---
title: Como o SSO para recursos locais funciona em dispositivos adicionados ao Azure AD | Microsoft Docs
description: Saiba como configurar dispositivos associados ao Azure Active Directory híbrido.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15ccbc568a2986fbb2a547eb958b5e853c8c9f77
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154827"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Como o SSO para recursos locais funciona em dispositivos ingressados no Azure AD

Provavelmente não é uma surpresa que um dispositivo ingressado no Azure Active Directory (Azure AD) forneça a você uma experiência de logon único (SSO) para os aplicativos de nuvem do seu locatário. Se o seu ambiente tiver um Active Directory local (AD), você poderá estender a experiência de SSO nesses dispositivos para ele.

Este artigo explica como isso funciona.

## <a name="prerequisites"></a>Pré-requisitos

 Se as máquinas Unidas do Azure AD não estiverem conectadas à rede da sua organização, uma VPN ou outra infraestrutura de rede será necessária. O SSO local requer comunicação de linha de visão com seus controladores de domínio AD DS locais.

## <a name="how-it-works"></a>Como funciona 

Como você precisa se lembrar apenas de um único nome de usuário e senha, o SSO simplifica o acesso aos seus recursos e melhora a segurança do seu ambiente. Com um dispositivo ingressado no Azure AD, os usuários já têm uma experiência de SSO para os aplicativos de nuvem em seu ambiente. Se o seu ambiente tiver um AD do Azure e um AD local, provavelmente você desejará expandir o escopo de sua experiência de SSO para seus aplicativos de LOB (linha de negócios) locais, compartilhamentos de arquivos e impressoras.  

Os dispositivos ingressados no Azure AD não têm conhecimento sobre seu ambiente do AD local porque eles não são associados a ele. No entanto, você pode fornecer informações adicionais sobre seu AD local para esses dispositivos com Azure AD Connect.

Um ambiente que tem ambos, um AD do Azure e um AD local, também é conhecido por um ambiente híbrido. Se você tiver um ambiente híbrido, é provável que você já tenha Azure AD Connect implantado para sincronizar suas informações de identidade local para a nuvem. Como parte do processo de sincronização, Azure AD Connect sincroniza informações de usuário local para o Azure AD. Quando um usuário entra em um dispositivo ingressado no Azure AD em um ambiente híbrido:

1. O Azure AD envia o nome do domínio local para o qual o usuário é membro de volta para o dispositivo. 
1. O serviço de autoridade de segurança local (LSA) habilita a autenticação Kerberos no dispositivo.

Durante uma tentativa de acesso a um recurso no domínio local do usuário, o dispositivo:

1. Usa as informações de domínio para localizar um controlador de domínio (DC). 
1. Envia as informações de domínio local e as credenciais do usuário para o DC localizado para que o usuário seja autenticado.
1. Recebe um [tíquete de concessão de tíquete (TGT)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets) Kerberos que é usado para acessar recursos ingressados no AD.

Todos os aplicativos configurados para **autenticação integrada do Windows** obtêm o SSO diretamente quando um usuário tenta acessá-los.  

O Windows Hello para empresas requer configuração adicional para habilitar o SSO local de um dispositivo ingressado no Azure AD. Para obter mais informações, consulte [configurar dispositivos ingressados no Azure ad para logon único local usando o Windows Hello para empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>O que recebe

Com o SSO, em um dispositivo ingressado no Azure AD, você pode: 

- Acessar um caminho UNC em um servidor membro do AD
- Acessar um servidor Web membro do AD configurado para segurança integrada do Windows 

Se você quiser gerenciar seu AD local de um dispositivo Windows, instale o [ferramentas de administração de servidor remoto para Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Você pode usar:

- O snap-in Active Directory usuários e computadores (ADUC) para administrar todos os objetos do AD. No entanto, você precisa especificar o domínio ao qual deseja se conectar manualmente.
- O snap-in do DHCP para administrar um servidor DHCP ingressado no AD. No entanto, talvez seja necessário especificar o nome ou endereço do servidor DHCP.
 
## <a name="what-you-should-know"></a>O que deve saber

Talvez seja necessário ajustar a [filtragem baseada em domínio](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) no Azure ad Connect para garantir que os dados sobre os domínios necessários sejam sincronizados.

Os aplicativos e recursos que dependem de Active Directory autenticação de máquina não funcionam porque os dispositivos ingressados no Azure AD não têm um objeto de computador no AD. 

Você não pode compartilhar arquivos com outros usuários em um dispositivo ingressado no Azure AD.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [o que é o gerenciamento de dispositivos no Azure Active Directory?](overview.md) 

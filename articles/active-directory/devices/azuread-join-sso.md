---
title: Como a SSO para os recursos no local funciona em dispositivos aderes à Azure AD [ AD] Microsoft Docs
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
ms.openlocfilehash: f9d8c0cd803424e117bd4dc7a3382b7b32df2d05
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78672709"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Como a SSO para os recursos no local funciona em dispositivos ligados à Azure AD

Provavelmente não é surpresa que um dispositivo azure Ative Directory (Azure AD) lhe dê uma única experiência de inscrição (SSO) para as aplicações em nuvem do seu inquilino. Se o seu ambiente tiver um Diretório Ativo no local (AD), pode estender a experiência SSO nestes dispositivos a ele.

Este artigo explica como isto funciona.

## <a name="prerequisites"></a>Pré-requisitos

 Se as máquinas aderes à Azure AD não estiverem ligadas à rede da sua organização, é necessária uma VPN ou outra infraestrutura de rede. No local, o SSO requer uma comunicação de linha de visão com os seus controladores de domínio AD DS no local.

## <a name="how-it-works"></a>Como funciona 

Como precisa de se lembrar apenas de um único nome de utilizador e palavra-passe, o SSO simplifica o acesso aos seus recursos e melhora a segurança do seu ambiente. Com um dispositivo Azure AD, os seus utilizadores já têm uma experiência SSO nas aplicações em nuvem no seu ambiente. Se o seu ambiente tiver um Anúncio Azure e um Anúncio no local, provavelmente pretende expandir o âmbito da sua experiência SSO para as suas aplicações line of business (LOB) no local, partilhas de ficheiros e impressoras.

Os dispositivos aderes à Azure AD não têm conhecimento do seu ambiente ad-in-local porque não estão unidos a ele. No entanto, pode fornecer informações adicionais sobre o seu AD no local a estes dispositivos com o Azure AD Connect.

Um ambiente que tem ambos, um Anúncio Azure e um Anúncio no local, também é conhecido tem ambiente híbrido. Se tiver um ambiente híbrido, é provável que já tenha o Azure AD Connect implantado para sincronizar as suas informações de identidade no local para a nuvem. Como parte do processo de sincronização, o Azure AD Connect sincroniza as informações dos utilizadores no local para a AD Azure. Quando um utilizador entra num dispositivo Azure AD num ambiente híbrido:

1. A Azure AD envia o nome do domínio no local que o utilizador é um membro de volta para o dispositivo.
1. O serviço da autoridade de segurança local (LSA) permite a autenticação kerberos no dispositivo.

Durante uma tentativa de acesso a um recurso que solicita a Kerberos no ambiente do utilizador no local, o dispositivo:

1. Envia as informações de domínio no local e as credenciais de utilizador para o DC localizado para autenticar o utilizador.
1. Recebe um [Bilhete Kerberos de concessão de bilhetes (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) que é usado para aceder a recursos ad-joined. Se a tentativa de obter o TGT para o domínio de ligação AAD falhar (o tempo de saída relacionado do DCLocator pode causar um atraso), as entradas do Credential Manager são tentadas, ou o utilizador pode receber uma autenticação popup solicitando credenciais para o recurso alvo.

Todas as aplicações configuradas para **autenticação integrada** no Windows obtêm sem problemas quando um utilizador tenta aceder às mesmas.

O Windows Hello for Business requer uma configuração adicional para ativar o SSO no local a partir de um dispositivo azure AD. Para mais informações, consulte o [Configure Azure AD juntou-se a dispositivos para on-in-premises single-sign on usando o Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>O que obtém

Com o SSO, num dispositivo azure ad uniu-se: 

- Aceda a um caminho unc em um servidor membro da AD
- Aceda a um servidor web membro da AD configurado para segurança integrada no Windows 

Se pretender gerir o seu AD no local a partir de um dispositivo Windows, instale as Ferramentas de Administração do [Servidor Remoto para o Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Pode utilizar:

- Os Utilizadores e Computadores de Diretório Ativo (ADUC) encaixem para administrar todos os objetos AD. No entanto, tem de especificar o domínio a que pretende ligar manualmente.
- O snap-in dHCP para administrar um servidor DHCP filiado em AD. No entanto, poderá ter de especificar o nome ou endereço do servidor DHCP.
 
## <a name="what-you-should-know"></a>O que deve saber

Pode ter de ajustar a [filtragem baseada](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) em domínios no Azure AD Connect para garantir que os dados sobre os domínios necessários são sincronizados.

Aplicações e recursos que dependem da autenticação da máquina de Diretório Ativo não funcionam porque o Azure AD juntou dispositivos não tem um objeto de computador em AD. 

Não é possível partilhar ficheiros com outros utilizadores num dispositivo ligado ao Azure AD.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte O que é a [gestão do dispositivo no Diretório Ativo do Azure?](overview.md) 

---
title: Como funciona o SSO para os recursos no local em dispositivos aderidos à Azure AD Microsoft Docs
description: Saiba como configurar dispositivos associados ao Azure Active Directory híbrido.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 149b01401cd6feb7610510efeb1ad9a3c69f3ecf
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024048"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Como a SSO para os recursos no local funciona em dispositivos aderidos a Azure AD

Provavelmente não é surpresa que um dispositivo aderido ao Azure Ative Directory (Azure AD) lhe dê uma única experiência de sign-on (SSO) às aplicações em nuvem do seu inquilino. Se o seu ambiente tiver um Ative Directory (AD) no local, pode alargar a experiência SSO nestes dispositivos a recursos e aplicações que também dependem de AD no local. 

Este artigo explica como isto funciona.

## <a name="prerequisites"></a>Pré-requisitos

 Se as máquinas aderidas a Azure AD não estiverem ligadas à rede da sua organização, é necessária uma VPN ou outra infraestrutura de rede. No local, o SSO requer uma comunicação de linha de visão com os controladores de domínio AD DS no local.

## <a name="how-it-works"></a>Como funciona 

Com um dispositivo AZure AD ligado, os seus utilizadores já têm uma experiência SSO para as aplicações na nuvem no seu ambiente. Se o seu ambiente tiver um AD Azure e um AD no local, talvez queira expandir o âmbito da sua experiência SSO para as suas aplicações line of business (LOB) no local, partilhas de ficheiros e impressoras.

Os dispositivos azure AD não têm conhecimento do seu ambiente de AD no local porque não estão unidos a ele. No entanto, pode fornecer informações adicionais sobre o seu AD no local para estes dispositivos com Azure AD Connect.

Um ambiente que tem um AD Azure e um AD no local, também é conhecido tem ambiente híbrido. Se tiver um ambiente híbrido, é provável que já tenha o Azure AD Connect implantado para sincronizar as suas informações de identidade no local para a nuvem. Como parte do processo de sincronização, o Azure AD Connect sincroniza as informações do utilizador no local para a Azure AD. Quando um utilizador assina um dispositivo AZure AD num ambiente híbrido:

1. A AZure AD envia os detalhes do domínio do utilizador no local de volta para o dispositivo, juntamente com o [Token De Atualização Primária](concept-primary-refresh-token.md)
1. O serviço da Autoridade de Segurança Local (LSA) permite a autenticação da Kerberos e da NTLM no dispositivo.

Durante uma tentativa de acesso a um recurso que solicita a Kerberos ou a NTLM no ambiente do utilizador no local, o dispositivo:

1. Envia as informações de domínio no local e credenciais de utilizador para o DC localizado para autenticar o utilizador.
1. Recebe um [bilhete kerberos de concessão de bilhetes (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) ou um token NTLM com base no protocolo que o recurso no local ou suporte de aplicação. Se a tentativa de obter o token Kerberos TGT ou NTLM para o domínio falhar (o tempo limite de DCLocator relacionado pode causar um atraso), as entradas do Credential Manager são tentadas, ou o utilizador pode receber um popup de autenticação solicitando credenciais para o recurso alvo.

Todas as aplicações configuradas para **autenticação integrada no Windows** obtêm SSO de forma perfeita quando um utilizador tenta aceder às suas.

O Windows Hello for Business requer uma configuração adicional para ativar o SSO no local a partir de um dispositivo azure AD. Para obter mais informações, consulte [dispositivos de ad Configure Azure Ad para o single-sign on using Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>O que obtém

Com sSO, num dispositivo azure AD ligado pode: 

- Aceda a um caminho unc em um servidor de membros da AD
- Aceda a um servidor web de membros AD configurado para segurança integrada no Windows 

Se pretender gerir o seu AD no local a partir de um dispositivo Windows, instale as Ferramentas de Administração do Servidor Remoto para o [Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Pode utilizar:

- O encaixe ativo dos Utilizadores e Computadores do Diretório (ADUC) para administrar todos os objetos AD. No entanto, tem de especificar o domínio a que pretende ligar manualmente.
- O snap-in DHCP para administrar um servidor DHCP ad-joined. No entanto, poderá ter de especificar o nome ou endereço do servidor DHCP.
 
## <a name="what-you-should-know"></a>O que deve saber

Poderá ter de ajustar a [sua filtragem baseada em domínios](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) no Azure AD Connect para garantir que os dados sobre os domínios necessários sejam sincronizados.

As aplicações e recursos que dependem da autenticação da máquina ative directory não funcionam porque os dispositivos azure AD não têm um objeto de computador em AD. 

Não é possível partilhar ficheiros com outros utilizadores num dispositivo ad azure.

## <a name="next-steps"></a>Próximos passos

Para mais informações, veja [o que é a gestão do dispositivo no Diretório Ativo Azure?](overview.md) 

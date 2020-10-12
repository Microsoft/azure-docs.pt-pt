---
title: Identidade Híbrida exigia portas e protocolos - Azure / Microsoft Docs
description: Esta página é uma página de referência técnica para portas que são necessárias para estar abertas para Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da318840426d1c0b94eab06b89ff3152df9d26fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80331099"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Portas e Protocolos Necessários para a Identidade Híbrida
O documento a seguir é uma referência técnica sobre as portas e protocolos necessários para a implementação de uma solução de identidade híbrida. Utilize a seguinte ilustração e consulte a tabela correspondente.

![O que é o Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1 - Azure AD Connect e On-in-in
Esta tabela descreve as portas e protocolos necessários para a comunicação entre o servidor AZure AD Connect e o AD no local.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Pesquisas de DNS na floresta de destino. |
| Kerberos |88 (TCP/UDP) |A autenticação de Kerberos para a floresta da AD. |
| MS-RPC |135 (TCP) |Utilizado durante a configuração inicial do assistente Azure AD Connect quando se liga à floresta AD, e também durante a sincronização da Palavra-Passe. |
| LDAP |389 (TCP/UDP) |Usado para importação de dados da AD. Os dados são encriptados com o Sinal de Kerberos & Selo. |
| SMB | 445 (TCP) |Usado pela Seamless SSO para criar uma conta de computador na floresta AD. |
| LDAP/SSL |636 (TCP/UDP) |Usado para importação de dados da AD. A transferência de dados é assinada e encriptada. Só é utilizado se estiver a utilizar o TLS. |
| RPC |49152- 65535 (Porta RPC alta aleatória)(TCP) |Utilizado durante a configuração inicial do Azure AD Connect quando se liga às florestas de AD e durante a sincronização da Palavra-Passe. Consulte [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)e [KB224196](https://support.microsoft.com/kb/224196) para mais informações. |
|WinRM  | 5985 (TCP) |Só utilizado se estiver a instalar AD FS com gMSA por Azure AD Connect Wizard|
|Serviços Web AD DS | 9389 (TCP) |Só utilizado se estiver a instalar AD FS com gMSA por Azure AD Connect Wizard |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 - Azure AD Connect e AZure AD
Esta tabela descreve as portas e protocolos necessários para a comunicação entre o servidor AZure AD Connect e o Azure AD.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP) |Utilizado para descarregar CRLs (Listas de Revogação de Certificados) para verificar certificados TLS/SSL. |
| HTTPS |443(TCP) |Costumava sincronizar com a Azure AD. |

Para obter uma lista de URLs e endereços IP que precisa de abrir na sua firewall, consulte [as gamas de endereços UrLs e IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) e [e a conectividade de resolução de problemas Azure AD Connect](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabela 3 - Azure AD Connect e AD FS Federation Servers/WAP
Esta tabela descreve as portas e protocolos necessários para a comunicação entre o servidor Azure AD Connect e os servidores AD FS Federation/WAP.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP) |Utilizado para descarregar CRLs (Listas de Revogação de Certificados) para verificar certificados TLS/SSL. |
| HTTPS |443(TCP) |Costumava sincronizar com a Azure AD. |
| WinRM |5985 |Ouvinte winrm |

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 - SERVIDORES WAP e Federação
Esta tabela descreve as portas e protocolos necessários para a comunicação entre os servidores da Federação e os servidores WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP) |Usado para autenticação. |

## <a name="table-5---wap-and-users"></a>Tabela 5 - WAP e Utilizadores
Esta tabela descreve as portas e protocolos necessários para a comunicação entre os utilizadores e os servidores WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP) |Usado para autenticação do dispositivo. |
| TCP |49443 (TCP) |Usado para autenticação de certificados. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a & 6b - Autenticação pass-through com sinal único on (SSO) e password Hash Sync com signo único ligado (SSO)
As tabelas que se seguem descrevem as portas e protocolos necessários para a comunicação entre o Azure AD Connect e o Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a - Autenticação pass-through com SSO
|Protocolo|Número da Porta|Descrição
| --- | --- | ---
|HTTP|80|Ativar o tráfego HTTP de saída para validação de segurança, como o SSL. Também necessário para que a capacidade de atualização automática do conector funcione corretamente.
|HTTPS|443| Ativar o tráfego HTTPS de saída para operações como ativar e desativar a funcionalidade, registar conectores, descarregar atualizações de conector e lidar com todos os pedidos de inscrição do utilizador.

Além disso, o Azure AD Connect precisa de ser capaz de então fazer ligações IP diretas aos intervalos IP do [Centro de Dados Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6b - Password Hash Sync com SSO

|Protocolo|Número da Porta|Descrição
| --- | --- | ---
|HTTPS|443| Ativar o registo SSO (obrigatório apenas para o processo de registo SSO).

Além disso, o Azure AD Connect precisa de ser capaz de então fazer ligações IP diretas aos intervalos IP do [Centro de Dados Azure](https://www.microsoft.com/download/details.aspx?id=41653). Mais uma vez, isto só é necessário para o processo de registo SSO.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Quadro 7a & 7b - Azure AD Connect Health agent para (AD FS/Sync) e Azure AD
As tabelas que se seguem descrevem os pontos finais, portas e protocolos necessários para a comunicação entre os agentes Azure AD Connect Health e a Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Quadro 7a - Portas e Protocolos para O Azure AD Connect Health agent para (AD FS/Sync) e Azure AD
Esta tabela descreve as seguintes portas e protocolos de saída que são necessários para a comunicação entre os agentes Azure AD Connect Health e Azure AD.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP) |Saída |
| Service Bus do Azure |5671 (TCP) |Saída |

A porta 5671 da Azure Service Bus já não é necessária para a versão mais recente do agente. A mais recente versão do agente Azure AD Connect Health apenas necessitava da porta 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - Pontos finais para O agente Azure AD Connect Health para (AD FS/Sync) e Azure AD
Para obter uma lista de pontos finais, consulte [a secção Requisitos do agente Azure AD Connect Health](how-to-connect-health-agent-install.md#requirements).


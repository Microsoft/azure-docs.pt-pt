---
title: Portas e protocolos necessários à identidade híbrida – Azure | Microsoft Docs
description: Esta página é uma página de referência técnica para as portas que precisam ser abertas para Azure AD Connect
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
ms.date: 08/02/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9277e35fceb382fbccd009e5bbfe63ce57b8361
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305182"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Portas e Protocolos Necessários para a Identidade Híbrida
O documento a seguir é uma referência técnica sobre as portas e os protocolos necessários para implementar uma solução de identidade híbrida. Use a ilustração a seguir e consulte a tabela correspondente.

![O que é o Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1-Azure AD Connect e AD local
Esta tabela descreve as portas e os protocolos necessários para a comunicação entre o servidor de Azure AD Connect e o AD local.

| Protocol | Portas | Descrição |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Pesquisas de DNS na floresta de destino. |
| Kerberos |88 (TCP/UDP) |Autenticação Kerberos para a floresta do AD. |
| MS-RPC |135 (TCP/UDP) |Usado durante a configuração inicial do assistente de Azure AD Connect quando ele é associado à floresta do AD e também durante a sincronização de senha. |
| LDAP |389 (TCP/UDP) |Usado para importação de dados do AD. Os dados são criptografados com o sinal Kerberos & lacre. |
| SMB | 445 (TCP/UDP) |Usado pelo SSO contínuo para criar uma conta de computador na floresta do AD. |
| LDAP/SSL |636 (TCP/UDP) |Usado para importação de dados do AD. A transferência de dados é assinada e criptografada. Usado somente se você estiver usando SSL. |
| RPC |49152-65535 (porta RPC alta aleatória) (TCP/UDP) |Usado durante a configuração inicial de Azure AD Connect quando ele é associado às florestas do AD e durante a sincronização de senha. Consulte [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)e [KB224196](https://support.microsoft.com/kb/224196) para obter mais informações. |
|WinRM  | 5985 (TCP/UDP) |Usado somente se você estiver instalando AD FS com o assistente de Azure AD Connect de gMSA|
|AD DS serviços Web | 9389 (TCP/UDP) |Usado somente se você estiver instalando AD FS com o assistente de Azure AD Connect de gMSA |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2-Azure AD Connect e Azure AD
Esta tabela descreve as portas e os protocolos necessários para a comunicação entre o Azure AD Connect Server e o Azure AD.

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Usado para baixar CRLs (listas de certificados revogados) para verificar certificados SSL. |
| HTTPS |443(TCP/UDP) |Usado para sincronizar com o Azure AD. |

Para obter uma lista de URLs e endereços IP que você precisa abrir em seu firewall, consulte [URLs do Office 365 e intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabela 3-Azure AD Connect e AD FS servidores de Federação/WAP
Esta tabela descreve as portas e os protocolos necessários para a comunicação entre o servidor de Azure AD Connect e AD FS servidores de Federação/WAP.  

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Usado para baixar CRLs (listas de certificados revogados) para verificar certificados SSL. |
| HTTPS |443(TCP/UDP) |Usado para sincronizar com o Azure AD. |
| WinRM |5985 |Ouvinte do WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4-WAP e servidores de Federação
Esta tabela descreve as portas e os protocolos necessários para a comunicação entre os servidores de Federação e os servidores WAP.

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Usado para autenticação. |

## <a name="table-5---wap-and-users"></a>Tabela 5-WAP e usuários
Esta tabela descreve as portas e os protocolos necessários para a comunicação entre os usuários e os servidores WAP.

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Usado para autenticação de dispositivo. |
| TCP |49443 (TCP) |Usado para autenticação de certificado. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a & 6B-autenticação de passagem com SSO (logon único) e sincronização de hash de senha com logon único (SSO)
As tabelas a seguir descrevem as portas e os protocolos necessários para a comunicação entre o Azure AD Connect e o Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a-autenticação de passagem com SSO
|Protocol|Número da Porta|Descrição
| --- | --- | ---
|HTTP|80|Habilite o tráfego HTTP de saída para validação de segurança, como SSL. Também é necessário que a funcionalidade de atualização automática do conector funcione corretamente.
|HTTPS|443| Habilite o tráfego HTTPS de saída para operações como habilitar e desabilitar o recurso, registrar conectores, baixar atualizações do conector e manipular todas as solicitações de entrada do usuário.

Além disso, Azure AD Connect precisa ser capaz de fazer conexões IP diretas com os [intervalos de IP de data center do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6B-sincronização de hash de senha com SSO

|Protocol|Número da Porta|Descrição
| --- | --- | ---
|HTTPS|443| Habilite o registro de SSO (necessário apenas para o processo de registro de SSO).

Além disso, Azure AD Connect precisa ser capaz de fazer conexões IP diretas com os [intervalos de IP de data center do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Novamente, isso só é necessário para o processo de registro de SSO.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a & 7B-Azure AD Connect Health Agent para (AD FS/sincronização) e Azure AD
As tabelas a seguir descrevem os pontos de extremidade, as portas e os protocolos necessários para a comunicação entre os agentes de Azure AD Connect Health e o Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a-portas e protocolos para Azure AD Connect Health agente para (AD FS/sincronização) e Azure AD
Esta tabela descreve as seguintes portas de saída e protocolos necessários para a comunicação entre os agentes de Azure AD Connect Health e o Azure AD.  

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443 (TCP) |Saída |
| Azure Service Bus |5671 (TCP) |Saída |

A porta 5671 do barramento de serviço do Azure não é mais necessária para a versão mais recente do agente. A versão mais recente do agente de Azure AD Connect Health só exigiu a porta 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7B-pontos de extremidade para o agente de Azure AD Connect Health para (AD FS/sincronização) e o Azure AD
Para obter uma lista de pontos de extremidade, consulte [a seção requisitos para o agente de Azure ad Connect Health](how-to-connect-health-agent-install.md#requirements).


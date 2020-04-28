---
title: Identidade Híbrida exigia portos e protocolos - Azure / Microsoft Docs
description: Esta página é uma página de referência técnica para portas que são necessárias para estar aberta para Azure AD Connect
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80331099"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Portas e Protocolos Necessários para a Identidade Híbrida
O seguinte documento é uma referência técnica sobre as portas e protocolos necessários para a implementação de uma solução de identidade híbrida. Utilize a seguinte ilustração e consulte a tabela correspondente.

![O que é o Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Quadro 1 - Azure AD Connect e On-premises AD
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor Azure AD Connect e a AD no local.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Procurações de DNS na floresta de destino. |
| Kerberos |88 (TCP/UDP) |A autenticação kerberos para a floresta ad. |
| MS-RPC |135 (TCP) |Utilizado durante a configuração inicial do assistente Azure AD Connect quando se liga à floresta AD, e também durante a sincronização da palavra-passe. |
| LDAP |389 (TCP/UDP) |Utilizado para a importação de dados a partir de AD. Os dados são encriptados com o Sign o & Seal da Kerberos. |
| SMB | 445 (TCP) |Usado por Seamless SSO para criar uma conta de computador na floresta aD. |
| LDAP/SSL |636 (TCP/UDP) |Utilizado para a importação de dados a partir de AD. A transferência de dados é assinada e encriptada. Só utilizado se estiver a utilizar TLS. |
| RPC |49152- 65535 (Porto RPC aleatório)(TCP) |Utilizado durante a configuração inicial do Azure AD Connect quando se liga às florestas ad, e durante a sincronização da palavra-passe. Ver [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)e [KB224196](https://support.microsoft.com/kb/224196) para obter mais informações. |
|WinRM  | 5985 (TCP) |Só utilizado se estiver a instalar AD FS com gMSA por Azure AD Connect Wizard|
|Serviços Web AD DS | 9389 (TCP) |Só utilizado se estiver a instalar AD FS com gMSA por Azure AD Connect Wizard |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 - Azure AD Connect e Azure AD
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor Azure AD Connect e o Azure AD.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP) |Utilizado para descarregar CRLs (Listas de Revogação de Certificados) para verificar os certificados TLS/SSL. |
| HTTPS |443 (TCP) |Usado para sincronizar com Azure D.D. |

Para obter uma lista de URLs e endereços IP, você precisa abrir na sua firewall, consulte os [intervalos de endereços do Office 365 urLs e IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) e [a conectividade Troubleshooting Azure AD Connect](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Quadro 3 - Azure AD Connect e AD FS Federation Servers/WAP
Esta tabela descreve as portas e protocolos necessários para a comunicação entre o servidor Azure AD Connect e os servidores AD FS Federation/WAP.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP) |Utilizado para descarregar CRLs (Listas de Revogação de Certificados) para verificar os certificados TLS/SSL. |
| HTTPS |443 (TCP) |Usado para sincronizar com Azure D.D. |
| WinRM |5985 |Ouvinte WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Quadro 4 - WAP e Servidores da Federação
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os servidores da Federação e os servidores WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443 (TCP) |Usado para autenticação. |

## <a name="table-5---wap-and-users"></a>Quadro 5 - WAP e Utilizadores
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os utilizadores e os servidores WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443 (TCP) |Utilizado para autenticação do dispositivo. |
| TCP |49443 (TCP) |Usado para autenticação de certificado. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a & 6b - Autenticação pass-through com placa única (SSO) e Password Hash Sync com Único Sinal Ligado (SSO)
As tabelas que se seguem descrevem as portas e protocolos que são necessários para a comunicação entre o Azure AD Connect e o Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a - Autenticação pass-through com SSO
|Protocolo|Número de porta|Descrição
| --- | --- | ---
|HTTP|80|Ativar tráfego HTTP de saída para validação de segurança, como SSL. Também necessário que a capacidade de atualização automática do conector funcione corretamente.
|HTTPS|443| Ativar o tráfego HTTPS de saída para operações como permitir e desativar a funcionalidade, registar conectores, descarregar atualizações de conector e lidar com todos os pedidos de entrada do utilizador.

Além disso, o Azure AD Connect precisa de ser capaz de fazer ligações IP diretas às gamas IP do centro de [dados Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6b - Password Hash Sync com SSO

|Protocolo|Número de porta|Descrição
| --- | --- | ---
|HTTPS|443| Ativar o registo SSO (necessário apenas para o processo de registo SSO).

Além disso, o Azure AD Connect precisa de ser capaz de fazer ligações IP diretas às gamas IP do centro de [dados Azure](https://www.microsoft.com/download/details.aspx?id=41653). Mais uma vez, isto só é necessário para o processo de registo SSO.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Quadro 7a & 7b - Agente de saúde Azure AD Connect health para (AD FS/Sync) e Azure AD
As tabelas seguintes descrevem os pontos finais, portas e protocolos que são necessários para a comunicação entre os agentes da Azure AD Connect Health e a Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Quadro 7a - Portos e Protocolos para o Agente de Saúde Azure AD Connect (AD FS/Sync) e Azure AD
Esta tabela descreve as seguintes portas e protocolos de saída que são necessários para a comunicação entre os agentes azure AD Connect Health e Azure AD.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443 (TCP) |Saída |
| Service Bus do Azure |5671 (TCP) |Saída |

A porta 5671 do ônibus de serviço Azure já não é necessária para a versão mais recente do agente. A mais recente versão do agente Azure AD Connect Health apenas exigia a porta 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - Pontos finais para o agente de saúde Azure AD Connect (AD FS/Sync) e Azure AD
Para obter uma lista de pontos finais, consulte a secção Requisitos para o agente de [saúde Azure AD Connect](how-to-connect-health-agent-install.md#requirements).


---
title: Identidade híbrida portas e protocolos necessários para-Azure | Documentos da Microsoft
description: Esta página é uma página de referência técnica para portas que têm de estar aberta para o Azure AD Connect
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
ms.openlocfilehash: 48d2ef0de9ae59e63cd9957200c46c788e2d785f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60387323"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Portas e Protocolos Necessários para a Identidade Híbrida
O documento seguinte é uma referência técnica sobre as portas e protocolos necessários para implementar uma solução de identidade híbrida. Utilize a seguinte ilustração e consulte a tabela correspondente.

![O que é o Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Ligue-se a tabela 1 - Azure AD e AD no local
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e AD no local.

| Protocol | Portas | Descrição |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Pesquisas de DNS na floresta de destino. |
| Kerberos |88 (TCP/UDP) |Autenticação Kerberos para a floresta do AD. |
| MS-RPC |135 (TCP/UDP) |Usado durante a configuração inicial do Assistente do Azure AD Connect, quando ele liga à floresta do AD e também durante a sincronização de palavra-passe. |
| LDAP |389 (TCP/UDP) |Utilizado para importação de dados do AD. Dados são encriptados com o início de sessão do Kerberos & selar. |
| SMB | 445 (TCP/UDP) |Utilizada pelo SSO totalmente integrado para criar uma conta de computador na floresta do AD. |
| LDAP/SSL |636 (TCP/UDP) |Utilizado para importação de dados do AD. A transferência de dados é assinada e criptografada. Utilizado apenas se estiver a utilizar SSL. |
| RPC |49152 a 65535 (Random elevada RPC Port)(TCP/UDP) |Usado durante a configuração inicial do Azure AD Connect, quando ele liga para as florestas do AD e durante a sincronização de palavra-passe. Ver [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017), e [KB224196](https://support.microsoft.com/kb/224196) para obter mais informações. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 - Azure AD Connect e o Azure AD
A tabela seguinte descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e o Azure AD.

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Utilizado para transferir CRLs (certificado de listas de revogação) para verificar os certificados SSL. |
| HTTPS |443(TCP/UDP) |Usada para sincronizar com o Azure AD. |

Para obter uma lista de URLs e IP endereços tem de abrir na firewall, consulte [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabela 3 – Azure AD Connect e de servidores/WAP do AD FS Federação
A tabela seguinte descreve as portas e protocolos que são necessários para a comunicação entre os servidores de Federação/WAP do AD FS e o servidor do Azure AD Connect.  

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Utilizado para transferir CRLs (certificado de listas de revogação) para verificar os certificados SSL. |
| HTTPS |443(TCP/UDP) |Usada para sincronizar com o Azure AD. |
| WinRM |5985 |Serviço de escuta do WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 - WAP e servidores de Federação
A tabela seguinte descreve as portas e protocolos que são necessários para a comunicação entre os servidores de Federação e os servidores WAP.

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Utilizado para autenticação. |

## <a name="table-5---wap-and-users"></a>Tabela 5 - WAP e utilizadores
A tabela seguinte descreve as portas e protocolos necessários para comunicação entre os utilizadores e os servidores WAP.

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Utilizado para autenticação do dispositivo. |
| TCP |49443 (TCP) |Utilizado para autenticação de certificado. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a & 6b - autenticação pass-through com o início de sessão único (SSO) e a sincronização de Hash de palavra-passe com o início de sessão único (SSO)
As tabelas seguintes descrevem as portas e protocolos que são necessários para a comunicação entre o Azure AD Connect e o Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a - autenticação pass-through com o SSO
|Protocol|Número de porta|Descrição
| --- | --- | ---
|HTTP|80|Ative o tráfego de saída HTTP para a validação de segurança, tais como o SSL. Também é necessário para a capacidade de atualização automática do conector a funcionar corretamente.
|HTTPS|443| Ative o tráfego HTTPS de saída para operações como a ativação e desativação da funcionalidade, registar conectores, transferência das atualizações do conector e lidar com todos os pedidos de início de sessão do utilizador.

Além disso, o Azure AD Connect tem de conseguir efetuar ligações diretas de IP para o [intervalos de IP do Centro de dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6b - sincronização de Hash de palavra-passe com o SSO

|Protocol|Número de porta|Descrição
| --- | --- | ---
|HTTPS|443| Ative o registo SSO (necessário apenas para o processo de registo SSO).

Além disso, o Azure AD Connect tem de conseguir efetuar ligações diretas de IP para o [intervalos de IP do Centro de dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Novamente, essa é apenas necessário para o processo de registo SSO.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a & 7b - agente de Azure AD Connect Health para (AD FS/sincronização) e o Azure AD
As tabelas seguintes descrevem os pontos de extremidade, portas e protocolos que são necessários para a comunicação entre agentes do Azure AD Connect Health e o Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a - portas e protocolos para o agente do Azure AD Connect Health para (AD FS/sincronização) e o Azure AD
A tabela seguinte descreve as seguintes portas de saída e os protocolos que são necessários para a comunicação entre os agentes do Azure AD Connect Health e o Azure AD.  

| Protocol | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Saída |
| Service Bus do Azure |5671 (TCP/UDP) |Saída |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7B - pontos finais para o agente de Azure AD Connect Health para (AD FS/sincronização) e o Azure AD
Para obter uma lista de pontos finais, consulte [a secção de requisitos para o agente do Azure AD Connect Health](how-to-connect-health-agent-install.md#requirements).


---
title: 'Azure AD Connect: Solucionar problemas de conectividade do SQL | Microsoft Docs'
description: Explica como solucionar problemas de conectividade do SQL que ocorrem com o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cde94dce13eeb7536f72fb0dcd937265960c7314
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842688"
---
# <a name="troubleshoot-sql-connectivity-issues-with-azure-ad-connect"></a>Solucionar problemas de conectividade do SQL com Azure AD Connect
Este artigo explica como solucionar problemas de conectividade entre Azure AD Connect e SQL Server. 

A captura de tela a seguir mostra um erro típico, se o SQL Server não puder ser encontrado.

![Erro de SQL](./media/tshoot-connect-tshoot-sql-connectivity/sql1.png)

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
Abrir uma janela do PowerShell e importar o módulo do PowerShell do ADSyncTools

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools.psm1" 
```

>[!NOTE]
>Install-Module requer atualização para o [PowerShell 5,0 (WMF 5,0)](https://www.microsoft.com/download/details.aspx?id=50395) ou posterior;  
Ou instalar [módulos do PowerShell de PackageManagement Preview-março de 2016 para o PowerShell 3.0/4.0](/powershell/module/PackageManagement) 

- **Mostrar todos os comandos**:`Get-Command -Module AdSyncTools` 
- **Execute a função do PowerShell**: `Connect-ADSyncDatabase` com os seguintes parâmetros
    - Servidor. O nome do SQL Server.
    - Cópia. Adicional O nome da instância do SQL Server e, opcionalmente, o número da porta, que você gostaria de usar. Não especifique esse parâmetro para usar a instância padrão.
    - Usu. Adicional A conta de usuário com a qual se conectar. Se for deixado em branco, o usuário conectado no momento será usado. Se você estiver se conectando a um SQL Server remoto, essa deve ser a conta de serviço personalizada que você criou para a conectividade do SQL ADConnect do Azure. Azure AD Connect usa a conta de serviço de sincronização Azure AD Connect como para autenticar para um SQL Server remoto.
    - La. Adicional Senha para o nome de usuário fornecido.

Essa função do PowerShell tentará se associar ao SQL Server e à instância especificados usando as credenciais passadas ou usar as credenciais do usuário atual. Se o SQL Server não puder ser encontrado, o script tentará se conectar ao serviço de SQL Browser para determinar protocolos e portas habilitados.

Exemplo usando apenas um nome de servidor:
```
PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> import-module .\AdSyncTools.psm1

PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> Connect-AdSyncDatabase -Server SQL1
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1 using a TCP binding for the default instance.
   Data Source=tcp:SQL1\;Integrated Security=True.ConnectionString
   Successfully connected.


StatisticsEnabled                : False
AccessToken                      : 
ConnectionString                 : Data Source=tcp:SQL1\;Integrated Security=True
ConnectionTimeout                : 15
Database                         : master
DataSource                       : tcp:SQL1\
PacketSize                       : 8000
ClientConnectionId               : 23e06ef2-0a38-4f5f-9291-da931de40375
ServerVersion                    : 13.00.4474
State                            : Open
WorkstationId                    : SQL1
Credential                       : 
FireInfoMessageEventOnUserErrors : False
Site                             : 
Container                        : 




PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> 
```

Exemplo usando uma instância e um número de porta que não existem:

```
PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1"
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1\INSTANCE1 using a TCP binding.
   Data Source=tcp:SQL1\INSTANCE1;Integrated Security=True.ConnectionString
Connect-AdSyncDatabase : Unable to connect using a TCP binding.  A network-related or instance-specific error occurred while establishing a connection 
to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow 
remote connections. (provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)
At line:1 char:1
+ Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1"
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Connect-AdSyncDatabase
 

TROUBLESHOOTING: Attempting to query the SQL Server Browser service configuration on SQL1.
Get-ADSyncSQLBrowserInstances : Unable to read the SQL Server Browser configuration. An existing connection was forcibly closed by the remote host. 
Ensure port 1434 (UDP) is open on SQL1 and the SQL Server Browser service is running. 
At C:\Program Files\Microsoft Azure Active Directory Connect\tools\AdSyncTools.psm1:1717 char:18
+     $instances = Get-ADSyncSQLBrowserInstances $Server
+                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Get-ADSyncSQLBrowserInstances
 


WHAT TO TRY NEXT:

Each SQL instance must be bound to an explicit static TCP port and paired with an inbound firewall rule on SQL1 to allow connection. Enable the SQL Se
rver Browser service temporarily on the SQL server and run this cmdLet again to further troubleshoot the issue. Alternatively use the SQL Server Configur
ation Manager on SQL1 to verify the instance name and TCP/IP port assignment manually. 

You must specify both the instance name and the port to connect when the SQL Server Browser service is not running. An inbound firewall rule on SQL1 is required for the associated port.
    Example: 'MySQLInstance,1234' where 1234 has a matching firewall rule.

PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> 
PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1,99"
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1\INSTANCE1,99 using a TCP binding.
   Data Source=tcp:SQL1\INSTANCE1,99;Integrated Security=True.ConnectionString
Connect-AdSyncDatabase : Unable to connect using a TCP binding.  A network-related or instance-specific error occurred while establishing a connection 
to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: TCP Provider, error: 0 - The remote computer refused the network connection.)
At line:1 char:1
+ Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1,99"
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Connect-AdSyncDatabase
 

TROUBLESHOOTING: Attempting to query the SQL Server Browser service configuration on SQL1.
SQL browser response contained 2 instances.
Verifying protocol bindings and port connectivity.
MSSQLSERVER     : Enabled - port 1433 is assigned and reachable through the firewall
INSTANCE1       : Blocked - the inbound firewall rule for port 58379 is missing or disabled


WHAT TO TRY NEXT:

Each SQL instance must be bound to an explicit static TCP port and paired with an
inbound firewall rule on SQL1 to allow connection. Review the TcpStatus field
for each instance and take corrective action.


InstanceName : MSSQLSERVER
tcp          : 1433
TcpStatus    : Enabled - port 1433 is assigned and reachable through the firewall

InstanceName : INSTANCE1
tcp          : 58379
TcpStatus    : Blocked - the inbound firewall rule for port 58379 is missing or disabled




PS C:\Program Files\Microsoft Azure Active Directory Connect\tools>  
```

## <a name="next-steps"></a>Próximos Passos
- [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
-  [Conectividade do Azure AD com o Azure AD Connect](tshoot-connect-connectivity.md)

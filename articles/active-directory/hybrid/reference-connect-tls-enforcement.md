---
title: 'Azure AD Connect: TLS 1.2 execução para Azure Ative Directory Connect. Microsoft Docs'
description: Este artigo lista todos os lançamentos de Azure AD Connect e Azure AD Sync
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff5c75785622b43e66b808009c4674d4b2f2b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300853"
---
# <a name="tls-12-enforcement-for-azure-ad-connect"></a>Aplicação tLS 1.2 para Azure AD Connect

Transport Layer Security (TLS) protocol version 1.2 é um protocolo de criptografia projetado para fornecer comunicações seguras.  O protocolo TLS visa principalmente fornecer privacidade e integridade de dados.  O TLS passou por muitas iterações com a versão 1.2 a ser definida no [RFC 5246](https://tools.ietf.org/html/rfc5246).  Azure Ative Directory Connect versão 1.2.65.0 e, mais tarde, suporte totalmente utilizando apenas TLS 1.2 para comunicações com o Azure.  Este documento fornecerá informações sobre como forçar o seu servidor Azure AD Connect a utilizar apenas TLS 1.2.

## <a name="update-the-registry"></a>Atualizar o registo
Para forçar o servidor Azure AD Connect a utilizar apenas o TLS 1.2, o registo do servidor Windows deve ser atualizado.  Detete as seguintes teclas de registo no servidor Azure AD Connect.

>[!IMPORTANT]
>Depois de ter atualizado o registo, tem de reiniciar o servidor do Windows para que as alterações se afetem.


### <a name="enable-tls-12"></a>Ativar o TLS 1.2
- [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:000000001
  - "SchUseStrongCrypto"=dword:00000001
- [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:000000001
  - "SchUseStrongCrypto"=dword:000000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
  - "Habilitado"=dword:0000000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
  - "DisabledByDefault"=dword:0000000000 
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
  - "Habilitado"=dword:0000000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
  - "DisabledByDefault"=dword:0000000000

### <a name="powershell-script-to-enable-tls-12"></a>Script PowerShell para ativar TLS 1.2
Pode utilizar o seguinte script PowerShell para ativar o TLS 1.2 no seu servidor Azure AD Connect.

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been enabled.'
```

### <a name="disable-tls-12"></a>Desativar tLS 1.2
- [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:000000000
  - "SchUseStrongCrypto"=dword:00000000
- [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:000000000
  - "SchUseStrongCrypto"=dword:0000000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
  - "Habilitado"=dword:00000000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
  - "DisabledByDefault"=dword:000000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
  - "Habilitado"=dword:00000000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
  - "DisabledByDefault"=dword:000000001 

### <a name="powershell-script-to-disable-tls-12"></a>Script PowerShell para desativar TLS 1.2
Pode utilizar o seguinte script PowerShell para desativar o TLS 1.2 no seu servidor Azure AD Connect.\

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been disabled.'
```

## <a name="next-steps"></a>Passos seguintes
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

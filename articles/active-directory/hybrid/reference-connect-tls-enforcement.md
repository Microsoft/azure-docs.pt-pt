---
title: 'Azure AD Connect: Aplicação TLS 1.2 para Azure Ative Directory Connect / Microsoft Docs'
description: Saiba como forçar o seu servidor Azure AD Connect a utilizar apenas a Segurança da Camada de Transporte (TLS) 1.2.
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
ms.openlocfilehash: 2c610eb8fe6caf308117fa7adfc6e2f2a35c6352
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325900"
---
# <a name="tls-12-enforcement-for-azure-ad-connect"></a>Aplicação TLS 1.2 para Azure AD Connect

O protocolo 1.2 do protocolo de Segurança da Camada de Transporte (TLS) é um protocolo de criptografia concebido para fornecer comunicações seguras.  O protocolo TLS visa principalmente fornecer privacidade e integridade de dados.  O TLS passou por muitas iterações com a versão 1.2 a ser definida no [RFC 5246](https://tools.ietf.org/html/rfc5246).  Azure Ative Directory Connect versão 1.2.65.0 e, mais tarde, agora totalmente suportado utilizando apenas TLS 1.2 para comunicações com a Azure.  Este documento fornecerá informações sobre como forçar o seu servidor Azure AD Connect a utilizar apenas O TLS 1.2.

## <a name="update-the-registry"></a>Atualizar o registo
Para forçar o servidor Azure AD Connect a utilizar apenas o TLS 1.2, o registo do servidor Windows deve ser atualizado.  Desactore as seguintes teclas de registo no servidor Azure AD Connect.

>[!IMPORTANT]
>Depois de ter atualizado o registo, tem de reiniciar o servidor do Windows para que as alterações sejam afetos.


### <a name="enable-tls-12"></a>Ativar o TLS 1.2
- \\[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:0000001
  - "SchUseStrongCrypto"=dword:0000001
- \\[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:0000001
  - "SchUseStrongCrypto"=dword:0000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Servidor]
  - "Ativado"=dword:0000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Servidor]
  - "DisabledByDefault"=dword:00000000 
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Cliente]
  - "Ativado"=dword:0000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Cliente]
  - "DisabledByDefault"=dword:00000000

### <a name="powershell-script-to-enable-tls-12"></a>Script PowerShell para ativar TLS 1.2
Pode utilizar o seguinte script PowerShell para ativar o TLS 1.2 no seu servidor AZure AD Connect.

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

### <a name="disable-tls-12"></a>Desativar TLS 1.2
- \\[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:0000000
  - "SchUseStrongCrypto"=dword:000000
- \\[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:0000000
  - "SchUseStrongCrypto"=dword:00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Servidor]
  - "Ativado"=dword:0000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Servidor]
  - "DisabledByDefault"=dword:0000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Cliente]
  - "Ativado"=dword:0000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Cliente]
  - "DisabledByDefault"=dword:0000001 

### <a name="powershell-script-to-disable-tls-12"></a>Script PowerShell para desativar TLS 1.2
Pode utilizar o seguinte script PowerShell para desativar o TLS 1.2 no seu servidor AD Connect Azure.\

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

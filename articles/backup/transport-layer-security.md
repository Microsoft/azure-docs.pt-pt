---
title: Segurança da camada de transporte em backup Azure
description: Saiba como permitir que o Azure Backup utilize o protocolo de encriptação Transport Layer Security (TLS) para manter os dados seguros quando são transferidos por uma rede.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96327122"
---
# <a name="transport-layer-security-in-azure-backup"></a>Segurança da camada de transporte em backup Azure

Transport Layer Security (TLS) é um protocolo de encriptação que mantém os dados seguros quando são transferidos por uma rede. O Azure Backup utiliza a segurança da camada de transporte para proteger a privacidade dos dados de backup que são transferidos. Este artigo descreve passos para permitir o protocolo TLS 1.2, que proporciona uma melhor segurança em relação às versões anteriores.

## <a name="earlier-versions-of-windows"></a>Versões anteriores do Windows

Se a máquina estiver a executar versões anteriores do Windows, devem ser instaladas as atualizações correspondentes a seguir e devem ser aplicadas as alterações de registo documentadas nos artigos do KB.

|Sistema operativo  |Artigo do KB |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>A atualização instalará os componentes de protocolo necessários. Após a instalação, deve escamar as alterações da chave de registo mencionadas nos artigos do KB acima para ativar adequadamente os protocolos necessários.

## <a name="verify-windows-registry"></a>Verificar registo do Windows

### <a name="configuring-schannel-protocols"></a>Configurar protocolos SChannel

As seguintes chaves de registo asseguram que o protocolo TLS 1.2 está ativado ao nível do componente SChannel:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>Os valores apresentados são definidos por padrão no Windows Server 2012 R2 e nas versões mais recentes. Para estas versões do Windows, se as chaves de registo estiverem ausentes, não precisam de ser criadas.

### <a name="configuring-net-framework"></a>Configuração .NET Framework

As seguintes chaves de registo configuram .NET Framework para suportar uma criptografia forte. Pode ler mais sobre [a configuração do quadro .NET aqui.](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-enable-tls-12"></a>Por que ativar o TLS 1.2?

O TLS 1.2 é mais seguro do que os protocolos criptográficos anteriores, tais como SSL 2.0, SSL 3.0, TLS 1.0 e TLS 1.1. Os serviços de Backup da Azure já suportam totalmente o TLS 1.2.

### <a name="what-determines-the-encryption-protocol-used"></a>O que determina o protocolo de encriptação utilizado?

A versão de protocolo mais alta suportada tanto pelo cliente como pelo servidor é negociada para estabelecer a conversa encriptada. Para obter mais informações sobre o protocolo de aperto de mão TLS, consulte [estabelecer uma Sessão Segura utilizando OLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>Qual é o impacto de não permitir o TLS 1.2?

Para uma melhor segurança dos ataques de downgrade de protocolos, o Azure Backup está a começar a desativar versões TLS com mais de 1.2 de forma faseada. Isto faz parte de uma mudança de serviços a longo prazo para não permitir o protocolo legado e cifras de conexões suite. Os serviços e componentes de backup da Azure suportam totalmente o TLS 1.2. No entanto, as versões do Windows que carecem de atualizações necessárias ou certas configurações personalizadas podem ainda impedir que sejam oferecidos protocolos TLS 1.2. Isto pode causar falhas, incluindo, mas não se limitando a um ou mais dos seguintes:

- As operações de backup e restauro podem falhar.
- Componentes de reserva ligam avarias com erro 10054 (Uma ligação existente foi fechada à força pelo hospedeiro remoto).
- Os serviços relacionados com a Azure Backup não param ou começam como de costume.

## <a name="additional-resources"></a>Recursos adicionais

- [TLSP (Transport Layer Security Protocol)](/windows/win32/secauthn/transport-layer-security-protocol)
- [Garantir o suporte para tls 1.2 em todos os sistemas operativos implantados](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Segurança da camada de transporte (TLS) boas práticas com o Quadro .NET](/dotnet/framework/network-programming/tls)
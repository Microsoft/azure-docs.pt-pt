---
title: O Azure AD self-service palavra-passe reposta Windows 7 e 8.1 - Azure Active Directory
description: Como ativar as senhas de auto-atendimento repor usando Esqueci-me da palavra-passe no ecrã de início de sessão 8.1 ou Windows 7
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a7752fac54f9dfb2f8fb0aecd3b6249c52c3bcf
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316359"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>Como: Ativar a reposição do Windows 7, 8 e 8.1 palavra-passe

Como um administrador tiver ativado a reposição de palavra-passe self-service (SSPR), mas os utilizadores manterem a chamar o suporte técnico para repor a palavra-passe, porque não conseguem abrir uma janela do browser para aceder a [portal SSPR](https://aka.ms/sspr). Para máquinas do Windows 10, pode ativar a ligação "Repor palavra-passe" na tela de logon usando o tutorial [palavra-passe do Azure AD a partir do ecrã de início de sessão de reposição](tutorial-sspr-windows.md), as seguintes orientações irão ajudar a permitir que os utilizadores de Windows 7, 8 e 8.1 para repor a palavra-passe a utilizar o SSPR no ecrã de início de sessão do Windows.

Ao contrário de máquinas do Windows 10, Windows 7, 8 e 8.1 computadores não têm um Azure AD associado a um domínio ou repor o requisito de associados a um domínio do Active Directory para a palavra-passe.

![Tela de logon do Windows 7 de exemplo com "Forgot password?" Link mostrado](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>Pré-requisitos

* A reposição de palavras-passe self-service do Azure AD tem de estar ativada.
* Corrigida Windows 7 ou o sistema de operativo do Windows 8.1.
* TLS 1.2, ativado através da orientação encontrados na [definições de registo de Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).

> [!WARNING]
> Tem de estar ativada TLS 1.2, negociar de não apenas definido como automático.

## <a name="install"></a>Instalar

1. Transferir o instalador apropriado para a versão do Windows que pretende ativar.

   1. O software está disponível no Centro de download da Microsoft em [https://aka.ms/sspraddin](https://aka.ms/sspraddin)

1. Inicie sessão para a máquina em que gostaria de instalar e executar o instalador.
1. Após a instalação, é altamente recomendável um reinício.
1. Após a reinicialização, no ecrã de início de sessão escolhe um utilizador e clique em "Esqueci-me da palavra-passe?" para iniciar a palavra-passe de reposição de fluxo de trabalho.
1. Conclua o fluxo de trabalho seguindo os passos na tela para repor a palavra-passe.

![Exemplo Windows 7 clicou em "Esqueci-me da palavra-passe?" fluxo de reposição de palavra-passe self-service](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>Instalação silenciosa

* Para instalações silenciosas, utilize o comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
* Para a desinstalação automática, utilize o comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

## <a name="caveats"></a>Limitações

Tem de registar para SSPR antes de que, poderá utilizar a hiperligação "Forgot password".

![Informações de segurança adicional é necessária para repor a palavra-passe](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

Utilizar a aplicação Microsoft Authenticator para notificações e códigos para repor a palavra-passe não funciona nesta versão inicial. Os utilizadores tem de ter métodos alternativos registados que cumprem os requisitos da sua política.

## <a name="troubleshooting"></a>Resolução de problemas

Eventos serão registados no computador e no Azure AD.

Eventos do Azure AD irão incluir informações sobre o endereço IP e ClientType onde ocorreu a reposição de palavra-passe.

![Exemplo 7 do Windows início de sessão ecrã reposição palavra-passe no log de auditoria do Azure AD](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

Se o registo adicional é necessário, uma chave de registo na máquina pode ser alterada para ativar o registo verboso. Ative o registo verboso para apenas para fins de resolução de problemas.

```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

* Para ativar o registo verboso, crie REG_DWORD: "EnableLogging" e defina-o como 1.
* Para desativar o registo verboso, altere o REG_DWORD "EnableLogging" para 0.

Se as máquinas do Windows 7, 8 e 8.1 estiverem atrás de um servidor proxy ou firewall, o tráfego HTTPS (443) para o site passwordreset.microsoftonline.com deve ser permitido.

## <a name="next-steps"></a>Passos Seguintes

[Permitir que os utilizadores de Windows 10 para repor a palavra-passe no ecrã de início de sessão](tutorial-sspr-windows.md)

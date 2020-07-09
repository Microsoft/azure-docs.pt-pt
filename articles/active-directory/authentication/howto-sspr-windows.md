---
title: Redefinição da palavra-passe de autosserviço para Windows - Azure Ative Directory
description: Como ativar o reset da palavra-passe de autosserviço usando a palavra-passe esquecida no ecrã de login do Windows
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d1ffec6a849cb97a6151717c3e30dc362b1403
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83826609"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Como: Ativar a palavra-passe reiniciada a partir do ecrã de login do Windows

Para as máquinas que executam o Windows 7, 8, 8.1 e 10, pode permitir que os utilizadores reiniciem a sua palavra-passe no ecrã de login do Windows. Os utilizadores já não têm de encontrar um dispositivo com um navegador web para aceder ao [portal SSPR.](https://aka.ms/sspr)

![Exemplo de ecrãs de login do Windows 7 e 10 com ligação SSPR mostrado](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Limitações gerais

- O reset da palavra-passe não é suportado atualmente a partir de um Ambiente de Trabalho Remoto ou de sessões melhoradas de Hiper-V.
- Sabe-se que alguns fornecedores de credenciais de terceiros causam problemas com esta funcionalidade.
- A desativação da UAC através da modificação da chave de [registo EnableLUA](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) é conhecida por causar problemas.
- Esta funcionalidade não funciona para redes com autenticação de rede 802.1x implementada e a opção "Executar imediatamente antes da apresentação do utilizador". Para redes com autenticação de rede de 802.1x implantada é aconselhável utilizar a autenticação da máquina para ativar esta funcionalidade.
- As máquinas híbridas AZURE AD devem ter linha de visão de conectividade de rede para um controlador de domínio para usar a nova palavra-passe e atualizar credenciais em cache. Isto significa que os dispositivos devem estar na rede interna da organização ou numa VPN com acesso à rede a um controlador de domínio no local. 
- Se utilizar uma imagem, antes de executar o sysprep, certifique-se de que a cache web está limpa para o administrador incorporado antes de executar o passo CopyProfile. Mais informações sobre este passo podem ser encontradas no artigo De suporte [Desempenho fraco ao utilizar o perfil de utilizador padrão personalizado](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- As seguintes definições são conhecidas por interferir com a capacidade de utilizar e redefinir palavras-passe em dispositivos Windows 10
    - Se o Ctrl+Alt+Del for exigido por política nas versões do Windows 10 antes do v1809, **a palavra-passe reset** não funcionará.
    - Se as notificações do ecrã de bloqueio estiverem desligadas, **a palavra-passe reset** não funcionará.
    - HideFastUserSwitching está definido para ativar ou 1
    - DontDisplayLastUserName está definido para ativar ou 1
    - NoLockScreen está definido para ativar ou 1
    - EnableLostMode está definido no dispositivo
    - Explorer.exe é substituído por uma concha personalizada
- A combinação das seguintes três definições específicas pode fazer com que esta função não funcione.
    - Logotipo interativo: Não requer CTRL+ALT+DEL = Desativado
    - DisableLockScreenAppNotificações = 1 ou Ativado
    - Windows SKU não é edição doméstica ou profissional

## <a name="windows-10-password-reset"></a>Reset da palavra-passe do Windows 10

### <a name="windows-10-prerequisites"></a>Pré-requisitos do Windows 10

- Um administrador deve ativar a palavra-passe de autosserviço Azure AD reposta a partir do portal Azure.
- **Os utilizadores devem registar-se no SSPR antes de utilizarem esta funcionalidade**
- Requisitos de procuração de rede
   - Dispositivos Windows 10 
       - Porto 443 para `passwordreset.microsoftonline.com` e`ajax.aspnetcdn.com`
       - Os dispositivos windows 10 suportam apenas a configuração de procuração a nível da máquina
- Executar pelo menos o Windows 10, versão April 2018 Update (v1803), e os dispositivos devem ser:
    - Azure AD associado
    - associado ao Azure AD Híbrido

### <a name="enable-for-windows-10-using-intune"></a>Ativar para o Windows 10 usando o Intune

Implementar a alteração da configuração para ativar a reposição de palavra-passe no ecrã de início de sessão com o Intune é o método mais flexível. O Intune permite implementar a alteração de configuração num grupo específico de máquinas que definir. Este método exige a inscrição do dispositivo no Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração de dispositivos no Intune

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
1. Criar um novo perfil de configuração do dispositivo indo para perfis **de configuração**do dispositivo  >  **Profiles**  >  **Criar Perfil**
   - Dê um nome relevante ao perfil
   - Opcionalmente, indique uma descrição relevante do perfil
   - Plataforma **Windows 10 e posterior**
   - Tipo de perfil **Personalizado**
1. Configurações de **configuração**
   - **Adicione** a Definição de OMA-URI seguinte para ativar a ligação Repor palavra-passe
      - Indique um nome relevante para explicar o que a definição faz
      - Opcionalmente, indique uma descrição relevante da definição
      - **OMA-URI** definido como `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Tipo de dados** definido como **Inteiro**
      - **Valor** definido como **1**
      - Clique **em OK**
   - Clique **em OK**
1. Clique **em Criar**
1. Esta política pode ser atribuída a utilizadores, dispositivos ou grupos específicos. Mais informações podem ser encontradas no artigo [Atribuir perfis de utilizador e dispositivo no Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Ativar o Windows 10 utilizando o Registo

1. Inscreva-se no Pc do Windows usando credenciais administrativas
1. Execute **regedit** como administrador
1. Defina a chave de registo seguinte
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Resolução de problemas da palavra-passe do Windows 10

O registo de auditoria do Azure AD irá incluir informações sobre o endereço IP e ClientType onde ocorreu a reposição de palavra-passe.

![Exemplo da palavra-passe do Windows 7 reiniciada no registo de auditoria AZure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Quando os utilizadores reiniciam a sua palavra-passe a partir do ecrã de login de um dispositivo Windows 10, é criada uma conta temporária de baixo privilégio `defaultuser1` chamada. Esta conta é utilizada para manter o processo de reset da palavra-passe seguro. A conta em si tem uma senha gerada aleatoriamente, não aparece para o sôm-in do dispositivo e será automaticamente removida após o utilizador redefinir a sua palavra-passe. Vários `defaultuser` perfis podem existir, mas podem ser ignorados com segurança.

## <a name="windows-7-8-and-81-password-reset"></a>Redefinição da palavra-passe do Windows 7, 8 e 8.1

### <a name="windows-7-8-and-81-prerequisites"></a>Pré-requisitos do Windows 7, 8 e 8.1

- Um administrador deve ativar a palavra-passe de autosserviço Azure AD reposta a partir do portal Azure.
- **Os utilizadores devem registar-se no SSPR antes de utilizarem esta funcionalidade**
- Requisitos de procuração de rede
   - Dispositivos Windows 7, 8 e 8.1
       - Porto 443 para`passwordreset.microsoftonline.com`
- Sistema operativo Do Windows 7 ou Windows 8.1 remendado.
- TLS 1.2 ativado utilizando as orientações encontradas nas [definições de registo de Segurança da Camada de Transporte (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- Se mais de um provedor de credenciais de 3ª parte estiver ativado na sua máquina, os utilizadores verão mais do que um perfil de utilizador no ecrã de login.

> [!WARNING]
> TLS 1.2 deve ser ativado, não apenas definido para negociar automaticamente

### <a name="install"></a>Instalar

1. Descarregue o instalador apropriado para a versão do Windows que pretende ativar.
   - O software está disponível no centro de descarregamento da Microsoft em[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Inscreva-se na máquina onde pretende instalar e execute o instalador.
1. Após a instalação, recomenda-se vivamente um reinício.
1. Após o reboot, no ecrã de login escolha um utilizador e clique em "Esqueci-me da palavra-passe?" para iniciar o fluxo de trabalho de redefinição da palavra-passe.
1. Complete o fluxo de trabalho seguindo os passos no ecrã para redefinir a sua palavra-passe.

![Exemplo O Windows 7 clicou em "Esqueci-me da palavra-passe?" Fluxo SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Instalação automática

- Para instalação silenciosa, utilize o comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Para desinstalar silenciosamente, utilize o comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Resolução de problemas do Windows 7, 8 e 8.1 redefinição da palavra-passe

Os eventos serão registados tanto na máquina como no Azure AD. Os Eventos AD da Azure incluirão informações sobre o endereço IP e o ClientType onde ocorreu o reset da palavra-passe.

![Exemplo da palavra-passe do Windows 7 reiniciada no registo de auditoria AZure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Se for necessário registar registos adicionais, pode ser alterada uma chave de registo na máquina para permitir a exploração de registo verboso. Ativar a registo verboso apenas para efeitos de resolução de problemas.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Para ativar a exploração de verbose, crie um `REG_DWORD: "EnableLogging"` , e desa um para 1.
- Para desativar a extração de verbose, altere o `REG_DWORD: "EnableLogging"` para 0.

## <a name="what-do-users-see"></a>O que os utilizadores veem

Agora que configurar a palavra-passe para os seus dispositivos Windows, o que muda para o utilizador? Como é que podem saber que é permitido repor a palavra-passe no ecrã de início de sessão?

![Exemplo de ecrãs de login do Windows 7 e 10 com ligação SSPR mostrado](./media/howto-sspr-windows/windows-reset-password.png)

Quando os utilizadores tentam iniciar sessão, vêem agora uma **palavra-passe reset** ou uma ligação **de senha esquecida** que abre a experiência de reset da palavra-passe de autosserviço no ecrã de login. Com esta funcionalidade, os utilizadores podem repor as palavras-passe sem terem de utilizar outro dispositivo para aceder a um browser.

Os seus utilizadores podem obter orientações sobre como utilizar esta funcionalidade em [Reset your work or school password](../user-help/active-directory-passwords-update-your-own-password.md) (Repor a palavra-passe da conta escolar ou profissional)

## <a name="next-steps"></a>Próximos passos

[Planear métodos de autenticação para permitir](concept-authentication-methods.md)

[Configurar o Windows 10](https://docs.microsoft.com/windows/configuration/)

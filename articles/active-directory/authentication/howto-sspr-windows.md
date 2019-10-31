---
title: Redefinição de senha de autoatendimento do Azure AD para Windows-Azure Active Directory
description: Como habilitar a redefinição de senha de autoatendimento usando a senha esquecida na tela de logon do Windows
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 519993be873e7864dab4de4f66919c56aebfc379
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171861"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Como habilitar a redefinição de senha na tela de logon do Windows

Para computadores que executam o Windows 7, 8, 8,1 e 10, você pode permitir que os usuários redefinam sua senha na tela de logon do Windows. Os usuários não precisam mais localizar um dispositivo com um navegador da Web para acessar o [portal do SSPR](https://aka.ms/sspr).

![Exemplo de telas de logon do Windows 7 e 10 com o link SSPR mostrado](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Limitações gerais

- Atualmente, a redefinição de senha não tem suporte de uma Área de Trabalho Remota ou de sessões avançadas do Hyper-V.
- Esse recurso não funciona para redes com autenticação de rede 802.1 x implantada e a opção "executar imediatamente antes do logon do usuário". Para redes com autenticação de rede 802.1 x implantada, é recomendável usar a autenticação do computador para habilitar esse recurso.
- Os computadores ingressados no Azure AD híbrido devem ter a linha de visão de conectividade de rede para um controlador de domínio para usar a nova senha e atualizar as credenciais armazenadas em cache.
- Se estiver usando uma imagem, antes de executar o Sysprep, verifique se o cache da Web foi limpo para o administrador interno antes de executar a etapa CopyProfile. Mais informações sobre essa etapa podem ser encontradas no artigo de suporte [desempenho ruim ao usar o perfil de usuário padrão personalizado](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- As configurações a seguir são conhecidas por interferir na capacidade de usar e redefinir senhas em dispositivos Windows 10
    - Se Ctrl + Alt + Del for exigido pela política em versões do Windows 10 antes do v1809, a **redefinição da senha** não funcionará.
    - Se as notificações da tela de bloqueio estiverem desativadas, a opção **Redefinir senha** não funcionará.
    - HideFastUserSwitching é definido como Enabled ou 1
    - DontDisplayLastUserName é definido como Enabled ou 1
    - Nobloqueio de tela está definido como habilitado ou 1
    - EnableLostMode está definido no dispositivo
    - O Explorer. exe é substituído por um shell personalizado
- A combinação das três configurações específicas a seguir pode fazer com que esse recurso não funcione.
    - Logon interativo: não exigir CTRL + ALT + DEL = Disabled
    - DisableLockScreenAppNotifications = 1 ou habilitado
    - IsContentDeliveryPolicyEnforced = 1 ou true

## <a name="windows-10-password-reset"></a>Redefinição de senha do Windows 10

### <a name="windows-10-prerequisites"></a>Pré-requisitos do Windows 10

- Um administrador deve habilitar a redefinição de senha de autoatendimento do Azure AD no portal do Azure.
- **Os usuários devem se registrar no SSPR antes de usar esse recurso**
- Requisitos de proxy de rede
   - Dispositivos Windows 10 
       - Porta 443 para `passwordreset.microsoftonline.com` e `ajax.aspnetcdn.com`
       - Dispositivos Windows 10 dão suporte apenas à configuração de proxy no nível de computador
- Execute pelo menos o Windows 10, versão de abril de 2018 atualização (v1803) e os dispositivos devem ser:
    - Ingressado no Azure AD
    - Ingressado no Azure AD híbrido

### <a name="enable-for-windows-10-using-intune"></a>Habilitar para Windows 10 usando o Intune

Implementar a alteração da configuração para ativar a reposição de palavra-passe no ecrã de início de sessão com o Intune é o método mais flexível. O Intune permite implementar a alteração de configuração num grupo específico de máquinas que definir. Este método exige a inscrição do dispositivo no Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração de dispositivos no Intune

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
1. Aceda a **Configuração de dispositivos** > **Perfis** > **Criar Perfil** para criar um perfil de configuração de dispositivos novo
   - Dê um nome relevante ao perfil
   - Opcionalmente, indique uma descrição relevante do perfil
   - Plataforma **Windows 10 e posterior**
   - Tipo de perfil **Personalizado**
1. Configure as **Definições**
   - **Adicione** a Definição de OMA-URI seguinte para ativar a ligação Repor palavra-passe
      - Indique um nome relevante para explicar o que a definição faz
      - Opcionalmente, indique uma descrição relevante da definição
      - **OMA-URI** definido como `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Tipo de dados** definido como **Inteiro**
      - **Valor** definido como **1**
      - Clique em **OK**
   - Clique em **OK**
1. Clique em **Criar**.
1. Essa política pode ser atribuída a usuários, dispositivos ou grupos específicos. Mais informações podem ser encontradas no artigo [atribuir perfis de usuário e de dispositivo no Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Habilitar para Windows 10 usando o registro

1. Entrar no computador Windows usando credenciais administrativas
1. Execute **regedit** como administrador
1. Defina a chave de registo seguinte
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Solucionando problemas de redefinição de senha do Windows 10

O registo de auditoria do Azure AD irá incluir informações sobre o endereço IP e ClientType onde ocorreu a reposição de palavra-passe.

![Exemplo de redefinição de senha do Windows 7 no log de auditoria do Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Quando os usuários redefinem sua senha na tela de logon de um dispositivo Windows 10, uma conta temporária de baixo privilégio chamada `defaultuser1` é criada. Essa conta é usada para manter o processo de redefinição de senha seguro. A conta em si tem uma senha gerada aleatoriamente, não aparece para entrar no dispositivo e será removida automaticamente depois que o usuário redefinir sua senha. Vários perfis de `defaultuser` podem existir, mas podem ser ignorados com segurança.

## <a name="windows-7-8-and-81-password-reset"></a>Redefinição de senha do Windows 7, 8 e 8,1

### <a name="windows-7-8-and-81-prerequisites"></a>Pré-requisitos do Windows 7, 8 e 8,1

- Um administrador deve habilitar a redefinição de senha de autoatendimento do Azure AD no portal do Azure.
- **Os usuários devem se registrar no SSPR antes de usar esse recurso**
- Requisitos de proxy de rede
   - Dispositivos Windows 7, 8 e 8,1
       - Porta 443 para `passwordreset.microsoftonline.com`
- Sistema operacional Windows 7 ou Windows 8.1 corrigido.
- TLS 1,2 habilitado usando as diretrizes encontradas nas [configurações do registro de TLS (segurança da camada de transporte)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- Se mais de um provedor de credenciais de terceiros estiver habilitado em seu computador, os usuários verão mais de um perfil de usuário na tela de logon.

> [!WARNING]
> O TLS 1,2 deve ser habilitado, não apenas definido como negociação automática

### <a name="install"></a>Instalação

1. Baixe o instalador apropriado para a versão do Windows que você deseja habilitar.
   - O software está disponível no centro de download da Microsoft em [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Entre no computador onde você deseja instalar e execute o instalador.
1. Após a instalação, é altamente recomendável reinicializar.
1. Após a reinicialização, na tela de logon, escolha um usuário e clique em "esqueceu a senha?" para iniciar o fluxo de trabalho de redefinição de senha.
1. Conclua o fluxo de trabalho seguindo as etapas na tela para redefinir sua senha.

![Exemplo, o Windows 7 clicou em "esqueceu a senha?" Fluxo de SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Instalação silenciosa

- Para instalação silenciosa, use o comando "msiexec/i SsprWindowsLogon. PROD. msi/qn"
- Para desinstalação silenciosa, use o comando "msiexec/x SsprWindowsLogon. PROD. msi/qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Solução de problemas de redefinição de senha do Windows 7, 8 e 8,1

Os eventos serão registrados no computador e no Azure AD. Os eventos do AD do Azure incluirão informações sobre o endereço IP e o ClientType em que a redefinição de senha ocorreu.

![Exemplo de redefinição de senha do Windows 7 no log de auditoria do Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Se o registro em log adicional for necessário, uma chave do registro no computador poderá ser alterada para habilitar o log detalhado. Habilite o log detalhado apenas para fins de solução de problemas.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Para habilitar o log detalhado, crie um `REG_DWORD: “EnableLogging”`e defina-o como 1.
- Para desabilitar o log detalhado, altere o `REG_DWORD: “EnableLogging”` para 0.

## <a name="what-do-users-see"></a>O que os utilizadores veem

Agora que você configurou a redefinição de senha para seus dispositivos Windows, o que muda para o usuário? Como é que podem saber que é permitido repor a palavra-passe no ecrã de início de sessão?

![Exemplo de telas de logon do Windows 7 e 10 com o link SSPR mostrado](./media/howto-sspr-windows/windows-reset-password.png)

Quando os usuários tentam entrar, eles agora veem um link **Redefinir senha** ou **esqueceu a senha** que abre a experiência de redefinição de senha de autoatendimento na tela de logon. Com esta funcionalidade, os utilizadores podem repor as palavras-passe sem terem de utilizar outro dispositivo para aceder a um browser.

Os seus utilizadores podem obter orientações sobre como utilizar esta funcionalidade em [Reset your work or school password](../user-help/active-directory-passwords-update-your-own-password.md) (Repor a palavra-passe da conta escolar ou profissional)

## <a name="next-steps"></a>Passos seguintes

[Planejar métodos de autenticação para permitir](concept-authentication-methods.md)

[Configurar o Windows 10](https://docs.microsoft.com/windows/configuration/)

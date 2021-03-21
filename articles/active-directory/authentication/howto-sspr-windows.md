---
title: Redefinição da palavra-passe de autosserviço para dispositivos Windows - Azure Ative Directory
description: Saiba como ativar a palavra-passe de autosserviço do Azure Ative Directory no ecrã de entrada do Windows.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2d910c017d3cc626f737bdab50315aef8d1e77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491390"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Ativar a palavra-passe de autosserviço do Azure Ative Directory reiniciada no ecrã de entrada do Windows

O reset da palavra-passe de autosserviço (SSPR) dá aos utilizadores do Azure Ative Directory (Azure AD) a capacidade de alterar ou redefinir a sua palavra-passe, sem qualquer administrador ou envolvimento no balcão de ajuda. Normalmente, os utilizadores abrem um navegador web noutro dispositivo para aceder ao [portal SSPR.](https://aka.ms/sspr) Para melhorar a experiência em computadores que executam o Windows 7, 8, 8.1 e 10, pode permitir que os utilizadores reiniciem a sua palavra-passe no ecrã de entrada do Windows.

![Exemplo de ecrãs de login do Windows 7 e 10 com ligação SSPR mostrado](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> Este tutorial mostra a um administrador como ativar o SSPR para dispositivos Windows numa empresa.
>
> Se a sua equipa de TI não tiver ativado a capacidade de utilizar o SSPR do seu dispositivo Windows ou tiver problemas durante a sing-in, contacte o seu helpdesk para obter assistência adicional.

## <a name="general-limitations"></a>Limitações gerais

Aplicam-se as seguintes limitações à utilização de SSPR a partir do ecrã de inscrição do Windows:

- O reset da palavra-passe não é suportado atualmente a partir de um Ambiente de Trabalho Remoto ou de sessões melhoradas de Hiper-V.
- Sabe-se que alguns fornecedores de credenciais de terceiros causam problemas com esta funcionalidade.
- A desativação da UAC através da modificação da chave de [registo EnableLUA](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) é conhecida por causar problemas.
- Esta funcionalidade não funciona para redes com autenticação de rede 802.1x implementada e a opção "Executar imediatamente antes da apresentação do utilizador". Para redes com autenticação de rede de 802.1x implantada, é aconselhável utilizar a autenticação da máquina para ativar esta funcionalidade.
- As máquinas híbridas AZURE AD devem ter linha de visão de conectividade de rede para um controlador de domínio para usar a nova palavra-passe e atualizar credenciais em cache. Isto significa que os dispositivos devem estar na rede interna da organização ou numa VPN com acesso à rede a um controlador de domínio no local.
- Se utilizar uma imagem, antes de executar o sysprep, certifique-se de que a cache web está limpa para o administrador incorporado antes de executar o passo CopyProfile. Mais informações sobre este passo podem ser encontradas no artigo De suporte [Desempenho fraco ao utilizar o perfil de utilizador padrão personalizado](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- As seguintes definições são conhecidas por interferir com a capacidade de utilizar e redefinir palavras-passe em dispositivos Windows 10:
    - Se o Ctrl+Alt+Del for exigido pela política no Windows 10, **a palavra-passe reset** não funcionará.
    - Se as notificações do ecrã de bloqueio estiverem desligadas, **a palavra-passe reset** não funcionará.
    - *HideFastUserSwitching* está definido para ativar ou 1
    - *DontDisplayLastUserName* está definido para ativar ou 1
    - *NoLockScreen* está definido para ativar ou 1
    - *EnableLostMode* está definido no dispositivo
    - Explorer.exe é substituído por uma concha personalizada
- A combinação das seguintes três definições específicas pode fazer com que esta função não funcione.
    - Logotipo interativo: Não requer CTRL+ALT+DEL = Desativado
    - *DisableLockScreenAppNotificações* = 1 ou Ativado
    - Windows SKU não é edição doméstica ou profissional

> [!NOTE]
> Estas limitações aplicam-se também ao Windows Hello for Business PIN reiniciado a partir do ecrã de bloqueio do dispositivo.
>

## <a name="windows-10-password-reset"></a>Reset da palavra-passe do Windows 10

Para configurar um dispositivo Windows 10 para SSPR no ecrã de início de sing-in, reveja os seguintes pré-requisitos e etapas de configuração.

### <a name="windows-10-prerequisites"></a>Pré-requisitos do Windows 10

- Um administrador [deve ativar a palavra-passe de autosserviço AZure AD reposta a partir do portal Azure](tutorial-enable-sspr.md).
- Os utilizadores devem registar-se no SSPR antes de utilizarem esta funcionalidade em [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Não é exclusivo da utilização de SSPR a partir do ecrã de entrada do Windows, todos os utilizadores devem fornecer as informações de contacto de autenticação antes de poderem redefinir a sua palavra-passe.
- Requisitos de procuração de rede:
    - Porto 443 para `passwordreset.microsoftonline.com` e `ajax.aspnetcdn.com`
    - Os dispositivos Windows 10 suportam apenas a configuração de procuração ao nível da máquina.
- Executar pelo menos o Windows 10, versão April 2018 Update (v1803), e os dispositivos devem ser:
    - Azure AD associado
    - associado ao Azure AD Híbrido

### <a name="enable-for-windows-10-using-intune"></a>Ativar para o Windows 10 usando o Intune

Implementar a alteração de configuração para ativar o SSPR a partir do ecrã de login utilizando o Intune é o método mais flexível. O Intune permite implementar a alteração de configuração num grupo específico de máquinas que definir. Este método exige a inscrição do dispositivo no Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração de dispositivos no Intune

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Intune**.
1. Crie um novo perfil de configuração do dispositivo indo para perfis **de configuração**  >  **do** dispositivo, em seguida, selecione **+ Criar Perfil**
   - Para **a Plataforma** escolher o Windows *10 e mais tarde*
   - Para **o tipo de perfil,** escolha o *costume*
1. Selecione **Criar,** em seguida, fornecer um nome significativo para o perfil, como *o Windows 10 iniciar sSPR*

    Opcionalmente, forneça uma descrição significativa do perfil e, em seguida, selecione **Next**.
1. Nas *definições de configuração*, selecione **Adicionar** e forneça a seguinte definição OMA-URI para ativar o link de palavra-passe reiniciado:
      - Forneça um nome significativo para explicar o que a configuração está a fazer, como *a ligação Add SSPR*.
      - Opcionalmente, fornecer uma descrição significativa da definição.
      - **OMA-URI** definido como `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Tipo de dados** definido como **Inteiro**
      - **Valor** definido como **1**

    **Selecione Adicionar** e, **em seguida, Seguinte**.
1. A política pode ser atribuída a utilizadores, dispositivos ou grupos específicos. Atribua o perfil como desejado para o seu ambiente, idealmente a um grupo de teste de dispositivos primeiro, e depois selecione **Next**.

    Para obter mais informações, consulte [atribuir perfis de utilizador e dispositivo no Microsoft Intune](/mem/intune/configuration/device-profile-assign).

1. Configure as regras de aplicabilidade desejadas para o seu ambiente, tais como atribuir perfil se a *edição de SISTEMA for o Windows 10 Enterprise,* então selecione **Next**.
1. Reveja o seu perfil e, em seguida, **selecione Criar**.

### <a name="enable-for-windows-10-using-the-registry"></a>Ativar o Windows 10 utilizando o Registo

Para ativar o SSPR no ecrã de inscrição utilizando uma chave de registo, complete os seguintes passos:

1. Inscreva-se no Pc do Windows utilizando credenciais administrativas.
1. Pressione o **Windows**  +  **R** para abrir o diálogo *run* e, em seguida, executar **regedit** como administrador
1. Defina a chave de registo seguinte:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Resolução de problemas da palavra-passe do Windows 10

Se tiver problemas com a utilização de SSPR a partir do ecrã de entrada de registo do Windows, o registo de auditoria AZure AD inclui informações sobre o endereço IP e *o ClientType* onde ocorreu o reset da palavra-passe, como mostra a seguinte saída de exemplo:

![Exemplo da palavra-passe do Windows 7 reiniciada no registo de auditoria AZure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Quando os utilizadores reiniciam a sua palavra-passe a partir do ecrã de entrada de um dispositivo Windows 10, é criada uma conta temporária de baixo privilégio `defaultuser1` chamada. Esta conta é utilizada para manter o processo de reset da palavra-passe seguro.

A conta em si tem uma senha gerada aleatoriamente, não aparece para o sôm-in do dispositivo e é automaticamente removida após o utilizador redefinir a sua palavra-passe. Vários `defaultuser` perfis podem existir, mas podem ser ignorados com segurança.

## <a name="windows-7-8-and-81-password-reset"></a>Redefinição da palavra-passe do Windows 7, 8 e 8.1

Para configurar um dispositivo Windows 7, 8 ou 8.1 para SSPR no ecrã de início, reveja os seguintes pré-requisitos e etapas de configuração.

### <a name="windows-7-8-and-81-prerequisites"></a>Pré-requisitos do Windows 7, 8 e 8.1

- Um administrador [deve ativar a palavra-passe de autosserviço AZure AD reposta a partir do portal Azure](tutorial-enable-sspr.md).
- Os utilizadores devem registar-se no SSPR antes de utilizarem esta funcionalidade em [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Não é exclusivo da utilização de SSPR a partir do ecrã de entrada do Windows, todos os utilizadores devem fornecer as informações de contacto de autenticação antes de poderem redefinir a sua palavra-passe.
- Requisitos de procuração de rede:
    - Porto 443 para `passwordreset.microsoftonline.com`
- Sistema operativo Do Windows 7 ou Windows 8.1 remendado.
- TLS 1.2 ativado utilizando as orientações encontradas nas [definições de registo de Segurança da Camada de Transporte (TLS).](/windows-server/security/tls/tls-registry-settings#tls-12)
- Se mais de um provedor de credenciais de 3ª parte estiver ativado na sua máquina, os utilizadores vêem mais de um perfil de utilizador no ecrã de login.

> [!WARNING]
> O TLS 1.2 deve ser ativado e não apenas definido para negociar automaticamente.

### <a name="install"></a>Instalar

Para o Windows 7, 8 e 8.1, deve ser instalado um pequeno componente na máquina para ativar o SSPR no ecrã de início de sing. Para instalar este componente SSPR, complete os seguintes passos:

1. Descarregue o instalador apropriado para a versão do Windows que pretende ativar.

    O instalador de software está disponível no centro de descarregamento da Microsoft em [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Inscreva-se na máquina onde pretende instalar e execute o instalador.
1. Após a instalação, recomenda-se vivamente um reinício.
1. Após o reboot, no ecrã de entrada escolha um utilizador e selecione "Esqueceu-se da palavra-passe?" para iniciar o fluxo de trabalho de redefinição da palavra-passe.
1. Complete o fluxo de trabalho seguindo os passos no ecrã para redefinir a sua palavra-passe.

![Exemplo O Windows 7 clicou em "Esqueci-me da palavra-passe?" Fluxo SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Instalação automática

O componente SSPR pode ser instalado ou desinstalado sem solicitações utilizando os seguintes comandos:

- Para instalação silenciosa, utilize o comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Para desinstalar silenciosamente, utilize o comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Resolução de problemas do Windows 7, 8 e 8.1 redefinição da palavra-passe

Se tiver problemas com a utilização de SSPR a partir do ecrã de entrada do Windows, os eventos são registados tanto na máquina como no AD AZure. Os eventos AD do Azure incluem informações sobre o endereço IP e o ClientType onde ocorreu o reset da palavra-passe, como mostrado na saída do exemplo seguinte:

![Exemplo da palavra-passe do Windows 7 reiniciada no registo de auditoria AZure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Se for necessário registar registos adicionais, pode ser alterada uma chave de registo na máquina para permitir a exploração de registo verboso. Ativar a registo verbose para efeitos de resolução de problemas apenas utilizando o seguinte valor chave de registo:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- Para ativar a exploração de verbose, crie um `REG_DWORD: "EnableLogging"` , e desa um para 1.
- Para desativar a extração de verbose, altere o `REG_DWORD: "EnableLogging"` para 0.

## <a name="what-do-users-see"></a>O que os utilizadores veem

Com o SSPR configurado para os seus dispositivos Windows, o que muda para o utilizador? Como é que podem saber que é permitido repor a palavra-passe no ecrã de início de sessão? As imagens de exemplo a seguir mostram as opções adicionais para um utilizador redefinir a sua palavra-passe utilizando sSPR:

![Exemplo de ecrãs de login do Windows 7 e 10 com ligação SSPR mostrado](./media/howto-sspr-windows/windows-reset-password.png)

Quando os utilizadores tentam iniciar sessão, vêem uma **palavra-passe reset** ou ligação **de senha esquecida** que abre a experiência de reset da palavra-passe de autosserviço no ecrã de login. Com esta funcionalidade, os utilizadores podem repor as palavras-passe sem terem de utilizar outro dispositivo para aceder a um browser.

Mais informações para os utilizadores sobre a utilização desta funcionalidade podem ser encontradas no [Reset do seu trabalho ou palavra-passe escolar](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Passos seguintes

Para simplificar a experiência de registo do utilizador, pode [pré-povoar as informações de contacto de autenticação do utilizador para SSPR](howto-sspr-authenticationdata.md).

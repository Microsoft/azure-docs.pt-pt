---
title: Microsoft Teams no Windows Virtual Desktop - Azure
description: Como utilizar as equipas do Microsoft no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0c528f183106472850d6b5d2a8b492ea8939eda6
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285263"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Utilize equipas da Microsoft no ambiente de trabalho virtual do Windows

>[!IMPORTANT]
>A otimização dos meios de comunicação para equipas é suportada para o Governo da Microsoft 365 (GCC) e GCC-High ambientes. A otimização dos meios de comunicação para equipas não é suportada para o DoD 365 da Microsoft.

>[!NOTE]
>A otimização de meios para o Microsoft Teams só está disponível para o cliente do Windows Desktop em máquinas Windows 10. As otimizações dos meios de comunicação requerem a versão 1.2.1026.0 ou posterior do cliente do Windows Desktop.

As equipas do Microsoft no Windows Virtual Desktop suportam o chat e a colaboração. Com otimizações de meios de comunicação, também suporta a funcionalidade de chamada e reunião. Para saber mais sobre como utilizar as Equipas microsoft em ambientes de Infraestruturas de Ambiente de Trabalho Virtuais (VDI), consulte [Equipas para Infraestruturas de Ambiente de Trabalho Virtualizadas](/microsoftteams/teams-for-vdi/).

Com a otimização dos meios de comunicação para as Equipas microsoft, o cliente do Windows Desktop lida com áudio e vídeo localmente para chamadas e reuniões de Equipas. Ainda pode utilizar as Equipas microsoft no Windows Virtual Desktop com outros clientes sem chamadas e reuniões otimizadas. As funcionalidades de chat e colaboração das equipas são suportadas em todas as plataformas. Para redirecionar dispositivos locais na sua sessão remota, consulte [as propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões.](#customize-remote-desktop-protocol-properties-for-a-host-pool)

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar as Equipas microsoft no Windows Virtual Desktop, terá de fazer estas coisas:

- [Prepare a sua rede](/microsoftteams/prepare-network/) para as Equipas microsoft.
- Instale o [cliente Windows Desktop](connect-windows-7-10.md) num dispositivo Do Windows 10 ou Windows 10 IoT Enterprise que satisfaça os [requisitos de hardware](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)das Equipas microsoft teams para equipas num PC Windows .
- Ligue-se a uma máquina virtual do Windows 10 ou do Windows 10 Enterprise (VM).

## <a name="install-the-teams-desktop-app"></a>Instale a aplicação de desktop Teams

Esta secção irá mostrar-lhe como instalar a aplicação de desktop Teams na sua multi-sessão Do Windows 10 ou na imagem VM do Windows 10 Enterprise. Para saber mais, confira [instalar ou atualizar a aplicação de ambiente de trabalho Teams no VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi).

### <a name="prepare-your-image-for-teams"></a>Prepare a sua imagem para equipas

Para permitir a otimização dos meios de comunicação para as equipas, desa estale a seguinte chave de registo no anfitrião:

1. A partir do menu inicial, executar **RegEdit** como administrador. Navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams.** Crie a chave equipas se já não existir.

2. Criar o seguinte valor para a tecla Equipas:

| Nome             | Tipo   | Dados/Valor  |
|------------------|--------|-------------|
| Ambiente IsWVD | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Instale o Serviço De Equipas WebSocket

Instale o mais recente [serviço de redirecionamento WebRTC de desktop remoto](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt) na sua imagem VM. Se encontrar um erro de instalação, instale o [mais recente Microsoft Visual C++ Redistribuable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) e tente novamente.

#### <a name="latest-websocket-service-versions"></a>Versões mais recentes do Serviço WebSocket

A tabela a seguir lista as versões mais recentes do Serviço WebSocket:

|Versão        |Data da versão  |
|---------------|--------------|
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10200611001"></a>Atualizações para a versão 1.0.2006.11001

- Corrigiu um problema em que minimizar a aplicação Teams durante uma chamada ou reunião fez com que o vídeo de entrada caísse.
- Suporte adicional para selecionar um monitor para partilhar em sessões de desktop multi-monitor.

### <a name="install-microsoft-teams"></a>Instalar equipas da Microsoft

Pode implementar a aplicação de ambiente de trabalho Teams utilizando uma instalação por máquina ou por utilizador. Para instalar as Equipas Microsoft no ambiente de ambiente de trabalho virtual do Windows:

1. Descarregue o [pacote DE MSI das equipas](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) que corresponde ao seu ambiente. Recomendamos a utilização do instalador de 64 bits num sistema operativo de 64 bits.

      > [!IMPORTANT]
      > A última atualização da versão 1.3.00.21759 do cliente teams mostrou o fuso horário da UTC em chat, canais e calendário. A nova versão do cliente mostrará o fuso horário de sessão remota.

2. Executar um dos seguintes comandos para instalar o MSI no VM anfitrião:

    - Instalação por utilizador

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        Este processo é a instalação predefinida, que instala equipas na pasta de utilizador **%AppData%.** As equipas não funcionarão corretamente com a instalação por utilizador numa configuração não persistente.

    - Instalação por máquina

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        Isto instala equipas para a pasta Ficheiros de Programa (x86) num sistema operativo de 32 bits e para a pasta Ficheiros de Programa num sistema operativo de 64 bits. Neste ponto, a configuração da imagem dourada está completa. A instalação de Equipas por máquina é necessária para configurações não persistentes.

        Existem duas bandeiras que podem ser definidas ao instalar equipas, **ALLUSER=1** e **ALLUSERS=1**. É importante compreender a diferença entre estes parâmetros. O parâmetro **ALLUSER=1** é utilizado apenas em ambientes VDI para especificar uma instalação por máquina. O parâmetro **ALLUSERS=1** pode ser utilizado em ambientes não-VDI e VDI. Quando define este parâmetro, **o Machine-Wide Instalador de Equipas** aparece no Programa e funcionalidades no Painel de Controlo, bem como aplicações & funcionalidades nas Definições do Windows. Todos os utilizadores com credenciais de administração na máquina podem desinstalar equipas.

        > [!NOTE]
        > Os utilizadores e administradores não podem desativar o lançamento automático das Equipas durante a entrada neste momento.

3. Para desinstalar o MSI do VM anfitrião, execute este comando:

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Isto desinstala equipas da pasta Ficheiros de Programas (x86) ou pasta de Ficheiros de Programas, dependendo do ambiente do sistema operativo.

      > [!NOTE]
      > Quando instalar equipas com a definição DE MSI ALLUSER=1, serão desativadas atualizações automáticas. Recomendamos que atualize as equipas pelo menos uma vez por mês. Para saber mais sobre a implementação da aplicação de ambiente de trabalho Teams, consulte [a aplicação de ambiente de trabalho Das Equipas para o VM.](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)

### <a name="verify-media-optimizations-loaded"></a>Verificar otimizações de mídia carregadas

Depois de instalar o Serviço WebSocket e a aplicação de desktop Teams, siga estes passos para verificar se as otimizações dos meios de comunicação das Equipas foram carregadas:

1. Demita-se e reinicie a aplicação das Equipas.

2. Selecione a imagem do perfil do utilizador e, em seguida, selecione **About**.

3. Selecione **versão**.

      Se as otimizações dos meios de comunicação forem carregadas, o banner irá mostrar-lhe **o Windows Virtual Desktop Media otimizado**. Se o banner mostrar que o **Windows Virtual Desktop Media não está ligado,** saia da aplicação Teams e tente novamente.

4. Selecione a imagem do perfil do utilizador e, em seguida, selecione **Definições**.

      Se as otimizações dos meios de comunicação forem carregadas, os dispositivos áudio e as câmaras disponíveis localmente serão enumerados no menu do dispositivo. Se o menu mostrar **áudio remoto,** saia da aplicação Teams e tente novamente. Se os dispositivos ainda não aparecerem no menu, verifique as definições de Privacidade no seu PC local. Certifique-se de que as permissões da App de Privacidade sob **Definições**  >    >   a definição **Permitir que as aplicações acedam ao seu microfone** é alternada **.** Desligue da sessão remota e volte a ligar e verifique novamente os dispositivos de áudio e vídeo. Para se juntar a chamadas e reuniões com vídeo, também deve conceder permissão para que as aplicações acedam à sua câmara.

      Se as otimizações não carregarem, desinstale as equipas e volte a verificar.

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

Usar equipas num ambiente virtualizado é diferente de usar equipas num ambiente não virtualizado. Para obter mais informações sobre as limitações das equipas em ambientes virtualizados, consulte [equipas de Infraestruturas de Ambiente de Trabalho Virtualizadas.](/microsoftteams/teams-for-vdi#known-issues-and-limitations)

### <a name="client-deployment-installation-and-setup"></a>Implantação, instalação e configuração do cliente

- Com a instalação por máquina, as equipas em VDI não são automaticamente atualizadas da mesma forma que os clientes não-VDI Teams são. Para atualizar o cliente, terá de atualizar a imagem VM instalando um novo MSI.
- A otimização de meios de comunicação para equipas só é suportada para o cliente do Windows Desktop em máquinas que executam o Windows 10.
- A utilização de proxies HTTP explícitos definidos num ponto final não é suportada.

### <a name="calls-and-meetings"></a>Chamadas e reuniões

- O cliente de desktop teams em ambientes de desktop virtual do Windows não suporta a criação de eventos ao vivo, mas pode participar em eventos ao vivo. Por enquanto, recomendamos que crie eventos ao vivo a partir do [cliente web teams](https://teams.microsoft.com) na sua sessão remota.
- Chamadas ou reuniões não suportam atualmente a partilha de aplicações. As sessões de desktop suportam a partilha do ambiente de trabalho.
- Dê o controlo e assuma o controlo não está apoiado.
- As equipas no Windows Virtual Desktop suportam apenas uma entrada de vídeo recebida de cada vez. Isto significa que sempre que alguém tenta partilhar o seu ecrã, o seu ecrã aparecerá em vez do ecrã do líder da reunião.
- Devido às limitações da WebRTC, a resolução de fluxo de vídeo de entrada e saída está limitada a 720p.
- A aplicação Teams não suporta botões HID ou comandos LED com outros dispositivos.
- A Nova Experiência de Reunião (NME) não é atualmente suportada em ambientes VDI.

Para equipas conhecidas que não estão relacionadas com ambientes virtualizados, consulte [equipas de apoio na sua organização.](/microsoftteams/known-issues)

## <a name="collect-teams-logs"></a>Recolher registos de equipas

Se encontrar problemas com a aplicação de ambiente de trabalho Teams no seu ambiente de ambiente de trabalho virtual do Windows, colete registos de clientes em **%appdata%\Microsoft\Teams\logs.txt** no VM do anfitrião.

Se encontrar problemas com chamadas e reuniões, colete registos de clientes web de Equipas com a combinação chave **Ctrl**  +  **Alt**  +  **Shift**  +  **1**. Os registos serão escritos para **%userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME.txt** no VM do anfitrião.

## <a name="contact-microsoft-teams-support"></a>Contacte o suporte da Microsoft Teams

Para contactar o suporte da Microsoft Teams, aceda ao [centro de administração Microsoft 365](/microsoft-365/admin/contact-support-for-business-products).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalize propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões

A personalização das propriedades do Protocolo remoto de Ambiente de Trabalho (RDP) de uma piscina de anfitriões, como experiência multi-monitor ou ativação de reorientação de microfone e áudio, permite-lhe oferecer uma experiência ideal para os seus utilizadores com base nas suas necessidades.

Não é necessário ativar as reorientações do dispositivo quando se utilizam equipas com otimização de meios. Se estiver a utilizar equipas sem otimização de meios, desaverta as seguintes propriedades RDP para permitir a reorientação do microfone e da câmara:

- `audiocapturemode:i:1` permite a captura de áudio a partir do dispositivo local e redireciona aplicações de áudio na sessão remota.
- `audiomode:i:0` reproduz áudio no computador local.
- `camerastoredirect:s:*` redireciona todas as câmaras.

Para saber mais, consulte [as propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões.](customize-rdp-properties.md)

---
title: Microsoft Teams no Windows Virtual Desktop - Azure
description: Como utilizar as equipas do Microsoft no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8b065a79abe4a4f5c23e28be111b09e51e5e6484
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84667051"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Utilize equipas da Microsoft no ambiente de trabalho virtual do Windows

>[!IMPORTANT]
>A otimização dos meios de comunicação para as Equipas microsoft está atualmente em pré-visualização pública. Recomendamos avaliar a experiência otimizada do utilizador das Equipas antes de implementar equipas para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.

>[!NOTE]
>A otimização de meios para o Microsoft Teams só está disponível para o cliente do Windows Desktop em máquinas Windows 10. As otimizações dos meios de comunicação requerem a versão 1.2.1026.0 ou posterior do cliente do Windows Desktop.

As equipas do Microsoft no Windows Virtual Desktop suportam o chat e a colaboração. Com otimizações de meios de comunicação, também suporta a funcionalidade de chamada e reunião. Para saber mais sobre como utilizar as Equipas microsoft em ambientes de Infraestruturas de Ambiente de Trabalho Virtuais (VDI), consulte [Equipas para Infraestruturas de Ambiente de Trabalho Virtualizadas](/microsoftteams/teams-for-vdi/).

Com a otimização dos meios de comunicação para as Equipas microsoft, o cliente do Windows Desktop lida com áudio e vídeo localmente para chamadas e reuniões de Equipas. Ainda pode utilizar as Equipas microsoft no Windows Virtual Desktop com outros clientes sem chamadas e reuniões otimizadas. As funcionalidades de chat e colaboração das equipas são suportadas em todas as plataformas. Para redirecionar dispositivos locais na sua sessão remota, consulte [as propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões.](#customize-remote-desktop-protocol-properties-for-a-host-pool)

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar as Equipas microsoft no Windows Virtual Desktop, terá de fazer estas coisas:

- [Prepare a sua rede](/microsoftteams/prepare-network/) para as Equipas microsoft.
- Instale o [cliente Windows Desktop](connect-windows-7-and-10.md) num dispositivo Do Windows 10 ou Windows 10 IoT Enterprise que satisfaça os [requisitos de hardware](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)das Equipas microsoft teams para equipas num PC Windows .
- Ligue-se a uma máquina virtual do Windows 10 ou do Windows 10 Enterprise (VM).
- Instale a aplicação de ambiente de trabalho Teams no anfitrião utilizando a instalação por máquina. A otimização dos meios de comunicação para as Equipas da Microsoft requer a versão 1.3.00.4461 ou posterior da aplicação de ambiente de trabalho das Equipas.

## <a name="install-the-teams-desktop-app"></a>Instale a aplicação de desktop Teams

Esta secção irá mostrar-lhe como instalar a aplicação de desktop Teams na sua multi-sessão Do Windows 10 ou na imagem VM do Windows 10 Enterprise. Para saber mais, confira [instalar ou atualizar a aplicação de ambiente de trabalho Teams no VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi/).

### <a name="prepare-your-image-for-teams"></a>Prepare a sua imagem para equipas

Para ativar a instalação de Equipas por máquina, desa estale a seguinte chave de registo no anfitrião:

1. A partir do menu inicial, executar **RegEdit** como administrador. Navegue para **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams**.
2. Criar o seguinte valor para a tecla Equipas:

| Name             | Tipo   | Dados/Valor  |
|------------------|--------|-------------|
| Ambiente IsWVD | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Instale o Serviço De Equipas WebSocket

Instale o [Serviço WebSocket](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4yj0i) na sua imagem VM. Se encontrar um erro de instalação, instale o [mais recente Microsoft Visual C++ Redistribuable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) e tente novamente.

### <a name="install-microsoft-teams"></a>Instalar equipas da Microsoft

Pode implementar a aplicação de ambiente de trabalho Teams utilizando uma instalação por máquina. Para instalar as Equipas Microsoft no ambiente de ambiente de trabalho virtual do Windows:

1. Descarregue o [pacote DE MSI das equipas](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/) que corresponde ao seu ambiente. Recomendamos a utilização do instalador de 64 bits num sistema operativo de 64 bits.

      > [!NOTE]
      > A otimização dos meios de comunicação para as Equipas da Microsoft requer a versão 1.3.00.4461 ou posterior da aplicação de ambiente de trabalho das Equipas.

2. Executar este comando para instalar o MSI no VM anfitrião.

      ```console
      msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1 ALLUSERS=1
      ```

      Isto instala equipas para a pasta Ficheiros de Programa (x86) num sistema operativo de 64 bits e para a pasta Ficheiros de Programa num sistema operativo de 32 bits. Neste ponto, a configuração da imagem dourada está completa. A instalação de Equipas por máquina é necessária para configurações não persistentes.

      Da próxima vez que abrires equipas numa sessão, serão-te pedidos as tuas credenciais.

      > [!NOTE]
      > Os utilizadores e administradores não podem desativar o lançamento automático das Equipas durante a entrada neste momento.

      Para desinstalar o MSI do VM anfitrião, execute este comando:

      ```console
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Isto desinstala equipas da pasta Ficheiros de Programas (x86) ou pasta de Ficheiros de Programas, dependendo do ambiente do sistema operativo.

      > [!NOTE]
      > Quando instalar equipas com a definição DE MSI ALLUSER=1, serão desativadas atualizações automáticas. Recomendamos que atualize as equipas pelo menos uma vez por mês. Para saber mais sobre a implementação da aplicação de ambiente de trabalho Teams, consulte [a aplicação de ambiente de trabalho Das Equipas para o VM.](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)

### <a name="verify-media-optimizations-loaded"></a>Verificar otimizações de mídia carregadas

Depois de instalar o Serviço WebSocket e a aplicação de desktop Teams, siga estes passos para verificar se as otimizações dos meios de comunicação das Equipas foram carregadas:

1. Selecione a imagem do perfil do utilizador e, em seguida, selecione **About**.
2. Selecione **versão**.

      Se as otimizações dos meios de comunicação forem carregadas, o banner irá mostrar-lhe **wVD Media otimizado**. Se o banner mostrar que a **WVD Media não está conectada,** saia da aplicação Teams e tente novamente.

3. Selecione a imagem do perfil do utilizador e, em seguida, selecione **Definições**.

      Se as otimizações dos meios de comunicação forem carregadas, os dispositivos áudio e as câmaras disponíveis localmente serão enumerados no menu do dispositivo. Se o menu mostrar **áudio remoto,** saia da aplicação Teams e tente novamente. Se os dispositivos ainda não aparecerem no menu, volte a instalar as [Equipas da Microsoft](#install-microsoft-teams) e certifique-se de que concluiu o processo de instalação.

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

Usar equipas num ambiente virtualizado é diferente de usar equipas num ambiente não virtualizado. Para obter mais informações sobre as limitações das equipas em ambientes virtualizados, consulte [equipas de Infraestruturas de Ambiente de Trabalho Virtualizadas.](/microsoftteams/teams-for-vdi#known-issues-and-limitations/)

### <a name="client-deployment-installation-and-setup"></a>Implantação, instalação e configuração do cliente

- Com a instalação por máquina, as equipas em VDI não são automaticamente atualizadas da mesma forma que os clientes não-VDI Teams são. Para atualizar o cliente, terá de atualizar a imagem VM instalando um novo MSI.
- Atualmente, as equipas apenas mostram o fuso horário UTC em chat, canais e calendário.
- A otimização de meios de comunicação para equipas só é suportada para o cliente do Windows Desktop em máquinas que executam o Windows 10.
- A utilização de proxies HTTP explícitos definidos num ponto final não é suportada.

### <a name="calls-and-meetings"></a>Chamadas e reuniões

- O cliente de desktop teams em ambientes de desktop virtual do Windows não suporta eventos ao vivo. Por enquanto, recomendamos que se junte a eventos ao vivo do [cliente web teams](https://teams.microsoft.com) na sua sessão remota.
- Minimizar a aplicação Teams durante uma chamada ou reunião pode resultar no desaparecimento do feed de vídeo de entrada quando expandir a aplicação.
- Chamadas ou reuniões não suportam atualmente a partilha de aplicações. As sessões de desktop suportam a partilha do ambiente de trabalho.
- Quando a partilha do ambiente de trabalho numa configuração multi-monitor, todos os monitores são partilhados.
- Dê o controlo e assuma o controlo não está apoiado.
- As equipas no Windows Virtual Desktop suportam apenas uma entrada de vídeo recebida de cada vez. Isto significa que sempre que alguém tenta partilhar o seu ecrã, o seu ecrã aparecerá em vez do ecrã do líder da reunião.
- Devido às limitações da WebRTC, a resolução de fluxo de vídeo de entrada e saída está limitada a 720p.
- A aplicação Teams não suporta botões HID ou comandos LED com outros dispositivos.

Para equipas conhecidas questões que não estão relacionadas com ambientes virtualizados, consulte [equipas de apoio na sua organização](/microsoftteams/known-issues/)

## <a name="feedback"></a>Comentários

Fornecer feedback para equipas da Microsoft no Ambiente de Trabalho Virtual do Windows no site Teams [UserVoice](https://microsoftteams.uservoice.com/).

## <a name="collect-teams-logs"></a>Recolher registos de equipas

Se encontrar problemas com a aplicação de ambiente de trabalho Teams no seu ambiente de ambiente de trabalho virtual do Windows, colete registos de clientes em **%appdata%\Microsoft\Teams\logs.txt** no VM do anfitrião.

Se encontrar problemas com chamadas e reuniões, colete registos de clientes web de Equipas com a combinação chave **Ctrl**  +  **Alt**  +  **Shift**  +  **1**. Os registos serão escritos para **%userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME.txt** no VM do anfitrião.

## <a name="contact-microsoft-teams-support"></a>Contacte o suporte da Microsoft Teams

Para contactar o suporte da Microsoft Teams, aceda ao [centro de administração Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/contact-support-for-business-products?view=o365-worldwide&tabs=online).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalize propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões

A personalização das propriedades do Protocolo remoto de Ambiente de Trabalho (RDP) de uma piscina de anfitriões, como experiência multi-monitor ou ativação de reorientação de microfone e áudio, permite-lhe oferecer uma experiência ideal para os seus utilizadores com base nas suas necessidades.

Não é necessário ativar as reorientações do dispositivo quando se utilizam equipas com otimização de meios. Se estiver a utilizar equipas sem otimização de meios, desaverta as seguintes propriedades RDP para permitir a reorientação do microfone e da câmara:

- `audiocapturemode:i:1`permite a captura de áudio a partir do dispositivo local e redirets aplicações de áudio na sessão remota.
- `audiomode:i:0`reproduz áudio no computador local.
- `camerastoredirect:s:*`redireciona todas as câmaras.

Para saber mais, consulte [as propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões.](customize-rdp-properties.md)

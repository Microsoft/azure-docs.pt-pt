---
title: Configurar reorientações de dispositivos - Azure
description: Como configurar as reorientações de dispositivos para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 37ecd06c4e3e71234e8fb1b6bad0cd05482dd31b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727854"
---
# <a name="configure-device-redirections"></a>Configurar redirecionamentos de dispositivo

Configurar as reorientações de dispositivos para o ambiente de ambiente de trabalho virtual do Windows permite-lhe utilizar impressoras, dispositivos USB, microfones e outros dispositivos periféricos na sessão remota. Algumas reorientações do dispositivo requerem alterações nas propriedades do Protocolo de Ambiente de Trabalho Remoto (RDP) e nas definições de Política de Grupo.

## <a name="supported-device-redirections"></a>Redirecionamentos de dispositivos suportados

Cada cliente suporta diferentes redirecionamentos de dispositivos. Confira [os clientes](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) para a lista completa de redirecionamentos de dispositivos suportados para cada cliente.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Personalizar propriedades RDP para uma piscina de anfitriões

Para saber mais sobre a personalização das propriedades RDP para uma piscina de anfitriões utilizando o PowerShell ou o portal Azure, consulte [as propriedades RDP](customize-rdp-properties.md). Para obter a lista completa das propriedades RDP suportadas, consulte [as definições de ficheiros RDP suportados](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Redirecionamentos de dispositivos de configuração

Pode utilizar as seguintes propriedades RDP e definições de Política de Grupo para configurar as reorientações do dispositivo.

### <a name="audio-input-microphone-redirection"></a>Reorientação de entrada de áudio (microfone)

Desafie a seguinte propriedade RDP para configurar a reorientação da entrada de áudio:

- `audiocapturemode:i:1` permite a reorientação da entrada de áudio.
- `audiocapturemode:i:0` desativa a reorientação da entrada de áudio.

### <a name="audio-output-speaker-redirection"></a>Reorientação da saída de áudio (altifalante)

Desafie a seguinte propriedade RDP para configurar a reorientação da saída de áudio:

- `audiomode:i:0` permite a reorientação da saída de áudio.
- `audiomode:i:1` ou `audiomode:i:2` desativar a reorientação da saída de áudio.

### <a name="camera-redirection"></a>Redirecionamento da câmera

Desafie a seguinte propriedade RDP para configurar a reorientação da câmara:

- `camerastoredirect:s:*` redireciona todas as câmaras.
- `camerastoredirect:s:` desativa a reorientação da câmara.

>[!NOTE]
>Mesmo que a `camerastoredirect:s:` propriedade esteja desativada, as câmaras locais podem ser redirecionadas através da `devicestoredirect:s:` propriedade. Para desativar totalmente o conjunto de reorientações da câmara `camerastoredirect:s:` e definir ou definir algum `devicestoredirect:s:` subconjunto de dispositivos de ficha e reprodução que não incluam nenhuma câmara.

Também pode redirecionar câmaras específicas usando uma lista de interfaces KSCATEGORY_VIDEO_CAMERA deslimitadas em pontos, tais como `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` .

### <a name="clipboard-redirection"></a>Reorientação da área de transferência

Desafie a seguinte propriedade RDP para configurar a reorientação da área de transferência:

- `redirectclipboard:i:1` permite a reorientação da área de transferência.
- `redirectclipboard:i:0` desativa a reorientação da área de transferência.

### <a name="com-port-redirections"></a>Redirecionamentos de porta COM

Desafie a seguinte propriedade RDP para configurar a reorientação da porta COM:

- `redirectcomports:i:1` permite a reorientação da porta COM.
- `redirectcomports:i:0` desativa a reorientação da porta COM.

### <a name="usb-redirection"></a>Reorientação USB

Em primeiro lugar, desaver a seguinte propriedade RDP para permitir a reorientação do dispositivo USB:

- `usbdevicestoredirect:s:*` permite a reorientação do dispositivo USB.
- `usbdevicestoredirect:s:` desativa a reorientação do dispositivo USB.

Em segundo lugar, desa um conjunto da seguinte Política de Grupo no dispositivo local do utilizador:

- Navegue para políticas **de configuração do computador**  >   >  **Modelos administrativos** Windows Componentes  >  **Remotos**  >  **serviços de ambiente de trabalho** remoto  >  **ligação de ambiente de trabalho cliente**  >  **RemoteFX USB Redirecionamento de dispositivo**.
- Selecione **Permite a reorientação rdp de outros dispositivos USB RemoteFX suportados a partir deste computador**.
- Selecione a opção **Ativada** e, em seguida, selecione os Administradores e Utilizadores na caixa **de direitos de acesso à reorientação USB RemoteFX.**
- Selecione **OK**.

### <a name="plug-and-play-device-redirection"></a>Redireccionamento de dispositivo de plug-and play

Desafie a seguinte propriedade RDP para configurar a ficha e redirecionar o dispositivo de reprodução:

- `devicestoredirect:s:*` permite a reorientação de todos os dispositivos de plug e play.
- `devicestoredirect:s:` desativa a reorientação de dispositivos de ficha e de reprodução.

Também pode selecionar dispositivos de tomada e reprodução específicos utilizando uma lista delimitada em pontos, tais como `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Redirecionamento de unidade local

Desafie a seguinte propriedade RDP para configurar a reorientação de unidade local:

- `drivestoredirect:s:*` permite a reorientação de todas as unidades de disco.
- `drivestoredirect:s:` desativa a reorientação de unidade local.

Também pode selecionar unidades específicas utilizando uma lista delimitadas em pontos, tais como `drivestoredirect:s:C:;E:;` .

Para configurar a transferência de ficheiros do cliente web, definir `drivestoredirect:s:*` .

### <a name="printer-redirection"></a>Redirecionamento da impressora

Desafie a seguinte propriedade RDP para configurar a reorientação da impressora:

- `redirectprinters:i:1` permite a reorientação da impressora.
- `redirectprinters:i:0` desativa a reorientação da impressora.

### <a name="smart-card-redirection"></a>Redirecionamento de cartão inteligente

Desconfiem da seguinte propriedade RDP para configurar a reorientação do cartão inteligente:

- `redirectsmartcards:i:1` permite a reorientação de cartões inteligentes.
- `redirectsmartcards:i:0` desativa a reorientação de cartões inteligentes.

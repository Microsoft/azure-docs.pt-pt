---
title: Máquinas virtuais Windows 7 Windows Virtual Desktop - Azure
description: Como resolver problemas para máquinas virtuais do Windows 7 (VMs) num ambiente de ambiente de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127385"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Resolução de problemas das máquinas virtuais do Windows 7 no Windows Virtual Desktop

Utilize este artigo para resolver problemas que está a ter ao configurar as máquinas virtuais do Windows Virtual Desktop (VMs).

## <a name="known-issues"></a>Problemas conhecidos

O Windows 7 nos Desktops Virtuais do Windows não suporta as seguintes funcionalidades:

- Aplicações virtualizadas (Aplicações Remotas)
- Redireção do fuso horário
- Escala automática de DPI

O Windows Virtual Desktop só pode virtualizar desktops completos para o Windows 7.

Embora a escala automática de DPI não seja suportada, pode alterar manualmente a resolução na sua máquina virtual clicando corretamente no ícone no cliente Remote Desktop e selecionando **Resolução**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Erro: Não pode aceder ao grupo de utilizadores de ambiente de trabalho remoto

Se o Windows Virtual Desktop não conseguir encontrar as credenciais dos seus utilizadores no grupo utilizador remote Desktop, poderá ver uma das seguintes mensagens de erro:

- "Este utilizador não é membro do grupo de utilizadores de ambiente de trabalho remoto"
- "Deve ser-lhe concedida permissão para iniciar sessão através dos Serviços de Ambiente de Trabalho Remoto"

Para corrigir este erro, adicione o utilizador ao grupo utilizador remote Desktop User:

1. Abra o portal do Azure.
2. Selecione a máquina virtual em que viu a mensagem de erro.
3. Selecione **Executar um comando**.
4. Executar o seguinte `<username>` comando substituído pelo nome do utilizador que pretende adicionar:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```
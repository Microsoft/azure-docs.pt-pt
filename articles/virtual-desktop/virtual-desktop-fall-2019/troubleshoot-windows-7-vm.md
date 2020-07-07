---
title: Windows 7 máquinas virtuais Windows Virtual Desktop - Azure
description: Como resolver problemas para máquinas virtuais (VMs) do Windows 7 num ambiente de ambiente de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 74f2e22bcc9d75070e4f7af304f92d9c5640ca7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82615282"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Resolução de problemas das máquinas virtuais do Windows 7 no Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure.

Utilize este artigo para resolver problemas que está a ter ao configurar as máquinas virtuais de sessão de desktop virtual do Windows (VMs).

## <a name="known-issues"></a>Problemas conhecidos

O Windows 7 no Windows Virtual Desktops não suporta as seguintes funcionalidades:

- Aplicações virtualizadas (RemoteApps)
- Reorientação do fuso horário
- Escala automática de DPI

O Windows Virtual Desktop só pode virtualizar ambientes de trabalho completos para o Windows 7.

Embora o escalonamento automático do DPI não seja suportado, pode alterar manualmente a resolução na sua máquina virtual clicando no ícone no cliente Remote Desktop e selecionando **a Resolução.**

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Erro: Não é possível aceder ao grupo de utilizadores de desktop remoto

Se o Windows Virtual Desktop não conseguir encontrar as credenciais de si ou dos seus utilizadores no grupo de utilizadores do Utilizador do Ambiente de Trabalho Remoto, poderá ver uma das seguintes mensagens de erro:

- "Este utilizador não é membro do grupo utilizador de desktop remoto"
- "Deve ser-lhe concedidas permissões para iniciar sedução através de Serviços remotos de desktop"

Para corrigir este erro, adicione o utilizador ao grupo utilizador de ambiente de trabalho remoto:

1. Abra o portal do Azure.
2. Selecione a máquina virtual em que viu a mensagem de erro acesa.
3. **Selecione Executar um comando**.
4. Executar o seguinte comando `<username>` com substituído pelo nome do utilizador que pretende adicionar:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```
---
title: Solucionar problemas de máquinas virtuais do Windows 7 na área de trabalho virtual do Windows – Azure
description: Como resolver problemas de VMs (máquinas virtuais) do Windows 7 em um ambiente de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: 7fdb46ac7cffc4eb6a791304c7a6b58378806296
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679896"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Solucionar problemas de máquinas virtuais do Windows 7 na área de trabalho virtual do Windows

Use este artigo para solucionar problemas que você está tendo ao configurar as VMs (máquinas virtuais) do host de sessão de área de trabalho virtual do Windows.

## <a name="known-issues"></a>Problemas conhecidos

O Windows 7 em áreas de trabalho virtuais do Windows não oferece suporte aos seguintes recursos:

- Aplicativos virtualizados (RemoteApps)
- Redirecionamento de fuso horário
- Dimensionamento automático de DPI

A área de trabalho virtual do Windows só pode virtualizar áreas de trabalho completas para o Windows 7.

Embora não haja suporte para o dimensionamento automático de DPI, você pode alterar manualmente a resolução em sua máquina virtual clicando com o botão direito do mouse no ícone no cliente Área de Trabalho Remota e selecionando **resolução**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Erro: Não é possível acessar o grupo de usuários Área de Trabalho Remota

Se a área de trabalho virtual do Windows não conseguir localizar você ou as credenciais de seus usuários no grupo Área de Trabalho Remota usuário, você poderá ver uma das seguintes mensagens de erro:

- "Este usuário não é membro do grupo de usuários do Área de Trabalho Remota"
- "Você deve receber permissões para entrar por meio do Serviços de Área de Trabalho Remota"

Para corrigir esse erro, adicione o usuário ao grupo de usuários Área de Trabalho Remota:

1. Abra o portal do Azure.
2. Selecione a máquina virtual na qual você viu a mensagem de erro.
3. Selecione **executar um comando**.
4. Execute o seguinte comando com `<username>` substituído pelo nome do usuário que você deseja adicionar:
   
   ```cmd
   localgroup "Remote Desktop Users" <username> /add
   ```
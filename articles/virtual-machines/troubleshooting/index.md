---
layout: LandingPage
description: Saiba como resolver problemas de implementação de máquinas virtuais.
title: Documentação de Resolução de Problemas das Máquinas Virtuais do Azure | Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: bdb459a69557269a20d36f05acc2da502064efb9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851354"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Resolução de problemas de máquinas virtuais do Azure

## <a name="tools-for-troubleshooting"></a>Ferramentas de resolução de problemas

- [Consola de Série](serial-console-overview.md)
- [Diagnósticos de Arranque](boot-diagnostics.md)
- [VM do Windows: anexe o disco de SO a outra VM para resolução de problemas](troubleshoot-recovery-disks-portal-windows.md)
- [VM do Linux: anexe o disco de SO a outra VM para resolução de problemas](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Não é possível ligar à VM

### <a name="windows"></a>Windows

**Solução comum**

- [Repor o RDP](reset-rdp.md)
- [Resolução de problemas de RDP](troubleshoot-rdp-connection.md)
- [Resolução de problemas detalhada de RDP](detailed-troubleshoot-rdp.md)

**Erros de RDP**

- [Não existe um servidor de licenças](troubleshoot-rdp-no-license-server.md)
- [Um ](Troubleshoot-rdp-internal-error.md) interno
- [Erros de autenticação](troubleshoot-authentication-error-rdp-vm.md)
- [Resolver problemas de erros específicos](troubleshoot-specific-rdp-errors.md)

**Erros de arranque da VM**

* [Erros de arranque do BitLocker](troubleshoot-bitlocker-boot-error.md)
* [O Windows mostra "A verificar o sistema de ficheiros" durante o arranque](troubleshoot-check-disk-boot-error.md)
* [Erros comuns de ecrã azul](troubleshoot-common-blue-screen-error.md)
* [O arranque da VM está bloqueado em "A Preparar o Windows](troubleshoot-vm-boot-configure-update.md)
* [Erro "FALHA DE SERVIÇO CRÍTICO" em ecrã azul](troubleshoot-critical-service-failed-boot-error.md)
* [Problema de ciclo de reinicialização](troubleshoot-reboot-loop.md)
* [O arranque da VM está bloqueado na fase de atualização do Windows](troubleshoot-stuck-updating-boot-error.md)
* [A VM é inicializada em Modo de Segurança](troubleshoot-rdp-safe-mode.md)

**Outro**
- [Reposição da palavra-passe da VM do Windows offline](reset-local-password-without-agent.md)
- [Reposição do NIC após configuração incorreta](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Resolução de problemas de SSH](troubleshoot-ssh-connection.md)
- [Resolução de problemas detalhada de SSH](detailed-troubleshoot-ssh-connection.md)
- [Mensagens de erro comuns](error-messages.md)
- [Reposição da palavra-passe da VM do Linux offline](reset-password.md)

## <a name="vm-deployment-issues"></a>Problemas de implementação da VM

- [Falhas de alocação](allocation-failure.md)
- Reimplementar uma VM
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Resolução de problemas de implementações
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Os nomes de dispositivo foram alterados](troubleshoot-device-names-problems.md)
- [Instalar o agente offline da VM do Windows](install-vm-agent-offline.md)
- [Reiniciar ou redimensionar uma VM](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Problema de desempenho da VM
- [Problemas de desempenho das VMs](performance-diagnostics.md)
- Windows
    - [Como utilizar o PerfInsights](how-to-use-perfinsights.md)
    - [Extensão de diagnóstico de desempenho](performance-diagnostics-vm-extension.md)
- Linux
    - [Como utilizar o PerfInsights](how-to-use-perfinsights-linux.md)
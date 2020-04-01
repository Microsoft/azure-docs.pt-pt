---
title: Firewall Os do Os do Hóspede Azure está mal configurado / Microsoft Docs
description: Aprenda a utilizar a consola em série ou o método offline para diagnosticar e corrigir uma firewall do sistema operativo de hóspedes mal configurada num VM Azure remoto.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e6f42bdf462ac5261f77bc05c62e50500345fe37
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422539"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Firewall os hóspedes do Azure VM está mal configurado

Este artigo introduz como corrigir firewall do sistema operativo de hóspedes configurado de forma misconfigurada no Azure VM.

## <a name="symptoms"></a>Sintomas

1.  O ecrã de boas-vindas da máquina virtual (VM) mostra que o VM está totalmente carregado.

2.  Dependendo da configuração do sistema operativo dos hóspedes, pode haver algum ou nenhum tráfego de rede a chegar ao VM.

## <a name="cause"></a>Causa

Uma configuração errada da firewall do sistema de hóspedes pode bloquear alguns ou todos os tipos de tráfego de rede para o VM.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco do sistema do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize a Consola em Série ou [repare o VM offline,](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) fixando o disco de sistema do VM a um VM de recuperação.

## <a name="online-mitigations"></a>Mitigações on-line

Ligue-se à [Consola em Série e, em seguida, abra uma instância PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se a Consola em Série não estiver ativada no VM, vá à secção "Reparar o VM Offline" do seguinte artigo do Azure:

 [An internal error occurs when you try to connect to an Azure VM through Remote Desktop](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) (Um erro interno ocorre quando se tenta ligar a uma VM do Azure através do Ambiente de Trabalho Remoto)

As seguintes regras podem ser editadas para permitir o acesso ao VM (através de RDP) ou para proporcionar uma experiência mais fácil de resolução de problemas:

*   Ambiente de Trabalho Remoto (TCP-In): Esta é a regra padrão que proporciona acesso primário ao VM permitindo RDP em Azure.

*   Windows Remote Management (HTTP-In): Esta regra permite-lhe ligar-se ao VM utilizando powerShell., In Azure, este tipo de acesso permite-lhe utilizar o aspeto de scripting remoto de scripts remotos e resolução de problemas.

*   Partilha de ficheiros e impressoras (SMB-In): Esta regra permite o acesso à partilha de rede como uma opção de resolução de problemas.

*   Partilha de ficheiros e impressoras (Echo Request - ICMPv4-In): Esta regra permite-lhe pingar o VM.

Na instância de Acesso à Consola Em Série, pode consultar o estado atual da regra da firewall.

*   Consulta utilizando o Nome do Visor como parâmetro:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Consulta utilizando o Porto Local que a aplicação utiliza:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Consulta utilizando o endereço IP local que a aplicação utiliza:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Se vir que a regra está desativada, pode activar-a executando o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Para resolução de problemas, pode desligar os perfis de firewall:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Se fizer isto para definir corretamente a firewall, reative a firewall depois de terminar a sua resolução de problemas.

    > [!Note]
    > Não é preciso reiniciar o VM para aplicar esta mudança.

*   Tente voltar a ligar-se ao VM através de RDP.

### <a name="offline-mitigations"></a>Mitigações Offline

1.  Para ativar ou desativar as regras de firewall, consulte [ativar ou desativar uma regra de firewall num Sistema de Hóspedes Azure VM](enable-disable-firewall-rule-guest-os.md).

2.  Verifique se está na [firewall do Os do Hóspede bloqueando o cenário](guest-os-firewall-blocking-inbound-traffic.md)de tráfego de entrada .

3.  Se ainda tiver dúvidas sobre se a firewall está a bloquear o seu acesso, consulte [o Desactivador do Sistema OPERATIVO de saúde em Azure VM](disable-guest-os-firewall-windows.md)e, em seguida, reativar a firewall do sistema de hóspedes utilizando as regras corretas.


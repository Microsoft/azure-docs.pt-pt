---
title: A firewall Azure VM Guest OS está mal configurada Microsoft Docs
description: Aprenda a utilizar a Consola em Série ou o método offline para diagnosticar e corrigir uma firewall do sistema operativo de hóspedes mal configurada num Azure VM remoto.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80422539"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>A firewall os OS convidados Azure VM está mal configurada

Este artigo introduz como corrigir a firewall do sistema operativo de hóspedes mal configurado no Azure VM.

## <a name="symptoms"></a>Sintomas

1.  O ecrã de boas-vindas da máquina virtual (VM) mostra que o VM está totalmente carregado.

2.  Dependendo da configuração do sistema operativo do hóspede, pode haver algum ou nenhum tráfego de rede a chegar ao VM.

## <a name="cause"></a>Causa

Uma configuração errada da firewall do sistema de hóspedes pode bloquear algum ou todo o tipo de tráfego de rede para o VM.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco do sistema do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize a Consola em Série ou [repare o VM offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) ligando o disco de sistema do VM a um VM de recuperação.

## <a name="online-mitigations"></a>Mitigações on-line

Ligue-se à [Consola em Série e, em seguida, abra uma instância PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se a Consola em Série não estiver ativada no VM, aceda à secção "Reparar o VM Offline" do seguinte artigo Azure:

 [An internal error occurs when you try to connect to an Azure VM through Remote Desktop](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) (Um erro interno ocorre quando se tenta ligar a uma VM do Azure através do Ambiente de Trabalho Remoto)

As seguintes regras podem ser editadas para permitir o acesso ao VM (através de PDR) ou para proporcionar uma experiência de resolução de problemas mais fácil:

*   Ambiente de trabalho remoto (TCP-In): Esta é a regra padrão que proporciona acesso primário ao VM, permitindo RDP em Azure.

*   Gestão Remota do Windows (HTTP-In): Esta regra permite-lhe ligar-se ao VM utilizando o PowerShell., Em Azure, este tipo de acesso permite-lhe utilizar o aspeto de scripting de scripts remotos e resolução de problemas.

*   Partilha de ficheiros e impressoras (SMB-In): Esta regra permite o acesso à partilha de rede como opção de resolução de problemas.

*   Partilha de ficheiros e impressoras (Pedido de Eco - ICMPv4-In): Esta regra permite-lhe pingar o VM.

Na instância de Acesso à Consola em Série, pode consultar o estado atual da regra de firewall.

*   Consulta utilizando o Nome de Visualização como parâmetro:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Consulta utilizando a Porta Local que a aplicação utiliza:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Consulta utilizando o endereço IP local que a aplicação utiliza:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Se vir que a regra está desativada, pode ativá-la executando o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Para a resolução de problemas, pode desligar os perfis de firewall:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Se o fizer para definir corretamente a firewall, reecam ative a firewall depois de terminar a resolução de problemas.

    > [!Note]
    > Não é preciso reiniciar o VM para aplicar esta alteração.

*   Tente novamente ligar-se ao VM através do RDP.

### <a name="offline-mitigations"></a>Mitigações offline

1.  Para ativar ou desativar as regras de firewall, consulte [ativar ou desativar uma regra de firewall num Os Hóspedes Azure VM](enable-disable-firewall-rule-guest-os.md).

2.  Verifique se está na firewall do [Guest OS bloqueando](guest-os-firewall-blocking-inbound-traffic.md)o cenário de tráfego de entrada .

3.  Se ainda tiver dúvidas sobre se a firewall está a bloquear o seu acesso, consulte [o oss desativação do osactivo do hóspede em Azure VM](disable-guest-os-firewall-windows.md), e, em seguida, reative a firewall do sistema de hóspedes utilizando as regras corretas.


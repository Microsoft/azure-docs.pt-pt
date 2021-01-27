---
title: Resolução de problemas do Agente Azure Linux
description: Resolva problemas com o agente Azure Linux.
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 62b462d8e75fc291ac599ac99dbe4fb3a74fde2b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878702"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Resolução de problemas do Agente Azure Linux

O [Agente Azure Linux](../extensions/agent-linux.md) permite que uma máquina virtual (VM) comunique com o Controlador de Tecidos (o servidor físico subjacente no qual o VM está hospedado) no endereço IP 168.63.129.16.

>[!NOTE]
>Este endereço IP é um endereço IP público virtual que facilita a comunicação e não deve ser bloqueado. Para mais informações, consulte [o endereço IP 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md)

## <a name="before-you-begin"></a>Antes de começar

Verifique o estado e a versão do agente para se certificar de que ainda está suportado. Consulte [o suporte mínimo de versão para agentes de máquinas virtuais em Azure](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) para verificar o suporte da versão ou consulte [WALinuxAgent FAQ](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) para obter etapas para encontrar o estado e a versão.

## <a name="troubleshoot-a-not-ready-status"></a>Resolução de problemas um estado de não pronto

1. Verifique o estado de serviço do agente Azure Linux para se certificar de que está a funcionar. O nome de serviço pode ser **walinuxagent** ou **waagent**.

   ```
   root@nam-u18:/home/nam# service walinuxagent status
   ● walinuxagent.service - Azure Linux Agent
      Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
      Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
    Main PID: 1036 (python3)
       Tasks: 4 (limit: 4915)
      CGroup: /system.slice/walinuxagent.service
              ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
              └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers
   Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
   root@nam-u18:/home/nam#
   ```

   Se o serviço estiver em funcionamento, reinicie-o para resolver o problema. Se o serviço for interrompido, inicie-o, aguarde alguns minutos e verifique novamente o estado.

1. Certifique-se de que a atualização automática está ativada. Verifique a definição de atualização automática em **/etc/waagent.conf**.

   ```
   AutoUpdate.Enabled=y
   ```

   Para obter mais informações sobre como atualizar o Agente Azure Linux, consulte [como atualizar o Agente Azure Linux num VM](../extensions/update-linux-agent.md).

1. Certifique-se de que o VM pode ligar-se ao controlador de tecido. Utilize uma ferramenta como o curl para testar se o VM pode ligar-se a 168.63.129.16 nas portas 80, 443 e 32526. Se o VM não se ligar como esperado, verifique se a comunicação de saída sobre as portas 80, 443 e 32526 está aberta na firewall local no VM. Se este endereço IP estiver bloqueado, o agente VM pode apresentar um comportamento inesperado.

## <a name="advanced-troubleshooting"></a>Resolução de problemas avançados

Os eventos para resolução de problemas do Agente Azure Linux são registados no ficheiro **/var/log/waagent.log.**

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Não é possível ligar ao Ip do WireServer (HOST IP)

O seguinte erro aparece no ficheiro **/var/log/waagent.log** quando o VM não consegue chegar ao IP do WireServer no servidor anfitrião.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

Para resolver este problema:

* Ligue-se ao VM utilizando sSH e tente aceder ao seguinte URL a partir do caracol: http://168.63.129.16/?comp=versions .
* Verifique se podem ser causados por uma firewall, um representante ou outra fonte que possa estar a bloquear o acesso ao endereço IP 168.63.129.16.
* Verifique se o Linux IPTables ou uma firewall de terceiros está a bloquear o acesso às portas 80, 443 e 32526.

## <a name="next-steps"></a>Próximos passos

Para resolver mais problemas, contacte o agente da Azure Linux, [contacte o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
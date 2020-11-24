---
title: Problemas na resolução de Azure Linux Agente Convidado
description: Resolução de problemas O Agente Convidado Azure Linux não está a trabalhar
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
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550003"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Problemas na resolução de Azure Linux Agente Convidado

[Azure Linux Guest Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) é um agente de máquina virtual (VM). Permite que o VM comunique com o Controlador de Tecidos (o servidor físico subjacente no qual o VM está hospedado) no endereço IP 168.63.129.16. Este endereço IP é um endereço IP público virtual que facilita a comunicação. Para mais informações, consulte [o endereço IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="checking-agent-status-and-version"></a>Verificação do estado e versão do agente

Veja https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Agente VM de resolução de problemas que está em estado de não pronto

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>Passo 1 Verifique se o serviço de agente convidado Azure Linux está em execução

Dependendo do distro, o nome de serviço pode ser walinuxagent ou waagent:

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


Se conseguir ver os serviços e eles estiverem a funcionar, reinicie o serviço para ver se o problema está resolvido. Se os serviços estiverem parados, inicie-os e espere alguns minutos. Em seguida, verifique se o **estado do Agente** está a reportar como **Ready**. Para uma maior resolução de problemas destes problemas, contacte [o Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-enabled"></a>Passo 2 Verifique se a atualização automática está ativada

Verifique esta definição em /etc/waagent.conf:

```
AutoUpdate.Enabled=y
```

Para mais informações sobre como atualizar o Agente Azure Linux, consulte https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Passo 3 Verifique se o VM pode ligar-se ao controlador de tecido

Utilize uma ferramenta como o curl para testar se o VM pode ligar-se a 168.63.129.16 nas portas 80, 32526 e 443. Se o VM não se ligar como esperado, verifique se a comunicação de saída sobre as portas 80, 443 e 32526 está aberta na firewall local no VM. Se este endereço IP estiver bloqueado, o Agente de VM poderá ter um comportamento inesperado em vários cenários.

## <a name="advanced-troubleshooting"></a>Resolução de problemas avançados

Os eventos para resolução de problemas Azure Linux Guest Agent são registados nos seguintes ficheiros de registo:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Não é possível ligar ao Ip do WireServer (HOST IP) 

Nota as seguintes entradas de erro em /var/log/waagent.log:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Análise**

O VM não pode chegar ao IP do WireServer no servidor anfitrião.

**Solução**

1. Como o IP do WireServer não é alcançável, ligue-se ao VM utilizando sSH e tente aceder ao seguinte URL a partir do caracol: http://168.63.129.16/?comp=versions 
1. Verifique se existem problemas que possam ser causados por uma firewall, um representante ou outra fonte que possa estar a bloquear o acesso ao endereço IP 168.63.129.16.
1. Verifique se o Linux IPTables ou uma firewall de terceiros está a bloquear o acesso às portas 80, 443 e 32526. Para obter mais informações sobre o motivo pelo qual este endereço não deve ser bloqueado, consulte [o endereço IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="next-steps"></a>Passos seguintes

Para resolver mais problemas, o problema do "Windows Azure Guest Agent não está a funcionar", [contacte o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

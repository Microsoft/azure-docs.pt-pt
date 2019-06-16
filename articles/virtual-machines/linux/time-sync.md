---
title: Tempo de sincronização para VMs do Linux no Azure | Documentos da Microsoft
description: Sincronização de hora para máquinas virtuais do Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 075fc48d4db4c4cfcc6f45f5fe93e8cfb38d5559
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991847"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Sincronização de hora para VMs do Linux no Azure

Sincronização de hora é importante para segurança e a correlação de eventos. Por vezes, é utilizado para a implementação de transações distribuídas. Precisão de hora entre vários sistemas de computador é obtido através da sincronização. Sincronização pode ser afetada por várias coisas, incluindo os reinícios e tráfego de rede entre a origem de hora e o computador a obter a hora. 

Azure beneficia de infraestrutura com o Windows Server 2016. Windows Server 2016 tem melhorados algoritmos utilizados para corrigir o tempo da Microsoft e o relógio local para sincronizar com o UTC.  A funcionalidade de hora exata do Windows Server 2016 melhorou muito como a VMICTimeSync service que rege a VMs com o anfitrião para o tempo preciso. As melhorias incluem tempo inicial mais preciso no início VM ou o restauro de VMS e correção de latência de interrupção. 

>[!NOTE]
>Para obter uma descrição geral do serviço de hora do Windows, dê uma olhada nisso [descrição geral de alto nível vídeo](https://aka.ms/WS2016TimeVideo).
>
> Para obter mais informações, consulte [tempo preciso para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Descrição geral

Precisão de um relógio de computador é gauged sobre como fechar o relógio do computador é o padrão de tempo de hora Universal Coordenada (UTC). UTC é definido por uma amostra multinacional de relógios Ocorreu precisas, que só pode ficar inativo por um segundo nos anos 300. No entanto, ler UTC diretamente requer hardware especializado. Em vez disso, os servidores de tempo são sincronizados com o UTC e são acessados a partir de outros computadores para fornecer escalabilidade e robustez. Cada computador tem de tempo de serviço de sincronização em execução que sabe que servidores de tempo para utilizar e verifica periodicamente se o relógio do computador precisa de ser corrigido e ajusta o tempo se for necessário. 

Anfitriões do Azure são sincronizadas com servidores de tempo de Microsoft internos que usam seu tempo a partir de dispositivos pertencentes à Microsoft o Stratum 1, com antenas GPS. Máquinas virtuais no Azure pode optar por confiar no host para passar o tempo preciso (*alojar tempo*) para a VM ou a VM pode diretamente obter hora de um servidor de tempo, ou uma combinação de ambos. 

Em hardware autónomo, o SO Linux só lê o hardware anfitrião relógio no arranque. Depois disso, o relógio é mantido utilizando o temporizador de interrupção no kernel do Linux. Nesta configuração, o relógio será inconsistências ao longo do tempo. Em mais recentes distribuições do Linux no Azure, as VMs podem utilizar o fornecedor de VMICTimeSync, incluído nos serviços de integração do Linux (LIS), para consultar atualizações do relógio do host com mais frequência.

Interações de máquina virtual com o host também podem afetar o relógio. Durante [memória preservação da manutenção](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), as VMs são colocadas em pausa para até 30 segundos. Por exemplo, antes do início da manutenção o relógio VM mostra: 00 10:00 e tem uma duração de 28 segundos. Depois da VM é retomada, o relógio na VM ainda mostraria: 00 10:00, que seria 28 segundos desativado. A correta para isso, o serviço de VMICTimeSync monitoriza o que está acontecendo no host e pedidos de alterações a ocorrer nas VMs para compensar.

Sem o trabalho de sincronização de hora, o relógio na VM seria acumular-se erros. Quando existe apenas uma VM, o efeito poderá não ser significativo, a menos que a carga de trabalho requer timekeeping altamente preciso. Mas, na maioria dos casos, podemos ter várias, interligados VMs que utilizam o tempo para controlar as transações e as necessidades de tempo para ser consistente em toda a implantação. Quando o tempo entre VMs é diferente, pode ver os seguintes efeitos:

- Autenticação irá falhar. Protocolos de segurança, como Kerberos ou tecnologia de dependente do certificado dependem de tempo a ser consistente em todos os sistemas.
- É muito difícil descobrir o que ocorreram num sistema se registos (ou outros dados) não concordam no tempo. O mesmo evento teria o aspeto, como ocorreu em alturas diferentes, correlação tornando difícil.
- Se o relógio estiver desativada, a faturação foi possível calcular o incorretamente.



## <a name="configuration-options"></a>Opções de configuração

Em geral, existem três formas de configurar a sincronização de hora para as VMs de Linux alojados no Azure:

- A configuração predefinida para imagens do Azure Marketplace utiliza hora NTP e VMICTimeSync-hora do anfitrião. 
- Anfitrião só VMICTimeSync a utilizar.
- Utilize o servidor de tempo de outro, externo com ou sem utilizar VMICTimeSync-hora do anfitrião.


### <a name="use-the-default"></a>Utilize a predefinição

Por predefinição, a maioria das imagens do Azure Marketplace para Linux estão configuradas para sincronização de duas fontes: 

- NTP como servidor primário, que obtém o tempo de um servidor NTP. Por exemplo, o uso de imagens de Ubuntu 16.04 LTS Marketplace **ntp.ubuntu.com**.
- O serviço de VMICTimeSync como secundário, utilizado para comunicar o tempo de anfitrião para as VMs e fazer correções depois da VM está em pausa para manutenção. Anfitriões do Azure utilizem dispositivos de 1 de Stratum pertencentes à Microsoft para manter o tempo preciso.

Distribuições de Linux mais recentes, o serviço de VMICTimeSync utiliza o protocolo de tempo de precisão (PTP), mas podem não suportar PTP distribuições anteriores e serão retorno para NTP para apresentar a hora do anfitrião.

Para confirmar a NTP está a sincronizar corretamente, execute o `ntpq -p` comando.

### <a name="host-only"></a>Só de anfitrião 

Uma vez que os servidores NTP como time.windows.com e ntp.ubuntu.com são públicos, a sincronização de hora com os mesmos requer a enviar o tráfego através da internet. Variados atrasos de pacotes podem afetar negativamente a qualidade da sincronização de hora. Remover NTP alternando para sincronização só de anfitrião, às vezes, pode melhorar o tempo de resultados de sincronização.

Mudar para faz de sincronização de tempo só de anfitrião sentido se tiver a sincronização de hora problemas com a configuração predefinida. Experimente a sincronização só de anfitrião para ver se que aprimorariam a sincronização de hora na sua VM. 

### <a name="external-time-server"></a>Servidor de hora externa

Se tiver requisitos de sincronização de hora específica, há também uma opção de utilizar servidores de hora externa. Servidores de hora externa podem fornecer hora específica, que pode ser útil para cenários de teste, garantir a uniformidade de tempo com máquinas alojadas em datacenters de terceiros ou segundos bissextos de manipulação de forma especial.

Pode combinar um servidor de hora externa com o serviço de VMICTimeSync para fornecer resultados semelhantes à configuração padrão. A combinação de um servidor de hora externa com VMICTimeSync é a melhor opção para lidar com problemas que pode ser causado quando as VMs são colocadas em pausa para manutenção. 

## <a name="tools-and-resources"></a>Ferramentas e recursos

Existem alguns comandos básicos para verificar a configuração de sincronização de hora. Documentação para a distribuição de Linux terão mais detalhes sobre a melhor forma de configurar a sincronização de hora para essa distribuição.

### <a name="integration-services"></a>Serviços de integração

Verifique se o serviço de integração (hv_utils) é carregado.

```bash
lsmod | grep hv_utils
```
Deverá ver algo semelhante a este:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Veja se o daemon de serviços de integração do Hyper-V está em execução.

```bash
ps -ef | grep hv
```

Deverá ver algo semelhante a este:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Procurar PTP

Com as versões mais recentes do Linux, uma origem de relógio de protocolo de tempo de precisão (PTP) está disponível como parte do fornecedor VMICTimeSync. Em versões anteriores do Red Hat Enterprise Linux ou CentOS 7.x a [Linux Integration Services](https://github.com/LIS/lis-next) podem ser transferidos e utilizado para instalar o driver atualizado. Quando utilizar PTP, o dispositivo de Linux irá ser do formulário/desenvolvimento/ptp*x*. 

Ver as origens de relógio PTP estão disponíveis.

```bash
ls /sys/class/ptp
```

Neste exemplo, o valor devolvido é *ptp0*, por isso, o que podemos usar para verificar o nome do relógio. Para verificar se o dispositivo, verifique o nome do relógio.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Isto deverá devolver **Hyper-v**.

### <a name="chrony"></a>chrony

No Red Hat Enterprise Linux e CentOS 7.x, [chrony](https://chrony.tuxfamily.org/) configurado para utilizar um relógio de origem do PTP. O daemon de Network Time Protocol (ntpd) não suporta origens PTP, assim, usando **chronyd** é recomendado. Para ativar PTP, atualize **chrony.conf**.

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Para obter mais informações sobre o Red Hat e NTP, consulte [NTP configurar](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Para obter mais informações sobre chrony, consulte [usando chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Se chrony e TimeSync origens são ativadas ao mesmo tempo, pode marcar um como **prefira** que define a outra origem de cópia de segurança. Uma vez que os serviços NTP não atualizar o relógio para desvios de grandes dimensões, exceto após um longo período, o VMICTimeSync recuperará o relógio de eventos VM em pausa muito mais rapidamente do que com base em NTP sozinhas.


### <a name="systemd"></a>systemd 

No Ubuntu e SUSE tempo de sincronização é configurada usando [systemd](https://www.freedesktop.org/wiki/Software/systemd/). Para obter mais informações sobre o Ubuntu, veja [sincronização de hora](https://help.ubuntu.com/lts/serverguide/NTP.html). Para obter mais informações sobre a SUSE, consulte a seção 4.5.8 [notas SP3 do SUSE Linux Enterprise Server 12](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).



## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte [tempo preciso para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).



---
title: Sincronização de horário para VMs do Linux no Azure
description: Sincronização de horário para máquinas virtuais do Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 1e459e96c128e20f44f1a5adcb18c5b1824c3bf5
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534128"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Sincronização de horário para VMs do Linux no Azure

A sincronização de tempo é importante para a correlação de segurança e evento. Às vezes, ele é usado para a implementação de transações distribuídas. A precisão de tempo entre vários sistemas de computador é obtida por meio da sincronização. A sincronização pode ser afetada por várias coisas, incluindo reinicializações e tráfego de rede entre a fonte de tempo e o computador que busca a hora. 

O Azure é apoiado pela infraestrutura que executa o Windows Server 2016. O Windows Server 2016 tem algoritmos aprimorados usados para corrigir a hora e a condição do relógio local para sincronizar com o UTC.  O recurso de tempo preciso do Windows Server 2016 melhorou muito a forma como o serviço VMICTimeSync que governa as VMs com o host para o tempo preciso. Os aprimoramentos incluem um tempo inicial mais preciso na inicialização da VM ou na recuperação da VM e na correção da latência de interrupção. 

>[!NOTE]
>Para obter uma visão geral rápida do serviço de tempo do Windows, veja este [vídeo de visão geral de alto nível](https://aka.ms/WS2016TimeVideo).
>
> Para obter mais informações, consulte [tempo preciso para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Visão geral

A precisão de um relógio de computador é avaliada em quão próximo o relógio do computador é o padrão de tempo UTC (tempo Universal Coordenado). O UTC é definido por um exemplo multinacional de relógios de Atomic precisos que só podem ser desativados por um segundo em 300 anos. Mas, ler o UTC diretamente requer hardware especializado. Em vez disso, os servidores de horário são sincronizados com o UTC e são acessados de outros computadores para fornecer escalabilidade e robustez. Cada computador tem o serviço de sincronização de tempo em execução que sabe quais servidores de tempo usar e verifica periodicamente se o relógio do computador precisa ser corrigido e ajusta o tempo se necessário. 

Os hosts do Azure são sincronizados com os servidores de tempo internos da Microsoft que levam seu tempo em dispositivos de camada 1 de propriedade da Microsoft, com antenas de GPS. As máquinas virtuais no Azure podem depender de seu host para passar o tempo preciso (*tempo de host*) para a VM ou a VM pode obter tempo diretamente de um servidor de horário ou uma combinação de ambos. 

No hardware autônomo, o sistema operacional Linux lê apenas o relógio de hardware do host na inicialização. Depois disso, o relógio é mantido usando o temporizador de interrupção no kernel do Linux. Nessa configuração, o relógio se desatualizará ao longo do tempo. Em distribuições mais recentes do Linux no Azure, as VMs podem usar o provedor VMICTimeSync, incluído no LIS (Linux Integration Services), para consultar atualizações de relógio do host com mais frequência.

As interações de máquina virtual com o host também podem afetar o relógio. Durante a [manutenção da preservação da memória](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), as VMs são pausadas por até 30 segundos. Por exemplo, antes de a manutenção começar, o relógio da VM mostra 10:00:00 AM e dura 28 segundos. Depois que a VM for retomada, o relógio na VM ainda mostrará 10:00:00 AM, que teria 28 segundos de desconto. Para corrigir isso, o serviço VMICTimeSync monitora o que está acontecendo no host e solicita que as alterações ocorram nas VMs para compensar.

Sem que a sincronização de tempo funcione, o relógio na VM acumularia erros. Quando há apenas uma VM, o efeito pode não ser significativo, a menos que a carga de trabalho exija uma redução do dia altamente precisa. Mas, na maioria dos casos, temos várias VMs interconectadas que usam tempo para controlar transações e o tempo precisa ser consistente em toda a implantação. Quando o tempo entre as VMs for diferente, você poderá ver os seguintes efeitos:

- A autenticação falhará. Protocolos de segurança como a tecnologia dependente de certificado ou Kerberos dependem do tempo de consistência entre os sistemas.
- É muito difícil descobrir o que aconteceu em um sistema se os logs (ou outros dados) não concordarem no tempo. O mesmo evento pareceria que ocorreu em momentos diferentes, dificultando a correlação.
- Se o relógio estiver desativado, a cobrança poderá ser calculada incorretamente.



## <a name="configuration-options"></a>Opções de configuração

Em geral, há três maneiras de configurar a sincronização de horário para suas VMs Linux hospedadas no Azure:

- A configuração padrão das imagens do Azure Marketplace usa tanto o tempo de NTP quanto o tempo de host VMICTimeSync. 
- Somente host usando VMICTimeSync.
- Use outro servidor de horário externo com ou sem usar o tempo de host VMICTimeSync.


### <a name="use-the-default"></a>Usar o padrão

Por padrão, a maioria das imagens do Azure Marketplace para Linux é configurada para sincronização de duas fontes: 

- NTP como primário, que obtém o tempo de um servidor NTP. Por exemplo, imagens do Ubuntu 16, 4 LTS Marketplace usam o **NTP.Ubuntu.com**.
- O serviço VMICTimeSync como secundário, usado para comunicar o tempo de host para as VMs e fazer correções depois que a VM é pausada para manutenção. Os hosts do Azure usam dispositivos de camada 1 de propriedade da Microsoft para manter o tempo preciso.

Em distribuições mais recentes do Linux, o serviço VMICTimeSync usa o protocolo PTP, mas as distribuições anteriores podem não dar suporte ao PTP e voltarão ao NTP para obter tempo do host.

Para confirmar que o NTP está sincronizando corretamente, execute o comando `ntpq -p`.

### <a name="host-only"></a>Somente host 

Como os servidores NTP, como time.windows.com e ntp.ubuntu.com, são públicos, o tempo de sincronização com eles requer o envio de tráfego pela Internet. Os atrasos de pacotes variados podem afetar negativamente a qualidade da sincronização de tempo. A remoção do NTP alternando para a sincronização somente de host pode às vezes melhorar o tempo de sincronização dos resultados.

Mudar para a sincronização de tempo somente de host faz sentido se você tiver problemas de sincronização de tempo usando a configuração padrão. Experimente a sincronização somente de host para ver se isso melhora a sincronização de horário em sua VM. 

### <a name="external-time-server"></a>Servidor de horário externo

Se você tiver requisitos de sincronização de tempo específicos, também há uma opção de usar servidores de horário externos. Os servidores de horário externos podem fornecer um tempo específico, o que pode ser útil para cenários de teste, garantindo a uniformidade do tempo com computadores hospedados em datacenters não Microsoft ou lidando com segundos bissextos de forma especial.

Você pode combinar um servidor de horário externo com o serviço VMICTimeSync para fornecer resultados semelhantes à configuração padrão. A combinação de um servidor de horário externo com o VMICTimeSync é a melhor opção para lidar com problemas que podem ocorrer quando as VMs são pausadas para manutenção. 

## <a name="tools-and-resources"></a>Ferramentas e recursos

Há alguns comandos básicos para verificar sua configuração de sincronização de horário. A documentação para distribuição do Linux terá mais detalhes sobre a melhor maneira de configurar a sincronização de horário para essa distribuição.

### <a name="integration-services"></a>Serviços de integração

Verifique se o serviço de integração (hv_utils) está carregado.

```bash
lsmod | grep hv_utils
```
Você verá algo semelhante a este:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Veja se o daemon do Integration Services do Hyper-V está em execução.

```bash
ps -ef | grep hv
```

Você verá algo semelhante a este:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Verificar o PTP

Com as versões mais recentes do Linux, uma fonte de tempo do PTP (protocolo de precisão temporal) está disponível como parte do provedor VMICTimeSync. Em versões mais antigas do Red Hat Enterprise Linux ou do CentOS 7. x, o [Integration Services do Linux](https://github.com/LIS/lis-next) pode ser baixado e usado para instalar o driver atualizado. Ao usar o PTP, o dispositivo Linux terá o formato/dev/PTP*x*. 

Veja quais fontes de relógio do PTP estão disponíveis.

```bash
ls /sys/class/ptp
```

Neste exemplo, o valor retornado é *ptp0*, portanto, usamos isso para verificar o nome do relógio. Para verificar o dispositivo, verifique o nome do relógio.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Isso deve retornar o **HyperV**.

### <a name="chrony"></a>chrony

Em Red Hat Enterprise Linux e CentOS 7. x, [chrony](https://chrony.tuxfamily.org/) configurado para usar um relógio de origem do PTP. O daemon de protocolo NTP (ntpd) não dá suporte a fontes PTP, portanto, usar **chronyd** é recomendado. Para habilitar o PTP, atualize **chrony. conf**.

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Para obter mais informações sobre o Red Hat e o NTP, consulte [Configurar o NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Para obter mais informações sobre o chrony, consulte [usando o chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Se as fontes chrony e TimeSync estiverem habilitadas simultaneamente, você poderá marcar uma como **preferir** , que define a outra fonte como um backup. Como os serviços NTP não atualizam o relógio para grandes distorções, exceto após um longo período, o VMICTimeSync recuperará o relógio de eventos de VM pausados muito mais rapidamente do que as ferramentas baseadas em NTP.

Por padrão, o chronyd acelera ou reduz o relógio do sistema para corrigir qualquer descompasso de tempo. Se a descompasso se tornar muito grande, o chrony falhará ao corrigir a descompasso. Para superar isso, o parâmetro `makestep` em **/etc/chrony.conf** pode ser alterado para forçar uma sincronização de TimeSync se a descompasso exceder o limite especificado.
 ```bash
makestep 1.0 -1
```
Aqui, o chrony forçará uma atualização de tempo se a descompasso for maior que 1 segundo. Para aplicar as alterações, reinicie o serviço chronyd.

```bash
systemctl restart chronyd
```


### <a name="systemd"></a>systemd 

No Ubuntu e no SUSE, a sincronização de horário é configurada usando o [sistema](https://www.freedesktop.org/wiki/Software/systemd/). Para obter mais informações sobre o Ubuntu, consulte [sincronização de horário](https://help.ubuntu.com/lts/serverguide/NTP.html). Para obter mais informações sobre o SUSE, consulte a seção 4.5.8 em [notas de versão do SUSE Linux Enterprise Server 12 SP3](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [tempo preciso para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).



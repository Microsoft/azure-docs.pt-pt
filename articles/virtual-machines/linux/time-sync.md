---
title: Sincronização de tempo para Os VMs Linux em Azure
description: Sincronização temporal para máquinas virtuais Linux.
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 18c8570a8066985cab5263c4779787062dc32d75
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552648"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Sincronização de tempo para Os VMs Linux em Azure

A sincronização do tempo é importante para a segurança e a correlação de eventos. Por vezes é usado para a implementação de transações distribuídas. A precisão do tempo entre vários sistemas informáticos é conseguida através da sincronização. A sincronização pode ser afetada por várias coisas, incluindo reinicializações e tráfego de rede entre a fonte de tempo e o computador a recolher o tempo. 

O Azure é apoiado por infraestruturas que executam o Windows Server 2016. O Windows Server 2016 melhorou os algoritmos utilizados para corrigir o tempo e condicionar o relógio local a sincronizar com o UTC.  A funcionalidade tempo exato do Windows Server 2016 melhorou consideravelmente a forma como o serviço VMICTimeSync que rege os VMs com o hospedeiro durante um período preciso. As melhorias incluem um tempo inicial mais preciso no início do VM ou na restauração de VM e interrupção da correção da latência. 

> [!NOTE]
> Para uma visão geral rápida do serviço Windows Time, veja este [vídeo de visão geral de alto nível](https://aka.ms/WS2016TimeVideo).
>
> Para obter mais informações, consulte [a hora exata para o Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Descrição Geral

A precisão para um relógio de computador é avaliada sobre a proximidade do relógio do computador ao padrão de tempo universal coordenado (UTC). A UTC é definida por uma amostra multinacional de relógios atómicos precisos que só podem ser desligados por um segundo em 300 anos. Mas, ler a UTC requer hardware especializado. Em vez disso, os servidores de tempo são sincronizados com utc e são acedidos a partir de outros computadores para fornecer escalabilidade e robustez. Todos os computadores têm o serviço de sincronização de tempo em execução que sabe a que horas os servidores devem utilizar e verifica periodicamente se o relógio do computador precisa de ser corrigido e ajusta o tempo se necessário. 

Os anfitriões do Azure são sincronizados com servidores internos do tempo da Microsoft que levam o seu tempo a partir de dispositivos Stratum 1 da Microsoft, com antenas GPS. As máquinas virtuais em Azure podem depender do seu anfitrião para passar o tempo exato (hora do *anfitrião)* para o VM ou o VM pode obter tempo diretamente a partir de um servidor de tempo, ou uma combinação de ambos. 

No hardware autónomo, o Linux OS apenas lê o relógio de hardware do anfitrião no arranque. Depois disso, o relógio é mantido utilizando o temporizador de interrupção no núcleo Linux. Nesta configuração, o relógio irá flutuar ao longo do tempo. Nas mais recentes distribuições do Linux no Azure, os VMs podem utilizar o fornecedor VMICTimeSync, incluído nos serviços de integração Linux (LIS), para consultar mais frequentemente as atualizações do relógio do anfitrião.

As interações virtuais da máquina com o hospedeiro também podem afetar o relógio. Durante [a manutenção da conservação da memória,](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)os VM são pausados até 30 segundos. Por exemplo, antes de a manutenção começar, o relógio VM mostra 10:00:00 am e dura 28 segundos. Após o recomeço do VM, o relógio do VM ainda mostraria 10:00:00 am, o que seria 28 segundos de desconto. Para corrigir isto, o serviço VMICTimeSync monitoriza o que está a acontecer no anfitrião e pede que as alterações ocorram nos VMs para compensar.

Sem sincronização de tempo, o relógio do VM acumularia erros. Quando há apenas um VM, o efeito pode não ser significativo a menos que a carga de trabalho exija uma cronometragem altamente precisa. Mas na maioria dos casos, temos múltiplos VMs interligados que usam o tempo para rastrear transações e o tempo precisa ser consistente em toda a implementação. Quando o tempo entre VMs é diferente, pode-se ver os seguintes efeitos:

- A autenticação falhará. Protocolos de segurança como Kerberos ou tecnologia dependente de certificados dependem do tempo ser consistente em todos os sistemas.
- É muito difícil descobrir o que aconteceu num sistema se os registos (ou outros dados) não chegarem a tempo. O mesmo evento pareceria ter ocorrido em momentos diferentes, dificultando a correlação.
- Se o relógio estiver desligado, a faturação pode ser calculada incorretamente.



## <a name="configuration-options"></a>Opções de configuração

Existem geralmente três formas de configurar a sincronização temporal para os seus VMs Linux hospedados em Azure:

- A configuração padrão para imagens Azure Marketplace utiliza tanto o tempo NTP como o tempo de anfitrião VMICTimeSync. 
- Só com o anfitrião com vmictimeSync.
- Utilize outro servidor de tempo externo com ou sem utilização da hora de anfitrião VMICTimeSync.


### <a name="use-the-default"></a>Use o padrão

Por padrão, a maioria das imagens do Azure Marketplace para Linux são configuradas para sincronizar a partir de duas fontes: 

- NTP como primária, que obtém tempo de um servidor NTP. Por exemplo, as imagens Ubuntu 16.04 LTS Marketplace utilizam **ntp.ubuntu.com**.
- O serviço VMICTimeSync como secundário, utilizado para comunicar o tempo de anfitrião aos VMs e fazer correções após a pausa do VM para manutenção. Os anfitriões do Azure usam dispositivos Stratum 1 da Microsoft para manter o tempo preciso.

Nas mais recentes distribuições do Linux, o serviço VMICTimeSync fornece uma fonte de relógio de hardware do Precision Time Protocol (PTP), mas as distribuições anteriores podem não fornecer esta fonte de relógio e irão recuar para NTP para obter tempo do anfitrião.

Para confirmar que o NTP está a sincronizar corretamente, executar o `ntpq -p` comando.

### <a name="host-only"></a>Só hospedeiro 

Como os servidores NTP como time.windows.com e ntp.ubuntu.com são públicos, sincronizar o tempo com eles requer o envio de tráfego pela internet. Os atrasos variados dos pacotes podem afetar negativamente a qualidade da sincronização do tempo. Remover o NTP ao mudar para sincronização apenas de hospedeiro pode, por vezes, melhorar os resultados da sincronização do seu tempo.

Mudar para sincronização de tempo apenas para anfitrião faz sentido se experimentar problemas de sincronização de tempo usando a configuração padrão. Experimente a sincronização apenas do anfitrião para ver se isso melhoraria a sincronização de tempo no seu VM. 

### <a name="external-time-server"></a>Servidor de tempo externo

Se tiver requisitos específicos de sincronização de tempo, existe também a opção de utilizar servidores de tempo externos. Os servidores de tempo externos podem fornecer tempo específico, o que pode ser útil para cenários de teste, garantindo a uniformidade do tempo com máquinas hospedadas em datacenters não Microsoft, ou manuseando segundos de salto de forma especial.

Pode combinar um servidor de tempo externo com o serviço VMICTimeSync para fornecer resultados semelhantes à configuração padrão. Combinar um servidor de tempo externo com o VMICTimeSync é a melhor opção para lidar com problemas que podem ser causados quando os VMs são pausados para manutenção. 

## <a name="tools-and-resources"></a>Ferramentas e recursos

Existem alguns comandos básicos para verificar a configuração da sincronização do tempo. A documentação para a distribuição do Linux terá mais detalhes sobre a melhor forma de configurar a sincronização do tempo para essa distribuição.

### <a name="integration-services"></a>Serviços de integração

Verifique se o serviço de integração (hv_utils) está carregado.

```bash
lsmod | grep hv_utils
```
Devia ver algo semelhante a isto:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Vê se os serviços de integração hyper-V estão a funcionar.

```bash
ps -ef | grep hv
```

Devia ver algo semelhante a isto:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>Verifique se a Fonte do Relógio PTP

Com versões mais recentes do Linux, está disponível uma fonte de relógio do Protocolo de Tempo de Precisão (PTP) como parte do fornecedor VMICTimeSync. Nas versões mais antigas da Red Hat Enterprise Linux ou CentOS 7.x os Serviços de [Integração Linux](https://github.com/LIS/lis-next) podem ser descarregados e utilizados para instalar o controlador atualizado. Quando a fonte do relógio PTP estiver disponível, o dispositivo Linux será do formulário /dev/ptp *x*. 

Veja quais as fontes de relógio PTP disponíveis.

```bash
ls /sys/class/ptp
```

Neste exemplo, o valor devolvido é *ptp0,* por isso usamos isso para verificar o nome do relógio. Para verificar o dispositivo, verifique o nome do relógio.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Isto deve `hyperv` voltar.

### <a name="chrony"></a>cronrony

Nas versões Ubuntu 19.10 e posterior, Red Hat Enterprise Linux e CentOS 8.x, [o cronografo](https://chrony.tuxfamily.org/) está configurado para usar um relógio de origem PTP. Em vez de chrony, as versões linux mais antigas usam o daemon do Protocolo de Tempo de Rede (ntpd), que não suporta fontes de PTP. Para permitir o PTP nessas versões, o cronoy deve ser instalado manualmente e configurado (em chrony.conf) utilizando o seguinte código:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Para obter mais informações sobre Ubuntu e NTP, consulte [a Sincronização temporal.](https://ubuntu.com/server/docs/network-ntp)

Para obter mais informações sobre o Red Hat e o NTP, consulte [Configure NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP). 

Para obter mais informações sobre o cronoy, consulte [Usando o crono.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony)

Se as fontes chrony e VMICTimeSync estiverem ativadas simultaneamente, pode marcar uma como **preferir**, o que define a outra fonte como uma cópia de segurança. Como os serviços NTP não atualizam o relógio para grandes distorções, exceto após um longo período, o VMICTimeSync recuperará o relógio de eventos VM pausados muito mais rapidamente do que apenas ferramentas baseadas em NTP.

Por predefinição, o cronondo acelera ou abranda o relógio do sistema para corrigir qualquer deriva temporal. Se a deriva se tornar muito grande, o cronoy não consegue consertar a deriva. Para ultrapassar isto, o `makestep` parâmetro em **/etc/chrony.conf** pode ser alterado para forçar uma sincronização temporal se a deriva exceder o limiar especificado.

 ```bash
makestep 1.0 -1
```

Aqui, o crono vai forçar uma atualização temporal se a deriva for superior a 1 segundo. Para aplicar as alterações reinicie o serviço cronyd:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>sistemas 

Nos lançamentos SUSE e Ubuntu antes das 19.10, a sincronização do tempo é configurada utilizando [sistemas](https://www.freedesktop.org/wiki/Software/systemd/). Para obter mais informações sobre ubuntu, consulte [a Sincronização temporal.](https://help.ubuntu.com/lts/serverguide/NTP.html) Para obter mais informações sobre sUSE, consulte a Secção 4.5.8 no [SUSE Linux Enterprise Server 12 SP3 Notas de lançamento](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [a hora exata para o Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time).



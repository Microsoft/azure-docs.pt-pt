---
title: Sincronização de tempo para VMs Linux em Azure
description: Sincronização de tempo para máquinas virtuais Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 7c93c1f525713a90abd71c30a21401b9d1cfcb9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81460907"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Sincronização de tempo para VMs Linux em Azure

A sincronização temporal é importante para a segurança e correlação de eventos. Por vezes, é utilizado para a implementação de transações distribuídas. A precisão do tempo entre vários sistemas informáticos é conseguida através da sincronização. A sincronização pode ser afetada por várias coisas, incluindo reboots e tráfego de rede entre a fonte de tempo e o computador que vai buscar o tempo. 

O Azure é apoiado pela infraestrutura que executa o Windows Server 2016. O Windows Server 2016 melhorou os algoritmos utilizados para corrigir o tempo e condicionar o relógio local para sincronizar com a UTC.  O windows Server 2016 Accurate Time melhorou muito a forma como o serviço VMICTimeSync que rege os VMs com o anfitrião por um tempo preciso. As melhorias incluem um tempo inicial mais preciso no arranque do VM ou na restauração vm e interromper a correção da latência. 

>[!NOTE]
>Para uma visão geral rápida do serviço Windows Time, veja este [vídeo de visão geral de alto nível](https://aka.ms/WS2016TimeVideo).
>
> Para mais informações, consulte [o tempo exato para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Descrição geral

A precisão de um relógio de computador é avaliada na proximidade do relógio do computador à norma de tempo coordenada do tempo universal (UTC). A UTC é definida por uma amostra multinacional de relógios atómicos precisos que só pode ser desligado por um segundo em 300 anos. Mas, ler UTC requer hardware especializado. Em vez disso, os servidores de tempo são sincronizados com utc e são acedidos a partir de outros computadores para fornecer escalabilidade e robustez. Todos os computadores têm um serviço de sincronização temporal em execução que sabe a que horas os servidores devem usar e verifica periodicamente se o relógio do computador precisa de ser corrigido e ajusta o tempo se necessário. 

Os anfitriões do Azure são sincronizados com servidores de tempo internos da Microsoft que levam o seu tempo a partir de dispositivos Stratum 1, propriedade da Microsoft, com antenas GPS. As máquinas virtuais em Azure podem depender do seu hospedeiro para passar o tempo exato (tempo de*anfitrião)* para o VM ou o VM pode obter diretamente tempo a partir de um servidor de tempo, ou uma combinação de ambos. 

No hardware autónomo, o Sistema Operativo Linux lê apenas o relógio de hardware do anfitrião no arranque. Depois disso, o relógio é mantido utilizando o temporizador de interrupção no kernel Linux. Nesta configuração, o relógio irá derivar ao longo do tempo. Nas mais recentes distribuições do Linux no Azure, os VMs podem utilizar o fornecedor VMICTimeSync, incluído nos serviços de integração do Linux (LIS), para consultar as atualizações do relógio do anfitrião com mais frequência.

As interações virtuais com o hospedeiro também podem afetar o relógio. Durante [a manutenção da conservação](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)da memória, os VMs são interrompidos por até 30 segundos. Por exemplo, antes de a manutenção começar, o relógio VM mostra 10:00:00 AM e dura 28 segundos. Após o reinício do VM, o relógio do VM ainda mostraria 10:00:00 am, o que seria 28 segundos de desconto. Para corrigir isto, o serviço VMICTimeSync monitoriza o que está a acontecer no hospedeiro e solicita que as alterações ocorram nos VMs para compensar.

Sem que a sincronização do tempo funcione, o relógio do VM acumularia erros. Quando há apenas um VM, o efeito pode não ser significativo a menos que a carga de trabalho exija uma cronometragem muito precisa. Mas na maioria dos casos, temos múltiplos VMs interligados que usam o tempo para rastrear transações e o tempo precisa ser consistente em toda a implementação. Quando o tempo entre VMs é diferente, pode ver os seguintes efeitos:

- A autenticação falhará. Protocolos de segurança como Kerberos ou tecnologia dependente de certificados dependem do tempo ser consistente em todos os sistemas.
- É muito difícil descobrir o que aconteceu num sistema se os registos (ou outros dados) não concordam com o tempo. O mesmo evento pareceria que ocorreu em momentos diferentes, dificultando a correlação.
- Se o relógio estiver desligado, a faturação pode ser calculada incorretamente.



## <a name="configuration-options"></a>Opções de configuração

Existem geralmente três formas de configurar a sincronização de tempo para os seus VMs Linux hospedados em Azure:

- A configuração padrão para imagens do Azure Marketplace utiliza tanto o tempo NTP como o tempo de anfitrião VMICTimeSync. 
- Apenas o hospedeiro utiliza o VMICTimeSync.
- Utilize outro servidor de tempo externo com ou sem utilizar o tempo de anfitrião Do VMICTimeSync.


### <a name="use-the-default"></a>Use o padrão

Por padrão, a maioria das imagens do Azure Marketplace para o Linux estão configuradas para sincronizar a partir de duas fontes: 

- NTP como primário, que obtém tempo de um servidor NTP. Por exemplo, as imagens do Ubuntu 16.04 LTS Marketplace usam **ntp.ubuntu.com**.
- O serviço VMICTimeSync como secundário, utilizado para comunicar o tempo de acolhimento aos VMs e fazer correções após a pausa do VM para manutenção. Os anfitriões do Azure utilizam dispositivos Stratum 1 da Microsoft para manter o tempo preciso.

Nas distribuições mais recentes do Linux, o serviço VMICTimeSync utiliza o protocolo de tempo de precisão (PTP), mas as distribuições anteriores podem não suportar o PTP e voltarão para a NTP para obter tempo do anfitrião.

Para confirmar que a NTP está a `ntpq -p` sincronizar corretamente, executa o comando.

### <a name="host-only"></a>Só para hospedeiros 

Como servidores NTP como time.windows.com e ntp.ubuntu.com são públicos, sincronizar o tempo com eles requer o envio de tráfego através da internet. Vários atrasos no pacote podem afetar negativamente a qualidade da sincronização do tempo. Remover o NTP mudando para sincronização apenas para o hospedeiro pode, por vezes, melhorar os resultados da sincronização do tempo.

Mudar para sincronização de tempo apenas para o anfitrião faz sentido se experimentar problemas de sincronização de tempo usando a configuração predefinida. Experimente a sincronização apenas do hospedeiro para ver se isso melhoraria a sincronização de tempo no seu VM. 

### <a name="external-time-server"></a>Servidor de tempo externo

Se tiver requisitos específicos de sincronização de tempo, existe também a opção de utilizar servidores de tempo externos. Os servidores de tempo externos podem fornecer um tempo específico, o que pode ser útil para cenários de teste, garantindo a uniformidade do tempo com máquinas alojadas em datacenters não Microsoft, ou manuseando segundos de salto de forma especial.

Pode combinar um servidor de tempo externo com o serviço VMICTimeSync para fornecer resultados semelhantes à configuração predefinida. Combinar um servidor de tempo externo com o VMICTimeSync é a melhor opção para lidar com problemas que podem ser causados quando os VMs são interrompidos para manutenção. 

## <a name="tools-and-resources"></a>Ferramentas e recursos

Existem alguns comandos básicos para verificar a configuração de sincronização do seu tempo. A documentação para a distribuição do Linux terá mais detalhes sobre a melhor forma de configurar a sincronização do tempo para essa distribuição.

### <a name="integration-services"></a>Serviços de integração

Verifique se o serviço de integração (hv_utils) está carregado.

```bash
lsmod | grep hv_utils
```
Devia ver algo parecido com isto:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Veja se o daemon dos serviços de integração Hyper-V está a funcionar.

```bash
ps -ef | grep hv
```

Devia ver algo parecido com isto:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Verifique se ptP

Com versões mais recentes do Linux, está disponível uma fonte de relógio do Protocolo de Tempo de Precisão (PTP) como parte do fornecedor VMICTimeSync. Em versões mais antigas da Red Hat Enterprise Linux ou CentOS 7.x, os Serviços de [Integração Linux](https://github.com/LIS/lis-next) podem ser descarregados e utilizados para instalar o controlador atualizado. Ao utilizar ptP, o dispositivo Linux será do formulário /dev/ptp*x*. 

Veja quais as fontes do relógio PTP disponíveis.

```bash
ls /sys/class/ptp
```

Neste exemplo, o valor devolvido é *ptp0*, por isso usamos isso para verificar o nome do relógio. Para verificar o dispositivo, verifique o nome do relógio.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Isto deve devolver **o hiperv.**

### <a name="chrony"></a>chrony

Em Ubuntu 19.10 e versões posteriores, Red Hat Enterprise Linux, e CentOS 7.x, a [chrony](https://chrony.tuxfamily.org/) é configurada para usar um relógio de origem PTP. Em vez de chrony, os lançamentos linux mais antigos usam o daemon do Protocolo de Tempo de Rede (ntpd), que não suporta fontes ptp. Para ativar o PTP nessas versões, a crina deve ser instalada manualmente e configurada (em chrony.conf) utilizando o seguinte código:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Para mais informações sobre Ubuntu e NTP, consulte A [Sincronização do Tempo](https://help.ubuntu.com/lts/serverguide/NTP.html).

Para mais informações sobre o Chapéu Vermelho e a NTP, consulte [Configure NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Para mais informações sobre a crina, consulte [A utilização da crina.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony)

Se as fontes chrony e TimeSync estiverem ativadas simultaneamente, pode marcar uma como **preferir**, o que define a outra fonte como uma cópia de segurança. Uma vez que os serviços NTP não atualizam o relógio para grandes distorções, exceto após um longo período, o VMICTimeSync recuperará o relógio de eventos VM pausados muito mais rapidamente do que apenas as ferramentas baseadas em NTP.

Por defeito, a cronônica acelera ou abranda o relógio do sistema para corrigir qualquer deriva de tempo. Se a deriva se tornar demasiado grande, a crina não consegue consertar a deriva. Para ultrapassar isto, o `makestep` parâmetro em **/etc/chrony.conf** pode ser alterado para forçar uma sincronização temporal se a deriva exceder o limiar especificado.

 ```bash
makestep 1.0 -1
```

Aqui, a chrony forçará uma atualização do tempo se a deriva for superior a 1 segundo. Para aplicar as alterações reiniciar o serviço de cronod:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>sistemad 

Nos lançamentos de SUSE e Ubuntu antes das 19.10, a sincronização de tempo é configurada utilizando [sistemad](https://www.freedesktop.org/wiki/Software/systemd/). Para mais informações sobre Ubuntu, consulte A [Sincronização do Tempo](https://help.ubuntu.com/lts/serverguide/NTP.html). Para mais informações sobre o SUSE, consulte a Secção 4.5.8 no [SUSE Linux Enterprise Server 12 SP3 Release Notes](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [o tempo exato para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).



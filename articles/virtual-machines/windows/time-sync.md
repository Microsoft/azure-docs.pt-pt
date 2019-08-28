---
title: Sincronização de horário para VMs do Windows no Azure | Microsoft Docs
description: Sincronização de horário para máquinas virtuais do Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 04b2eb70a9e304fb50f4f6cb94daf0a0dda86d63
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100263"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Sincronização de horário para VMs do Windows no Azure

A sincronização de tempo é importante para a correlação de segurança e evento. Às vezes, ele é usado para a implementação de transações distribuídas. A precisão de tempo entre vários sistemas de computador é obtida por meio da sincronização. A sincronização pode ser afetada por várias coisas, incluindo reinicializações e tráfego de rede entre a fonte de tempo e o computador que busca a hora. 

O Azure agora tem o suporte da infraestrutura que executa o Windows Server 2016. O Windows Server 2016 tem algoritmos aprimorados usados para corrigir a hora e a condição do relógio local para sincronizar com o UTC.  O Windows Server 2016 também melhorou o serviço VMICTimeSync que controla como as VMs são sincronizadas com o host para o tempo preciso. Os aprimoramentos incluem um tempo inicial mais preciso na inicialização da VM ou na recuperação da VM e na correção da latência de interrupção para exemplos fornecidos para o tempo do Windows (W32time). 


>[!NOTE]
>Para obter uma visão geral rápida do serviço de tempo do Windows, veja este [vídeo de visão geral de alto nível](https://aka.ms/WS2016TimeVideo).
>
> Para obter mais informações, consulte [tempo preciso para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Descrição geral

A precisão de um relógio de computador é avaliada em quão próximo o relógio do computador é o padrão de tempo UTC (tempo Universal Coordenado). O UTC é definido por um exemplo multinacional de relógios de Atomic precisos que só podem ser desativados por um segundo em 300 anos. Mas, ler o UTC diretamente requer hardware especializado. Em vez disso, os servidores de horário são sincronizados com o UTC e são acessados de outros computadores para fornecer escalabilidade e robustez. Cada computador tem o serviço de sincronização de tempo em execução que sabe quais servidores de tempo usar e verifica periodicamente se o relógio do computador precisa ser corrigido e ajusta o tempo se necessário. 

Os hosts do Azure são sincronizados com os servidores de tempo internos da Microsoft que levam seu tempo em dispositivos de camada 1 de propriedade da Microsoft, com antenas de GPS. As máquinas virtuais no Azure podem depender de seu host para passar o tempo preciso (*tempo de host*) para a VM ou a VM pode obter tempo diretamente de um servidor de horário ou uma combinação de ambos. 

As interações de máquina virtual com o host também podem afetar o relógio. Durante a [manutenção da preservação da memória](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), as VMs são pausadas por até 30 segundos. Por exemplo, antes de a manutenção começar, o relógio da VM mostra 10:00:00 AM e dura 28 segundos. Depois que a VM for retomada, o relógio na VM ainda mostrará 10:00:00 AM, que teria 28 segundos de desconto. Para corrigir isso, o serviço VMICTimeSync monitora o que está acontecendo no host e solicita que as alterações ocorram nas VMs para compensar.

O serviço VMICTimeSync opera no modo de exemplo ou de sincronização e influenciará apenas o relógio. No modo de exemplo, que exige que o W32time esteja em execução, o serviço VMICTimeSync sonda o host a cada 5 segundos e fornece amostras de tempo para o W32time. Aproximadamente a cada 30 segundos, o serviço W32time usa o último exemplo de tempo e o usa para influenciar o relógio do convidado. O modo de sincronização será ativado se um convidado tiver sido retomado ou se o relógio de um convidado ficar mais de 5 segundos atrás do relógio do host. Nos casos em que o serviço W32time está em execução corretamente, o último caso nunca deve acontecer.

Sem que a sincronização de tempo funcione, o relógio na VM acumularia erros. Quando há apenas uma VM, o efeito pode não ser significativo, a menos que a carga de trabalho exija uma redução do dia altamente precisa. Mas, na maioria dos casos, temos várias VMs interconectadas que usam tempo para controlar transações e o tempo precisa ser consistente em toda a implantação. Quando o tempo entre as VMs for diferente, você poderá ver os seguintes efeitos:

- A autenticação falhará. Protocolos de segurança como a tecnologia dependente de certificado ou Kerberos dependem do tempo de consistência entre os sistemas. 
- É muito difícil descobrir o que aconteceu em um sistema se os logs (ou outros dados) não concordarem no tempo. O mesmo evento pareceria que ocorreu em momentos diferentes, dificultando a correlação.
- Se o relógio estiver desativado, a cobrança poderá ser calculada incorretamente.

Os melhores resultados para implantações do Windows são obtidos usando o Windows Server 2016 como o sistema operacional convidado, o que garante que você possa usar as melhorias mais recentes na sincronização de tempo.

## <a name="configuration-options"></a>Opções de configuração

Há três opções para configurar a sincronização de horário para suas VMs do Windows hospedadas no Azure:

- Tempo de host e time.windows.com. Essa é a configuração padrão usada nas imagens do Azure Marketplace.
- Somente host.
- Use outro servidor de horário externo com ou sem usar a hora do host.


### <a name="use-the-default"></a>Usar o padrão

Por padrão, as imagens de VM do sistema operacional Windows são configuradas para W32Time para sincronização de duas fontes: 

- O provedor do NtpClient, que obtém informações de time.windows.com.
- O serviço VMICTimeSync, usado para comunicar o tempo de host para as VMs e fazer correções depois que a VM é pausada para manutenção. Os hosts do Azure usam dispositivos de camada 1 de propriedade da Microsoft para manter o tempo preciso.

o W32Time prefere o provedor de tempo na seguinte ordem de prioridade: nível de estrato, atraso raiz, dispersão de raiz, deslocamento de tempo. Na maioria dos casos, o W32Time prefere o time.windows.com ao host porque o time.windows.com relata a parte inferior do estrato. 

Para computadores ingressados no domínio, o próprio domínio estabelece a hierarquia de sincronização de tempo, mas a raiz da floresta ainda precisa levar tempo de algum lugar e as considerações a seguir ainda seriam verdadeiras.


### <a name="host-only"></a>Somente host 

Como o time.windows.com é um servidor NTP público, o tempo de sincronização com ele requer o envio de tráfego pela Internet, os atrasos de pacotes variados podem afetar negativamente a qualidade da sincronização de tempo. A remoção de time.windows.com alternando para sincronização somente de host pode às vezes melhorar o tempo de sincronização dos resultados.

Mudar para a sincronização de tempo somente de host faz sentido se você tiver problemas de sincronização de tempo usando a configuração padrão. Experimente a sincronização somente de host para ver se isso melhoraria a sincronização de horário na VM. 

### <a name="external-time-server"></a>Servidor de horário externo

Se você tiver requisitos de sincronização de tempo específicos, também há uma opção de usar servidores de horário externos. Os servidores de horário externos podem fornecer um tempo específico, o que pode ser útil para cenários de teste, garantindo a uniformidade do tempo com computadores hospedados em datacenters não Microsoft ou lidando com segundos bissextos de forma especial.

Você pode combinar servidores externos com o serviço VMICTimeSync e VMICTimeProvider para fornecer resultados semelhantes à configuração padrão. 

## <a name="check-your-configuration"></a>Verifique sua configuração


Verifique se o provedor de tempo do NtpClient está configurado para usar servidores NTP explícitos (NTP) ou sincronização de horário do domínio (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Se a VM estiver usando o NTP, você verá a seguinte saída:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Para ver qual servidor de horário o provedor de tempo do NtpClient está usando, em um prompt de comando elevado, digite:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Se a VM estiver usando o padrão, a saída terá a seguinte aparência:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Para ver qual provedor de tempo está sendo usado no momento.

```
w32tm /query /source
```


Aqui está a saída que você poderia ver e o que significaria:
    
- **time.Windows.com** -na configuração padrão, W32Time obteria o tempo de time.Windows.com. A qualidade da sincronização de tempo depende da conectividade com a Internet e é afetada por atrasos de pacotes. Essa é a saída normal da configuração padrão.
- **Provedor de sincronização de tempo da VM IC** -a VM está sincronizando o tempo do host. Isso geralmente é o resultado se você aceitar a sincronização de horário somente de host ou o NtpServer não estiver disponível no momento. 
- *Seu servidor de domínio* -o computador atual está em um domínio e o domínio define a hierarquia de sincronização de tempo.
- *Algum outro servidor* -W32Time foi configurado explicitamente para obter o tempo desse outro servidor. A qualidade de sincronização de tempo depende dessa qualidade de servidor de tempo.
- Relógio **CMOS local** -o relógio não está sincronizado. Você poderá obter essa saída se o W32Time não tiver tempo suficiente para iniciar após uma reinicialização ou quando todas as fontes de tempo configuradas não estiverem disponíveis.


## <a name="opt-in-for-host-only-time-sync"></a>Aceitar a sincronização de tempo somente de host

O Azure trabalha constantemente para melhorar a sincronização de tempo em hosts e pode garantir que toda a infraestrutura de sincronização seja colocada em data centers de propriedade da Microsoft. Se você tiver problemas de sincronização de tempo com a configuração padrão que prefere usar o time.windows.com como a fonte de tempo primária, poderá usar os comandos a seguir para aceitar a sincronização de horário somente de host.

Marque o provedor VMIC como habilitado. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Marque o provedor do NTPClient como desabilitado.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Reinicie o serviço W32Time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>VMs do Windows Server 2012 e R2 

O Windows Server 2012 e o Windows Server 2012 R2 têm configurações padrão diferentes para a sincronização de horário. O W32Time, por padrão, é configurado de forma que prefira uma baixa sobrecarga do serviço em relação ao tempo preciso. 

Se você quiser mover suas implantações do Windows Server 2012 e 2012 R2 para usar os padrões mais recentes que preferem o tempo preciso, você poderá aplicar as seguintes configurações.

Atualize os intervalos de pesquisa e atualização do W32Time para corresponder às configurações do Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Para que o W32Time possa usar os novos intervalos de sondagem, o NtpServers deve ser marcado como usando-os. Se os servidores forem anotados com a máscara bitflag 0x1, isso substituiria esse mecanismo e o W32Time usaria SpecialPollInterval em vez disso. Verifique se os servidores NTP especificados estão usando o sinalizador 0x8 ou nenhum sinalizador:

Verifique quais sinalizadores estão sendo usados para os servidores NTP usados.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Passos seguintes

Abaixo estão os links para obter mais detalhes sobre a sincronização de horário:

- [Ferramentas e configurações de serviço de tempo do Windows](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Aprimoramentos do Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Tempo preciso para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Limite de suporte para configurar o serviço de tempo do Windows para ambientes de alta precisão](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)



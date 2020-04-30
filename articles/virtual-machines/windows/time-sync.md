---
title: Sincronização de tempo para VMs windows em Azure
description: Sincronização de tempo para máquinas virtuais windows.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: cd9a196e5f957782de91cff69c01fbfa5716369a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100503"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Sincronização de tempo para VMs windows em Azure

A sincronização temporal é importante para a segurança e correlação de eventos. Por vezes, é utilizado para a implementação de transações distribuídas. A precisão do tempo entre vários sistemas informáticos é conseguida através da sincronização. A sincronização pode ser afetada por várias coisas, incluindo reboots e tráfego de rede entre a fonte de tempo e o computador que vai buscar o tempo. 

O Azure é agora apoiado pela infraestrutura que executa o Windows Server 2016. O Windows Server 2016 melhorou os algoritmos utilizados para corrigir o tempo e condicionar o relógio local para sincronizar com a UTC.  O Windows Server 2016 também melhorou o serviço VMICTimeSync que rege a forma como os VMs se sincronizam com o anfitrião por um tempo preciso. As melhorias incluem um tempo inicial mais preciso no arranque de VM ou no restabelecimento de VM e interromper a correção de latência para as amostras fornecidas ao Windows Time (W32time). 


>[!NOTE]
>Para uma visão geral rápida do serviço Windows Time, veja este [vídeo de visão geral de alto nível](https://aka.ms/WS2016TimeVideo).
>
> Para mais informações, consulte [o tempo exato para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Descrição geral

A precisão de um relógio de computador é avaliada na proximidade do relógio do computador à norma de tempo coordenada do tempo universal (UTC). A UTC é definida por uma amostra multinacional de relógios atómicos precisos que só pode ser desligado por um segundo em 300 anos. Mas, ler UTC requer hardware especializado. Em vez disso, os servidores de tempo são sincronizados com utc e são acedidos a partir de outros computadores para fornecer escalabilidade e robustez. Todos os computadores têm um serviço de sincronização temporal em execução que sabe a que horas os servidores devem usar e verifica periodicamente se o relógio do computador precisa de ser corrigido e ajusta o tempo se necessário. 

Os anfitriões do Azure são sincronizados com servidores de tempo internos da Microsoft que levam o seu tempo a partir de dispositivos Stratum 1, propriedade da Microsoft, com antenas GPS. As máquinas virtuais em Azure podem depender do seu hospedeiro para passar o tempo exato (tempo de*anfitrião)* para o VM ou o VM pode obter diretamente tempo a partir de um servidor de tempo, ou uma combinação de ambos. 

As interações virtuais com o hospedeiro também podem afetar o relógio. Durante [a manutenção da conservação](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)da memória, os VMs são interrompidos por até 30 segundos. Por exemplo, antes de a manutenção começar, o relógio VM mostra 10:00:00 AM e dura 28 segundos. Após o reinício do VM, o relógio do VM ainda mostraria 10:00:00 am, o que seria 28 segundos de desconto. Para corrigir isto, o serviço VMICTimeSync monitoriza o que está a acontecer no hospedeiro e solicita que as alterações ocorram nos VMs para compensar.

O serviço VMICTimeSync funciona em modo de amostra ou sincronização e só influenciará o relógio para a frente. No modo amostra, que requer tempo w32 para estar em execução, o serviço VMICTimeSync vota o anfitrião a cada 5 segundos e fornece amostras de tempo para W32time. Aproximadamente a cada 30 segundos, o serviço W32time tira a amostra de tempo mais recente e usa-a para influenciar o relógio do hóspede. O modo sync ativa-se se um hóspede tiver sido retomado ou se o relógio de um hóspede se desviar mais de 5 segundos atrás do relógio do anfitrião. Nos casos em que o serviço W32 time está em funcionamento adequado, este último caso nunca deverá acontecer.

Sem que a sincronização do tempo funcione, o relógio do VM acumularia erros. Quando há apenas um VM, o efeito pode não ser significativo a menos que a carga de trabalho exija uma cronometragem muito precisa. Mas na maioria dos casos, temos múltiplos VMs interligados que usam o tempo para rastrear transações e o tempo precisa ser consistente em toda a implementação. Quando o tempo entre VMs é diferente, pode ver os seguintes efeitos:

- A autenticação falhará. Protocolos de segurança como Kerberos ou tecnologia dependente de certificados dependem do tempo ser consistente em todos os sistemas. 
- É muito difícil descobrir o que aconteceu num sistema se os registos (ou outros dados) não concordam com o tempo. O mesmo evento pareceria que ocorreu em momentos diferentes, dificultando a correlação.
- Se o relógio estiver desligado, a faturação pode ser calculada incorretamente.

Os melhores resultados para implementações do Windows são alcançados utilizando o Windows Server 2016 como o sistema operativo de hóspedes, que garante que pode utilizar as mais recentes melhorias na sincronização de tempo.

## <a name="configuration-options"></a>Opções de configuração

Existem três opções para configurar a sincronização de tempo para os seus VMs windows hospedados no Azure:

- Hora e time.windows.com. Esta é a configuração predefinida utilizada nas imagens do Azure Marketplace.
- Só de anfitrião.
- Utilize outro servidor de tempo externo com ou sem o tempo de anfitrião.


### <a name="use-the-default"></a>Use o padrão

Por predefinição, as imagens VM do Windows Estão configuradas para o sincronismo de duas fontes: 

- O fornecedor NtpClient, que obtém informações de time.windows.com.
- O serviço VMICTimeSync, utilizado para comunicar o tempo de acolhimento aos VMs e fazer correções após a pausa do VM para manutenção. Os anfitriões do Azure utilizam dispositivos Stratum 1 da Microsoft para manter o tempo preciso.

w32 time preferiria o provedor de tempo na seguinte ordem de prioridade: nível de estrato, atraso de raiz, dispersão de raízes, compensação de tempo. Na maioria dos casos, a w32 time preferiria time.windows.com ao hospedeiro porque time.windows.com reporta menor estratos. 

Para as máquinas unidas de domínio, o domínio em si estabelece a hierarquia da sincronização do tempo, mas a raiz da floresta ainda precisa de levar tempo de algum lugar e as seguintes considerações continuariam a ser verdadeiras.


### <a name="host-only"></a>Só para hospedeiros 

Como time.windows.com é um servidor PÚBLICO ntp, sincronizar o tempo com ele requer o envio de tráfego através da internet, vários atrasos de pacotes podem afetar negativamente a qualidade da sincronização de tempo. Remover time.windows.com mudando para sincronização apenas para o hospedeiro pode, por vezes, melhorar os resultados da sincronização do tempo.

Mudar para sincronização de tempo apenas para o anfitrião faz sentido se experimentar problemas de sincronização de tempo usando a configuração predefinida. Experimente a sincronização apenas do hospedeiro para ver se isso melhoraria a sincronização de tempo em VM. 

### <a name="external-time-server"></a>Servidor de tempo externo

Se tiver requisitos específicos de sincronização de tempo, existe também a opção de utilizar servidores de tempo externos. Os servidores de tempo externos podem fornecer um tempo específico, o que pode ser útil para cenários de teste, garantindo a uniformidade do tempo com máquinas alojadas em datacenters não Microsoft, ou manuseando segundos de salto de forma especial.

Pode combinar servidores externos com o serviço VMICTimeSync e vMICTimeProvider para fornecer resultados semelhantes à configuração predefinida. 

## <a name="check-your-configuration"></a>Verifique a configuração


Verifique se o fornecedor de tempo NtpClient está configurado para utilizar servidores NTP explícitos (NTP) ou sincronização de tempo de domínio (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Se o VM estiver a utilizar a NTP, verá a seguinte saída:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Para ver que servidor de tempo o fornecedor de tempo NtpClient está a usar, num tipo de pedido de comando elevado:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Se o VM estiver a utilizar o padrão, a saída será assim:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Para ver que horas o fornecedor está a ser utilizado atualmente.

```
w32tm /query /source
```


Aqui está a saída que se pode ver e o que significaria:
    
- **time.windows.com** - na configuração predefinida, a w32 time obteria tempo de time.windows.com. A qualidade da sincronização temporal depende da conectividade da Internet e é afetada por atrasos nos pacotes. Esta é a saída habitual da configuração predefinida.
- **VM IC Time Synchronization Provider** - o VM está sincronizando o tempo do hospedeiro. Este é normalmente o resultado se optar por sincronização de tempo apenas para o anfitrião ou o NtpServer não estiver disponível no momento. 
- *O seu servidor* de domínio - a máquina atual está num domínio e o domínio define a hierarquia da sincronização temporal.
- *Algum outro servidor* - o w32time foi explicitamente configurado para obter o tempo de outro servidor. A qualidade da sincronização de tempo depende da qualidade do servidor do tempo.
- **Relógio CMOS local** - o relógio não está sincronizado. Pode obter esta saída se o w32time não tiver tido tempo suficiente para começar após um reboot ou quando todas as fontes de tempo configuradas não estiverem disponíveis.


## <a name="opt-in-for-host-only-time-sync"></a>Opt-in para sincronização de tempo apenas para hospedeiro

O Azure está constantemente a trabalhar na melhoria da sincronização de tempo nos anfitriões e pode garantir que todo o tempo de infraestrutura de sincronização está colocalizado em datacenters da Microsoft. Se tiver problemas de sincronização de tempo com a configuração predefinida que prefere usar time.windows.com como fonte de tempo principal, pode utilizar os seguintes comandos para optar pela sincronização de tempo apenas do hospedeiro.

Marque o fornecedor VMIC como ativado. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Marque o fornecedor NTPClient como desativado.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Reinicie o serviço w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>VMs do Windows Server 2012 e R2 

O Windows Server 2012 e o Windows Server 2012 R2 têm diferentes definições predefinidas para sincronização de tempo. A w32time por padrão é configurada de uma forma que prefere a sobrecarga baixa do serviço até ao tempo preciso. 

Se pretender mover as implementações do Windows Server 2012 e 2012 R2 para utilizar as falhas mais recentes que preferem o tempo preciso, pode aplicar as seguintes definições.

Atualize os intervalos da sondagem e da atualização da w32time para corresponder às definições do Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Para que a w32 time possa utilizar os novos intervalos de sondagens, os NtpServers serão marcados como usando-os. Se os servidores forem anotados com máscara de bitflag 0x1, isso substituiria este mecanismo e o w32time usaria o SpecialPollInterval. Certifique-se de que os servidores NTP especificados estão a usar uma bandeira 0x8 ou nenhuma bandeira:

Verifique quais as bandeiras que estão a ser utilizadas para os servidores NTP usados.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Passos seguintes

Abaixo estão os links para mais detalhes sobre a sincronização do tempo:

- [Ferramentas e Definições do Serviço de Hora do Windows](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Melhorias do Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Hora precisa para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Limite de suporte para configurar o serviço Windows Time para ambientes de alta precisão](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)



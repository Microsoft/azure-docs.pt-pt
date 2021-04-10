---
title: Sincronização de tempo para VMs do Windows em Azure
description: Sincronização temporal para máquinas virtuais do Windows.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 85abc77757d31f3b1054a0670ea3f65a4fcb2e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555963"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Sincronização de tempo para VMs do Windows em Azure

A sincronização do tempo é importante para a segurança e a correlação de eventos. Por vezes é usado para a implementação de transações distribuídas. A precisão do tempo entre vários sistemas informáticos é conseguida através da sincronização. A sincronização pode ser afetada por várias coisas, incluindo reinicializações e tráfego de rede entre a fonte de tempo e o computador a recolher o tempo. 

O Azure é agora apoiado por infraestruturas que executam o Windows Server 2016. O Windows Server 2016 melhorou os algoritmos utilizados para corrigir o tempo e condicionar o relógio local a sincronizar com o UTC.  O Windows Server 2016 também melhorou o serviço VMICTimeSync que rege a forma como os VMs sincronizam com o hospedeiro durante um tempo preciso. As melhorias incluem um tempo inicial mais preciso no início do VM ou na restauração de VM e interrupção da correção de latência para amostras fornecidas ao Windows Time (W32time). 


>[!NOTE]
>Para uma visão geral rápida do serviço Windows Time, veja este [vídeo de visão geral de alto nível](https://aka.ms/WS2016TimeVideo).
>
> Para obter mais informações, consulte [a hora exata para o Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Descrição Geral

A precisão para um relógio de computador é avaliada sobre a proximidade do relógio do computador ao padrão de tempo universal coordenado (UTC). A UTC é definida por uma amostra multinacional de relógios atómicos precisos que só podem ser desligados por um segundo em 300 anos. Mas, ler a UTC requer hardware especializado. Em vez disso, os servidores de tempo são sincronizados com utc e são acedidos a partir de outros computadores para fornecer escalabilidade e robustez. Todos os computadores têm o serviço de sincronização de tempo em execução que sabe a que horas os servidores devem utilizar e verifica periodicamente se o relógio do computador precisa de ser corrigido e ajusta o tempo se necessário. 

Os anfitriões do Azure são sincronizados com servidores internos do tempo da Microsoft que levam o seu tempo a partir de dispositivos Stratum 1 da Microsoft, com antenas GPS. As máquinas virtuais em Azure podem depender do seu anfitrião para passar o tempo exato (hora do *anfitrião)* para o VM ou o VM pode obter tempo diretamente a partir de um servidor de tempo, ou uma combinação de ambos. 

As interações virtuais da máquina com o hospedeiro também podem afetar o relógio. Durante [a manutenção da conservação da memória,](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)os VM são pausados até 30 segundos. Por exemplo, antes de a manutenção começar, o relógio VM mostra 10:00:00 am e dura 28 segundos. Após o recomeço do VM, o relógio do VM ainda mostraria 10:00:00 am, o que seria 28 segundos de desconto. Para corrigir isto, o serviço VMICTimeSync monitoriza o que está a acontecer no anfitrião e pede que as alterações ocorram nos VMs para compensar.

O serviço VMICTimeSync funciona no modo de amostra ou sincronização e só influenciará o relógio para a frente. No modo de amostra, que requer que o W32time esteja em funcionamento, o serviço VMICTimeSync sonda o anfitrião a cada 5 segundos e fornece amostras de tempo para w32time. Aproximadamente a cada 30 segundos, o serviço W32time toma a última amostra de tempo e usa-a para influenciar o relógio do hóspede. O modo sync ativa-se se um hóspede tiver sido retomado ou se o relógio de um hóspede deslizar mais de 5 segundos atrás do relógio do anfitrião. Nos casos em que o serviço W32time esteja a funcionar corretamente, este último caso nunca deverá acontecer.

Sem sincronização de tempo, o relógio do VM acumularia erros. Quando há apenas um VM, o efeito pode não ser significativo a menos que a carga de trabalho exija uma cronometragem altamente precisa. Mas na maioria dos casos, temos múltiplos VMs interligados que usam o tempo para rastrear transações e o tempo precisa ser consistente em toda a implementação. Quando o tempo entre VMs é diferente, pode-se ver os seguintes efeitos:

- A autenticação falhará. Protocolos de segurança como Kerberos ou tecnologia dependente de certificados dependem do tempo ser consistente em todos os sistemas. 
- É muito difícil descobrir o que aconteceu num sistema se os registos (ou outros dados) não chegarem a tempo. O mesmo evento pareceria ter ocorrido em momentos diferentes, dificultando a correlação.
- Se o relógio estiver desligado, a faturação pode ser calculada incorretamente.

Os melhores resultados para implementações do Windows são alcançados utilizando o Windows Server 2016 como sistema operativo para convidados, o que garante que pode utilizar as mais recentes melhorias na sincronização de tempo.

## <a name="configuration-options"></a>Opções de configuração

Existem três opções para configurar a sincronização de tempo para os seus VMs windows hospedados em Azure:

- Hora do anfitrião e time.windows.com. Esta é a configuração padrão utilizada nas imagens do Azure Marketplace.
- Só para anfitriões.
- Utilize outro servidor de tempo externo com ou sem tempo de anfitrião.


### <a name="use-the-default"></a>Use o padrão

Por predefinição, as imagens VM do Windows OS são configuradas para que o w32time se sincronize a partir de duas fontes: 

- O fornecedor NtpClient, que obtém informações de time.windows.com.
- O serviço VMICTimeSync, utilizado para comunicar o tempo de anfitrião aos VMs e fazer correções após a pausa do VM para manutenção. Os anfitriões do Azure usam dispositivos Stratum 1 da Microsoft para manter o tempo preciso.

w32time prefere o fornecedor de tempo na seguinte ordem de prioridade: nível de estrato, atraso de raiz, dispersão de raízes, compensação do tempo. Na maioria dos casos, w32time em um Azure VM preferiria o tempo de anfitrião devido à avaliação que faria para comparar ambas as fontes de tempo. 

Para máquinas de domínio que se juntam, o próprio domínio estabelece a hierarquia da sincronização do tempo, mas a raiz da floresta ainda precisa de tempo de algum lugar e as seguintes considerações continuariam a ser verdadeiras.


### <a name="host-only"></a>Só hospedeiro 

Como time.windows.com é um servidor NTP público, sincronizar o tempo com ele requer o envio de tráfego através da internet, diferentes atrasos de pacotes podem afetar negativamente a qualidade da sincronização de tempo. Remover time.windows.com ao mudar para sincronização apenas de hospedeiro pode, por vezes, melhorar os resultados da sincronização do seu tempo.

Mudar para sincronização de tempo apenas para anfitrião faz sentido se experimentar problemas de sincronização de tempo usando a configuração padrão. Experimente a sincronização apenas do anfitrião para ver se isso melhoraria a sincronização de tempo em VM. 

### <a name="external-time-server"></a>Servidor de tempo externo

Se tiver requisitos específicos de sincronização de tempo, existe também a opção de utilizar servidores de tempo externos. Os servidores de tempo externos podem fornecer tempo específico, o que pode ser útil para cenários de teste, garantindo a uniformidade do tempo com máquinas hospedadas em datacenters não Microsoft, ou manuseando segundos de salto de forma especial.

Pode combinar servidores externos com o serviço VMICTimeSync e vmicTimeProvider para fornecer resultados semelhantes à configuração padrão. 

## <a name="check-your-configuration"></a>Verifique a configuração


Verifique se o fornecedor de tempo NtpClient está configurado para utilizar servidores NTP explícitos (NTP) ou sincronização de tempo de domínio (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Se o VM estiver a utilizar o NTP, verá a seguinte saída:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Para ver a que altura o fornecedor de tempo NtpClient está a usar, com um tipo de pedido de comando elevado:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Se o VM estiver a usar o padrão, a saída será assim:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Para ver que fornecedor de tempo está a ser utilizado atualmente.

```
w32tm /query /source
```


Aqui está a saída que se pode ver e o que significaria:
    
- **time.windows.com** - na configuração padrão, w32time obteria tempo de time.windows.com. A qualidade da sincronização de tempo depende da conectividade da internet e é afetada por atrasos nos pacotes. Esta é a saída habitual que se consegue numa máquina física.
- **VM IC Time Synchronization Provider**  - o VM está a sincronizar o tempo do anfitrião. Esta é a saída habitual que se obtém numa máquina virtual a funcionar no Azure. 
- *O seu servidor de domínio* - a máquina atual está num domínio e o domínio define a hierarquia da sincronização do tempo.
- *Algum outro servidor* - w32time foi explicitamente configurado para obter o tempo de outro servidor. A qualidade da sincronização de tempo depende desta qualidade do servidor de tempo.
- **Relógio CMOS local** - o relógio não está sincronizado. Pode obter esta saída se a W32time não tiver tido tempo suficiente para começar depois de um reboot ou quando todas as fontes de tempo configuradas não estiverem disponíveis.


## <a name="opt-in-for-host-only-time-sync"></a>Opt-in para sincronização de tempo só para anfitrião

O Azure está constantemente a trabalhar na melhoria da sincronização de tempo nos anfitriões e pode garantir que toda a infraestrutura de sincronização de tempo é collocada em datacenters da Microsoft. Se tiver problemas de sincronização de tempo com a configuração padrão que prefere usá-time.windows.com como fonte de tempo primária, pode utilizar os seguintes comandos para optar pela sincronização do tempo apenas de hospedeiro.

Marque o fornecedor VMIC conforme ativado. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Marque o provedor NTPClient como deficiente.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Reinicie o serviço w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 e VMs R2 

O Windows Server 2012 e o Windows Server 2012 R2 têm diferentes definições padrão para sincronização de tempo. O w32time por padrão é configurado de uma forma que prefere a baixa sobrecarga do serviço até tempo preciso. 

Se pretender mover as suas implementações R2 do Windows Server 2012 e 2012 para utilizar as mais recentes padrão que preferem tempo preciso, pode aplicar as seguintes definições.

Atualize a pesquisa w32time e intervalos de atualização para corresponder às definições do Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Para que a w32time possa utilizar os novos intervalos de votação, os NtpServers precisam de ser marcados como usando-os. Se os servidores forem anotados com 0x1 máscara bitflag, isso iria anular este mecanismo e w32time usaria SpecialPollInterval em vez disso. Certifique-se de que os servidores NTP especificados estão a usar 0x8 bandeira ou nenhum sinal:

Verifique quais as bandeiras que estão a ser usadas para os servidores NTP usados.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Passos seguintes

Abaixo estão os links para mais detalhes sobre a sincronização de tempo:

- [Ferramentas e Definições do Serviço de Hora do Windows](/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [Melhorias do Windows Server 2016 ](/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Tempo preciso para o Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time)
- [Limite de suporte para configurar o serviço Windows Time para ambientes de alta precisão](/windows-server/networking/windows-time-service/support-boundary)

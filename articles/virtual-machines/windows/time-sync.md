---
title: Tempo de sincronização para VMs do Windows no Azure | Documentos da Microsoft
description: Sincronização de hora para as máquinas virtuais do Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: d7363c3d1cd3aaf6aae8cadbea232c909000f214
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669607"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Sincronização de hora para VMs do Windows no Azure

Sincronização de hora é importante para segurança e a correlação de eventos. Por vezes, é utilizado para a implementação de transações distribuídas. Precisão de hora entre vários sistemas de computador é obtido através da sincronização. Sincronização pode ser afetada por várias coisas, incluindo os reinícios e tráfego de rede entre a origem de hora e o computador a obter a hora. 

Azure agora conta com a infraestrutura com o Windows Server 2016. Windows Server 2016 tem melhorados algoritmos utilizados para corrigir o tempo da Microsoft e o relógio local para sincronizar com o UTC.  Windows Server 2016 melhorada, também, o serviço de VMICTimeSync que controla como as VMs sincronizar com o anfitrião para o tempo preciso. As melhorias incluem tempo inicial mais preciso no início VM ou o restauro de VMS e correção de latência de interrupção para os exemplos fornecidos para o tempo do Windows (W32time). 


>[!NOTE]
>Para obter uma descrição geral do serviço de hora do Windows, dê uma olhada nisso [descrição geral de alto nível vídeo](https://aka.ms/WS2016TimeVideo).
>
> Para obter mais informações, consulte [tempo preciso para o Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Descrição geral

Precisão de um relógio de computador é gauged sobre como fechar o relógio do computador é o padrão de tempo de hora Universal Coordenada (UTC). UTC é definido por uma amostra multinacional de relógios Ocorreu precisas, que só pode ficar inativo por um segundo nos anos 300. No entanto, ler UTC diretamente requer hardware especializado. Em vez disso, os servidores de tempo são sincronizados com o UTC e são acessados a partir de outros computadores para fornecer escalabilidade e robustez. Cada computador tem de tempo de serviço de sincronização em execução que sabe que servidores de tempo para utilizar e verifica periodicamente se o relógio do computador precisa de ser corrigido e ajusta o tempo se for necessário. 

Anfitriões do Azure são sincronizadas com servidores de tempo de Microsoft internos que usam seu tempo a partir de dispositivos pertencentes à Microsoft o Stratum 1, com antenas GPS. Máquinas virtuais no Azure pode optar por confiar no host para passar o tempo preciso (*alojar tempo*) para a VM ou a VM pode diretamente obter hora de um servidor de tempo, ou uma combinação de ambos. 

Interações de máquina virtual com o host também podem afetar o relógio. Durante [memória preservação da manutenção](maintenance-and-updates.md#memory-preserving-maintenance), as VMs são colocadas em pausa para até 30 segundos. Por exemplo, antes do início da manutenção o relógio VM mostra: 00 10:00 e tem uma duração de 28 segundos. Depois da VM é retomada, o relógio na VM ainda mostraria: 00 10:00, que seria 28 segundos desativado. A correta para isso, o serviço de VMICTimeSync monitoriza o que está acontecendo no host e pedidos de alterações a ocorrer nas VMs para compensar.

O serviço de VMICTimeSync funciona em modo de exemplo ou sincronização e apenas irão influenciar o relógio para a frente. No modo de exemplo, o que requer W32time estar em execução, o serviço de VMICTimeSync consulta o anfitrião em 5 segundos e fornece exemplos de tempo para W32time. Aproximadamente a cada 30 segundos, o serviço W32time leva o exemplo de tempo mais recente e utiliza-o para influenciar o relógio do convidado. O modo de sincronização ativa se um convidado foi retomado ou se o relógio de um convidado drifts mais de 5 segundos por trás do relógio do anfitrião. Em casos em que o serviço W32time está sendo executando corretamente, o último caso deveriam ocorrer nunca.

Sem o trabalho de sincronização de hora, o relógio na VM seria acumular-se erros. Quando existe apenas uma VM, o efeito poderá não ser significativo, a menos que a carga de trabalho requer timekeeping altamente preciso. Mas, na maioria dos casos, podemos ter várias, interligados VMs que utilizam o tempo para controlar as transações e as necessidades de tempo para ser consistente em toda a implantação. Quando o tempo entre VMs é diferente, pode ver os seguintes efeitos:

- Autenticação irá falhar. Protocolos de segurança, como Kerberos ou tecnologia de dependente do certificado dependem de tempo a ser consistente em todos os sistemas. 
- É muito difícil descobrir o que ocorreram num sistema se registos (ou outros dados) não concordam no tempo. O mesmo evento teria o aspeto, como ocorreu em alturas diferentes, correlação tornando difícil.
- Se o relógio estiver desativada, a faturação foi possível calcular o incorretamente.

Os melhores resultados para implementações do Windows são alcançados ao utilizar o Windows Server 2016 como o sistema de operativo convidado, que garante que pode usar as melhorias mais recentes em sincronia de tempo.

## <a name="configuration-options"></a>Opções de configuração

Existem três opções para configurar a sincronização de hora para as VMs do Windows alojados no Azure:

- Hora do anfitrião e time.windows.com. Esta é a configuração predefinida utilizada nas imagens do Azure Marketplace.
- Apenas de anfitrião.
- Utilize o servidor de tempo de outro, externo com ou sem utilizar a hora do anfitrião.


### <a name="use-the-default"></a>Utilize a predefinição

Por predefinição, imagens de VM de SO do Windows estão configuradas para w32time a sincronização de duas fontes: 

- O fornecedor de NtpClient, que obtém informações do time.windows.com.
- O serviço de VMICTimeSync, utilizado para comunicar o tempo de anfitrião para as VMs e fazer correções depois da VM está em pausa para manutenção. Anfitriões do Azure utilizem dispositivos de 1 de Stratum pertencentes à Microsoft para manter o tempo preciso.

W32Time prefere o fornecedor de tempo na seguinte ordem de prioridade: nível de stratum, atraso de raiz, dispersão de raiz, compensação de tempo. Na maioria dos casos, w32time iria preferir time.windows.com para o anfitrião porque time.windows.com relatórios stratum inferior. 

Para máquinas de um domínio associado o domínio propriamente dito estabelece a hierarquia de sincronização de tempo, mas a raiz de floresta ainda precisa levar o tempo de algum lugar e as considerações seguintes seriam ainda eram verdadeiras.


### <a name="host-only"></a>Só de anfitrião 

Uma vez time.windows.com é um servidor NTP público, a sincronização de hora com ele requer o envio de tráfego através da internet, variados atrasos de pacote podem afetar negativamente a qualidade da sincronização de hora. Remover time.windows.com alternando para sincronização só de anfitrião, às vezes, pode melhorar o tempo de resultados de sincronização.

Mudar para faz de sincronização de tempo só de anfitrião sentido se tiver a sincronização de hora problemas com a configuração predefinida. Experimente a sincronização só de anfitrião para ver se que aprimorariam a sincronização de hora na VM. 

### <a name="external-time-server"></a>Servidor de hora externa

Se tiver requisitos de sincronização de hora específica, há também uma opção de utilizar servidores de hora externa. Servidores de hora externa podem fornecer hora específica, que pode ser útil para cenários de teste, garantir a uniformidade de tempo com máquinas alojadas em datacenters de terceiros ou segundos bissextos de manipulação de forma especial.

Pode combinar servidores externos com o serviço de VMICTimeSync e VMICTimeProvider para fornecer resultados semelhantes à configuração padrão. 

## <a name="check-your-configuration"></a>Verifique a configuração


Verifique se o fornecedor de tempo de NtpClient está configurado para utilizar os servidores NTP explícitos (NTP) ou a sincronização de hora de domínio (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Se a VM estiver a utilizar NTP, verá a seguinte saída:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Para ver o que servidor de tempo é utilizar o fornecedor de tempo NtpClient, num tipo de linha de comandos elevada:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Se a VM estiver a utilizar a predefinição, a saída será assim:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Para ver que provedor de tempo está a ser utilizado atualmente.

```
w32tm /query /source
```


Eis a saída, que pode ver e o que poderia significar:
    
- **Time.Windows.com** -na configuração predefinida, o w32time obteria tempo de time.windows.com. A qualidade de sincronização de tempo depende da ligação à internet para ele e é afetada por atrasos de pacote. Esta é a saída normal da configuração do padrão.
- **Provedor de sincronização de hora do VM IC** -a VM está a sincronizar a hora do anfitrião. Normalmente, isso é o resultado se optar ativamente pela sincronização de hora de só de anfitrião ou o NtpServer não está disponível neste momento. 
- *O servidor de domínio* - a máquina atual está num domínio e o domínio define a hierarquia de sincronização de tempo.
- *Algum outro servidor* -w32time tenha sido configurada explicitamente para obter a hora a partir desse servidor outro. Qualidade de sincronização de tempo depende da qualidade de servidor neste momento.
- **Local de VOLTAGEM de relógio** -relógio não está sincronizado. Pode obter esta saída se w32time ainda não teve tempo suficiente para iniciar após um reinício ou quando todas as origens de tempo configurado não estão disponíveis.


## <a name="opt-in-for-host-only-time-sync"></a>Optar Ativamente pela sincronização de hora de só de anfitrião

O Azure está constantemente a trabalhar em melhorar a sincronização de hora em anfitriões e garante que toda a infraestrutura de sincronização de hora é colocalizada nos centros de dados pertencentes à Microsoft. Se tiver problemas com a configuração padrão que prefere usar time.windows.com como a origem de hora primário de sincronização de hora, pode utilizar os seguintes comandos para optar ativamente pela sincronização de hora de só de anfitrião.

Marca o fornecedor VMIC como ativada. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Marca o fornecedor de NTPClient como desativados.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Reinicie o serviço w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 e VMs do R2 

Windows Server 2012 e Windows Server 2012 R2 tem as configurações padrão diferentes para sincronização de hora. W32time por predefinição está configurado de modo que prefere baixa sobrecarga do serviço ao longo do tempo preciso. 

Se pretender mover o seu Windows Server 2012 e 2012 R2 implementações para usar os padrões mais recentes que preferem o tempo preciso, pode aplicar as seguintes definições.

Atualizar a pesquisa de w32time e atualizar intervalos para corresponder as definições do Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Para w32time poder utilizar os novos intervalos de consulta, o NtpServers ser marcado como usá-los. Se os servidores são anotados com 0x1 bitflag máscara, que substituirá esse mecanismo e w32time usaria SpecialPollInterval em vez disso. Certifique-se de que os servidores NTP especificados estão a utilizar o sinalizador de 0x8 ou nenhum sinalizador em todos os:

Verifique os sinalizadores estão a ser utilizadas para os servidores NTP utilizados.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para obter mais detalhes sobre a sincronização de hora:

- [Ferramentas de serviço de tempo do Windows e definições](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Aprimoramentos do Windows Server 2016 ](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Hora exata do Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Limites de suporte para configurar o serviço de hora do Windows para ambientes de alta precisão](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)



---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: b13b809b04f6cf878d68311b756ed2ca826f9697
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935283"
---
**Última atualização do documento**: 9 de agosto de 2019 10:00 AM PST.

A divulgação de uma [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) conhecida como ataques de canal lateral de execução especulativa resultou em perguntas de clientes que buscam mais clareza.  

A Microsoft implantou mitigações em todos os nossos serviços de nuvem. A infraestrutura que executa o Azure e isola as cargas de trabalho do cliente umas das outras é protegida. Isso significa que um invasor potencial usando a mesma infraestrutura não pode atacar seu aplicativo usando essas vulnerabilidades.

O Azure está usando a [manutenção de preservação de memória](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) sempre que possível, para minimizar o impacto do cliente e eliminar a necessidade de reinicializações. O Azure continuará utilizando esses métodos ao fazer atualizações em todo o âmbito do host e proteger nossos clientes.

Mais informações sobre como a segurança é integrada em todos os aspectos do Azure estão disponíveis no site de [documentação de segurança do Azure](https://docs.microsoft.com/azure/security/) . 

> [!NOTE] 
> Como este documento foi publicado pela primeira vez, várias variantes dessa classe de vulnerabilidade foram divulgadas. A Microsoft continua a ser investido intensamente na proteção de nossos clientes e no fornecimento de diretrizes. Esta página será atualizada à medida que continuarmos lançando correções adicionais. 
> 
> Em 14 de maio de 2019, a [Intel divulgau](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) um novo conjunto de vulnerabilidades de canal lateral de execução especulativa, conhecido como amostragem de dados de microarquitetura (MDS, consulte o guia de segurança da Microsoft [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), que recebeu vários CVEs: 
> - CVE-2019-11091-microarquitetura dados de amostragem de memória não cache (MDSUM)
> - CVE-2018-12126-MSBDS (amostragem de dados de buffer de armazenamento de microarquitetura) 
> - CVE-2018-12127-microarquitetura carregar dados de porta (MLPDS)
> - CVE-2018-12130-microarquitetura de amostragem de dados de buffer de preenchimento (MFBDS)
>
> Essa vulnerabilidade afeta os processadores Intel® Core® e os processadores Intel® Xeon®.  O Microsoft Azure lançou atualizações do sistema operacional e está implantando o novo microcódigo, pois ele é disponibilizado pela Intel, em toda a frota para proteger nossos clientes contra essas novas vulnerabilidades.   O Azure está trabalhando de forma minuciosa com a Intel para testar e validar o novo microcódigo antes do lançamento oficial na plataforma. 
>
> **Os clientes que estão executando código não confiável dentro de sua VM** precisam tomar medidas para proteger contra essas vulnerabilidades lendo abaixo as orientações adicionais sobre todas as vulnerabilidades de canal lateral de execução especulativa (Microsoft insiders avçd [ 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)e [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Outros clientes devem avaliar essas vulnerabilidades de uma perspectiva de defesa profunda e considerar as implicações de segurança e desempenho de sua configuração escolhida.



## <a name="keeping-your-operating-systems-up-to-date"></a>Mantendo seus sistemas operacionais atualizados

Embora uma atualização do sistema operacional não seja necessária para isolar seus aplicativos em execução no Azure de outros clientes do Azure, é sempre uma prática recomendada manter seu software atualizado. Os pacotes cumulativos de segurança mais recentes para o Windows contêm mitigações para várias vulnerabilidades de canal lateral de execução especulativa. Da mesma forma, as distribuições do Linux lançaram várias atualizações para resolver essas vulnerabilidades. Aqui estão nossas ações recomendadas para atualizar seu sistema operacional:

| Oferta | Ação Recomendada  |
|----------|---------------------|
| Serviços Cloud do Azure  | Habilite a [atualização automática](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) ou verifique se você está executando o SO convidado mais recente. |
| Máquinas Virtuais do Azure para Linux | Instale atualizações do seu provedor de sistema operacional. Para obter mais informações, consulte [Linux](#linux) mais adiante neste documento. |
| Máquinas Virtuais do Windows do Azure  | Instale o pacote cumulativo de segurança mais recente.
| Outros serviços de PaaS do Azure | Não há nenhuma ação necessária para os clientes que usam esses serviços. O Azure mantém as versões do sistema operacional atualizadas automaticamente. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Diretrizes adicionais se você estiver executando código não confiável 

Os clientes que permitem que usuários não confiáveis executem código arbitrário podem desejar implementar alguns recursos de segurança adicionais dentro de suas máquinas virtuais ou serviços de nuvem do Azure. Esses recursos protegem contra os vetores de divulgação dentro do processo que várias vulnerabilidades de execução especulativa descrevem.

Cenários de exemplo em que são recomendados recursos de segurança adicionais:

- Você permite que o código que você não confia na sua VM seja executado.  
    - *Por exemplo, você permite que um de seus clientes carregue um script ou binário que você executa em seu aplicativo*. 
- Você permite que os usuários que você não confiam para fazer logon na sua VM usando contas com poucos privilégios.   
    - *Por exemplo, você permite que um usuário com poucos privilégios faça logon em uma de suas VMs usando a área de trabalho remota ou SSH*.  
- Você permite que usuários não confiáveis acessem máquinas virtuais implementadas por meio de virtualização aninhada.  
    - *Por exemplo, você controla o host Hyper-V, mas aloca as VMs a usuários não confiáveis*. 

Os clientes que não implementam um cenário que envolva código não confiável não precisam habilitar esses recursos de segurança adicionais. 

## <a name="enabling-additional-security"></a>Habilitando segurança adicional 

Você pode habilitar recursos de segurança adicionais dentro de sua VM ou serviço de nuvem se você estiver executando código não confiável. Em paralelo, verifique se o sistema operacional está atualizado para habilitar recursos de segurança dentro de sua VM ou serviço de nuvem

### <a name="windows"></a>Windows 

O sistema operacional de destino deve estar atualizado para habilitar esses recursos de segurança adicionais. Embora várias atenuações de canal lateral de execução especulativa estejam habilitadas por padrão, os recursos adicionais descritos aqui devem ser habilitados manualmente e podem causar um impacto no desempenho. 


**Etapa 1: Desabilitar o hyperthreading na VM** -os clientes que executam código não confiável em uma VM Hyper-threaded precisarão desabilitar o Hyper-Threading ou mover para um tamanho de VM não Hyper-thread. Referencie [este documento](https://docs.microsoft.com/azure/virtual-machines/windows/acu) para obter uma lista de tamanhos de VM Hyper-threaded (em que a taxa de vCPU para Core é 2:1). Para verificar se sua VM tem o hyperthreading habilitado, consulte o script abaixo usando a linha de comando do Windows de dentro da VM.

Digite `wmic` para inserir a interface interativa. Em seguida, digite o seguinte para exibir a quantidade de processadores físicos e lógicos na VM.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Se o número de processadores lógicos for maior que processadores físicos (núcleos), o hyperthreading será habilitado.  Se você estiver executando uma VM Hyper-threaded, [entre em contato com o suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter o hyperthreading desabilitado.  Depois que o hyperthreading estiver desabilitado, **o suporte exigirá uma reinicialização completa da VM**. Consulte a [contagem](#core-count) de núcleos para entender por que sua contagem de núcleos de VM diminuiu.


**Etapa 2**: Em paralelo à etapa 1, siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para verificar se as proteções estão habilitadas usando o módulo [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell.

> [!NOTE]
> Se você tiver baixado este módulo anteriormente, será necessário instalar a versão mais recente.
>


A saída do script do PowerShell deve ter os valores abaixo para validar as proteções habilitadas contra essas vulnerabilidades:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Se a saída for `MDS mitigation is enabled: False`mostrada, [entre em contato com o suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter as opções de mitigação disponíveis.



**Etapa 3**: Para habilitar a KVAS (sombra de endereço virtual) do kernel e o suporte do sistema operacional BTI (injeção de destino de [](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) Branch), siga as instruções em `Session Manager` KB4072698 para habilitar as proteções usando as chaves do registro. Uma reinicialização é necessária.


**Etapa 4**: Para implantações que estão [](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) usando virtualização aninhada (apenas D3 e E3): Essas instruções se aplicam dentro da VM que você está usando como um host Hyper-V.

1.  Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para habilitar as proteções usando `MinVmVersionForCpuBasedMitigations` as chaves do registro.
2.  Defina o tipo de Agendador do `Core` hipervisor como seguindo as instruções [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Habilitar o conjunto de recursos de segurança adicionais dentro exige que o sistema operacional de destino esteja totalmente atualizado. Algumas atenuações serão habilitadas por padrão. A seção a seguir descreve os recursos que estão desativados por padrão e/ou dependem do suporte de hardware (microcódigo). Habilitar esses recursos pode causar um impacto no desempenho. Consulte a documentação do provedor do seu sistema operacional para obter mais instruções


**Etapa 1: Desabilitar o hyperthreading na VM** -os clientes que executam código não confiável em uma VM Hyper-threaded precisarão desabilitar o Hyper-Threading ou mover para uma VM não Hyper-thread.  Referencie [este documento](https://docs.microsoft.com/azure/virtual-machines/linux/acu) para obter uma lista de tamanhos de VM Hyper-threaded (em que a taxa de vCPU para Core é 2:1). Para verificar se você está executando uma VM Hyper-threaded, execute o `lscpu` comando na VM Linux. 

Se `Thread(s) per core = 2`, o hyperthreading foi habilitado. 

Se `Thread(s) per core = 1`, o hyperthreading foi desabilitado. 

 
Exemplo de saída para uma VM com o hyperthreading habilitado: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Se você estiver executando uma VM Hyper-threaded, [entre em contato com o suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter o hyperthreading desabilitado.  Depois que o hyperthreading estiver desabilitado, **o suporte exigirá uma reinicialização completa da VM**. Consulte a [contagem](#core-count) de núcleos para entender por que sua contagem de núcleos de VM diminuiu.



**Etapa 2**: Para atenuar qualquer uma das vulnerabilidades de canal lateral de execução especulativa abaixo, consulte a documentação do provedor do sistema operacional:   
 
- [RedHat e CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Contagem de núcleos

Quando uma VM Hyper-threaded é criada, o Azure aloca 2 threads por núcleo – eles são chamados de vCPUs. Quando o hyperthreading está desabilitado, o Azure remove um thread e superfícies de núcleos de thread único (núcleos físicos). A taxa de vCPU para CPU é 2:1, assim, depois que o hyperthreading estiver desabilitado, a contagem de CPU na VM parecerá ter diminuído pela metade. Por exemplo, uma VM D8_v3 é uma VM Hyper-thread executada em 8 vCPUs (2 threads por núcleos x 4 núcleos).  Quando o hyperthreading estiver desabilitado, as CPUs serão descartadas para quatro núcleos físicos com 1 thread por núcleo. 

## <a name="next-steps"></a>Passos Seguintes

Este artigo fornece orientação para os ataques de canal lateral de execução especulativo abaixo que afetam muitos processadores modernos:

[Meltdown Spectre](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 – injeção de destino de ramificação (BTI)  
- CVE-2017-5754-isolamento de tabela de página de kernel (KPTI)
- CVE-2018-3639 – bypass de armazenamento especulativo (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – informações de kernel do Windows – variante da variante de Spectre 1
 
[Falha de terminal L1 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615-extensões do Intel software Guard (Intel SGX)
- CVE-2018-3620-sistemas operacionais (SO) e modo de gerenciamento do sistema (SMM)
- CVE-2018-3646 – afeta Virtual Machine Manager (VMM)

[Amostragem de dados](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013)de microarquitetura: 
- CVE-2019-11091-microarquitetura dados de amostragem de memória não cache (MDSUM)
- CVE-2018-12126-MSBDS (amostragem de dados de buffer de armazenamento de microarquitetura)
- CVE-2018-12127-microarquitetura carregar dados de porta (MLPDS)
- CVE-2018-12130-microarquitetura de amostragem de dados de buffer de preenchimento (MFBDS)









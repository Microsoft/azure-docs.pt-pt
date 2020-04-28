---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73935895"
---
**Última atualização**do documento : 12 de novembro de 2019 10:00 AM PST.

A divulgação de uma [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) conhecidas como ataques de canais laterais de execução especulativa resultou em perguntas de clientes que procuram mais clareza.  

A Microsoft implementou mitigações em todos os nossos serviços na nuvem. A infraestrutura que gere o Azure e isola as cargas de trabalho dos clientes uns dos outros está protegida. Isto significa que um potencial intruso usando a mesma infraestrutura não pode atacar a sua aplicação usando estas vulnerabilidades.

O Azure está a utilizar a manutenção de [conservação](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) da memória sempre que possível, para minimizar o impacto do cliente e eliminar a necessidade de reinicialização. O Azure continuará a utilizar estes métodos ao fazer atualizações em todo o sistema para o anfitrião e proteger os nossos clientes.

Mais informações sobre como a segurança é integrada em todos os aspetos do Azure estão disponíveis no site de Documentação de [Segurança do Azure.](https://docs.microsoft.com/azure/security/) 

> [!NOTE] 
> Desde que este documento foi publicado pela primeira vez, várias variantes desta classe de vulnerabilidade foram divulgadas. A Microsoft continua a investir fortemente na proteção dos nossos clientes e na prestação de orientação. Esta página será atualizada à medida que continuarmos a lançar novas correções. 
> 
> Em 12 de novembro de 2019, a [Intel publicou](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) um consultor técnico em torno da Intel® Extensões de Sincronização Transacional (Intel® TSX) Vulnerabilidade de Transações Asincronizadas (TAA) que é atribuída [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135). Esta vulnerabilidade afeta processadores Intel® Core® e processadores® ® Intel Xeon.  O Microsoft Azure lançou atualizações do sistema operativo e está a implementar um novo microcódigo, uma vez que é disponibilizado pela Intel, em toda a nossa frota para proteger os nossos clientes contra estas novas vulnerabilidades.   O Azure está a trabalhar de perto com a Intel para testar e validar o novo microcódigo antes do seu lançamento oficial na plataforma. 
>
> **Os clientes que estão** a executar código sem confiança dentro do seu VM precisam de tomar medidas para proteger contra estas vulnerabilidades, lendo abaixo para obter orientações adicionais sobre todas as vulnerabilidades especulativas de canais laterais de execução (Microsoft Advisories ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018), e [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Outros clientes devem avaliar estas vulnerabilidades a partir de uma perspetiva de Defesa em Profundidade e considerar as implicações de segurança e desempenho da sua configuração escolhida.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Manter os seus sistemas operativos atualizados

Embora não seja necessária uma atualização de OS para isolar as suas aplicações em funcionamento no Azure de outros clientes do Azure, é sempre uma boa prática manter o seu software atualizado. Os mais recentes Rollups de Segurança para Windows contêm atenuações para várias vulnerabilidades especulativas do canal de execução. Da mesma forma, as distribuições do Linux lançaram várias atualizações para resolver estas vulnerabilidades. Aqui estão as nossas ações recomendadas para atualizar o seu sistema operativo:

| Oferta | Ação Recomendada  |
|----------|---------------------|
| Cloud Services do Azure  | Ative a [atualização automática](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) ou certifique-se de que está a executar o mais recente Sistema operativo convidado. |
| Máquinas virtuais Azure Linux | Instale atualizações do seu fornecedor de sistema operativo. Para mais informações, consulte [linux](#linux) mais tarde neste documento. |
| Máquinas virtuais Do Windows Azure  | Instale o último roll-up de segurança.
| Outros Serviços Azure PaaS | Não são necessárias medidas para os clientes que utilizam estes serviços. O Azure mantém automaticamente as versões DE SO atualizadas. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Orientação adicional se estiver a executar código não confiável 

Os clientes que permitem que utilizadores não fidedignos executem códigoar arbitrário podem querer implementar algumas funcionalidades de segurança adicionais dentro das suas Máquinas Virtuais Azure ou Serviços Cloud. Estas características protegem contra os vetores de divulgação intra-processo que várias vulnerabilidades especulativas de execução descrevem.

Cenários de exemplo em que são recomendadas funcionalidades de segurança adicionais:

- Permite que o código em que não confia seja executado dentro do seu VM.  
    - *Por exemplo, permite que um dos seus clientes carregue um binário ou script que executa dentro*da sua aplicação . 
- Permite que os utilizadores em quem não confie acedam ao seu VM utilizando contas privilegiadas baixas.   
    - *Por exemplo, permite que um utilizador de baixo privilégio entre num dos seus VMs utilizando desktop remoto ou SSH*.  
- Permite aos utilizadores não confiáveis o acesso a máquinas virtuais implementadas através da virtualização aninhada.  
    - *Por exemplo, controla o hospedeiro Hyper-V, mas aloque os VMs a utilizadores não confiáveis*. 

Os clientes que não implementam um cenário que envolva código não confiável não precisam de ativar estas funcionalidades de segurança adicionais. 

## <a name="enabling-additional-security"></a>Permitindo segurança adicional 

Pode ativar funcionalidades de segurança adicionais dentro do seu VM ou Cloud Service se estiver a executar código não confiável. Paralelamente, certifique-se de que o seu sistema operativo está atualizado para ativar funcionalidades de segurança dentro do seu VM ou Cloud Service

### <a name="windows"></a>Windows 

O seu sistema operativo alvo deve estar atualizado para ativar estas funcionalidades de segurança adicionais. Embora numerosas mitigações especulativas de canais laterais de execução sejam ativadas por padrão, as funcionalidades adicionais descritas aqui devem ser ativadas manualmente e podem causar um impacto no desempenho. 


**Passo 1: Desative a hiper-rosca ção no VM** - Os clientes que executam código sem confiança num VM hiper-roscado terão de desativar hiper-threading ou mover-se para um tamanho VM não hiper-roscado. [Refira-se a este doc](https://docs.microsoft.com/azure/virtual-machines/windows/acu) para uma lista de tamanhos vm hiper-roscados (onde a relação entre vCPU e Core é de 2:1). Para verificar se o seu VM tem hiper-rosca ativado, consulte o script abaixo utilizando a linha de comando Windows a partir do VM.

Digite `wmic` para introduzir a interface interativa. Em seguida, digite o abaixo para ver a quantidade de processadores físicos e lógicos no VM.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Se o número de processadores lógicos for maior do que os processadores físicos (núcleos), então o hiper-roscaestá ativado.  Se estiver a executar um VM hiper-roscado, [contacte o Suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter hiper-threading desativado.  Uma vez desativada a hiper-rosca, o suporte necessitará de **uma reinicialização vm completa**. Por favor, consulte a [contagem de Núcleo](#core-count) para entender por que a contagem de núcleos vM diminuiu.


**Passo 2**: Paralelamente ao Passo 1, siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para verificar se as proteções estão ativadas utilizando o módulo [PowerShell De Controlo de Especulação.](https://aka.ms/SpeculationControlPS)

> [!NOTE]
> Se já descarregou este módulo, terá de instalar a versão mais recente.
>


A saída do script PowerShell deve ter os valores abaixo para validar as proteções ativadas contra estas vulnerabilidades:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Se a `MDS mitigation is enabled: False`saída aparecer, contacte o [Suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter opções de mitigação disponíveis.



**Passo 3**: Para ativar o suporte de OS de Shadowing de Endereços Virtuais kernel (KVAS) e de Injeção `Session Manager` de Alvo de Ramificação (BTI), siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para permitir proteções utilizando as teclas de registo. É necessário reiniciar.


**Passo 4**: Para as implementações que utilizam [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (apenas D3 e E3): Estas instruções aplicam-se dentro do VM que está a utilizar como hospedeiro Hyper-V.

1.  Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para `MinVmVersionForCpuBasedMitigations` permitir proteções utilizando as teclas de registo.
2.  Delineie o tipo `Core` de programador hipervisor seguindo as instruções [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Permitir o conjunto de funcionalidades de segurança adicionais no seu interior requer que o sistema operativo alvo esteja totalmente atualizado. Algumas mitigações serão ativadas por defeito. A secção seguinte descreve as funcionalidades que estão desligadas por defeito e/ou dependentes do suporte de hardware (microcódigo). Ativar estas funcionalidades pode causar um impacto no desempenho. Consulte a documentação do seu fornecedor de sistema operativo para mais instruções


**Passo 1: Desative a hiper-rosca ção no VM** - Os clientes que executam código sem confiança num VM hiper-roscado terão de desativar hiper-threading ou mover-se para um VM não-roscado.  [Refira-se a este doc](https://docs.microsoft.com/azure/virtual-machines/linux/acu) para uma lista de tamanhos vm hiper-roscados (onde a relação entre vCPU e Core é de 2:1). Para verificar se está a executar um VM `lscpu` hiper-roscado, execute o comando no VM Linux. 

Se, `Thread(s) per core = 2`então a hiper-rosca tiver sido ativada. 

Se, `Thread(s) per core = 1`então a hiper-rosca tiver sido desativada. 

 
Saída de amostra para um VM com hiper-rosca ativada: 

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

Se estiver a executar um VM hiper-roscado, [contacte o Suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter hiper-threading desativado.  Uma vez desativada a hiper-rosca, o suporte necessitará de **uma reinicialização vm completa**. Por favor, consulte a [contagem de Núcleo](#core-count) para entender por que a contagem de núcleos vM diminuiu.



**Passo 2**: Para atenuar qualquer uma das vulnerabilidades do canal lateral de execução especulativa abaixo, consulte a documentação do seu fornecedor de sistema operativo:   
 
- [Redhat e CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Contagem de núcleos

Quando um VM hiper-roscado é criado, O Azure aloca 2 fios por núcleo - estes são chamados vCPUs. Quando a hiper-rosca é desativada, o Azure remove um fio e aparece em núcleos roscados únicos (núcleos físicos). O rácio de vCPU para CPU é de 2:1, por isso, uma vez que a hiper-rosca é desativada, a contagem de CPU no VM parece ter diminuído para metade. Por exemplo, um D8_v3 VM é um VM hiper-roscado em 8 vCPUs (2 fios por núcleo x 4 núcleos).  Quando a hiper-rosca é desativada, os CPUs cairão para 4 núcleos físicos com 1 fio por núcleo. 

## <a name="next-steps"></a>Passos seguintes

Este artigo fornece orientações para os ataques de canais laterais de execução especulativa abaixo que afetam muitos processadores modernos:

[Fusão de espectro:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002)
- CVE-2017-5715 - Injeção de alvo de ramo (BTI)  
- CVE-2017-5754 - Isolamento da mesa de página kernel (KPTI)
- CVE-2018-3639 – Bypass da Loja Especulativa (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows Kernel Information – variante da Variante Spectre 1
 
[Falha terminal L1 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Extensões de Guarda de Software Intel (Intel SGX)
- CVE-2018-3620 - Sistemas operativos (OS) e Modo de Gestão de Sistemas (SMM)
- CVE-2018-3646 – impacta o Gestor de Máquinas Virtuais (VMM)

[Amostragem de dados microarquitetónicos:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013) 
- CVE-2019-11091 - Amostragem de dados microarquitetónicos Memória Não Cachepável (MDSUM)
- CVE-2018-12126 - Amostragem de dados tampão da loja microarquitetónica (MSBDS)
- CVE-2018-12127 - Amostragem de dados da porta de carga microarquitetónica (MLPDS)
- CVE-2018-12130 - Amostragem de dados do tampão de enchimento microarquitetónico (MFBDS)

Extensões de sincronização transacional (Intel® TSX) Abortar assíncronos transacionais:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX Transaction Asynchronous Abort (TAA)









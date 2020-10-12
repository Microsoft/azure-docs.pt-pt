---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73935895"
---
**Última atualização do documento**: 12 de novembro de 2019 10:00 PST.

A divulgação de uma [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) conhecidas como ataques de canais laterais de execução especulativa resultou em questões de clientes que procuram mais clareza.  

A Microsoft implementou mitigações em todos os nossos serviços na nuvem. A infraestrutura que gere o Azure e isola as cargas de trabalho dos clientes uns dos outros está protegida. Isto significa que um potencial intruso que usa a mesma infraestrutura não pode atacar a sua aplicação usando estas vulnerabilidades.

O Azure está a utilizar [a manutenção de preservação da memória](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) sempre que possível, para minimizar o impacto do cliente e eliminar a necessidade de reinicialização. O Azure continuará a utilizar estes métodos ao fazer atualizações a nível do sistema para o anfitrião e proteger os nossos clientes.

Mais informações sobre como a segurança é integrada em todos os aspetos do Azure estão disponíveis no site da [Documentação de Segurança Azure.](https://docs.microsoft.com/azure/security/) 

> [!NOTE] 
> Desde que este documento foi publicado pela primeira vez, várias variantes desta classe de vulnerabilidade foram divulgadas. A Microsoft continua a investir fortemente na proteção dos nossos clientes e na prestação de orientação. Esta página será atualizada à medida que continuarmos a lançar correções adicionais. 
> 
> Em 12 de novembro de 2019, [a Intel publicou](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) um aviso técnico em torno da vulnerabilidade de Transação Intel® Transactional Synchronization Extensions (Intel® TSX) Que é atribuída à vulnerabilidade [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135). Esta vulnerabilidade afeta os processadores ®® ® Intel Core e os processadores ® Xeon ® Intel.  A Microsoft Azure lançou atualizações do sistema operativo e está a implementar um novo microcódigo, uma vez que é disponibilizado pela Intel, em toda a nossa frota para proteger os nossos clientes contra estas novas vulnerabilidades.   O Azure está a trabalhar de perto com a Intel para testar e validar o novo microcódigo antes do seu lançamento oficial na plataforma. 
>
> **Os clientes que estão a executar códigos não fidedidos dentro** dos seus VM precisam de tomar medidas para proteger contra estas vulnerabilidades, lendo abaixo para obter orientações adicionais sobre todas as vulnerabilidades de execução especulativa de canais laterais (Microsoft Advisories ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)e [190013).](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)
>
> Outros clientes devem avaliar estas vulnerabilidades a partir de uma perspetiva de Defesa em Profundidade e considerar as implicações de segurança e desempenho da sua configuração escolhida.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Manter os seus sistemas operativos atualizados

Embora uma atualização de SO não seja necessária para isolar as suas aplicações em execução no Azure de outros clientes da Azure, é sempre uma boa prática manter o seu software atualizado. Os mais recentes Rollups de Segurança para Windows contêm mitigações para várias vulnerabilidades de canais laterais de execução especulativa. Da mesma forma, as distribuições do Linux lançaram várias atualizações para resolver estas vulnerabilidades. Estas são as nossas ações recomendadas para atualizar o seu sistema operativo:

| Oferta | Ação Recomendada  |
|----------|---------------------|
| Cloud Services do Azure  | Ative [a atualização automática](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) ou certifique-se de que está a executar o mais recente Sistema operativo convidado. |
| Máquinas virtuais Azure Linux | Instale atualizações do seu fornecedor do sistema operativo. Para mais informações, consulte [Linux](#linux) mais tarde neste documento. |
| Máquinas virtuais Azure Windows  | Instale o último rollup de segurança.
| Outros Serviços Azure PaaS | Não é necessária qualquer ação para os clientes que utilizam estes serviços. O Azure mantém automaticamente as versões de SO atualizadas. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Orientação adicional se estiver a executar código não fidedidido 

Os clientes que permitem que utilizadores não fidedidos executem código arbitrário podem desejar implementar algumas funcionalidades de segurança adicionais dentro das suas Máquinas Virtuais Azure ou Serviços cloud. Estas características protegem contra os vetores de divulgação intra-processo que várias vulnerabilidades de execução especulativa descrevem.

Cenários de exemplo onde são recomendadas funcionalidades de segurança adicionais:

- Permite que o código em que não confia seja executado dentro do seu VM.  
    - *Por exemplo, permite que um dos seus clientes carre deixe de carregar um binário ou script que executa dentro da sua aplicação.* 
- Permite que os utilizadores em que não confia entrem no seu VM utilizando contas privilegiadas baixas.   
    - *Por exemplo, permite que um utilizador com poucos privilégios inicie sessão num dos seus VMs utilizando um ambiente de trabalho remoto ou SSH*.  
- Permite que os utilizadores não fidedqui os acessos a máquinas virtuais implementadas através da virtualização aninhada.  
    - *Por exemplo, controla o anfitrião Hiper-V, mas aloca os VMs a utilizadores não fidebólicos*. 

Os clientes que não implementam um cenário que envolva código falso não necessitam de ativar estas funcionalidades de segurança adicionais. 

## <a name="enabling-additional-security"></a>Habilitando segurança adicional 

Pode ativar funcionalidades de segurança adicionais dentro do seu VM ou Cloud Service se estiver a executar código não fidedicordo. Paralelamente, certifique-se de que o seu sistema operativo está atualizado para permitir funcionalidades de segurança dentro do seu VM ou Cloud Service

### <a name="windows"></a>Windows 

O seu sistema operativo-alvo deve estar atualizado para permitir estas funcionalidades de segurança adicionais. Embora numerosas mitigações de canais laterais de execução especulativa sejam ativadas por padrão, as funcionalidades adicionais descritas aqui devem ser ativadas manualmente e podem causar um impacto no desempenho. 


**Passo 1: Desativar a hiper rosca no VM** - Os clientes que executam códigos não fidedicordos num VM hiper roscado terão de desativar a hiper rosca ou passar para um tamanho VM não hiper-roscado. Consulte [este doc](https://docs.microsoft.com/azure/virtual-machines/windows/acu) para uma lista de tamanhos VM hiper roscados (onde a razão entre vCPU e Core é de 2:1). Para verificar se o seu VM tem hiper rosca ativada, consulte o script abaixo utilizando a linha de comando do Windows a partir do VM.

Escreva `wmic` para entrar na interface interativa. Em seguida, digite o seguinte para ver a quantidade de processadores físicos e lógicos no VM.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Se o número de processadores lógicos for maior do que os processadores físicos (núcleos), então a hiper rosca está ativada.  Se estiver a executar um VM hiper roscado, [contacte o Suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter um desactivamento hiper-roscado.  Uma vez desativado o hiper rosco, **o suporte requer um reboot completo do VM**. Consulte a [contagem de núcleo](#core-count) para entender por que a sua contagem de núcleos VM diminuiu.


**Passo 2**: Paralelamente ao passo 1, siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para verificar se as proteções estão ativadas utilizando o módulo [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell.

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

Se a saída aparecer, contacte o `MDS mitigation is enabled: False` [Suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter as opções de mitigação disponíveis.



**Passo 3**: Para ativar o suporte de sombra de endereço virtual kernel (KVAS) e de injeção de alvo de ramo (BTI), siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para permitir proteções utilizando as chaves de `Session Manager` registo. É necessário reiniciar.


**Passo 4**: Para implementações que estejam a utilizar [a virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (apenas D3 e E3): Estas instruções aplicam-se no interior do VM que está a utilizar como hospedeiro Hiper-V.

1.  Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para permitir proteções utilizando as `MinVmVersionForCpuBasedMitigations` chaves de registo.
2.  Desave o tipo de programador de hipervisores seguindo `Core` as instruções [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Permitir o conjunto de funcionalidades de segurança adicionais no interior requer que o sistema operativo-alvo esteja totalmente atualizado. Algumas mitigações serão ativadas por defeito. A secção seguinte descreve as funcionalidades que estão desligadas por predefinição e/ou dependentes do suporte de hardware (microcódigo). Ativar estas funcionalidades pode causar um impacto no desempenho. Consulte a documentação do seu fornecedor do sistema operativo para mais instruções


**Passo 1: Desativar a hiper rosca no VM** - Os clientes que executam código não fidedicordos num VM hiper roscado terão de desativar a hiper rosca ou passar para um VM não hiper roscado.  Consulte [este doc](https://docs.microsoft.com/azure/virtual-machines/linux/acu) para uma lista de tamanhos VM hiper roscados (onde a razão entre vCPU e Core é de 2:1). Para verificar se está a executar um VM hiper-roscado, verifique o `lscpu` comando no Linux VM. 

Se, `Thread(s) per core = 2` então, a hiper rosca tiver sido ativada. 

Se, `Thread(s) per core = 1` então, a hiper rosca tiver sido desativada. 

 
Saída de amostra para um VM com hiper rosca ativada: 

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

Se estiver a executar um VM hiper roscado, [contacte o Suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter um desactivamento hiper-roscado.  Uma vez desativado o hiper rosco, **o suporte requer um reboot completo do VM**. Consulte a [contagem de núcleo](#core-count) para entender por que a sua contagem de núcleos VM diminuiu.



**Passo 2**: Para atenuar qualquer uma das vulnerabilidades de execução lateral abaixo, consulte a documentação do seu fornecedor do sistema operativo:   
 
- [Redhat e CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Contagem de núcleos

Quando um VM hiper roscado é criado, Azure atribui 2 fios por núcleo - estes são chamados vCPUs. Quando a hiper rosca é desativada, o Azure remove um fio e coloca as superfícies em núcleos roscados únicos (núcleos físicos). A razão de vCPU para CPU é de 2:1, pelo que uma vez que o hiper-rosco é desativado, a contagem de CPU no VM parece ter diminuído para metade. Por exemplo, um VM D8_v3 é um VM hiper roscado que funciona em 8 vCPUs (2 fios por núcleo x 4 núcleos).  Quando a hiper rosca é desativada, os CPUs descerão para 4 núcleos físicos com 1 fio por núcleo. 

## <a name="next-steps"></a>Passos seguintes

Este artigo fornece orientações para os ataques de canal laterais de execução abaixo que afetam muitos processadores modernos:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - Injeção de alvo de ramo (BTI)  
- CVE-2017-5754 - Kernel Page Table Table Isolation (KPTI)
- CVE-2018-3639 – Bypass da Loja Especulativa (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Informação do Kernel do Windows – variante da Variante 1 do Espectro
 
[Falha terminal L1 (L1TF):](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018)
- CVE-2018-3615 - Extensões da Intel Software Guard (Intel SGX)
- CVE-2018-3620 - Sistemas Operativos (SISTEMAS) e Modo de Gestão do Sistema (SMM)
- CVE-2018-3646 – Impacts Virtual Machine Manager (VMM)

[Amostragem de dados microarquites:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013) 
- CVE-2019-11091 - Microarchitectural Data Sampling Uncacheable Memory (MDSUM)
- CVE-2018-12126 - Amostra de Dados de Buffer da Loja Microarchitectural (MSBDS)
- CVE-2018-12127 - Amostragem de Dados de Porta de Carga Microarchitetectural (MLPDS)
- CVE-2018-12130 - Amostragem de dados de tampão de enchimento microarchitetectural (MFBDS)

Extensões de sincronização transacional (Intel® TSX) Transação Assíncronea Abortar:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) - Transação TSX Assíncrona Abortar (TAA)









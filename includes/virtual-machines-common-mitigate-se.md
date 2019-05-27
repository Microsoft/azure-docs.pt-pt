---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/22/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: d2312fac64515756f5ed2e0feb22fdc6b7205376
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125174"
---
**Última atualização de documentos**: 14 de Maio de 2019 10 4:00 PST.

A divulgação de um [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) também conhecida como ataques de canal de lado a execução especulativa resultou em perguntas dos clientes que procuram mais clareza.  

A Microsoft implantou o atenuações em todos os nossos serviços cloud. A infraestrutura que executa o Azure e isola as cargas de trabalho do cliente uns dos outros está protegida. Isso significa que um potencial atacante terá usando a mesma infraestrutura não é possível atacar o seu aplicativo usando estas vulnerabilidades.

Está a utilizar o Azure [memória preservação da manutenção](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot) sempre que possível, para minimizar o impacto de cliente e elimina a necessidade de reinicializações. O Azure irá continuar a utilizar esses métodos quando efetuar atualizações em todo o sistema para o anfitrião e proteger os nossos clientes.

Obter mais informações sobre como a segurança integra todos os aspectos do Azure estão disponíveis na [documentação de segurança do Azure](https://docs.microsoft.com/azure/security/) site. 

> [!NOTE] 
> Uma vez que este documento foi publicado pela primeira vez, vários variantes desta classe de vulnerabilidade tem sido divulgadas. A Microsoft continua a ser muito investido em proteger nossos clientes e fornecer orientação. Esta página será atualizada à medida que Continuamos a liberar correções adicionais. 
> 
> 14 de Maio de 2019 [Intel divulgada](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) um novo conjunto de vulnerabilidade de canal de lado a execução especulativa conhecido como Microarchitectural amostragem de dados (MDS consulte o guia de segurança da Microsoft [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), que foi atribuída a várias relacionadas: 
> - CVE-2019-11091 - dados Microarchitectural amostragem atribuir memória (MDSUM)
> - CVE-2018-12126 - Store Microarchitectural dados na memória intermédia de amostragem (MSBDS) 
> - CVE-2018-12127 - dados de porta de carga Microarchitectural amostragem (MLPDS)
> - CVE-2018-12130 - dados de memória intermédia de preenchimento de Microarchitectural amostragem (MFBDS)
>
> Este vulnerabilidade afeta processadores Intel® Core® e processadores Intel® Xeon®.  Microsoft Azure lançou a atualizações do sistema operativo e está a implementar ativação do microcódigo novos, como é disponibilizada pela Intel, em toda a nossa frota para proteger os nossos clientes em relação a estas novas vulnerabilidades.   Azure está a trabalhar de perto com Intel para testar e validar a ativação do microcódigo novo antes de seu lançamento oficial na plataforma. 
>
> **Os clientes que estejam a executar não confiáveis de código dentro de suas VM** precisa de tomar a ação para proteger contra estas vulnerabilidades ler o artigo abaixo para obter orientações adicionais sobre todas as vulnerabilidades de side-channel de execução especulativa (Microsoft Consultorias AVD [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018), e [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Outros clientes devem avaliar essas vulnerabilidades de uma linha de defesa na perspetiva de profundidade e considerar as implicações de segurança e desempenho da sua configuração escolhida.



## <a name="keeping-your-operating-systems-up-to-date"></a>Manter seus sistemas de operativos atualizadas

Embora seja uma atualização de SO não é necessário para isolar as aplicações em execução no Azure a partir de outros clientes do Azure, é sempre uma prática recomendada manter o software atualizado. O mais recente Rollups para o Windows do segurança conter atenuações para várias vulnerabilidades de canal de lado a execução especulativa. Da mesma forma, as distribuições de Linux lançaram várias atualizações para resolver estas vulnerabilidades. Aqui estão nossas ações recomendadas para atualizar o sistema operativo:

| Oferta | Ação Recomendada  |
|----------|---------------------|
| Serviços Cloud do Azure  | Ativar [atualização automática](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) ou certifique-se de que está a executar o SO convidado mais recente. |
| Máquinas Virtuais do Azure para Linux | Instale atualizações a partir do seu fornecedor do sistema operativo. Para obter mais informações, consulte [Linux](#linux) mais adiante neste documento. |
| Máquinas virtuais do Windows Azure  | Instale o rollup de segurança mais recente.
| Outros serviços PaaS do Azure | Não existe nenhuma ação necessária para clientes que utilizam estes serviços. Azure mantém automaticamente as versões de SO atualizado. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Orientações adicionais, se estiver a executar o código não confiável 

Os clientes que permitir que os utilizadores não fidedignos executar código arbitrário, podem querer implementar alguns recursos de segurança adicionais dentro de suas máquinas virtuais do Azure ou serviços Cloud. Estas funcionalidades de proteção contra os vetores de divulgação flui processos que descrevem várias vulnerabilidades de execução especulativa.

Cenários de exemplo em que os recursos de segurança adicionais são recomendados:

- Permitir que código não seja fidedigna para ser executado dentro de sua VM.  
    - *Por exemplo, permitir que um dos seus clientes para carregar um binário ou de script que, em seguida, executar na sua aplicação*. 
- Permitir que os utilizadores que não seja fidedigna para iniciar sessão na sua VM usando contas com privilégios baixas.   
    - *Por exemplo, permitir que um utilizador de baixo privilégio iniciar sessão em uma das suas VMs através de RDP ou SSH*.  
- Permitir acesso às máquinas virtuais implementadas por meio da Virtualização aninhada a usuários não confiáveis.  
    - *Por exemplo, controlar o anfitrião de Hyper-V, mas atribuir as VMs a usuários não confiáveis*. 

Os clientes que não implementam um cenário que envolvem o código não confiável não é necessário ativar estas funcionalidades de segurança adicional. 

## <a name="enabling-additional-security"></a>Ativar segurança adicional 

Se estiver a executar o código não confiável, pode habilitar recursos de segurança adicionais dentro de sua VM ou serviço em nuvem. Em paralelo, certifique-se de que seu sistema operativo está atualizado para habilitar os recursos de segurança dentro da sua VM ou serviço em nuvem

### <a name="windows"></a>Windows 

O sistema operacional de destino tem de ser atualizado para ativar estas funcionalidades de segurança adicional. Embora inúmeras atenuações de canal de lado a execução especulativa estão ativadas por predefinição, as funcionalidades adicionais descritas aqui devem ser habilitadas manualmente e podem causar um impacto no desempenho. 


**Passo 1: Desativar o hyperthreading na VM** - clientes que executam o código não confiável numa VM tem de desativar o hyperthreading ou mover para um tamanho de VM não threading de threading. Para verificar se a VM tem o hyperthreading ativado, consulte o script com a linha de comandos do Windows de dentro da VM abaixo.

Tipo de `wmic` para introduzir a interface interativa. Em seguida, escreva abaixo para ver a quantidade de física e lógica processadores na VM.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Se o número de processadores lógicos for maior do que processadores físicos (núcleos), em seguida, o hyperthreading está ativado.  Se estiver a executar uma VM de threading, [contacte o suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter o hyperthreading desabilitado.  Assim que o hyperthreading estiver desativada, **suporte irá exigir um reinício total da VM**. 


**Passo 2**: Em paralelo para o passo 1, siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para verificar as proteções estão ativadas utilizando o [SpeculationControl](https://aka.ms/SpeculationControlPS) módulo do PowerShell.

> [!NOTE]
> Se tiver transferido anteriormente este módulo, terá de instalar a versão mais recente.
>


A saída do script do PowerShell deve ter os valores para validar abaixo ativada proteções contra estas vulnerabilidades:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Se o resultado mostra `MDS mitigation is enabled: False`, inicie [contacte o suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para as opções de atenuação disponíveis.



**Passo 3**: Para ativar o suporte de Injeção de destino da ramificação (BTI) sistema operacional e de Kernel Virtual endereço sombreamento (KVAS), siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para ativar proteções usando o `Session Manager` chaves do Registro. É necessário um reinício.


**Passo 4**: Para implementações que estão a utilizar [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 e apenas E3): Estas instruções aplicam-se dentro da VM estiver a utilizar como um anfitrião Hyper-V.

1.  Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para ativar proteções usando o `MinVmVersionForCpuBasedMitigations` chaves do Registro.
2.  Defina o tipo de scheduler de hipervisor para `Core` ao seguir as instruções [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Ativar o conjunto de recursos de segurança adicionais dentro requer que o sistema operacional de destino esteja totalmente atualizada. Algumas mitigações serão ativadas por predefinição. A secção seguinte descreve as funcionalidades que estão desativadas por predefinição e/ou baseia-se no suporte de hardware (ativação do microcódigo). Ativar estas funcionalidades, pode causar um impacto no desempenho. Documentação do seu fornecedor de sistema operativo para obter mais instruções de referência


**Passo 1: Desativar o hyperthreading na VM** - clientes que executam o código não confiável numa VM tem de desativar o hyperthreading ou mover para uma VM não threading de threading.  Para verificar se estiver a executar uma VM de threading, execute o `lscpu` comando na VM do Linux. 

Se `Thread(s) per core = 2`, em seguida, o hyperthreading tiver sido ativada. 

Se `Thread(s) per core = 1`, em seguida, o hyperthreading foi desativada. 

 
Exemplo de saída para uma VM com hyperthreading ativado: 

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

Se estiver a executar uma VM de threading, [contacte o suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter o hyperthreading desabilitado.  Assim que o hyperthreading estiver desativada, **suporte irá exigir um reinício total da VM**.


**Passo 2**: Para atenuar os efeitos de qualquer um do abaixo vulnerabilidades side-channel de execução especulativa, consulte a documentação do seu fornecedor de sistema operativo:   
 
- [VM de Redhat e CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 

## <a name="next-steps"></a>Passos Seguintes

Este artigo fornece orientações para o abaixo ataques de canal de lado a execução especulativa que afetam vários processadores modernos:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - ramo Injeção de destino (BTI)  
- CVE-2017-5754 - isolamento de tabela de página de Kernel (KPTI)
- CVE-2018-3639 – Store especulativa ignorar (KPTI) 
 
[L1 Falha de Terminal (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - extensões de proteção de Software do Intel (Intel SGX)
- CVE-2018-3620 - operar sistemas (SO) e o modo de gestão do sistema (SMM)
- Tem impacto sobre a CVE-2018-3646 – Virtual Machine Manager (VMM)

[Amostragem de dados de Microarchitectural](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - dados Microarchitectural amostragem atribuir memória (MDSUM)
- CVE-2018-12126 - Store Microarchitectural dados na memória intermédia de amostragem (MSBDS)
- CVE-2018-12127 - dados de porta de carga Microarchitectural amostragem (MLPDS)
- CVE-2018-12130 - dados de memória intermédia de preenchimento de Microarchitectural amostragem (MFBDS)









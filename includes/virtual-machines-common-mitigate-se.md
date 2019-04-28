---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: cbd86571cbdcd600ef3acdea3833568a34657931
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337960"
---
**Última atualização de documentos**: 14 de Agosto de 2018 10 4:00 PST.

A divulgação de um [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) também conhecida como ataques de canal de lado a execução especulativa resultou em perguntas dos clientes que procuram mais clareza.  

A Microsoft implantou o atenuações em todos os nossos serviços cloud. A infraestrutura que executa o Azure e isola as cargas de trabalho do cliente uns dos outros está protegida. Isso significa que um potencial atacante terá usando a mesma infraestrutura não é possível atacar o seu aplicativo usando estas vulnerabilidades.

Está a utilizar o Azure [memória preservação da manutenção](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot) sempre que possível, para minimizar o impacto de cliente e elimina a necessidade de reinicializações. O Azure irá continuar a utilizar esses métodos quando efetuar atualizações em todo o sistema para o anfitrião e proteger os nossos clientes.

Obter mais informações sobre como a segurança integra todos os aspectos do Azure estão disponíveis na [documentação de segurança do Azure](https://docs.microsoft.com/azure/security/) site. 

> [!NOTE] 
> Uma vez que este documento foi publicado pela primeira vez, vários variantes desta classe de vulnerabilidade tem sido divulgadas. A Microsoft continua a ser muito investido em proteger nossos clientes e fornecer orientação. Esta página será atualizada à medida que Continuamos a liberar correções adicionais. 
> 
> 14 de Agosto de 2018, o setor divulgadas uma nova vulnerabilidade de canal de lado a execução especulativa conhecida como [L1 Terminal falhas](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (L1TF) que foi atribuído a várias relacionadas ([CVE-2018-3615, CVE-2018-3620, e CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)). Este vulnerabilidade afeta processadores Intel® Core® e processadores Intel® Xeon®. A Microsoft implantou o atenuações em nossos serviços cloud que reforçam o isolamento entre os clientes. Leia abaixo para obter orientações adicionais proteger contra L1TF e vulnerabilidades anteriores ([Spectre variante 2 CVE-2017-5715 e Meltdown variante 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)).
>  






## <a name="keeping-your-operating-systems-up-to-date"></a>Manter seus sistemas de operativos atualizadas

Embora seja uma atualização de SO não é necessário para isolar as aplicações em execução no Azure a partir de outros clientes do Azure, é sempre uma prática recomendada manter o software atualizado. O mais recente Rollups para o Windows do segurança conter atenuações para várias vulnerabilidades de canal de lado a execução especulativa. Da mesma forma, as distribuições de Linux lançaram várias atualizações para resolver estas vulnerabilidades. Aqui estão nossas ações recomendadas para atualizar o sistema operativo:

| Oferta | Ação Recomendada  |
|----------|---------------------|
| Cloud Services do Azure  | Ativar [atualização automática](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) ou certifique-se de que está a executar o SO convidado mais recente. |
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

Pode ativar funcionalidades de segurança adicionais dentro de sua VM ou serviço em nuvem.

### <a name="windows"></a>Windows 

O sistema operacional de destino tem de ser atualizado para ativar estas funcionalidades de segurança adicional. Embora inúmeras atenuações de canal de lado a execução especulativa estão ativadas por predefinição, as funcionalidades adicionais descritas aqui devem ser habilitadas manualmente e podem causar um impacto no desempenho. 

**Passo 1**: [Contacte o suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) expõem atualizado firmware (ativação do microcódigo) em suas máquinas virtuais. 

**Passo 2**: Ative o suporte de Kernel Virtual endereço sombreamento (KVAS) e o SO de Injeção de destino da ramificação (BTI). Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para ativar a proteção através do `Session Manager` chaves do Registro. É necessário um reinício. 

**Passo 3**: Para implementações que estão a utilizar [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 e apenas E3): Estas instruções aplicam-se dentro da VM estiver a utilizar como um anfitrião Hyper-V. 

1. Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para ativar a proteção através do `MinVmVersionForCpuBasedMitigations` chaves do Registro.  
 
1. Defina o tipo de scheduler de hipervisor para **Core** ao seguir as instruções [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types). 

**Passo 4**: Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para verificar as proteções estão ativadas utilizando o [SpeculationControl](https://aka.ms/SpeculationControlPS) módulo do PowerShell. 

> [!NOTE]
> Se tiver transferido anteriormente este módulo, terá de instalar a versão mais recente.
>

Todas as VMs devem mostrar:

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>Ativar o conjunto de recursos de segurança adicionais dentro requer que o sistema operacional de destino esteja totalmente atualizada. Algumas mitigações serão ativadas por predefinição. A secção seguinte descreve as funcionalidades que estão desativadas por predefinição e/ou baseia-se no suporte de hardware (ativação do microcódigo). Ativar estas funcionalidades, pode causar um impacto no desempenho. Documentação do seu fornecedor de sistema operativo para obter mais instruções de referência
 
**Passo 1**: [Contacte o suporte Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) expõem atualizado firmware (ativação do microcódigo) em suas máquinas virtuais.
 
**Passo 2**: Ative o suporte de Injeção de destino da ramificação (BTI) sistema operacional atenuar a CVE-2017-5715 (Spectre variante 2), seguindo a documentação do fornecedor do seu sistema operativo. 
 
**Passo 3**: Ative o Kernel página tabela isolamento (KPTI) para atenuar a CVE-2017-5754 (Meltdown variante 3), seguindo a documentação do fornecedor do seu sistema operativo. 
 
Obter mais informações estão disponíveis no fornecedor de seu sistema operativo:  
 
- [VM de Redhat e CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [SUSE](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte [os clientes da proteção do Azure da vulnerabilidade de CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).

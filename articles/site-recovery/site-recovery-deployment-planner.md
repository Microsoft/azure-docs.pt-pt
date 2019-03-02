---
title: Sobre o Azure Site Recovery Deployment Planner para recuperação após desastre de VMs de VMware para o Azure | Documentos da Microsoft
description: Saiba mais sobre o Azure Site Recovery Deployment Planner para recuperação após desastre de VMs de VMware para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mayg
ms.openlocfilehash: 55ccfd17a39a19bec01924e263d8f4892bd6101d
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216752"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Sobre o Azure Site Recovery Deployment Planner de VMware para o Azure
Este artigo é o manual do utilizador do Planeador de Implementações do Azure Site Recovery para implementações de produção de VMware para o Azure.

## <a name="overview"></a>Descrição geral

Antes de começar a proteger máquinas virtuais do VMware (VMs) com o Azure Site Recovery, tem de alocar largura de banda suficiente com base na sua taxa de alterações de dados diária, de modo a cumprir o objetivo de ponto de recuperação (RPO) pretendido. Certifique-se de que implementa o número certo de servidores de configuração e de servidores de processos no local.

Também tem de criar o tipo e o número certo de contas de Armazenamento do Azure de destino. Vai criar contas de armazenamento standard ou premium levando em linha de conta o crescimento dos servidores de produção de origem devido ao aumento da utilização ao longo do tempo. O tipo de armazenamento é escolhido por VM, com base nas características da carga de trabalho (por exemplo, operações de leitura / escrita / E/S por segundo [IOPS] ou alterações a dados) e nos limites do Site Recovery.

 O Planeador de Implementações do Site Recovery é uma ferramenta de linha de comandos para os cenários de recuperação após desastre Hyper-V para o Azure e VMware para o Azure. Pode utilizar esta ferramenta para criar remotamente o perfil das VMs de VMware (sem qualquer tipo de impacto na produção) para compreender os requisitos de largura de banda e de Armazenamento necessários para replicação e ativação pós-falha de teste bem-sucedidas. Pode executar a ferramenta sem instalar nenhum componente do Site Recovery no local. Para obter resultados de débito precisos, execute o planeador num Windows Server que cumpra os requisitos mínimos do servidor de configuração do Site Recovery que terá de, a determinada altura, implementar como um dos primeiros passos na implementação de produção.

A ferramenta disponibiliza os seguintes detalhes:

**Avaliação de compatibilidade**

* Avaliação de elegibilidade de VMs com base no número de discos, no tamanho do disco, em IOPS, na alteração a dados, no tipo de arranque (EFI/BIOS) e versão de SO

**Necessidade de largura de banda de rede vs avaliação de RPO**

* Largura de banda de rede estimada necessária para a replicação delta
* Débito que o Site Recovery consegue obter a partir do local para o Azure
* Número de VMs a colocar em lotes, com base na largura de banda estimada para concluir a replicação inicial dentro de um determinado período de tempo
* RPO que pode ser obtido para uma determinada largura de banda
* Impacto no RPO pretendido se for aprovisionada uma largura de banda mais reduzida

**Requisitos de infraestrutura do Azure**

* Requisito de tipo de armazenamento (conta armazenamento standard ou premium) para cada VM
* Número total de contas de armazenamento standard e premium a configurar para a replicação
* Sugestões de nomenclatura de contas de armazenamento, com base na documentação de orientação do Armazenamento
* Colocação da conta de armazenamento para todas as VMs
* Número de núcleos do Azure a configurar antes da ativação pós-falha ou reativação pós-falha de teste na subscrição
* Tamanho recomendado da VM do Azure para cada VM no local

**Requisitos de infraestrutura no local**
* Número necessário de servidores de configuração e de servidores de processos a implementar no local

**Custo de recuperação após desastre estimado para o Azure**
* Custo total estimado da recuperação após desastre para o Azure: custo de computação, armazenamento, rede e da licença do Site Recovery
* Análise detalhada do custo por VM


>[!IMPORTANT]
>
>Uma vez que a utilização é suscetível de aumentar ao longo do tempo, todos os cálculos da ferramenta anteriores são feitos presumindo um fator de crescimento de 30 por cento nas características das cargas de trabalho. Os cálculos também utilizam um valor de percentil 95 de todas as métricas de criação de perfis, como IOPS de leitura/escrita e abandono. Tanto o fator de crescimento, como o cálculo do percentil, são configuráveis. Para saber mais sobre o fator de crescimento, veja a secção "Considerações sobre o fator de crescimento". Para saber mais sobre o valor de percentil, veja a secção "Valor de percentil serve para o cálculo".
>

## <a name="support-matrix"></a>Matriz de suporte

| | **VMware para o Azure** |**Hyper-V para o Azure**|**Azure para o Azure**|**Hyper-V para um site secundário**|**VMware para um site secundário**
--|--|--|--|--|--
Cenários suportados |Sim|Sim|Não|Sim*|Não
Versão suportada | vCenter 6.5, 6.0 ou 5.5| Windows Server 2016, Windows Server 2012 R2 | ND |Windows Server 2016, Windows Server 2012 R2|ND
Configuração suportada|vCenter, ESXi| Cluster Hyper-V, anfitrião Hyper-V|ND|Cluster Hyper-V, anfitrião Hyper-V|ND|
Número de servidores para os quais podem ser criados perfis por instância de execução do Planeador de Implementações do Site Recovery |Único (é possível criar um perfil de cada vez de VMs pertencentes a um vCenter Server ou um servidor ESXi)|Vários (pode criar um perfil de cada vez de VMs em vários anfitriões ou clusters anfitriões)| ND |Vários (pode criar um perfil de cada vez de VMs em vários anfitriões ou clusters anfitriões)| ND

*A ferramenta destina-se principalmente ao cenário de recuperação após desastre Hyper-V para o Azure. Para a recuperação após desastre de Hyper-V para um site secundário, só pode ser utilizada para compreender recomendações do lado da origem, como a largura de banda de rede necessária, espaço de armazenamento livre necessário em cada um dos servidores Hyper-V de origem e definições de batches e números de criação de batches na replicação inicial. Ignore as recomendações e os custos do Azure do relatório. Além disso, a operação Obter Débito não é aplicável ao cenário de recuperação após desastre de Hyper-V para um site secundário.

## <a name="prerequisites"></a>Pré-requisitos
A ferramenta tem duas fases principais – a criação de perfis e a geração de relatórios. Também existe uma terceira opção, para calcular apenas o débito. Os requisitos para o servidor a partir do qual é iniciada a medição de criação de perfis e do débito são apresentados na tabela seguinte.

| Requisito do servidor | Descrição|
|---|---|
|Medição da criação de perfis e do débito| <ul><li>Sistema operativo: Windows Server 2016 ou Windows Server 2012 R2<br>(que corresponda idealmente, pelo menos, às [recomendações de tamanho do servidor de configuração](https://aka.ms/asr-v2a-on-prem-components))</li><li>Configuração da máquina: 8 vCPUs, 16 GB de RAM, 300 GB HDD</li><li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Visual C++ Redistributable para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Acesso à Internet para o Azure a partir deste servidor</li><li>Conta de armazenamento do Azure</li><li>Acesso de administrador no servidor</li><li>Mínimo de 100 GB de espaço livre no disco (presumindo mil VMs com uma média de três discos cada, com perfis criados para 30 dias)</li><li>Definições de nível de estatísticas do VMware vCenter podem ser o nível 1 ou superior</li><li>Permitir porta do vCenter (o valor predefinido 443): Site Recovery Deployment Planner utiliza esta porta para ligar para o vCenter server/anfitrião ESXi</ul></ul>|
| Geração de relatórios | Um Windows PC ou Windows Server com o Excel 2013 ou posterior.<li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Visual C++ Redistributable para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli) é necessário apenas quando passar - opção de utilizador no comando de geração de relatório para obter as últimas informações de configuração de VM das VMs. O Deployment Planner liga-se ao servidor do vCenter. Permitir vCenter porta da porta (predefinição 443) ligar ao vCenter server.</li>|
| Permissões de utilizador | Permissão só de leitura para a conta de utilizador utilizada para aceder ao VMware vCenter Server/anfitrião ESXi do VMware vSphere durante a criação do perfil |

> [!NOTE]
>
>A ferramenta só pode criar perfis para VMs com discos VMDK e RDM. Não pode criar perfis para VMs com discos iSCSI ou NFS. O Site Recovery suporta discos iSCSI e NFS para servidores do VMware. Contudo, uma vez que o planeador de implementações não está dentro do convidado e cria perfis apenas com os contadores de desempenho do vCenter, a ferramenta não tem visibilidade para estes tipos de discos.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Transferir e extrair a ferramenta Planeador de Implementações
1. Transfira a versão mais recente do [ Planeador de Implementações do Site Recovery](https://aka.ms/asr-deployment-planner).
A ferramenta está comprimida numa pasta .zip. A versão atual só suporta o cenário de VMware para o Azure.

2. Copie a pasta .zip para o Windows Server a partir do qual quer executar a ferramenta.
Pode executá-la no Windows Server 2012 R2 se o servidor tiver acesso à rede para ligar ao vCenter Server/anfitrião ESXi do vSphere que contém as VMs para as quais criar perfis. No entanto, recomendamos que execute num servidor cuja configuração de hardware cumpra as [orientações de tamanho do servidor de configuração](https://aka.ms/asr-v2a-on-prem-components). Se já tiver implementado componentes do Site Recovery no local, execute a ferramenta no servidor de configuração.

    Recomendamos que a configuração do hardware seja igual à do servidor de configuração (que tem um servidor de processos incorporado) no qual vai executar a ferramenta. Esta configuração garante que o débito obtido comunicado pela ferramenta corresponde ao débito real que o Site Recovery pode obter durante a replicação. O cálculo de débito depende da largura de banda de rede disponível na configuração do servidor e do hardware (como a CPU e o armazenamento) do servidor. Se executar a ferramenta a partir de qualquer outro servidor, o débito é calculado a partir desse servidor para o Azure. Além disso, uma vez que a configuração de hardware do servidor pode diferir da configuração do servidor de configuração, o débito obtido que a ferramenta comunica poderá estar incorreto.

3. Extraia a pasta .zip.
Esta contém vários ficheiros e sub-pastas. O ficheiro executável é ASRDeploymentPlanner.exe, na pasta principal.

    Exemplo: Copie o ficheiro .zip para a unidade E:\ e extraia-o.
    E:\ASR Deployment Planner_v2.3.zip

    E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="update-to-the-latest-version-of-deployment-planner"></a>Atualizar para a versão mais recente do Planeador de Implementações
Se tiver uma versão anterior do Planeador de Implementações, execute um dos seguintes procedimentos:
 * Se a versão mais recente não contiver uma correção de criação de perfis e a criação de perfis já estiver em curso na sua versão atual da ferramenta, continue com a mesma.
 * Se a versão mais recente contiver essa correção, recomendamos-lhe que pare a criação de perfis na versão atual e que a reinicie na nova.


 >[!NOTE]
 >
 >Quando iniciar a criação de perfis com a nova versão, transmita o mesmo caminho do diretório de saída, para que a ferramenta anexe os dados da criação de perfis nos ficheiros existentes. Para gerar o relatório, é utilizado um conjunto completo de dados de criação de perfis. Se transmitir um diretório de saída diferente, são criados ficheiros novos e os dados antigos não servem para gerar o relatório.
 >
 >Cada versão do Planeador de Implementações novo é uma atualização acumulativa do ficheiro .zip. Não tem de copiar os ficheiros mais recentes para a pasta anterior. Pode criar e utilizar uma pasta nova.


## <a name="version-history"></a>Histórico de versões
A versão mais recente da ferramenta Site Recovery Deployment Planner é 2.3.
Veja a página [Histórico de versões do Planeador de Implementações do Site Recovery](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) para obter as correções que foram adicionadas em cada atualização.

## <a name="next-steps"></a>Passos Seguintes
[Planeador de Implementações do Site Recovery](site-recovery-vmware-deployment-planner-run.md)

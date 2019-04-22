---
title: Sobre o Azure Site Recovery Deployment Planner para recuperação após desastre de VMs de Hyper-V para o Azure | Documentos da Microsoft
description: Saiba mais sobre a recuperação de desastre do Azure Site Recovery Deployment Planner Hyper-V para o Azure.
author: mayurigupta13
manager: garavd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 43431c401f13117af1f60d3affd284fc125be7eb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360268"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Sobre o Azure Site Recovery Deployment Planner para recuperação após desastre de Hyper-V para o Azure

Este artigo é o manual do utilizador do Planeador de Implementações do Azure Site Recovery para implementações de produção de Hyper-V para o Azure.

Antes de começar a proteger máquinas virtuais (VMs) de Hyper-V com o Site Recovery, tem de alocar largura de banda suficiente com base na sua taxa de alteração de dados diária, de modo a cumprir o Objetivo de Ponto de Recuperação (RPO) pretendido, e alocar espaço de armazenamento livre suficiente em cada volume de armazenamento de Hyper-V no local.

Também tem de criar o tipo e o número certo de contas de armazenamento do Azure de destino. Vai criar contas de armazenamento standard ou premium levando em linha de conta o crescimento dos servidores de produção de origem devido ao aumento da utilização ao longo do tempo. O tipo de armazenamento é escolhido por VM, com base nas características da carga de trabalho, por exemplo, operações de E/S de leitura/escrita por segundo (IOPS), ou alterações a dados, e limites do Azure Site Recovery. 

O Planeador de Implementações do Azure Site Recovery é uma ferramenta de linha de comandos para os cenários de recuperação após desastre Hyper-V para o Azure e VMware para o Azure. Pode utilizar esta ferramenta para criar remotamente o perfil das suas VMs de VMware presentes em vários anfitriões Hyper-V (sem qualquer tipo de impacto na produção) para compreender os requisitos de largura de banda e de armazenamento do Azure necessários para a replicação e ativação pós-falha de teste/ativação pós-falha bem-sucedidas. Pode executar a ferramenta sem instalar nenhum componente do Azure Site Recovery no local. No entanto, para obter resultados precisos de débito alcançado, recomendamos que execute o planeador num Windows Server que tenha a mesma configuração de hardware do que um dos servidores Hyper-V que irá utilizar para ativar a proteção de recuperação após desastre para o Azure. 

A ferramenta disponibiliza os seguintes detalhes:

**Avaliação de compatibilidade**

* Avaliação de elegibilidade de VMs com base no número de discos, no tamanho do disco, em IOPS, na alteração a dados e em algumas características da VM.

**Necessidade de largura de banda de rede vs avaliação de RPO**

* A largura de banda de rede estimada necessária para a replicação delta
* O débito que o Azure Site Recovery consegue a partir do local para o Azure
* RPO que pode ser obtido para uma determinada largura de banda
* Impacto no RPO pretendido se for aprovisionada uma largura de banda mais reduzida.

    
**Requisitos de infraestrutura do Azure**

* O requisito de tipo de armazenamento (conta armazenamento standard ou premium) para cada VM
* O número total de contas de armazenamento standard e premium a configurar para a replicação
* Sugestões de nomenclatura de contas de armazenamento, com base nas orientações do Armazenamento do Azure
* A colocação da conta de armazenamento para todas as VMs
* O número de núcleos do Azure a configurar antes da ativação pós-falha ou reativação pós-falha de teste na subscrição
* O tamanho recomendado da VM do Azure para cada VM no local

**Requisitos de infraestrutura no local**
* O espaço de armazenamento livre necessário em cada volume de armazenamento de Hyper-V para a replicação inicial e a replicação de diferenças com êxito para garantir que a replicação de VM não irá causar qualquer período de inatividade indesejável para as suas aplicações de produção
* Frequência de cópia máxima a definir para replicação do Hyper-V

**Orientação para a criação de batches na replicação inicial** 
* Número de batches de VM a utilizar para proteção
* Lista de VMs em cada batch
* Ordem pela qual cada batch deve ser protegido
* Tempo estimado para realizar a replicação inicial para cada batch

**Custo estimado de DR para o Azure**
* Custo total estimado de DR para o Azure: custo de computação, armazenamento, rede e da licença do Azure Site Recovery
* Análise detalhada do custo por VM



>[!IMPORTANT]
>
>Uma vez que a utilização é suscetível de aumentar ao longo do tempo, todos os cálculos da ferramenta anteriores são feitos presumindo um fator de crescimento de 30% nas características das cargas de trabalho e utilizando um valor de percentil 95 de todas as métricas de criação de perfis (IOPS de leitura/escrita, alterações a dados, etc.). Ambos os elementos (fator de crescimento e cálculo do percentil) são configuráveis. Para saber mais sobre o fator de crescimento, veja a secção "Considerações sobre o fator de crescimento". Para saber mais sobre o valor de percentil, veja a secção "Valor de percentil utilizado para o cálculo".
>

## <a name="support-matrix"></a>Matriz de suporte

| | **VMware para o Azure** |**Hyper-V para o Azure**|**Azure para o Azure**|**Hyper-V para um site secundário**|**VMware para um site secundário**
--|--|--|--|--|--
Cenários suportados |Sim|Sim|Não|Sim*|Não
Versão suportada | vCenter 6.5, 6.0 ou 5.5| Windows Server 2016, Windows Server 2012 R2 | ND |Windows Server 2016, Windows Server 2012 R2|ND
Configuração suportada|vCenter, ESXi| Cluster Hyper-V, anfitrião Hyper-V|ND|Cluster Hyper-V, anfitrião Hyper-V|ND|
Número de servidores para os quais podem ser criados perfis por instância de execução do Planeador de Implementações do Azure Site Recovery |Único (é possível criar um perfil de cada vez de VMs pertencentes a um vCenter Server ou um servidor ESXi)|Vários (é possível criar um perfil de cada vez de VMs em vários anfitriões ou clusters anfitriões)| ND |Vários (é possível criar um perfil de cada vez de VMs em vários anfitriões ou clusters anfitriões)| ND

*A ferramenta destina-se principalmente ao cenário de recuperação após desastre Hyper-V para o Azure. Para a recuperação após desastre de Hyper-V para um site secundário, só pode ser utilizada para compreender recomendações do lado da origem, como a largura de banda de rede necessária, espaço de armazenamento livre necessário em cada um dos servidores Hyper-V de origem e definições de batches e números de criação de batches na replicação inicial.  Ignore as recomendações e os custos do Azure do relatório. Além disso, a operação Obter Débito não é aplicável ao cenário de recuperação após desastre de Hyper-V para um site secundário.

## <a name="prerequisites"></a>Pré-requisitos
A ferramenta tem três fases principais para Hyper-V: obter a lista de VMs, criação de perfis e geração de relatórios. Também existe uma quarta opção para calcular apenas o débito. Os requisitos para o servidor no qual as diferentes fases têm de ser executadas são apresentados na tabela seguinte:

| Requisito do servidor | Descrição |
|---|---|
|Obter lista de VMs, criação de perfis e medição de débito |<ul><li>Sistema operativo: Microsoft Windows Server 2016 ou Microsoft Windows Server 2012 R2 </li><li>Configuração da máquina: 8 vCPUs, 16 GB de RAM, 300 GB HDD</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Microsoft Visual C++ Redistributable para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Acesso à Internet para o Azure a partir deste servidor</li><li>Conta de armazenamento do Azure</li><li>Acesso de administrador no servidor</li><li>Mínimo de 100 GB de espaço livre no disco (presumindo mil VMs com uma média de três discos cada, com perfis criados para 30 dias)</li><li>A VM a partir da qual está a executar a ferramenta Planeador de Implementações do Azure Site Recovery tem de ser adicionada à lista TrustedHosts de todos os servidores Hyper-V.</li><li>Todos os servidores de Hyper-V ser criados perfis tem de ser adicionados à lista TrustedHosts da VM cliente a partir de onde a ferramenta está a ser executada. [Saiba mais sobre como adicionar servidores à lista TrustedHosts](#steps-to-add-servers-into-trustedhosts-list). </li><li> A ferramenta deve ser executada a partir de privilégios de administrador do PowerShell ou da consola da linha de comandos no cliente</ul></ul>|
| Geração de relatórios | Um PC Windows ou Windows Server com o Microsoft Excel 2013 ou posterior |
| Permissões de utilizador | Conta de administrador para aceder ao cluster Hyper-V/anfitrião Hyper-V durante as operações de obtenção de lista de VMs e criação de perfis.<br>Todos os anfitriões para os quais têm de ser criados perfis devem ter uma conta de administrador de domínio com as mesmas credenciais, ou sejam nome de utilizador e palavra-passe
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Passos para adicionar servidores à Lista TrustedHosts
1.  A VM a partir da qual a ferramenta vai ser implementada deve ter todos os anfitriões a criar perfis na respetiva lista TrustedHosts. Para adicionar o cliente à lista Trustedhosts, execute o seguinte comando a partir de um PowerShell elevado na VM. A VM pode ser um Windows Server 2012 R2 ou Windows Server 2016. 

            set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate

1.  Cada Anfitrião Hyper-V para o qual têm de ser criados perfis devem ter:

    a. A VM na qual a ferramenta vai ser executada na respetiva lista TrustedHosts. Execute o seguinte comando a partir de um PowerShell elevado no anfitrião Hyper-V.

            set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate

    b. Comunicação remota do PowerShell ativada.

            Enable-PSRemoting -Force

## <a name="download-and-extract-the-deployment-planner-tool"></a>Transferir e extrair a ferramenta Planeador de Implementações

1.  Transfira a versão mais recente do [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
A ferramenta está comprimida numa pasta .zip. A mesma ferramenta suporta os cenários de recuperação após desastre VMware para o Azure e Hyper-V para o Azure. Também pode utilizar esta ferramenta para o cenário de recuperação após desastre Hyper-V para site secundário, mas ignore a recomendação de infraestrutura do Azure do relatório.

1.  Copie a pasta .zip para o Windows Server no qual pretende executar a ferramenta. Pode executar a ferramenta num Windows Server 2012 R2 ou Windows Server 2016. O servidor tem de ter acesso de rede para ligar ao cluster Hyper-V ou anfitrião Hyper-V que contém as VMs das quais quer criar perfis. Recomendamos que tenha a mesma configuração de hardware da VM onde a ferramenta vai ser executada que a do servidor Hyper-V que pretende proteger. Esta configuração garante que o débito obtido comunicado pela ferramenta corresponde ao débito real que o Azure Site Recovery pode obter durante a replicação. O cálculo de débito depende da largura de banda de rede disponível na configuração do servidor e do hardware (CPU, armazenamento e assim sucessivamente) do servidor. O débito é calculado do servidor onde a ferramenta está a ser executada para o Azure. Se a configuração de hardware do servidor diferir da do servidor Hyper-V, o débito obtido que a ferramenta comunica irá estar incorreto.
A configuração recomendada da VM: 8 vCPUs, 16 GB de RAM, 300 GB HDD.

1.  Extraia a pasta .zip.
Esta contém vários ficheiros e sub-pastas. O ficheiro executável é ASRDeploymentPlanner.exe, na pasta principal.

Exemplo: Copie o ficheiro .zip para a unidade E:\ e extraia-o. E:\ASR Deployment Planner_v2.3.zip

E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Atualizar para a versão mais recente do Planeador de Implementações
Se tiver uma versão anterior do Planeador de Implementações, execute um dos seguintes procedimentos:
 * Se a versão mais recente não contiver uma correção de criação de perfis e a criação de perfis já estiver em curso na sua versão atual da ferramenta, continue com a mesma.
 * Se a versão mais recente contiver essa correção, recomendamos-lhe que pare a criação de perfis na versão atual e que a reinicie na nova.


  >[!NOTE]
  >
  >Quando iniciar a criação de perfis com a nova versão, transmita o mesmo caminho do diretório de saída, para que a ferramenta anexe os dados da criação de perfis nos ficheiros existentes. Para gerar o relatório, vai ser utilizado um conjunto completo de dados de criação de perfis. Se transmitir um diretório de saída diferente, são criados ficheiros novos e os dados antigos não são utilizados para gerar o relatório.
  >
  >Cada Deployment Planner novo é uma atualização acumulativa do ficheiro .zip. Não tem de copiar os ficheiros mais recentes para a pasta anterior. Pode criar e utilizar uma pasta nova.

## <a name="version-history"></a>Histórico de versões
A versão mais recente da ferramenta do Azure Site Recovery Deployment Planner é 2.3.
Consulte a [histórico de versões do Planeador de implementações do Azure Site Recovery implementação](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) página para obter as correções que foram adicionadas em cada atualização.


## <a name="next-steps"></a>Passos Seguintes
* [Executar o planeador de implementação](site-recovery-hyper-v-deployment-planner-run.md).

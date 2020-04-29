---
title: Avaliar um grande número de VMware VMs para migração para Azure com Azure Migrate
description: Descreve como avaliar um grande número de VMware VMs para migração para Azure usando o serviço Azure Migrate.e
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336859"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Avaliar um grande número de VMware VMs para migração para Azure


Este artigo descreve como avaliar grandes números (1000-35.000) de VMware no local para migração para Azure, utilizando a ferramenta de avaliação do servidor migratório Azure.

[A Azure Migrate](migrate-services-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV). 

Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Plano de avaliação à escala.
> * Configure as permissões do Azure e prepare o VMware para avaliação.
> * Crie um projeto Azure Migrate e crie uma avaliação.
> * Reveja a avaliação como planeia para a migração.


> [!NOTE]
> Se quiser experimentar uma prova de conceito para avaliar um par de VMs antes de avaliar em escala, siga a nossa [série tutorial](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Plano de avaliação

Ao planear a avaliação de um grande número de VMware VMs, há algumas coisas em que pensar:

- **Projetos de Migração Do Plano Azure**: Descubra como implementar projetos de migração Azure. Por exemplo, se os seus centros de dados estiverem em geografias diferentes, ou precisar de armazenar metadados de descoberta, avaliação ou migração numa geografia diferente, poderá necessitar de vários projetos. 
- **Aparelhos de plano**: O Azure Migrate utiliza um aparelho azure migrate no local, implantado como VMware VM, para descobrir continuamente VMs. O aparelho monitoriza alterações ambientais, tais como a adição de VMs, discos ou adaptadores de rede. Também envia metadados e dados de desempenho sobre eles para o Azure. Tens de descobrir quantos aparelhos precisas de usar.
- **Conta do plano para descoberta**: O aparelho Azure Migrate usa uma conta com acesso ao VCenter Server para descobrir VMs para avaliação e migração. Se está a descobrir mais de 10.000 VMs, estabeleça várias contas.


## <a name="planning-limits"></a>Limites de planeamento
 
Utilize os limites resumidos nesta tabela para planear.

**Planeamento** | **Limites**
--- | --- 
**Projetos azure migrate** | Avalie até 35.000 VMs num projeto.
**Aplicação do Azure Migrate** | Um aparelho pode descobrir até 10.000 VMs num servidor vCenter.<br/> Um aparelho só pode ligar-se a um único servidor vCenter.<br/> Um aparelho só pode ser associado a um único projeto Azure Migrate.<br/>  Qualquer número de aparelhos pode ser associado a um único projeto Azure Migrate. <br/><br/> 
**Grupo** | Pode adicionar até 35.000 VMs num único grupo.
**Avaliação de Migração Azure** | Você pode avaliar até 35.000 VMs numa única avaliação.

Com estes limites em mente, aqui estão alguns exemplos de implementações:


**vCenter Server** | **VMs no servidor** | **Recomendação** | **Ação**
---|---|---
Um | < 10.000 | Um projeto Azure Migrate.<br/> Um aparelho.<br/> Uma conta vCenter para descoberta. | Configurar o aparelho, ligar-se ao VCenter Server com uma conta.
Um | > 10.000 | Um projeto Azure Migrate.<br/> Vários aparelhos.<br/> Múltiplas contas vCenter. | Instale aparelho para cada 10.000 VMs.<br/><br/> Configurar contas vCenter e dividir o inventário para limitar o acesso a uma conta a menos de 10.000 VMs.<br/> Ligue cada aparelho ao servidor vCenter com uma conta.<br/> Pode analisar dependências através de máquinas que são descobertas com diferentes aparelhos.
Vários | < 10.000 |  Um projeto Azure Migrate.<br/> Vários aparelhos.<br/> Uma conta vCenter para descoberta. | Configurar aparelhos, ligar ao VCenter Server com uma conta.<br/> Pode analisar dependências através de máquinas que são descobertas com diferentes aparelhos.
Vários | > 10.000 | Um projeto Azure Migrate.<br/> Vários aparelhos.<br/> Múltiplas contas vCenter. | Se a descoberta do VCenter Server < 10.000 VMs, instale um aparelho para cada servidor vCenter.<br/><br/> Se a descoberta do VCenter Server > 10.000 VMs, instale um aparelho para cada 10.000 VMs.<br/> Configurar contas vCenter e dividir o inventário para limitar o acesso a uma conta a menos de 10.000 VMs.<br/> Ligue cada aparelho ao servidor vCenter com uma conta.<br/> Pode analisar dependências através de máquinas que são descobertas com diferentes aparelhos.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Descoberta de plano em um ambiente multi-inquilino

Se você está planejando um ambiente multi-inquilino, você pode ver a descoberta no vCenter Server.

- Pode definir o âmbito de descoberta do aparelho para um vCenter Server datacenters, clusters ou pasta de clusters, anfitriões ou pasta de anfitriões ou VMs individuais.
- Se o seu ambiente for partilhado entre inquilinos e quiser descobrir cada inquilino separadamente, pode aceder à conta vCenter que o aparelho utiliza para ser descoberta. 
    - Você pode querer ver através de pastas VM se os inquilinos partilharem anfitriões. O Azure Migrate não consegue descobrir VMs se a conta vCenter tiver acesso concedido ao nível da pasta VCenter VM. Se procura analisar a sua descoberta através de pastas VM, pode fazê-lo garantindo que a conta vCenter tem acesso apenas de leitura atribuído a um nível VM. [Saiba mais](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Preparar para avaliação

Prepare azure e VMware para avaliação do servidor. 

1. Verifique [os requisitos e limitações de suporte vMware](migrate-support-matrix-vmware.md).
2. Estabeleça permissões para que a sua conta Azure interaja com a Azure Migrate.
3. Prepare vMware para avaliação.

Siga as instruções [deste tutorial](tutorial-prepare-vmware.md) para configurar estas definições.


## <a name="create-a-project"></a>Criar um projeto

De acordo com os seus requisitos de planeamento, faça o seguinte:

1. Criar um azure migrate projetos.
2. Adicione a ferramenta de avaliação do servidor de migração Azure aos projetos.

[Mais informações](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e rever uma avaliação

1. Crie avaliações para VMware VMs.
1. Reveja as avaliações em preparação para o planeamento migratório.


Siga as instruções [deste tutorial](tutorial-assess-vmware.md) para configurar estas definições.
    

## <a name="next-steps"></a>Passos seguintes

Neste artigo, irá:
 
> [!div class="checklist"] 
> * Planejado para escalar avaliações de Migração Azure para VMware VMs
> * Azure preparado e VMware para avaliação
> * Criou um projeto Azure Migrate e realizou avaliações
> * Avaliações revistas na preparação para a migração.

Agora, [saiba como](concepts-assessment-calculation.md) as avaliações são calculadas e como modificar as [avaliações.](how-to-modify-assessment.md)

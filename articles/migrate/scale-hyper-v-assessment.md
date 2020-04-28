---
title: Avaliar um grande número de VMs Hiper-V para migração para Azure com Azure Migrate [ Microsoft Docs
description: Descreve como avaliar um grande número de VMs hiper-V para migração para Azure usando o serviço Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70279443"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Avaliar um grande número de VMs hiper-V para migração para Azure

Este artigo descreve como avaliar um grande número de VMs hiper-V no local para migração para Azure, utilizando a ferramenta de avaliação do servidor migratório Azure.

[A Azure Migrate](migrate-services-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV). 


Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Plano de avaliação à escala.
> * Configure as permissões do Azure e prepare o Hyper-V para avaliação.
> * Crie um projeto Azure Migrate e crie uma avaliação.
> * Reveja a avaliação como planeia para a migração.


> [!NOTE]
> Se quiser experimentar uma prova de conceito para avaliar um par de VMs antes de avaliar em escala, siga a nossa [série tutorial](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Plano de avaliação

Ao planear a avaliação de um grande número de VMs hiper-V, há algumas coisas em que pensar:

- **Projetos de Migração Do Plano Azure**: Descubra como implementar projetos de migração Azure. Por exemplo, se os seus centros de dados estiverem em geografias diferentes, ou precisar de armazenar metadados de descoberta, avaliação ou migração numa geografia diferente, poderá necessitar de vários projetos.
- **Aparelhos de plano**: O Azure Migrate utiliza um aparelho azure migrate no local, implantado como VM Hiper-V, para descobrir continuamente VMs para avaliação e migração. O aparelho monitoriza alterações ambientais, tais como a adição de VMs, discos ou adaptadores de rede. Também envia metadados e dados de desempenho sobre eles para o Azure. Tens de descobrir quantos aparelhos devem ser acionados.


## <a name="planning-limits"></a>Limites de planeamento
 
Utilize os limites resumidos nesta tabela para planear.

**Planeamento** | **Limites**
--- | --- 
**Projetos azure migrate** | Avalie até 35.000 VMs num projeto.
**Aplicação do Azure Migrate** | Um aparelho pode descobrir até 5000 VMs.<br/> Um aparelho pode ligar até 300 hospedeiros Hyper-V.<br/> Um aparelho só pode ser associado a um único projeto Azure Migrate.<br/> Qualquer número de aparelhos pode ser associado a um único projeto Azure Migrate. <br/><br/> 
**Grupo** | Pode adicionar até 35.000 VMs num único grupo.
**Avaliação de Migração Azure** | Você pode avaliar até 35.000 VMs numa única avaliação.



## <a name="other-planning-considerations"></a>Outras considerações de planeamento

- Para começar a ser descoberto a partir do aparelho, tem de selecionar cada hospedeiro Hyper-V. 
- Se você está dirigindo um ambiente multi-inquilino, você não pode atualmente descobrir apenas VMs que pertencem a um inquilino específico. 

## <a name="prepare-for-assessment"></a>Preparar para avaliação

Prepare Azure e Hyper-V para avaliação do servidor. 

1. Verifique [os requisitos e limitações de suporte hyper-V](migrate-support-matrix-hyper-v.md).
2. Configurar permissões para a sua conta Azure interagir com a Azure Migrate
3. Prepare anfitriões e VMs hiper-V

Siga as instruções [deste tutorial](tutorial-prepare-hyper-v.md) para configurar estas definições.

## <a name="create-a-project"></a>Criar um projeto

De acordo com os seus requisitos de planeamento, faça o seguinte:

1. Criar um azure migrate projetos.
2. Adicione a ferramenta de avaliação do servidor de migração Azure aos projetos.

[Mais informações](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e rever uma avaliação

1. Crie avaliações para VMs Hiper-V.
1. Reveja as avaliações em preparação para o planeamento migratório.

[Saiba mais](tutorial-assess-hyper-v.md) sobre a criação e revisão de avaliações.
    

## <a name="next-steps"></a>Passos seguintes

Neste artigo, irá:
 
> [!div class="checklist"] 
> * Planejado para escalar avaliações de Migração Azure para VMs hiper-V
> * Azure preparado e Hiper-V para avaliação
> * Criou um projeto Azure Migrate e realizou avaliações
> * Avaliações revistas na preparação para a migração.

Agora, [saiba como](concepts-assessment-calculation.md) as avaliações são calculadas, e como modificar [avaliações](how-to-modify-assessment.md)

---
title: Avaliar um grande número de VMs de Hyper-V para migração para o Azure com o Azure Migrate | Documentos da Microsoft
description: Descreve como avaliar um grande número de VMs de Hyper-V para migração para o Azure com o serviço Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811547"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Avaliar um grande número de VMs de Hyper-V para migração para o Azure

Este artigo descreve como avaliar (> 1000) um grande número de VMs de Hyper-V no local para migração para o Azure com a ferramenta de avaliação do servidor de migrar do Azure.

[O Azure Migrate](migrate-services-overview.md) fornece um hub de ferramentas que ajudam a detetar, avaliar e migrar cargas de trabalho, aplicações e infraestrutura para o Microsoft Azure. O hub inclui as ferramentas do Azure Migrate e ofertas de (ISV fornecedor) de terceiros independentes de software. 


Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Plano para a avaliação à escala.
> * Configurar as permissões do Azure e preparar o Hyper-V para avaliação.
> * Crie um projeto do Azure Migrate e criar uma avaliação.
> * Quando planear de migração, reveja a avaliação.


> [!NOTE]
> Se quiser experimentar um prova de conceito para avaliar algumas VMs antes de avaliar em escala, siga nosso [série de tutoriais](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Plano de avaliação

Quando planear a avaliação do grande número de VMs de Hyper-V, existem algumas coisas a considerar:

- **Planear os projetos do Azure Migrate**: Descobrir como implementar projetos do Azure Migrate. Por exemplo, se seus centros de dados estão em diferentes geografias, ou se tiver de armazenar a deteção, avaliação ou metadados relacionados com a migração de uma localização geográfica de diferente, poderá ter vários projetos.
- **Planear aplicações**: O Azure Migrate utiliza uma aplicação do Azure Migrate de no local, implementada como uma VM de Hyper-V, para detetar continuamente as VMs para avaliação e migração. A aplicação monitoriza alterações de ambiente como adicionar VMs, discos ou adaptadores de rede. Também envia dados de desempenho e de metadados sobre as mesmas para o Azure. Terá de descobrir quantas aplicações a implementar.


## <a name="planning-limits"></a>Limites de planeamento
 
Utilize os limites resumidos nesta tabela para o planejamento.

**Planeamento** | **Limites**
--- | --- 
**Projetos de migração do Azure** | Avalie até 10 000 VMs num projeto.
**Aplicação do Azure Migrate** | Uma aplicação pode detetar VMs até 5000.<br/> Uma aplicação pode ligar-se até 300 anfitriões de Hyper-V.<br/> Uma aplicação só pode ser associada a um único projeto do Azure Migrate.<br/><br/> 
**Avaliação do Azure Migrate** | Pode avaliar até 10 000 VMs numa única avaliação.



## <a name="other-planning-considerations"></a>Outras considerações de planeamento

- Para iniciar a deteção de aplicação, tem de selecionar cada anfitrião Hyper-V. 
- Se estiver a executar um ambiente multi-inquilino, atualmente não consegue de detetar apenas as VMs que pertencem a um inquilino específico. 

## <a name="prepare-for-assessment"></a>Preparar a avaliação da

Prepare o Azure e Hyper-V para avaliação de servidor. 

1. Certifique-se [requisitos de suporte de Hyper-V e limitações](migrate-support-matrix-hyper-v.md).
2. Configurar permissões para a sua conta do Azure interagir com o Azure Migrate
3. Preparar VMs e anfitriões Hyper-V

Siga as instruções em [deste tutorial](tutorial-prepare-hyper-v.md) para configurar estas definições.

## <a name="create-a-project"></a>Criar um projeto

De acordo com suas necessidades de planejamento, faça o seguinte:

1. Pode crie projetos de Azure Migrate.
2. Adicione a ferramenta de avaliação do servidor de migrar do Azure para os projetos.

[Saiba mais](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e rever uma avaliação

1. Crie avaliações para as VMs de Hyper-V.
1. Reveja as avaliações em preparação para planeamento da migração.

[Saiba mais](tutorial-assess-hyper-v.md) sobre como criar e rever avaliações.
    

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, irá:
 
> [!div class="checklist"] 
> * Planejado dimensionar o Azure Migrate avaliações para as VMs de Hyper-V
> * Preparar o Azure e o Hyper-V para avaliação
> * Criou um projeto do Azure Migrate e executou avaliações
> * Revimos as avaliações em preparação para a migração.

Agora, [Saiba como](concepts-assessment-calculation.md) são calculadas as avaliações e como [modificar avaliações](how-to-modify-assessment.md)

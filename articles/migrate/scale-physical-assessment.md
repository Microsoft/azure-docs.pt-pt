---
title: Avaliar um grande número de servidores físicos para migração para Azure com o Azure Migrate Microsoft Docs
description: Descreve como avaliar um grande número de servidores físicos para migração para Azure usando o serviço Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76294373"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Avaliar um grande número de servidores físicos para migração para OTe

Este artigo descreve como avaliar um grande número de servidores físicos no local para migração para Azure, utilizando a ferramenta de avaliação do servidor migratório Azure.

[A Azure Migrate](migrate-services-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV). 


Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Plano de avaliação à escala.
> * Configure as permissões do Azure e prepare servidores físicos para avaliação.
> * Crie um projeto Azure Migrate e crie uma avaliação.
> * Reveja a avaliação como planeia para a migração.


> [!NOTE]
> Se quiser experimentar uma prova de conceito para avaliar alguns servidores antes de avaliar em escala, siga a nossa [série tutorial](tutorial-prepare-physical.md).

## <a name="plan-for-assessment"></a>Plano de avaliação

Ao planear a avaliação de um grande número de servidores físicos, há algumas coisas em que pensar:

- **Projetos de Migração Do Plano Azure**: Descubra como implementar projetos de migração Azure. Por exemplo, se os seus centros de dados estiverem em geografias diferentes, ou precisar de armazenar metadados de descoberta, avaliação ou migração numa geografia diferente, poderá necessitar de vários projetos.
- **Aparelhos de plano**: O Azure Migrate utiliza um aparelho azure migrate no local, implantado numa máquina Windows, para descobrir continuamente servidores para avaliação e migração. O aparelho monitoriza alterações ambientais, tais como a adição de VMs, discos ou adaptadores de rede. Também envia metadados e dados de desempenho sobre eles para o Azure. Tens de descobrir quantos aparelhos devem ser acionados.


## <a name="planning-limits"></a>Limites de planeamento
 
Utilize os limites resumidos nesta tabela para planear.

**Planeamento** | **Limites**
--- | --- 
**Projetos azure migrate** | Avalie até 35.000 servidores num projeto.
**Aplicação do Azure Migrate** | Um aparelho pode descobrir até 250 servidores.<br/> Um aparelho só pode ser associado a um único projeto Azure Migrate.<br/> Qualquer número de aparelhos pode ser associado a um único projeto Azure Migrate. <br/><br/> 
**Grupo** | Pode adicionar até 35.000 servidores num único grupo.
**Avaliação de Migração Azure** | Pode avaliar até 35.000 servidores numa única avaliação.


## <a name="other-planning-considerations"></a>Outras considerações de planeamento

- Para iniciar a descoberta a partir do aparelho, tem de selecionar cada servidor físico. 

## <a name="prepare-for-assessment"></a>Preparar para avaliação

Prepare o Azure e os servidores físicos para avaliação do servidor. 

1. Verifique [os requisitos e limitações de suporte físico](migrate-support-matrix-physical.md)do servidor .
2. Estabeleça permissões para que a sua conta Azure interaja com a Azure Migrate.
3. Prepare os servidores físicos.

Siga as instruções [deste tutorial](tutorial-prepare-physical.md) para configurar estas definições.

## <a name="create-a-project"></a>Criar um projeto

De acordo com os seus requisitos de planeamento, faça o seguinte:

1. Criar um projeto do Azure Migrate.
2. Adicione a ferramenta de avaliação do servidor de migração Azure aos projetos.

[Mais informações](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e rever uma avaliação

1. Crie avaliações para servidores físicos.
1. Reveja as avaliações em preparação para o planeamento migratório.

[Saiba mais](tutorial-assess-physical.md) sobre a criação e revisão de avaliações.
    

## <a name="next-steps"></a>Passos seguintes

Neste artigo, irá:
 
> [!div class="checklist"] 
> * Planejado escalar avaliações de Migração Azure para servidores físicos.
> * Azure preparado e servidores físicos para avaliação.
> * Criou um projeto Azure Migrate e realizou avaliações.
> * Avaliações revistas na preparação para a migração.

Agora, [saiba como](concepts-assessment-calculation.md) as avaliações são calculadas e como modificar as [avaliações.](how-to-modify-assessment.md)

---
title: Avaliar um grande número de servidores físicos para a migração para Azure com Azure Migrate | Microsoft Docs
description: Descreve como avaliar um grande número de servidores físicos para migração para Azure usando o serviço Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/19/2020
ms.openlocfilehash: 232475c50ab56fe6fb7a39a3497a8de3947fe851
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780315"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Avaliar um grande número de servidores físicos para a migração para Azure

Este artigo descreve como avaliar um grande número de servidores físicos no local para migração para Azure, usando a Azure Migrate Discovery e a ferramenta de avaliação.

[A Azure Migrate](migrate-services-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar aplicações, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV). 


Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Plano de avaliação em escala.
> * Configure permissões Azure e prepare servidores físicos para avaliação.
> * Crie um projeto Azure Migrate e crie uma avaliação.
> * Reveja a avaliação como planeia para a migração.


> [!NOTE]
> Se quiser experimentar uma prova de conceito para avaliar alguns servidores antes de avaliar em escala, siga as nossas [séries tutoriais](./tutorial-discover-physical.md).

## <a name="plan-for-assessment"></a>Plano de avaliação

Ao planear a avaliação de um grande número de servidores físicos, há algumas coisas em que pensar:

- **Plano Azure Migrate :** Descubra como implementar projetos da Azure Migrate. Por exemplo, se os seus centros de dados estiverem em geografias diferentes, ou precisar de armazenar metadados relacionados com a descoberta, avaliação ou migração numa geografia diferente, poderá precisar de vários projetos.
- **Planos :** A Azure Migrate utiliza um aparelho Azure Migrate, implantado num servidor Windows, para descobrir continuamente servidores para avaliação e migração. O aparelho monitoriza as alterações ambientais, tais como a adição de servidores, discos ou adaptadores de rede. Também envia metadados e dados de desempenho sobre eles para a Azure. Tens de descobrir quantos aparelhos deves implantar.


## <a name="planning-limits"></a>Limites de planeamento
 
Utilize os limites resumidos nesta tabela para planeamento.

**Planeamento** | **Limites**
--- | --- 
**Projetos Azure Migrate** | Avalie até 35.000 servidores num projeto.
**Aplicação do Azure Migrate** | Um aparelho pode descobrir até 1000 servidores.<br/> Um aparelho só pode ser associado a um único projeto Azure Migrate.<br/> Qualquer número de aparelhos pode ser associado a um único projeto Azure Migrate. <br/><br/> 
**Grupo** | Pode adicionar até 35.000 servidores num único grupo.
**Avaliação de Azure Migrate** | Pode avaliar até 35.000 servidores numa única avaliação.


## <a name="other-planning-considerations"></a>Outras considerações de planeamento

- Para iniciar a descoberta do aparelho, tem de selecionar cada servidor físico. 

## <a name="prepare-for-assessment"></a>Preparar para avaliação

Prepare o Azure e os servidores físicos para a ferramenta Discovery e assessment:  

1. Verifique os [requisitos e limitações de suporte físico do servidor.](migrate-support-matrix-physical.md)
2. Configure as permissões para que a sua conta Azure interaja com a Azure Migrate.
3. Prepare os servidores físicos.

Siga as instruções [deste tutorial](./tutorial-discover-physical.md) para configurar estas definições.

## <a name="create-a-project"></a>Criar um projeto

De acordo com os seus requisitos de planeamento, faça o seguinte:

1. Criar um projeto do Azure Migrate.
2. Adicione a Azure Migrate Discovery e a ferramenta de avaliação aos projetos.

[Saiba mais](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Criar e rever uma avaliação

1. Criar avaliações para servidores físicos.
1. Reveja as avaliações em preparação para o planeamento migratório.

[Saiba mais](tutorial-assess-physical.md) sobre a criação e revisão de avaliações.
    

## <a name="next-steps"></a>Passos seguintes

Neste artigo, irá:
 
> [!div class="checklist"] 
> * Planejado escalar avaliações do Azure Migrate para servidores físicos.
> * Azure preparado e servidores físicos para avaliação.
> * Criei um projeto Azure Migrate e fez avaliações.
> * Avaliações revistas em preparação para a migração.

Agora, [saiba como as](concepts-assessment-calculation.md) avaliações são calculadas e como [modificar avaliações.](how-to-modify-assessment.md)
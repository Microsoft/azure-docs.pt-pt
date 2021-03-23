---
title: Avalie um grande número de servidores em ambiente Hiper-V para migração para Azure com Azure Migrate | Microsoft Docs
description: Descreve como avaliar um grande número de servidores em ambiente Hiper-V para migração para Azure usando o serviço Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 495e1bf415146471fcccad34e2879398e12e1769
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780298"
---
# <a name="assess-large-numbers-of-servers-in-hyper-v-environment-for-migration-to-azure"></a>Avaliar um grande número de servidores em ambiente Hiper-V para migração para Azure

Este artigo descreve como avaliar um grande número de servidores no local em ambiente Hiper-V para migração para Azure, usando a Azure Migrate Discovery e a ferramenta de avaliação.

[A Azure Migrate](migrate-services-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar aplicações, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV). 


Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Plano de avaliação em escala.
> * Configure permissões Azure, e prepare o Hyper-V para avaliação.
> * Crie um projeto Azure Migrate e crie uma avaliação.
> * Reveja a avaliação como planeia para a migração.


> [!NOTE]
> Se quiser experimentar uma prova de conceito para avaliar alguns servidores antes de avaliar em escala, siga as nossas [séries tutoriais](./tutorial-discover-hyper-v.md)

## <a name="plan-for-assessment"></a>Plano de avaliação

Ao planear a avaliação de um grande número de servidores em ambiente Hiper-V, há algumas coisas em que pensar:

- **Plano Azure Migrate :** Descubra como implementar projetos da Azure Migrate. Por exemplo, se os seus centros de dados estiverem em geografias diferentes, ou precisar de armazenar metadados relacionados com a descoberta, avaliação ou migração numa geografia diferente, poderá precisar de vários projetos.
- **Aparelhos de plano**: A Azure Migrate utiliza um aparelho Azure Migrate, implantado como Um Hiper-VM, para descobrir continuamente servidores para avaliação e migração. O aparelho monitoriza as alterações ambientais, tais como a adição de servidores, discos ou adaptadores de rede. Também envia metadados e dados de desempenho sobre eles para a Azure. Tens de descobrir quantos aparelhos deves implantar.


## <a name="planning-limits"></a>Limites de planeamento
 
Utilize os limites resumidos nesta tabela para planeamento.

**Planeamento** | **Limites**
--- | --- 
**Projetos Azure Migrate** | Avalie até 35.000 servidores num projeto.
**Aplicação do Azure Migrate** | Um aparelho pode descobrir até 5000 servidores.<br/> Um aparelho pode ligar até 300 anfitriões Hiper-V.<br/> Um aparelho só pode ser associado a um único projeto Azure Migrate.<br/> Qualquer número de aparelhos pode ser associado a um único projeto Azure Migrate. <br/><br/> 
**Grupo** | Pode adicionar até 35.000 servidores num único grupo.
**Avaliação de Azure Migrate** | Pode avaliar até 35.000 servidores numa única avaliação.



## <a name="other-planning-considerations"></a>Outras considerações de planeamento

- Para iniciar a descoberta do aparelho, tem de selecionar cada hospedeiro Hyper-V. 
- Se você está executando um ambiente multi-inquilino, você não pode atualmente descobrir apenas servidores que pertencem a um inquilino específico. 

## <a name="prepare-for-assessment"></a>Preparar para avaliação

Prepare o Azure e o Hyper-V para a ferramenta de descoberta e avaliação: 

1. Verifique os [requisitos e limitações de suporte do Hiper-V](migrate-support-matrix-hyper-v.md).
2. Configure as permissões para a sua conta Azure interagir com a Azure Migrate
3. Preparar anfitriões e servidores Hiper-V

Siga as instruções [deste tutorial](./tutorial-discover-hyper-v.md) para configurar estas definições.

## <a name="create-a-project"></a>Criar um projeto

De acordo com os seus requisitos de planeamento, faça o seguinte:

1. Criar um Azure Migrate projetos.
2. Adicione a Azure Migrate Discovery e a ferramenta de avaliação aos projetos.

[Saiba mais](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Criar e rever uma avaliação

1. Criar avaliações para servidores em ambiente Hiper-V.
1. Reveja as avaliações em preparação para o planeamento migratório.

[Saiba mais](tutorial-assess-hyper-v.md) sobre a criação e revisão de avaliações.
    

## <a name="next-steps"></a>Passos seguintes

Neste artigo, irá:
 
> [!div class="checklist"] 
> * Planejado escalar avaliações do Azure Migrate para servidores em ambiente Hiper-V
> * Azure preparado e Hiper-V para avaliação
> * Criei um projeto Azure Migrate e fez avaliações
> * Avaliações revistas em preparação para a migração.

Agora, [saiba como as](concepts-assessment-calculation.md) avaliações são calculadas e como [modificar avaliações](how-to-modify-assessment.md)
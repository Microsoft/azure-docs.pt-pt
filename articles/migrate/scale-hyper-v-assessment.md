---
title: Avaliar grandes números de VMs do Hyper-V para migração para o Azure com migrações para Azure | Microsoft Docs
description: Descreve como avaliar grandes números de VMs do Hyper-V para migração para o Azure usando o serviço migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279443"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Avaliar grandes números de VMs do Hyper-V para migração para o Azure

Este artigo descreve como avaliar grandes números de VMs do Hyper-V locais para migração para o Azure, usando a ferramenta de avaliação do servidor de migrações para Azure.

As migrações para [Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros. 


Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Planeje a avaliação em escala.
> * Configure as permissões do Azure e prepare o Hyper-V para avaliação.
> * Crie um projeto de migrações para Azure e crie uma avaliação.
> * Examine a avaliação conforme planeja a migração.


> [!NOTE]
> Se você quiser experimentar uma prova de conceito para avaliar algumas VMs antes de avaliar em escala, siga nossa [série de tutoriais](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Planejar a avaliação

Ao planejar a avaliação de um grande número de VMs do Hyper-V, há algumas coisas a considerar:

- **Planejar projetos**de migrações para Azure: Descubra como implantar projetos de migrações para Azure. Por exemplo, se seus data centers estiverem em geografias diferentes, ou se você precisar armazenar metadados relacionados à migração, à avaliação ou ao migrar em uma geografia diferente, talvez precise de vários projetos.
- **Dispositivos de plano**: As migrações para Azure usam um dispositivo de migrações do Azure local, implantado como uma VM do Hyper-V, para descobrir continuamente as VMs para avaliação e migração. O dispositivo monitora as alterações de ambiente, como adicionar VMs, discos ou adaptadores de rede. Ele também envia metadados e dados de desempenho sobre eles para o Azure. Você precisa descobrir quantos dispositivos implantar.


## <a name="planning-limits"></a>Limites de planejamento
 
Use os limites resumidos nesta tabela para planejamento.

**Planeamento** | **Limites**
--- | --- 
**Projetos de migrações para Azure** | Avalie até 35.000 VMs em um projeto.
**Dispositivo de migrações para Azure** | Um dispositivo pode descobrir até 5000 VMs.<br/> Um dispositivo pode se conectar a até 300 hosts Hyper-V.<br/> Um dispositivo só pode ser associado a um único projeto de migrações para Azure.<br/> Qualquer número de dispositivos pode ser associado a um único projeto de migrações para Azure. <br/><br/> 
**Grupo** | Você pode adicionar até 35.000 VMs em um único grupo.
**Avaliação de migrações para Azure** | Você pode avaliar até 35.000 VMs em uma única avaliação.



## <a name="other-planning-considerations"></a>Outras considerações de planejamento

- Para iniciar a descoberta do dispositivo, você precisa selecionar cada host Hyper-V. 
- Se você estiver executando um ambiente multilocatário, não será possível descobrir atualmente somente as VMs que pertencem a um locatário específico. 

## <a name="prepare-for-assessment"></a>Preparar para avaliação

Prepare o Azure e o Hyper-V para avaliação do servidor. 

1. Verifique [os requisitos de suporte e as limitações do Hyper-V](migrate-support-matrix-hyper-v.md).
2. Configurar permissões para sua conta do Azure para interagir com as migrações para Azure
3. Preparar hosts e VMs do Hyper-V

Siga as instruções neste [tutorial](tutorial-prepare-hyper-v.md) para definir essas configurações.

## <a name="create-a-project"></a>Criar um projeto

De acordo com seus requisitos de planejamento, faça o seguinte:

1. Criar projetos de migrações para Azure.
2. Adicione a ferramenta de avaliação do servidor de migrações para projetos.

[Saiba mais](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e examinar uma avaliação

1. Crie Avaliações para VMs do Hyper-V.
1. Examine as avaliações em preparação para o planejamento de migração.

[Saiba mais](tutorial-assess-hyper-v.md) sobre como criar e revisar avaliações.
    

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, irá:
 
> [!div class="checklist"] 
> * Planejado para dimensionar as avaliações de migração do Azure para VMs do Hyper-V
> * Azure e Hyper-V preparados para avaliação
> * Criou um projeto de migrações para Azure e executou avaliações
> * Avaliações revisadas em preparação para a migração.

Agora, [saiba como](concepts-assessment-calculation.md) as avaliações são calculadas e como [modificar as avaliações](how-to-modify-assessment.md)

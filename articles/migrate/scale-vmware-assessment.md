---
title: Avaliar um grande número de VMs de VMware para migração para o Azure com o Azure Migrate | Documentos da Microsoft
description: Descreve como avaliar um grande número de VMs de VMware para migração para o Azure com o serviço Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 6102a1c59be3627b95dc1e0cb1d1d712d5832d2f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811339"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Avaliar um grande número de VMs de VMware para migração para o Azure


Este artigo descreve como avaliar (35.000 1000) um grande número de VMs de VMware no local para migração para o Azure com a ferramenta de avaliação do servidor de migrar do Azure

[O Azure Migrate](migrate-services-overview.md) fornece um hub de ferramentas que ajudam a detetar, avaliar e migrar cargas de trabalho, aplicações e infraestrutura para o Microsoft Azure. O hub inclui as ferramentas do Azure Migrate e ofertas de (ISV fornecedor) de terceiros independentes de software. 

Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Plano para a avaliação à escala.
> * Configurar as permissões do Azure e preparar o VMware para avaliação.
> * Crie um projeto do Azure Migrate e criar uma avaliação.
> * Quando planear de migração, reveja a avaliação.


> [!NOTE]
> Se quiser experimentar um prova de conceito para avaliar algumas VMs antes de avaliar em escala, siga nosso [série de tutoriais](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Plano de avaliação

Quando planear a avaliação do grande número de VMs de VMware, existem algumas coisas a considerar:

- **Planear os projetos do Azure Migrate**: Descobrir como implementar projetos do Azure Migrate. Por exemplo, se seus centros de dados estão em diferentes geografias, ou se tiver de armazenar a deteção, avaliação ou metadados relacionados com a migração de uma localização geográfica de diferente, poderá ter vários projetos. 
- **Planear aplicações**: O Azure Migrate utiliza uma aplicação do Azure Migrate de no local, implementada como uma VM de VMware para detetar continuamente as VMs. A aplicação monitoriza alterações de ambiente como adicionar VMs, discos ou adaptadores de rede. Também envia dados de desempenho e de metadados sobre as mesmas para o Azure. Terá de descobrir quantas aplicações de que necessita para implementar.
- **Plano de contas para a deteção**: A aplicação do Azure Migrate utiliza uma conta com acesso ao vCenter Server para detetar VMs para avaliação e migração. Se estiver a detetar mais de 10.000 VMs, configure várias contas.


## <a name="planning-limits"></a>Limites de planeamento
 
Utilize os limites resumidos nesta tabela para o planejamento.

**Planeamento** | **Limites**
--- | --- 
**Projetos de migração do Azure** | Avalie até 35 000 VMs num projeto.
**Aplicação do Azure Migrate** | Uma aplicação só pode ligar a um único servidor do vCenter.<br/><br/> Uma aplicação só pode ser associada a um único projeto do Azure Migrate.<br/> Uma aplicação pode detetar até 10 000 VMs num vCenter Server.
**Avaliação do Azure Migrate** | Pode avaliar até 35 000 VMs numa única avaliação.

Com estes limites em mente, aqui estão algumas implementações de exemplo:


**Servidor vCenter** | **VMs no servidor** | **Recomendação** | **ação**
---|---|---
Um | < 10,000 | Um projeto do Azure Migrate.<br/> Uma aplicação.<br/> Uma conta de vCenter para deteção. | Configurar a aplicação, ligar ao vCenter Server com uma conta.
Um | > 10,000 | Um projeto do Azure Migrate.<br/> Várias aplicações.<br/> Várias contas do vCenter. | Configure a aplicação para cada 10 000 VMs.<br/><br/> Configurar contas do vCenter e dividir o inventário para limitar o acesso para uma conta para menos de 10 000 VMs.<br/> Ligue-se cada aplicação para o vCenter server com uma conta.<br/> Pode analisar as dependências entre computadores que são detetados com diferentes aplicações.
Vários | < 10,000 |  Um projeto do Azure Migrate.<br/> Várias aplicações.<br/> Uma conta de vCenter para deteção. | Configurar dispositivos, ligar ao vCenter Server com uma conta.<br/> Pode analisar as dependências entre computadores que são detetados com diferentes aplicações.
Vários | > 10,000 | Um projeto do Azure Migrate.<br/> Várias aplicações.<br/> Várias contas do vCenter. | Se a deteção do vCenter Server < 10 000 VMs, configurar uma aplicação para cada servidor do vCenter.<br/><br/> Se a deteção do vCenter Server > 10.000 VMs, configurar uma aplicação para cada 10 000 VMs.<br/> Configurar contas do vCenter e dividir o inventário para limitar o acesso para uma conta para menos de 10 000 VMs.<br/> Ligue-se cada aplicação para o vCenter server com uma conta.<br/> Pode analisar as dependências entre computadores que são detetados com diferentes aplicações.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Deteção de plano num ambiente de multi-inquilino

Se estiver a planear para um ambiente de vários inquilino, pode definir o âmbito de deteção no vCenter Server.

- Pode definir o âmbito de deteção da aplicação para um datacenter do vCenter Server, o cluster ou a pasta de clusters, anfitrião ou pasta de anfitriões ou VMs individuais.
- Se seu ambiente é compartilhado entre inquilinos e não pretender detetar cada inquilino em separado, pode definir o âmbito de acesso à conta do vCenter que a aplicação utiliza para a deteção. 
    - Se os inquilinos partilham anfitriões, crie as credenciais com acesso só de leitura para as VMs que pertencem ao inquilino específico. 
    - Utilize estas credenciais para a deteção de aplicação do Azure Migrate.
    - Avaliação do Azure Migrate não consegue detetar VMs, se a conta do vCenter tem acesso concedido ao nível da pasta VM do vCenter. Pastas de anfitriões e clusters são suportadas. 

## <a name="prepare-for-assessment"></a>Preparar a avaliação da

Prepare o Azure e VMware para avaliação de servidor. 

1. Certifique-se [VMware suportar requisitos e limitações](migrate-support-matrix-vmware.md).
2. Configure permissões para a sua conta do Azure interagir com o Azure Migrate.
3. Prepare o VMware para avaliação.


Siga as instruções em [deste tutorial](tutorial-prepare-vmware.md) para configurar estas definições.


## <a name="create-a-project"></a>Criar um projeto

De acordo com suas necessidades de planejamento, faça o seguinte:

1. Pode crie projetos de Azure Migrate.
2. Adicione a ferramenta de avaliação do servidor de migrar do Azure para os projetos.

[Saiba mais](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e rever uma avaliação

1. Crie avaliações para as VMs de VMware.
1. Reveja as avaliações em preparação para planeamento da migração.


Siga as instruções em [deste tutorial](tutorial-assess-vmware.md) para configurar estas definições.
    

## <a name="next-steps"></a>Passos seguintes

Neste artigo, irá:
 
> [!div class="checklist"] 
> * Planejado dimensionar o Azure Migrate avaliações para as VMs de VMware
> * Preparado Azure e VMware para avaliação
> * Criou um projeto do Azure Migrate e executou avaliações
> * Revimos as avaliações em preparação para a migração.

Agora, [Saiba como](concepts-assessment-calculation.md) são calculadas as avaliações e como [modificar avaliações](how-to-modify-assessment.md).

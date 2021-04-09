---
title: Avaliar um grande número de servidores em ambiente VMware para migração para Azure com Azure Migrate
description: Descreve como avaliar um grande número de servidores em ambiente VMware para migração para Azure usando o serviço Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778258"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Avaliar um grande número de servidores em ambiente VMware para migração para Azure


Este artigo descreve como avaliar grandes números (1000-35.000) de servidores no local em ambiente VMware para migração para Azure, usando a ferramenta de descoberta e avaliação Azure Migrate.

[A Azure Migrate](migrate-services-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar aplicações, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV). 

Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Plano de avaliação em escala.
> * Configure permissões Azure e prepare o VMware para avaliação.
> * Crie um projeto Azure Migrate e crie uma avaliação.
> * Reveja a avaliação como planeia para a migração.


> [!NOTE]
> Se quiser experimentar uma prova de conceito para avaliar alguns servidores antes de avaliar em escala, siga as nossas [séries tutoriais](./tutorial-discover-vmware.md)

## <a name="plan-for-assessment"></a>Plano de avaliação

Ao planear a avaliação de um grande número de servidores em ambiente VMware, há algumas coisas em que pensar:

- **Plano Azure Migrate :** Descubra como implementar projetos da Azure Migrate. Por exemplo, se os seus centros de dados estiverem em geografias diferentes, ou precisar de armazenar metadados relacionados com a descoberta, avaliação ou migração numa geografia diferente, poderá precisar de vários projetos. 
- **Planos :** A Azure Migrate utiliza um aparelho Azure Migrate, implantado como VMware VMware, para descobrir continuamente os servidores. O aparelho monitoriza as alterações ambientais, tais como a adição de servidores, discos ou adaptadores de rede. Também envia metadados e dados de desempenho sobre eles para a Azure. Tens de descobrir quantos aparelhos precisas de usar.
- **Plano de contas para a descoberta**: O aparelho Azure Migrate utiliza uma conta com acesso ao servidor vCenter para descobrir servidores para avaliação e migração. Se estiver a descobrir mais de 10.000 servidores, crie várias contas, pois não existe sobreposição entre servidores descobertos a partir de dois aparelhos num projeto. 

> [!NOTE]
> Se estiver a configurar vários aparelhos, certifique-se de que não existe sobreposição entre os servidores nas contas vCenter fornecidas. Uma deteção com uma sobreposição desse tipo é um cenário não suportado. Se um servidor for descoberto por mais de um aparelho, isto resulta em duplicados na descoberta e em problemas, permitindo a replicação para o servidor utilizando o portal Azure na Migração do Servidor.

## <a name="planning-limits"></a>Limites de planeamento
 
Utilize os limites resumidos nesta tabela para planeamento.

**Planeamento** | **Limites**
--- | --- 
**Projetos Azure Migrate** | Avalie até 35.000 servidores num projeto.
**Aplicação do Azure Migrate** | Um aparelho pode descobrir até 10.000 servidores num servidor vCenter.<br/> Um aparelho só pode ligar-se a um único vCenter Server.<br/> Um aparelho só pode ser associado a um único projeto Azure Migrate.<br/>  Qualquer número de aparelhos pode ser associado a um único projeto Azure Migrate. <br/><br/> 
**Grupo** | Pode adicionar até 35.000 servidores num único grupo.
**Avaliação de Azure Migrate** | Pode avaliar até 35.000 servidores numa única avaliação.

Com estes limites em mente, eis alguns exemplos de implementações:


**vCenter Server** | **Servidores no servidor** | **Recomendação** | **Ação**
---|---|---|---
Um | < 10.000 | Um projeto Azure Migrate.<br/> Um aparelho.<br/> Uma conta vCenter para a descoberta. | Configurar o aparelho, ligar ao servidor vCenter com uma conta.
Um | > 10.000 | Um projeto Azure Migrate.<br/> Vários aparelhos.<br/> Várias contas vCenter. | Configurar o aparelho para cada 10.000 servidores.<br/><br/> Crie contas vCenter e divida o inventário para limitar o acesso a uma conta a menos de 10.000 servidores.<br/> Ligue cada aparelho ao servidor vCenter com uma conta.<br/> Pode analisar dependências em servidores que são descobertos com diferentes aparelhos. <br/> <br/> Certifique-se de que não existe sobreposição entre os servidores nas contas vCenter fornecidas. Uma deteção com uma sobreposição desse tipo é um cenário não suportado. Se um servidor for descoberto por mais de um aparelho, isto resulta numa duplicação na descoberta e em problemas, permitindo a replicação para o servidor utilizando o portal Azure na Migração do Servidor.
Vários | < 10.000 |  Um projeto Azure Migrate.<br/> Vários aparelhos.<br/> Uma conta vCenter para a descoberta. | Configurar aparelhos, ligar ao servidor vCenter com uma conta.<br/> Pode analisar dependências em servidores que são descobertos com diferentes aparelhos.
Vários | > 10.000 | Um projeto Azure Migrate.<br/> Vários aparelhos.<br/> Várias contas vCenter. | Se a descoberta do vCenter Server < 10.000 servidores, crie um aparelho para cada servidor vCenter.<br/><br/> Se a descoberta do vCenter Server > 10.000 servidores, crie um aparelho para cada 10.000 servidores.<br/> Crie contas vCenter e divida o inventário para limitar o acesso a uma conta a menos de 10.000 servidores.<br/> Ligue cada aparelho ao servidor vCenter com uma conta.<br/> Pode analisar dependências em servidores que são descobertos com diferentes aparelhos. <br/><br/> Certifique-se de que não existe sobreposição entre os servidores nas contas vCenter fornecidas. Uma deteção com uma sobreposição desse tipo é um cenário não suportado. Se um servidor for descoberto por mais de um aparelho, isto resulta numa duplicação na descoberta e em problemas, permitindo a replicação para o servidor utilizando o portal Azure na Migração do Servidor.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Plano de descoberta em um ambiente multi-inquilino

Se você está planejando um ambiente multi-inquilino, você pode examinar a descoberta no vCenter Server.

- Pode definir o âmbito de descoberta do aparelho para um vCenter Server datacenters, clusters ou pasta de clusters, anfitriões ou pasta de anfitriões ou servidores individuais.
- Se o seu ambiente é partilhado entre inquilinos e pretende descobrir cada inquilino separadamente, pode aceder à conta vCenter que o aparelho utiliza para ser descoberto. 
    - Você pode querer ser examinado por pastas VM se os inquilinos compartilham anfitriões. O Azure Migrate não consegue descobrir servidores se a conta vCenter tiver acesso concedido ao nível da pasta VM do vCenter. Se estiver a procurar analisar a sua descoberta por pastas VM, pode fazê-lo garantindo que a conta vCenter tem acesso apenas de leitura atribuído a um nível de servidor. [Saiba mais](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Preparar para avaliação

Prepare a ferramenta Azure e VMware para a Descoberta e a ferramenta de avaliação:

1. Verifique os [requisitos e limitações de suporte da VMware](migrate-support-matrix-vmware.md).
2. Configure as permissões para que a sua conta Azure interaja com a Azure Migrate.
3. Prepare o VMware para avaliação.

Siga as instruções [deste tutorial](./tutorial-discover-vmware.md) para configurar estas definições.


## <a name="create-a-project"></a>Criar um projeto

De acordo com os seus requisitos de planeamento, faça o seguinte:

1. Criar um Azure Migrate projetos.
2. Adicione a Azure Migrate Discovery e a ferramenta de avaliação aos projetos.

[Saiba mais](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Criar e rever uma avaliação

1. Criar avaliações para servidores em ambiente VMware.
1. Reveja as avaliações em preparação para o planeamento migratório.


Siga as instruções [deste tutorial](./tutorial-assess-vmware-azure-vm.md) para configurar estas definições.
    

## <a name="next-steps"></a>Passos seguintes

Neste artigo, irá:
 
> [!div class="checklist"] 
> * Planejado escalar avaliações do Azure Migrate para servidores em ambiente VMware
> * Azure preparado e VMware para avaliação
> * Criei um projeto Azure Migrate e fez avaliações
> * Avaliações revistas em preparação para a migração.

Agora, [saiba como as](concepts-assessment-calculation.md) avaliações são calculadas e como [modificar avaliações.](how-to-modify-assessment.md)
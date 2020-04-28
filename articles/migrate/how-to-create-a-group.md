---
title: Máquinas de grupo para avaliação com a Azure Migrate Microsoft Docs
description: Descreve como agrupar máquinas antes de fazer uma avaliação com o serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68301666"
---
# <a name="create-a-group-for-assessment"></a>Criar um grupo para avaliação

Este artigo descreve como criar grupos de máquinas para avaliação com o Azure Migrate: Server Assessment.

[A Azure Migrate](migrate-services-overview.md) ajuda-o a migrar para Azure. A Azure Migrate fornece um centro centralizado para acompanhar a descoberta, avaliação e migração de infraestruturas, aplicações e dados no local para o Azure. O hub fornece ferramentas Azure para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV). 

## <a name="grouping-machines"></a>Máquinas de agrupamento

Reúne-se máquinas em grupos para avaliar se são adequadas para a migração para Azure, e para obter o tamanho do Azure e estimativas de custos para eles. Há algumas formas de criar grupos:

- Se conhecer as máquinas que precisam de ser migradas em conjunto, pode criar manualmente o grupo em Azure Migrate.
- Se não tem a certeza sobre as máquinas que precisam de ser agrunadas, pode utilizar a funcionalidade de visualização da dependência no Azure Migrate para criar grupos. 

> [!NOTE]
> A funcionalidade de visualização da dependência não está disponível no Governo do Azure.

## <a name="create-a-group-manually"></a>Criar um grupo manualmente

Pode criar um grupo ao mesmo tempo que [cria uma avaliação.](how-to-create-assessment.md)

Se quiser criar um grupo manualmente fora da criação de uma avaliação, faça o seguinte:

1. No projeto Azure Migrate > **visão geral,** clique em **avaliar e migrar servidores**. In **Azure Migrate: Server Assessment**, clique em **Grupos**
    - Se ainda não adicionou a ferramenta De Avaliação do Servidor Azure Migração, clique para adicioná-la. [Saiba mais](how-to-assess.md).
    - Se ainda não criou um projeto Azure Migrate, [saiba mais.](how-to-add-tool-first-time.md)

    ![Selecione grupos](./media/how-to-create-a-group/select-groups.png)

2. Clique no ícone **do Grupo.**
3. No **grupo Create**, especifique um nome de grupo e, em nome de **aparelho,** selecione o aparelho Azure Migrate que está a utilizar para a descoberta da máquina.
1. Na lista de máquinas, selecione as máquinas que pretende adicionar ao grupo > **Criar**.

    ![Criar grupos](./media/how-to-create-a-group/create-group.png)

Agora pode utilizar este grupo quando [criar uma avaliação.](how-to-create-assessment.md)

## <a name="refine-a-group-with-dependency-mapping"></a>Refinar um grupo com mapeamento de dependência

O mapeamento de dependência ajuda-o a visualizar dependências através de máquinas. Normalmente, utiliza-se mapeamento de dependência quando se quer avaliar grupos de máquinas com níveis de confiança mais elevados.
- Ajuda-o a cruzar as dependências das máquinas, antes de fazer uma avaliação. 
- Também ajuda a planear eficazmente a sua migração para Azure, garantindo que nada é deixado para trás, evitando assim interrupções surpresa durante a migração.
- Pode descobrir sistemas interdependentes que precisam de migrar em conjunto e identificar se um sistema de funcionamento ainda serve os utilizadores, ou se é candidato ao desmantelamento em vez de migração.

Se já [criou o mapeamento](how-to-create-group-machine-dependencies.md)da dependência e quer refinar um grupo existente, faça o seguinte:

1. No separador **Servidores,** em **Azure Migrate: Server Assessment** tile, clique **em Grupos**.
2. Clique no grupo que pretende aperfeiçoar.
    - Se ainda não tiver configurado o mapeamento da dependência, a coluna **Dependencies** mostrará um estado de **instalação requer.** Para cada VM para o qual pretende visualizar dependências, clique em **Necessitar de instalação**. Instale um par de agentes em cada VM, antes de mapear as dependências da máquina. [Saiba mais](how-to-create-group-machine-dependencies.md).

        ![Adicionar mapeamento de dependência](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Se já criou o mapeamento da dependência, na página do grupo, clique em **Ver dependências** para abrir o mapa de dependência do grupo.

3. Depois de clicar Em **dependências**de visualização, o mapa de dependência do grupo mostra o seguinte:

    - Ligações TCP de entrada (clientes) e outbound (servidores) de e para todas as máquinas do grupo que têm os agentes de dependência instalados.
    - Máquinas dependentes que não têm os agentes de dependência instalados são agruparadas por números de porta.
    - As máquinas dependentes com agentes de dependência instalados são mostradas como caixas separadas.
    - Processos a correr dentro da máquina. Expanda cada caixa de máquinas para ver os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operativo, endereço MAC). Clique em cada caixa de máquinas para ver os detalhes.

4. Para ver dependências num intervalo de tempo à sua escolha, modifique o intervalo de tempo (uma hora por defeito) especificando datas de início e fim, ou a duração.

    > [!NOTE]
    > O intervalo de tempo pode chegar a uma hora. Se necessitar de um alcance mais longo, utilize o [Monitor Azure para consultar dados dependentes](how-to-create-group-machine-dependencies.md) por um período mais longo.

5. Depois de identificar as dependências que gostaria de adicionar ou remover do grupo, pode modificar o grupo. Utilize CTRL+Clique para adicionar ou remover máquinas do grupo.

    - Só se pode adicionar máquinas que tenham sido descobertas.
    - Adicionar e remover máquinas invalida avaliações passadas para um grupo.
    - Pode criar opcionalmente uma nova avaliação quando modificar o grupo.


## <a name="next-steps"></a>Passos seguintes

Aprenda a configurar e usar [o mapeamento](how-to-create-group-machine-dependencies.md) de dependência para criar grupos de alta confiança.


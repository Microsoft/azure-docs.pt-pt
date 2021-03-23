---
title: Servidores do grupo para avaliação com a Azure Migrate | Microsoft Docs
description: Descreve como agrupar servidores antes de executar uma avaliação com o serviço Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 0570ed73b86223025b250e269d7e2f358473f004
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780859"
---
# <a name="create-a-group-for-assessment"></a>Criar um grupo para avaliação

Este artigo descreve como criar grupos de servidores para avaliação com Azure Migrate: Discovery e assessment.

[Azure Migrate](migrate-services-overview.md) ajuda-o a migrar para Azure. A Azure Migrate fornece um centro centralizado para acompanhar a descoberta, avaliação e migração de infraestruturas, aplicações e dados no local para Azure. O hub fornece ferramentas Azure para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV). 

## <a name="grouping-servers"></a>Servidores de agrupamento

Você reúne servidores em grupos para avaliar se eles são adequados para a migração para Azure, e para obter Azure tamanho e estimativas de custos para eles. Há algumas formas de criar grupos:

- Se souber os servidores que precisam de ser migrados juntos, pode criar manualmente o grupo em Azure Migrate.
- Se não tiver a certeza sobre os servidores que precisam de ser agrupados, pode utilizar a funcionalidade de visualização de dependência no Azure Migrate para criar grupos. 

> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Governo de Azure.

## <a name="create-a-group-manually"></a>Criar um grupo manualmente

Pode criar um grupo ao mesmo tempo que [cria uma avaliação.](how-to-create-assessment.md)

Se pretender criar um grupo manualmente fora da criação de uma avaliação, faça o seguinte:

1. No projeto Azure Migrate > **Visão Geral,** clique em **Avaliar e migrar servidores**. In **Azure Migrate: Discovery and assessment**, click **Groups**
    - Se ainda não adicionou a ferramenta Azure Migrate: Discovery and assessment, clique para adicioná-la. [Saiba mais](how-to-assess.md).
    - Se ainda não criou um projeto Azure Migrate, [saiba mais.](./create-manage-projects.md)

    ![Selecione grupos](./media/how-to-create-a-group/select-groups.png)

2. Clique no ícone **grupo.**
3. No **Grupo Create**, especifique um nome de grupo e, em nome do **aparelho,** selecione o aparelho Azure Migrate que está a utilizar para a descoberta do servidor.
4. Na lista de servidores, selecione os servidores que pretende adicionar ao grupo > **Criar**.

    ![Criar grupos](./media/how-to-create-a-group/create-group.png)

Pode agora utilizar este grupo quando [criar uma avaliação Azure VM](how-to-create-assessment.md) ou [uma avaliação da Solução VMware Azure (AVS)](how-to-create-azure-vmware-solution-assessment.md) ou [uma avaliação do Azure SQL](how-to-create-azure-sql-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Refinar um grupo com mapeamento de dependência

O mapeamento de dependência ajuda-o a visualizar dependências através dos servidores. Normalmente, usa mapeamento de dependência quando pretende avaliar grupos de servidores com níveis de confiança mais elevados.
- Ajuda-o a cruzar as dependências do servidor antes de fazer uma avaliação. 
- Também ajuda a planear eficazmente a sua migração para Azure, garantindo que nada seja deixado para trás, evitando assim interrupções surpresa durante a migração.
- Pode descobrir sistemas interdependentes que precisam de migrar em conjunto e identificar se um sistema de funcionamento ainda está a servir os utilizadores, ou se é candidato ao desmantelamento em vez de migração.

Se já [criou o mapeamento de dependência](how-to-create-group-machine-dependencies.md)e quer refinar um grupo existente, faça o seguinte:

1. No separador **Servidores,** em **Azure Migrate: Discovery and assessment** tile, clique em **Grupos**.
2. Clique no grupo que pretende refinar.
    - Se ainda não tiver configurado o mapeamento de dependência, a coluna **Dependencies** mostrará um **estado de instalação requer.** Para cada servidor para o qual pretende visualizar dependências, clique em **Requerer a instalação**. Instale um par de agentes em cada servidor, antes de mapear as dependências do servidor. [Saiba mais](how-to-create-group-machine-dependencies.md).

        ![Adicionar mapeamento de dependência](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Se já configurar o mapeamento de dependência, na página de grupo, clique em **Ver dependências** para abrir o mapa de dependência do grupo.

3. Depois de clicar **nas dependências do Ver,** o mapa de dependência do grupo mostra o seguinte:

    - Ligações TCP de e para todos os servidores do grupo que tenham os agentes de dependência instalados.
    - Os servidores dependentes que não têm os agentes de dependência instalados são agrupados por números de porta.
    - Os servidores dependentes com agentes de dependência instalados são apresentados como caixas separadas.
    - Processos a correr dentro do servidor. Expandir cada caixa de servidor para ver os processos.
    - Propriedades do servidor (incluindo FQDN, sistema operativo, endereço MAC). Clique em cada caixa de servidor para ver os detalhes.

4. Para visualizar as dependências num intervalo de tempo à sua escolha, modifique o intervalo de tempo (uma hora por padrão) especificando datas de início e fim, ou a duração.

    > [!NOTE]
    > O intervalo de tempo pode chegar a uma hora. Se precisar de um alcance mais longo, utilize [o Azure Monitor para consultar dados dependentes](how-to-create-group-machine-dependencies.md) por um período mais longo.

5. Depois de identificar as dependências que gostaria de adicionar ou remover do grupo, pode modificar o grupo. Utilize o Ctrl+Click para adicionar ou remover servidores do grupo.

    - Só é possível adicionar servidores que tenham sido descobertos.
    - Adicionar e remover servidores invalida avaliações passadas para um grupo.
    - Pode, opcionalmente, criar uma nova avaliação quando modificar o grupo.


## <a name="next-steps"></a>Passos seguintes

Aprenda a configurar e usar [o mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.
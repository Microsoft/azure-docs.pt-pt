---
title: Máquinas de grupo para avaliação com Azure Migrate | Microsoft Docs
description: Descreve como agrupar máquinas antes de fazer uma avaliação com o serviço Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 364b5949e944a4317aa25f1f1b12545122881cec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752112"
---
# <a name="create-a-group-for-assessment"></a>Criar um grupo para avaliação

Este artigo descreve como criar grupos de máquinas para avaliação com Azure Migrate: Avaliação do servidor.

[Azure Migrate](migrate-services-overview.md) ajuda-o a migrar para Azure. A Azure Migrate fornece um centro centralizado para acompanhar a descoberta, avaliação e migração de infraestruturas, aplicações e dados no local para Azure. O hub fornece ferramentas Azure para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV). 

## <a name="grouping-machines"></a>Máquinas de agrupamento

Você recolhe máquinas em grupos para avaliar se são adequadas para a migração para Azure, e para obter Azure tamanho e estimativas de custos para eles. Há algumas formas de criar grupos:

- Se conhecer as máquinas que precisam de ser migradas juntas, pode criar manualmente o grupo em Azure Migrate.
- Se não tiver a certeza sobre as máquinas que precisam de ser agrupadas, pode utilizar a funcionalidade de visualização de dependência no Azure Migrate para criar grupos. 

> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Governo de Azure.

## <a name="create-a-group-manually"></a>Criar um grupo manualmente

Pode criar um grupo ao mesmo tempo que [cria uma avaliação.](how-to-create-assessment.md)

Se pretender criar um grupo manualmente fora da criação de uma avaliação, faça o seguinte:

1. No projeto Azure Migrate > **Visão Geral,** clique em **Avaliar e migrar servidores**. In **Azure Migrate: Avaliação do servidor**, clique em **Grupos**
    - Se ainda não adicionou a ferramenta Azure Migrate: Server Assessment, clique em adicioná-la. [Saiba mais](how-to-assess.md).
    - Se ainda não criou um projeto Azure Migrate, [saiba mais.](./create-manage-projects.md)

    ![Selecione grupos](./media/how-to-create-a-group/select-groups.png)

2. Clique no ícone **grupo.**
3. No **Grupo Create**, especifique um nome de grupo e, em nome do **aparelho,** selecione o aparelho Azure Migrate que está a utilizar para a descoberta da máquina.
4. Na lista de máquinas, selecione as máquinas que pretende adicionar ao grupo > **Criar**.

    ![Criar grupos](./media/how-to-create-a-group/create-group.png)

Agora pode utilizar este grupo quando [criar uma avaliação Azure VM](how-to-create-assessment.md) ou [uma avaliação da Solução VMware Azure VMware (AVS).](how-to-create-azure-vmware-solution-assessment.md) Note que pode criar uma avaliação AVS em grupos apenas VMware VMs. 

## <a name="refine-a-group-with-dependency-mapping"></a>Refinar um grupo com mapeamento de dependência

O mapeamento de dependência ajuda-o a visualizar dependências através de máquinas. Normalmente, usa mapeamento de dependência quando quer avaliar grupos de máquinas com níveis de confiança mais elevados.
- Ajuda-o a cruzar as dependências das máquinas antes de fazer uma avaliação. 
- Também ajuda a planear eficazmente a sua migração para Azure, garantindo que nada seja deixado para trás, evitando assim interrupções surpresa durante a migração.
- Pode descobrir sistemas interdependentes que precisam de migrar em conjunto e identificar se um sistema de funcionamento ainda está a servir os utilizadores, ou se é candidato ao desmantelamento em vez de migração.

Se já [criou o mapeamento de dependência](how-to-create-group-machine-dependencies.md)e quer refinar um grupo existente, faça o seguinte:

1. No separador **Servidores,** em **Azure Migrate: Telha de avaliação** do servidor, clique em **Grupos**.
2. Clique no grupo que pretende refinar.
    - Se ainda não tiver configurado o mapeamento de dependência, a coluna **Dependencies** mostrará um **estado de instalação requer.** Para cada VM para o qual pretende visualizar dependências, clique em **Requerer a instalação**. Instale um par de agentes em cada VM, antes de mapear as dependências da máquina. [Saiba mais](how-to-create-group-machine-dependencies.md).

        ![Adicionar mapeamento de dependência](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Se já configurar o mapeamento de dependência, na página de grupo, clique em **Ver dependências** para abrir o mapa de dependência do grupo.

3. Depois de clicar **nas dependências do Ver,** o mapa de dependência do grupo mostra o seguinte:

    - Ligações TCP de e para todas as máquinas do grupo que tenham os agentes de dependência instalados.
    - As máquinas dependentes que não têm os agentes de dependência instalados são agrupadas por números de porta.
    - As máquinas dependentes com agentes de dependência instalados são apresentadas como caixas separadas.
    - Processos que correm dentro da máquina. Expandir cada caixa de máquinas para ver os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operativo, endereço MAC). Clique em cada caixa de máquinas para ver os detalhes.

4. Para visualizar as dependências num intervalo de tempo à sua escolha, modifique o intervalo de tempo (uma hora por padrão) especificando datas de início e fim, ou a duração.

    > [!NOTE]
    > O intervalo de tempo pode chegar a uma hora. Se precisar de um alcance mais longo, utilize [o Azure Monitor para consultar dados dependentes](how-to-create-group-machine-dependencies.md) por um período mais longo.

5. Depois de identificar as dependências que gostaria de adicionar ou remover do grupo, pode modificar o grupo. Utilize ctrl+Clique para adicionar ou remover máquinas do grupo.

    - Só é possível adicionar máquinas que tenham sido descobertas.
    - Adicionar e remover máquinas invalida avaliações passadas para um grupo.
    - Pode, opcionalmente, criar uma nova avaliação quando modificar o grupo.


## <a name="next-steps"></a>Passos seguintes

Aprenda a configurar e usar [o mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.
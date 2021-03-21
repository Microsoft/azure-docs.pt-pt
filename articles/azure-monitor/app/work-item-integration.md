---
title: Integração de Item de Trabalho (pré-visualização) - Insights de Aplicações
description: Saiba como criar itens de trabalho no GitHub ou Azure DevOps com dados de Insights de Aplicação incorporados nos mesmos.
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731923"
---
# <a name="work-item-integration-preview"></a>Integração de Artigos de Trabalho (pré-visualização)

A funcionalidade de integração de artigos de trabalho permite-lhe criar facilmente itens de trabalho no GitHub ou Azure DevOps que tenham dados relevantes de Insights de Aplicação incorporados nos mesmos.

> [!IMPORTANT]
> A integração do Item de Trabalho está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-and-configure-a-work-item-template"></a>Criar e configurar um modelo de item de trabalho

1. Para criar um modelo de item de trabalho, aceda ao seu recurso Application Insights e à esquerda sob *configurar* **Itens de trabalho selecionados** e, em seguida, no selecionador superior **Criar um novo modelo**

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Screenshot do separador Itens de Trabalho com criar um novo modelo selecionado." lightbox="./media/work-item-integration/create-work-item-template.png":::

    Também pode criar um modelo de produto de trabalho a partir do separador detalhes de transações de ponta a ponta, se não existir atualmente nenhum modelo. Selecione um evento e no seleto direito **Crie um item de trabalho** e, em seguida, inicie com um modelo de **livro**.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Screenshot do separador de detalhes de transações de ponta a ponta com a criação de um item de trabalho, comece com um modelo de livro selecionado." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Depois de selecionar **criar um novo modelo,** pode escolher os seus sistemas de rastreio, nomear o seu livro, ligar ao seu sistema de rastreio selecionado e escolher uma região para armazenar o modelo (o padrão é a região em que o seu recurso Application Insights está localizado). Os parâmetros URL são o URL padrão para o seu repositório, por exemplo, `https://github.com/myusername/reponame` ou `https://mydevops.visualstudio.com/myproject` .

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Screenshot de criar um novo modelo de livro de artigo de trabalho.":::

## <a name="create-a-work-item"></a>Criar um item de trabalho

 Pode aceder ao seu novo modelo a partir de quaisquer dados de transações de ponta a ponta que possa aceder a partir de Desempenho, Falhas, Disponibilidade ou outros separadores.

1. Para criar um item de trabalho aceda a detalhes de transações de ponta a ponta, selecione um evento e, em seguida, **selecione Criar item de trabalho** e escolher o seu modelo de produto de trabalho.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Screenshot do separador de detalhes de transação de ponta a fim com criar item de trabalho selecionado." lightbox="./media/work-item-integration/create-work-item.png":::

1. Um novo separador no seu navegador abrir-se-á para o seu sistema de rastreio selecionado. No Azure DevOps pode criar um bug ou tarefa, e no GitHub pode criar um novo problema no seu repositório. Um novo item de trabalho é automaticamente criado com informação contextual fornecida pela Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Screenshot do problema GitHub criado automaticamente" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Screenshot de criar automaticamente bug em Azure DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Editar um modelo

Para editar o seu modelo, aceda ao separador **Itens de Trabalho** em *Configurar* e selecione o ícone de lápis ao lado do livro que pretende atualizar.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Screenshot do separador de item de trabalho com o ícone de lápis de edição selecionado.":::

Selecione ![ editar o ícone de edição ](./media/work-item-integration/edit-icon.png) na parte superior para começar a editar o modelo. Os modelos de item de trabalho são baseados em [livros de trabalho do Monitor Azure](../visualize/workbooks-overview.md). A informação do item de trabalho é gerada utilizando o idioma de consulta de palavras-chave. Pode modificar as consultas para adicionar mais contexto essencial à sua equipa. Quando terminar a edição, guarde o livro selecionando o ![ ícone de guardar o ícone de poupança na barra de ](./media/work-item-integration/save-icon.png) ferramentas superior.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Screenshot do livro de trabalho do modelo de artigo de trabalho no modo de edição." lightbox="./media/work-item-integration/edit-workbook.png":::

Pode criar mais do que uma configuração de produto de trabalho e ter um livro personalizado para atender a cada cenário. Os livros também podem ser implementados pelo Azure Resource Manager garantindo implementações padrão em todos os seus ambientes.
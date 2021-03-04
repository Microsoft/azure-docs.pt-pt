---
title: Azure Monitor vê designer para resumo de conversão de livros e acesso
description: Permissões necessárias para aceder a livros de trabalho durante a transição de vistas no Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 42cb8db0f67d3a01b2e8443e6cb7c47f0fc44c4b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043395"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Ver designer para resumo de conversão de livros de trabalho e acesso
[O view designer](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar visualizações personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e linhas de tempo. Estão a ser eliminados e substituídos por livros de trabalho, que fornecem funcionalidades adicionais. Este artigo detalha como pode criar um resumo geral e permissões necessárias para aceder a livros de trabalho.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Criando o seu Resumo do Espaço de Trabalho a partir do Painel de Instrumentos Azure
Ver utilizadores de designers pode estar familiarizado com ter um azulejo geral para representar um conjunto de pontos de vista. Para manter uma visão geral visual como o resumo do espaço de trabalho do designer de visualização, os livros de trabalho oferecem passos fixados, que podem ser fixados ao seu [painel de instrumentos do portal Azure](../../azure-portal/azure-portal-dashboards.md). Tal como os azulejos de visão geral no resumo do Workspace, os artigos de livro fixos ligar-se-ão diretamente à vista do livro.

Pode tirar partido do alto nível de funcionalidades de personalização fornecidas com dashboards Azure, que permitem atualização automática, movimento, dimensionamento e filtragem adicional para os seus itens e visualizações fixados. 

![A screenshot mostra um dashboard Azure personalizado intitulado Workspace Summary.](media/view-designer-conversion-access/dashboard.png)

Crie um novo painel de instrumentos Azure ou selecione um dashboard existente para começar a fixar itens de livros de trabalho.

Para fixar um item individual, terá de ativar o ícone do pino para o seu passo específico. Para tal, selecione o botão **de edição** correspondente para o seu passo e, em seguida, selecione o ícone de engrenagem para abrir **Definições Avançadas**. Verifique a opção para **mostrar sempre o ícone do pino neste passo**, e aparecerá um ícone de pino no canto superior direito do seu passo. Este pino permite-lhe fixar visualizações específicas no seu painel de instrumentos, como os azulejos de visão geral.

![Passo pin](media/view-designer-conversion-access/pin-step.png)


Pode também pretender fixar várias visualizações do Livro de Trabalho ou de todo o conteúdo do Livro a um dashboard. Para fixar todo o livro, **selecione Editar** na barra de ferramentas superior para alternar **o Modo de Edição**. Aparecerá um ícone de pino, permitindo-lhe fixar todo o item do Livro de Trabalho ou todos os passos e visualizações individuais no livro.

![Pin todos](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Permissões de partilha e visualização 
Os livros têm o benefício de ser um documento privado ou partilhado. Por predefinição, os livros de trabalho guardados serão guardados nos **meus relatórios**, o que significa que apenas o criador pode ver este livro.

Pode partilhar os seus livros selecionando o ícone **Partilhar** a partir da barra de ferramentas superior enquanto está no **Modo de Edição.** Será solicitado que mude o seu livro para **Relatórios Partilhados,** o que gerará um link que proporciona acesso direto ao livro.

Para que um utilizador veja um livro partilhado, deve ter acesso ao grupo de subscrição e recursos em que o livro é guardado.

![Acesso baseado em assinaturas](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Passos seguintes

- [Tarefas comuns](view-designer-conversion-tasks.md)
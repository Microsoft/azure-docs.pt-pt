---
title: Azure Monitor vê designer para resumo de conversão de livros de livros e acesso
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658851"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Ver designer para reconversão de livros de trabalho e acesso
O designer de [visualização](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar vistas personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e cronologias. Estão a ser gradualmente eliminados e substituídos por livros de trabalho, que fornecem funcionalidades adicionais. Este artigo detalha como pode criar um resumo geral e permissões necessárias para aceder a livros de trabalho.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Criando o seu resumo do espaço de trabalho a partir do Painel De Instrumentos Azure
Ver os utilizadores de designers podem estar familiarizados com ter um azulejo de visão geral para representar um conjunto de vistas. Para manter uma visão geral visual como o resumo do espaço de trabalho do designer de vistas, os livros de trabalho oferecem passos presos, que podem ser fixados no seu [portal Azure dashboard](../../azure-portal/azure-portal-dashboards.md). Tal como os azulejos de visão geral no resumo do Workspace, os itens de livro fixados ligar-se-ão diretamente à vista do livro.

Pode aproveitar o elevado nível de funcionalidades de personalização fornecidas com dashboards Azure, que permite atualização automática, movimento, dimensionamento e filtragem adicional para os seus itens e visualizações fixados. 

![Dashboard](media/view-designer-conversion-access/dashboard.png)

Crie um novo painel de instrumentos Azure ou selecione um painel de instrumentos existente para começar a fixar itens de livros.

Para fixar o item individual, terá de ativar o ícone do pino para o seu passo específico. Para isso, selecione o botão **Editar** correspondente para o seu passo e, em seguida, selecione o ícone de engrenagem para abrir **Definições Avançadas**. Verifique a opção de **mostrar sempre o ícone do pino neste passo**, e aparecerá um ícone pino no canto superior direito do seu passo. Este pino permite-lhe fixar visualizações específicas no seu painel de instrumentos, como os azulejos de visão geral.

![Passo pino](media/view-designer-conversion-access/pin-step.png)


Também pode desejar fixar múltiplas visualizações do Livro ou de todo o conteúdo do Livro num dashboard. Para fixar todo o livro, **selecione Editar** na barra de ferramentas superior para alternar modo **de edição**. Aparecerá um ícone pino, permitindo-lhe fixar todo o item do Livro ou todos os passos e visualizações individuais no livro.

![Pin all](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Permissões de partilha e visualização 
Os livros têm o benefício de ser um documento privado ou partilhado. Por padrão, os livros guardados serão guardados ao abrigo de **My Reports**, o que significa que apenas o criador pode ver este livro.

Pode partilhar os seus livros selecionando o ícone **Partilhar** a partir da barra de ferramentas superior estivaenquanto no **Modo De Edição**. Será solicitado a mover o seu livro para **Relatórios Partilhados,** que irá gerar uma ligação que proporciona acesso direto ao livro.

Para que um utilizador veja um livro partilhado, deve ter acesso tanto ao grupo de subscrição como ao grupo de recursos em que o livro é guardado.

![Acesso baseado em subscrição](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Passos seguintes

- [Tarefas comuns](view-designer-conversion-tasks.md)
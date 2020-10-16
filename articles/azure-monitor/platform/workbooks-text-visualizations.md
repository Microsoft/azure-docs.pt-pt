---
title: Visualizações de texto de livro do Azure Monitor
description: Saiba mais sobre todas as visualizações de texto do livro do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89664924"
---
# <a name="text-visualizations"></a>Visualizações de texto

Os livros permitem que os autores incluam blocos de texto nos seus livros. O texto pode ser a análise humana da telemetria, informação para ajudar os utilizadores a interpretar os seus dados, títulos de secção, etc.

[![Screenshot da tabela de texto apdex](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

O texto é adicionado através de um controlo Markdown, que proporciona controlo de formatação completa. Estes incluem diferentes estilos de posição e fonte, hiperligações, mesas, etc.

Modo de Edição:

![Screenshot de um passo de texto no modo de edição.](./media/workbooks-text-visualizations/text-edit-mode.png)

Modo de pré-visualização:

![Screenshot de um passo de texto no modo de edição no separador de pré-visualização.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Adicionar um controlo de texto

1. Mude o livro para editar o modo clicando no item da barra de ferramentas **Editar.**
2. Utilize o link **de texto Adicionar** para adicionar um controlo de texto ao livro.
3. Adicione Markdown no campo dos editores.
4. Utilize a opção *Text Style* para alternar entre a marcação simples e a marcação embrulhada com o estilo padrão de informação/aviso/sucesso/erro do portal Azure.
5. Utilize o **separador Pré-visualização** para ver como será o seu conteúdo. Durante a edição, a pré-visualização mostrará o conteúdo dentro de uma área de barra de deslocamento para limitar o seu tamanho; no entanto, em tempo de execução, o conteúdo de marcação expandir-se-á para preencher o espaço de que necessita, sem barras de deslocamento.
6. Selecione o botão **de edição feito** para completar a edição do passo.

> [!TIP]
> Utilize esta [folha de batota Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) para aprender sobre diferentes opções de formatação.

## <a name="text-styles"></a>Estilos de texto

Os seguintes estilos de texto estão disponíveis para passo de texto:

| Estilo     | Explicação                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | Não é aplicada formatação adicional.                                                      |
| `info`    | O estilo "info" do portal, com um  `ℹ` ícone ou ícone semelhante e geralmente fundo azul.      |
| `error`   | O estilo de "erro" do portal, com um `❌` ícone ou ícone semelhante e fundo geralmente vermelho.     |
| `success` | O estilo de "sucesso" do portal, com um `✔` ícone ou ícone semelhante e geralmente o fundo verde.  |
| `upsell`  | O estilo "upsell" do portal, com um `🚀` ícone ou ícone semelhante e geralmente o fundo roxo. |
| `warning` | O estilo de "aviso" do portal, com um `⚠` ícone ou ícone semelhante e fundo geralmente azul.   |

Em vez de escolher um estilo específico, também pode escolher um parâmetro de texto como a fonte do estilo. O valor do parâmetro deve ser um dos valores de texto acima. A ausência de um valor ou qualquer valor não reconhecido será tratada como `plain` estilo.

Exemplo de estilo de informação:

![Screenshot de como é o estilo de informação.](./media/workbooks-text-visualizations/text-preview-info-style.png)

Exemplo de estilo de aviso:

![Screenshot de como é o estilo de aviso.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar um [gráfico em livros de trabalho.](workbooks-chart-visualizations.md)
* Saiba como criar uma grelha em livros de [trabalho.](workbooks-grid-visualizations.md)

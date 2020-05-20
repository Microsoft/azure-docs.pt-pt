---
title: Formato Markdown - QnA Maker
description: Segue-se a lista de formatos de marcação que pode utilizar no texto de resposta do QnA Maker.
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: 38532783c38263b3f1364e30125414958eab1394
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652765"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Formato de markdown suportado no texto de resposta qnA Maker

As lojas QnA Maker respondem texto como marcação. Há muitos sabores de marcação. Para se certificar de que o texto de resposta é devolvido e apresentado corretamente, utilize esta referência.

Utilize o tutorial **[CommonMark](https://commonmark.org/help/tutorial/index.html)** para validar o seu Markdown. O tutorial tem uma funcionalidade **Experimente** para validação rápida de cópia/pasta.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Quando utilizar a edição de texto rico versus marcação

[A edição de](How-To/edit-knowledge-base.md#add-an-editorial-qna-set) respostas de texto rico permite-lhe, como autor, utilizar uma barra de ferramentas para selecionar e formatar rapidamente texto.

O markdown é uma ferramenta melhor quando é necessário regenerar automaticamente o conteúdo para criar bases de conhecimento para serem importadas como parte de um oleoduto CI/CD ou para testes de [lote](Quickstarts/batch-testing.md).

## <a name="supported-markdown-format"></a>Formato de marcação suportado

Segue-se a lista de formatos de marcação que pode utilizar no texto de resposta do QnA Maker.

|Objetivo|Formato|Markdown de exemplo|Composição<br>como exibido no Chat bot|
|--|--|--|--|
Uma nova linha entre duas frases.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formato nova linha entre duas frases](./media/qnamaker-concepts-datasources/format-newline.png)|
|Cabeçalhos de h1 a h6, o número de `#` notas que cabeçalho. 1 `#` é o h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formato com cabeçalhos de marcação](./media/qnamaker-concepts-datasources/format-headers.png)<br>![formato com cabeçalhos de marcação H1 a H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Itálico |`*text*`|`How do I create a bot with *QnA Maker*?`|![formato com itálico](./media/qnamaker-concepts-datasources/format-italics.png)|
|Forte (arrojado)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formato com forte marcação para arrojado](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL para link|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![formato para URL (hiperligação)](./media/qnamaker-concepts-datasources/format-url.png)|
|*URL para imagem pública|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formato para URL de imagem pública ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Rasurado|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formato para strikethrough](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Arrojado e itálico|`***text***`|`How can I create a ***QnA Maker*** bot?`|![formato para arrojado e itálico](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|URL arrojado para link|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![formato para URL arrojado](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL itálico para link|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![formato para URL itálico](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Símbolos de marcação de fuga|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![formato para URL itálico](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Lista ordenada|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>O exemplo anterior utiliza a numeração automática incorporada na marcação.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>O exemplo anterior utiliza numeração explícita.|![formato para lista ordenada](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Lista não ordenada|`\n * item1 \n * item2`<br>ou<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![formato para lista não ordenada](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Listas aninhadas|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Pode sintethar listas ordenadas e desordenadas juntas. O `\t` separador, indica o nível de entalhe do elemento da criança.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formato para lista não ordenada aninhada](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formato para lista ordenada aninhada](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*O Fabricante QnA não processa a imagem de forma alguma. É o papel da aplicação do cliente para renderizar a imagem.

Se pretender adicionar conteúdo utilizando APIs de base de conhecimento atualizada/substituição e o conteúdo/ficheiro contiver tags html, pode preservar o HTML no seu ficheiro, garantindo que a abertura e o fecho das etiquetas são convertidos no formato codificado.

| Preservar HTML  | Representação no pedido da API  | Representação em KB |
|-----------|---------|-------------------------|
| Sim | \&Lt;br \& gt; | &lt;br&gt; |
| Sim | \&Lt;h3 \& gt;cabeçalho \& lt;/h3 \& gt; | &lt;h3 &gt; cabeçalho &lt; /h3&gt; |

Adicionalmente, o CR LF (\r\n) é convertido para \n no KB. LF (\n) é mantido como está. Se quiser escapar a qualquer sequência de fuga como um \t ou \n, pode usar backslash, por exemplo: \\ \\ 'r \\ \\ n' e \\ \\ 't'

## <a name="next-steps"></a>Passos seguintes

Reveja os [formatos](reference-tsv-format-batch-testing.md)de ficheiros de teste de lote .

---
title: Como usar etiquetas de mesa para treinar o seu modelo de formulário personalizado - Form Recogniser
titleSuffix: Azure Cognitive Services
description: Aprenda a utilizar eficazmente a etiqueta de mesa supervisionada.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467863"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Use etiquetas de mesa para treinar o seu modelo de formulário personalizado

Neste artigo, você vai aprender a treinar o seu modelo de formulário personalizado com etiquetas de mesa (etiquetas). Alguns cenários requerem uma rotulagem mais complexa do que simplesmente alinhar pares de valores-chave. Tais cenários incluem extrair informação de formas com estruturas hierárquicas complexas ou encontrar itens que não são detetados e extraídos automaticamente pelo serviço. Nestes casos, pode utilizar etiquetas de mesa para treinar o seu modelo de formulário personalizado.

## <a name="when-should-i-use-table-tags"></a>Quando devo usar etiquetas de mesa?

Aqui estão alguns exemplos de quando se utilizam etiquetas de mesa:

- Há dados que deseja extrair apresentados como tabelas nas suas formas, e a estrutura das tabelas são significativas. Por exemplo, cada linha da tabela representa um item e cada coluna da linha representa uma característica específica desse item. Neste caso, pode utilizar uma etiqueta de tabela onde uma coluna representa funcionalidades e uma linha representa informações sobre cada recurso.
- Há dados que deseja extrair que não são apresentados em campos de formulário específicos, mas semântica, os dados podem caber numa grelha bidimensional. Por exemplo, o seu formulário tem uma lista de pessoas, e inclui, um primeiro nome, um sobrenome e um endereço de e-mail. Gostaria de extrair esta informação. Neste caso, você pode usar uma etiqueta de mesa com nome próprio, apelido e endereço de e-mail como colunas e cada linha é preenchida com informações sobre uma pessoa da sua lista.

> [!NOTE]
> O Form Recogniser encontra e extrai automaticamente todas as tabelas dos seus documentos, quer as tabelas estejam ou não marcadas. Portanto, não precisa de rotular todas as tabelas do seu formulário com uma etiqueta de mesa e as suas etiquetas de mesa não têm de replicar a estrutura da mesma tabela encontrada na sua forma. As tabelas extraídas automaticamente pelo Form Recogniser serão incluídas na secção pageResults da saída JSON.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Criar uma etiqueta de mesa com ferramenta de teste de OCR de formulário (FOTT)
<!-- markdownlint-disable MD004 -->
* Determine se deseja uma etiqueta de mesa **dinâmica** ou **de tamanho fixo.** Se o número de linhas variar de documento para documento, utilize uma etiqueta de tabela dinâmica. Se o número de linhas for consistente nos seus documentos, utilize uma etiqueta de mesa de tamanho fixo.
* Se a sua etiqueta de tabela for dinâmica, defina os nomes das colunas e o tipo de dados e formato para cada coluna.
* Se a sua tabela for de tamanho fixo, defina o nome da coluna, nome da linha, tipo de dados e formato para cada tag.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Configurar uma etiqueta de mesa":::

## <a name="label-your-table-tag-data"></a>Rotular os dados da sua etiqueta de tabela

* Se o seu projeto tiver uma etiqueta de mesa, pode abrir o painel de rotulagem e preencher a etiqueta como você iria rotular campos de valor-chave.
:::image type="content" source="media/table-labeling.png" alt-text="Etiqueta com etiquetas de mesa":::

## <a name="next-steps"></a>Passos seguintes

Siga o nosso quickstart para treinar e use o seu modelo personalizado De Reconhecimento de Formulários:

> [!div class="nextstepaction"]
> [Treine com etiquetas usando a ferramenta de rotulagem da amostra](quickstarts/label-tool.md)

## <a name="see-also"></a>Ver também

* [O que é o Reconhecedor de Formato?](overview.md)
>

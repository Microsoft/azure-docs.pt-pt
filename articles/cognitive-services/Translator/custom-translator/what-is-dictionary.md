---
title: O que é um dicionário? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Um dicionário é um documento alinhado que especifica uma lista de expressões ou frases (e respetivas traduções) que pretende que sempre Microsoft Translator para traduzir da mesma forma. Por vezes, também são chamados de dicionários glossários ou bases de termo.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 87ea4edd9bb548701d6bcf63dd542ed6e35d9f7f
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585558"
---
# <a name="what-is-a-dictionary"></a>O que é um dicionário?

Um dicionário é um par alinhado de documentos que especifica uma lista de expressões ou frases e respetivas traduções correspondentes. Utilize um dicionário no seu treinamento, quando quiser Microsoft Translator para traduzir sempre quaisquer instâncias da expressão de origem ou frase, através da tradução que forneceu no dicionário. Às vezes são chamados de dicionários glossários ou bases de termo. Pode considerar o dicionário como uma de força bruta "copiar e substituir" para os termos da lista.

Dicionários só funcionam em projetos em pares de idiomas que têm um sistema de tradução automática neuronal (NMT) do Microsoft totalmente suportado por trás delas. [Veja a lista completa das linguagens](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Dicionário da frase 
Quando inclui um dicionário de frase no seu modelo de formação, qualquer palavra ou frase listado é traduzido de forma que especificou. O restante da sentença é traduzido como de costume. Pode usar um dicionário de frase para especificar as frases que não devem ser traduzidos, fornecendo a mesma frase sem tradução no ficheiro de origem e de destino no dicionário.

## <a name="sentence-dictionary"></a>Dicionário da sentença
O dicionário de sentença permite-lhe especificar uma tradução de destino exato para uma frase de origem. Para obter uma correspondência de dicionário de frase ocorrer, a frase inteira de submetido tem de corresponder a entrada de dicionário de origem.  Se apenas uma parte da sentença corresponde, a entrada não corresponde ao.  Quando é detetada uma correspondência, a entrada de destino do dicionário frase vai ser devolvida.

## <a name="dictionary-only-trainings"></a>Treinamentos somente de dicionário
Pode preparar um modelo a utilizar apenas os dados do dicionário. Para tal, selecione apenas o documento de dicionário (ou vários documentos de dicionário) que pretende incluir e toque no modelo de criar. Como se trata de um treinamento de só de dicionário, não existe um número mínimo de frases de treinamento necessário. Seu modelo, normalmente, irá concluir treinamento muito mais rápido do que um treinamento padrão.  Os modelos resultantes irão utilizar os modelos de linha de base da Microsoft para a tradução com a adição de dicionários que adicionou.  Não obterá um relatório de teste.

>[!Note]
>Tradutor personalizado não frase alinhar os ficheiros de dicionário, pelo que é importante que há um número igual de expressões de origem e de destino / frases em seu dicionário documentos e que eles estão alinhados com precisão.

## <a name="recommendations"></a>Recomendações

- Dicionários não são um substituto para um modelo preparado com dados de treinamento.  Dicionários, essencialmente, localizar e substituem palavras ou frases.  Permitir que o sistema Aprenda com seu material de treinamento em frases completas é, geralmente, uma opção melhor do que usando um dicionário. 
- O dicionário de frase deve ser usado com moderação. Quando uma frase dentro de uma sentença é substituída, o contexto dentro daquela frase for perdido ou limitado para traduzir o resto da sentença. O resultado é que, ao mesmo tempo a frase ou word dentro da sentença, serão traduzidas, de acordo com o dicionário de frase, a qualidade geral de tradução da sentença, muitas vezes, será afetado.
- O dicionário de frase funciona bem para substantivos compostos, como nomes de produtos ("Microsoft SQL Server"), nomes apropriados ("Cidade de Hamburgo") ou recursos do produto ("tabela dinâmica"). Ele não funciona igualmente bem para verbos ou os adjetivos porque estes são normalmente altamente inflected na origem ou no idioma de destino. Evite as entradas de dicionário de frase para qualquer coisa além de substantivos compostas. 
- Ao usar um dicionário, capitalização e pontuação na suas traduções irão refletir a capitalização e pontuação fornecidos em seu arquivo de destino. Capitalização e pontuação são ignorados ao tentar identificar correspondências entre a sua frase de entrada e as frases de origem no seu arquivo de dicionário. Por exemplo, digamos que preparado um inglês para espanhol sistema que é utilizado um dicionário que especificado "Cidade de Hamburgo" no arquivo de origem e "Cidade de Hamburgo" no ficheiro de destino. Se solicitei a tradução de uma sentença que incluía a frase "Cidade de Hamburgo", em seguida, "Cidade de Hamburgo" corresponderia ao meu arquivo de dicionário para a entrada "Cidade de Hamburgo" e seriam mapeados para "Cidade de Hamburgo", na minha tradução final.
- Se uma palavra aparece mais do que uma vez num arquivo de dicionário, o sistema utilizará sempre a última entrada fornecida. Seu dicionário não deve conter várias traduções da mesma palavra.

## <a name="next-steps"></a>Passos Seguintes

- Leia sobre [diretrizes sobre formatos de documento](document-formats-naming-convention.md).

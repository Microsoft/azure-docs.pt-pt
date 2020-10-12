---
title: O que é um dicionário? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Um dicionário é um documento alinhado que especifica uma lista de frases ou frases (e suas traduções) que sempre pretende que o Microsoft Tradutor traduza da mesma forma. Os dicionários são por vezes também chamados glossários ou bases de termo.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f4c3d23f6abbdc20d210e5ddda6c527d27654bb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510764"
---
# <a name="what-is-a-dictionary"></a>O que é um dicionário?

Um dicionário é um par de documentos alinhado que especifica uma lista de frases ou frases e suas traduções correspondentes. Use um dicionário no seu treino, quando quiser que o Microsoft Tradutor traduza sempre quaisquer instâncias da frase ou frase de origem, utilizando a tradução que forneceu no dicionário. Os dicionários são por vezes chamados glossários ou bases de termo. Pode pensar no dicionário como uma força bruta "copiar e substituir" por todos os termos que listar. Além disso, o serviço Microsoft Custom Tradutor constrói e utiliza os seus próprios dicionários para melhorar a qualidade da sua tradução. No entanto, um cliente fornecido dicionário tem precedentes e será procurado primeiro para procurar palavras ou frases.

Os dicionários só funcionam para projetos em pares linguísticos que têm um modelo de rede neural geral da Microsoft totalmente suportado por trás deles. [Veja a lista completa de idiomas.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)

## <a name="phrase-dictionary"></a>Dicionário de frases
O dicionário de frases é sensível a casos. É uma operação exata de localização e substituição. Quando se inclui um dicionário de frases no treino do seu modelo, qualquer palavra ou frase listada é traduzida da forma como especificou. O resto da frase é traduzido como de costume. Pode utilizar um dicionário de frases para especificar frases que não devem ser traduzidas fornecendo a mesma frase não traduzida no ficheiro de origem e alvo no dicionário.

## <a name="sentence-dictionary"></a>Dicionário de frases
O dicionário da sentença é insensível a caso. O dicionário da frase permite especificar uma tradução exata do alvo para uma frase de origem. Para que ocorra uma correspondência no dicionário da frase, toda a frase submetida deve corresponder à entrada do dicionário de origem. Se a entrada do dicionário de origem terminar com pontuação, é ignorada durante a partida. Se apenas uma parte da frase corresponder, a entrada não corresponderá.  Quando uma correspondência for detetada, a entrada do alvo do dicionário da frase será devolvida.

## <a name="dictionary-only-trainings"></a>Treinamentos apenas para dicionários
Pode treinar um modelo usando apenas dados de dicionário. Para isso, selecione apenas o documento do dicionário (ou vários documentos dicionários) que pretende incluir e toque no modelo Criar. Uma vez que se trata de um treino apenas para dicionários, não é necessário um número mínimo de frases de formação. O seu modelo normalmente completa o treino muito mais rápido do que um treino padrão.  Os modelos resultantes utilizarão os modelos de base da Microsoft para tradução com a adição dos dicionários que adicionou.  Não vai conseguir um relatório de teste.

>[!Note]
>O Tradutor Personalizado não alinha ficheiros de dicionário, pelo que é importante que existam um número igual de frases/frases-alvo nos seus documentos do dicionário e que estejam precisamente alinhados.

## <a name="recommendations"></a>Recomendações

- Os dicionários não substituem a formação de um modelo utilizando dados de formação. Recomenda-se evitá-los e deixar que o sistema aprenda com os seus dados de treino. No entanto, quando as frases ou substantivos compostos devem ser tornados como estão, utilize um dicionário.
- A frase dicionário deve ser usada com moderação. Portanto, esteja ciente de que quando uma frase dentro de uma frase é substituída, o contexto dentro dessa frase é perdido ou limitado para traduzir o resto da frase. O resultado é que, embora a frase ou palavra dentro da frase se traduza de acordo com o dicionário fornecido, a qualidade geral de tradução da frase sofrerá frequentemente.
- A frase dicionário funciona bem para substantivos compostos como nomes de produtos ("Microsoft SQL Server"), nomes apropriados ("Cidade de Hamburgo"), ou características do produto ("mesa de pivô"). Não funciona igualmente bem para verbos ou adjetivos, porque estes são tipicamente altamente inflexiveis na fonte ou na língua-alvo. As melhores práticas são evitar entradas de dicionário de frases para tudo menos substantivos compostos.
- Ao utilizar uma frase dicionário, a capitalização e a pontuação são importantes. As entradas no dicionário apenas corresponderão a palavras e frases na frase de entrada que usam exatamente a mesma capitalização e pontuação especificadas no ficheiro do dicionário de origem. Também as traduções refletirão a capitalização e pontuação fornecidas no ficheiro do dicionário-alvo. Por exemplo, se treinasse um sistema inglês para espanhol que usa um dicionário de frases que especifica "EUA" no ficheiro de origem e "EE". UU." no ficheiro alvo. Quando solicita a tradução de uma frase que inclua a palavra "nós" (não capitalizada), isso NÃO corresponderia ao dicionário. No entanto, se solicitar a tradução de uma frase que contenha a palavra "EUA" (capitalizada), então corresponderia ao dicionário e a tradução conteria "EE. UU." Note que a capitalização e pontuação na tradução podem ser diferentes das especificadas no ficheiro-alvo do dicionário, podendo ser diferentes da capitalização e pontuação na fonte. Segue as regras da linguagem-alvo.
- Ao usar um dicionário de frases, o fim da pontuação da frase é ignorado. Por exemplo, se o seu dicionário de origem contiver "esta frase termina com pontuação!", então quaisquer pedidos de tradução que contenham "esta frase termina com pontuação" corresponderiam.
- Se uma palavra aparecer mais de uma vez num ficheiro de dicionário, o sistema utilizará sempre a última entrada fornecida. Portanto, o seu dicionário não deve conter múltiplas traduções da mesma palavra.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre [as diretrizes sobre formatos de documentos.](document-formats-naming-convention.md)

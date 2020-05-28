---
title: O que é um dicionário? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Um dicionário é um documento alinhado que especifica uma lista de frases ou frases (e suas traduções) que sempre quer que o Microsoft Tradutor traduza da mesma forma. Os dicionários às vezes também são chamados glossários ou bases de termo.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 510c75b6e1a06ab800128534a72c5e1f6e510600
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997048"
---
# <a name="what-is-a-dictionary"></a>O que é um dicionário?

Um dicionário é um par de documentos alinhados que especifica uma lista de frases ou frases e suas traduções correspondentes. Utilize um dicionário no seu treino, quando quiser que o Microsoft Tradutor traduza sempre quaisquer casos da frase ou frase de origem, utilizando a tradução que forneceu no dicionário. Os dicionários às vezes são chamados glossários ou bases de termo. Pode pensar no dicionário como uma força bruta "copiar e substituir" por todos os termos que enumera. Além disso, o serviço Microsoft Custom Tradutor constrói e faz uso dos seus próprios dicionários para fins gerais para melhorar a qualidade da sua tradução. No entanto, um cliente fornecido dicionário tem precedentes e será procurado primeiro para procurar palavras ou frases.

Os dicionários só funcionam para projetos em pares de idiomas que tenham um modelo de rede neural geral da Microsoft totalmente suportado por trás deles. [Ver a lista completa de idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Dicionário de frases
Quando inclui um dicionário de frases no treino do seu modelo, qualquer palavra ou frase listada é traduzida da forma como especificou. O resto da frase é traduzido como de costume. Pode utilizar um dicionário de frases para especificar frases que não devem ser traduzidas fornecendo a mesma frase não traduzida no ficheiro fonte e alvo no dicionário.

## <a name="sentence-dictionary"></a>Dicionário de frases
O dicionário da frase permite especificar uma tradução exata do alvo para uma frase de origem. Para que ocorra uma correspondência do dicionário da sentença, toda a frase submetida deve corresponder à entrada do dicionário de origem.  Se apenas uma parte da frase corresponder, a entrada não corresponderá.  Quando uma correspondência for detetada, a entrada-alvo do dicionário da frase será devolvida.

## <a name="dictionary-only-trainings"></a>Formações só para dicionários
Pode treinar um modelo usando apenas dados do dicionário. Para tal, selecione apenas o documento do dicionário (ou vários documentos do dicionário) que pretende incluir e toque no modelo Criar. Uma vez que se trata de uma formação só para dicionários, não é necessário um número mínimo de penas de formação. O seu modelo normalmente completa o treino muito mais rápido do que um treino padrão.  Os modelos resultantes utilizarão os modelos de base da Microsoft para tradução com a adição dos dicionários que adicionou.  Não vai receber um relatório de teste.

>[!Note]
>O Tradutor Personalizado não sentencia ficheiros do dicionário, pelo que é importante que existam um número igual de frases/frases/frases-alvo iguais nos documentos do dicionário e que estejam precisamente alinhados.

## <a name="recommendations"></a>Recomendações

- Os dicionários não substituem a formação de um modelo utilizando dados de formação. Recomenda-se evitá-los e deixar o sistema aprender com os seus dados de treino. No entanto, quando as frases ou substantivos compostos devem ser tornados como estão, utilize um dicionário.
- O dicionário da frase deve ser usado com moderação. Portanto, esteja ciente de que quando uma frase dentro de uma frase é substituída, o contexto dentro dessa frase é perdido ou limitado para traduzir o resto da frase. O resultado é que, embora a frase ou a palavra dentro da frase se traduza de acordo com o dicionário fornecido, a qualidade de tradução global da frase sofrerá frequentemente.
- O dicionário da frase funciona bem para substantivos compostos como nomes de produtos ("Microsoft SQL Server"), nomes próprios ("Cidade de Hamburgo"), ou características do produto ("tabela de pivô"). Não funciona igualmente bem para verbos ou adjetivos porque estes são tipicamente altamente inflexidos na fonte ou na língua-alvo. As melhores práticas são evitar entradas de dicionário de frases para qualquer coisa, mas substantivos compostos.
- Quando se usa uma frase dicionário, a capitalização e a pontuação são importantes. As entradas no dicionário apenas corresponderão a palavras e frases na frase de entrada que usam exatamente a mesma capitalização e pontuação especificada no ficheiro do dicionário fonte. Também as traduções refletirão a capitalização e pontuação fornecidas no ficheiro do dicionário-alvo. Por exemplo, se treinou um sistema inglês para espanhol que usa um dicionário de frases que especifica "EUA" no ficheiro fonte, e "EE. UU. no ficheiro alvo. Quando solicita a tradução de uma frase que inclui a palavra "nós" (não capitalizada), isso não corresponderia ao dicionário. No entanto, se solicitar a tradução de uma frase que contenha a palavra "EUA" (capitalizada), então corresponderia ao dicionário e a tradução conteria "EE. UU. Note que a capitalização e pontuação na tradução podem ser diferentes das especificadas no ficheiro-alvo do dicionário, podendo ser diferente da capitalização e pontuação na fonte. Segue as regras da linguagem-alvo.
- Se uma palavra aparecer mais de uma vez num ficheiro de dicionário, o sistema utilizará sempre a última entrada fornecida. Por isso, o seu dicionário não deve conter múltiplas traduções da mesma palavra.

## <a name="next-steps"></a>Próximos passos

- Leia sobre [as diretrizes sobre os formatos](document-formats-naming-convention.md)de documentos .

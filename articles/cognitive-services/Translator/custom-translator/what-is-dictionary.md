---
title: O que é um dicionário? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Um dicionário é um documento alinhado que especifica uma lista de frases ou sentenças (e suas traduções) que você sempre deseja que o Microsoft Translator traduza da mesma maneira. Os dicionários às vezes também são chamados de glossários ou bases de termos.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5103526956b5041771a1d8e4abb5e8800b971059
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595381"
---
# <a name="what-is-a-dictionary"></a>O que é um dicionário?

Um dicionário é um par alinhado de documentos que especifica uma lista de frases ou sentenças e suas traduções correspondentes. Use um dicionário em seu treinamento, quando desejar que o Microsoft Translator sempre traduza todas as instâncias da frase ou sentença de origem, usando a tradução que você forneceu no dicionário. Os dicionários às vezes são chamados de glossários ou bases de termos. Você pode considerar o dicionário como uma força bruta "copiar e substituir" para todos os termos que listar.

Os dicionários só funcionam para projetos em pares de idiomas que tenham um sistema de conversão de máquina neural da Microsoft (NMT) totalmente suportado por trás deles. [Exiba a lista completa de idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Dicionário de frases
Quando você inclui um dicionário de frases no treinamento de seu modelo, qualquer palavra ou frase listada é traduzida da maneira especificada. O restante da sentença é traduzido como de costume. Você pode usar um dicionário de frases para especificar frases que não devem ser convertidas fornecendo a mesma frase não traduzida no arquivo de origem e de destino no dicionário.

## <a name="sentence-dictionary"></a>Dicionário de sentenças
O dicionário de sentenças permite que você especifique uma tradução de destino exata para uma sentença de origem. Para que uma correspondência de dicionário de sentença ocorra, a sentença enviada inteira deve corresponder à entrada do dicionário de origem.  Se apenas uma parte da frase corresponder, a entrada não será correspondente.  Quando uma correspondência for detectada, a entrada de destino do dicionário da sentença será retornada.

## <a name="dictionary-only-trainings"></a>Treinamentos somente de dicionário
Você pode treinar um modelo usando apenas dados de dicionário. Para fazer isso, selecione apenas o documento de dicionário (ou vários documentos de dicionário) que você deseja incluir e toque em criar modelo. Como este é um treinamento somente para dicionário, não há um número mínimo de frases de treinamento necessárias. Seu modelo normalmente concluirá o treinamento de forma muito mais rápida do que um treinamento padrão.  Os modelos resultantes usarão os modelos de linha de base da Microsoft para tradução com a adição dos dicionários que você adicionou.  Você não receberá um relatório de teste.

>[!Note]
>O tradutor personalizado não alinha arquivos de dicionário, portanto, é importante que haja um número igual de frases/sentenças/frases de origem e de destino em seus documentos de dicionário e que eles estejam alinhados com precisão.

## <a name="recommendations"></a>Recomendações

- Os dicionários não são um substituto para um modelo treinado com dados de treinamento.  Essencialmente, os dicionários localizam e substituem palavras ou frases.  Permitir que o sistema Aprenda com seu material de treinamento em frases completas geralmente é uma opção melhor do que usar um dicionário.
- O dicionário de frases deve ser usado com moderação. Quando uma frase dentro de uma sentença é substituída, o contexto dentro dessa sentença é perdido ou limitado para a tradução do restante da sentença. O resultado é que, embora a frase ou palavra dentro da frase seja traduzida de acordo com o dicionário de frases, a qualidade geral da tradução da sentença geralmente será prejudicada.
- O dicionário de frases funciona bem para substantivos compostos como nomes de produtos ("Microsoft SQL Server"), nomes próprios ("cidade de Hamburgo") ou recursos do produto ("tabela dinâmica"). Ele não funciona igualmente bem para verbos ou adjetivos porque eles geralmente são altamente formas flexionadass na origem ou no idioma de destino. Evite entradas de dicionário de frase para qualquer coisa, exceto nomes compostos.
- Ao usar um dicionário, a capitalização e a pontuação em suas traduções refletirão a capitalização e a pontuação fornecidas no arquivo de destino. A capitalização e a pontuação são ignoradas ao tentar identificar correspondências entre sua frase de entrada e as frases de origem no arquivo de dicionário. Por exemplo, digamos que treinamos um sistema em inglês para espanhol que usava um dicionário que especificasse "City of Hamburgo" no arquivo de origem e "Ciudad de Hamburgo" no arquivo de destino. Se eu solicitasse a tradução de uma frase que incluía a frase "City of Hamburgo", "City of Hamburgo" corresponderia ao meu arquivo de dicionário para a entrada "City of Hamburgo" e mapearia "Ciudad de Hamburgo" na minha tradução final.
- Se uma palavra aparecer mais de uma vez em um arquivo de dicionário, o sistema sempre usará a última entrada fornecida. O dicionário não deve conter várias traduções da mesma palavra.

## <a name="next-steps"></a>Passos Seguintes

- Leia sobre as [diretrizes em formatos de documento](document-formats-naming-convention.md).

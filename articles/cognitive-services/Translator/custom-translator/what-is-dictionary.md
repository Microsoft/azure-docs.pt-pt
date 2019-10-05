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
ms.openlocfilehash: a4aac8afb7974be402ee98bb65c920133d4c118f
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947692"
---
# <a name="what-is-a-dictionary"></a>O que é um dicionário?

Um dicionário é um par alinhado de documentos que especifica uma lista de frases ou sentenças e suas traduções correspondentes. Use um dicionário em seu treinamento, quando desejar que o Microsoft Translator sempre traduza todas as instâncias da frase ou sentença de origem, usando a tradução que você forneceu no dicionário. Os dicionários às vezes são chamados de glossários ou bases de termos. Você pode considerar o dicionário como uma força bruta "copiar e substituir" para todos os termos que listar. Além disso, o serviço Tradutor personalizado da Microsoft cria e usa seus próprios dicionários de uso geral para melhorar a qualidade de sua tradução. No entanto, um dicionário fornecido pelo cliente usa o precedente e será pesquisado primeiro em palavras ou sentenças de pesquisa.

Os dicionários só funcionam para projetos em pares de idiomas que tenham um modelo de rede neural geral da Microsoft totalmente compatível por trás deles. [Exiba a lista completa de idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Dicionário de frases
Quando você inclui um dicionário de frases no treinamento de seu modelo, qualquer palavra ou frase listada é traduzida da maneira especificada. O restante da sentença é traduzido como de costume. Você pode usar um dicionário de frases para especificar frases que não devem ser convertidas fornecendo a mesma frase não traduzida no arquivo de origem e de destino no dicionário.

## <a name="sentence-dictionary"></a>Dicionário de sentenças
O dicionário de sentenças permite que você especifique uma tradução de destino exata para uma sentença de origem. Para que uma correspondência de dicionário de sentença ocorra, a sentença enviada inteira deve corresponder à entrada do dicionário de origem.  Se apenas uma parte da frase corresponder, a entrada não será correspondente.  Quando uma correspondência for detectada, a entrada de destino do dicionário da sentença será retornada.

## <a name="dictionary-only-trainings"></a>Treinamentos somente de dicionário
Você pode treinar um modelo usando apenas dados de dicionário. Para fazer isso, selecione apenas o documento de dicionário (ou vários documentos de dicionário) que você deseja incluir e toque em criar modelo. Como este é um treinamento somente para dicionário, não há um número mínimo de frases de treinamento necessárias. Seu modelo normalmente concluirá o treinamento de forma muito mais rápida do que um treinamento padrão.  Os modelos resultantes usarão os modelos de linha de base da Microsoft para tradução com a adição dos dicionários que você adicionou.  Você não receberá um relatório de teste.

>[!Note]
>O tradutor personalizado não alinha arquivos de dicionário, portanto, é importante que haja um número igual de frases/sentenças/frases de origem e de destino em seus documentos de dicionário e que eles estejam alinhados com precisão.

## <a name="recommendations"></a>Recomendações

- Os dicionários não são um substituto para treinar um modelo usando dados de treinamento. É recomendável evitá-los e permitir que o sistema Aprenda com seus dados de treinamento. No entanto, quando frases ou substantivos compostos devem ser renderizados como estão, use um dicionário.
- O dicionário de frases deve ser usado com moderação. Então, lembre-se de que, quando uma frase dentro de uma sentença é substituída, o contexto dentro dessa sentença é perdido ou limitado para a tradução do restante da sentença. O resultado é que, embora a frase ou palavra dentro da frase seja traduzida de acordo com o dicionário fornecido, a qualidade geral da tradução da sentença geralmente será prejudicada.
- O dicionário de frases funciona bem para substantivos compostos como nomes de produtos ("Microsoft SQL Server"), nomes próprios ("cidade de Hamburgo") ou recursos do produto ("tabela dinâmica"). Ele não funciona igualmente bem para verbos ou adjetivos porque eles geralmente são altamente formas flexionadass na origem ou no idioma de destino. As práticas recomendadas são evitar entradas de dicionário de frase para qualquer coisa, exceto os substantivos compostos.
- Ao usar um dicionário, a capitalização e a pontuação são importantes. As entradas de dicionário só corresponderão a palavras e frases que tenham a mesma capitalização e pontuação que a entrada encontrada no dicionário. Suas traduções refletirão a capitalização e a pontuação fornecidas no lado de destino do arquivo de dicionário. Por exemplo, você treinou um modelo em inglês para espanhol usando um dicionário que especificou "Olá" no arquivo de origem a ser convertido em "Buenos dias" no arquivo de destino. Quando você solicitar tradução de uma frase que inclua "Olá", o sistema pesquisará seu dicionário primeiro e encontrará uma correspondência ("Olá") e retornaria "Buenos dias" na tradução final.
- Se uma palavra aparecer mais de uma vez em um arquivo de dicionário, o sistema sempre usará a última entrada fornecida. Portanto, o dicionário não deve conter várias traduções da mesma palavra.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre as [diretrizes em formatos de documento](document-formats-naming-convention.md).

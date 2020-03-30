---
title: 'Texto pré-processo: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de texto pré-processo em Aprendizagem automática Azure para limpar e simplificar o texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 6e4d4c8f798418e090caeba091dec33c71f0458f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477498"
---
# <a name="preprocess-text"></a>Pré-processar Texto

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize o módulo **de texto pré-processo** para limpar e simplificar o texto. Apoia estas operações comuns de processamento de texto:

* Remoção de palavras-stop
* Utilização de expressões regulares para procurar e substituir cordas específicas do alvo
* Lemmatização, que converte múltiplas palavras relacionadas com uma única forma canónica
* Normalização de casos
* Remoção de certas classes de caracteres, tais como números, personagens especiais e sequências de caracteres repetidos, tais como "aaaaa"
* Identificação e remoção de e-mails e URLs

O módulo **de texto pré-processo** atualmente apenas suporta inglês.

## <a name="configure-text-preprocessing"></a>Configurar o pré-processamento de texto  

1.  Adicione o módulo **de texto pré-processo** ao seu pipeline em Azure Machine Learning. Pode encontrar este módulo em Análise de **Texto.**

1. Ligue um conjunto de dados que tenha pelo menos uma coluna contendo texto.

1. Selecione o idioma da lista de abandono seletiva do **Idioma.**

1. **Coluna**de texto para limpar : Selecione a coluna que pretende pré-processar.

1. **Remova as palavras**de paragem : Selecione esta opção se pretender aplicar uma lista de palavras-stop predefinidas na coluna de texto. 

    As listas de palavras-stop são dependentes da linguagem e personalizáveis.

1. **Lemmatization**: Selecione esta opção se quiser que as palavras sejam representadas na sua forma canónica. Esta opção é útil para reduzir o número de ocorrências únicas de tokens de texto semelhantes.

    O processo de lemtização é altamente dependente da linguagem.

1. **Detete frases**: Selecione esta opção se pretender que o módulo insira uma marca de limite de frase ao efetuar análises.

    Este módulo usa uma série `|||` de três caracteres de tubo para representar o exterminador da frase.

1. Execute operações opcionais de find-and-replace utilizando expressões regulares.

    * **Expressão regular personalizada**: Defina o texto que procura.
    * **Fio de substituição personalizado**: Defina um único valor de substituição.

1. **Normalizar a caixa para minúscula**: Selecione esta opção se pretender converter caracteres maiúsculos ASCII nas suas formas minúsculas.

    Se os caracteres não forem normalizados, a mesma palavra em letras maiúsculas e minúsculas é considerada duas palavras diferentes.

1. Também pode remover os seguintes tipos de caracteres ou sequências de caracteres do texto de saída processado:

    * **Remova os números**: Selecione esta opção para remover todos os caracteres numéricos para o idioma especificado. Os números de identificação são dependentes do domínio e dependentes da linguagem. Se os caracteres numéricos forem parte integrante de uma palavra conhecida, o número pode não ser removido.
    
    * **Remova caracteres especiais**: Utilize esta opção para remover quaisquer caracteres especiais não alfanuméricos.
    
    * **Remova caracteres duplicados**: Selecione esta opção para remover caracteres extra em quaisquer sequências que se repitam por mais de duas vezes. Por exemplo, uma sequência como "aaaaaa" seria reduzida a "aa".
    
    * **Remova os endereços de e-mail**: `<string>@<string>`Selecione esta opção para remover qualquer sequência do formato .  
    * **Remover URLs**: Selecione esta opção para remover `http`qualquer `https` `ftp`sequência que inclua os seguintes prefixos URL: , ,`www`
    
1. **Expandir contrações verbos**: Esta opção aplica-se apenas a línguas que utilizam contrações verbos; atualmente, apenas inglês. 

    Por exemplo, ao selecionar esta opção, pode substituir a frase *"não ficaria lá"* por *"não ficaria lá"*.

1. **Normalizar as costas a cortes**: Selecione esta `\\` opção para mapear todas as instâncias de `/`.

1. **Fichas divididas em caracteres especiais:** Selecione esta opção se quiser quebrar palavras em caracteres como `&`, `-`e assim por diante. Esta opção também pode reduzir os caracteres especiais quando se repete mais do dobro. 

    Por exemplo, `MS---WORD` a corda seria separada em `MS`três `-`fichas, e `WORD`.

1. Submeta o oleoduto.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
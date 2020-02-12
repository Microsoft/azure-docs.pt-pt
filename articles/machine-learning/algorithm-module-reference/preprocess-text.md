---
title: 'Pré-processar texto: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de texto de pré-processamento em Azure Machine Learning para limpar e simplificar o texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: a8938eba0f7af995086ab1e2baba41aee7dc6330
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153813"
---
# <a name="preprocess-text"></a>Pré-processar Texto

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize o módulo **de texto pré-processo** para limpar e simplificar o texto. Ele dá suporte a essas operações comuns de processamento de texto:

* Remoção de palavras irrelevantes
* Usando expressões regulares para pesquisar e substituir cadeias de caracteres de destino específicas
* Lematização, que converte várias palavras relacionadas em um único Formulário canônico
* Normalização de caso
* Remoção de determinadas classes de caracteres, como números, caracteres especiais e sequências de caracteres repetidos, como "aaaa"
* Identificação e remoção de emails e URLs

O módulo **de texto pré-processo** atualmente apenas suporta inglês.

## <a name="configure-text-preprocessing"></a>Configurar o pré-processamento de texto  

1.  Adicione o módulo **de texto pré-processo** ao seu pipeline em Azure Machine Learning. Pode encontrar este módulo em Análise de **Texto.**

1. Conecte um conjunto de um DataSet que tenha pelo menos uma coluna contendo texto.

1. Selecione o idioma da lista de abandono seletiva do **Idioma.**

1. **Coluna**de texto para limpar : Selecione a coluna que pretende pré-processar.

1. **Remova as palavras**de paragem : Selecione esta opção se pretender aplicar uma lista de palavras-stop predefinidas na coluna de texto. 

    As listas de palavra irrelevante são dependentes de idioma e personalizáveis.

1. **Lemmatization**: Selecione esta opção se quiser que as palavras sejam representadas na sua forma canónica. Essa opção é útil para reduzir o número de ocorrências exclusivas de outros tokens de texto semelhantes.

    O processo lematização é dependente de linguagem altamente.

1. **Detete frases**: Selecione esta opção se pretender que o módulo insira uma marca de limite de frase ao efetuar análises.

    Este módulo usa uma série de três caracteres de tubo`|||` para representar o exterminador da frase.

1. Execute operações de localização e substituição opcionais usando expressões regulares.

    * **Expressão regular personalizada**: Defina o texto que procura.
    * **Fio de substituição personalizado**: Defina um único valor de substituição.

1. **Normalizar a caixa para minúscula**: Selecione esta opção se pretender converter caracteres maiúsculos ASCII nas suas formas minúsculas.

    Se os caracteres não forem normalizados, a mesma palavra em letras maiúsculas e minúsculas será considerada duas palavras diferentes.

1. Você também pode remover os seguintes tipos de caracteres ou sequências de caracteres do texto de saída processado:

    * **Remova os números**: Selecione esta opção para remover todos os caracteres numéricos para o idioma especificado. Os números de identificação são dependentes de domínio e de idioma. Se os caracteres numéricos forem parte integrante de uma palavra conhecida, o número poderá não ser removido.
    
    * **Remova caracteres especiais**: Utilize esta opção para remover quaisquer caracteres especiais não alfanuméricos.
    
    * **Remova caracteres duplicados**: Selecione esta opção para remover caracteres extra em quaisquer sequências que se repitam por mais de duas vezes. Por exemplo, uma sequência como "AAAAA" seria reduzida para "AA".
    
    * **Remova os endereços de e-mail**: Selecione esta opção para remover qualquer sequência do formato `<string>@<string>`.  
    * **Remover URLs**: Selecione esta opção para remover qualquer sequência que inclua os seguintes prefixos URL: `http`, `https`, `ftp`, `www`
    
1. **Expandir contrações verbos**: Esta opção aplica-se apenas a línguas que utilizam contrações verbos; atualmente, apenas inglês. 

    Por exemplo, ao selecionar esta opção, pode substituir a frase *"não ficaria lá"* por *"não ficaria lá"* .

1. **Normalizar as costas para cortar**: Selecione esta opção para mapear todas as instâncias de `\\` a `/`.

1. **Fichas divididas em caracteres especiais:** Selecione esta opção se quiser quebrar palavras em personagens como `&`, `-`, e assim por diante. Essa opção também pode reduzir os caracteres especiais quando ele se repetir mais de duas vezes. 

    Por exemplo, a cadeia `MS---WORD` seria separada em três fichas, `MS`, `-`e `WORD`.

1. Executar o pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
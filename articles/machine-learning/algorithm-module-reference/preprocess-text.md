---
title: 'Texto pré-processamento: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de texto pré-processamento no designer de aprendizagem automática Azure para limpar e simplificar o texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: d512a691b76cb7cbc72b4cbcb1fc821e928ea1b0
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421231"
---
# <a name="preprocess-text"></a>Pré-processar Texto

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize o módulo **de texto pré-processamento** para limpar e simplificar o texto. Apoia estas operações comuns de processamento de texto:

* Remoção de palavras-stop
* Usando expressões regulares para procurar e substituir cordas-alvo específicas
* Lematização, que converte várias palavras relacionadas a uma única forma canónica
* Normalização de casos
* Remoção de certas classes de caracteres, tais como números, caracteres especiais e sequências de caracteres repetidos como "aaaa"
* Identificação e remoção de e-mails e URLs

O módulo **de texto pré-processamento** suporta apenas inglês.

## <a name="configure-text-preprocessing"></a>Configurar pré-processamento de texto  

1.  Adicione o módulo **de texto pré-processamento** ao seu pipeline em Azure Machine Learning. Pode encontrar este módulo em **Text Analytics.**

1. Ligue um conjunto de dados que tenha pelo menos uma coluna contendo texto.

1. Selecione o idioma da lista de abandono de **idiomas.**

1. **Coluna de texto para limpar** : Selecione a coluna que pretende pré-processar.

1. **Remova as palavras de paragem** : Selecione esta opção se pretender aplicar uma lista de palavras de ordem predefinidas na coluna de texto. 

    As listas de palavras de ordem são dependentes da linguagem e personalizáveis.

1. **Lemmatização** : Selecione esta opção se quiser que as palavras sejam representadas na sua forma canónica. Esta opção é útil para reduzir o número de ocorrências únicas de tokens de texto semelhantes.

    O processo de lematização é altamente dependente da linguagem.

1. **Detetar frases** : Selecione esta opção se quiser que o módulo insira uma marca de limite de frase ao efetuar a análise.

    Este módulo usa uma série de três caracteres de tubo `|||` para representar o exterminador da frase.

1. Execute operações de operação de operação de operação opcional de mente e substituição utilizando expressões regulares.

    * **Expressão regular personalizada** : Defina o texto que procura.
    * **Cadeia de substituição personalizada** : Defina um único valor de substituição.

1. **Normalizar a caixa para minúsculas** : Selecione esta opção se pretender converter caracteres maiúsculas ASCII para as suas formas minúsculas.

    Se os caracteres não forem normalizados, a mesma palavra em letras maiúsculas e minúsculas é considerada duas palavras diferentes.

1. Também pode remover os seguintes tipos de caracteres ou sequências de caracteres do texto de saída processado:

    * **Remover números** : Selecione esta opção para remover todos os caracteres numéricos para o idioma especificado. Os números de identificação são dependentes do domínio e dependentes da linguagem. Se os caracteres numéricos forem parte integrante de uma palavra conhecida, o número pode não ser removido.
    
    * **Remover caracteres especiais** : Utilize esta opção para remover caracteres especiais não alfanuméricos.
    
    * **Remover caracteres duplicados** : Selecione esta opção para remover caracteres extras em quaisquer sequências que se repitam por mais de duas vezes. Por exemplo, uma sequência como "aaaaa" seria reduzida a "aa".
    
    * **Remova os endereços de e-mail** : Selecione esta opção para remover qualquer sequência do formato `<string>@<string>` .  
    * **Remover URLs** : Selecione esta opção para remover qualquer sequência que inclua os seguintes prefixos URL: `http` , , , `https` `ftp` , `www`
    
1. **Expandir contrações verbos** : Esta opção aplica-se apenas às línguas que utilizam contrações de verbo; atualmente, apenas inglês. 

    Por exemplo, selecionando esta opção, pode substituir a frase *"não ficaria lá"* por *"não ficar lá"*.

1. **Normalizar as costas para os cortes** : Selecione esta opção para mapear todas as instâncias de `\\` `/` .

1. **Fichas divididas em caracteres especiais** : Selecione esta opção se quiser quebrar palavras em caracteres como `&` , e assim por `-` diante. Esta opção também pode reduzir os caracteres especiais quando se repete mais de duas vezes. 

    Por exemplo, a corda `MS---WORD` seria separada em três fichas, e `MS` `-` `WORD` .

1. Envie o oleoduto.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
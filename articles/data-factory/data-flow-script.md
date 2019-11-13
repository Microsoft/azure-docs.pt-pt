---
title: Script de fluxo de dados de mapeamento de Azure Data Factory
description: Visão geral da linguagem code-behind do script de fluxo de dados do Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/10/2019
ms.openlocfilehash: 4ff5a05fd40ef086c1f2332443ca03d5e872e9a8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010167"
---
# <a name="data-flow-script-dfs"></a>Script de fluxo de dados (DFS)

O script de fluxo de dados (DFS) são os metadados subjacentes, semelhantes a uma linguagem de codificação, que é usada para executar as transformações incluídas em um fluxo de dados de mapeamento. Cada transformação é representada por uma série de propriedades que fornecem as informações necessárias para executar o trabalho corretamente. O script é visível e editável do ADF clicando no botão "script" na faixa de vista superior da interface do usuário do navegador.

![Botão de script](media/data-flow/scriptbutton.png "Botão de script")

Por exemplo, `allowSchemaDrift: true,` em uma transformação de origem instrui o serviço a incluir todas as colunas do conjunto de dados de origem no fluxo de dados, mesmo que elas não estejam incluídas na projeção do esquema.

## <a name="use-cases"></a>Casos de utilização
O DFS é produzido automaticamente pela interface do usuário. Você pode clicar no botão script para exibir e personalizar o script. Você também pode gerar scripts fora da interface do usuário do ADF e, em seguida, passá-los para o cmdlet do PowerShell. Ao depurar fluxos de dados complexos, você pode achar mais fácil verificar o code-behind do script em vez de verificar a representação do grafo de interface do usuário de seus fluxos.

Aqui estão alguns exemplos de casos de uso:
- Produzir programaticamente muitos fluxos de dados que são bastante semelhantes, ou seja, fluxos de dados de "carimbo".
- Expressões complexas que são difíceis de gerenciar na interface do usuário ou que resultam em problemas de validação.
- Depuração e melhor compreensão de vários erros retornados durante a execução.

Ao criar um script de fluxo de dados para usar com o PowerShell ou uma API, você deve recolher o texto formatado em uma única linha. Você pode manter guias e novas linhas como caracteres de escape. Mas o texto deve ser formatado para se ajustar dentro de uma propriedade JSON. Há um botão na interface do usuário do editor de scripts, na parte inferior, que formatará o script como uma única linha para você.

![Botão de cópia](media/data-flow/copybutton.png "botão Copiar")

## <a name="how-to-add-transforms"></a>Como adicionar transformações
A adição de transformações requer três etapas básicas: adicionar os dados de transformação de núcleo, redirecionar o fluxo de entrada e, em seguida, rotear novamente o fluxo de saída. Isso pode ser visto mais fácil em um exemplo.
Digamos que comecemos com uma fonte simples para coletar fluxo de dados como o seguinte:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Se decidirmos adicionar uma transformação derivar, primeiro precisamos criar o texto de transformação principal, que tem uma expressão simples para adicionar uma nova coluna em maiúsculas chamada `upperCaseTitle`:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Em seguida, pegamos o DFS existente e adicionamos a transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

E agora redirecionamos o fluxo de entrada identificando a transformação que queremos que a nova transformação venha após (neste caso, `source1`) e copiando o nome do fluxo para a nova transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Por fim, identificamos a transformação que desejamos vir após essa nova transformação e substituo seu fluxo de entrada (nesse caso, `sink1`) pelo nome do fluxo de saída da nossa nova transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Conceitos básicos do DFS
O DFS é composto por uma série de transformações conectadas, incluindo fontes, coletores e vários outros que podem adicionar novas colunas, filtrar dados, unir dados e muito mais. Normalmente, o script com início com uma ou mais fontes seguidas por muitas transformações e terminando com um ou mais coletores.

Todas as fontes têm a mesma construção básica:
```
source(
  source properties
) ~> source_name
```

Por exemplo, uma fonte simples com três colunas (MovieID, título, gêneros) seria:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Todas as transformações que não sejam fontes têm a mesma construção básica:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Por exemplo, uma transformação derivar simples que usa uma coluna (título) e a substitui por uma versão em maiúsculas seria a seguinte:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

E um coletor sem esquema seria simplesmente:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>Passos seguintes

Explore os fluxos de dados iniciando com o [artigo Visão geral de fluxos de dados](concepts-data-flow-overview.md)

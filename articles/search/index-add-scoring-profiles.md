---
title: Aumentar o ranking de pesquisa usando perfis de pontuação
titleSuffix: Azure Cognitive Search
description: Aumente as pontuações de classificação de pesquisa para resultados de Pesquisa Cognitiva Azure adicionando perfis de pontuação.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/28/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 516637b812afece1966006ce6d894dd1e32e6293
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379655"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Adicione perfis de pontuação a um índice de pesquisa cognitiva Azure

  A pontuação refere-se à computação de uma *pontuação* de pesquisa para cada item devolvido nos resultados da pesquisa. A classificação é um indicador da relevância do item no contexto da operação de pesquisa atual. Quanto maior a classificação, mais relevante será o item. Nos resultados da pesquisa, os itens são ordenados de alto a baixo, com base nas pontuações de pesquisa calculadas para cada item.  

 A Azure Cognitive Search utiliza a pontuação padrão para calcular uma pontuação inicial, mas pode personalizar o cálculo através de um *perfil de pontuação*. Os perfis de pontuação dão-lhe um maior controlo sobre o ranking dos itens nos resultados da pesquisa. Por exemplo, é possível que queira impulsionar itens com base no seu potencial de receitas, promover itens mais recentes ou talvez impulsionar itens que estão há demasiado tempo no inventário.  

 Um perfil de pontuação faz parte da definição de índice, composta por campos ponderados, funções e parâmetros.  

 Para lhe dar uma ideia de como é um perfil de pontuação, o exemplo seguinte mostra um perfil simples chamado 'geo'. Este aumenta itens que têm o termo de pesquisa no campo **hotelName.** Também usa a função `distance` para favorecer itens que estão a dez quilómetros da localização atual. Se alguém pesquisar o termo 'pousada', e 'pousada' por acaso fazer parte do nome do hotel, documentos que incluem hotéis com 'pousada' num raio de 10 KM da localização atual aparecerão mais altos nos resultados da pesquisa.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 Para utilizar este perfil de pontuação, a sua consulta é formulada para especificar o perfil na cadeia de consulta. Na consulta abaixo, note o parâmetro de consulta `scoringProfile=geo` no pedido.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 Esta consulta procura o termo 'pousada' e passa na localização atual. Note que esta consulta inclui outros parâmetros, tais como `scoringParameter`. Os parâmetros de consulta são descritos em [ &#40;Documentos de Pesquisa Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Clique [em Exemplo](#bkmk_ex) para rever um exemplo mais detalhado de um perfil de pontuação.  

## <a name="what-is-default-scoring"></a>O que é pontuação padrão?  
 Marcar calcula uma pontuação de pesquisa para cada item em um conjunto de resultados ordenado. Cada item de um conjunto de resultados de pesquisa é atribuído a uma pontuação de pesquisa, em seguida, classificado como o mais alto para o mais baixo. Os itens com as pontuações mais altas são devolvidos à aplicação. Por padrão, os 50 melhores são devolvidos, mas pode utilizar o parâmetro `$top` para devolver um número menor ou maior de itens (até 1000 numa única resposta).  

A pontuação de pesquisa é calculada com base em propriedades estatísticas dos dados e na consulta. A Azure Cognitive Search encontra documentos que incluem os termos de pesquisa na cadeia de consulta (alguns ou todos, dependendo de `searchMode`), favorecendo documentos que contenham muitos casos do termo de pesquisa. A pontuação de pesquisa sobe ainda mais se o termo for raro em todo o índice de dados, mas comum dentro do documento. A base para esta abordagem à relevância da computação é conhecida como [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) ou frequência de documentos inverso de frequência a termo.  

 Assumindo que não há classificação personalizada, os resultados são classificados pela pontuação de pesquisa antes de serem devolvidos à aplicação de chamada. Se $top não for especificado, 50 itens com a pontuação de pesquisa mais alta são devolvidos.  

 Os valores de pontuação de pesquisa podem ser repetidos ao longo de um conjunto de resultados. Por exemplo, pode ter 10 itens com uma pontuação de 1,2, 20 itens com uma pontuação de 1.0, e 20 itens com uma pontuação de 0,5. Quando vários hits têm a mesma pontuação de pesquisa, a encomenda dos mesmos itens pontuados não é definida, e não é estável. Faça a consulta de novo, e poderá ver itens mudarem de posição. Tendo em conta dois itens com uma pontuação idêntica, não há garantia de qual aparece primeiro.  

## <a name="when-to-use-custom-scoring"></a>Quando usar pontuação personalizada  
 Deve criar um ou mais perfis de pontuação quando o comportamento do ranking padrão não for suficientemente longe para cumprir os seus objetivos de negócio. Por exemplo, pode decidir que a relevância da pesquisa deve favorecer itens recém-adicionados. Da mesma forma, você pode ter um campo que contenha margem de lucro, ou algum outro campo que indique potencial de receita. Impulsionar sucessos que trazem benefícios para o seu negócio pode ser um fator importante na decisão de usar perfis de pontuação.  

 A ordem baseada na relevância também é implementada através de perfis de pontuação. Considere páginas de resultados de pesquisa que usou no passado que lhe permitem classificar por preço, data, classificação ou relevância. Na Pesquisa Cognitiva Azure, os perfis de pontuação impulsionam a opção de "relevância". A definição de relevância é controlada por si, baseada em objetivos de negócio e no tipo de experiência de pesquisa que pretende realizar.  

##  <a name="bkmk_ex"></a>Exemplo  
 Como notado anteriormente, a pontuação personalizada é implementada através de um ou mais perfis de pontuação definidos num esquema de índice.  

 Este exemplo mostra o esquema de um índice com dois perfis pontuais (`boostGenre`, `newAndHighlyRated`). Qualquer consulta contra este índice que inclua qualquer um dos perfis como parâmetro de consulta usará o perfil para marcar o conjunto de resultados.  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Fluxo de trabalho  
 Para implementar o comportamento personalizado de pontuação, adicione um perfil de pontuação ao esquema que define o índice. Pode ter até 100 perfis de pontuação dentro de um índice (ver Limites de [Serviço),](search-limits-quotas-capacity.md)mas só pode especificar um perfil no momento em qualquer consulta.  

 Comece com o [Modelo](#bkmk_template) fornecido neste tópico.  

 Forneça um nome. Os perfis de pontuação são opcionais, mas se adicionar um, o nome é necessário. Certifique-se de seguir as convenções de nomeação para campos (começa com uma carta, evita caracteres especiais e palavras reservadas). Consulte [as &#40;regras de&#41; nomeação Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/naming-rules) para a lista completa.  

 O corpo do perfil de pontuação é construído a partir de campos e funções ponderados.  

|||  
|-|-|  
|**Pesos**|Especifique pares de valor de nome que atribuam um peso relativo a um campo. No [Exemplo](#bkmk_ex), os campos de álbunsTitle, genre e artistName são impulsionados 1.5, 5 e 2 respectivamente. Porque é que o género é impulsionado muito mais alto que os outros? Se a pesquisa for conduzida sobre dados que sejam um pouco homogéneos (como é o caso do 'género' no `musicstoreindex`), poderá necessitar de uma maior variação nos pesos relativos. Por exemplo, no `musicstoreindex`, 'rock' aparece como um género e em descrições de género sinuosamente fraseadas. Se quiser que o género supere a descrição do género, o campo de género precisará de um peso relativo muito maior.|  
|**Funções**|Utilizado sumido quando são necessários cálculos adicionais para contextos específicos. Os valores válidos são `freshness`, `magnitude`, `distance`e `tag`. Cada função tem parâmetros únicos.<br /><br /> -   `freshness` deve ser usado quando se quer impulsionar pela forma como um item é novo ou antigo. Esta função só pode ser utilizada com campos `datetime` (edm. DataTimeOffset). Note que o atributo `boostingDuration` é utilizado apenas com a função `freshness`.<br />-   `magnitude` deve ser usado quando pretende aumentar com base no alto ou baixo valor numérico. Os cenários que exigem esta função incluem o aumento da margem de lucro, o preço mais alto, o preço mais baixo ou a contagem de downloads. Esta função só pode ser utilizada com campos duplos e inteiros.<br />     Para a função `magnitude`, pode inverter a gama, de alto a baixo, se quiser o padrão inverso (por exemplo, para aumentar os itens a preços mais baixos do que os itens mais caros). Dada uma gama de preços de $100 a $1, você definiria `boostingRangeStart` a 100 e `boostingRangeEnd` a 1 para aumentar os itens mais baratos.<br />-   `distance` deve ser usado quando pretende aumentar por proximidade ou localização geográfica. Esta função só pode ser utilizada com campos `Edm.GeographyPoint`.<br />-   `tag` deve ser usado quando pretende aumentar por etiquetas em comum entre documentos e consultas de pesquisa. Esta função só pode ser utilizada com campos `Edm.String` e `Collection(Edm.String)`.<br /><br /> **Regras para o uso de funções**<br /><br /> Tipo de função (`freshness`, `magnitude`, `distance`), `tag` deve ser inferior.<br /><br /> As funções não podem incluir valores nulos ou vazios. Especificamente, se incluir o nome de campo, tem que defini-lo em algo.<br /><br /> As funções só podem ser aplicadas em campos filtrantes. Consulte [Create &#40;Index Azure Cognitive&#41; Search REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter mais informações sobre campos filtrados.<br /><br /> As funções só podem ser aplicadas aos campos definidos na coleção de campos de um índice.|  

 Depois de definido o índice, construa o índice carregando o esquema do índice, seguido de documentos. Consulte [Criar &#40;API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) de pesquisa cognitiva index Azure e [adicionar, atualizar ou eliminar documentos &#40;Azure Cognitive Search REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para obter instruções sobre estas operações. Uma vez construído o índice, deve ter um perfil de pontuação funcional que funcione com os seus dados de pesquisa.  

##  <a name="bkmk_template"></a>Modelo  
 Esta secção mostra a sintaxe e o modelo para marcar perfis. Consulte a referência de [atributos indexados](#bkmk_indexref) na secção seguinte para descrições dos atributos.  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="bkmk_indexref"></a>Referência de atributos de índice  

> [!NOTE]  
>  Uma função de pontuação só pode ser aplicada em campos que sejam filtrados.  

|Atributo|Descrição|  
|---------------|-----------------|  
|`name`|Necessário. Este é o nome do perfil de pontuação. Segue as mesmas convenções de nomeação de um campo. Deve começar com uma letra, não pode conter pontos, cólons ou símbolos @, e não pode começar com a frase 'azureSearch' (sensível a caso).|  
|`text`|Contém a propriedade de pesos.|  
|`weights`|Opcional. Contém pares de valor de nome que cada um especifica um nome de campo e peso relativo. O peso relativo deve ser um número inteiro ou ponto flutuante positivo.<br /><br /> Os pesos são usados para indicar a importância de um campo pesquisável em relação ao outro.|  
|`functions`|Opcional. Uma função de pontuação só pode ser aplicada em campos que sejam filtrados.|  
|`type`|Necessário para marcar funções. Indica o tipo de função a utilizar. Os valores válidos incluem magnitude, frescura, distância e etiqueta. Pode incluir mais do que uma função em cada perfil de pontuação. O nome da função deve ser minúsculo.|  
|`boost`|Necessário para marcar funções. Um número positivo usado como multiplicador para pontuação bruta. Não pode ser igual a 1.|  
|`fieldname`|Necessário para marcar funções. Uma função de pontuação só pode ser aplicada em campos que fazem parte da coleção de campo do índice, e que são filtrados. Além disso, cada tipo de função introduz restrições adicionais (a frescura é utilizada com campos de data, magnitude com campos inteiros ou duplos, e distância com campos de localização). Só pode especificar um único campo por definição de função. Por exemplo, para usar a magnitude duas vezes no mesmo perfil, você precisaria incluir duas definições de magnitude, uma para cada campo.|  
|`interpolation`|Necessário para marcar funções. Define a inclinação para a qual o aumento da pontuação aumenta desde o início da gama até ao fim da gama. Os valores válidos incluem Linear (padrão), Constante, Quadrático e Logarítmico. Consulte [as interpolações de set](#bkmk_interpolation) para obter detalhes.|  
|`magnitude`|A função de pontuação de magnitude é usada para alterar classificações com base na gama de valores para um campo numérico. Alguns dos exemplos de utilização mais comuns são:<br /><br /> **-   classificações Star:** Altere a pontuação com base no valor dentro do campo "Star Rating". Quando dois itens são relevantes, o item com a classificação mais alta será apresentado primeiro.<br />Margem **-   :** Quando dois documentos são relevantes, um retalhista pode querer aumentar os documentos que têm margens mais elevadas primeiro.<br />-   **Conta o Click:** Para aplicações que rastreiam clique através de ações a produtos ou páginas, você poderia usar magnitude para aumentar itens que tendem a obter mais tráfego.<br />-   **Download conta:** Para aplicações que rastreiam downloads, a função de magnitude permite-lhe aumentar itens que têm mais downloads.|  
|`magnitude` &#124; `boostingRangeStart`|Define o valor inicial do intervalo sobre o qual a magnitude é pontuada. O valor deve ser um número inteiro ou de ponto flutuante. Para as classificações de estrelas de 1 a 4, este seria 1. Para margens acima de 50%, seriam 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Define o valor final do intervalo sobre o qual a magnitude é pontuada. O valor deve ser um número inteiro ou de ponto flutuante. Para as classificações de estrelas de 1 a 4, este seria 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Os valores válidos são verdadeiros ou falsos (predefinidos). Quando definido como verdadeiro, o impulso total continuará a aplicar-se a documentos que têm um valor para o campo alvo que é superior à extremidade superior da gama. Se for falso, o aumento desta função não será aplicado a documentos com um valor para o campo-alvo que cai fora do alcance.|  
|`freshness`|A função de pontuação de frescura é usada para alterar as pontuações de classificação para itens baseados em valores em `DateTimeOffset` campos. Por exemplo, um item com uma data mais recente pode ser classificado acima de itens mais antigos.<br /><br /> Também é possível classificar itens como eventos de calendário com datas futuras de modo a que os itens mais próximos do presente possam ser classificados mais alto do que itens no futuro.<br /><br /> No atual lançamento do serviço, uma extremidade da gama será fixada ao tempo atual. O outro extremo é um tempo no passado baseado no `boostingDuration`. Para aumentar uma série de vezes no futuro, use um `boostingDuration`negativo .<br /><br /> A taxa a que o aumento muda de um intervalo máximo e mínimo é determinada pela Interpolação aplicada ao perfil de pontuação (ver o valor abaixo). Para inverter o fator de impulsionamento aplicado, escolha um fator de reforço inferior a 1.|  
|`freshness` &#124; `boostingDuration`|Estabelece um período de validade após o qual o reforço irá parar para um determinado documento. Consulte O aumento do [conjuntoDurana](#bkmk_boostdur) seguinte secção para sintaxe e exemplos.|  
|`distance`|A função de pontuação à distância é usada para afetar a pontuação de documentos com base na proximidade ou longe que estão relativamente a uma localização geográfica de referência. A localização de referência é dada como parte da consulta num parâmetro (utilizando a opção de corda `scoringParameterquery`) como argumento lon,lat.|  
|`distance` &#124; `referencePointParameter`|Um parâmetro a ser passado em consultas para usar como localização de referência. `scoringParameter` é um parâmetro de consulta. Consulte [ &#40;documentos de pesquisa Azure Cognitive Search REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter descrições de parâmetros de consulta.|  
|`distance` &#124; `boostingDistance`|Um número que indica a distância em quilómetros do local de referência onde termina a gama de reforços.|  
|`tag`|A função de pontuação de etiquetas é usada para afetar a pontuação de documentos com base em etiquetas em documentos e consultas de pesquisa. Os documentos que tenham etiquetas em comum com a consulta de pesquisa serão reforçados. As etiquetas para a consulta de pesquisa são fornecidas como um parâmetro de pontuação em cada pedido de pesquisa (utilizando a opção de cadeia `scoringParameterquery`).|  
|`tag` &#124; `tagsParameter`|Um parâmetro a ser passado em consultas para especificar etiquetas para um pedido específico. `scoringParameter` é um parâmetro de consulta. Consulte [ &#40;documentos de pesquisa Azure Cognitive Search REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter descrições de parâmetros de consulta.|  
|`functionAggregation`|Opcional. Só se aplica quando as funções são especificadas. Os valores válidos incluem: soma (padrão), média, mínima, máxima e primeira Correspondência. Uma pontuação de pesquisa é um único valor que é calculado a partir de várias variáveis, incluindo múltiplas funções. Este atributo indica como os impulsos de todas as funções são combinados num único impulso agregado que é aplicado à pontuação do documento base. A pontuação base baseia-se no valor [tf-idf](http://www.tfidf.com/) calculado a partir do documento e na consulta de pesquisa.|  
|`defaultScoringProfile`|Ao executar um pedido de pesquisa, se não for especificado o perfil de pontuação, então a pontuação padrão é usada (apenas[tf-idf).](http://www.tfidf.com/)<br /><br /> Um nome de perfil de pontuação padrão pode ser definido aqui, fazendo com que a Pesquisa Cognitiva Azure utilize esse perfil quando não é dado nenhum perfil específico no pedido de pesquisa.|  

##  <a name="bkmk_interpolation"></a>Definir interpolações  
 As interpolações permitem definir a forma da inclinação utilizada para a pontuação. Como a pontuação é alta a baixa, a inclinação está sempre a diminuir, mas a interpolação determina a curva da inclinação descendente. Podem ser utilizadas as seguintes interpolações:  

|||  
|-|-|  
|`linear`|Para itens que estejam dentro do alcance máximo e min, o impulso aplicado ao item será feito em uma quantidade em constante diminuição. Linear é a interpolação padrão para um perfil de pontuação.|  
|`constant`|Para itens que estejam dentro do intervalo de início e de fim, será aplicado um impulso constante aos resultados do ranking.|  
|`quadratic`|Em comparação com uma interpolação linear que tem um aumento constantemente diminuindo, a Quadratic irá inicialmente diminuir a um ritmo menor e, depois, à medida que se aproxima da faixa de finalidade, diminui a um intervalo muito mais elevado. Esta opção de interpolação não é permitida nas funções de pontuação de etiquetas.|  
|`logarithmic`|Em comparação com uma interpolação linear que tem um aumento constantemente diminuindo, a Logarithmic irá inicialmente diminuir a um ritmo mais elevado e, em seguida, à medida que se aproxima da gama final, diminui a um intervalo muito menor. Esta opção de interpolação não é permitida nas funções de pontuação de etiquetas.|  

 ![Linhas constantes, lineares, quadráticas, log10 no gráfico](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="bkmk_boostdur"></a>Definir aumento Duração  
 `boostingDuration` é um atributo da função `freshness`. Usa-o para definir um período de validade após o qual o reforço irá parar para um determinado documento. Por exemplo, para impulsionar uma linha de produtos ou marca por um período promocional de 10 dias, especificaria o período de 10 dias como "P10D" para esses documentos.  

 `boostingDuration` deve ser formatada como um valor XSD "daytimeduration" (um subconjunto restrito de um valor de duração ISO 8601). O padrão para isto é: "P[nD][T[nH][nM][nS]]".  

 A tabela que se segue dá vários exemplos.  

|Duração|boostingDuration|  
|--------------|----------------------|  
|1 dia|"P1D"|  
|2 dias e 12 horas|"P2DT12H"|  
|15 minutos|"PT15M"|  
|30 dias, 5 horas, 10 minutos e 6,334 segundos|"P30DT5H10M6.334S"|  

 Para mais exemplos, consulte [XML Schema: Tipos de dados (W3.org site)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Consulte também  
   de [repouso de pesquisa cognitiva azure](https://docs.microsoft.com/rest/api/searchservice/)  
 [Criar &#40;o Índice Azure&#41; Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Pesquisa Cognitiva Azure .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  

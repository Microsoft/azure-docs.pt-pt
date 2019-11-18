---
title: Aumentar a classificação de pesquisa usando perfis de Pontuação
titleSuffix: Azure Cognitive Search
description: Aumente as pontuações de classificação de pesquisa para resultados de Pesquisa Cognitiva do Azure adicionando perfis de pontuação.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 60442ab101423d0a91fa35a7a12a0b930417af71
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113617"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Adicionar perfis de Pontuação a um índice de Pesquisa Cognitiva do Azure

  A pontuação refere-se à computação de uma *Pontuação de pesquisa* para cada item retornado nos resultados da pesquisa. A pontuação é um indicador da relevância de um item no contexto da operação de pesquisa atual. Quanto maior a pontuação, mais relevante será o item. Nos resultados da pesquisa, os itens são classificados ordenados de alto para baixo, com base nas pontuações de pesquisa calculadas para cada item.  

 O Pesquisa Cognitiva do Azure usa a pontuação padrão para calcular uma pontuação inicial, mas você pode personalizar o cálculo por meio de um *perfil de Pontuação*. Os perfis de Pontuação oferecem maior controle sobre a classificação de itens nos resultados da pesquisa. Por exemplo, você pode desejar aumentar os itens com base em seu potencial de receita, promover itens mais novos ou talvez aumentar os itens que estão no estoque muito longos.  

 Um perfil de pontuação faz parte da definição de índice, composta de campos, funções e parâmetros ponderados.  

 Para dar uma ideia de como um perfil de Pontuação se parece, o exemplo a seguir mostra um perfil simples chamado ' geo '. Essa delas aumenta os itens que têm o termo de pesquisa no campo **hotelname** . Ele também usa a função `distance` para favorecer itens que estão dentro de dez quilômetros do local atual. Se alguém Pesquisar no termo "Estalagem" e "Estalagem" for parte do nome do Hotel, os documentos que incluírem Hotéis com "Estalagem" dentro de um raio de 10 KM do local atual aparecerão mais altos nos resultados da pesquisa.  


```  
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


 Para usar esse perfil de pontuação, sua consulta é formulada para especificar o perfil na cadeia de caracteres de consulta. Na consulta abaixo, observe o parâmetro de consulta `scoringProfile=geo` na solicitação.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 Essa consulta pesquisa o termo ' Estalagem ' e passa o local atual. Observe que essa consulta inclui outros parâmetros, como `scoringParameter`. Os parâmetros de consulta são descritos em [Pesquisar documentos &#40;Azure pesquisa cognitiva&#41;API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Clique em [exemplo](#bkmk_ex) para examinar um exemplo mais detalhado de um perfil de pontuação.  

## <a name="what-is-default-scoring"></a>O que é a pontuação padrão?  
 A pontuação computa uma pontuação de pesquisa para cada item em um conjunto de resultados ordenados de classificação. Cada item em um conjunto de resultados de pesquisa é atribuído a uma pontuação de pesquisa e, em seguida, classificado como mais alto para o mais baixo. Os itens com as pontuações mais altas são retornados ao aplicativo. Por padrão, os 50 principais são retornados, mas você pode usar o parâmetro `$top` para retornar um número menor ou maior de itens (até 1000 em uma única resposta).  

A pontuação de pesquisa é calculada com base nas propriedades estatísticas dos dados e da consulta. O Azure Pesquisa Cognitiva localiza documentos que incluem os termos de pesquisa na cadeia de caracteres de consulta (alguns ou todos, dependendo de `searchMode`), favorecendo documentos que contenham muitas instâncias do termo de pesquisa. A pontuação de pesquisa aumentará ainda mais se o termo for raro em todo o índice de dados, mas comum no documento. A base dessa abordagem para calcular a relevância é conhecida como [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) ou termo Frequency-inverter frequência de documento.  

 Supondo que não haja nenhuma classificação personalizada, os resultados são então classificados por Pontuação de pesquisa antes de serem retornados para o aplicativo de chamada. Se $top não for especificado, 50 itens com a pontuação de pesquisa mais alta serão retornados.  

 Os valores de Pontuação de pesquisa podem ser repetidos ao longo de um conjunto de resultados. Por exemplo, você pode ter 10 itens com uma pontuação de 1,2, 20 itens com uma pontuação de 1,0 e 20 itens com uma pontuação de 0,5. Quando várias ocorrências têm a mesma pontuação de pesquisa, a ordem dos mesmos itens pontuados não é definida e não é estável. Execute a consulta novamente e você poderá ver os itens mudarem para a posição. Considerando dois itens com uma pontuação idêntica, não há garantia de qual deles aparece primeiro.  

## <a name="when-to-use-custom-scoring"></a>Quando usar a pontuação personalizada  
 Você deve criar um ou mais perfis de Pontuação quando o comportamento de classificação padrão não for suficiente para atender aos seus objetivos de negócios. Por exemplo, você pode decidir que a relevância da pesquisa deve favorecer itens recém-adicionados. Da mesma forma, você pode ter um campo que contenha margem de lucro ou algum outro campo indicando o potencial de receita. Aumentar as ocorrências que trazem benefícios para sua empresa pode ser um fator importante na decisão de usar perfis de pontuação.  

 A ordenação baseada em relevância também é implementada por meio de perfis de pontuação. Considere as páginas de resultados da pesquisa que você usou no passado que lhe permitem classificar por preço, data, classificação ou relevância. No Azure Pesquisa Cognitiva, os perfis de Pontuação orientam a opção ' relevância '. A definição de relevância é controlada por você, predicado em objetivos de negócios e o tipo de experiência de pesquisa que você deseja fornecer.  

##  <a name="bkmk_ex"></a>Exemplo  
 Como observado anteriormente, a pontuação personalizada é implementada por meio de um ou mais perfis de Pontuação definidos em um esquema de índice.  

 Este exemplo mostra o esquema de um índice com dois perfis de Pontuação (`boostGenre`, `newAndHighlyRated`). Qualquer consulta em relação a esse índice que inclua um dos perfis como um parâmetro de consulta usará o perfil para pontuar o conjunto de resultados.  

```  
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
 Para implementar o comportamento de Pontuação personalizado, adicione um perfil de pontuação ao esquema que define o índice. Você pode ter até 100 perfis de pontuação em um índice (consulte [limites de serviço](search-limits-quotas-capacity.md)), mas você só pode especificar um perfil no tempo em qualquer consulta específica.  

 Comece com o [modelo](#bkmk_template) fornecido neste tópico.  

 Forneça um nome. Os perfis de pontuação são opcionais, mas se você adicionar um, o nome será necessário. Certifique-se de seguir as convenções de nomenclatura para campos (começa com uma letra, evita caracteres especiais e palavras reservadas). Consulte [regras &#40;de nomenclatura pesquisa cognitiva&#41; do Azure](https://docs.microsoft.com/rest/api/searchservice/naming-rules) para obter a lista completa.  

 O corpo do perfil de pontuação é construído a partir de campos e funções ponderados.  

|||  
|-|-|  
|**Gramatura**|Especifique os pares de nome-valor que atribuem um peso relativo a um campo. No [exemplo](#bkmk_ex), os campos campos AlbumTitle, gênero e artistas são aumentados 1,5, 5 e 2, respectivamente. Por que o gênero aumentou muito mais do que os outros? Se a pesquisa for realizada sobre os dados que são um pouco homogêneos (como é o caso com ' gênero ' no `musicstoreindex`), talvez seja necessário uma variação maior nos pesos relativos. Por exemplo, na `musicstoreindex`, ' Rock ' aparece como um gênero e em descrições de gênero com frase idêntica. Se você quiser que gênero supere a descrição do gênero, o campo gênero precisará de um peso relativo muito maior.|  
|**Funções**|Usado quando cálculos adicionais são necessários para contextos específicos. Os valores válidos são `freshness`, `magnitude`, `distance`e `tag`. Cada função tem parâmetros que são exclusivos para ele.<br /><br /> -   `freshness` deve ser usado quando você quiser aumentar de quão novo ou antigo um item é. Essa função só pode ser usada com campos de `datetime` (EDM. DataTimeOffset). Observe que o atributo `boostingDuration` é usado somente com a função `freshness`.<br />-   `magnitude` deve ser usado quando você deseja aumentar com base em quão alto ou baixo um valor numérico é. Os cenários que chamam essa função incluem aumento por margem de lucro, preço mais alto, preço mais baixo ou uma contagem de downloads. Essa função só pode ser usada com campos Double e Integer.<br />     Para a função `magnitude`, você pode reverter o intervalo, de alto para baixo, se desejar o padrão inverso (por exemplo, para aumentar os itens com preço mais baixo mais do que os itens com preço mais alto). Dado um intervalo de preços de $100 a $1, você definiria `boostingRangeStart` em 100 e `boostingRangeEnd` em 1 para impulsionar os itens com preço menor.<br />-   `distance` deve ser usado quando você deseja aumentar por proximidade ou localização geográfica. Essa função só pode ser usada com `Edm.GeographyPoint` campos.<br />-   `tag` deve ser usado quando você desejar aumentar por marcas em comum entre documentos e consultas de pesquisa. Essa função só pode ser usada com campos `Edm.String` e `Collection(Edm.String)`.<br /><br /> **Regras para usar funções**<br /><br /> Tipo de função (`freshness`, `magnitude`, `distance`), `tag` deve estar em letras minúsculas.<br /><br /> As funções não podem incluir valores nulos ou vazios. Especificamente, se você incluir FieldName, precisará defini-lo como algo.<br /><br /> As funções só podem ser aplicadas a campos filtráveis. Consulte [criar índice &#40;Azure pesquisa cognitiva&#41; API REST](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter mais informações sobre campos filtráveis.<br /><br /> As funções só podem ser aplicadas a campos que são definidos na coleção Fields de um índice.|  

 Depois que o índice for definido, compile o índice carregando o esquema de índice, seguido por documentos. Consulte [criar índice &#40;Azure pesquisa cognitiva API&#41; REST](https://docs.microsoft.com/rest/api/searchservice/create-index) e [Adicionar, atualizar ou excluir documentos &#40;Azure pesquisa cognitiva API&#41; REST](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para obter instruções sobre essas operações. Depois que o índice for criado, você deverá ter um perfil de pontuação funcional que funcione com seus dados de pesquisa.  

##  <a name="bkmk_template"></a>Modelos  
 Esta seção mostra a sintaxe e o modelo para perfis de pontuação. Consulte a [referência de atributos de índice](#bkmk_indexref) na próxima seção para obter descrições dos atributos.  

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
>  Uma função de Pontuação só pode ser aplicada a campos que são filtráveis.  

|Atributo|Descrição|  
|---------------|-----------------|  
|`Name`|Necessário. Este é o nome do perfil de pontuação. Ele segue as mesmas convenções de nomenclatura de um campo. Ele deve começar com uma letra, não pode conter pontos, dois-pontos ou @ símbolos e não pode começar com a frase ' azureSearch ' (diferencia maiúsculas de minúsculas).|  
|`Text`|Contém a propriedade pesos.|  
|`Weights`|Opcional. Um par de nome-valor que especifica um nome de campo e peso relativo. O peso relativo deve ser um inteiro positivo ou um número de ponto flutuante. O valor máximo é Int32. MaxValue.<br /><br /> Você pode especificar o nome do campo sem um peso correspondente. Os pesos são usados para indicar a importância de um campo em relação a outro.|  
|`Functions`|Opcional. Uma função de Pontuação só pode ser aplicada a campos que são filtráveis.|  
|`Type`|Necessário para funções de pontuação. Indica o tipo de função a ser usado. Os valores válidos incluem magnitude, atualização, distância e marca. Você pode incluir mais de uma função em cada perfil de pontuação. O nome da função deve estar em letras minúsculas.|  
|`Boost`|Necessário para funções de pontuação. Um número positivo usado como multiplicador para pontuação bruta. Ele não pode ser igual a 1.|  
|`Fieldname`|Necessário para funções de pontuação. Uma função de Pontuação só pode ser aplicada a campos que fazem parte da coleção de campos do índice e que são filtráveis. Além disso, cada tipo de função introduz restrições adicionais (a atualização é usada com campos DateTime, magnitude com campos inteiros ou duplos e distância com campos de localização). Você só pode especificar um único campo por definição de função. Por exemplo, para usar a magnitude duas vezes no mesmo perfil, você precisaria incluir duas magnitudes de definições, uma para cada campo.|  
|`Interpolation`|Necessário para funções de pontuação. Define a inclinação para a qual o aumento da Pontuação aumenta desde o início do intervalo até o fim do intervalo. Os valores válidos incluem linear (padrão), constante, quadrática e logarítmica. Consulte [definir interpolações](#bkmk_interpolation) para obter detalhes.|  
|`magnitude`|A função de Pontuação de magnitude é usada para alterar as classificações com base no intervalo de valores de um campo numérico. Alguns dos exemplos de uso mais comuns disso são:<br /><br /> -   **classificações de estrelas:** altere a pontuação com base no valor do campo "classificação por estrelas". Quando dois itens forem relevantes, o item com a classificação mais alta será exibido primeiro.<br />Margem de -    **:** quando dois documentos são relevantes, um varejista pode desejar aumentar os documentos que têm margens mais altas primeiro.<br />-   **contagens de cliques:** para aplicativos que acompanham ações de cliques em produtos ou páginas, você pode usar a magnitude para impulsionar itens que tendem a obter o máximo de tráfego.<br />-   **contagens de download:** para aplicativos que acompanham downloads, a função de magnitude permite que você aumente os itens que têm mais downloads.|  
|`magnitude` &#124; `boostingRangeStart`|Define o valor inicial do intervalo em que a magnitude é pontuada. O valor deve ser um número inteiro ou de ponto flutuante. Para classificações por estrelas de 1 a 4, isso seria 1. Para obter margens acima de 50%, isso seria 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Define o valor final do intervalo em que a magnitude é classificada. O valor deve ser um número inteiro ou de ponto flutuante. Para classificações por estrelas de 1 a 4, isso seria 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Os valores válidos são true ou false (padrão). Quando definido como true, o aumento completo continuará a ser aplicado a documentos que têm um valor para o campo de destino que é maior do que a extremidade superior do intervalo. Se for false, o aumento dessa função não será aplicado a documentos com um valor para o campo de destino que está fora do intervalo.|  
|`freshness`|A função de Pontuação de atualização é usada para alterar as pontuações de classificação de itens com base em valores em campos de `DateTimeOffset`. Por exemplo, um item com uma data mais recente pode ser classificado como mais alto do que os itens mais antigos.<br /><br /> Também é possível classificar itens como eventos de calendário com datas futuras, de forma que os itens mais próximos da apresentação possam ser classificados mais altos do que os itens no futuro.<br /><br /> Na versão atual do serviço, uma extremidade do intervalo será corrigida para a hora atual. A outra extremidade é uma hora no passado com base na `boostingDuration`. Para impulsionar um intervalo de vezes no futuro, use um `boostingDuration`negativo.<br /><br /> A taxa na qual as alterações de aumento de um intervalo máximo e mínimo é determinada pela interpolação aplicada ao perfil de Pontuação (consulte a figura abaixo). Para inverter o fator de aumento aplicado, escolha um fator de aumento inferior a 1.|  
|`freshness` &#124; `boostingDuration`|Define um período de expiração após o qual o aumento será interrompido para um determinado documento. Consulte [set boostingDuration](#bkmk_boostdur) na seção a seguir para obter a sintaxe e exemplos.|  
|`distance`|A função de Pontuação de distância é usada para afetar a pontuação de documentos com base em quão perto ou longe eles são relativos a uma localização geográfica de referência. O local de referência é fornecido como parte da consulta em um parâmetro (usando a opção de cadeia de caracteres `scoringParameterquery`) como um argumento Lon, Lat.|  
|`distance` &#124; `referencePointParameter`|Um parâmetro a ser passado em consultas para usar como local de referência. `scoringParameter` é um parâmetro de consulta. Consulte [Pesquisar documentos &#40;Azure pesquisa cognitiva&#41; API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter descrições dos parâmetros de consulta.|  
|`distance` &#124; `boostingDistance`|Um número que indica a distância em quilômetros do local de referência em que o intervalo de aumento termina.|  
|`tag`|A função de Pontuação de marca é usada para afetar a pontuação de documentos com base em marcas em documentos e consultas de pesquisa. Os documentos que têm marcas em comum com a consulta de pesquisa serão aumentados. As marcas da consulta de pesquisa são fornecidas como um parâmetro de pontuação em cada solicitação de pesquisa (usando a opção de `scoringParameterquery` cadeia de caracteres).|  
|`tag` &#124; `tagsParameter`|Um parâmetro a ser passado em consultas para especificar marcas para uma solicitação específica. `scoringParameter` é um parâmetro de consulta. Consulte [Pesquisar documentos &#40;Azure pesquisa cognitiva&#41; API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter descrições dos parâmetros de consulta.|  
|`functionAggregation`|Opcional. Aplica-se somente quando as funções são especificadas. Os valores válidos incluem: soma (padrão), média, mínimo, máximo e firstMatching. Uma pontuação de pesquisa é um valor único que é computado de várias variáveis, incluindo várias funções. Esse atributo indica como os aumentos de todas as funções são combinados em um único aumento agregado que, em seguida, é aplicado à pontuação de documento base. A pontuação base é baseada no valor [TF-IDF](http://www.tfidf.com/) computado do documento e da consulta de pesquisa.|  
|`defaultScoringProfile`|Ao executar uma solicitação de pesquisa, se nenhum perfil de pontuação for especificado, a pontuação padrão será usada ([TF-IDF](http://www.tfidf.com/) somente).<br /><br /> Um nome de perfil de Pontuação padrão pode ser definido aqui, fazendo com que o Azure Pesquisa Cognitiva Use esse perfil quando nenhum perfil específico é fornecido na solicitação de pesquisa.|  

##  <a name="bkmk_interpolation"></a>Definir interpolações  
 As interpolações permitem que você defina a forma da inclinação usada para pontuação. Como a pontuação é alta para a baixa, a inclinação está sempre diminuindo, mas a interpolação determina a curva da inclinação para baixo. As seguintes interpolações podem ser usadas:  

|||  
|-|-|  
|`Linear`|Para itens que estão dentro do intervalo máximo e mínimo, o aumento aplicado ao item será feito em um valor decrescente constantemente. Linear é a interpolação padrão para um perfil de pontuação.|  
|`Constant`|Para itens que estão dentro do intervalo inicial e final, um aumento constante será aplicado aos resultados da classificação.|  
|`Quadratic`|Em comparação com uma interpolação linear que tem um aumento que diminui constantemente, o quadrática inicialmente diminuirá em um ritmo menor e, em seguida, à medida que se aproximar do intervalo de término, ele diminui em um intervalo muito maior. Essa opção de interpolação não é permitida em funções de Pontuação de marca.|  
|`Logarithmic`|Em comparação com uma interpolação linear que tem um aumento que diminui constantemente, o logaritmo inicialmente diminuirá no ritmo mais alto e, em seguida, à medida que ele se aproximar do intervalo de término, ele diminui em um intervalo muito menor. Essa opção de interpolação não é permitida em funções de Pontuação de marca.|  

 ![Constantes, lineares, quadráticas, log10 linhas no grafo](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="bkmk_boostdur"></a>Definir boostingDuration  
 `boostingDuration` é um atributo da função `freshness`. Você o usa para definir um período de expiração após o qual o aumento será interrompido para um determinado documento. Por exemplo, para aumentar uma linha de produto ou marca para um período promocional de 10 dias, você deve especificar o período de 10 dias como "P10D" para esses documentos.  

 `boostingDuration` deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de duração ISO 8601). O padrão para isso é: "P [nD] [T [nH] [nM] [nS]]".  

 A tabela a seguir fornece vários exemplos.  

|Duração|boostingDuration|  
|--------------|----------------------|  
|1 dia|P1D|  
|2 dias e 12 horas|"P2DT12H"|  
|15 minutos|"PT15M"|  
|30 dias, 5 horas, 10 minutos e 6,334 segundos|"P30DT5H10M6.334S"|  

 Para obter mais exemplos, consulte [esquema XML: tipos de w3.org (site da Web)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Ver também  
   [REST do Azure pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice/)  
 [Criar índice &#40;pesquisa cognitiva API&#41; REST do Azure](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [SDK do .NET Pesquisa Cognitiva do Azure](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  

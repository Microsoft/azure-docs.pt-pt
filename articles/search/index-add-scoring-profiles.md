---
title: Adicionar perfis de classificação para um índice de pesquisa - Azure Search
description: Aumente as pontuações de classificação de pesquisa para resultados de pesquisa do Azure Search através da adição de perfis de classificação.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: eae7de00294a6a09cb7f942d11ee2391710fc55f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844306"
---
# <a name="add-scoring-profiles-to-an-azure-search-index"></a>Adicionar perfis de classificação para um índice da Azure Search

  Classificação refere-se para a computação de um *pontuação de pesquisa* para todos os itens nos resultados de pesquisa. A classificação é um indicador de relevância de um item no contexto da operação de pesquisa atual. Quanto maior for a pontuação, mais relevante o item. Nos resultados de pesquisa, os itens são ordenada de alto para baixo, com base nas pontuações da pesquisa calculadas para cada item de classificação.  

 O Azure Search utiliza a predefinição de classificação para computar uma pontuação inicial, mas pode personalizar o cálculo por meio de um *perfil de classificação*. Perfis de classificação dão-lhe maior controlo sobre a classificação de itens nos resultados da pesquisa. Por exemplo, pode querer aumentar itens com base em seu potencial de receita, promover itens mais recente ou, talvez aumentar itens que foram no inventário demasiado longo.  

 Um perfil de classificação é parte da definição do índice, compostas por campos ponderados, funções e parâmetros.  

 Para dar uma idéia da aparência um perfil de classificação, o exemplo seguinte mostra um perfil simples com o nome 'geo'. Isso aumenta a itens que tenham o termo de pesquisa na **hotelName** campo. Ele também usa o `distance` função para favorecer itens que estão dentro de dez quilómetros de distância da localização atual. Se alguém pesquisa o termo 'in' e 'in' acontece fazer parte do nome do hotel, documentos que incluem hotéis com scritpt dentro de um raio de 10 KM de distância da localização atual serão apresentada nos resultados da pesquisa superiores.  


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


 Para utilizar este perfil de classificação, a consulta é formulada para especificar o perfil na seqüência de consulta. Na consulta abaixo, tenha em atenção o parâmetro de consulta `scoringProfile=geo` no pedido.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2017-11-11  
```  

 Esta consulta pesquisa o termo "in" e passe a localização atual. Tenha em atenção que esta consulta inclui outros parâmetros, tais como `scoringParameter`. Parâmetros de consulta são descritos nas [documentos sobre pesquisa &#40;API de REST do serviço de pesquisa do Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Clique em [exemplo](#bkmk_ex) para examinar um exemplo mais detalhado de um perfil de classificação.  

## <a name="what-is-default-scoring"></a>O que é a classificação de predefinição?  
 Classificação computa uma pontuação de pesquisa para cada item num conjunto de resultados de ordem de classificação. Todos os itens num conjunto de resultados de pesquisa é atribuído uma pontuação de pesquisa, em seguida, com a classificação mais alta ao mais baixo. Os itens com as pontuações mais altas são devolvidos para o aplicativo. Por predefinição, são devolvidos os 50 principais, mas pode usar o `$top` parâmetro para devolver um número menor ou maior de itens (até 1000 numa única resposta).  

A pontuação de pesquisa é calculada com base nas propriedades de estatísticas dos dados e a consulta. O Azure Search localiza os documentos que incluem os termos de pesquisa na cadeia de consulta (alguns ou todos os, dependendo do `searchMode`), favorável documentos que contenham muitas instâncias do termo de pesquisa. A pontuação de pesquisa aumenta ainda mais elevada se o termo é raro entre o corpus de dados, mas comuns dentro do documento. A base para esta abordagem relevância de computação é conhecida como [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) ou a frequência de documento de frequência inverso do termo.  

 Resultados partindo do princípio de que não existe nenhuma classificação personalizada, são, em seguida, classificados pelo pontuação de pesquisa antes de eles são retornados ao aplicativo de chamada. Se não for especificado $top, 50 itens com a pontuação mais alta da pesquisa são devolvidos.  

 Valores de pontuação de pesquisa podem ser repetidos ao longo de um conjunto de resultados. Por exemplo, poderá ter 10 itens com uma pontuação de 1,2, 20 itens com uma pontuação de 1.0 e 20 itens com uma pontuação de 0,5. Quando vários resultados têm a mesmo pontuação de pesquisa, a ordem dos mesmos itens com a pontuação não está definida e não está estável. Executar a consulta novamente e poderá ver itens de posição de mudança. Tendo em conta dois itens com uma classificação idêntico, não há garantia qual aparece em primeiro lugar.  

## <a name="when-to-use-custom-scoring"></a>Quando utilizar a classificação personalizada  
 Deve criar um ou mais perfis de classificação quando a predefinição de comportamento de classificação não é o suficiente em atender seus objetivos empresariais. Por exemplo, poderá decidir que relevância de pesquisa deve de favorece itens adicionados recentemente. Da mesma forma, pode ter um campo que contém a margem de lucro ou algum outro campo que indicam potencial de receita. Priorização baseada em pedidos com êxito na trazer benefícios para sua empresa pode ser um fator importante decidir utilizar perfis de classificação.  

 Com base em relevância ordenação também é implementado através de perfis de classificação. Considere as páginas de resultados de pesquisa usou no passado, que permitem-lhe ordenar por preço, data, classificação ou relevância. No Azure Search, perfis de classificação da unidade a opção 'relevância'. A definição de relevância é controlada por si, vinculada ao fato dos objetivos comerciais e o tipo de experiência de pesquisa que deseja fornecer.  

##  <a name="bkmk_ex"></a> Exemplo  
 Conforme observado anteriormente, a classificação personalizada é implementada através de um ou mais perfis de classificação definidos num esquema de índice.  

 Este exemplo mostra o esquema de um índice com dois perfis de classificação (`boostGenre`, `newAndHighlyRated`). Qualquer consulta em relação a este índice que inclui o perfil, como um parâmetro de consulta irá utilizar o perfil para o conjunto de resultados de pontuação.  

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
 Para implementar o comportamento de classificação personalizado, adicione um perfil de classificação para o esquema que define o índice. Pode ter até mais de 100 perfis dentro de um índice de classificação (consulte [limites de serviço](search-limits-quotas-capacity.md)), mas só pode especificar um perfil no momento em qualquer determinada consulta.  

 Começar com o [modelo](#bkmk_template) fornecidas neste tópico.  

 Forneça um nome. Perfis de classificação são opcionais, mas se adicionar um, é necessário o nome. Certifique-se de que siga as convenções de nomenclatura para os campos (começa com uma letra, evita carateres especiais e palavras reservadas). Ver [regras de nomenclatura &#40;do Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/naming-rules) para obter a lista completa.  

 O corpo do perfil de classificação é construído a partir de campos ponderados e funções.  

|||  
|-|-|  
|**Pesos**|Especifique pares nome-valor que atribuir um peso relativo a um campo. Na [exemplo](#bkmk_ex), os campos albumTitle gênero e artistName são elevada 1.5, 5 e 2, respectivamente. Por que motivo é gênero estimulado muito superior a outras pessoas? Se a pesquisa é realizada sobre os dados que é um pouco homogéneo (tal como acontece com 'gênero' no `musicstoreindex`), poderá ter uma variação maior nos pesos relativos. Por exemplo, no `musicstoreindex`, 'rock' aparece como ambos os um gênero e em descrições de gênero escrito de forma idêntica. Se pretender que o gênero para superam a descrição do género, o campo de gênero será necessário muito peso relativo mais alto.|  
|**Funções**|Utilizado quando cálculos adicionais são necessários para contextos específicos. Os valores válidos são `freshness`, `magnitude`, `distance`, e `tag`. Cada função tem parâmetros que são exclusivos à mesma.<br /><br /> -   `freshness` deve ser usado quando deseja melhorar por novo ou antigo como um item é. Esta função só pode ser utilizada com `datetime` campos (edm. DataTimeOffset). Tenha em atenção a `boostingDuration` atributo é utilizado apenas com o `freshness` função.<br />-   `magnitude` deve ser usado quando deseja melhorar com base em como alta ou baixa é de um valor numérico. Os cenários que pedem a essa função incluem adaptativo por margem de lucro, o preço mais elevado, o preço mais baixo ou uma contagem de downloads. Esta função só pode ser utilizada com campos de double e integer.<br />     Para o `magnitude` função, pode reverter o intervalo, alto para baixo, se pretender que o padrão inverso (por exemplo, para itens de boost com preços inferiores mais do que um preço mais elevado de itens). Um determinado intervalo de preços de US $100 a US $1, definiria `boostingRangeStart` em 100 e `boostingRangeEnd` em 1 para aumentar os itens com preços inferiores.<br />-   `distance` deve ser usado quando deseja melhorar por proximidade ou localização geográfica. Esta função só pode ser utilizada com `Edm.GeographyPoint` campos.<br />-   `tag` deve ser usado quando deseja melhorar por etiquetas em comum entre documentos e consultas de pesquisa. Esta função só pode ser utilizada com `Edm.String` e `Collection(Edm.String)` campos.<br /><br /> **Regras para a utilização de funções**<br /><br /> Tipo de função (`freshness`, `magnitude`, `distance`), `tag` tem de estar em minúsculas.<br /><br /> As funções não podem incluir valores nulos nem vazios. Especificamente, se incluir fieldname, terá de configurá-lo para algo.<br /><br /> As funções só podem ser aplicadas para os campos filtráveis. Ver [Create Index &#40;API de REST do serviço de pesquisa do Azure&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter mais informações sobre os campos filtráveis.<br /><br /> As funções só podem ser aplicadas aos campos que estão definidos na coleção de campos de um índice.|  

 Depois do índice é definido, crie o índice através do carregamento do esquema de índice, seguido de documentos. Ver [Create Index &#40;API de REST do serviço de pesquisa do Azure&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) e [adicionar, atualizar ou eliminar documentos &#40;API de REST do serviço de pesquisa do Azure&#41; ](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para obter instruções sobre estas operações. Depois do índice é criado, deve ter um perfil de classificação funcional que funciona com os seus dados de pesquisa.  

##  <a name="bkmk_template"></a> Modelo  
 Esta secção mostra a sintaxe e o modelo para perfis de classificação. Consulte a [referência de atributos de índice](#bkmk_indexref) na secção seguinte para obter descrições dos atributos.  

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

##  <a name="bkmk_indexref"></a> Referência de atributos de índice  

> [!NOTE]  
>  Só pode ser aplicada uma função de classificação para os campos que podem ser filtrados.  

|Atributo|Descrição|  
|---------------|-----------------|  
|`Name`|Necessário. Este é o nome do perfil de classificação. Ela segue as mesmas convenções de nomenclatura de um campo. Ele tem de começar com uma letra, não pode conter pontos, dois pontos ou @ símbolos e não pode começar com a frase "azureSearch" (diferencia maiúsculas de minúsculas).|  
|`Text`|Contém a propriedade de pesos.|  
|`Weights`|Opcional. Um par de nome-valor que especifica um nome de campo e o peso relativo. Peso relativo tem de ser um número inteiro positivo ou o número de vírgula flutuante. O valor máximo é int32. MaxValue.<br /><br /> Pode especificar o nome do campo sem um peso correspondente. Pesos são utilizados para indicar a importância de um campo em relação ao outro.|  
|`Functions`|Opcional. Tenha em atenção que só pode ser aplicada uma função de classificação para os campos que podem ser filtrados.|  
|`Type`|É necessário para as funções de classificação. Indica o tipo de função a utilizar. Valores válidos incluem magnitude, atualização, distância e etiqueta. Pode incluir mais do que uma função em cada perfil de classificação. O nome da função tem de estar em minúsculas.|  
|`Boost`|É necessário para as funções de classificação. Um número positivo utilizado como multiplicador para a classificação não processada. Não pode ser igual a 1.|  
|`Fieldname`|É necessário para as funções de classificação. Só pode ser aplicada uma função de classificação para os campos que fazem parte da coleção de campo do índice e que são filtrável. Além disso, cada tipo de função apresenta restrições adicionais (atualização é utilizada com campos de datetime, magnitude com o número inteiro ou duplo campos e a distância com campos de localização). Só pode especificar um único campo por definição de função. Por exemplo, para utilizar a magnitude duas vezes no mesmo perfil, precisaria incluir duas magnitude de definições, um para cada campo.|  
|`Interpolation`|É necessário para as funções de classificação. Define o declive para o qual a classificação de aumento das classificações aumentam desde o início do intervalo no final do intervalo. Valores válidos incluem Linear (predefinição), constante, quadrática e logarítmica. Ver [definir interpolações](#bkmk_interpolation) para obter detalhes.|  
|`magnitude`|A função de classificação de magnitude é utilizada para alterar as classificações com base no intervalo de valores para um campo numérico. Alguns dos exemplos de utilização mais comuns disso são:<br /><br /> -   **Classificações em estrela:** Altera a classificação com base no valor dentro do campo "Estrela de classificação". Quando dois itens são relevantes, será apresentado o item com a classificação mais elevada primeiro.<br />-   **Margin:** Quando dois documentos são relevantes, um varejista pode querer aumentar documentos com margens superior pela primeira vez.<br />-   **Clique em contagens de:** Para aplicativos que controlam clicam por meio de ações de produtos ou páginas, poderia usar a magnitude para itens de boost que tendem a ficar mais tráfego.<br />-   **Contagens de download:** Para aplicações que os downloads de controle, o permite de função de magnitude que aumente itens que têm mais downloads.|  
|`magnitude` &#124; `boostingRangeStart`|Define o valor inicial do intervalo sobre o qual é classificada a magnitude. O valor tem de ser um número inteiro ou o número de vírgula flutuante. Para obter classificações em estrelas de 1 a 4, isso seria 1. Para margens superiores a 50%, isso seria 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Define o valor final do intervalo sobre o qual é classificada a magnitude. O valor tem de ser um número inteiro ou o número de vírgula flutuante. Para obter classificações em estrelas de 1 a 4, isso seria 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Valores válidos são VERDADEIRO ou FALSO (predefinição). Quando definido como true, o aumento total irá continuar a aplicar aos documentos que tenham um valor para o campo de destino que é superior à extremidade superior do intervalo. Se for FALSO, a intensificação desta função não será aplicada aos documentos que tenham um valor para o campo de destino que esteja fora do intervalo.|  
|`freshness`|A função de classificação de atualização é utilizada para alterar pontuações de classificação para os itens com base nos valores no `DateTimeOffset` campos. Por exemplo, um item com uma data mais recente pode ser classificado superior do que os itens mais antigos.<br /><br /> Tenha em atenção que também é possível classificar itens como eventos do calendário com datas futuras, de modo a que os itens mais próximo ao presente podem ser classificados superior itens ainda mais no futuro.<br /><br /> Na versão atual do serviço, será corrigida numa extremidade do intervalo para a hora atual. Outra extremidade é um momento no passado, com base no `boostingDuration`. Para aumentar um intervalo de vezes que no futuro, utilize um negativo `boostingDuration`.<br /><br /> A velocidade a que o adaptativo é alterado de um máximo e mínimo intervalo é determinado pela interpolação aplicado para o perfil de classificação (consulte a figura abaixo). Para reverter o fator adaptativo aplicado, escolha um fator de aumento de menos de 1.|  
|`freshness` &#124; `boostingDuration`|Define um período de expiração após o qual os aumentos param para um documento em particular. Ver [definir boostingDuration](#bkmk_boostdur) na seção a seguir para sintaxe e exemplos.|  
|`distance`|Fechar da classificação dos documentos com base em como a distância de função de classificação é usada para afetar ou relativa a uma localização geográfica de referência. A localização de referência é fornecida como parte da consulta num parâmetro (usando o `scoringParameterquery` opção de cadeias de caracteres) como um lon, o argumento de lat.|  
|`distance` &#124; `referencePointParameter`|Um parâmetro para ser transmitido nas consultas para utilizar como localização de referência. `scoringParameter` é um parâmetro de consulta. Ver [documentos sobre pesquisa &#40;API de REST do serviço de pesquisa do Azure&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter descrições dos parâmetros de consulta.|  
|`distance` &#124; `boostingDistance`|Um número que indica a distância em quilômetros da localização de referência onde termina o intervalo de aumento.|  
|`tag`|A etiqueta de classificação a função é usada para afetar a classificação dos documentos com base em etiquetas em documentos e consultas de pesquisa. Vão ser aumentados documentos que têm em comum com a consulta de pesquisa. As etiquetas para a consulta de pesquisa é fornecido como um parâmetro de classificação em cada solicitação de pesquisa (usando o `scoringParameterquery` opção de cadeias de caracteres).|  
|`tag` &#124; `tagsParameter`|Um parâmetro a ser transmitido nas consultas para especificar etiquetas para uma determinada solicitação. `scoringParameter` é um parâmetro de consulta. Ver [documentos sobre pesquisa &#40;API de REST do serviço de pesquisa do Azure&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter descrições dos parâmetros de consulta.|  
|`functionAggregation`|Opcional. Aplica-se apenas quando as funções são especificadas. Valores válidos incluem: (predefinição) de soma, média, mínimo, máximo e firstMatching. Uma pontuação de pesquisa é o único valor que é calculado a partir de várias variáveis, incluindo várias funções. Este atributo indica como a aumenta a competitividade de todas as funções são combinadas num único agregado aumente que, em seguida, é aplicado para a classificação de documento base. A pontuação básica baseia-se sobre o [tf-idf](http://www.tfidf.com/) valor calculado a partir do documento e a consulta de pesquisa.|  
|`defaultScoringProfile`|Ao executar um pedido de pesquisa, se nenhum perfil de classificação é especificada, em seguida, é utilizada a predefinição de classificação ([tf-idf](http://www.tfidf.com/) apenas).<br /><br /> Um padrão de nome do perfil de classificação pode ser definido aqui, fazendo com que o Azure Search utilizar esse perfil quando nenhum perfil específico é fornecido no pedido de pesquisa.|  

##  <a name="bkmk_interpolation"></a> Conjunto interpolações  
 Interpolações permitem-lhe definir a forma do declive utilizado para a classificação. Como a classificação é alta a baixa, o declive está sempre a diminuir, mas a interpolação determina a curva do declive descendente. Podem ser utilizadas as interpolações seguintes:  

|||  
|-|-|  
|`Linear`|Para itens que estão dentro do intervalo máximo e mínimo, o aumento aplicado ao item será efetuado numa quantidade constantemente diminuir. Linear é a interpolação predefinido para um perfil de classificação.|  
|`Constant`|Para itens que estejam no início e fim do intervalo, será aplicado um aumento constante para os resultados de classificação.|  
|`Quadratic`|Em comparação com uma interpolação Linear que tenha um aumento constante diminuir, quadrática inicialmente irá diminuir ao ritmo menor e, em seguida, à medida que se aproxima do intervalo final, ela diminui num intervalo muito superior. Esta opção de interpolação não é permitida na marca as funções de classificação.|  
|`Logarithmic`|Em comparação com uma interpolação Linear que tenha um aumento constante diminuir, logarítmica inicialmente sofrerão uma redução no ritmo superior e, em seguida, à medida que se aproxima do intervalo final, ela diminui num intervalo muito menor. Esta opção de interpolação não é permitida na marca as funções de classificação.|  

 ![Log10 constante, linear, quadrática, linhas no gráfico](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="bkmk_boostdur"></a> Conjunto boostingDuration  
 `boostingDuration` é um atributo do `freshness` função. Usá-lo para definir uma expiração do período após o qual os aumentos param para um documento em particular. Por exemplo, para impulsionar uma linha de produto ou marca durante um período promocional de 10 dias, deve especificar o período de 10 dias como "P10D" para esses documentos.  

 `boostingDuration` tem de ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto restrito de um valor de duração ISO 8601). O padrão para isto é: "P[nD][T[nH][nM][nS]]".  

 A tabela seguinte fornece vários exemplos.  

|Duração|boostingDuration|  
|--------------|----------------------|  
|1 dia|"P1D"|  
|2 dias e 12 horas|"P2DT12H"|  
|15 minutos|"PT15M"|  
|30 dias, 5 horas, 10 minutos, e 6.334 segundos|"P30DT5H10M6.334S"|  

 Para obter mais exemplos, consulte [esquema XML: Tipos de dados (W3.org web site)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Consulte também  
 [REST do serviço de pesquisa do Azure](https://docs.microsoft.com/rest/api/searchservice/)   
 [Criar índice &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [O Azure Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  

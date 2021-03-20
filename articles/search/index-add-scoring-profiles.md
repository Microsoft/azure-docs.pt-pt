---
title: Aumentar o ranking de pesquisa usando perfis de pontuação
titleSuffix: Azure Cognitive Search
description: Aumente as pontuações do ranking de pesquisa para os resultados da Pesquisa Cognitiva Azure adicionando perfis de pontuação.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 97797e309c32c6ea996d5ae1901b9a266a683173
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537638"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Adicionar perfis de classificação a um índice do Azure Cognitive Search

*A pontuação* calcula uma pontuação de pesquisa para cada item num conjunto de resultados ordenados de classificação. Cada item num conjunto de resultados de pesquisa é atribuído uma pontuação de pesquisa, em seguida, classificado mais alto a mais baixo.

 A Azure Cognitive Search utiliza a pontuação padrão para calcular uma pontuação inicial, mas pode personalizar o cálculo através de um *perfil de pontuação*. Os perfis de pontuação dão-lhe um maior controlo sobre o ranking de itens nos resultados de pesquisa. Por exemplo, pode querer aumentar itens com base no seu potencial de receita, promover itens mais recentes ou talvez aumentar itens que estão em inventário há demasiado tempo.  

 O segmento de vídeo que se segue avança para a forma como os perfis de pontuação funcionam na Azure Cognitive Search.
 
> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="scoring-profile-definitions"></a>Definições de perfis de pontuação

 Um perfil de pontuação faz parte da definição de índice, composto por campos ponderados, funções e parâmetros.  

 Para lhe dar uma ideia de como é um perfil de pontuação, o exemplo a seguir mostra um perfil simples chamado 'geo'. Este aqui impulsiona itens que têm o termo de pesquisa no campo do nome do **hotel.** Também usa a `distance` função para favorecer itens que estão a menos de dez quilómetros da localização atual. Se alguém pesquisar o termo 'inn', e 'inn' passar a fazer parte do nome do hotel, os documentos que incluem hotéis com 'inn' num raio de 10 KM da localização atual aparecerão mais altos nos resultados da pesquisa.  


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
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2020-06-30 
```  

 Esta consulta procura o termo 'inn' e passa na localização atual. Note que esta consulta inclui outros parâmetros, tais como `scoringParameter` . Os parâmetros de consulta são descritos em [Documentos de Busca &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents).  

 Clique [em Exemplo](#bkmk_ex) para rever um exemplo mais detalhado de um perfil de pontuação.  

## <a name="what-is-default-scoring"></a>O que é pontuação padrão?  
 A pontuação calcula uma pontuação de pesquisa para cada item num conjunto de resultados ordenados de classificação. Cada item num conjunto de resultados de pesquisa é atribuído uma pontuação de pesquisa, em seguida, classificado mais alto a mais baixo. Os itens com as pontuações mais altas são devolvidos à aplicação. Por predefinição, os 50 melhores são devolvidos, mas pode utilizar o `$top` parâmetro para devolver um número menor ou maior de itens (até 1000 numa única resposta).  

A pontuação de pesquisa é calculada com base nas propriedades estatísticas dos dados e na consulta. A Azure Cognitive Search encontra documentos que incluem os termos de pesquisa na cadeia de consulta (alguns ou todos, `searchMode` dependendo), favorecendo documentos que contêm muitos casos do termo de pesquisa. A pontuação de pesquisa sobe ainda mais se o termo for raro em todo o índice de dados, mas comum dentro do documento. A base para esta abordagem da relevância da computação é conhecida como [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) ou frequência de documento inverso de frequência a prazo.  

 Assumindo que não existe uma triagem personalizada, os resultados são classificados por pontuação de pesquisa antes de serem devolvidos à aplicação de chamada. Se não for especificado $top, 50 itens com a pontuação de pesquisa mais alta são devolvidos.  

 Os valores de pontuação de pesquisa podem ser repetidos ao longo de um conjunto de resultados. Por exemplo, pode ter 10 itens com uma pontuação de 1,2, 20 itens com uma pontuação de 1.0 e 20 itens com uma pontuação de 0,5. Quando vários hits têm a mesma pontuação de pesquisa, a encomenda dos mesmos itens pontuados não é definida, e não é estável. Faça a consulta novamente, e poderá ver itens mudar de posição. Tendo em conta dois itens com uma pontuação idêntica, não há garantias de qual deles aparece primeiro.  

## <a name="when-to-add-scoring-logic"></a>Quando adicionar lógica de pontuação 
 Deve criar um ou mais perfis de pontuação quando o comportamento de ranking padrão não for suficientemente longe no cumprimento dos objetivos do seu negócio. Por exemplo, pode decidir que a relevância da pesquisa deve favorecer itens recém-adicionados. Da mesma forma, você pode ter um campo que contém margem de lucro, ou algum outro campo indicando potencial de receita. Impulsionar sucessos que trazem benefícios para o seu negócio pode ser um fator importante na decisão de usar perfis de pontuação.  

 A encomenda baseada em relevância também é implementada através de perfis de pontuação. Considere as páginas de resultados de pesquisa que usou no passado que lhe permitem classificar por preço, data, classificação ou relevância. Na Pesquisa Cognitiva Azure, os perfis de pontuação conduzem a opção de "relevância". A definição de relevância é controlada por si, baseada em objetivos de negócio e no tipo de experiência de pesquisa que pretende realizar.  

##  <a name="example"></a><a name="bkmk_ex"></a> Exemplo  
 Como notado anteriormente, a pontuação personalizada é implementada através de um ou mais perfis de pontuação definidos num esquema de índice.  

 Este exemplo mostra o esquema de um índice com dois perfis de pontuação `boostGenre` (, . `newAndHighlyRated` Qualquer consulta contra este índice que inclua qualquer perfil como parâmetro de consulta utilizará o perfil para marcar o conjunto de resultados.  

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
 Para implementar o comportamento de pontuação personalizada, adicione um perfil de pontuação ao esquema que define o índice. Pode ter até 100 perfis de pontuação dentro de um índice (ver [Limites de Serviço),](search-limits-quotas-capacity.md)mas só pode especificar um perfil de cada vez em qualquer consulta.  

 Comece com o [Modelo](#bkmk_template) fornecido neste tópico.  

 Forneça um nome. Os perfis de pontuação são opcionais, mas se adicionar um, o nome é necessário. Certifique-se de seguir as convenções de nomeação para campos (começa com uma letra, evita caracteres especiais e palavras reservadas). Consulte [as regras de nomeação &#40;&#41;de pesquisa cognitiva Azure ](/rest/api/searchservice/naming-rules) para a lista completa.  

 O corpo do perfil de pontuação é construído a partir de campos e funções ponderados.  

|||  
|-|-|  
|**Pesos**|Especifique os pares de valor-nome que atribuem um peso relativo a um campo. No [Exemplo](#bkmk_ex), o álbumTitle, género e artistName fields são impulsionados 1,5, 5 e 2 respectivamente. Por que o género é aumentado muito mais alto que os outros? Se a pesquisa for realizada sobre dados que são um pouco homogéneos (como é o caso do 'género' `musicstoreindex` no), poderá necessitar de uma maior variação nos pesos relativos. Por exemplo, no `musicstoreindex` "rock" aparece como um género e em descrições de géneros com frases idênticas. Se quiser que o género sobrepõe a descrição do género, o campo de género precisará de um peso relativo muito maior.|  
|**Funções**|Utilizados quando são necessários cálculos adicionais para contextos específicos. Os valores válidos são `freshness`, `magnitude`, `distance` e `tag`. Cada função tem parâmetros únicos.<br /><br /> -   `freshness` deve ser usado quando quiser aumentar o quão novo ou antigo é um item. Esta função só pode ser utilizada com `datetime` campos (edm. DataTimeOffset). Note que o `boostingDuration` atributo é utilizado apenas com a `freshness` função.<br />-   `magnitude` deve ser usado quando pretender aumentar com base no valor numérico alto ou baixo. Os cenários que exigem esta função incluem o aumento da margem de lucro, o preço mais alto, o preço mais baixo ou a contagem de downloads. Esta função só pode ser utilizada com campos duplos e inteiros.<br />     Para a `magnitude` função, pode inverter a gama, de alto a baixo, se quiser o padrão inverso (por exemplo, para aumentar os itens a preços mais baixos do que os itens mais caros). Dada uma gama de preços de $100 a $1, você definiria `boostingRangeStart` a 100 e `boostingRangeEnd` a 1 para aumentar os itens mais baixos.<br />-   `distance` deve ser usado quando pretender aumentar pela proximidade ou localização geográfica. Esta função só pode ser utilizada com `Edm.GeographyPoint` campos.<br />-   `tag` deve ser usado quando pretender aumentar por etiquetas em comum entre documentos e consultas de pesquisa. Esta função só pode ser utilizada com `Edm.String` campos e `Collection(Edm.String)` campos.<br /><br /> **Regras para a utilização de funções**<br /><br /> O tipo de função `freshness` `magnitude` (, , `distance` , deve ser `tag` mais baixo.<br /><br /> As funções não podem incluir valores nulos ou vazios. Especificamente, se incluir o nome de campo, tem que defini-lo para algo.<br /><br /> As funções só podem ser aplicadas em campos filtrados. Consulte [o Índice de Criação &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/create-index) para obter mais informações sobre campos filtrados.<br /><br /> As funções só podem ser aplicadas em campos definidos na recolha de campos de um índice.|  

 Após a definição do índice, constroem o índice carregando o esquema de índice, seguido de documentos. Consulte [Criar Índice &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/create-index) e [Adicionar, Atualizar ou Eliminar Documentos &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/addupdate-or-delete-documents) de instruções sobre estas operações. Uma vez que o índice é construído, você deve ter um perfil de pontuação funcional que funciona com os seus dados de pesquisa.  

##  <a name="template"></a><a name="bkmk_template"></a> Modelo  
 Esta secção mostra a sintaxe e o modelo para perfis de pontuação. Consulte a [referência de atributos Index](#bkmk_indexref) na secção seguinte para descrições dos atributos.  

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

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a> Referência de atributos de índice  

> [!NOTE]  
>  Uma função de pontuação só pode ser aplicada em campos que são filtrados.  

|Atributo|Descrição|  
|---------------|-----------------|  
|`name`|Obrigatório. Este é o nome do perfil de pontuação. Segue as mesmas convenções de nomeação de um campo. Deve começar com uma letra, não pode conter pontos, cólons ou símbolos @, e não pode começar com a frase 'azureSearch' (sensível a maiúsculas).|  
|`text`|Contém a propriedade dos pesos.|  
|`weights`|Opcional. Contém pares de valor de nome que cada um especifica um nome de campo e peso relativo. O peso relativo deve ser um número inteiro ou de ponto flutuante positivo.<br /><br /> Os pesos são usados para indicar a importância de um campo pesculável em relação a outro.|  
|`functions`|Opcional. Uma função de pontuação só pode ser aplicada em campos que são filtrados.|  
|`type`|Necessário para as funções de pontuação. Indica o tipo de função a utilizar. Valores válidos incluem magnitude, frescura, distância e etiqueta. Pode incluir mais de uma função em cada perfil de pontuação. O nome da função deve ser mais baixo.|  
|`boost`|Necessário para as funções de pontuação. Um número positivo usado como multiplicador para pontuação bruta. Não pode ser igual a 1.|  
|`fieldname`|Necessário para as funções de pontuação. Uma função de pontuação só pode ser aplicada em campos que fazem parte da recolha de campo do índice, e que são filtrados. Além disso, cada tipo de função introduz restrições adicionais (a frescura é utilizada com campos de data, magnitude com campos inteiros ou duplos, e distância com campos de localização). Só é possível especificar um único campo por definição de função. Por exemplo, para usar a magnitude duas vezes no mesmo perfil, você precisaria incluir duas definições de magnitude, uma para cada campo.|  
|`interpolation`|Necessário para as funções de pontuação. Define a inclinação para a qual a pontuação aumenta desde o início da gama até ao fim da gama. Valores válidos incluem Linear (padrão), Constante, Quadrático e Logarítmico. Consulte [as interpolações definidas](#bkmk_interpolation) para obter mais detalhes.|  
|`magnitude`|A função de pontuação de magnitude é usada para alterar classificações com base na gama de valores para um campo numérico. Alguns dos exemplos de utilização mais comuns são:<br /><br /> -   **Classificações de estrelas:** Altere a pontuação com base no valor dentro do campo "Star Rating". Quando dois itens forem relevantes, o item com a classificação mais alta será apresentado primeiro.<br />-   **Margem:** Quando dois documentos são relevantes, um retalhista pode querer aumentar os documentos que têm margens mais elevadas primeiro.<br />-   **Click conta:** Para aplicações que rastreiam clique através de ações para produtos ou páginas, você poderia usar a magnitude para aumentar itens que tendem a obter mais tráfego.<br />-   **As contagens de descarregamento:** Para aplicações que rastreiam downloads, a função de magnitude permite aumentar itens que têm mais downloads.|  
|`magnitude` &#124; `boostingRangeStart`|Define o valor inicial do intervalo sobre o qual a magnitude é pontuada. O valor deve ser um número inteiro ou de ponto flutuante. Para classificações de estrelas de 1 a 4, este seria 1. Para margens superiores a 50%, seriam 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Define o valor final do intervalo sobre o qual a magnitude é pontuada. O valor deve ser um número inteiro ou de ponto flutuante. Para classificações de estrelas de 1 a 4, este seria 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Valores válidos são verdadeiros ou falsos (padrão). Quando definido como verdadeiro, o boost completo continuará a aplicar-se a documentos que têm um valor para o campo alvo que é superior à extremidade superior da gama. Se for falso, o impulso desta função não será aplicado a documentos com um valor para o campo alvo que cai fora do alcance.|  
|`freshness`|A função de pontuação de frescura é usada para alterar as pontuações de classificação para itens baseados em valores nos `DateTimeOffset` campos. Por exemplo, um item com uma data mais recente pode ser classificado mais alto do que itens mais antigos.<br /><br /> Também é possível classificar itens como eventos de calendário com datas futuras de modo a que os itens mais próximos do presente possam ser classificados mais alto do que itens mais adiante no futuro.<br /><br /> Na atual versão de serviço, uma das extremidades da gama será fixada à hora atual. A outra extremidade é uma época no passado baseada `boostingDuration` no. Para aumentar uma série de vezes no futuro, use um negativo `boostingDuration` .<br /><br /> A taxa a que o aumento muda de um intervalo máximo e mínimo é determinada pela Interpolação aplicada ao perfil de pontuação (ver figura abaixo). Para inverter o fator de impulso aplicado, escolha um fator de impulso inferior a 1.|  
|`freshness` &#124; `boostingDuration`|Estabelece um período de validade após o qual o reforço irá parar para um determinado documento. Ver [Conjunto de aumentoDuration](#bkmk_boostdur) na secção seguinte para sintaxe e exemplos.|  
|`distance`|A função de pontuação de distância é utilizada para afetar a pontuação de documentos com base na sua proximidade ou longe em relação a uma localização geográfica de referência. A localização de referência é dada como parte da consulta num parâmetro (usando a `scoringParameterquery` opção de corda) como um argumento lon,lat.|  
|`distance` &#124; `referencePointParameter`|Um parâmetro a ser passado em consultas para usar como local de referência. `scoringParameter` é um parâmetro de consulta. Consulte [documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents) para descrições de parâmetros de consulta.|  
|`distance` &#124; `boostingDistance`|Um número que indica a distância em quilómetros do local de referência onde termina a distância.|  
|`tag`|A função de pontuação da etiqueta é usada para afetar a pontuação de documentos com base em etiquetas em documentos e consultas de pesquisa. Os documentos que tenham etiquetas em comum com a consulta de pesquisa serão aumentados. As etiquetas para a consulta de pesquisa são fornecidas como um parâmetro de pontuação em cada pedido de pesquisa (usando a `scoringParameterquery` opção de cadeia).|  
|`tag` &#124; `tagsParameter`|Um parâmetro a ser passado em consultas para especificar etiquetas para um pedido específico. `scoringParameter` é um parâmetro de consulta. Consulte [documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents) para descrições de parâmetros de consulta.|  
|`functionAggregation`|Opcional. Só se aplica quando as funções são especificadas. Os valores válidos incluem: soma (padrão), média, mínima, máxima e primeira partida. Uma pontuação de pesquisa é um único valor que é calculado a partir de múltiplas variáveis, incluindo múltiplas funções. Este atributo indica como os impulsos de todas as funções são combinados num único impulso agregado que depois é aplicado na pontuação do documento base. A pontuação base baseia-se no valor [tf-idf](http://www.tfidf.com/) calculado a partir do documento e na consulta de pesquisa.|  
|`defaultScoringProfile`|Ao executar um pedido de pesquisa, se não for especificado nenhum perfil de pontuação, então é utilizada a pontuação predefinida (apenas[tf-idf).](http://www.tfidf.com/)<br /><br /> Um nome de perfil de pontuação padrão pode ser definido aqui, fazendo com que a Azure Cognitive Search use esse perfil quando não é dado nenhum perfil específico no pedido de pesquisa.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a> Definir interpolações  
 As interpolações permitem definir a forma da inclinação utilizada para a pontuação. Como a pontuação é alta a baixa, a inclinação está sempre a diminuir, mas a interpolação determina a curva da inclinação descendente. Podem ser utilizadas as seguintes interpolações:  

| Interpolação | Description |  
|-|-|  
|`linear`|Para itens que se encontrem dentro da gama max e min, o impulso aplicado ao item será feito numa quantidade constantemente decrescente. Linear é a interpolação padrão para um perfil de pontuação.|  
|`constant`|Para itens que estão dentro do intervalo de início e fim, será aplicado um impulso constante aos resultados do ranking.|  
|`quadratic`|Em comparação com uma interpolação linear que tem um impulso constantemente diminuindo, o Quadrante irá inicialmente diminuir a um ritmo menor e, em seguida, à medida que se aproxima da gama final, diminui a um intervalo muito mais alto. Esta opção de interpolação não é permitida nas funções de pontuação de etiquetas.|  
|`logarithmic`|Em comparação com uma interpolação linear que tem um impulso constantemente em declínio, logarítmica irá inicialmente diminuir a um ritmo mais elevado e, em seguida, à medida que se aproxima da gama final, diminui a um intervalo muito menor. Esta opção de interpolação não é permitida nas funções de pontuação de etiquetas.|  

 ![Linhas constantes, lineares, quadráticas, log10 no gráfico](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a> Conjunto impulsionandoDuração  
 `boostingDuration` é um atributo da `freshness` função. Utilize-o para definir um período de validade após o qual o reforço irá parar para um determinado documento. Por exemplo, para impulsionar uma linha de produto ou marca por um período promocional de 10 dias, você especificaria o período de 10 dias como "P10D" para esses documentos.  

 `boostingDuration` deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de duração ISO 8601). O padrão para isso é: "P[nD][T[nH][nM][nS]]]".  

 A tabela a seguir apresenta vários exemplos.  

|Duração|impulsionar aduração|  
|--------------|----------------------|  
|1 dia|"P1D"|  
|2 dias e 12 horas|"P2DT12H"|  
|15 minutos|"PT15M"|  
|30 dias, 5 horas, 10 minutos e 6.334 segundos|"P30DT5H10M6.334S"|  

 Para mais exemplos, consulte [XML Schema: Datatypes (W3.org web site)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Ver também  

+ [Referência API DE REPOUSO](/rest/api/searchservice/)
+ [Criar API de Índice](/rest/api/searchservice/create-index)
+ [Pesquisa Cognitiva Azure .NET SDK](/dotnet/api/overview/azure/search?)
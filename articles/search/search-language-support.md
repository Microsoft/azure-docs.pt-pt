---
title: Consultas - Azure Search de pesquisa de vários idiomas de indexação para inglês
description: O Azure Search suporta 56 idiomas, aproveitando os analisadores de idiomas da tecnologia de Lucene e processamento de linguagem Natural da Microsoft.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: a198fa7fe5e1ed81e30987990359f9ecedbe225b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197297"
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Criar um índice para documentos em vários idiomas no Azure Search
> [!div class="op_single_selector"]
>
> * [Portal](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Mostrar o poder de analisadores de idiomas é tão fácil como uma propriedade de definição num campo pesquisável na definição do índice. Agora pode efetuar este passo no portal.

Seguem-se capturas de ecrã dos painéis de Portal do Azure para o Azure Search, que permitem aos utilizadores definir um esquema de índice. A partir deste painel, os utilizadores podem criar todos os campos e definir a propriedade de analisador para cada uma delas.

> [!IMPORTANT]
> Pode apenas definir um analisador de idioma durante a definição de campo, como na quando criar um novo índice desde o backup ou ao adicionar um novo campo para um índice existente. Certifique-se de que especifica completamente todos os atributos, incluindo o analisador, ao criar o campo. Não será possível editar os atributos ou alterar o tipo de analisador depois de guardar as alterações.
>
>

## <a name="define-a-new-field-definition"></a>Definir uma nova definição de campo
1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e abra o painel de serviço do seu serviço de pesquisa.
2. Clique em **Adicionar índice** na barra de comandos na parte superior do dashboard do serviço para iniciar um novo índice ou abrir um índice existente para definir um analisador em novos campos estiver a adicionar a um índice existente.
3. No painel de campos que aparece, dando-lhe opções para definir o esquema do índice, incluindo o separador de analisador utilizado para a escolha de um analisador de idioma.
4. Nos campos, comece uma definição de campo por fornecer um nome, escolhendo o tipo de dados e definir atributos para marcar o campo como texto completo pesquisáveis, recuperáveis nos resultados de pesquisa, utilizáveis em estruturas de navegação de Faceta, ordenável e assim por diante.
5. Antes de passar para o próximo campo, abra a **Analyzer** separador.

![][1]
*Para selecionar um analisador, clique no separador de analisador no painel campos*

## <a name="choose-an-analyzer"></a>Escolha um analisador
1. Desloque-se para localizar o campo que está a definir.
2. Se ainda não tiver marcado o campo como pesquisável, clique na caixa de verificação agora para marcá-lo como **pesquisável**.
3. Clique na área de analisador para apresentar a lista de analisadores disponíveis.
4. Escolha o analisador que pretende utilizar.

![][2]
*Selecione uma dos analisadores suportados para cada campo*

Por predefinição, todos os campos pesquisáveis utilizam o [analisador de Lucene padrão](https://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) que é independente de idioma. Para ver a lista completa de analisadores suportados, consulte [suporte de idiomas no Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Assim que o analisador de idioma é selecionado para um campo, será utilizado com cada pedido de indexação e pesquisa para esse campo. Quando uma consulta é emitida relativamente a vários campos, com analisadores diferentes, a consulta será processada independentemente pelos analisadores certos para cada campo.

Muitas aplicações móveis e web servem os utilizadores em todo o mundo utilizam idiomas diferentes. É possível definir um índice para um cenário assim através da criação de um campo para cada idioma suportado.

![][3]
*Definição de índice com um campo de descrição para cada idioma suportado*

Se o idioma do agente de emissão de uma consulta for conhecido, um pedido de pesquisa pode ser confinado a um campo específico usando a **searchFields** parâmetro de consulta. A consulta seguinte será emitida apenas em relação a descrição na Polaco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2017-11-11`

Pode consultar o índice no portal, usando **Explorador de pesquisa** colar uma consulta semelhante ao mostrado acima. Explorador de pesquisa está disponível na barra de comandos no painel do serviço. Ver [consultar o índice da Azure Search no portal do](search-explorer.md) para obter detalhes.

Por vezes, não é conhecido o idioma do agente de emissão de uma consulta, caso em que a consulta pode de ser emitida em relação a todos os campos em simultâneo. Se for necessário, a preferência de resultados num determinado idioma pode ser definida usando [perfis de classificação](https://msdn.microsoft.com/library/azure/dn798928.aspx). No exemplo abaixo, correspondências encontradas na descrição em inglês irão ser classificadas superior em relação ao correspondências em Polonês e francês:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2017-11-11`

Se for um desenvolvedor de .NET, tenha em atenção que pode configurar os analisadores de idioma a utilizar o [SDK .NET da Azure Search](https://www.nuget.org/packages/Microsoft.Azure.Search). A versão mais recente inclui suporte para os analisadores de idiomas da Microsoft também.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png

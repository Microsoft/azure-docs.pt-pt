---
title: 'Exemplo: Introdução ao - API de serviço de exploração de conhecimento'
titlesuffix: Azure Cognitive Services
description: Utilize a API de Serviço de Exploração de Conhecimento (KES) para criar um motor para uma experiência de pesquisa interativa em publicações académicas.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a48fecd910d87af23a9385270878b0d1b6737007
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452543"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Introdução ao Serviço de Exploração de Conhecimento

Nestas instruções, irá utilizar o Serviço de Exploração de Conhecimento (KES) para criar um motor para uma experiência de pesquisa interativa em publicações académicas. Pode instalar a ferramenta da linha de comandos [`kes.exe`](CommandLine.md) e todos os ficheiros de exemplo do [SDK do Serviço de Exploração de Conhecimento](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

O exemplo de publicações académicas contém uma amostra de 1000 artigos académicos, publicados por investigadores da Microsoft.  A cada documento está associado um título, um ano de publicação, autores e palavras-chave. Cada autor é representado por um ID, nome e afiliação no momento da publicação. A cada palavra-chave pode ser associado um conjunto de sinónimos (por exemplo, a palavra-chave "máquina de vetor do suporte" pode ser associada ao sinónimo "svm").

## <a name="define-the-schema"></a>Definir o esquema

O esquema descreve a estrutura de atributos dos objetos no domínio. Especifica o nome e o tipo de dados para cada atributo num formato de ficheiro JSON. O exemplo seguinte é o conteúdo do ficheiro *Academic.schema*.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Aqui, irá definir *Título*, *Ano*, e *Palavra-chave* como uma cadeia de caracteres, um inteiro e um atributo de cadeia, respetivamente. Uma vez que os autores são representados por ID, name e afiliação, define *autor* como um atributo composto com três atributos secundárias: *Author.Id*, *Author.Name*, e *Author.Affiliation*.

Por predefinição, os atributos suportam todas as operações disponíveis para o seu tipo de dados, incluindo *equals*, *starts_with* e *is_between*. Dado que o ID do autor apenas é utilizado internamente como identificador, substitua a predefinição e especifique *equals* como a única operação indexada.

Para o atributo *Palavra-chave*, permita sinónimos para corresponder a valores canónicos da palavra-chave, ao especificar o ficheiro de sinónimos *Keyword.syn* na definição do atributo. Este ficheiro contém uma lista de pares de valores canónicos e sinónimos:

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

Para obter mais informações sobre a definição de esquema, veja [Formato de Esquema](SchemaFormat.md).

## <a name="generate-data"></a>Gerar dados

O ficheiro de dados descreve a lista de publicações a indexar, com cada linha a especificar os valores dos atributos de um documento no [formato JSON](http://json.org/).  O exemplo seguinte é uma única linha do ficheiro de dados *Academic.data*, formatado para legibilidade:

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

Neste fragmento, especifique o atributo *Título* e *Ano* do documento como uma cadeia JSON e um número, respetivamente. Vários valores são representados com matrizes JSON. Uma vez que o *Autor* é um atributo composto, cada valor é representado através de um objeto JSON que consiste nos seus atributos secundários. Os atributos com valores em falta, tal como a *Palavra-chave* neste caso, podem ser excluídos da representação JSON.

Para diferenciar a probabilidade de diferentes documentos, especifique a probabilidade relativa do logaritmo ao utilizar o atributo incorporado *logprob*. Dada uma probabilidade *p* entre 0 e 1, vai calcular a probabilidade de logaritmo como log (*p*), em que log() é a função de logaritmo natural.

Para obter mais informações, veja [Formato de Dados](DataFormat.md).

## <a name="build-a-compressed-binary-index"></a>Criar um índice binário comprimido

Depois de ter um ficheiro de esquema e um ficheiro de dados, pode criar um índice binário comprimido dos objetos de dados com [`kes.exe build_index`](CommandLine.md#build_index-command). Neste exemplo, vai criar o ficheiro de índice *Academic.index* do ficheiro de esquema de entrada *Academic.schema* e do ficheiro de dados *Academic.data*. Utilize o seguinte comando:

`kes.exe build_index Academic.schema Academic.data Academic.index`

Para criar um protótipo rápido fora do Azure, [`kes.exe build_index`](CommandLine.md#build_index-command) pode criar pequenos índices localmente, a partir dos ficheiros de dados que contêm até 10 000 objetos. Para ficheiros de dados maiores, pode optar por executar o comando a partir de dentro de uma [VM do Windows no Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) ou por executar uma criação remota no Azure. Para obter detalhes, consulte Scaling cópia de segurança.

## <a name="use-an-xml-grammar-specification"></a>Utilizar uma especificação de gramática XML

A gramática especifica o conjunto de consultas de linguagem natural que o serviço pode interpretar, bem como a forma como estas consultas de linguagem natural são traduzidas em expressões de consulta semântica. Neste exemplo, vai utilizar a gramática especificada em *academic.xml*:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

Para obter mais informações sobre a sintaxe de especificação de gramática, veja [Formato de Gramática](GrammarFormat.md).

## <a name="compile-the-grammar"></a>Compilar a gramática

Depois de ter uma especificação de gramática XML, pode compilá-la numa gramática binária com [`kes.exe build_grammar`](CommandLine.md#build_grammar-command). Note que, se a gramática importar um esquema, o ficheiro de esquema terá de estar localizado no mesmo caminho que o XML da gramática. Neste exemplo, vai criar o ficheiro binário de gramática *Academic.grammar* a partir do ficheiro de gramática XML de entrada *Academic.xml*. Utilize o seguinte comando:

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>Alojar a gramática e o índice num serviço Web

Para criar um protótipo rápido, pode alojar a gramática e o índice num serviço Web no computador local com [`kes.exe host_service`](CommandLine.md#host_service-command). É possível aceder ao serviço através de [APIs Web](WebAPI.md) para validar a correção dos dados e a conceção da gramática. Neste exemplo, aloja o ficheiro de gramática *Academic.grammar* e o ficheiro de índice *Academic.index* em http://localhost:8000/. Utilize o seguinte comando:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Esta ação inicia uma instância local do serviço Web. Pode testar interativamente o serviço, ao visitar `http::localhost:<port>` a partir de um browser. Para obter mais informações, veja a serviço de teste.

Também pode chamar diretamente várias [APIs Web](WebAPI.md) para testar a interpretação de linguagem natural, a conclusão de consulta, a avaliação de consultas estruturadas e a computação de histograma. Para parar o serviço, introduza "quit" na linha de comandos `kes.exe host_service` ou prima Ctrl + C. Eis alguns exemplos:

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Fora do Azure, [`kes.exe host_service`](CommandLine.md#host_service-command) está limitado a índices de um máximo de 10 000 objetos. Outros limites incluem uma taxa de API de 10 pedidos por segundo e um total de 1000 pedidos antes do processo de execução concluir. Para ignorar essas restrições, execute o comando a partir de uma [VM do Windows no Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) ou implemente-o num serviço cloud do Azure com o comando [`kes.exe deploy_service`](CommandLine.md#deploy_service-command). Para obter detalhes, consulte a implementação de serviço.

## <a name="scale-up-to-host-larger-indices"></a>Aumentar verticalmente para alojar índices maiores

Quando estiver a executar `kes.exe` fora do Azure, o índice está limitado a 10 000 objetos. Pode criar e alojar índices maiores ao utilizar o Azure. Inscreva-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Em alternativa, se se inscrever para o Visual Studio ou MSDN, poderá [ativar os benefícios de subscritor](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Estes oferecem alguns créditos do Azure por mês.

Para permitir o acesso de `kes.exe` a uma conta do Azure, [transfira o ficheiro Definições de Publicação do Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) a partir do portal do Azure. Se lhe for pedido, inicie sessão na conta do Azure desejada. Guarde o ficheiro como *AzurePublishSettings.xml* no diretório de trabalho donde `kes.exe` é executado.

Existem duas formas de criar e alojar índices grandes. A primeira é preparar os ficheiros de esquema e de dados numa VM do Windows no Azure. Em seguida, execute `kes.exe build_index` para criar o índice localmente na VM, sem quaisquer restrições de tamanho. O índice resultante pode ser hospedado localmente na VM com o `kes.exe host_service` de prototipagem rápida, novamente, sem quaisquer restrições. Para obter passos detalhados, veja o [tutorial de VM do Azure](../../../articles/virtual-machines/windows/quick-create-portal.md).

O segundo método é executar uma compilação do Azure remota, ao utilizar [`kes.exe build_index`](CommandLine.md#build_index-command) com o parâmetro `--remote`. Esta ação especifica um tamanho de VM do Azure. Quando o parâmetro `--remote` for especificado, o comando cria uma VM do Azure temporária desse tamanho. Em seguida, cria o índice na VM, carrega o índice para o armazenamento de blobs de destino e elimina a VM após a conclusão. A subscrição do Azure é cobrada pelo custo da VM enquanto o índice está a ser criado.

Esta capacidade de compilação do Azure remoto permite [`kes.exe build_index`](CommandLine.md#build_index-command) seja executado em qualquer ambiente. Quando estiver a efetuar uma compilação remota, os argumentos de esquema e de dados de entrada podem ser caminhos de ficheiro locais ou URLs de [armazenamento de blobs do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). O argumento de índice de saída tem de ser um URL de armazenamento de blobs. Para criar uma conta de armazenamento do Azure, veja [Acerca de contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md). Para copiar ficheiros de forma eficiente de e para armazenamento de blobs, utilize o utilitário [AzCopy](../../storage/common/storage-use-azcopy.md).

Neste exemplo, pode assumir que o seguinte contentor de armazenamento de blobs já foi criado: http://&lt;*account*&gt;.blob.core.windows.net/&lt;*container*&gt;/. Contém o esquema *Academic.schema*, o ficheiro de sinónimos referenciado *Keywords.syn* e o ficheiro de dados de grande escala *Academic.full.data*. Pode criar o índice completo remotamente com o seguinte comando:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Tenha em atenção que poderá demorar 5 a 10 minutos para Aprovisionar uma VM temporária para criar o índice. Para criar um protótipo rápido, pode:
- Desenvolver com um conjunto de dados mais pequeno localmente em qualquer máquina.
- [Crie uma VM do Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) manualmente, [ligue-a](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) por meio do Ambiente de Trabalho Remoto, instale o [SDK do Serviço de Exploração de Conhecimento](https://www.microsoft.com/en-us/download/details.aspx?id=51488) e execute [`kes.exe`](CommandLine.md) a partir de dentro da VM.

A paginação pode atrasar o processo de criação. Para evitar a paginação, utilize uma VM com três vezes a quantidade de RAM como o tamanho do ficheiro de dados de entrada para a criação do índice. Utilize uma VM com mais 1 GB de RAM que o tamanho do índice para o alojamento. Para obter uma lista de tamanhos de VM disponíveis, veja [Tamanhos de máquinas virtuais](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

## <a name="deploy-the-service"></a>Implementar o serviço

Depois de ter uma gramática e um índice, está pronto para implementar o serviço num serviço cloud do Azure. Para criar um novo serviço cloud do Azure, veja [Como criar e implementar um serviço cloud](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Não especifique um pacote de implementação neste momento.  

Quando tiver criado o serviço cloud, pode utilizar [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) para implementar o serviço. Um serviço cloud do Azure tem dois blocos de implementação: produção e teste. Para um serviço que recebe tráfego de utilizadores em direto, deve implementar inicialmente no bloco de teste. Aguarde que o serviço arranque e se inicialize a si mesmo. Em seguida, pode enviar alguns pedidos para validar a implementação e verificar se ele passa testes básicos.

[Trocar](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) o conteúdo do bloco de teste com o bloco de produção para que o tráfego em direto seja agora direcionado para o serviço recentemente implementado. Pode repetir este processo ao implementar uma versão atualizada do serviço com novos dados. Assim como em todos os outros serviços cloud do Azure, pode utilizar, em alternativa, o portal do Azure para configurar o [dimensionamento automático](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

Neste exemplo, vai implementar o índice *Académico* no bloco de teste de um serviço cloud existente com as VMs de *< vm_size >*. Utilize o seguinte comando:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Para obter uma lista de tamanhos de VM disponíveis, veja [Tamanhos de máquinas virtuais](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

Após implementar o serviço, pode chamar várias [APIs Web](WebAPI.md) para testar a interpretação de linguagem natural, conclusão de consulta, avaliação de consultas estruturadas e computação de histograma.  

## <a name="test-the-service"></a>Testar o serviço

Para depurar um serviço em direto, navegue para a máquina de anfitrião a partir de um browser. Para um serviço local implementado por meio de host_service, visite `http://localhost:<port>/`.  Para um serviço cloud do Azure implementado através de deploy_service, visite `http://<serviceName>.cloudapp.net/`.

Esta página contém uma ligação para informações sobre estatísticas de chamada de API básicas, bem como a gramática e o índice alojados neste serviço. Esta página também contém uma interface de pesquisa interativa que demonstra a utilização das APIs Web. Introduza as consultas na caixa de pesquisa para ver os resultados das chamadas de API [interpret](interpretMethod.md), [evaluate](evaluateMethod.md) e [calchistogram](calchistogramMethod.md). O código-fonte HTML subjacente desta página também serve como um exemplo de como integrar APIs Web numa aplicação, para criar uma experiência de pesquisa sofisticada e interativa.



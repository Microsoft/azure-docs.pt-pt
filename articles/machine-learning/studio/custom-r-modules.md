---
title: Definir módulos R personalizados
titleSuffix: ML Studio (classic) Azure
description: Este tópico descreve como criar e implantar um R Studio personalizado (clássico). Ele explica o que são módulos R personalizados e quais arquivos são usados para defini-los.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: dba1b092d9a12f189ce7b4a207c362cad2bf8858
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619483"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Definir módulos R personalizados para Azure Machine Learning Studio (clássico)

Este tópico descreve como criar e implantar um R Studio personalizado (clássico). Ele explica o que são módulos R personalizados e quais arquivos são usados para defini-los. Ele ilustra como construir os arquivos que definem um módulo e como registrar o módulo para implantação em um espaço de trabalho Machine Learning. Os elementos e atributos usados na definição do módulo personalizado são então descritos mais detalhadamente. Como usar funcionalidades e arquivos auxiliares e várias saídas também é discutido. 



## <a name="what-is-a-custom-r-module"></a>O que é um módulo R personalizado?
Um **módulo personalizado** é um módulo definido pelo usuário que pode ser carregado em seu espaço de trabalho e executado como parte da versão clássica do Azure Machine Learning Studio experimento. Um **módulo R personalizado** é um módulo personalizado que executa uma função r definida pelo usuário. **R** é uma linguagem de programação para computação e gráficos estatísticos amplamente usados por estatísticos e cientistas de dados para implementar algoritmos. Atualmente, o R é o único idioma com suporte nos módulos personalizados, mas o suporte para idiomas adicionais está agendado para versões futuras.

Os módulos personalizados têm o **status de primeira classe** na versão clássica do Azure Machine Learning Studio no sentido de que eles podem ser usados assim como qualquer outro módulo. Eles podem ser executados com outros módulos, incluídos em experimentos publicados ou em visualizações. Você tem controle sobre o algoritmo implementado pelo módulo, as portas de entrada e saída a serem usadas, os parâmetros de modelagem e outros vários comportamentos de tempo de execução. Um experimento que contém módulos personalizados também pode ser publicado no Galeria de IA do Azure para facilitar o compartilhamento.

## <a name="files-in-a-custom-r-module"></a>Arquivos em um módulo R personalizado
Um módulo R personalizado é definido por um arquivo. zip que contém, no mínimo, dois arquivos:

* Um **arquivo de origem** que implementa a função R exposta pelo módulo
* Um **arquivo de definição XML** que descreve a interface do módulo personalizado

Arquivos auxiliares adicionais também podem ser incluídos no arquivo. zip que fornece a funcionalidade que pode ser acessada por meio do módulo personalizado. Essa opção é discutida na parte **argumentos** da seção de referência **elementos no arquivo de definição XML** após o exemplo de início rápido.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Exemplo de início rápido: definir, empacotar e registrar um módulo R personalizado
Este exemplo ilustra como construir os arquivos exigidos por um módulo R personalizado, empacotá-los em um arquivo zip e, em seguida, registrar o módulo em seu espaço de trabalho do Machine Learning. O pacote zip de exemplo e os arquivos de exemplo podem ser baixados do [arquivo de download CustomAddRows. zip](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>O arquivo de origem
Considere o exemplo de um módulo de **adição de linhas personalizado** que modifica a implementação padrão do módulo **adicionar linhas** usado para concatenar linhas (observações) de dois conjuntos de dados (frame Data). O módulo **adicionar linhas** padrão acrescenta as linhas do segundo conjunto de dados de entrada ao final do primeiro conjunto de dados de entrada usando o algoritmo `rbind`. A função de `CustomAddRows` personalizada aceita de forma semelhante dois conjuntos de dados, mas também aceita um parâmetro de permuta booliano como uma entrada adicional. Se o parâmetro de permuta for definido como **false**, ele retornará o mesmo conjunto de dados que a implementação padrão. Mas se o parâmetro de permuta for **true**, a função acrescentará as linhas do primeiro conjunto de dados de entrada ao final do segundo. O arquivo CustomAddRows. R que contém a implementação da função `CustomAddRows` do R exposta pelo módulo **adicionar linhas personalizado** tem o seguinte código R.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>O arquivo de definição XML
Para expor essa `CustomAddRows` função como a versão clássica de um módulo Azure Machine Learning Studio, um arquivo de definição XML deve ser criado para especificar como o módulo **adicionar linhas personalizado** deve parecer e se comportar. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


É fundamental observar que o valor dos atributos de **ID** dos elementos **Input** e **ARG** no arquivo XML deve corresponder aos nomes de parâmetro de função do código R no arquivo CustomAddRows. R exatamente: (*dataSet1*, *dataset2*, e *troque* no exemplo). Da mesma forma, o valor do atributo **EntryPoint** do elemento **Language** deve corresponder ao nome da função no script R exatamente: (*CustomAddRows* no exemplo). 

Por outro lado, o atributo **ID** do elemento **output** não corresponde a nenhuma variável no script R. Quando mais de uma saída é necessária, basta retornar uma lista da função do R com resultados colocados *na mesma ordem* em que os elementos de **saída** são declarados no arquivo XML.

### <a name="package-and-register-the-module"></a>Empacotar e registrar o módulo
Salve esses dois arquivos como *CustomAddRows. R* e *CustomAddRows. xml* e, em seguida, zip os dois arquivos em um arquivo *CustomAddRows. zip* .

Para registrá-los em seu espaço de trabalho do Machine Learning, acesse seu espaço de trabalho na versão clássica do Machine Learning Studio, clique no botão **+ novo** na parte inferior e escolha **módulo-> do pacote zip** para carregar as novas **linhas de adição personalizadas** modulo.

![Carregar zip](./media/custom-r-modules/upload-from-zip-package.png)

O módulo **adicionar linhas personalizado** agora está pronto para ser acessado por seus experimentos Machine Learning.

## <a name="elements-in-the-xml-definition-file"></a>Elementos no arquivo de definição XML
### <a name="module-elements"></a>Elementos do módulo
O elemento **Module** é usado para definir um módulo personalizado no arquivo XML. Vários módulos podem ser definidos em um arquivo XML usando vários elementos de **módulo** . Cada módulo em seu espaço de trabalho deve ter um nome exclusivo. Registre um módulo personalizado com o mesmo nome de um módulo personalizado existente e ele substituirá o módulo existente pelo novo. No entanto, os módulos personalizados podem ser registrados com o mesmo nome de um módulo Azure Machine Learning Studio (clássico) existente. Nesse caso, eles aparecem na categoria **personalizada** da paleta do módulo.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


Dentro do elemento **Module** , você pode especificar dois elementos opcionais adicionais:

* um elemento de **proprietário** que é inserido no módulo  
* um elemento **Description** que contém o texto que é exibido na ajuda rápida do módulo e quando você passa o mouse sobre o módulo na interface do usuário do Machine Learning.

Regras para limites de caracteres nos elementos do módulo:

* O valor do atributo **Name** no elemento **Module** não deve exceder 64 caracteres de comprimento. 
* O conteúdo do elemento **Description** não deve exceder 128 caracteres de comprimento.
* O conteúdo do elemento do **proprietário** não deve exceder 32 caracteres de comprimento.

Os resultados de um módulo podem ser determinísticos ou não determinísticos. * * por padrão, todos os módulos são considerados determinísticos. Ou seja, dado um conjunto inalterável de dados e parâmetros de entrada, o módulo deve retornar os mesmos resultados Rand ou uma hora de função que é executada. Devido a esse comportamento, a versão clássica do Azure Machine Learning Studio executa novamente os módulos marcados como determinísticos se um parâmetro ou os dados de entrada foram alterados. Retornar os resultados armazenados em cache também fornece uma execução muito mais rápida de experimentos.

Há funções que não são determinísticas, como RAND ou uma função que retorna a data ou hora atual. Se o módulo usar uma função não determinística, você poderá especificar que o módulo não é determinístico definindo o atributo **Isdeterminísticase** opcional como **false**. Isso garante que o módulo será executado novamente sempre que o experimento for executado, mesmo que os parâmetros e a entrada do módulo não tenham sido alterados. 

### <a name="language-definition"></a>Definição de linguagem
O elemento **Language** no arquivo de definição XML é usado para especificar a linguagem de módulo personalizada. Atualmente, a única linguagem suportada é R. O valor do atributo **SourceFile** deve ser o nome do arquivo R que contém a função a ser chamada quando o módulo é executado. Esse arquivo deve fazer parte do pacote zip. O valor do atributo **EntryPoint** é o nome da função que está sendo chamada e deve corresponder a uma função válida definida com no arquivo de origem.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portas
As portas de entrada e saída para um módulo personalizado são especificadas nos elementos filho da seção **portas** do arquivo de definição XML. A ordem desses elementos determina o layout experiente (UX) pelos usuários. A primeira **entrada** ou **saída** filho listada no elemento **portas** do arquivo XML se torna a porta de entrada mais à esquerda no Machine Learning UX.
Cada porta de entrada e saída pode ter um elemento filho **Descrição** opcional que especifica o texto mostrado quando você passa o cursor do mouse sobre a porta na interface do usuário do Machine Learning.

**Regras de portas**:

* O número máximo de **portas de entrada e saída** é 8 para cada.

### <a name="input-elements"></a>Elementos de entrada
As portas de entrada permitem que você passe dados para sua função e espaço de trabalho do R. Os **tipos de dados** com suporte para portas de entrada são os seguintes: 

**DataTable:** Esse tipo é passado para a função do R como um data. frame. Na verdade, todos os tipos (por exemplo, arquivos CSV ou arquivos ARFF) com suporte pelo Machine Learning e que são compatíveis com **DataTable** são convertidos em um data. frame automaticamente. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

O atributo de **ID** associado a cada porta de entrada **DataTable** deve ter um valor exclusivo e esse valor deve corresponder ao seu parâmetro nomeado correspondente em sua função de R.
As portas **DataTable** opcionais que não são passadas como entrada em um experimento têm o valor **NULL** passado para a função R e as portas zip opcionais serão ignoradas se a entrada não estiver conectada. O atributo **Isoptionl** é opcional para os tipos **DataTable** e **zip** e é *false* por padrão.

**Zip:** Os módulos personalizados podem aceitar um arquivo zip como entrada. Essa entrada é desempacotada no diretório de trabalho do R de sua função

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Para módulos R personalizados, a ID de uma porta zip não precisa corresponder a nenhum parâmetro da função R. Isso ocorre porque o arquivo zip é extraído automaticamente para o diretório de trabalho do R.

**Regras de entrada:**

* O valor do atributo **ID** do elemento **Input** deve ser um nome de variável R válido.
* O valor do atributo **ID** do elemento **Input** não deve ter mais de 64 caracteres.
* O valor do atributo **Name** do elemento **Input** não deve ter mais de 64 caracteres.
* O conteúdo do elemento **Descrição** não deve ter mais de 128 caracteres
* O valor do atributo **Type** do elemento **Input** deve ser *zip* ou *DataTable*.
* O valor do atributo **IsOptional** do elemento **Input** não é necessário (e é *false* por padrão quando não especificado); Mas se for especificado, ele deverá ser *true* ou *false*.

### <a name="output-elements"></a>Elementos de saída
**Portas de saída padrão:** As portas de saída são mapeadas para os valores de retorno de sua função do R, que podem ser usadas por módulos subsequentes. *DataTable* é o único tipo de porta de saída padrão com suporte no momento. (O suporte para *aprendizes* e *transformações* está disponível.) Uma saída *DataTable* é definida como:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Para saídas em módulos de R personalizados, o valor do atributo **ID** não precisa corresponder a nada no script R, mas deve ser exclusivo. Para uma saída de módulo única, o valor de retorno da função R deve ser um *Data. frame*. Para gerar mais de um objeto de um tipo de dados com suporte, as portas de saída apropriadas precisam ser especificadas no arquivo de definição XML e os objetos precisam ser retornados como uma lista. Os objetos de saída são atribuídos às portas de saída da esquerda para a direita, refletindo a ordem na qual os objetos são colocados na lista retornada.

Por exemplo, se você quiser modificar o módulo **adicionar linhas personalizado** para gerar os dois conjuntos de valores originais, *dataSet1* e *dataset2*, além do novo DataSet Unido, *DataSet*, (em uma ordem, da esquerda para a direita, as: *DataSet*, *dataSet1*, *dataset2*), em seguida, defina as portas de saída no arquivo CustomAddRows. XML da seguinte maneira:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


E retorne a lista de objetos em uma lista na ordem correta em ' CustomAddRows. R ':

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Saída de visualização:** Você também pode especificar uma porta de saída do tipo *Visualização*, que exibe a saída do dispositivo gráfico do R e saída do console. Essa porta não faz parte da saída da função do R e não interfere na ordem dos outros tipos de porta de saída. Para adicionar uma porta de visualização aos módulos personalizados, adicione um elemento de **saída** com um valor de *Visualização* para seu atributo de **tipo** :

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Regras de saída:**

* O valor do atributo de **ID** do elemento de **saída** deve ser um nome de variável R válido.
* O valor do atributo **ID** do elemento de **saída** não deve ter mais de 32 caracteres.
* O valor do atributo **Name** do elemento de **saída** não deve ter mais de 64 caracteres.
* O valor do atributo de **tipo** do elemento de **saída** deve ser *Visualização*.

### <a name="arguments"></a>Argumentos
Dados adicionais podem ser passados para a função R por meio de parâmetros de módulo que são definidos no elemento **arguments** . Esses parâmetros aparecem no painel de propriedades mais à direita da interface do usuário do Machine Learning quando o módulo é selecionado. Os argumentos podem ser qualquer um dos tipos com suporte ou você pode criar uma enumeração personalizada quando necessário. Semelhante aos elementos de **portas** , os elementos de **argumentos** podem ter um elemento de **Descrição** opcional que especifica o texto que aparece quando você passa o mouse sobre o nome do parâmetro.
As propriedades opcionais de um módulo, como defaultValue, minValue e maxValue, podem ser adicionadas a qualquer argumento como atributos a um elemento **Properties** . As propriedades válidas para o elemento **Properties** dependem do tipo de argumento e são descritas com os tipos de argumento com suporte na próxima seção. Os argumentos com a propriedade **IsOptional** definida como **"true"** não exigem que o usuário insira um valor. Se um valor não for fornecido para o argumento, o argumento não será passado para a função de ponto de entrada. Os argumentos da função de ponto de entrada que são opcionais precisam ser manipulados explicitamente pela função, por exemplo, atribuído um valor padrão de NULL na definição da função de ponto de entrada. Um argumento opcional imporá apenas as outras restrições de argumento, ou seja, mín. ou máx., se um valor for fornecido pelo usuário.
Assim como acontece com entradas e saídas, é essencial que cada um dos parâmetros tenha valores de ID exclusivos associados a eles. Em nosso exemplo de início rápido, a ID/o parâmetro associado foi *alternado*.

### <a name="arg-element"></a>Elemento ARG
Um parâmetro de módulo é definido usando o elemento filho **ARG** da seção **arguments** do arquivo de definição XML. Assim como acontece com os elementos filho na seção **portas** , a ordem dos parâmetros na seção **argumentos** define o layout encontrado no UX. Os parâmetros aparecem de cima para baixo na interface do usuário na mesma ordem em que são definidos no arquivo XML. Os tipos com suporte pelo Machine Learning para parâmetros são listados aqui. 

**int** – um parâmetro de tipo inteiro (32 bits).

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Propriedades opcionais*: **min**, **Max**, **Default** e **IsOptional**

**Double** – um parâmetro de tipo Double.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Propriedades opcionais*: **min**, **Max**, **Default** e **IsOptional**

**bool** – um parâmetro booliano que é representado por uma caixa de seleção em UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Propriedades opcionais*: **Default** -false se não estiver definido

**cadeia de caracteres**: uma cadeia de caracteres padrão

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Propriedades opcionais*: **Default** e **IsOptional**

**Columnpickerfor**: um parâmetro de seleção de coluna. Esse tipo é renderizado na UX como um seletor de coluna. O elemento **Property** é usado aqui para especificar a ID da porta da qual as colunas são selecionadas, onde o tipo de porta de destino deve ser *DataTable*. O resultado da seleção de coluna é passado para a função R como uma lista de cadeias de caracteres que contêm os nomes de coluna selecionados. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Propriedades obrigatórias*: **portid** -corresponde à ID de um elemento input com o tipo *DataTable*.
* *Propriedades opcionais*:
  
  * **allowedTypes** -filtra os tipos de coluna dos quais você pode escolher. Os valores válidos incluem: 
    
    * numeric
    * Booleano
    * Categórico
    * String
    * Label
    * Funcionalidade
    * Classificação
    * Todos
  * **padrão** -as seleções padrão válidas para o seletor de coluna incluem: 
    
    * Nenhum
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * Nome da unidade
    * Recurso
    * Subscore
    * Todos

**DropDown**: uma lista enumerada (DropDown) especificada pelo usuário. Os itens suspensos são especificados dentro do elemento **Properties** usando um elemento **Item** . A **ID** de cada **Item** deve ser exclusiva e uma variável R válida. O valor do **nome** de um **Item** serve como o texto que você vê e o valor que é passado para a função de R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Propriedades opcionais*:
  * **padrão** – o valor da propriedade padrão deve corresponder a um valor de ID de um dos elementos de **Item** .

### <a name="auxiliary-files"></a>Arquivos auxiliares
Qualquer arquivo colocado em seu arquivo ZIP do módulo personalizado estará disponível para uso durante o tempo de execução. Todas as estruturas de diretório presentes são preservadas. Isso significa que a origem do arquivo funciona da mesma forma localmente e na versão clássica da execução do Azure Machine Learning Studio. 

> [!NOTE]
> Observe que todos os arquivos são extraídos para o diretório "src" para que todos os caminhos tenham o prefixo "src/".
> 
> 

Por exemplo, digamos que você deseja remover todas as linhas com NAs do conjunto de e também remover todas as linhas duplicadas, antes de inseri-las em CustomAddRows, e já escreveu uma função de R que faz isso em um arquivo RemoveDupNARows. R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Você pode originar o arquivo auxiliar RemoveDupNARows. R na função CustomAddRows:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

Em seguida, carregue um arquivo ZIP contendo ' CustomAddRows. R ', ' CustomAddRows. xml ' e ' RemoveDupNARows. R ' como um módulo R personalizado.

## <a name="execution-environment"></a>Ambiente de execução
O ambiente de execução para o script R usa a mesma versão do R como o módulo **Executar script r** e pode usar os mesmos pacotes padrão. Você também pode adicionar pacotes de R adicionais ao módulo personalizado incluindo-os no pacote zip do módulo personalizado. Basta carregá-los em seu script R como você faria em seu próprio ambiente de R. 

**As limitações do ambiente de execução** incluem:

* Sistema de arquivos não persistente: os arquivos gravados quando o módulo personalizado é executado não são persistidos entre várias execuções do mesmo módulo.
* Sem acesso à rede


---
title: Definir módulos R personalizados
titleSuffix: Azure Machine Learning Studio
description: Este tópico descreve como criar e implementar um módulo R personalizado no Azure Machine Learning Studio. Ele explica o que são módulos R personalizados e os ficheiros que são utilizados para defini-las.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 0dec86eff9b9df70514be6f32f3aad60bfb311ca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120385"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio"></a>Definir módulos R personalizados para o Azure Machine Learning Studio

Este tópico descreve como criar e implementar um módulo R personalizado no Azure Machine Learning Studio. Ele explica o que são módulos R personalizados e os ficheiros que são utilizados para defini-las. Ela ilustra como construir os ficheiros que definem um módulo e como registar o módulo para a implementação numa área de trabalho do Machine Learning. Os elementos e atributos usados na definição do módulo personalizado, em seguida, são descritos mais detalhadamente. Como utilizar a funcionalidade de auxiliar e de ficheiros e de várias saídas também é abordado. 



## <a name="what-is-a-custom-r-module"></a>O que é um módulo R personalizado?
R **módulo personalizado** é um módulo definido pelo utilizador que pode ser carregado para a área de trabalho e executado como parte de uma experimentação do Azure Machine Learning Studio. R **módulo R personalizado** é um módulo personalizado que executa uma função de R definidas pelo utilizador. **R** é uma linguagem de programação para computação estatística e de gráficos, que é amplamente utilizados por estatísticos e cientistas de dados para a implementação de algoritmos. Atualmente, o R é o único idioma suportado em módulos personalizados, mas o suporte para idiomas adicionais está agendado para versões futuras.

Módulos personalizados tenham **status de primeira classe** no Azure Machine Learning Studio no sentido de que pode ser usados como qualquer outro módulo. Eles podem ser executados com outros módulos, incluídos no experimentações publicadas ou em visualizações. Tem controle sobre o algoritmo implementado, o módulo, a entrada e portas de saída a ser utilizada, os parâmetros de modelagem e outros vários comportamentos de tempo de execução. Uma experimentação que contém módulos personalizados também pode ser publicada na Galeria de IA do Azure para a partilha fácil.

## <a name="files-in-a-custom-r-module"></a>Arquivos num módulo R personalizado
Um módulo R personalizado é definido por um ficheiro. zip que contém, no mínimo, dois ficheiros:

* R **arquivo de origem** que implementa a função de R exposta pelo módulo
* Uma **arquivo de definição XML** que descreve a interface do módulo personalizado

Os arquivos auxiliares adicionais também podem ser incluídos no ficheiro. zip que fornece uma funcionalidade que pode ser acessada a partir do módulo personalizado. Esta opção é discutida a **argumentos** faz parte da secção de referência **elementos no arquivo de definição XML** seguindo o exemplo de início rápido.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Exemplo de guia de introdução: definir, empacotar e registar um módulo R personalizado
Este exemplo ilustra como construir os arquivos necessários por um módulo R personalizado, empacotá-las num ficheiro zip e, em seguida, registar o módulo na sua área de trabalho do Machine Learning. Os exemplo de pacote e arquivos zip de exemplo podem ser transferidos a partir [CustomAddRows.zip transferir ficheiro](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>O ficheiro de origem
Considere o exemplo de um **personalizado adicionar linhas** módulo que modifica a implementação padrão da **adicionar linhas** módulo utilizado para concatenar linhas (observações) de dois conjuntos de dados (quadros de dados). A norma **adicionar linhas** módulo acrescenta as linhas do segundo conjunto de dados de entrada para o final do primeiro conjunto de dados de entrada a `rbind` algoritmo. O personalizado `CustomAddRows` função da mesma forma aceita dois conjuntos de dados, mas também aceita um parâmetro de comutação booleano como entrada adicional. Se o parâmetro de comutação é definido como **FALSE**, ele retorna o mesmo conjunto de dados como a implementação padrão. Mas se o parâmetro de comutação é **TRUE**, a função acrescenta linhas do primeiro conjunto de dados de entrada para o fim do segundo conjunto de dados em vez disso. O ficheiro de CustomAddRows.R que contém a implementação do R `CustomAddRows` função expostos pela **personalizado adicionar linhas** módulo tem o seguinte código de R.

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

### <a name="the-xml-definition-file"></a>O ficheiro de definição XML
Para expor esse `CustomAddRows` funcione como um módulo do Azure Machine Learning Studio, um ficheiro de definição de XML tem de ser criada para especificar como o **personalizado adicionar linhas** módulo deve ter um aspeto e comportam-se. 

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


É fundamental ter em atenção que o valor do **id** atributos da **entrada** e **Arg** elementos no arquivo XML tem de coincidir com os nomes dos parâmetros da função do código R no CustomAddRows.R EXATAMENTE de ficheiros: (*dataset1*, *dataset2*, e *troca* no exemplo). Da mesma forma, o valor do **entryPoint** atributo da **linguagem** elemento tem de corresponder ao nome da função no R script EXATAMENTE: (*CustomAddRows* no exemplo). 

Por outro lado, o **id** atributo para o **saída** elemento não corresponde a todas as variáveis no R script. Quando mais do que uma saída for necessária, simplesmente devolver uma lista da função R com resultados colocados *na mesma ordem* como **saídas** elementos são declarados no arquivo XML.

### <a name="package-and-register-the-module"></a>O pacote e registrar o módulo
Guardar como, esses dois arquivos *CustomAddRows.R* e *CustomAddRows.xml* e, em seguida, zip os dois arquivos juntos num *CustomAddRows.zip* ficheiro.

Para registá-los na sua área de trabalho do Machine Learning, aceda à área de trabalho no Machine Learning Studio, clique nas **+ novo** botão na parte inferior e escolha **módulo -> a partir do pacote ZIP** para carregar o novo **Personalizado adicionar linhas** módulo.

![Carregar Zip](./media/custom-r-modules/upload-from-zip-package.png)

O **personalizado adicionar linhas** módulo agora está pronto para ser acedida por suas experimentações do Machine Learning.

## <a name="elements-in-the-xml-definition-file"></a>Elementos no arquivo de definição XML
### <a name="module-elements"></a>Elementos de módulo
O **módulo** elemento é usado para definir um módulo personalizado no arquivo XML. Vários módulos podem ser definidos num arquivo XML usando várias **módulo** elementos. Cada módulo na sua área de trabalho tem de ter um nome exclusivo. Registar um módulo personalizado com o mesmo nome que um módulo personalizado existente e substitui o módulo existente pelo novo. Módulos personalizados no entanto, podem ser registado com o mesmo nome que um módulo do Azure Machine Learning Studio existente. Se, por isso, eles aparecem no **personalizado** categoria da paleta do módulo.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


Dentro de **módulo** elemento, pode especificar dois elementos opcionais adicionais:

* uma **proprietário** elemento que está incorporado no módulo  
* um **Descrição** elemento que contém o texto que é apresentado na Ajuda rápida para o módulo e quando focaliza o módulo na interface do Usuário do Machine Learning.

Regras para limites de carateres nos elementos de módulo:

* O valor do **name** atributo a **módulo** elemento não pode exceder os 64 carateres de comprimento. 
* O conteúdo do **Descrição** elemento não pode exceder 128 carateres de comprimento.
* O conteúdo do **proprietário** elemento não pode exceder 32 carateres de comprimento.

Resultados de um módulo podem ser determinísticos ou nondeterministic.* * por predefinição, todos os módulos são considerados como determinística. Ou seja, devido um conjunto sem alteração de parâmetros de entrada e de dados, o módulo deve devolver a mesma eacRAND de resultados ou um tempo de função que for executada. Dado esse comportamento, Azure Machine Learning Studio volta a executar apenas módulos marcados como determinista, se um parâmetro ou os dados de entrada foi alterado. Retornar os resultados em cache igualmente muito execução mais rápida de experimentações.

Existem funções que são não determinística, como o RAND ou uma função que devolve a data atual ou a hora. Se o seu módulo utiliza uma função não determinística, pode especificar que o módulo é determinística ao definir o opcional **campos isDeterministic** para o atributo **falso**. Desta forma, assegura que o módulo será novamente executado sempre que a experimentação é executada, mesmo que o módulo de entrada e os parâmetros não foram alterados. 

### <a name="language-definition"></a>Definição de linguagem
O **linguagem** elemento no seu ficheiro de definição XML é usado para especificar o idioma de módulo personalizado. Atualmente, a única linguagem suportada é R. O valor do **sourceFile** atributo tem de ser o nome do ficheiro de R que contém a função a ser chamada quando o módulo for executado. Este ficheiro tem de ser parte do pacote zip. O valor do **entryPoint** atributo é o nome da função a ser chamado e tem de coincidir com uma função válida definida com o ficheiro de origem.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portas
As portas de entrada e saídas para um módulo personalizado são especificadas em elementos filho do **portas** secção do ficheiro de definição XML. A ordem destes elementos determina o layout experiente (UX) por utilizadores. O primeiro subordinado **entrada** ou **saída** listados no **portas** elemento do ficheiro XML torna-se a porta de entrada mais à esquerda na experiência de usuário do Machine Learning.
Cada entrada e a porta de saída pode ter um opcional **Descrição** elemento subordinado que especifica o texto mostrado quando focaliza o cursor do rato sobre a porta na interface do Usuário do Machine Learning.

**Portas regras**:

* Número máximo de **portas de entrada e saídas** é 8 para cada um.

### <a name="input-elements"></a>Elementos de entrada
Portas de entrada permitem-lhe transmitir dados a sua função de R e a área de trabalho. O **tipos de dados** que são suportadas para portas de entrada são da seguinte forma: 

**DataTable:** Este tipo é passado para a sua função de R como uma data. frame. Na verdade, todos os tipos (por exemplo, ficheiros CSV ou ficheiros ARFF) que são suportados pelo Machine Learning e que são compatíveis com o **DataTable** são convertidos numa data. frame automaticamente. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

O **id** atributo associado a cada **DataTable** porta de entrada tem de ter um valor exclusivo e este valor tem de corresponder ao seu parâmetro na sua função de R com o nome correspondente.
Opcional **DataTable** portas que não forem transmitidas como entrada numa experimentação de ter o valor **nulo** passado para a função de R e zip opcional portas são ignoradas, se a entrada não estiver ligada. O **é opcional** atributo é opcional para ambos os **DataTable** e **Zip** tipos e é *false* por predefinição.

**Zip:** Módulos personalizados podem aceitar um arquivo zip como entrada. Esta entrada é descompactada no diretório de trabalho de R da sua função

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Para módulos R personalizados, o id para uma porta de Zip não tem de corresponder a todos os parâmetros da função de R. Isto acontece porque o ficheiro zip é extraído automaticamente para o diretório de trabalho de R.

**Regras de entrada:**

* O valor do **id** atributo da **entrada** elemento tem de ser um nome de variável de R válido.
* O valor do **id** atributo da **entrada** elemento não pode ser superior a 64 carateres.
* O valor do **name** atributo da **entrada** elemento não pode ser superior a 64 carateres.
* O conteúdo do **Descrição** elemento não pode ter mais de 128 carateres
* O valor do **tipo** atributo da **entrada** elemento tem de ser *Zip* ou *DataTable*.
* O valor do **é opcional** atributo da **entrada** elemento não é necessário (e é *false* por predefinição, quando não especificado); mas se for especificado, tem de ser *verdadeira* ou *false*.

### <a name="output-elements"></a>Elementos de saída
**Portas de saída padrão:** Portas de saída são mapeadas para os valores de retorno da sua função de R, o que, em seguida, pode ser utilizada por módulos subsequentes. *DataTable* é o tipo de porta de saída padrão apenas suportado atualmente. (O suporte para *aprendizes* e *transforma* aprimoramentos.) R *DataTable* saída é definida como:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Para saídas em módulos R personalizados, o valor do **id** atributo não tem de corresponder a qualquer coisa no R script, mas tem de ser exclusivo. Para uma saída de módulo único, o valor de retorno da função R tem de ser um *data. frame*. Para mais de um objeto de um tipo de dados suportados de saída, as portas de saída adequados tem de ser especificado no ficheiro de definição XML e os objetos tem de ser devolvido como uma lista. Os objetos de saída são atribuídos a portas da esquerda para a direita, refletindo a ordem em que os objetos são colocados na lista devolvida de saída.

Por exemplo, se pretender modificar a **personalizado adicionar linhas** módulo para a saída originais dois conjuntos de dados, *dataset1* e *dataset2*, além do conjunto de dados associado ao novo, *conjunto de dados*, (numa ordem, da esquerda para a direita, como: *conjunto de dados*, *dataset1*, *dataset2*), em seguida, definir as portas de saída no CustomAddRows.xml do ficheiro da seguinte forma:

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


E retornar a lista de objetos numa lista na ordem correta em "CustomAddRows.R":

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Saída de visualização:** Também pode especificar uma porta de saída do tipo *visualização*, que apresenta o resultado a partir da saída de dispositivo e a consola de gráficos do R. Esta porta não é parte da saída da função de R e não interfere com a ordem dos outros tipos de porta de saída. Para adicionar uma porta de visualização para os módulos personalizados, adicione uma **saída** elemento com um valor de *visualização* para seu **tipo** atributo:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Regras de saída:**

* O valor do **id** atributo da **saída** elemento tem de ser um nome de variável de R válido.
* O valor do **id** atributo da **saída** elemento não pode ter mais do que 32 carateres.
* O valor do **name** atributo da **saída** elemento não pode ser superior a 64 carateres.
* O valor do **tipo** atributo da **saída** elemento tem de ser *visualização*.

### <a name="arguments"></a>Argumentos
Dados adicionais podem ser passados para a função de R através de parâmetros de módulo que são definidos no **argumentos** elemento. Esses parâmetros são apresentados no painel de propriedades mais à direita da interface do Usuário do Machine Learning, quando o módulo é selecionado. Os argumentos podem ser qualquer um dos tipos suportados ou pode criar uma enumeração personalizada quando necessário. Semelhante a **portas** elementos, **argumentos** elementos podem ter um opcional **Descrição** elemento que especifica o texto que aparece quando focaliza o mouse sobre o nome do parâmetro.
Propriedades opcionais de um módulo, tal como defaultValue minValue e maxValue podem ser adicionadas a qualquer argumento como atributos para um **propriedades** elemento. Propriedades válidas para o **propriedades** elemento dependem do tipo de argumento e são descritas com os tipos de argumento suportados na secção seguinte. Argumentos com o **é opcional** definida como **"true"** não exigem que o usuário inserir um valor. Se um valor não for fornecido para o argumento, o argumento não é transmitido para a função de ponto de entrada. Argumentos da função de ponto de entrada que são opcionais precisam ser explicitamente manipulados pela função, por exemplo, atribuída um valor predefinido nulo na definição de função de ponto de entrada. Um argumento opcional só irá impor as outras restrições de argumento, ou seja, min ou max, se um valor é fornecido pelo utilizador.
Tal como acontece com entradas e saídas, é fundamental que cada um dos parâmetros têm valores de id exclusivo associados a eles. No nosso exemplo de início rápido ao parâmetro/id associado foi *troca*.

### <a name="arg-element"></a>Elemento de arg
Um parâmetro de módulo é definido usando o **Arg** elemento subordinado do **argumentos** secção do ficheiro de definição XML. Tal como acontece com os elementos filho a **portas** secção, a ordem dos parâmetros na **argumentos** secção define o layout encontrado na experiência do usuário. Os parâmetros são apresentados de cima para baixo na interface de Usuário na mesma ordem em que são definidos no arquivo XML. Os tipos suportados pelo Machine Learning para os parâmetros estão listados aqui. 

**Int** – um parâmetro de tipo (32-bit) de número inteiro.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Propriedades opcionais*: **min**, **máximo**, **predefinição** e **é opcional**

**duplo** – um parâmetro de tipo double.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Propriedades opcionais*: **min**, **máximo**, **predefinição** e **é opcional**

**bool** – um parâmetro booleano, que é representado por uma caixa de verificação na experiência do usuário.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Propriedades opcionais*: **predefinição** -false Se não estiver definido

**cadeia de caracteres**: uma cadeia de caracteres padrão

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Propriedades opcionais*: **predefinição** e **é opcional**

**ColumnPicker**: um parâmetro de seleção de coluna. Este tipo é representado na UX como um Seletor de coluna. O **propriedade** elemento é utilizado aqui para especificar o id da porta partir dos quais colunas são selecionadas, onde o tipo de porta de destino tem de ser *DataTable*. O resultado da seleção da coluna é passado para a função de R como uma lista de cadeias de caracteres que contém os nomes de coluna selecionada. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *As propriedades necessárias*: **portId** -corresponde ao id de um elemento de entrada com o tipo *DataTable*.
* *Propriedades opcionais*:
  
  * **allowedTypes** -filtros de tipos de coluna da qual pode escolher. Valores válidos incluem: 
    
    * Numérico
    * Booleano
    * Categórico
    * Cadeia
    * Etiqueta
    * Funcionalidade
    * Classificação
    * Todos
  * **predefinição** -seleções padrão válido para o Seletor de coluna incluem: 
    
    * Nenhuma
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
    * AllLabel
    * AllFeature
    * AllScore
    * Todos

**Lista pendente**: lista enumerados especificado pelo utilizador (pendente). Os itens de lista pendente são especificados na **propriedades** elemento usando um **Item** elemento. O **id** para cada **Item** tem de ser exclusivo e uma variável de R válida. O valor do **name** de um **Item** funciona como o texto que consulte e o valor que é passado para a função de R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Propriedades opcionais*:
  * **predefinição** -o valor da propriedade padrão tem de corresponder com um valor de identificação de um da **Item** elementos.

### <a name="auxiliary-files"></a>Ficheiros auxiliares
Qualquer ficheiro que é colocado no seu ficheiro ZIP do módulo personalizado vai estar disponível para utilização durante o tempo de execução. Qualquer estruturas de diretório presentes são preservadas. Isso significa que esse arquivo de origem do funciona da mesma localmente e na execução do Azure Machine Learning Studio. 

> [!NOTE]
> Tenha em atenção que todos os ficheiros são extraídos para o diretório de 'src' pelo que devem ter todos os caminhos ' src /' prefixo.
> 
> 

Por exemplo, digamos que queira remover quaisquer linhas do conjunto de dados e também de remover quaisquer linhas duplicadas, antes de gerá-lo em CustomAddRows e que já criou uma função de R que faz isso num arquivo RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Podem obter o ficheiro auxiliar RemoveDupNARows.R na função CustomAddRows:

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

Em seguida, carregue um ficheiro zip que contém 'CustomAddRows.R', 'CustomAddRows.xml' e 'RemoveDupNARows.R' como um módulo R personalizado.

## <a name="execution-environment"></a>Ambiente de execução
O ambiente de execução para o script R utiliza a mesma versão do R como o **executar Script do R** módulo e pode utilizar os mesmos pacotes padrão. Também pode adicionar pacotes de R adicionais ao módulo personalizado ao incluí-los do pacote zip do módulo personalizado. Basta carregá-los no seu script R como faria no seu próprio ambiente de R. 

**Limitações do ambiente de execução** incluem:

* Sistema de ficheiros não persistente: Arquivos escritos quando o módulo personalizado é executado não são mantidos nas várias execuções do módulo do mesmo.
* Sem acesso de rede


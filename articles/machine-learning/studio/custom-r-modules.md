---
title: Definir módulos R personalizados
titleSuffix: ML Studio (classic) - Azure
description: Este tópico descreve como autoria e implementa um Estúdio R personalizado (clássico). Explica quais são os módulos R personalizados e quais os ficheiros utilizados para os definir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 5b8dab14a9416795eccef1f71988a048c8bedb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218172"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Defina módulos R personalizados para o Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Este tópico descreve como autoria e implementa um Estúdio R personalizado (clássico). Explica quais são os módulos R personalizados e quais os ficheiros utilizados para os definir. Ilustra como construir os ficheiros que definem um módulo e como registar o módulo para implantação num espaço de trabalho de Machine Learning. Os elementos e atributos utilizados na definição do módulo personalizado são descritos com mais detalhes. Como utilizar funcionalidades e ficheiros auxiliares e várias saídas também são discutidos. 



## <a name="what-is-a-custom-r-module"></a>O que é um módulo R personalizado?
Um **módulo personalizado** é um módulo definido pelo utilizador que pode ser enviado para o seu espaço de trabalho e executado como parte da experiência Azure Machine Learning Studio (clássico). Um **módulo R personalizado** é um módulo personalizado que executa uma função R definida pelo utilizador. **R** é uma linguagem de programação para computação estatística e gráficos que é amplamente utilizado por estatísticos e cientistas de dados para implementar algoritmos. Atualmente, R é o único idioma suportado em módulos personalizados, mas o suporte para idiomas adicionais está agendado para futuras versões.

Os módulos personalizados têm **estatuto de primeira classe** no Azure Machine Learning Studio (clássico) no sentido em que podem ser usados como qualquer outro módulo. Podem ser executados com outros módulos, incluídos em experiências publicadas ou em visualizações. Tem controlo sobre o algoritmo implementado pelo módulo, as portas de entrada e saída a utilizar, os parâmetros de modelação e outros comportamentos de tempo de execução. Uma experiência que contém módulos personalizados também pode ser publicada na Galeria Azure AI para uma partilha fácil.

## <a name="files-in-a-custom-r-module"></a>Ficheiros num módulo R personalizado
Um módulo R personalizado é definido por um ficheiro .zip que contém, no mínimo, dois ficheiros:

* Um **ficheiro fonte** que implementa a função R exposta pelo módulo
* Um **ficheiro de definição XML** que descreve a interface personalizada do módulo

Ficheiros auxiliares adicionais também podem ser incluídos no ficheiro .zip que fornece funcionalidade que pode ser acedida a partir do módulo personalizado. Esta opção é discutida nos **Argumentos** parte da secção de referência Elementos no ficheiro de **definição XML** seguindo o exemplo de arranque rápido.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Exemplo de arranque rápido: defina, empreitae e registe um módulo R personalizado
Este exemplo ilustra como construir os ficheiros exigidos por um módulo R personalizado, embalá-los num ficheiro zip e, em seguida, registar o módulo no seu espaço de trabalho machine learning. O pacote zip exemplo e os ficheiros de amostra podem ser descarregados a partir do [download CustomAddRows.zip file](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>O ficheiro fonte
Considere o exemplo de um módulo **Custom Add Rows** que modifica a implementação padrão do módulo **Add Rows** usado para concatenar linhas (observações) de dois conjuntos de dados (quadros de dados). O módulo standard **Add Rows** afixa as linhas do segundo conjunto de dados de entrada `rbind` até ao final do primeiro conjunto de dados de entrada utilizando o algoritmo. A função personalizada `CustomAddRows` aceita igualmente dois conjuntos de dados, mas também aceita um parâmetro de troca booleancomo uma entrada adicional. Se o parâmetro de permuta estiver definido para **FALSO,** devolve o mesmo conjunto de dados que a implementação padrão. Mas se o parâmetro de permuta for **TRUE,** a função anexa as linhas do primeiro conjunto de dados de entrada até ao final do segundo conjunto de dados. O ficheiro CustomAddRows.R que contém `CustomAddRows` a implementação da função R exposta pelo módulo **Custom Add Rows** tem o seguinte código R.

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
Para expor `CustomAddRows` esta função como módulo Azure Machine Learning Studio (clássico), deve ser criado um ficheiro de definição XML para especificar como o módulo **Custom Add Rows** deve parecer e comportar-se. 

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


É fundamental notar que o valor dos atributos **id** dos elementos **Input** e **Arg** no ficheiro XML deve corresponder aos nomes de parâmetros de função do código R no ficheiro CustomAddRows.R EXATAMENTE:*(dataset1*, *dataset2*, e *trocar* no exemplo). Da mesma forma, o valor do atributo do ponto de **entrada** do elemento **Idioma** deve corresponder ao nome da função no script R EXATAMENTE: (*CustomAddRows* no exemplo). 

Em contraste, o atributo **id** para o elemento **Saída** não corresponde a quaisquer variáveis no script R. Quando for necessária mais de uma saída, basta devolver uma lista da função R com os resultados colocados *na mesma ordem* que os **elementos de saída** são declarados no ficheiro XML.

### <a name="package-and-register-the-module"></a>Pacote e registe o módulo
Guarde estes dois ficheiros como *CustomAddRows.R* e *CustomAddRows.xml* e, em seguida, feche os dois ficheiros juntos num ficheiro *CustomAddRows.zip.*

Para os registar no seu espaço de trabalho machine learning, vá ao seu espaço de trabalho no Azure Machine Learning Studio (clássico), clique no botão **+NEW** na parte inferior e escolha **O MÓDULO -> DO ZIP PACKAGE** para carregar o novo módulo Custom Add **Rows.**

![Upload Zip](./media/custom-r-modules/upload-from-zip-package.png)

O módulo **Custom Add Rows** está agora pronto para ser acedido pelas suas experiências de Machine Learning.

## <a name="elements-in-the-xml-definition-file"></a>Elementos no ficheiro de definição XML
### <a name="module-elements"></a>Elementos do módulo
O elemento **Módulo** é utilizado para definir um módulo personalizado no ficheiro XML. Vários módulos podem ser definidos num ficheiro XML utilizando vários elementos de **módulo.** Cada módulo no seu espaço de trabalho deve ter um nome único. Registe um módulo personalizado com o mesmo nome que um módulo personalizado existente e substitui o módulo existente pelo novo. Os módulos personalizados podem, no entanto, ser registados com o mesmo nome que um módulo de Machine Learning Studio (clássico) existente. Em caso afirmativo, aparecem na categoria **Personalizada** da paleta de módulos.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


Dentro do elemento **Módulo,** pode especificar dois elementos opcionais adicionais:

* um elemento **proprietário** que está incorporado no módulo  
* um elemento **descrição** que contém texto que é exibido em ajuda rápida para o módulo e quando paira sobre o módulo na UI de Aprendizagem automática.

Regras para os limites dos caracteres nos elementos do Módulo:

* O valor do **atributo** de nome no elemento **Módulo** não deve exceder 64 caracteres de comprimento. 
* O conteúdo do elemento **Descrição** não deve exceder 128 caracteres de comprimento.
* O conteúdo do elemento **Proprietário** não deve exceder 32 caracteres de comprimento.

Os resultados de um módulo podem ser determinísticos ou não determinísticos.** Por padrão, todos os módulos são considerados determinísticos. Isto é, dado um conjunto imutável de parâmetros e dados de entrada, o módulo deve devolver os mesmos resultados eacRAND ou um tempo de função que é executado. Dado este comportamento, o Azure Machine Learning Studio (clássico) só repete módulos marcados como determinísticos se um parâmetro ou os dados de entrada mudaram. A devolução dos resultados em cache também fornece uma execução muito mais rápida de experiências.

Existem funções que não são determinísticas, como rand ou uma função que devolve a data ou hora atual. Se o seu módulo utilizar uma função não determinística, pode especificar que o módulo não é determinista, definindo o atributo **isDeterminístico** opcional a **FALSO**. Isto assegura que o módulo é reexecutado sempre que a experiência é executada, mesmo que a entrada e os parâmetros do módulo não tenham mudado. 

### <a name="language-definition"></a>Definição de Linguagem
O elemento **Idioma** no seu ficheiro de definição XML é utilizado para especificar o idioma do módulo personalizado. Atualmente, a única linguagem suportada é R. O valor do atributo **sourceFile** deve ser o nome do ficheiro R que contém a função de chamada quando o módulo é executado. Este ficheiro deve fazer parte do pacote zip. O valor do atributo do ponto de **entrada** é o nome da função que está a ser chamada e deve corresponder a uma função válida definida no ficheiro fonte.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portas
As portas de entrada e saída para um módulo personalizado são especificadas em elementos infantis da secção **Portas** do ficheiro de definição XML. A ordem destes elementos determina o layout experimentado (UX) pelos utilizadores. A primeira **entrada** ou **saída** de crianças listada no elemento **Portas** do ficheiro XML torna-se a porta de entrada mais à esquerda no UX de Aprendizagem automática.
Cada entrada e porta de saída pode ter um elemento opcional **Descrição** da criança que especifica o texto mostrado quando paira sobre o cursor do rato sobre a porta da UI de Aprendizagem automática.

**Regras dos Portos:**

* O número máximo de portas de **entrada e saída** é de 8 para cada.

### <a name="input-elements"></a>Elementos de entrada
As portas de entrada permitem-lhe passar dados para a sua função R e espaço de trabalho. Os tipos de **dados** suportados para as portas de entrada são os seguintes: 

**DataTable:** Este tipo é passado para a sua função R como um data.frame. De facto, quaisquer tipos (por exemplo, ficheiros CSV ou ficheiros ARFF) que sejam suportados por Machine Learning e que sejam compatíveis com **dataTable** são convertidos automaticamente para um data.frame. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

O atributo **id** associado a cada porta de entrada **DataTable** deve ter um valor único e este valor deve corresponder ao seu parâmetro nomeado correspondente na sua função R.
As portas Opcionais **DataTable** que não são passadas como entrada numa experiência têm o valor **NULL** passado para a função R e as portas zip opcionais são ignoradas se a entrada não estiver ligada. O atributo **isOptional** é opcional tanto para os tipos **DataTable** como **Zip** e é *falso* por padrão.

**Zip:** Os módulos personalizados podem aceitar um ficheiro zip como entrada. Esta entrada é desembalada no diretório de trabalho R da sua função

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Para módulos R personalizados, o ID para uma porta Zip não tem de corresponder a quaisquer parâmetros da função R. Isto porque o ficheiro zip é automaticamente extraído para o diretório de trabalho R.

**Regras de entrada:**

* O valor do atributo **id** do elemento **Input** deve ser um nome variável R válido.
* O valor do atributo **id** do elemento **Input** não deve ser superior a 64 caracteres.
* O valor do **atributo** de nome do elemento **Deentrada** não deve ser superior a 64 caracteres.
* O conteúdo do elemento **Descrição** não deve ser superior a 128 caracteres
* O valor do **atributo do tipo** do elemento **de entrada** deve ser *Zip* ou *DataTable*.
* O valor do **isOptional** atributo do elemento **Deentrada** não é necessário (e é *falso* por defeito quando não especificado); mas se for especificado, deve ser *verdadeiro* ou *falso.*

### <a name="output-elements"></a>Elementos de saída
Portas de **saída padrão:** As portas de saída são mapeadas para os valores de devolução da sua função R, que podem ser usadas por módulos subsequentes. *DataTable* é o único tipo de porta de saída padrão suportado atualmente. (O apoio aos *alunos* e *transformações* está a chegar.) Uma saída *DataTable* é definida como:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Para saídas em módulos R personalizados, o valor do atributo **id** não tem de corresponder a nada no script R, mas deve ser único. Para uma única saída de módulo, o valor de devolução da função R deve ser um *data.frame*. Para obter mais do que um objeto de um tipo de dados suportado, as portas de saída adequadas devem ser especificadas no ficheiro de definição XML e os objetos precisam de ser devolvidos como uma lista. Os objetos de saída são atribuídos às portas de saída da esquerda para a direita, refletindo a ordem em que os objetos são colocados na lista devolvida.

Por exemplo, se pretender modificar o módulo **Custom Add Rows** para a saída dos dois conjuntos de dados originais, *dataset1* e *dataset2,* para além do novo conjunto de dados, conjunto de *dados,*(numa ordem, da esquerda para a direita, como: *dataset,* *dataset1,* *dataset2*), então defina as portas de saída no ficheiro CustomAddRows.xml da seguinte forma:

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


E devolva a lista de objetos numa lista na ordem correta em 'CustomAddRows.R':

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Saída de visualização:** Também pode especificar uma porta de saída do tipo *Visualização,* que exibe a saída do dispositivo gráfico R e a saída da consola. Esta porta não faz parte da saída da função R e não interfere com a ordem dos outros tipos da porta de saída. Para adicionar uma porta de visualização aos módulos personalizados, adicione um elemento **Saída** com um valor de *Visualização* para o seu **atributo tipo:**

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Regras de saída:**

* O valor do atributo **id** do elemento **Saída** deve ser um nome variável R válido.
* O valor do atributo **id** do elemento **Saída** não deve ser superior a 32 caracteres.
* O valor do **atributo** de nome do elemento **Saída** não deve ser superior a 64 caracteres.
* O valor do **atributo tipo** do elemento **Saída** deve ser *visualização*.

### <a name="arguments"></a>Argumentos
Os dados adicionais podem ser transmitidos para a função R através de parâmetros de módulo que são definidos no elemento **Argumentos.** Estes parâmetros aparecem no painel de propriedades mais à direita da UI de Aprendizagem automática quando o módulo é selecionado. Os argumentos podem ser qualquer um dos tipos suportados ou pode criar uma enumeração personalizada quando necessário. Semelhante aos **elementos ports,** os **elementos argumentos** podem ter um elemento de **descrição** opcional que especifica o texto que aparece quando paira sobre o nome do parâmetro.
As propriedades opcionais para um módulo, tais como defaultValue, minValue e maxValue podem ser adicionadas a qualquer argumento como atributos a um elemento **Properties.** As propriedades válidas para o elemento **Propriedades** dependem do tipo de argumento e são descritas com os tipos de argumentos suportados na secção seguinte. Os argumentos com a **propriedade isOptional** definida para **"verdadeiro"** não exigem que o utilizador introduza um valor. Se não for concedido um valor ao argumento, o argumento não é passado para a função do ponto de entrada. Os argumentos da função do ponto de entrada que são opcionais precisam de ser explicitamente tratados pela função, por exemplo, atribuído um valor predefinido de NULO na definição de função do ponto de entrada. Um argumento opcional só irá impor as outras restrições de argumento, ou seja, min ou máx, se o utilizador fornecer um valor.
Tal como acontece com as inputs e saídas, é fundamental que cada um dos parâmetros tenha valores de ID únicos associados a eles. No nosso exemplo de arranque rápido, o id/parâmetro associado foi *trocado*.

### <a name="arg-element"></a>Elemento arg
Um parâmetro de módulo é definido utilizando o elemento da criança **Arg** da secção **Argumentos** do ficheiro de definição XML. Tal como acontece com os elementos infantis da secção **Portos,** a ordem dos **parâmetros** na secção Argumentos define o layout encontrado no UX. Os parâmetros aparecem de cima para baixo na UI na mesma ordem em que são definidos no ficheiro XML. Os tipos suportados pela Machine Learning para parâmetros estão listados aqui. 

**int** – um parâmetro do tipo Integer (32-bits).

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Propriedades Opcionais:* **min,** **máx,** **predefinição** e **isOptional**

**duplo** – um parâmetro de tipo duplo.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Propriedades Opcionais:* **min,** **máx,** **predefinição** e **isOptional**

**bool** – um parâmetro Boolean que é representado por uma caixa de verificação em UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Propriedades Opcionais*: **padrão** - falso se não definido

**corda**: uma corda padrão

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Propriedades Opcionais*: **padrão** e **éOpcional**

**ColumnPicker**: um parâmetro de seleção de colunas. Este tipo torna-se no UX como um escolhidor de coluna. O elemento **Propriedade** é utilizado aqui para especificar o ID da porta a partir da qual as colunas são selecionadas, onde o tipo de porta-alvo deve ser *DataTable*. O resultado da seleção da coluna é passado para a função R como uma lista de cordas que contêm os nomes de colunas selecionados. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Propriedades Requeridas*: **portId** - corresponde ao ID de um elemento de entrada com o tipo *DataTable*.
* *Propriedades Opcionais:*
  
  * **allowedTypes** - Filtra os tipos de colunas a partir dos quais pode escolher. Os valores válidos incluem: 
    
    * Numérico
    * Booleano
    * Categórico
    * Cadeia
    * Etiqueta
    * Funcionalidade
    * Classificação
    * Todos
  * **predefinição** - As seleções de predefinição válidas para o apanhador de colunas incluem: 
    
    * Nenhuma
    * Recurso numérico
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * Rótulo booleano
    * BooleanScore
    * BooleanAll
    * Recurso Categórico
    * Rótulo Categórico
    * Pontuação Categórica
    * Categoricato
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Todos

**DropDown**: uma lista enumerada pelo utilizador (dropdown). Os itens de dropdown são especificados dentro do elemento **Propriedades** utilizando um elemento **Item.** O **id** para cada **item** deve ser único e uma variável R válida. O valor do **nome** de um **Item** serve tanto como o texto que vê e o valor que é passado para a função R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Propriedades Opcionais:*
  * **predefinição** - O valor da propriedade por defeito deve corresponder a um valor de ID de um dos elementos **item.**

### <a name="auxiliary-files"></a>Arquivos Auxiliares
Qualquer ficheiro colocado no seu ficheiro ZIP do módulo personalizado estará disponível para utilização durante o tempo de execução. Quaisquer estruturas de directóriopresentes presentes são preservadas. Isto significa que o fornecimento de ficheiros funciona da mesma forma local e na execução do Azure Machine Learning Studio (clássico). 

> [!NOTE]
> Note que todos os ficheiros são extraídos para o diretório 'src', pelo que todos os caminhos devem ter prefixo 'src/'.
> 
> 

Por exemplo, diga que pretende remover quaisquer linhas com NAs do conjunto de dados, e também remover quaisquer linhas duplicadas, antes de as colocar em CustomAddRows, e já escreveu uma função R que faz isso num ficheiro RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Pode obter o ficheiro auxiliar RemoveDupNARows.R na função CustomAddRows:

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

Em seguida, faça upload de um ficheiro zip contendo 'CustomAddRows.R', 'CustomAddRows.xml', e 'RemoveDupNARows.R' como um módulo R personalizado.

## <a name="execution-environment"></a>Ambiente de Execução
O ambiente de execução do script R utiliza a mesma versão de R que o módulo **Execute R Script** e pode utilizar os mesmos pacotes predefinidos. Também pode adicionar pacotes R adicionais ao seu módulo personalizado, incluindo-os no pacote zip do módulo personalizado. Basta carregá-los no seu script R como faria no seu próprio ambiente R. 

**As limitações do ambiente de execução** incluem:

* Sistema de ficheiros não persistente: Os ficheiros escritos quando o módulo personalizado é executado não são persistidos em várias correções do mesmo módulo.
* Sem acesso à rede


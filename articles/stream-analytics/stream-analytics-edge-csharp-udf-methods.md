---
title: Desenvolver funções .NET Standard para trabalhos do Azure Stream Analytics Edge
description: Saiba como desenvolver funções definidas pelo usuário do .NET para trabalhos de Azure Stream Analytics usando codebehind, um pacote existente ou um projeto local.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: cdfe4d2ccc39385b4020f60905b12c4683b1758c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935028"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>Desenvolver .NET Standard funções definidas pelo usuário para trabalhos do Azure Stream Analytics Edge (versão prévia)

O Azure Stream Analytics oferece uma linguagem de consulta do tipo SQL para executar transformações e computações em fluxos de dados de evento. Há muitas funções internas, mas alguns cenários complexos exigem flexibilidade adicional. Com .net Standard UDF (funções definidas pelo usuário), você pode invocar suas próprias funções escritas em qualquer linguagem .NET padrãoC#( F#, etc.) para estender a linguagem de consulta de Stream Analytics. As UDFs permitem executar cálculos matemáticos complexos, importar modelos de ML personalizados usando ML.NET e usar lógica imputação personalizada para dados ausentes. A funcionalidade UDF das tarefas do Stream Analytics Edge está atualmente em pré-visualização e não deve ser utilizada em cargas de trabalho de produção.

## <a name="overview"></a>Visão geral
As ferramentas do Visual Studio para Azure Stream Analytics facilitam a gravação de UDFs, o teste de seus trabalhos localmente (até mesmo offline) e a publicação do trabalho de Stream Analytics no Azure. Depois de publicado no Azure, você pode implantar seu trabalho em dispositivos IoT usando o Hub IoT.

Há três maneiras de implementar UDFs:

* Arquivos code-behind em um projeto ASA
* UDF de um projeto local
* Um pacote existente de uma conta de armazenamento do Azure

## <a name="package-path"></a>Caminho do pacote

O formato de qualquer pacote UDF tem o caminho `/UserCustomCode/CLR/*`. As DLLs (bibliotecas de vínculo dinâmico) e os recursos são copiados na pasta `/UserCustomCode/CLR/*`, o que ajuda a isolar as DLLs de usuário do sistema e Azure Stream Analytics DLLs. Esse caminho de pacote é usado para todas as funções, independentemente do método usado para empregue-las.

## <a name="supported-types-and-mapping"></a>Tipos e mapeamento com suporte

|**Tipo de UDFC#()**  |**Tipo de Azure Stream Analytics**  |
|---------|---------|
|Longas  |  bigint   |
|double  |  double   |
|string  |  nvarchar (max)   |
|Horário  |  Horário   |
|struct  |  IRecord   |
|objeto  |  IRecord   |
|Objeto de\<de matriz >  |  IArray   |
|dicionário < cadeia de caracteres, objeto >  |  IRecord   |

## <a name="codebehind"></a>Behind
Você pode gravar funções definidas pelo usuário no codebehind **script. asql** . As ferramentas do Visual Studio compilarão automaticamente o arquivo code-behind em um arquivo de assembly. Os assemblies são empacotados como um arquivo zip e carregados em sua conta de armazenamento quando você envia seu trabalho para o Azure. Você pode aprender a escrever um C# UDF usando o codebehind seguindo o [ C# tutorial UDF para trabalhos do Stream Analytics Edge](stream-analytics-edge-csharp-udf.md) . 

## <a name="local-project"></a>Projeto local
As funções definidas pelo usuário podem ser gravadas em um assembly que é referenciado posteriormente em uma consulta Azure Stream Analytics. Essa é a opção recomendada para funções complexas que exigem todo o poder de uma linguagem de .NET Standard além de sua linguagem de expressão, como lógica de procedimento ou recursão. As UDFs de um projeto local também podem ser usadas quando você precisa compartilhar a lógica de função em várias consultas de Azure Stream Analytics. A adição de UDFs ao seu projeto local oferece a capacidade de depurar e testar suas funções localmente no Visual Studio.

Para fazer referência a um projeto local:

1. Crie uma nova biblioteca de classes em sua solução.
2. Escreva o código em sua classe. Lembre-se de que as classes devem ser definidas como *públicas* e que os objetos devem ser definidos como *públicos estáticos*. 
3. Compile seu projeto. As ferramentas irão empacotar todos os artefatos na pasta bin para um arquivo zip e carregar o arquivo zip para a conta de armazenamento. Para referências externas, use a referência de assembly em vez do pacote NuGet.
4. Referencie a nova classe em seu projeto Azure Stream Analytics.
5. Adicione uma nova função em seu projeto Azure Stream Analytics.
6. Configure o caminho do assembly no arquivo de configuração do trabalho, `JobConfig.json`. Defina o caminho do assembly como **referência de projeto local ou codebehind**.
7. Recrie o projeto de função e o projeto Azure Stream Analytics.  

### <a name="example"></a>Exemplo

Neste exemplo, **UDFTest** é um C# projeto de biblioteca de classes e **ASAEdgeUDFDemo** é o projeto Azure Stream Analytics Edge, que fará referência a **UDFTest**.

![Azure Stream Analytics IoT Edge projeto no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Compile seu C# projeto, o que permitirá que você adicione uma referência ao C# seu UDF por meio da consulta Azure Stream Analytics.
    
   ![Criar um projeto de IoT Edge de Azure Stream Analytics no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Adicione a referência ao C# projeto no projeto asa Edge. Clique com o botão direito do mouse no nó referências e escolha Adicionar referência.

   ![Adicionar uma referência a um C# projeto no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Escolha o C# nome do projeto na lista. 
    
   ![Escolha o C# nome do projeto na lista de referências](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Você deve ver o **UDFTest** listado em **referências** em **Gerenciador de soluções**.

   ![Exibir a referência de função definida pelo usuário no Gerenciador de soluções](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Clique com o botão direito do mouse na pasta **funções** e escolha **novo item**.

   ![Adicionar novo item a funções na solução Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Adicione uma C# função **SquareFunction. JSON** ao seu projeto Azure Stream Analytics.

   ![Selecionar a função CSharp de Stream Analytics itens de borda no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Clique duas vezes na função em **Gerenciador de soluções** para abrir a caixa de diálogo de configuração.

   ![Configuração da função Sharp C no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Na configuração C# da função, escolha **carregar na referência do projeto asa** e os nomes do assembly, da classe e do método relacionados na lista suspensa. Para se referir aos métodos, tipos e funções na consulta Stream Analytics Edge, as classes devem ser definidas como *públicas* e os objetos devem ser definidos como *públicos estáticos*.

   ![Configuração da função Sharp Stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Pacotes existentes

Você pode criar .NET Standard UDFs em qualquer IDE de sua escolha e chamá-los de sua consulta de Azure Stream Analytics. Primeiro, compile seu código e empacote todas as DLLs. O formato do pacote tem o caminho `/UserCustomCode/CLR/*`. Em seguida, carregue `UserCustomCode.zip` na raiz do contêiner em sua conta de armazenamento do Azure.

Depois que os pacotes zip do assembly forem carregados em sua conta de armazenamento do Azure, você poderá usar as funções em Azure Stream Analytics consultas. Tudo o que você precisa fazer é incluir as informações de armazenamento na configuração do trabalho do Stream Analytics Edge. Não é possível testar a função localmente com essa opção, pois as ferramentas do Visual Studio não baixarão seu pacote. O caminho do pacote é analisado diretamente para o serviço. 

Para configurar o caminho do assembly no arquivo de configuração do trabalho, `JobConfig.json`:

Expanda a secção **Configuração do Código Definido Pelo Utilizador** e preencha a configuração com os seguintes valores sugeridos:

 |**Definição**  |**Valor sugerido**  |
 |---------|---------|
 |Origem da Assemblagem  | Pacotes de assembly existentes da nuvem    |
 |Recurso  |  Escolher os dados na conta atual   |
 |Subscrição  |  Escolha a sua subscrição.   |
 |Conta de Armazenamento  |  Escolha a sua conta de armazenamento.   |
 |Contentor  |  Escolha o contentor que criou na conta de armazenamento.   |

![Configuração da tarefa do Azure Stream Analytics Edge no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>Limitações
A visualização de UDF atualmente tem as seguintes limitações:

* .NET Standard idiomas só podem ser usados para Azure Stream Analytics no IoT Edge. Para trabalhos de nuvem, você pode gravar funções definidas pelo usuário do JavaScript. Para saber mais, visite o tutorial [Azure Stream Analytics UDF do JavaScript](stream-analytics-javascript-user-defined-functions.md) .

* .NET Standard UDFs só podem ser criadas no Visual Studio e publicadas no Azure. As versões somente leitura do .NET Standard UDFs podem ser exibidas em **funções** no portal do Azure. A criação de funções de .NET Standard não tem suporte no portal do Azure.

* O editor de consultas portal do Azure mostra um erro ao usar o .NET Standard UDF no Portal. 

* Como o código personalizado compartilha o contexto com Azure Stream Analytics mecanismo, o código personalizado não pode fazer referência a nada que tenha um namespace/dll_name conflitante com Azure Stream Analytics código. Por exemplo, você não pode fazer referência a *JSON Newtonsoft*.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: gravar uma C# função definida pelo usuário para um trabalho do Azure Stream Analytics Edge (versão prévia)](stream-analytics-edge-csharp-udf.md)
* [Tutorial: Azure Stream Analytics funções definidas pelo usuário do JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Usar o Visual Studio para exibir Azure Stream Analytics trabalhos](stream-analytics-vs-tools.md)

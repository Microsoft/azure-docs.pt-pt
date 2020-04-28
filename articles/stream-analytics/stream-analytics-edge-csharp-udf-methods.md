---
title: Desenvolver funções padrão .NET para trabalhos de Análise de Fluxo de Azure (Pré-visualização)
description: Aprenda a escrever funções c# definidas pelo utilizador para trabalhos stream analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426320"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Desenvolver funções definidas pelo utilizador .NET Standard para trabalhos de Análise de Fluxo de Azure (Pré-visualização)

O Azure Stream Analytics oferece uma linguagem de consulta semelhante a SQL para realizar transformações e cálculos sobre fluxos de dados de eventos. Existem muitas funções incorporadas, mas alguns cenários complexos requerem flexibilidade adicional. Com funções definidas pelo utilizador (UDF), pode invocar as suas próprias funções escritas em qualquer idioma padrão .NET (C#, F#, etc.) para estender a linguagem de consulta stream analytics. Os UDFs permitem-lhe realizar computações matemáticas complexas, importar modelos ML personalizados usando ML.NET e usar lógica de imputação personalizada para dados em falta. A funcionalidade UDF para trabalhos stream analytics está atualmente em pré-visualização e não deve ser usada em cargas de trabalho de produção.

.NET função definida pelo utilizador para trabalhos na nuvem está disponível em:
* E.U.A. Centro-Oeste
* Europa do Norte
* E.U.A. Leste
* E.U.A. Oeste
* E.U.A. Leste 2
* Europa ocidental

Se estiver interessado em utilizar esta funcionalidade em qualquer outra região, pode [solicitar acesso.](https://aka.ms/ccodereqregion)

## <a name="overview"></a>Descrição geral
As ferramentas do Visual Studio para o Azure Stream Analytics facilitam-lhe a escrita de UDFs, testa os seus trabalhos localmente (mesmo offline) e publique o seu trabalho de Stream Analytics para o Azure. Uma vez publicado no Azure, pode implementar o seu trabalho em dispositivos IoT utilizando o IoT Hub.

Há três formas de implementar UDFs:

* CodeBehind ficheiros num projeto ASA
* UDF de um projeto local
* Um pacote existente a partir de uma conta de armazenamento Azure

## <a name="package-path"></a>Caminho de pacote

O formato de qualquer pacote `/UserCustomCode/CLR/*`UDF tem o caminho. Bibliotecas de ligação dinâmica (DLLs) `/UserCustomCode/CLR/*` e recursos são copiados sob a pasta, o que ajuda a isolar os DLLs do sistema e dLLs azure Stream Analytics. Este caminho de embalagem é utilizado para todas as funções, independentemente do método utilizado para as utilizar.

## <a name="supported-types-and-mapping"></a>Tipos e mapeamento suportados

|**Tipo UDF (C#)**  |**Tipo de Analytics Azure Stream**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|string  |  nvarchar (max)   |
|dataTempo  |  dataTempo   |
|estruturar  |  IRecord   |
|objeto  |  IRecord   |
|>\<de objeto de matriz  |  IArray   |
|<corda,> de objetos  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
Pode escrever funções definidas pelo utilizador no **Script.asql** CodeBehind. As ferramentas do Estúdio Visual compilam automaticamente o ficheiro CodeBehind num ficheiro de montagem. Os conjuntos são embalados como um ficheiro zip e enviados para a sua conta de armazenamento quando submete o seu trabalho ao Azure. Você pode aprender a escrever um C# UDF usando CodeBehind seguindo o [C# UDF para stream analytics edge jobs](stream-analytics-edge-csharp-udf.md) tutorial. 

## <a name="local-project"></a>Projeto local
As funções definidas pelo utilizador podem ser escritas num conjunto que é posteriormente referenciado numa consulta do Azure Stream Analytics. Esta é a opção recomendada para funções complexas que requerem o poder total de uma linguagem padrão .NET para além da sua linguagem de expressão, como lógica processual ou recursão. Os UDFs de um projeto local também podem ser usados quando você precisa partilhar a lógica de função em várias consultas Azure Stream Analytics. Adicionar UDFs ao seu projeto local dá-lhe a capacidade de depurar e testar as suas funções localmente a partir do Visual Studio.

Para fazer referência a um projeto local:

1. Crie uma nova biblioteca de classes na sua solução.
2. Escreva o código na sua turma. Lembre-se que as aulas devem ser *definidas* como públicas e os objetos devem ser definidos como *públicoestático.* 
3. Crie o projeto. As ferramentas irão embalar todos os artefactos da pasta do lixo para um ficheiro zip e enviar o ficheiro zip para a conta de armazenamento. Para referências externas, utilize referência de montagem em vez do pacote NuGet.
4. Consulte a nova classe no seu projeto Azure Stream Analytics.
5. Adicione uma nova função no seu projeto Azure Stream Analytics.
6. Configure o caminho de montagem `JobConfig.json`no ficheiro de configuração do trabalho, . Desloque o caminho de montagem para referência de **projeto local ou código por trás**.
7. Reconstruir tanto o projeto de função como o projeto Azure Stream Analytics.  

### <a name="example"></a>Exemplo

Neste exemplo, **o UDFTest** é um projeto de biblioteca de classe C# e a **ASAUDFDemo** é o projeto Azure Stream Analytics, que fará referência ao **UDFTest.**

![Projeto Azure Stream Analytics IoT Edge em Estúdio Visual](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Construa o seu projeto C#, que lhe permitirá adicionar uma referência à sua C# UDF da consulta Azure Stream Analytics.
    
   ![Construa um projeto Azure Stream Analytics IoT Edge no Estúdio Visual](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Adicione a referência ao projeto C# no projeto ASA. Clique no nó de Referências e escolha Adicionar Referência.

   ![Adicione uma referência a um projeto C# no Estúdio Visual](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Escolha o nome do projeto C# na lista. 
    
   ![Escolha o nome do seu projeto C# na lista de referência](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Deve ver o **UDFTest** listado em **Referências** no **Solution Explorer**.

   ![Ver a referência de função definida pelo utilizador no explorador de soluções](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Clique na pasta **Funções** e escolha **o Novo Item**.

   ![Adicione novo item às funções na solução Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Adicione uma função C# **SquareFunction.json** ao seu projeto Azure Stream Analytics.

   ![Selecione função CSharp a partir de itens Stream Analytics Edge em Estúdio Visual](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Clique duas vezes na função no **Solution Explorer** para abrir o diálogo de configuração.

   ![C configuração de função afiada no Estúdio Visual](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Na configuração da função C#, escolha **a Carga da Referência do Projeto ASA** e os nomes de montagem, classe e método saem da lista de dropdown. Para se referir aos métodos, tipos e funções na consulta Stream Analytics, as classes devem ser definidas como *públicas* e os objetos devem ser definidos como *públicos estáticos*.

   ![Configuração de função afiada Stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Pacotes existentes

Pode ser autor de .NET Standard UDFs em qualquer IDE à sua escolha e invocá-los a partir da sua consulta Azure Stream Analytics. Primeiro compile o seu código e embala todos os DLLs. O formato do pacote `/UserCustomCode/CLR/*`tem o caminho. Em seguida, faça o upload `UserCustomCode.zip` para a raiz do recipiente na sua conta de armazenamento Azure.

Uma vez que os pacotes zip de montagem tenham sido enviados para a sua conta de armazenamento Azure, pode utilizar as funções em consultas Azure Stream Analytics. Tudo o que precisa fazer é incluir as informações de armazenamento na configuração de trabalho do Stream Analytics. Não pode testar a função localmente com esta opção porque as ferramentas do Estúdio Visual não descarregam o seu pacote. O caminho do pacote é analisado diretamente para o serviço. 

Para configurar o caminho de montagem `JobConfig.json`no ficheiro de configuração de trabalho,

Expanda a secção **Configuração do Código Definido Pelo Utilizador** e preencha a configuração com os seguintes valores sugeridos:

   |**Definição**|**Valor Sugerido**|
   |-------|---------------|
   |Recurso de definições de armazenamento global|Escolha a origem de dados na conta atual|
   |Subscrição de configurações globais de armazenamento| < o seu > de subscrição|
   |Conta de armazenamento de configurações globais| < a sua conta de armazenamento >|
   |Recurso de definições de armazenamento de código personalizado|Escolha a origem de dados na conta atual|
   |Conta de armazenamento de código personalizado|< a sua conta de armazenamento >|
   |Recipiente de definições de armazenamento de código personalizado|< o seu > de recipiente de armazenamento|
   |Fonte de montagem de código personalizado|Pacotes de montagem existentes da nuvem|
   |Fonte de montagem de código personalizado|UserCustomCode.zip|

## <a name="limitations"></a>Limitações
A pré-visualização da UDF tem atualmente as seguintes limitações:

* .NET Os UDFs standard só podem ser da autoria do Estúdio Visual e publicados no Azure. As versões apenas de .NET Standard UDFs podem ser vistas em **funções** no portal Azure. A autoria das funções .NET Standard não é suportada no portal Azure.

* O editor de consulta do portal Azure mostra um erro ao utilizar .NET Standard UDF no portal. 

* Como o código personalizado partilha o contexto com o motor Azure Stream Analytics, o código personalizado não pode referenciar nada que tenha um espaço de nome/dll_name conflituoso com o código Azure Stream Analytics. Por exemplo, não se pode fazer referência a *Newtonsoft Json.*

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Escreva uma função c# definida pelo utilizador para um trabalho de Azure Stream Analytics (Pré-visualização)](stream-analytics-edge-csharp-udf.md)
* [Tutorial: funções definidas pelo utilizador do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Use o Estúdio Visual para ver empregos da Azure Stream Analytics](stream-analytics-vs-tools.md)

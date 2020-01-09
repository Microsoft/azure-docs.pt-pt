---
title: Desenvolver funções de .NET Standard para trabalhos de Azure Stream Analytics (versão prévia)
description: Saiba como escrever funções definidas pelo usuário em c# para trabalhos de Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426320"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Desenvolver .NET Standard funções definidas pelo usuário para trabalhos de Azure Stream Analytics (versão prévia)

O Azure Stream Analytics oferece uma linguagem de consulta do tipo SQL para a execução de transformações e as computações sobre fluxos de dados de eventos. Existem muitas funções incorporadas, mas alguns cenários complexos exigem flexibilidade adicional. Com o .NET Standard definidas pelo utilizador Udfs (funções), pode invocar as suas próprias funções escritas em qualquer linguagem de padrão do .NET (C#, F#, etc.) para estender a linguagem de consulta do Stream Analytics. UDFs permitem-lhe realizar cálculos matemáticos complexos, importar modelos de ML personalizados usando ML.NET e usar a lógica de imputation personalizada para dados em falta. O recurso UDF para trabalhos de Stream Analytics está atualmente em visualização e não deve ser usado em cargas de trabalho de produção.

A função definida pelo usuário do .NET para trabalhos de nuvem está disponível em:
* E.U.A. Centro-Oeste
* Europa do Norte
* Este dos E.U.A.
* Oeste dos E.U.A.
* Este dos E.U.A. 2
* Europa Ocidental

Se você estiver interessado em usar esse recurso em qualquer outra região, poderá [solicitar acesso](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Visão geral
O Visual Studio tools para o Azure Stream Analytics tornam mais fácil para que escreva UDFs, testar localmente as tarefas (até mesmo offline) e publique a sua tarefa do Stream Analytics no Azure. Depois de publicado para o Azure, pode implementar a sua tarefa dispositivos de IoT através do IoT Hub.

Existem três formas de implementar UDFs:

* Arquivos Code-behind num projeto do ASA
* UDF num projeto para local
* Um pacote existente de uma conta de armazenamento do Azure

## <a name="package-path"></a>Caminho do pacote

O formato de qualquer pacote UDF tem o caminho `/UserCustomCode/CLR/*`. Bibliotecas de ligação dinâmica (DLLs) e os recursos são copiados sob a `/UserCustomCode/CLR/*` pasta, que ajuda a isolar as DLLs de utilizador do sistema e DLLs do Azure Stream Analytics. Este caminho de pacote é utilizado para todas as funções, independentemente do método utilizado para empregá-los.

## <a name="supported-types-and-mapping"></a>Mapeamento e tipos suportados

|**Tipo UDF (c#)**  |**Escreva o Azure Stream Analytics**  |
|---------|---------|
|longa  |  bigint   |
|double  |  double   |
|string  |  nvarchar (Max)   |
|DateTime  |  DateTime   |
|estrutura  |  IRecord   |
|objeto  |  IRecord   |
|Objeto de\<de matriz >  |  IArray   |
|Dictionary < string, object >  |  IRecord   |

## <a name="codebehind"></a>Code-behind
Pode escrever funções definidas pelo usuário **Script.asql** Code-behind. O Visual Studio tools compilará automaticamente o arquivo de Code-behind num arquivo de assembly. Os assemblies são empacotados como um ficheiro zip e carregados para a sua conta de armazenamento, ao submeter a tarefa para o Azure. Pode aprender a escrever uma UDF do c# com o Code-behind ao seguir a [UDF do c# para tarefas do Stream Analytics Edge](stream-analytics-edge-csharp-udf.md) tutorial. 

## <a name="local-project"></a>Projeto local
Funções definidas pelo utilizador podem ser escritas num assembly que mais tarde é referenciado numa consulta do Azure Stream Analytics. Esta é a opção recomendada para funções complexas que necessitem de todo o potencial de uma linguagem .NET Standard para além da sua linguagem de expressão, como procedimento lógica ou recursão. UDFs de um projeto local podem também ser utilizados quando precisar de partilhar a lógica da função em várias consultas do Azure Stream Analytics. Adicionar UDFs ao seu projeto local dá-lhe a capacidade de depurar e testar as suas funções localmente a partir do Visual Studio.

Para fazer referência a um projeto local:

1. Crie uma nova biblioteca de classes na solução.
2. Escreva o código na sua classe. Lembre-se de que as classes tem de ser definidas como *pública* e objetos tem de ser definidos como *públicos estáticos*. 
3. Crie seu projeto. As ferramentas irão compactar todos os artefactos na pasta bin para um ficheiro zip e carregue o ficheiro zip para a conta de armazenamento. Para as referências externas, utilize a referência ao assembly em vez do pacote NuGet.
4. Referência à classe nova no seu projeto do Azure Stream Analytics.
5. Adicione uma nova função em seu projeto do Azure Stream Analytics.
6. Configurar o caminho de assembly no arquivo de configuração de tarefa, `JobConfig.json`. Definir o caminho de assemblagem para **referência de projeto Local ou Code-behind**.
7. Reconstrua o projeto de função e o projeto do Azure Stream Analytics.  

### <a name="example"></a>Exemplo

Neste exemplo, **UDFTest** é um C# projeto de biblioteca de classes e **ASAUDFDemo** é o projeto Azure Stream Analytics, que fará referência a **UDFTest**.

![Projeto de IoT Edge do Stream Analytics do Azure no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Crie projeto c#, que irá permitir-lhe adicionar uma referência para o UDF do c# da consulta do Azure Stream Analytics.
    
   ![Criar um projeto do Azure Stream Analytics do IoT Edge no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Adicione a referência ao C# projeto no projeto asa. Com o botão direito no nó de referências e escolher adicionar referência.

   ![Adicionar uma referência a um projeto c# no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Escolha o nome do projeto c# da lista. 
    
   ![Escolher o nome do projeto c# a partir da lista de referência](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Deverá ver o **UDFTest** listados na **referências** na **Explorador de soluções**.

   ![Vista do utilizador definida referência de função no Explorador de soluções](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Clique com o botão direito do rato sobre o **funções** pasta e escolha **Novo Item**.

   ![Adicionar novo item a funções na solução de Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Adicionar uma função c# **SquareFunction.json** ao seu projeto do Azure Stream Analytics.

   ![Selecione a função de CSharp itens do Edge do Stream Analytics no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Faça duplo clique em função no **Explorador de soluções** para abrir a caixa de diálogo de configuração.

   ![Configuração de função sharp C no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. No c# configuração de função, escolha **carga de referência de projeto do ASA** e os nomes de assembly, classe e método relacionados na lista pendente. Para se referir aos métodos, tipos e funções na consulta Stream Analytics, as classes devem ser definidas como *públicas* e os objetos devem ser definidos como *públicos estáticos*.

   ![Configuração do Stream Analytics C sharp de função](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Pacotes existentes

Pode criar UDFs padrão do .NET em qualquer IDE à sua escolha e invocá-los da sua consulta do Azure Stream Analytics. Em primeiro lugar compile seu código e o pacote de todas as DLLs. O formato do pacote tem o caminho `/UserCustomCode/CLR/*`. Em seguida, carregue `UserCustomCode.zip` na raiz do contentor na sua conta de armazenamento do Azure.

Depois de serem carregados pacotes zip de assembly para a sua conta de armazenamento do Azure, pode utilizar as funções em consultas do Azure Stream Analytics. Tudo o que você precisa fazer é incluir as informações de armazenamento na configuração do trabalho de Stream Analytics. Não é possível testar a função localmente com esta opção, porque as ferramentas do Visual Studio não irão transferir o pacote. O caminho de pacote é analisado diretamente para o serviço. 

Para configurar o caminho de assembly no arquivo de configuração de tarefa, `JobConfig.json`:

Expanda a secção **Configuração do Código Definido Pelo Utilizador** e preencha a configuração com os seguintes valores sugeridos:

   |**Definição**|**Valor Sugerido**|
   |-------|---------------|
   |Recurso de configurações de armazenamento global|Escolha a origem de dados na conta atual|
   |Assinatura de configurações de armazenamento global| < sua assinatura >|
   |Conta de armazenamento de configurações de armazenamento global| < sua conta de armazenamento >|
   |Recurso de configurações de armazenamento de código personalizado|Escolha a origem de dados na conta atual|
   |Conta de armazenamento de configurações de armazenamento de código personalizado|< sua conta de armazenamento >|
   |Contêiner de configurações de armazenamento de código personalizado|< seu contêiner de armazenamento >|
   |Origem do assembly de código personalizado|Pacotes de assembly existentes da nuvem|
   |Origem do assembly de código personalizado|UserCustomCode. zip|

## <a name="limitations"></a>Limitações
A pré-visualização do UDF atualmente tem as seguintes limitações:

* UDFs padrão do .NET só podem ser criados no Visual Studio e publicados para o Azure. Versões de só de leitura do UDFs padrão do .NET podem ser visualizadas em **funções** no portal do Azure. Criação de funções de .NET Standard não é suportada no portal do Azure.

* O editor de consultas de portal do Azure mostra um erro ao utilizar UDF padrão do .NET no portal. 

* Uma vez que o código personalizado partilha contexto com o motor do Azure Stream Analytics, o código personalizado não pode referenciar-tudo o que tem um espaço de nomes/dll_name em conflito com o código do Azure Stream Analytics. Por exemplo, não pode referenciar *Newtonsoft Json*.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: gravar uma C# função definida pelo usuário para um trabalho de Azure Stream Analytics (versão prévia)](stream-analytics-edge-csharp-udf.md)
* [Tutorial: Funções definidas pelo utilizador JavaScript do Azure do Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Use o Visual Studio para ver tarefas do Azure Stream Analytics](stream-analytics-vs-tools.md)

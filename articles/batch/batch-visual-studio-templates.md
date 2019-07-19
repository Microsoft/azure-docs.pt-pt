---
title: Criar soluções com modelos do Visual Studio – lote do Azure | Microsoft Docs
description: Saiba como os modelos de projeto do Visual Studio podem ajudá-lo a implementar e executar suas cargas de trabalho de computação intensiva no lote do Azure.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: bb4c71f2c7f42ef599796bc380bb7a9f35b8c64e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322780"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Usar modelos de projeto do Visual Studio para iniciar soluções de lote

Os modelos do **Gerenciador de trabalhos** e do **Visual Studio do processador de tarefas** para o lote fornecem código para ajudá-lo a implementar e executar suas cargas de trabalho de computação intensiva no lote com a menor quantidade de esforço. Este documento descreve esses modelos e fornece orientações sobre como usá-los.

> [!IMPORTANT]
> Este artigo aborda apenas as informações aplicáveis a esses dois modelos e pressupõe que você esteja familiarizado com o serviço de lote e os principais conceitos relacionados a ele: pools, nós de computação, trabalhos e tarefas, tarefas do Gerenciador de trabalho, variáveis de ambiente e outros relevantes divulgação. Você pode encontrar mais informações em [noções básicas do lote do Azure](batch-technical-overview.md) e [visão geral do recurso de lote para desenvolvedores](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Descrição geral de alto nível
Os modelos do Gerenciador de trabalho e do processador de tarefas podem ser usados para criar dois componentes úteis:

* Uma tarefa do Gerenciador de trabalho que implementa um divisor de trabalho que pode dividir um trabalho em várias tarefas que podem ser executadas de forma independente, em paralelo.
* Um processador de tarefas que pode ser usado para executar o pré-processamento e o pós-processamento em torno de uma linha de comando do aplicativo.

Por exemplo, em um cenário de renderização de filme, o divisor de trabalho transformaria um único trabalho de filme em centenas ou milhares de tarefas separadas que processariam quadros individuais separadamente. De forma correspondente, o processador de tarefas invocaria o aplicativo de renderização e todos os processos dependentes necessários para renderizar cada quadro, bem como executar quaisquer ações adicionais (por exemplo, copiar o quadro renderizado para um local de armazenamento).

> [!NOTE]
> Os modelos do Gerenciador de trabalho e do processador de tarefas são independentes um do outro, de modo que você pode optar por usar ambos, ou apenas um deles, dependendo dos requisitos de seu trabalho de computação e de suas preferências.
> 
> 

Conforme mostrado no diagrama a seguir, um trabalho de computação que usa esses modelos passará por três estágios:

1. O código do cliente (por exemplo, aplicativo, serviço Web, etc.) envia um trabalho para o serviço de lote no Azure, especificando como sua tarefa do Gerenciador de trabalho é o programa do Gerenciador de trabalho.
2. O serviço de lote executa a tarefa do Gerenciador de trabalho em um nó de computação e o divisor de trabalho inicia o número especificado de tarefas do processador de tarefas, em quantos nós de computação forem necessários, com base nos parâmetros e especificações no código do divisor de trabalho.
3. As tarefas do processador de tarefas são executadas de forma independente, em paralelo, para processar os dados de entrada e gerar os dados de saída.

![Diagrama mostrando como o código do cliente interage com o serviço de lote][diagram01]

## <a name="prerequisites"></a>Pré-requisitos
Para usar os modelos de lote, você precisará do seguinte:

* Um computador com o Visual Studio 2015 instalado. Atualmente, os modelos de lote têm suporte apenas para o Visual Studio 2015.
* Os modelos de lote, que estão disponíveis na [Galeria do Visual Studio][vs_gallery] como extensões do Visual Studio. Há duas maneiras de obter os modelos:
  
  * Instale os modelos usando a caixa de diálogo **extensões e atualizações** no Visual Studio (para obter mais informações, consulte [localizando e usando extensões do Visual Studio][vs_find_use_ext]). Na caixa de diálogo **extensões e atualizações** , pesquise e baixe as duas extensões a seguir:
    
    * Gerenciador de trabalhos do lote do Azure com divisor de trabalho
    * Processador de tarefas do lote do Azure
  * Baixe os modelos da galeria online para o Visual Studio: [Modelos de projeto Lote do Microsoft Azure][vs_gallery_templates]
* Se você planeja usar o recurso de [pacotes de aplicativos](batch-application-packages.md) para implantar o Gerenciador de trabalhos e o processador de tarefas nos nós de computação do lote, será necessário vincular uma conta de armazenamento à sua conta do lote.

## <a name="preparation"></a>Preparação
É recomendável criar uma solução que possa conter o Gerenciador de trabalho, bem como o processador de tarefas, porque isso pode facilitar o compartilhamento de código entre o Gerenciador de trabalho e os programas do processador de tarefas. Para criar essa solução, siga estas etapas:

1. Abra o Visual Studio e selecione **arquivo** > **novo** > **projeto**.
2. Em **modelos**, expanda **outros tipos de projeto**, clique em **soluções do Visual Studio**e selecione **solução em branco**.
3. Digite um nome que descreva seu aplicativo e a finalidade dessa solução (por exemplo, "Programasdetarefasdolotelitware").
4. Para criar a nova solução, clique em **OK**.

## <a name="job-manager-template"></a>Modelo do Gerenciador de trabalho
O modelo do Gerenciador de trabalho ajuda a implementar uma tarefa do Gerenciador de trabalho que pode executar as seguintes ações:

* Divida um trabalho em várias tarefas.
* Envie essas tarefas para execução no lote.

> [!NOTE]
> Para obter mais informações sobre tarefas do Gerenciador de trabalho, consulte [visão geral do recurso de lote para desenvolvedores](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Criar um Gerenciador de trabalho usando o modelo
Para adicionar um Gerenciador de trabalhos à solução que você criou anteriormente, siga estas etapas:

1. Abra sua solução existente no Visual Studio.
2. Em Gerenciador de soluções, clique com o botão direito do mouse na solução, clique em **Adicionar** > **novo projeto**.
3. Em **Visual C#** , clique em **nuvem**e, em seguida, clique em **Gerenciador de trabalho do lote do Azure com divisor de trabalho**.
4. Digite um nome que descreva seu aplicativo e identifique esse projeto como o Gerenciador de trabalhos (por exemplo, "LitwareJobManager").
5. Para criar o projeto, clique em **OK**.
6. Por fim, compile o projeto para forçar o Visual Studio a carregar todos os pacotes NuGet referenciados e verificar se o projeto é válido antes de você começar a modificá-lo.

### <a name="job-manager-template-files-and-their-purpose"></a>Arquivos de modelo do Gerenciador de trabalho e sua finalidade
Quando você cria um projeto usando o modelo do Gerenciador de trabalho, ele gera três grupos de arquivos de código:

* O arquivo de programa principal (Program.cs). Ele contém o ponto de entrada do programa e a manipulação de exceção de nível superior. Normalmente, você não precisa modificar isso.
* O diretório da estrutura. Ele contém os arquivos responsáveis pelo trabalho ' clichê ' feito pelo programa gerenciador de trabalho – descompactando parâmetros, adicionando tarefas ao trabalho em lotes, etc. Normalmente, você não precisa modificar esses arquivos.
* O arquivo do divisor de trabalho (JobSplitter.cs). É aí que você colocará a lógica específica do aplicativo para dividir um trabalho em tarefas.

É claro que você pode adicionar arquivos adicionais conforme necessário para dar suporte ao seu código de divisor de trabalho, dependendo da complexidade da lógica de divisão de trabalho.

O modelo também gera arquivos de projeto .NET padrão, como um arquivo. csproj, app. config, packages. config, etc.

O restante desta seção descreve os diferentes arquivos e sua estrutura de código e explica o que cada classe faz.

![O Visual Studio Gerenciador de Soluções mostrando a solução de modelo do Gerenciador de trabalho][solution_explorer01]

**Arquivos do Framework**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de trabalho, como detalhes da conta do lote, credenciais de conta de armazenamento vinculadas, informações de trabalho e tarefa e parâmetros de trabalho. Ele também fornece acesso a variáveis de ambiente definidas pelo lote (consulte Configurações de ambiente para tarefas, na documentação do lote) por meio da classe Configuration. EnvironmentVariable.
* `IConfiguration.cs`: Abstrai a implementação da classe de configuração para que você possa testar a unidade do divisor de trabalho usando um objeto de configuração falso ou fictício.
* `JobManager.cs`: Orquestra os componentes do programa gerenciador de trabalho. Ele é responsável pela inicialização do divisor de trabalho, invocando o divisor de trabalho e expedindo as tarefas retornadas pelo divisor de trabalho para o emissor da tarefa.
* `JobManagerException.cs`: Representa um erro que exige que o Gerenciador de trabalhos seja encerrado. JobManagerException é usado para encapsular erros "esperados" onde informações de diagnóstico específicas podem ser fornecidas como parte do encerramento.
* `TaskSubmitter.cs`: Essa classe é responsável por adicionar tarefas retornadas pelo divisor de trabalho para o trabalho em lotes. A classe JobManager agrega a sequência de tarefas em lotes para uma adição eficiente, mas oportuna ao trabalho, então chama TaskSubmitter. SubmitTasks em um thread em segundo plano para cada lote.

**Divisor de trabalho**

`JobSplitter.cs`: Essa classe contém lógica específica do aplicativo para dividir o trabalho em tarefas. A estrutura invoca o método JobSplitter. Split para obter uma sequência de tarefas, que ele adiciona ao trabalho conforme o método os retorna. Essa é a classe na qual você injetará a lógica do seu trabalho. Implemente o método Split para retornar uma sequência de instâncias de CloudTask que representam as tarefas nas quais você deseja particionar o trabalho.

**Arquivos de projeto de linha de comando padrão do .NET**

* `App.config`: Arquivo de configuração de aplicativo .NET padrão.
* `Packages.config`: Arquivo de dependência do pacote NuGet padrão.
* `Program.cs`: Contém o ponto de entrada do programa e a manipulação de exceção de nível superior.

### <a name="implementing-the-job-splitter"></a>Implementando o divisor de trabalho
Quando você abrir o projeto de modelo do Gerenciador de trabalho, o projeto terá o arquivo JobSplitter.cs aberto por padrão. Você pode implementar a lógica de divisão para as tarefas em sua carga de trabalho usando o método Split () mostrado abaixo:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> A seção anotada no `Split()` método é a única seção do código do modelo do Gerenciador de trabalho que se destina a você modificar adicionando a lógica para dividir seus trabalhos em tarefas diferentes. Se você quiser modificar uma seção diferente do modelo, verifique se está familiarizado com o funcionamento do lote e experimente alguns dos [exemplos de código do lote][github_samples].
> 
> 

Sua implementação de Split () tem acesso a:

* Os parâmetros do trabalho, por `_parameters` meio do campo.
* O objeto CloudJob que representa o trabalho, por `_job` meio do campo.
* O objeto CloudTask que representa a tarefa do Gerenciador de trabalho `_jobManagerTask` , por meio do campo.

Sua `Split()` implementação não precisa adicionar tarefas diretamente ao trabalho. Em vez disso, seu código deve retornar uma sequência de objetos CloudTask, e eles serão adicionados ao trabalho automaticamente pelas classes de estrutura que invocam o divisor de trabalho. É comum usar C#o recurso iterador (`yield return`) do para implementar divisores de trabalho, pois isso permite que as tarefas comecem a ser executadas assim que possível, em vez de esperar que todas as tarefas sejam calculadas.

**Falha do divisor de trabalho**

Se o divisor de trabalho encontrar um erro, ele deverá:

* Encerre a sequência usando a C# `yield break` instrução, caso em que o Gerenciador de trabalho será tratado como bem-sucedido; ou
* Lance uma exceção. nesse caso, o Gerenciador de trabalho será tratado como com falha e poderá ser repetido dependendo de como o cliente o configurou).

Em ambos os casos, todas as tarefas já retornadas pelo divisor de trabalho e adicionadas ao trabalho do lote serão elegíveis para execução. Se não quiser que isso aconteça, você poderia:

* Encerrar o trabalho antes de retornar do divisor de trabalho
* Formular a coleção de tarefas inteira antes de retorná-la ( `ICollection<CloudTask>` ou `IList<CloudTask>` seja, retornar um ou em vez de implementar C# seu divisor de trabalho usando um iterador)
* Usar dependências de tarefas para fazer com que todas as tarefas dependam da conclusão bem-sucedida do Gerenciador de trabalho

**Novas tentativas do Gerenciador de trabalho**

Se o Gerenciador de trabalho falhar, ele poderá ser tentado novamente pelo serviço de lote, dependendo das configurações de repetição do cliente. Em geral, isso é seguro, porque quando a estrutura adiciona tarefas ao trabalho, ela ignora todas as tarefas que já existem. No entanto, se o cálculo das tarefas for caro, talvez você não queira incorrer no custo de recalcular as tarefas que já foram adicionadas ao trabalho; por outro lado, se a nova execução não tiver garantia de gerar as mesmas identificações de tarefa, o comportamento ' ignorar duplicatas ' não será ativado. Nesses casos, você deve projetar seu divisor de trabalho para detectar o trabalho que já foi feito e não repeti-lo, por exemplo, executando um CloudJob. ListTasks antes de começar a gerar tarefas.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Códigos de saída e exceções no modelo do Gerenciador de trabalho
Códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e eles podem ajudar a identificar problemas com a execução do programa. O modelo do Gerenciador de trabalho implementa os códigos de saída e as exceções descritas nesta seção.

Uma tarefa do Gerenciador de trabalho implementada com o modelo do Gerenciador de trabalho pode retornar três códigos de saída possíveis:

| Código | Descrição |
| --- | --- |
| 0 |O Gerenciador de trabalho foi concluído com êxito. O código do divisor de trabalho foi executado até a conclusão e todas as tarefas foram adicionadas ao trabalho. |
| 1 |A tarefa do Gerenciador de trabalho falhou com uma exceção em uma parte "esperada" do programa. A exceção foi convertida em um JobManagerException com informações de diagnóstico e, quando possível, sugestões para resolver a falha. |
| 2 |A tarefa do Gerenciador de trabalho falhou com uma exceção ' inesperada '. A exceção foi registrada na saída padrão, mas o Gerenciador de trabalho não pôde adicionar informações adicionais de diagnóstico ou correção. |

No caso de falha da tarefa do Gerenciador de trabalho, algumas tarefas ainda podem ter sido adicionadas ao serviço antes de o erro ter ocorrido. Essas tarefas serão executadas normalmente. Consulte "falha do divisor de trabalho" acima para obter uma discussão sobre esse caminho de código.

Todas as informações retornadas por exceções são gravadas em arquivos stdout. txt e stderr. txt. Para obter mais informações, consulte [tratamento de erros](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considerações do cliente
Esta seção descreve alguns requisitos de implementação do cliente ao invocar um Gerenciador de trabalho com base neste modelo. Consulte [como passar parâmetros e variáveis de ambiente do código do cliente](#pass-environment-settings) para obter detalhes sobre como passar parâmetros e configurações de ambiente.

**Credenciais obrigatórias**

Para adicionar tarefas ao trabalho do lote do Azure, a tarefa do Gerenciador de trabalho requer a URL e a chave da conta do lote do Azure. Você deve passá-los em variáveis de ambiente chamadas YOUR_BATCH_URL e YOUR_BATCH_KEY. Você pode defini-las nas configurações do ambiente de tarefas do Gerenciador de trabalho. Por exemplo, em um C# cliente:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciais de armazenamento**

Normalmente, o cliente não precisa fornecer as credenciais da conta de armazenamento vinculada à tarefa do Gerenciador de trabalho porque (a) a maioria dos gerentes de trabalho não precisa acessar explicitamente a conta de armazenamento vinculada e (b) a conta de armazenamento vinculada é geralmente fornecida a todas as tarefas como um configuração de ambiente comum para o trabalho. Se você não estiver fornecendo a conta de armazenamento vinculada por meio das configurações de ambiente comuns e o Gerenciador de trabalho exigir acesso ao armazenamento vinculado, você deverá fornecer as credenciais de armazenamento vinculadas da seguinte maneira:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Configurações de tarefa do Gerenciador de trabalho**

O cliente deve definir o sinalizador *killJobOnCompletion* do Gerenciador de trabalho como **falso**.

Geralmente, é seguro que o cliente defina *runExclusive* como **false**.

O cliente deve usar a coleção *resourceFiles* ou *applicationPackageReferences* para que o executável do Gerenciador de trabalho (e suas DLLs necessárias) sejam implantados no nó de computação.

Por padrão, o Gerenciador de trabalho não será repetido se falhar. Dependendo da lógica do Gerenciador de trabalho, o cliente pode querer habilitar repetições por meio de *restrições*/*maxTaskRetryCount*.

**Configurações de trabalho**

Se o divisor de trabalho emitir tarefas com dependências, o cliente deverá definir o usesTaskDependencies do trabalho como true.

No modelo de divisor de trabalho, é incomum para os clientes desejarem adicionar tarefas a trabalhos além do que o divisor de trabalho cria. O cliente, portanto, deve, normalmente, definir o *onAllTasksComplete* do trabalho como **terminatejob**.

## <a name="task-processor-template"></a>Modelo do processador de tarefas
Um modelo de processador de tarefas ajuda a implementar um processador de tarefas que pode executar as seguintes ações:

* Configure as informações exigidas por cada tarefa do lote a ser executada.
* Execute todas as ações exigidas por cada tarefa do lote.
* Salvar saídas de tarefa para armazenamento persistente.

Embora não seja necessário um processador de tarefas para executar tarefas no lote, a principal vantagem de usar um processador de tarefas é que ele fornece um wrapper para implementar todas as ações de execução de tarefas em um único local. Por exemplo, se você precisar executar vários aplicativos no contexto de cada tarefa, ou se precisar copiar dados para o armazenamento persistente depois de concluir cada tarefa.

As ações executadas pelo processador de tarefas podem ser simples ou complexas, e tantas ou poucas, conforme exigido pela sua carga de trabalho. Além disso, ao implementar todas as ações de tarefa em um processador de tarefas, você pode facilmente atualizar ou adicionar ações com base em alterações em aplicativos ou requisitos de carga de trabalho. No entanto, em alguns casos, um processador de tarefas pode não ser a solução ideal para sua implementação, pois pode adicionar complexidade desnecessária, por exemplo, ao executar trabalhos que podem ser rapidamente iniciados a partir de uma linha de comando simples.

### <a name="create-a-task-processor-using-the-template"></a>Criar um processador de tarefas usando o modelo
Para adicionar um processador de tarefas à solução que você criou anteriormente, siga estas etapas:

1. Abra sua solução existente no Visual Studio.
2. Em Gerenciador de Soluções, clique com o botão direito do mouse na solução, clique em **Adicionar**e em **novo projeto**.
3. Em **Visual C#** , clique em **nuvem**e, em seguida, clique em **processador de tarefas do lote do Azure**.
4. Digite um nome que descreva seu aplicativo e identifique esse projeto como o processador de tarefas (por exemplo, "LitwareTaskProcessor").
5. Para criar o projeto, clique em **OK**.
6. Por fim, compile o projeto para forçar o Visual Studio a carregar todos os pacotes NuGet referenciados e verificar se o projeto é válido antes de você começar a modificá-lo.

### <a name="task-processor-template-files-and-their-purpose"></a>Arquivos de modelo do processador de tarefas e sua finalidade
Quando você cria um projeto usando o modelo de processador de tarefas, ele gera três grupos de arquivos de código:

* O arquivo de programa principal (Program.cs). Ele contém o ponto de entrada do programa e a manipulação de exceção de nível superior. Normalmente, você não precisa modificar isso.
* O diretório da estrutura. Ele contém os arquivos responsáveis pelo trabalho ' clichê ' feito pelo programa gerenciador de trabalho – descompactando parâmetros, adicionando tarefas ao trabalho em lotes, etc. Normalmente, você não precisa modificar esses arquivos.
* O arquivo do processador de tarefas (TaskProcessor.cs). É aí que você colocará a lógica específica do aplicativo para executar uma tarefa (normalmente chamando um executável existente). Código de pré e pós-processamento, como baixar dados adicionais ou carregar arquivos de resultado, também aparece aqui.

É claro que você pode adicionar arquivos adicionais conforme necessário para dar suporte ao código do processador de tarefas, dependendo da complexidade da lógica de divisão do trabalho.

O modelo também gera arquivos de projeto .NET padrão, como um arquivo. csproj, app. config, packages. config, etc.

O restante desta seção descreve os diferentes arquivos e sua estrutura de código e explica o que cada classe faz.

![O Visual Studio Gerenciador de Soluções mostrando a solução de modelo do processador de tarefas][solution_explorer02]

**Arquivos do Framework**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de trabalho, como detalhes da conta do lote, credenciais de conta de armazenamento vinculadas, informações de trabalho e tarefa e parâmetros de trabalho. Ele também fornece acesso a variáveis de ambiente definidas pelo lote (consulte Configurações de ambiente para tarefas, na documentação do lote) por meio da classe Configuration. EnvironmentVariable.
* `IConfiguration.cs`: Abstrai a implementação da classe de configuração para que você possa testar a unidade do divisor de trabalho usando um objeto de configuração falso ou fictício.
* `TaskProcessorException.cs`: Representa um erro que exige que o Gerenciador de trabalhos seja encerrado. TaskProcessorException é usado para encapsular erros "esperados" onde informações de diagnóstico específicas podem ser fornecidas como parte do encerramento.

**Processador de tarefas**

* `TaskProcessor.cs`: Executa a tarefa. A estrutura invoca o método TaskProcessor. Run. Essa é a classe na qual você injetará a lógica específica do aplicativo da sua tarefa. Implemente o método Run para:
  * Analisar e validar qualquer parâmetro de tarefa
  * Compor a linha de comando para qualquer programa externo que você deseja invocar
  * Registrar as informações de diagnóstico que você pode exigir para fins de depuração
  * Iniciar um processo usando essa linha de comando
  * Aguarde a saída do processo
  * Capturar o código de saída do processo para determinar se ele teve êxito ou falhou
  * Salve os arquivos de saída que você deseja manter no armazenamento persistente

**Arquivos de projeto de linha de comando padrão do .NET**

* `App.config`: Arquivo de configuração de aplicativo .NET padrão.
* `Packages.config`: Arquivo de dependência do pacote NuGet padrão.
* `Program.cs`: Contém o ponto de entrada do programa e a manipulação de exceção de nível superior.

## <a name="implementing-the-task-processor"></a>Implementando o processador de tarefas
Quando você abrir o projeto de modelo do processador de tarefas, o projeto terá o arquivo TaskProcessor.cs aberto por padrão. Você pode implementar a lógica de execução para as tarefas em sua carga de trabalho usando o método Run () mostrado abaixo:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> A seção anotada no método Run () é a única seção do código do modelo do processador de tarefas que se destina a você modificar adicionando a lógica de execução para as tarefas em sua carga de trabalho. Se você quiser modificar uma seção diferente do modelo, familiarize-se primeiro com o funcionamento do lote examinando a documentação do lote e experimentando alguns dos exemplos de código do lote.
> 
> 

O método Run () é responsável por iniciar a linha de comando, iniciar um ou mais processos, aguardar a conclusão de todo o processo, salvar os resultados e, finalmente, retornar com um código de saída. O método Run () é onde você implementa a lógica de processamento para suas tarefas. A estrutura do processador de tarefas invoca o método Run () para você; Você não precisa chamá-lo por conta própria.

Sua implementação de Run () tem acesso a:

* Os parâmetros da tarefa, por `_parameters` meio do campo.
* As IDs de trabalho e tarefa, por `_jobId` meio `_taskId` dos campos e.
* A configuração da tarefa, por `_configuration` meio do campo.

**Falha da tarefa**

Em caso de falha, você pode sair do método Run () lançando uma exceção, mas isso deixa o manipulador de exceção de nível superior no controle do código de saída da tarefa. Se você precisar controlar o código de saída para que possa distinguir diferentes tipos de falha, por exemplo, para fins de diagnóstico ou porque alguns modos de falha devem encerrar o trabalho e outros não devem, então você deve sair do método Run () retornando um valor diferente de zero código de saída. Isso se torna o código de saída da tarefa.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Códigos de saída e exceções no modelo do processador de tarefas
Códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e eles podem ajudar a identificar quaisquer problemas com a execução do programa. O modelo do processador de tarefas implementa os códigos de saída e as exceções descritas nesta seção.

Uma tarefa do processador de tarefas que é implementada com o modelo do processador de tarefas pode retornar três códigos de saída possíveis:

| Código | Descrição |
| --- | --- |
| [Process. ExitCode][process_exitcode] |O processador de tarefas foi executado até a conclusão. Observe que isso não significa que o programa que você chamou foi bem-sucedido – apenas que o processador de tarefas o invocou com êxito e executou qualquer pós-processamento sem exceções. O significado do código de saída depende do programa invocado – normalmente o código de saída 0 significa que o programa foi bem-sucedido e qualquer outro código de saída significa que o programa falhou. |
| 1 |O processador de tarefas falhou com uma exceção em uma parte "esperada" do programa. A exceção foi convertida em `TaskProcessorException` uma com informações de diagnóstico e, quando possível, sugestões para resolver a falha. |
| 2 |O processador de tarefas falhou com uma exceção ' inesperada '. A exceção foi registrada na saída padrão, mas o processador de tarefas não pôde adicionar informações adicionais de diagnóstico ou correção. |

> [!NOTE]
> Se o programa que você invocar usa os códigos de saída 1 e 2 para indicar modos de falha específicos, o uso de códigos de saída 1 e 2 para erros do processador de tarefas é ambíguo. Você pode alterar esses códigos de erro do processador de tarefas para códigos de saída distintivos editando os casos de exceção no arquivo Program.cs.
> 
> 

Todas as informações retornadas por exceções são gravadas em arquivos stdout. txt e stderr. txt. Para obter mais informações, consulte tratamento de erros, na documentação do lote.

### <a name="client-considerations"></a>Considerações do cliente
**Credenciais de armazenamento**

Se o processador de tarefas usar o armazenamento de BLOBs do Azure para manter as saídas, por exemplo, usando a biblioteca auxiliar de convenções de arquivo, ele precisará acessar as *credenciais da conta* de armazenamento em nuvem *ou* uma URL de contêiner de BLOB que inclui um acesso compartilhado assinatura (SAS). O modelo inclui suporte para fornecer credenciais por meio de variáveis de ambiente comuns. O cliente pode passar as credenciais de armazenamento da seguinte maneira:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A conta de armazenamento fica disponível na classe TaskProcessor por meio da `_configuration.StorageAccount` propriedade.

Se preferir usar uma URL de contêiner com SAS, você também poderá passá-la por meio de uma configuração de ambiente comum de trabalho, mas o modelo de processador de tarefas não inclui atualmente suporte interno para isso.

**Configuração de armazenamento**

É recomendável que a tarefa cliente ou Gerenciador de trabalho crie todos os contêineres exigidos pelas tarefas antes de adicionar as tarefas ao trabalho. Isso é obrigatório se você usar uma URL de contêiner com SAS, pois tal URL não inclui permissão para criar o contêiner. É recomendável mesmo que você passe as credenciais da conta de armazenamento, pois ela salva todas as tarefas que têm que chamar CloudBlobContainer. CreateIfNotExistsAsync no contêiner.

## <a name="pass-parameters-and-environment-variables"></a>Passar parâmetros e variáveis de ambiente
### <a name="pass-environment-settings"></a>Passar configurações de ambiente
Um cliente pode passar informações para a tarefa do Gerenciador de trabalho na forma de configurações de ambiente. Essas informações podem ser usadas pela tarefa do Gerenciador de trabalho ao gerar as tarefas do processador de tarefas que serão executadas como parte do trabalho de computação. Exemplos das informações que você pode passar como configurações de ambiente são:

* Nome da conta de armazenamento e chaves de conta
* URL da conta do lote
* Chave de conta do lote

O serviço de lote tem um mecanismo simples para passar configurações de ambiente para uma tarefa do Gerenciador de `EnvironmentSettings` trabalho usando a propriedade em [Microsoft. Azure. Batch. JobManagerTask][net_jobmanagertask].

Por exemplo, para obter a `BatchClient` instância para uma conta do lote, você pode passar como variáveis de ambiente do código do cliente a URL e as credenciais de chave compartilhada para a conta do lote. Da mesma forma, para acessar a conta de armazenamento que está vinculada à conta do lote, você pode passar o nome da conta de armazenamento e a chave da conta de armazenamento como variáveis de ambiente.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passar parâmetros para o modelo do Gerenciador de trabalho
Em muitos casos, é útil passar parâmetros por trabalho para a tarefa do Gerenciador de trabalho, para controlar o processo de divisão de trabalho ou para configurar as tarefas para o trabalho. Você pode fazer isso carregando um arquivo JSON chamado Parameters. JSON como um arquivo de recurso para a tarefa do Gerenciador de trabalho. Os parâmetros podem então ser disponibilizados no `JobSplitter._parameters` campo no modelo do Gerenciador de trabalho.

> [!NOTE]
> O manipulador de parâmetro interno dá suporte apenas a dicionários de cadeia de caracteres para cadeia de caracteres. Se você quiser passar valores JSON complexos como valores de parâmetro, será necessário passá-los como cadeias de caracteres e analisá-los no divisor de trabalho ou modificar o método `Configuration.GetJobParameters` da estrutura.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Passar parâmetros para o modelo de processador de tarefas
Você também pode passar parâmetros para tarefas individuais implementadas usando o modelo de processador de tarefas. Assim como ocorre com o modelo do Gerenciador de trabalho, o modelo do processador de tarefas procura um arquivo de recurso chamado

Parameters. JSON e, se encontrado, ele o carrega como o dicionário de parâmetros. Há algumas opções para passar parâmetros para as tarefas do processador de tarefas:

* Reutilize o JSON dos parâmetros do trabalho. Isso funciona bem se os únicos parâmetros forem de todo o trabalho (por exemplo, uma altura e largura de renderização). Para fazer isso, ao criar um CloudTask no divisor de trabalho, adicione uma referência ao objeto do arquivo de recurso Parameters. JSON da ResourceFiles da tarefa do Gerenciador`JobSplitter._jobManagerTask.ResourceFiles`de trabalho () à coleção ResourceFiles do CloudTask.
* Gerar e carregar um documento. JSON de parâmetros específicos da tarefa como parte da execução do divisor de trabalho e fazer referência a esse blob na coleção de arquivos de recursos da tarefa. Isso será necessário se diferentes tarefas tiverem parâmetros diferentes. Um exemplo pode ser um cenário de renderização 3D em que o índice de quadro é passado para a tarefa como um parâmetro.

> [!NOTE]
> O manipulador de parâmetro interno dá suporte apenas a dicionários de cadeia de caracteres para cadeia de caracteres. Se você quiser passar valores JSON complexos como valores de parâmetro, será necessário passá-los como cadeias de caracteres e analisá-los no processador de tarefas ou modificar o `Configuration.GetTaskParameters` método da estrutura.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
### <a name="persist-job-and-task-output-to-azure-storage"></a>Manter saída de trabalho e tarefa no armazenamento do Azure
Outra ferramenta útil no desenvolvimento da solução do lote é as [convenções de arquivo do lote do Azure][nuget_package]. Use essa biblioteca de classes .NET (atualmente em visualização) em seus aplicativos .NET do lote para armazenar e recuperar facilmente saídas de tarefas para e do armazenamento do Azure. [Persistir o trabalho do lote do Azure e a saída da tarefa](batch-task-output.md) contém uma discussão completa sobre a biblioteca e seu uso.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png

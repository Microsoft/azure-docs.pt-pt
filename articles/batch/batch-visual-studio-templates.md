---
title: Construa soluções com modelos de Estúdio Visual
description: Saiba como os modelos de projeto do Visual Studio podem ajudá-lo a implementar e executar as suas cargas de trabalho intensivas em Azure Batch.
ms.topic: how-to
ms.date: 02/27/2017
ms.custom: seodec18
ms.openlocfilehash: c4cdc3fa7b3238a83d55113c5f7dc551d637c8e2
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959777"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Use modelos de projeto visual Studio para iniciar soluções de Lote

Os modelos de Estúdio Visual **do Gestor de Trabalho** e do Processador de **Tarefas** do Batch fornecem código para o ajudar a implementar e executar as suas cargas de trabalho intensivas em Batch com o mínimo de esforço. Este documento descreve estes modelos e fornece orientações para como usá-los.

> [!IMPORTANT]
> Este artigo discute apenas informações aplicáveis a estes dois modelos, e assume que está familiarizado com o serviço Batch e conceitos-chave relacionados com ele: pools, nómada computacional, empregos e tarefas, tarefas de gestor de emprego, variáveis ambientais e outras informações relevantes. Pode encontrar mais informações sobre o fluxo de trabalho e recursos básicos do serviço [Azure Batch](batch-technical-overview.md) [e Batch.](batch-service-workflow-features.md) 

## <a name="high-level-overview"></a>Descrição geral de alto nível
Os modelos de Gestor de Trabalho e processador de tarefas podem ser usados para criar dois componentes úteis:

* Uma tarefa de gestor de emprego que implementa um divisor de emprego que pode dividir um emprego em múltiplas tarefas que podem ser executadas de forma independente, em paralelo.
* Um processador de tarefas que pode ser utilizado para realizar pré-processamento e pós-processamento em torno de uma linha de comando de aplicação.

Por exemplo, num cenário de renderização de filmes, o divisa de emprego transformaria um único trabalho de filme em centenas ou milhares de tarefas separadas que processariam quadros individuais separadamente. Consequentemente, o processador de tarefas invocaria a aplicação de renderização e todos os processos dependentes necessários para renderizar cada quadro, bem como executar quaisquer ações adicionais (por exemplo, copiar o quadro renderizado para um local de armazenamento).

> [!NOTE]
> Os modelos de Gestor de Trabalho e Processador de Tarefas são independentes uns dos outros, pelo que pode optar por utilizar ambos, ou apenas um deles, dependendo dos requisitos do seu trabalho de computação e das suas preferências.
> 
> 

Como mostrado no diagrama abaixo, um trabalho de computação que usa estes modelos passará por três fases:

1. O código do cliente (por exemplo, aplicação, serviço web, etc.) submete um trabalho ao serviço Batch em Azure, especificando como seu gestor de emprego o programa de gestor de emprego.
2. O serviço Batch executa a tarefa de gerente de trabalho num nó de computação e o separador de trabalho lança o número especificado de tarefas de processador de tarefas, em todos os nós computacional necessários, com base nos parâmetros e especificações no código de divisão de trabalho.
3. As tarefas do processador de tarefas são executadas de forma independente, paralelamente, para processar os dados de entrada e gerar os dados de saída.

![Diagrama mostrando como o código do cliente interage com o serviço Batch][diagram01]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar os modelos lote, necessitará do seguinte:

* Um computador com Visual Studio 2015 instalado. Os modelos de lote são atualmente suportados apenas para Visual Studio 2015.
* Os modelos Batch, que estão disponíveis na [Visual Studio Gallery][vs_gallery] como extensões do Visual Studio. Há duas maneiras de obter os modelos:
  
  * Instale os modelos utilizando a caixa de diálogo **extensões e atualizações** no Estúdio Visual (para mais informações, ver [Encontrar e utilizar extensões de estúdio visual).][vs_find_use_ext] Na caixa de diálogo **Extensões e Atualizações,** procure e descarregue as seguintes duas extensões:
    
    * Gestor de emprego de Azure Batch com job splitter
    * Processador de tarefas Azure Batch
  * Descarregue os modelos da galeria online para Visual Studio: [Microsoft Azure Batch Project Templates][vs_gallery_templates]
* Se planeia utilizar a função [Pacotes de Aplicação](batch-application-packages.md) para implantar o gestor de emprego e o processador de tarefas nos nós de computação Batch, tem de ligar uma conta de armazenamento à sua conta Batch.

## <a name="preparation"></a>Preparação
Recomendamos a criação de uma solução que possa conter o seu gestor de emprego, bem como o seu processador de tarefas, porque isso pode facilitar a partilha de código entre o seu gestor de emprego e programas de processador de tarefas. Para criar esta solução, siga estes passos:

1. Abra o Estúdio Visual e selecione **File**  >  **New**  >  **Project**.
2. Em **Modelos**, expanda **outros tipos de projetos,** clique em **Soluções de Estúdio Visual**e, em seguida, selecione Blank **Solution**.
3. Digite um nome que descreva a sua aplicação e o propósito desta solução (por exemplo, "LitwareBatchTaskPrograms").
4. Para criar a nova solução, clique **em OK**.

## <a name="job-manager-template"></a>Modelo de gestor de emprego
O modelo de Gestor de Emprego ajuda-o a implementar uma tarefa de gestor de emprego que pode executar as seguintes ações:

* Dividir um trabalho em várias tarefas.
* Submeta essas tarefas para executar no Batch.

> [!NOTE]
> Para obter mais informações sobre tarefas de gestor de emprego, consulte [Jobs e tarefas](jobs-and-tasks.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Criar um gestor de emprego usando o modelo
Para adicionar um gestor de emprego à solução que criou anteriormente, siga estes passos:

1. Abra a sua solução existente no Visual Studio.
2. No Solution Explorer, clique com o botão direito na solução, clique em **Adicionar**  >  **Novo Projeto**.
3. Em **Visual C.** clique em **Cloud**e, em seguida, clique em **Azure Batch Job Manager com Job Splitter**.
4. Digite um nome que descreva a sua aplicação e identifique este projeto como gestor de emprego (por exemplo, "LitwareJobManager").
5. Para criar o projeto, clique **em OK.**
6. Por fim, construa o projeto para forçar o Visual Studio a carregar todos os pacotes NuGet referenciados e verificar se o projeto é válido antes de começar a modificá-lo.

### <a name="job-manager-template-files-and-their-purpose"></a>Ficheiros de modelo de gestor de emprego e seu propósito
Quando cria um projeto utilizando o modelo de Gestor de Emprego, gera três grupos de ficheiros de código:

* O ficheiro principal do programa (Program.cs). Isto contém o ponto de entrada do programa e o manuseamento de exceções de nível superior. Normalmente não devias ter de modificar isto.
* O directório-quadro. Isto contém os ficheiros responsáveis pelo trabalho de 'caldeira' realizado pelo programa de gestor de trabalho – desembalar parâmetros, adicionar tarefas à função Batch, etc. Normalmente não é preciso modificar estes ficheiros.
* O ficheiro de divisão de trabalho (JobSplitter.cs). É aqui que colocará a sua lógica específica para dividir um emprego em tarefas.

Claro que pode adicionar ficheiros adicionais, conforme necessário para suportar o código de divisão de trabalho, dependendo da complexidade da lógica de divisão de trabalho.

O modelo também gera ficheiros de projetos .NET padrão, tais como um ficheiro .csproj, app.config, packages.config, etc.

O resto desta secção descreve os diferentes ficheiros e a sua estrutura de código, e explica o que cada classe faz.

![Visual Studio Solution Explorer mostrando a solução de modelo de gestor de emprego][solution_explorer01]

**Ficheiros-quadro**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de emprego, tais como detalhes da conta Batch, credenciais de conta de armazenamento ligadas, informações de trabalho e de tarefas e parâmetros de trabalho. Também fornece acesso a variáveis ambientais definidas em lote (ver definições de ambiente para tarefas, na documentação do Lote) através da classe Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Resumos da implementação da classe Configuração, para que possa testar o seu separador de trabalho usando um objeto de configuração falso ou simulado.
* `JobManager.cs`: Orquestra os componentes do programa de gestor de emprego. É responsável pela inicialização do divisa de trabalho, invocando o divisa de emprego, e despachando as tarefas devolvidas pelo divisa de trabalho para o proponente da tarefa.
* `JobManagerException.cs`: Representa um erro que exige que o gestor de emprego seja encerrado. JobManagerException é usado para embrulhar erros "esperados" onde informações específicas de diagnóstico podem ser fornecidas como parte da rescisão.
* `TaskSubmitter.cs`: Esta classe é responsável por adicionar tarefas devolvidas pelo separador de trabalho à função Batch. A classe JobManager agrega a sequência de tarefas em lotes para uma adição eficiente mas oportuna ao trabalho, e depois chama TaskSubmitter.SubmitTasks num fio de fundo para cada lote.

**Divisão de Emprego**

`JobSplitter.cs`: Esta classe contém lógica específica de aplicação para dividir o trabalho em tarefas. O quadro invoca o método JobSplitter.Split para obter uma sequência de tarefas, que adiciona ao trabalho à medida que o método as devolve. Esta é a aula onde vais injetar a lógica do teu trabalho. Implemente o método Split para devolver uma sequência de instâncias CloudTask que representam as tarefas em que pretende dividir o trabalho.

**Ficheiros de projeto de linha de comando standard .NET**

* `App.config`: Ficheiro de configuração de aplicação standard .NET.
* `Packages.config`: Ficheiro de dependência de pacotes Standard NuGet.
* `Program.cs`: Contém o ponto de entrada do programa e o manuseamento de exceções de nível superior.

### <a name="implementing-the-job-splitter"></a>Implementação do divisa de trabalho
Quando abrir o projeto de modelo do Job Manager, o projeto terá o ficheiro JobSplitter.cs aberto por padrão. Pode implementar a lógica de divisão para as tarefas na sua carga de trabalho utilizando o método Split() mostrado abaixo:

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
> A secção anotada no `Split()` método é a única secção do código de modelo do Gestor de Emprego que se destina a modificar adicionando a lógica para dividir os seus trabalhos em diferentes tarefas. Se pretender modificar uma secção diferente do modelo, certifique-se de que está familiarizado com o funcionamento do Batch e experimente algumas das [amostras][github_samples]de código do Lote .
> 
> 

A sua implementação split() tem acesso a:

* Os parâmetros de trabalho, através do `_parameters` campo.
* O objeto CloudJob que representa o trabalho, através do `_job` campo.
* O objeto CloudTask que representa a tarefa de gerente de emprego, através do `_jobManagerTask` campo.

A sua `Split()` implementação não precisa de adicionar tarefas ao trabalho diretamente. Em vez disso, o seu código deve devolver uma sequência de objetos CloudTask, e estes serão adicionados automaticamente ao trabalho pelas classes-quadro que invocam o divisa de trabalho. É comum usar o iterador de C#( `yield return` ) para implementar divisores de emprego, pois isto permite que as tarefas comecem a funcionar o mais rapidamente possível, em vez de esperar que todas as tarefas sejam calculadas.

**Falha no separador de emprego**

Se o seu separador de trabalho encontrar um erro, deve:

* Terminar a sequência utilizando a `yield break` declaração C#, caso em que o gestor de emprego será tratado como bem sucedido;
* Atire uma exceção, caso em que o gestor de emprego será tratado como falhado e poderá ser novamente julgado dependendo da forma como o cliente o configurar).

Em ambos os casos, quaisquer tarefas já devolvidas pelo separador de emprego e adicionadas ao trabalho do Batch serão elegíveis para executar. Se não queres que isto aconteça, então podes:

* Termine o trabalho antes de voltar do separador de emprego
* Formular toda a recolha de tarefas antes de a devolver (isto é, devolva um `ICollection<CloudTask>` ou em vez de implementar o seu `IList<CloudTask>` separador de trabalho utilizando um iterador C#)
* Utilize as dependências de tarefas para que todas as tarefas dependam da conclusão bem sucedida do gestor de emprego

**Retries de gestor de emprego**

Se o gestor de emprego falhar, poderá ser novamente julgado pelo serviço Batch, dependendo das definições de retíria do cliente. Em geral, isto é seguro, porque quando o quadro adiciona tarefas ao trabalho, ignora quaisquer tarefas que já existam. No entanto, se as tarefas de cálculo forem dispendiosas, não poderá incorrer no custo de recalculação das tarefas que já foram adicionadas ao trabalho; inversamente, se a re-execução não for garantida para gerar os mesmos IDs de tarefa, então o comportamento de "ignorar duplicados" não vai funcionar. Nestes casos, deve desenhar o seu separador de trabalho para detetar o trabalho que já foi feito e não repeti-lo, por exemplo, realizando um CloudJob.ListTasks antes de começar a produzir tarefas.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Códigos de saída e exceções no modelo de Gestor de Emprego
Códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e podem ajudar a identificar quaisquer problemas com a execução do programa. O modelo de Gestor de Emprego implementa os códigos de saída e exceções descritas nesta secção.

Uma tarefa de gestor de emprego que é implementada com o modelo de Gestor de Emprego pode devolver três códigos de saída possíveis:

| Código | Descrição |
| --- | --- |
| 0 |O gerente de emprego completou com sucesso. O teu código de divisão de trabalho correu para o fim, e todas as tarefas foram adicionadas ao trabalho. |
| 1 |A tarefa de gerente de emprego falhou com uma exceção numa parte 'esperada' do programa. A exceção foi traduzida para um JobManagerException com informação de diagnóstico e, sempre que possível, sugestões para resolver a falha. |
| 2 |A tarefa de gerente de emprego falhou com uma exceção "inesperada". A exceção foi registada na produção padrão, mas o gestor de emprego não foi capaz de adicionar qualquer informação adicional de diagnóstico ou remediação. |

No caso de falha de tarefa do gestor de emprego, algumas tarefas podem ainda ter sido adicionadas ao serviço antes do erro ocorrer. Estas tarefas serão normais. Consulte "Job Splitter Failure" acima para discutir este caminho de código.

Todas as informações devolvidas por exceções estão escritas em ficheiros stdout.txt e stderr.txt. Para obter mais informações, consulte [Error Handling](error-handling.md).

### <a name="client-considerations"></a>Considerações do cliente
Esta secção descreve alguns requisitos de implementação do cliente ao invocar um gestor de emprego com base neste modelo. Veja [como passar parâmetros e variáveis ambientais a partir do código do cliente](#pass-environment-settings) para detalhes sobre a passagem de parâmetros e configurações de ambiente.

**Credenciais obrigatórias**

Para adicionar tarefas ao trabalho do Azure Batch, a tarefa de gestor de emprego requer o URL e a chave da sua conta Azure Batch. Você deve passar estas variáveis em ambiente chamado YOUR_BATCH_URL e YOUR_BATCH_KEY. Pode defini-las nas definições de ambiente de tarefas do Gestor de Emprego. Por exemplo, num cliente C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciais de armazenamento**

Normalmente, o cliente não precisa de fornecer as credenciais de conta de armazenamento ligadas à tarefa de gestor de emprego, porque (a) a maioria dos gestores de emprego não precisa de aceder explicitamente à conta de armazenamento ligada e (b) a conta de armazenamento ligada é muitas vezes fornecida a todas as tarefas como um ambiente comum para o trabalho. Se não estiver a fornecer a conta de armazenamento ligada através das definições ambientais comuns, e o gestor de emprego necessitar de acesso ao armazenamento ligado, então deve fornecer as credenciais de armazenamento ligadas da seguinte forma:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Definições de tarefa de gestor de emprego**

O cliente deve colocar o gestor de emprego a matar a bandeira do *Presidente DabOnCompletion* em **falso**.

Normalmente é seguro para o cliente definir *runExclusivo* a **falso**.

O cliente deve utilizar o *recursoFiles* ou aplicação A recolha *dePackageReferences* deve ter o gestor de emprego executável (e os seus DLLs necessários) implantados no nó de computação.

Por defeito, o gestor de emprego não será novamente julgado se falhar. Dependendo da lógica do seu gestor de emprego, o cliente pode querer ativar as retrações através de *restrições* / *maxTaskRetryCount*.

**Definições da tarefa**

Se o divisa de trabalho emite tarefas com dependências, o cliente deve definir as utilizações do trabalhoTaskDependencies para verdade.

No modelo de divisão de emprego, é incomum que os clientes desejem adicionar tarefas a empregos para além do que o divisa de emprego cria. Por isso, o cliente deve normalmente definir o trabalho *noAllTasksComplete* para **terminar o trabalho**.

## <a name="task-processor-template"></a>Modelo de processador de tarefas
Um modelo de processador de tarefas ajuda-o a implementar um processador de tarefas que pode executar as seguintes ações:

* Configurar as informações necessárias por cada tarefa do Lote a executar.
* Executar todas as ações necessárias por cada tarefa do Lote.
* Guarde as saídas de tarefa para um armazenamento persistente.

Embora um processador de tarefas não seja necessário para executar tarefas em Batch, a principal vantagem de usar um processador de tarefas é que fornece um invólucro para implementar todas as ações de execução de tarefas num local. Por exemplo, se precisar de executar várias aplicações no contexto de cada tarefa, ou se precisar de copiar dados para armazenamento persistente após completar cada tarefa.

As ações realizadas pelo processador de tarefas podem ser tão simples ou complexas, e quantas ou poucas, como exigido pela sua carga de trabalho. Além disso, ao implementar todas as ações de tarefa num único processador de tarefas, pode facilmente atualizar ou adicionar ações com base em alterações a aplicações ou requisitos de carga de trabalho. No entanto, em alguns casos, um processador de tarefas pode não ser a solução ideal para a sua implementação, uma vez que pode adicionar complexidade desnecessária, por exemplo, quando executa trabalhos que podem ser rapidamente iniciados a partir de uma simples linha de comando.

### <a name="create-a-task-processor-using-the-template"></a>Criar um processador de tarefas usando o modelo
Para adicionar um processador de tarefas à solução que criou anteriormente, siga estes passos:

1. Abra a sua solução existente no Visual Studio.
2. No Solution Explorer, clique com o botão direito na solução, clique em **Adicionar**e, em seguida, clique em **Novo Projeto**.
3. Em **Visual C.** clique em **Cloud**e, em seguida, clique no Processador **de Tarefas Azure Batch**.
4. Digite um nome que descreva a sua aplicação e identifique este projeto como processador de tarefas (por exemplo, "LitwareTaskProcessor").
5. Para criar o projeto, clique **em OK.**
6. Por fim, construa o projeto para forçar o Visual Studio a carregar todos os pacotes NuGet referenciados e verificar se o projeto é válido antes de começar a modificá-lo.

### <a name="task-processor-template-files-and-their-purpose"></a>Ficheiros de modelo de processador de tarefas e a sua finalidade
Quando cria um projeto utilizando o modelo do processador de tarefas, gera três grupos de ficheiros de código:

* O ficheiro principal do programa (Program.cs). Isto contém o ponto de entrada do programa e o manuseamento de exceções de nível superior. Normalmente não devias ter de modificar isto.
* O directório-quadro. Isto contém os ficheiros responsáveis pelo trabalho de 'caldeira' realizado pelo programa de gestor de trabalho – desembalar parâmetros, adicionar tarefas à função Batch, etc. Normalmente não é preciso modificar estes ficheiros.
* O ficheiro do processador de tarefas (TaskProcessor.cs). É aqui que colocará a sua lógica específica de aplicação para executar uma tarefa (normalmente chamando para um executável existente). O código pré e pós-processamento, como o descarregamento de dados adicionais ou o upload de ficheiros de resultados, também vai aqui.

É claro que pode adicionar ficheiros adicionais, conforme necessário para suportar o código do processador de tarefas, dependendo da complexidade da lógica de divisão de trabalho.

O modelo também gera ficheiros de projetos .NET padrão, tais como um ficheiro .csproj, app.config, packages.config, etc.

O resto desta secção descreve os diferentes ficheiros e a sua estrutura de código, e explica o que cada classe faz.

![Explorador de solução de estúdio visual mostrando a solução de modelo de processador de tarefa][solution_explorer02]

**Ficheiros-quadro**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de emprego, tais como detalhes da conta Batch, credenciais de conta de armazenamento ligadas, informações de trabalho e de tarefas e parâmetros de trabalho. Também fornece acesso a variáveis ambientais definidas em lote (ver definições de ambiente para tarefas, na documentação do Lote) através da classe Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Resumos da implementação da classe Configuração, para que possa testar o seu separador de trabalho usando um objeto de configuração falso ou simulado.
* `TaskProcessorException.cs`: Representa um erro que exige que o gestor de emprego seja encerrado. TaskProcessorException é utilizado para embrulhar erros "esperados" onde informações de diagnóstico específicas podem ser fornecidas como parte da rescisão.

**Processador de tarefas**

* `TaskProcessor.cs`: Executa a tarefa. O quadro invoca o método TaskProcessor.Run. Esta é a classe onde irá injetar a lógica específica da aplicação da sua tarefa. Implementar o método Executar para:
  * Parse e valida quaisquer parâmetros de tarefa
  * Componha a linha de comando para qualquer programa externo que queira invocar
  * Registar qualquer informação de diagnóstico que possa necessitar para fins de depuragem
  * Inicie um processo usando esta linha de comando
  * Aguarde a saída do processo.
  * Capture o código de saída do processo para determinar se conseguiu ou falhou
  * Guarde quaisquer ficheiros de saída que pretenda manter para armazenamento persistente

**Ficheiros de projeto de linha de comando standard .NET**

* `App.config`: Ficheiro de configuração de aplicação standard .NET.
* `Packages.config`: Ficheiro de dependência de pacotes Standard NuGet.
* `Program.cs`: Contém o ponto de entrada do programa e o manuseamento de exceções de nível superior.

## <a name="implementing-the-task-processor"></a>Implementação do processador de tarefas
Quando abrir o projeto do modelo do processador de tarefas, o projeto terá o ficheiro TaskProcessor.cs aberto por predefinição. Pode implementar a lógica de execução para as tarefas na sua carga de trabalho utilizando o método Run() apresentado abaixo:

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
> A secção anotada no método Run() é a única secção do código de modelo do processador de tarefas que se destina a modificar adicionando a lógica de execução para as tarefas na sua carga de trabalho. Se pretender modificar uma secção diferente do modelo, por favor, familiarize-se primeiro com o funcionamento do Batch, revendo a documentação do Lote e experimentando algumas das amostras de código batch.
> 
> 

O método Run() é responsável pelo lançamento da linha de comando, iniciando um ou mais processos, aguardando que todo o processo esteja concluído, guardando os resultados e, finalmente, regressando com um código de saída. O método Run() é onde implementa a lógica de processamento para as suas tarefas. O quadro do processador de tarefas invoca o método Run() para si; Não precisa chamá-lo de si mesmo.

A sua implementação de Run() tem acesso a:

* Os parâmetros de tarefa, através do `_parameters` campo.
* O trabalho e a tarefa identificam-se, através dos `_jobId` `_taskId` campos e campos.
* A configuração da tarefa, através do `_configuration` campo.

**Falha de tarefa**

Em caso de avaria, pode sair do método Executar,.) lançando uma exceção, mas isso deixa o manipulador de exceção de nível superior no controlo do código de saída de tarefa. Se precisar de controlar o código de saída para que possa distinguir diferentes tipos de falha, por exemplo, para fins de diagnóstico ou porque alguns modos de falha devem terminar o trabalho e outros não, então deve sair do método Executar,devolvendo um código de saída sem zero. Isto torna-se o código de saída de tarefa.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Códigos de saída e exceções no modelo do processador de tarefas
Códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e podem ajudar a identificar quaisquer problemas com a execução do programa. O modelo do processador de tarefas implementa os códigos de saída e exceções descritos nesta secção.

Uma tarefa do processador de tarefas que é implementada com o modelo do processador de tarefas pode devolver três códigos de saída possíveis:

| Código | Descrição |
| --- | --- |
| [Process.ExitCode][process_exitcode] |O processador de tarefas correu para o final. Note que isto não implica que o programa que invocou tenha sido bem sucedido – apenas que o processador de tarefas o invocou com sucesso e realizou qualquer pós-processamento sem exceções. O significado do código de saída depende do programa invocado – normalmente o código de saída 0 significa que o programa foi bem sucedido e qualquer outro código de saída significa que o programa falhou. |
| 1 |O processador de tarefas falhou com uma exceção numa parte 'esperada' do programa. A exceção foi traduzida para uma `TaskProcessorException` informação de diagnóstico e, sempre que possível, sugestões para resolver a falha. |
| 2 |O processador de tarefas falhou com uma exceção "inesperada". A exceção foi registada na saída padrão, mas o processador de tarefas não foi capaz de adicionar qualquer informação adicional de diagnóstico ou remediação. |

> [!NOTE]
> Se o programa invocar utiliza os códigos de saída 1 e 2 para indicar modos de falha específicos, então a utilização dos códigos de saída 1 e 2 para erros do processador de tarefas é ambíguo. Pode alterar estes códigos de erro do processador de tarefas para códigos de saída distintos, editando os casos de exceção no ficheiro Program.cs.
> 
> 

Todas as informações devolvidas por exceções estão escritas em ficheiros stdout.txt e stderr.txt. Para obter mais informações, consulte Error Handling, na documentação do Lote.

### <a name="client-considerations"></a>Considerações do cliente
**Credenciais de armazenamento**

Se o seu processador de tarefas utilizar o armazenamento de blob Azure para persistir saídas, por exemplo, utilizando a biblioteca de ajuda às convenções de ficheiros, então precisa de ter acesso *às* credenciais de conta de armazenamento em nuvem *ou* a um URL de contentores blob que inclua uma assinatura de acesso partilhado (SAS). O modelo inclui suporte para fornecer credenciais através de variáveis ambientais comuns. O seu cliente pode passar as credenciais de armazenamento da seguinte forma:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A conta de armazenamento está então disponível na classe TaskProcessor através da `_configuration.StorageAccount` propriedade.

Se preferir utilizar um URL de contentor com SAS, também pode passá-lo através de uma definição de ambiente comum de trabalho, mas o modelo do processador de tarefas não inclui atualmente suporte incorporado para isso.

**Configuração de armazenamento**

Recomenda-se que a tarefa do cliente ou do gestor de emprego crie quaisquer recipientes necessários para tarefas antes de adicionar as tarefas ao trabalho. Isto é obrigatório se utilizar um URL de contentor com SAS, uma vez que tal url não inclui permissão para criar o recipiente. Recomenda-se mesmo que passe credenciais de conta de armazenamento, uma vez que poupa todas as tarefas que têm de ligar para CloudBlobContainer.CreateIfNotExistsAsync no recipiente.

## <a name="pass-parameters-and-environment-variables"></a>Passar parâmetros e variáveis ambientais
### <a name="pass-environment-settings"></a>Ajustes de ambiente de passe
Um cliente pode passar informações para a tarefa de gestor de emprego sob a forma de configurações ambientais. Estas informações podem então ser utilizadas pela tarefa do gestor de emprego ao gerar as tarefas do processador de tarefas que serão executadas como parte do trabalho de computação. Exemplos da informação que pode passar como configurações ambientais são:

* Nome da conta de armazenamento e chaves de conta
* URL de conta de lote
* Chave da conta do lote

O serviço Batch tem um mecanismo simples para passar as definições de ambiente para uma tarefa de gestor de emprego, usando a `EnvironmentSettings` propriedade em [Microsoft.Azure.Batch. JobManagerTask.][net_jobmanagertask]

Por exemplo, para obter a `BatchClient` instância de uma conta Batch, pode passar como variáveis ambientais a partir do código do cliente o URL e credenciais-chave partilhadas para a conta Batch. Da mesma forma, para aceder à conta de armazenamento que está ligada à conta Batch, pode passar o nome da conta de armazenamento e a chave da conta de armazenamento como variáveis ambientais.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passe parâmetros para o modelo de Gestor de Emprego
Em muitos casos, é útil passar parâmetros por trabalho para a tarefa de gerente de emprego, seja para controlar o processo de divisão de trabalho ou para configurar as tarefas para o trabalho. Pode fazê-lo enviando um ficheiro JSON nomeado parameters.jscomo um ficheiro de recursos para a tarefa de gestor de emprego. Os parâmetros podem então ficar disponíveis no `JobSplitter._parameters` campo no modelo de Gestor de Emprego.

> [!NOTE]
> O manipulador de parâmetros incorporado suporta apenas dicionários de cordas a cordas. Se quiser passar valores complexos de JSON como valores de parâmetro, terá de passar estes como cordas e analisá-los no separador de trabalho, ou modificar o método do `Configuration.GetJobParameters` quadro.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Passe parâmetros para o modelo do processador de tarefas
Também pode passar parâmetros para tarefas individuais implementadas usando o modelo do Processador de Tarefas. Assim como com o modelo de gestor de emprego, o modelo de processador de tarefas procura um ficheiro de recursos chamado

parameters.jsligado, e se o encontrou carrega-o como dicionário de parâmetros. Existem algumas opções para como passar parâmetros para as tarefas do processador de tarefas:

* Reutilizar os parâmetros de trabalho JSON. Isto funciona bem se os únicos parâmetros forem de largura de trabalho (por exemplo, uma altura e largura renderizadas). Para isso, ao criar um CloudTask no separador de trabalho, adicione uma referência ao parameters.jsno objeto de ficheiro de recursos dos Recursos da tarefa do gestor de emprego ( `JobSplitter._jobManagerTask.ResourceFiles` ) à coleção De Recursos da CloudTask.
* Gerencie e carrega um parameters.jsespecífico de tarefas no documento como parte da execução do splitter de trabalho, e faça referência a essa bolha na recolha de ficheiros de recursos da tarefa. Isto é necessário se diferentes tarefas tiverem parâmetros diferentes. Um exemplo pode ser um cenário de renderização 3D onde o índice de quadro é passado para a tarefa como um parâmetro.

> [!NOTE]
> O manipulador de parâmetros incorporado suporta apenas dicionários de cordas a cordas. Se pretender passar valores complexos de JSON como valores de parâmetro, terá de passar estes como cordas e analisá-los no processador de tarefas ou modificar o método do `Configuration.GetTaskParameters` quadro.
> 
> 

## <a name="next-steps"></a>Próximos passos
### <a name="persist-job-and-task-output-to-azure-storage"></a>Persistir de trabalho e saída de tarefa para o Azure Storage
Outra ferramenta útil no desenvolvimento de soluções de Lote é [as Convenções de Ficheiros Azure Batch][nuget_package]. Utilize esta biblioteca de classe .NET (atualmente em pré-visualização) nas suas aplicações Batch .NET para armazenar e recuperar facilmente saídas de tarefa de e para o Azure Storage. [Persist Azure Batch trabalho e saída de tarefa](batch-task-output.md) contém uma discussão completa sobre a biblioteca e sua utilização.


[net_jobmanagertask]: /dotnet/api/microsoft.azure.batch.jobmanagertask
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: /dotnet/api/system.diagnostics.process.exitcode
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://github.com/Azure/batch-extension-templates
[vs_find_use_ext]: /visualstudio/ide/finding-and-using-visual-studio-extensions

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png

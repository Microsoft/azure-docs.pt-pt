---
title: Construir soluções com modelos de Estúdio Visual - Lote Azure [ Lote ] Microsoft Docs
description: Saiba como os modelos do projeto Visual Studio podem ajudá-lo a implementar e executar as suas cargas de trabalho intensivas em Computação no Lote Azure.
ms.topic: article
ms.date: 02/27/2017
ms.custom: seodec18
ms.openlocfilehash: 8e8d5be4a9f0fb5482ba6c86a8766a25e5713c09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117527"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Use modelos de projeto do Estúdio Visual para lançar soluções de lote

Os modelos do Estúdio Visual do Gestor **de Emprego** e do Processador de **Tarefas** para o Batch fornecem código para ajudá-lo a implementar e executar as suas cargas de trabalho intensivas em Computação no Lote com o menor esforço. Este documento descreve estes modelos e fornece orientações para como usá-los.

> [!IMPORTANT]
> Este artigo discute apenas informações aplicáveis a estes dois modelos, e assume que está familiarizado com o serviço De lote e conceitos-chave relacionados com ele: piscinas, nós de computação, empregos e tarefas, tarefas de gestor de emprego, variáveis ambientais e outras informações relevantes. Pode encontrar mais informações no [Basics of Azure Batch](batch-technical-overview.md) e [No lote para desenvolvedores](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Descrição geral de alto nível
Os modelos do Gestor de Emprego e do Processador de Tarefas podem ser usados para criar dois componentes úteis:

* Uma tarefa de gestor de emprego que implementa um divisor de emprego que pode quebrar um emprego em múltiplas tarefas que podem funcionar de forma independente, em paralelo.
* Um processador de tarefas que pode ser utilizado para realizar o pré-processamento e o pós-processamento em torno de uma linha de comando de aplicação.

Por exemplo, num cenário de renderização de filmes, o splitter de trabalho transformaria um único trabalho cinematográfico em centenas ou milhares de tarefas separadas que processassem quadros individuais separadamente. Consequentemente, o processador de tarefas invocaria a aplicação de renderização e todos os processos dependentes necessários para renderizar cada quadro, bem como realizar quaisquer ações adicionais (por exemplo, copiar a moldura renderizada para um local de armazenamento).

> [!NOTE]
> Os modelos do Job Manager e do Processador de Tarefas são independentes uns dos outros, para que possa optar por utilizar ambos, ou apenas um deles, dependendo dos requisitos do seu trabalho de computação e das suas preferências.
> 
> 

Como mostrado no diagrama abaixo, um trabalho de cálculo que usa estes modelos passará por três fases:

1. O código do cliente (por exemplo, aplicação, serviço web, etc.) submete um trabalho ao serviço Batch no Azure, especificando como tarefa de gestor de emprego o programa de gestor de emprego.
2. O serviço Batch executa a tarefa de gestor de trabalho num nó de cálculo e o separador de trabalho lança o número especificado de tarefas do processador de tarefas, em todos os nós de computação que necessário, com base nos parâmetros e especificações do código de divisão de trabalho.
3. As tarefas do processador de tarefas funcionam de forma independente, paralela, para processar os dados de entrada e gerar os dados de saída.

![Diagrama mostrando como o código do cliente interage com o serviço Batch][diagram01]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar os modelos de Lote, necessitará do seguinte:

* Um computador com Visual Studio 2015 instalado. Os modelos de lote são atualmente suportados apenas para Visual Studio 2015.
* Os modelos de Lote, que estão disponíveis na [Galeria de Estúdio Visual][vs_gallery] como extensões do Estúdio Visual. Há duas maneiras de obter os modelos:
  
  * Instale os modelos utilizando a caixa de diálogo de **extensões e atualizações** no Estúdio Visual (para obter mais informações, consulte [encontrar e utilizar extensões][vs_find_use_ext]de estúdio visual). Na caixa de diálogo **de extensões e atualizações,** procure e baixe as duas seguintes extensões:
    
    * Gestor de emprego de lote azure com splitter de trabalho
    * Processador de tarefas de lote azure
  * Descarregue os modelos da galeria online para Visual Studio: [Microsoft Azure Batch Project Templates][vs_gallery_templates]
* Se planeia utilizar a funcionalidade Pacotes de [Aplicação](batch-application-packages.md) para implementar o gestor de trabalho e o processador de tarefas nos nós de computação do Lote, precisa de ligar uma conta de armazenamento à sua conta Batch.

## <a name="preparation"></a>Preparação
Recomendamos a criação de uma solução que possa conter o seu gestor de emprego, bem como o seu processador de tarefas, pois isso pode facilitar a partilha de código entre o seu gestor de emprego e os programas de processadores de tarefas. Para criar esta solução, siga estes passos:

1. Open Visual Studio e selecione **File** > **New** > **Project**.
2. Em **Modelos,** expanda **Outros Tipos de Projeto,** clique em **Soluções de Estúdio Visual,** e, em seguida, selecione **Solução Em Branco**.
3. Digite um nome que descreva a sua aplicação e o propósito desta solução (por exemplo, "LitwareBatchTaskPrograms").
4. Para criar a nova solução, clique em **OK**.

## <a name="job-manager-template"></a>Modelo de Gestor de Emprego
O modelo do Gestor de Emprego ajuda-o a implementar uma tarefa de gestor de emprego que pode executar as seguintes ações:

* Divida um emprego em várias tarefas.
* Submeta essas tarefas para executar em Batch.

> [!NOTE]
> Para obter mais informações sobre as tarefas do gestor de emprego, consulte a visão geral da [funcionalidade do Lote para os desenvolvedores.](batch-api-basics.md#job-manager-task)
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Criar um Gestor de Emprego usando o modelo
Para adicionar um gestor de emprego à solução que criou anteriormente, siga estes passos:

1. Abra a sua solução existente no Estúdio Visual.
2. No Solution Explorer, clique na solução, clique em **Adicionar** > **Novo Projeto**.
3. Em **Visual C#** clique em **Cloud,** e clique em **Azure Batch Job Manager com Job Splitter**.
4. Digite um nome que descreva a sua aplicação e identifique este projeto como gerente de emprego (por exemplo, "LitwareJobManager").
5. Para criar o projeto, clique em **OK**.
6. Por fim, construa o projeto para forçar o Visual Studio a carregar todos os pacotes NuGet referenciados e a verificar se o projeto é válido antes de começar a modificá-lo.

### <a name="job-manager-template-files-and-their-purpose"></a>Ficheiros de modelo de gestor de emprego e seu propósito
Quando cria um projeto utilizando o modelo do Job Manager, gera três grupos de ficheiros de código:

* O ficheiro principal do programa (Program.cs). Isto contém o ponto de entrada do programa e o manuseamento de exceção de nível superior. Normalmente não precisas de modificar isto.
* O diretório-quadro. Isto contém os ficheiros responsáveis pelo trabalho de 'placa de caldeira' realizado pelo programa de gestor de trabalho – desembalar parâmetros, adicionando tarefas ao trabalho do Lote, etc. Normalmente não precisas de modificar estes ficheiros.
* O ficheiro splitter de trabalho (JobSplitter.cs). É aqui que colocará a sua lógica específica de aplicação para dividir um trabalho em tarefas.

Claro que pode adicionar ficheiros adicionais conforme necessário para apoiar o seu código de divisão de emprego, dependendo da complexidade da lógica de divisão de trabalho.

O modelo também gera ficheiros de projeto sintetizados .NET, tais como um ficheiro .csproj, app.config, packages.config, etc.

O resto desta secção descreve os diferentes ficheiros e a sua estrutura de código, e explica o que cada classe faz.

![Visual Studio Solution Explorer mostrando a solução de modelo do Job Manager][solution_explorer01]

**Ficheiros-quadro**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de trabalho, tais como dados da conta do Lote, credenciais de conta de armazenamento ligadas, informações sobre trabalho e tarefas e parâmetros de trabalho. Também fornece acesso a variáveis ambientais definidas por Lote (ver configurações ambientais para tarefas, na documentação do Lote) através da classe Configuração.EnvironmentVariable.
* `IConfiguration.cs`: Resumos da implementação da classe Configuração, de modo a que possa testar o seu separador de trabalho utilizando um objeto de configuração falso ou simulado.
* `JobManager.cs`: Orquestra os componentes do programa de gestor de emprego. É responsável pela inicialização do separador de trabalho, invocando o divisor de trabalho, e despachando as tarefas devolvidas pelo separador de trabalho para o apresentador de tarefas.
* `JobManagerException.cs`: Representa um erro que obriga o gestor de emprego a rescindir. JobManagerException é usado para embrulhar erros 'esperados' onde informações específicas de diagnóstico podem ser fornecidas como parte da rescisão.
* `TaskSubmitter.cs`: Esta classe é responsável pela adição de tarefas devolvidas pelo separador de trabalho ao trabalho do Lote. A classe JobManager agrega a sequência de tarefas em lotes para uma adição eficiente mas oportuna ao trabalho, em seguida, chama TaskSubmitter.SubmitTasks em um fio de fundo para cada lote.

**Splitter de Emprego**

`JobSplitter.cs`: Esta classe contém uma lógica específica para a divisão do trabalho em tarefas. O quadro invoca o método JobSplitter.Split para obter uma sequência de tarefas, que adiciona ao trabalho à medida que o método as devolve. Esta é a aula onde vais injetar a lógica do teu trabalho. Implemente o método Split para devolver uma sequência de casos cloudTask que representam as tarefas em que pretende dividir o trabalho.

**Ficheiros de projeto de linha de comando Standard .NET**

* `App.config`: Ficheiro de configuração de aplicação Standard .NET.
* `Packages.config`: Ficheiro de dependência de pacotes NuGet Standard.
* `Program.cs`: Contém o ponto de entrada do programa e o manuseamento de exceção de nível superior.

### <a name="implementing-the-job-splitter"></a>Implementação do splitter de trabalho
Quando abrir o projeto de modelo do Job Manager, o projeto terá o ficheiro JobSplitter.cs aberto por padrão. Pode implementar a lógica dividida para as tarefas na sua carga de trabalho utilizando o método Split() abaixo:

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
> A secção anotada `Split()` no método é a única secção do código de modelo do Job Manager que se destina a modificar, adicionando a lógica para dividir os seus empregos em diferentes tarefas. Se quiser modificar uma secção diferente do modelo, certifique-se de que está familiarizado com o funcionamento do Lote e experimente algumas das [amostras][github_samples]de código do Lote .
> 
> 

A sua implementação split tem acesso a:

* Os parâmetros de `_parameters` trabalho, através do campo.
* O objeto CloudJob que representa `_job` o trabalho, através do campo.
* O objeto CloudTask que representa a `_jobManagerTask` tarefa do gestor de emprego, através do campo.

A `Split()` sua implementação não necessita de adicionar tarefas diretamente ao trabalho. Em vez disso, o seu código deve devolver uma sequência de objetos CloudTask, e estes serão adicionados automaticamente ao trabalho pelas classes-quadro que invocam o divisor de trabalho. É comum usar o iterator de`yield return`C#para implementar divisores de trabalho, pois permite que as tarefas comecem a funcionar o mais rapidamente possível, em vez de esperar que todas as tarefas sejam calculadas.

**Falha de splitter de emprego**

Se o seu separador de trabalho encontrar um erro, deve:

* Terminar a sequência utilizando `yield break` a declaração De C#, caso em que o gestor de emprego será tratado como bem sucedido; ou
* Atire uma exceção, caso em que o gestor de emprego será tratado como falhado e poderá ser novamente julgado dependendo da forma como o cliente o configurou).

Em ambos os casos, quaisquer tarefas já devolvidas pelo separador de trabalho e adicionadas ao trabalho do Batch serão elegíveis para executar. Se não queres que isto aconteça, então podes:

* Desabote o emprego antes de voltar do separador de trabalho
* Formule toda a coleção de tarefas antes de a devolver (isto é, devolva um `ICollection<CloudTask>` ou `IList<CloudTask>` em vez de implementar o seu separador de trabalho utilizando um iterator C#)
* Use dependências de tarefas para fazer todas as tarefas dependerem da conclusão bem-sucedida do gestor de emprego

**Retenta gerente de emprego**

Se o gestor de emprego falhar, poderá ser novamente julgado pelo serviço Batch, dependendo das definições de retry do cliente. Em geral, isto é seguro, porque quando o quadro adiciona tarefas ao trabalho, ignora quaisquer tarefas que já existam. No entanto, se o cálculo das tarefas for dispendioso, poderá não querer incorrer no custo do recálculo das tarefas que já foram adicionadas ao trabalho; inversamente, se a recandidatura não for garantida para gerar as mesmas identificações de tarefa, então o comportamento de "ignorar duplicados" não vai funcionar. Nestes casos, deve projetar o seu splitter de trabalho para detetar o trabalho que já foi feito e não repeti-lo, por exemplo, executando um CloudJob.ListTasks antes de começar a produzir tarefas.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Códigos de saída e exceções no modelo do Gestor de Emprego
Os códigos de saída e as exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e podem ajudar a identificar quaisquer problemas com a execução do programa. O modelo do Gestor de Emprego implementa os códigos de saída e exceções descritos nesta secção.

Uma tarefa de gestor de emprego que é implementada com o modelo de Gestor de Emprego pode devolver três possíveis códigos de saída:

| Código | Descrição |
| --- | --- |
| 0 |O gerente de emprego completou com sucesso. O seu código de divisão de emprego correu para a conclusão, e todas as tarefas foram adicionadas ao trabalho. |
| 1 |A tarefa de gestor de emprego falhou com uma exceção numa parte "esperada" do programa. A exceção foi traduzida para uma JobManagerException com informações de diagnóstico e, sempre que possível, sugestões para resolver a falha. |
| 2 |A tarefa de gestor de emprego falhou com uma exceção "inesperada". A exceção foi registada à produção normal, mas o gestor de emprego não pôde adicionar qualquer informação adicional de diagnóstico ou remediação. |

No caso de falha na tarefa do gestor de emprego, algumas tarefas podem ainda ter sido adicionadas ao serviço antes do erro ocorrer. Estas tarefas serão executadas normalmente. Consulte "Job Splitter Failure" acima para discutir este caminho de código.

Toda a informação devolvida por exceções está escrita em ficheiros stdout.txt e stderr.txt. Para mais informações, consulte [o Error Handling](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considerações de clientes
Esta secção descreve alguns requisitos de implementação do cliente ao invocar um gestor de emprego com base neste modelo. Consulte [como passar parâmetros e variáveis ambientais do código do cliente](#pass-environment-settings) para obter detalhes sobre a passagem de parâmetros e configurações ambientais.

**Credenciais obrigatórias**

Para adicionar tarefas ao trabalho do Lote Azure, a tarefa do gestor de trabalho requer o URL e a chave da conta do Lote Azure. Deve passá-las em variáveis ambientais chamadas YOUR_BATCH_URL e YOUR_BATCH_KEY. Pode defini-las nas definições de ambiente de tarefas do Gestor de Emprego. Por exemplo, num cliente C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciais de armazenamento**

Normalmente, o cliente não precisa de fornecer as credenciais de conta de armazenamento ligadas à tarefa do gestor de emprego porque (a) a maioria dos gestores de emprego não precisa de aceder explicitamente à conta de armazenamento ligada e (b) a conta de armazenamento ligada é muitas vezes fornecida a todas as tarefas como um ambiente comum para o trabalho. Se não estiver a fornecer a conta de armazenamento ligada através das configurações do ambiente comum, e o gestor de emprego exigir acesso ao armazenamento ligado, então deve fornecer as credenciais de armazenamento ligadas da seguinte forma:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Definições de tarefa sintetidade de gestor de emprego**

O cliente deve definir a bandeira *jobOnCompletion* do gestor de emprego para **falsas**.

Normalmente é seguro para o cliente definir *runExclusive* a **falso**.

O cliente deve utilizar os *recursosFiles* ou *aplicaçãoRecolha De Referências* para que o gestor de emprego seja executável (e os seus DLLs necessários) implantados no nó computacional.

Por defeito, o gestor de emprego não será novamente julgado se falhar. Dependendo da lógica do gestor de emprego, o cliente pode querer permitir repetições através de *restrições*/*maxTaskRetryCount*.

**Definições da tarefa**

Se o divisor de trabalho emite tarefas com dependências, o cliente deve definir as utilizações do trabalhoTaskDependencies para verdade.

No modelo de divisão de emprego, é incomum que os clientes desejem adicionar tarefas a empregos para além do que o divisor de emprego cria. Por conseguinte, o cliente deve normalmente definir o trabalho *no AllTasksComplete* para terminar o **trabalho**.

## <a name="task-processor-template"></a>Modelo de processador de tarefas
Um modelo de processador de tarefas ajuda-o a implementar um processador de tarefas que possa executar as seguintes ações:

* Configurar as informações necessárias por cada tarefa do Lote para executar.
* Executar todas as ações necessárias por cada tarefa do Lote.
* Guarde as saídas de tarefa para armazenamento persistente.

Embora um processador de tarefas não seja necessário executar tarefas no Batch, a principal vantagem de usar um processador de tarefas é que fornece um invólucro para implementar todas as ações de execução de tarefas num só local. Por exemplo, se precisar de executar várias aplicações no contexto de cada tarefa, ou se precisar de copiar dados para armazenamento persistente após completar cada tarefa.

As ações realizadas pelo processador de tarefas podem ser tão simples ou complexas, e quantas ou poucas, conforme exigido pela sua carga de trabalho. Além disso, ao implementar todas as ações de tarefa num único processador de tarefas, pode atualizar ou adicionar prontamente ações com base em alterações a aplicações ou requisitos de carga de trabalho. No entanto, em alguns casos, um processador de tarefas pode não ser a solução ideal para a sua implementação, pois pode adicionar complexidade desnecessária, por exemplo, quando executa trabalhos que podem ser rapidamente iniciados a partir de uma linha de comando simples.

### <a name="create-a-task-processor-using-the-template"></a>Criar um processador de tarefas usando o modelo
Para adicionar um processador de tarefas à solução que criou anteriormente, siga estes passos:

1. Abra a sua solução existente no Estúdio Visual.
2. No Solution Explorer, clique na solução, clique em **Adicionar**, e depois clique em **Novo Projeto**.
3. Em **C# visual,** clique em **Cloud,** e, em seguida, clique no processador de **tarefas do lote Azure**.
4. Digite um nome que descreva a sua aplicação e identifique este projeto como processador de tarefas (por exemplo, "Processador de Tarefas Litware").
5. Para criar o projeto, clique em **OK**.
6. Por fim, construa o projeto para forçar o Visual Studio a carregar todos os pacotes NuGet referenciados e a verificar se o projeto é válido antes de começar a modificá-lo.

### <a name="task-processor-template-files-and-their-purpose"></a>Ficheiros de modelo de processador de tarefas e o seu propósito
Quando cria um projeto utilizando o modelo do processador de tarefas, gera três grupos de ficheiros de código:

* O ficheiro principal do programa (Program.cs). Isto contém o ponto de entrada do programa e o manuseamento de exceção de nível superior. Normalmente não precisas de modificar isto.
* O diretório-quadro. Isto contém os ficheiros responsáveis pelo trabalho de 'placa de caldeira' realizado pelo programa de gestor de trabalho – desembalar parâmetros, adicionando tarefas ao trabalho do Lote, etc. Normalmente não precisas de modificar estes ficheiros.
* O ficheiro do processador de tarefas (TaskProcessor.cs). É aqui que colocará a sua lógica específica de aplicação para executar uma tarefa (normalmente chamando para um executável existente). O código de pré-e pós-processamento, como o descarregamento de dados adicionais ou o upload de ficheiros de resultados, também aqui.

Claro que pode adicionar ficheiros adicionais conforme necessário para suportar o seu código de processador de tarefas, dependendo da complexidade da lógica de divisão de trabalho.

O modelo também gera ficheiros de projeto sintetizados .NET, tais como um ficheiro .csproj, app.config, packages.config, etc.

O resto desta secção descreve os diferentes ficheiros e a sua estrutura de código, e explica o que cada classe faz.

![Visual Studio Solution Explorer mostrando a solução de modelo de processador de tarefas][solution_explorer02]

**Ficheiros-quadro**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de trabalho, tais como dados da conta do Lote, credenciais de conta de armazenamento ligadas, informações sobre trabalho e tarefas e parâmetros de trabalho. Também fornece acesso a variáveis ambientais definidas por Lote (ver configurações ambientais para tarefas, na documentação do Lote) através da classe Configuração.EnvironmentVariable.
* `IConfiguration.cs`: Resumos da implementação da classe Configuração, de modo a que possa testar o seu separador de trabalho utilizando um objeto de configuração falso ou simulado.
* `TaskProcessorException.cs`: Representa um erro que obriga o gestor de emprego a rescindir. TaskProcessorException é usado para embrulhar erros 'esperados' onde informações específicas de diagnóstico podem ser fornecidas como parte da rescisão.

**Processador de Tarefas**

* `TaskProcessor.cs`: Executa a tarefa. A estrutura invoca o método TaskProcessor.Executar. Esta é a classe onde injetará a lógica específica da aplicação da sua tarefa. Implementar o método Executar para:
  * Parse e validar quaisquer parâmetros de tarefa
  * Componha a linha de comando para qualquer programa externo que queira invocar
  * Faça logação em qualquer informação de diagnóstico que possa necessitar para fins de depuração
  * Inicie um processo usando esta linha de comando
  * Aguarde a saída do processo
  * Capture o código de saída do processo para determinar se foi bem sucedido ou falhado
  * Guarde quaisquer ficheiros de saída que pretenda manter para armazenamento persistente

**Ficheiros de projeto de linha de comando Standard .NET**

* `App.config`: Ficheiro de configuração de aplicação Standard .NET.
* `Packages.config`: Ficheiro de dependência de pacotes NuGet Standard.
* `Program.cs`: Contém o ponto de entrada do programa e o manuseamento de exceção de nível superior.

## <a name="implementing-the-task-processor"></a>Implementação do processador de tarefas
Quando abrir o projeto do modelo do Processador de Tarefas, o projeto terá o ficheiro TaskProcessor.cs aberto por defeito. Pode implementar a lógica de execução para as tarefas na sua carga de trabalho utilizando o método Run() abaixo indicado:

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
> A secção anotada no método Executar é a única secção do código do modelo do processador de tarefas que se destina a modificar, adicionando a lógica de execução para as tarefas na sua carga de trabalho. Se quiser modificar uma secção diferente do modelo, por favor, familiarize-se primeiro com o funcionamento do Batch, revendo a documentação do Lote e experimentando algumas das amostras de código do Lote.
> 
> 

O método Run é responsável pelo lançamento da linha de comando, iniciando um ou mais processos, aguardando que todo o processo esteja concluído, economizando os resultados e, finalmente, regressando com um código de saída. O método Run é onde implementa a lógica de processamento para as suas tarefas. O quadro do processador de tarefas invoca o método Run() para si; Não precisa chamá-lo por si mesmo.

A sua implementação tem acesso a:

* Os parâmetros de `_parameters` tarefa, através do campo.
* O trabalho e as tarefas ids, através dos `_jobId` campos e campos. `_taskId`
* A configuração da `_configuration` tarefa, através do campo.

**Falha de tarefa**

Em caso de falha, pode sair do método Run() lançando uma exceção, mas isso deixa o manipulador de exceção de nível superior no controlo do código de saída de tarefas. Se precisar de controlar o código de saída para que possa distinguir diferentes tipos de falhas, por exemplo para fins de diagnóstico ou porque alguns modos de falha devem terminar o trabalho e outros não devem, então deve sair do método Run() devolvendo um código de saída não zero. Isto torna-se o código de saída de tarefas.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Códigos de saída e exceções no modelo do Processador de Tarefas
Os códigos de saída e as exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e podem ajudar a identificar quaisquer problemas com a execução do programa. O modelo do Processador de Tarefas implementa os códigos de saída e exceções descritos nesta secção.

Uma tarefa de processador de tarefas que é implementada com o modelo do Processador de Tarefas pode devolver três códigos de saída possíveis:

| Código | Descrição |
| --- | --- |
| [Processo.Código de saída][process_exitcode] |O processador de tarefas correu para a conclusão. Note que isso não implica que o programa que invocou tenha sido bem sucedido – apenas que o processador de tarefas o invocou com sucesso e realizou qualquer pós-processamento sem exceções. O significado do código de saída depende do programa invocado – normalmente sair do código 0 significa que o programa foi bem sucedido e qualquer outro código de saída significa que o programa falhou. |
| 1 |O processador de tarefas falhou com uma exceção numa parte 'esperada' do programa. A exceção foi `TaskProcessorException` traduzida para um com informação de diagnóstico e, sempre que possível, sugestões para resolver a falha. |
| 2 |O processador de tarefas falhou com uma exceção "inesperada". A exceção foi registada à saída padrão, mas o processador de tarefas não foi capaz de adicionar qualquer informação adicional de diagnóstico ou reparação. |

> [!NOTE]
> Se o programa invocado utilizar os códigos de saída 1 e 2 para indicar modos de falha específicos, então a utilização dos códigos de saída 1 e 2 para erros no processador de tarefas é ambígua. Pode alterar estes códigos de erro do processador de tarefas para códigos de saída distintos, editando os casos de exceção no ficheiro Program.cs.
> 
> 

Toda a informação devolvida por exceções está escrita em ficheiros stdout.txt e stderr.txt. Para mais informações, consulte o Error Handling, na documentação do Lote.

### <a name="client-considerations"></a>Considerações de clientes
**Credenciais de armazenamento**

Se o seu processador de tarefas utilizar o armazenamento de blob Azure para persistir nas saídas, por exemplo, utilizando a biblioteca de ajudantes de convenções de ficheiros, então precisa de acesso *às* credenciais da conta de armazenamento em nuvem *ou* a um URL de recipiente blob que inclua uma assinatura de acesso partilhado (SAS). O modelo inclui suporte para fornecer credenciais através de variáveis ambientais comuns. O seu cliente pode passar as credenciais de armazenamento da seguinte forma:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A conta de armazenamento está então `_configuration.StorageAccount` disponível na classe TaskProcessor através da propriedade.

Se preferir utilizar um URL de recipiente com SAS, também pode passá-lo através de uma configuração ambiente comum de trabalho, mas o modelo do processador de tarefas não inclui atualmente suporte incorporado para isso.

**Configuração de armazenamento**

Recomenda-se que a tarefa do cliente ou do gestor de emprego crie quaisquer recipientes necessários pelas tarefas antes de adicionar as tarefas ao trabalho. Isto é obrigatório se utilizar um URL de recipiente com SAS, uma vez que um URL não inclui permissão para criar o recipiente. Recomenda-se mesmo que passe credenciais de conta de armazenamento, uma vez que guarda todas as tarefas que têm de chamar CloudBlobContainer.CreateIfNotExistsAsync no recipiente.

## <a name="pass-parameters-and-environment-variables"></a>Passar parâmetros e variáveis ambientais
### <a name="pass-environment-settings"></a>Passe as configurações ambientais
Um cliente pode passar informações para a tarefa do gestor de emprego sob a forma de configurações ambientais. Estas informações podem então ser utilizadas pela tarefa do gestor de trabalho ao gerar as tarefas do processador de tarefas que serão executadas como parte do trabalho de computação. Exemplos da informação que pode passar como configurações ambientais são:

* Nome da conta de armazenamento e chaves de conta
* URL de conta de lote
* Chave da conta do lote

O serviço Batch tem um mecanismo simples para passar as `EnvironmentSettings` definições ambientais para uma tarefa de gestor de emprego, utilizando a propriedade em [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Por exemplo, para `BatchClient` obter a instância de uma conta Batch, você pode passar como variáveis ambientais do código do cliente o URL e credenciais-chave partilhadas para a conta Batch. Da mesma forma, para aceder à conta de armazenamento que está ligada à conta 'Lote', pode passar o nome da conta de armazenamento e a chave da conta de armazenamento como variáveis ambientais.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passar parâmetros para o modelo de Gestor de Emprego
Em muitos casos, é útil passar parâmetros por trabalho para a tarefa de gestor de emprego, quer para controlar o processo de divisão de trabalho, quer para configurar as tarefas para o trabalho. Pode fazê-lo enviando um ficheiro JSON chamado parâmetros.json como um ficheiro de recursos para a tarefa do gestor de emprego. Os parâmetros podem então `JobSplitter._parameters` ficar disponíveis no campo no modelo do Gestor de Emprego.

> [!NOTE]
> O manipulador de parâmetros incorporado suporta apenas dicionários de cordas a cordas. Se quiser passar valores complexos de JSON como valores de parâmetros, terá de os passar como cordas `Configuration.GetJobParameters` e analisá-los no separador de trabalho, ou modificar o método do quadro.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Passar parâmetros para o modelo do processador de tarefas
Também pode passar parâmetros para tarefas individuais implementadas utilizando o modelo do Processador de Tarefas. Tal como com o modelo de gestor de emprego, o modelo do processador de tarefas procura um ficheiro de recursos nomeado

parâmetros.json, e se encontrá-lo carrega-lo como o dicionário de parâmetros. Existem algumas opções para como passar parâmetros para as tarefas do processador de tarefas:

* Reutilizar os parâmetros de trabalho JSON. Isto funciona bem se os únicos parâmetros forem os de toda a obra (por exemplo, uma altura e largura de renderização). Para tal, ao criar uma CloudTask no separador de trabalho, adicione uma referência ao objeto de ficheiro de`JobSplitter._jobManagerTask.ResourceFiles`recursos dos parâmetros.json dos Recursos Ficheiros de Recursos () do gestor de trabalho para a coleção de Recursos Ficheiros da CloudTask.
* Gere e carregue um documento de parâmetros específicos da tarefa.json como parte da execução de splitter de trabalho, e referência que blob na coleção de ficheiros de recursos da tarefa. Isto é necessário se diferentes tarefas tiverem parâmetros diferentes. Um exemplo pode ser um cenário de renderização 3D onde o índice de quadros é passado para a tarefa como parâmetro.

> [!NOTE]
> O manipulador de parâmetros incorporado suporta apenas dicionários de cordas a cordas. Se quiser passar valores complexos de JSON como valores de parâmetros, terá de os passar como `Configuration.GetTaskParameters` cordas e analisá-los no processador de tarefas, ou modificar o método do quadro.
> 
> 

## <a name="next-steps"></a>Passos seguintes
### <a name="persist-job-and-task-output-to-azure-storage"></a>Persistir na produção de trabalho e tarefa sintetizar para o Armazenamento Azure
Outra ferramenta útil no desenvolvimento da solução de lote é as Convenções de [Ficheiros de Lote Azure][nuget_package]. Utilize esta biblioteca de classe .NET (atualmente em pré-visualização) nas suas aplicações Batch .NET para armazenar e recuperar facilmente as saídas de tarefas de e para o Armazenamento Azure. O trabalho e a saída de tarefas do [Lote Azure Persist](batch-task-output.md) contêm uma discussão completa sobre a biblioteca e a sua utilização.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://github.com/Azure/batch-extension-templates
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png

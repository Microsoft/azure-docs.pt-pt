---
title: Crie soluções com modelos do Visual Studio - Azure Batch | Documentos da Microsoft
description: Saiba como modelos de projeto do Visual Studio podem ajudá-lo a implementar e executar cargas de trabalho intensivas de computação no Azure Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
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
ms.openlocfilehash: 085bfa582b676f34a02e4c1c5ae7e69c49e5cb4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550099"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Utilizar modelos de projeto do Visual Studio para o pontapé inicial em soluções do Batch

O **Gestor de tarefas** e **modelos de tarefas processador Visual Studio** para o Batch fornecem código para ajudá-lo a implementar e executar cargas de trabalho intensivas de computação no Batch com o mínimo de esforço. Este documento descreve estes modelos e fornece orientações sobre como usá-los.

> [!IMPORTANT]
> Este artigo aborda apenas as informações aplicáveis a estes dois modelos e parte do princípio de que está familiarizado com o serviço de Batch e os principais conceitos relacionados a ele: conjuntos, nós de computação, trabalhos e tarefas, tarefas do Gestor, variáveis de ambiente e outros relevantes informações. Pode encontrar mais informações nos [Noções básicas do Azure Batch](batch-technical-overview.md) e [descrição geral da funcionalidade Batch para programadores](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Descrição geral de alto nível
Os modelos do Gestor de tarefas e o processador de tarefa podem ser usados para criar dois componentes úteis:

* Uma tarefa do Gestor de tarefas que implementa um divisor de tarefa que pode dividir uma tarefa em várias tarefas que podem ser executado de forma independente, em paralelo.
* Um processador de tarefas que pode ser usado para executar de pré-processamento e pós-processamento em torno de uma linha de comandos do aplicativo.

Por exemplo, num cenário de processamento de filme, o divisor de tarefa seria de transformar um trabalho de filme único em centenas ou milhares de tarefas separadas que seriam processada quadros individuais separadamente. Do mesmo modo, o processador de tarefa invocará o aplicativo de processamento e todos os processos dependentes que são necessários para processar cada quadro, bem como executam quaisquer ações adicionais (por exemplo, copiar o fotograma composto para uma localização de armazenamento).

> [!NOTE]
> Os modelos do Gestor de tarefas e o processador de tarefas são independentes entre si, para que pode optar por utilizar ambas, ou apenas um deles, consoante os requisitos da sua tarefa de computação e nas suas preferências.
> 
> 

Conforme mostrado no diagrama abaixo, uma tarefa de computação que utiliza estes modelos irão passar pelo três fases:

1. O código de cliente (por exemplo, aplicações, serviço web, etc.) submete um trabalho para o serviço do Batch no Azure, especificar como a sua tarefa do Gestor de tarefas do programa de Gestor de trabalho.
2. O serviço Batch executa a tarefa de gestão num nó de computação e o divisor de tarefa inicia o número de tarefas de processador de tarefas, especificado no como muitos nós de computação conforme necessário, com base nos parâmetros e especificações no código de divisor de tarefa.
3. As tarefas de processador de tarefas são executadas de forma independente, em paralelo, para processar os dados de entrada e gerar os dados de saída.

![Diagrama que mostra como o código de cliente interage com o serviço Batch][diagram01]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar os modelos do Batch, terá o seguinte:

* Um computador com o Visual Studio 2015 instalado. Modelos do batch são atualmente suportadas apenas para o Visual Studio 2015.
* Os modelos do Batch, o que estão disponíveis a partir da [galeria do Visual Studio] [ vs_gallery] como extensões do Visual Studio. Existem duas formas de obter os modelos:
  
  * Instale os modelos utilizando o **extensões e atualizações** caixa de diálogo no Visual Studio (para obter mais informações, consulte [localizando e usando o Visual Studio Extensions][vs_find_use_ext]). Na **extensões e atualizações** diálogo caixa, procurar e transferir as extensões de dois seguintes:
    
    * Gestor de tarefas de lote do Azure com o divisor de tarefa
    * Processador de tarefa de lote do Azure
  * Transferir os modelos a partir da Galeria online para o Visual Studio: [Modelos de projeto do Microsoft Azure Batch][vs_gallery_templates]
* Se planeia utilizar o [pacotes de aplicações](batch-application-packages.md) para implementar a tarefa do Gestor de recursos e nós de computação de processador de tarefas para o Batch, precisa de associar uma conta de armazenamento à conta do Batch.

## <a name="preparation"></a>Preparação
Recomendamos que crie uma solução que pode conter o seu Gestor de trabalho, bem como o processador de tarefa, porque isso pode tornar mais fácil de compartilhar código entre o Gestor de tarefas e os programas de processador de tarefa. Para criar esta solução, siga estes passos:

1. Abra o Visual Studio e selecione **arquivo** > **New** > **projeto**.
2. Sob **modelos**, expanda **Other Project Types**, clique em **soluções do Visual Studio**e, em seguida, selecione **solução em branco**.
3. Escreva um nome que descreve a sua aplicação e o objetivo desta solução (por exemplo, "LitwareBatchTaskPrograms").
4. Para criar a nova solução, clique em **OK**.

## <a name="job-manager-template"></a>Modelo de Gestor de tarefas
O modelo de Gestor de tarefas ajuda-o a implementar uma tarefa do Gestor de tarefas que pode realizar as seguintes ações:

* Divida uma tarefa em várias tarefas.
* Submeta as tarefas para executar no Batch.

> [!NOTE]
> Para obter mais informações sobre tarefas do Gestor de mensagens em fila, consulte [descrição geral da funcionalidade Batch para programadores](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Criar uma tarefa do Gestor utilizando o modelo
Para adicionar um Gestor de trabalhos para a solução que criou anteriormente, siga estes passos:

1. Abra a solução existente no Visual Studio.
2. No Solution Explorer, clique com botão direito a solução, clique em **Add** > **novo projeto**.
3. Sob **em Visual C#** , clique em **Cloud**e, em seguida, clique em **Gestor de tarefas do Azure Batch com a tarefa de Splitter**.
4. Escreva um nome que descreve o seu aplicativo e identifica este projeto como o Gerenciador de trabalhos (por exemplo "LitwareJobManager").
5. Para criar o projeto, clique em **OK**.
6. Por fim, compile o projeto para forçar o Visual Studio para carregar todos os pacotes de NuGet referenciados e certifique-se de que o projeto é válido antes de começar a modificá-la.

### <a name="job-manager-template-files-and-their-purpose"></a>Arquivos de modelo do Gestor de tarefas e sua finalidade
Quando cria um projeto usando o modelo de Gestor de tarefas, ela gera três grupos de arquivos de código:

* O ficheiro de programa principal (Program.cs). Contém o ponto de entrada de programa e manipulação de exceção de nível superior. Normalmente não deve ser preciso modificar isso.
* O diretório da estrutura. Contém os ficheiros responsáveis pelo trabalho de 'automático' realizado pelo programa de Gestor de trabalho – descompactar os parâmetros, adicionar tarefas ao trabalho do Batch, etc. Normalmente não deve necessitar de modificar esses arquivos.
* O ficheiro de divisor de tarefa (JobSplitter.cs). Isso é onde irá colocar sua lógica de específicas da aplicação para a divisão de um trabalho em tarefas.

Claro que pode adicionar ficheiros adicionais conforme necessário para suportar o seu código de divisor de tarefa, dependendo da complexidade do trabalho separação da lógica.

O modelo também gera arquivos de projeto do .NET padrão, como um arquivo. csproj, App. config, Packages, etc.

O resto desta secção descreve os diferentes arquivos e suas estruturas de código e explica o que faz cada classe.

![Visual Studio Solution Explorer que mostra o Gerenciador de trabalhos a solução de modelo][solution_explorer01]

**Ficheiros de estrutura**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de trabalho, como detalhes de conta do Batch, as credenciais da conta de armazenamento ligada, tarefa e informações de tarefas e parâmetros da tarefa. Ele também fornece acesso a variáveis de ambiente definidas pelo Batch (consulte as definições de ambiente para tarefas, na documentação do Batch) por meio da classe Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrai a implementação da classe de configuração, para que possa teste de unidade o divisor de tarefa com um objeto de configuração de falsos ou fictícios.
* `JobManager.cs`: Orquestra os componentes do programa de Gestor de trabalho. Ele é responsável pela inicializar o divisor de tarefa, invocar o divisor de tarefa e fazer a expedição tarefas retornados pelo divisor de tarefa para o submissor da tarefa.
* `JobManagerException.cs`: Representa um erro que exige o Gerenciador de trabalhos terminar. JobManagerException é utilizado para encapsular erros 'esperados' onde as informações de diagnóstico específicas podem ser fornecidas como parte de terminação.
* `TaskSubmitter.cs`: Esta classe é da responsabilidade adicionando tarefas retornados pelo divisor de tarefa para a tarefa de lote. Agregações de classe JobManager a sequência de tarefas em lotes para adição atempada mas eficiente para a tarefa, em seguida, chama TaskSubmitter.SubmitTasks num thread em segundo plano para cada lote.

**Divisor de tarefa**

`JobSplitter.cs`: Essa classe contém lógica de aplicativo específica para dividir o trabalho em tarefas. O framework invoca o método de JobSplitter.Split para obter uma sequência de tarefas, que adiciona ao trabalho, como o método retorna-los. Esta é a classe em que injetará a lógica do seu trabalho. Implemente o método Split para retornar uma seqüência de instâncias de CloudTask que representa as tarefas no qual deseja particionar o trabalho.

**Arquivos de projeto de linha de comandos de .NET Standard**

* `App.config`: Arquivo de configuração do aplicativo de .NET padrão.
* `Packages.config`: Ficheiro de dependência do pacote de NuGet padrão.
* `Program.cs`: Contém o ponto de entrada de programa e manipulação de exceções de nível superior.

### <a name="implementing-the-job-splitter"></a>Implementando o divisor de tarefa
Quando abrir o projeto de modelo de Gestor de tarefas, o projeto terá o ficheiro de JobSplitter.cs abrir por predefinição. Pode implementar a lógica de divisão para as tarefas na sua carga de trabalho utilizando o show do método Split() abaixo:

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
> A secção anotada no `Split()` método é a única seção do código do modelo de Gestor de tarefas que pretende modificar ao adicionar a lógica para dividir os trabalhos em tarefas diferentes. Se quiser modificar uma secção diferente do modelo, verifique se são familiarized com como o Batch funciona e experimente algumas da [exemplos de código do Batch][github_samples].
> 
> 

A sua implementação de Split() tem acesso a:

* Os parâmetros da tarefa, via o `_parameters` campo.
* O objeto de CloudJob que representa a tarefa, via o `_job` campo.
* O objeto de CloudTask que representa a tarefa de gestão, via o `_jobManagerTask` campo.

Sua `Split()` implementação não precisa de adicionar tarefas ao trabalho diretamente. Em vez disso, seu código deverá devolver uma seqüência de objetos de CloudTask e estes serão adicionados ao trabalho automaticamente por classes do framework que invocam o divisor de tarefa. É comum para utilizar o C#do iterador (`yield return`) funcionalidade para implementar os separadores de tarefa, pois isso permite que as tarefas iniciar em execução logo que possível, em vez de aguardar que todas as tarefas deve ser calculado.

**Falha da tarefa divisor**

Se o divisor de tarefa encontra um erro, ele deve optar por:

* Terminar a seqüência usando o c# `yield break` instrução, nesse caso, o Gerenciador de trabalhos será tratado conclusão com êxito; ou
* Gera uma exceção, no qual o caso, que o Gerenciador de trabalhos será tratado como falha e pode ser repetido dependendo de como o cliente tenha configurado).

Em ambos os casos, será elegíveis para executar quaisquer tarefas já devolvidos pelo divisor de tarefa e adicionadas ao trabalho do Batch. Se não quiser que isso aconteça, em seguida, pode:

* Terminar a tarefa antes do retorno do divisor de tarefa
* Formular a coleção inteira de tarefas antes de o devolver (ou seja, retornar uma `ICollection<CloudTask>` ou `IList<CloudTask>` em vez de implementar o divisor de tarefa com um iterador c#)
* Utilizar as dependências de tarefas para fazer todas as tarefas que dependem a conclusão com êxito do Gestor de trabalho

**Tentativas de Gestor de trabalho**

Se o Gestor de tarefas falhar, pode ser repetida pelo serviço Batch consoante as definições de repetição do cliente. Em geral, isso é seguro, porque quando o framework adiciona tarefas ao trabalho, ele ignora todas as tarefas que já existem. No entanto, se tarefas de cálculo é cara, pode não querer incorrer em custos de recalcular as tarefas que já foram adicionadas para a tarefa; por outro lado, se a voltar a executar não é garantida para gerar a mesma tarefa IDs, em seguida, o comportamento de 'Ignorar duplicados' será não iniciada. Nestes casos deve conceber a sua divisor de tarefa para detetar o trabalho que já foi feito e não repeti-lo, por exemplo, efetuando uma CloudJob.ListTasks antes de começar a gerar tarefas.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Códigos de saída e as exceções no modelo do Gestor de tarefas
Códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e eles podem ajudar a identificar quaisquer problemas com a execução do programa. O modelo de Gestor de trabalho implementa os códigos de saída e exceções descritas nesta secção.

Uma tarefa de gestão é implementada com o modelo de Gestor de tarefas pode retornar códigos de saída possível três:

| Código | Descrição |
| --- | --- |
| 0 |O Gestor de tarefas foi concluído com êxito. O código de divisor de tarefa foi executada até à conclusão e todas as tarefas foram adicionadas ao trabalho. |
| 1 |A tarefa de gestão falhou com uma exceção numa parte 'esperada' do programa. A exceção foi traduzida para um JobManagerException com informações de diagnóstico e, sempre que possível, sugestões para resolver a falha. |
| 2 |A tarefa de gestão falhou com uma exceção 'inesperada'. A exceção foi registada para a saída padrão, mas o Gerenciador de trabalhos não foi possível adicionar as informações adicionais de diagnóstico ou de remediação. |

No caso de falha de tarefa do Gestor de trabalho, algumas tarefas ainda foram adicionadas ao serviço antes do erro ocorreu. Estas tarefas serão executadas normalmente. Consulte "Falha de divisor de tarefa" acima para discussão sobre este caminho de código.

Todas as informações retornadas pela exceções são escritas em arquivos stdout.txt e stderr. txt. Para obter mais informações, consulte [tratamento de erro](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considerações de cliente
Esta secção descreve alguns requisitos de implementação do cliente ao invocar um Gestor de trabalhos com base nesse modelo. Ver [como passar parâmetros e variáveis de ambiente de código do cliente](#pass-environment-settings) para obter detalhes sobre a passagem de parâmetros e definições de ambiente.

**Obrigatórias credenciais**

Para adicionar tarefas ao trabalho do Azure Batch, a tarefa de gestão requer o URL da conta do Azure Batch e a chave. Tem de passar em variáveis de ambiente com o nome YOUR_BATCH_URL e YOUR_BATCH_KEY. Pode definir estas no Gestor de trabalho definições de ambiente de tarefas. Por exemplo, no c# cliente:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciais de armazenamento**

Normalmente, o cliente não precisa de fornecer as credenciais da conta de armazenamento ligada para a tarefa de gestão porque (a) a maioria dos gerentes de tarefa não é necessário aceder explicitamente a conta de armazenamento associada e (b) a conta de armazenamento ligada, muitas vezes, é fornecida para todas as tarefas como um configuração de ambiente comum para a tarefa. Se não está a fornecer a conta de armazenamento associada através das definições de ambiente comuns, e o Gerenciador de trabalhos requer acesso ao armazenamento ligado, em seguida, deve fornecer as credenciais de armazenamento ligada da seguinte forma:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Definições de tarefa do Gestor de trabalhos**

O cliente deve definir o Gerenciador de trabalhos *killJobOnCompletion* sinalizador para **falso**.

É normalmente seguro para o cliente defina *runExclusive* ao **falso**.

O cliente deve utilizar o *resourceFiles* ou *applicationPackageReferences* coleção para que a tarefa Gestor de executável (e suas DLLs necessárias) implementado para o nó de computação.

Por predefinição, o Gerenciador de trabalhos não será repetido se falhar. Dependendo de sua lógica de Gestor de trabalho, o cliente poderá querer permitir repetição de tentativas via *restrições*/*maxTaskRetryCount*.

**Definições de tarefa**

Se o divisor de tarefa emite tarefas com as dependências, o cliente tem de definir usesTaskDependencies da tarefa como true.

No modelo de divisor de tarefa, é incomum para que os clientes desejam adicionar tarefas a trabalhos ativamos o que cria o divisor de tarefa. O cliente deve, portanto, definido normalmente da tarefa *onAllTasksComplete* ao **terminatejob**.

## <a name="task-processor-template"></a>Modelo de processador de tarefas
Um modelo de processador de tarefa ajuda-o a implementar um processador de tarefas que pode realizar as seguintes ações:

* Configure as informações necessárias por cada tarefa do Batch para executar.
* Execute todas as ações necessárias por cada tarefa do Batch.
* Guarde saídas de tarefas para armazenamento persistente.

Embora um processador de tarefa não é necessário para executar tarefas no Batch, a principal vantagem de usar um processador de tarefa é que ele fornece um wrapper para implementar todas as ações de execução de tarefas num único local. Por exemplo, se tiver de executar vários aplicativos no contexto de cada tarefa, ou se precisa copiar dados para armazenamento persistente, depois de concluir cada tarefa.

As ações executadas pelo processador de tarefa podem ser simples ou complexo e o máximo ou, no mínimo, conforme exigido pela sua carga de trabalho. Além disso, ao implementar todas as ações de tarefas num processador de tarefas, pode prontamente atualizar ou adicionar ações com base nas alterações a aplicações ou requisitos de carga de trabalho. No entanto, em alguns casos um processador de tarefas pode não ser a solução ideal para a sua implementação, pode adicionar complexidade desnecessária, por exemplo ao executar tarefas que podem ser rapidamente iniciadas a partir de uma linha de comandos simple.

### <a name="create-a-task-processor-using-the-template"></a>Criar um processador de tarefas utilizando o modelo
Para adicionar um processador de tarefas para a solução que criou anteriormente, siga estes passos:

1. Abra a solução existente no Visual Studio.
2. No Solution Explorer, clique com botão direito a solução, clique em **Add**e, em seguida, clique em **novo projeto**.
3. Sob **em Visual C#** , clique em **Cloud**e, em seguida, clique em **processador de tarefa do Azure Batch**.
4. Escreva um nome que descreve o seu aplicativo e identifica este projeto como o processador de tarefa (por exemplo "LitwareTaskProcessor").
5. Para criar o projeto, clique em **OK**.
6. Por fim, compile o projeto para forçar o Visual Studio para carregar todos os pacotes de NuGet referenciados e certifique-se de que o projeto é válido antes de começar a modificá-la.

### <a name="task-processor-template-files-and-their-purpose"></a>Arquivos de modelo de processador de tarefa e sua finalidade
Quando cria um projeto usando o modelo de processador de tarefa, ele gera três grupos de arquivos de código:

* O ficheiro de programa principal (Program.cs). Contém o ponto de entrada de programa e manipulação de exceção de nível superior. Normalmente não deve ser preciso modificar isso.
* O diretório da estrutura. Contém os ficheiros responsáveis pelo trabalho de 'automático' realizado pelo programa de Gestor de trabalho – descompactar os parâmetros, adicionar tarefas ao trabalho do Batch, etc. Normalmente não deve necessitar de modificar esses arquivos.
* O ficheiro de processador de tarefa (TaskProcessor.cs). Isso é onde irá colocar a lógica de específico do aplicativo para executar uma tarefa (normalmente, ao chamar um executável existente). Código de pré e pós-processamento, como a transferência de dados adicionais ou carregar arquivos de resultados, também vai aqui.

Claro que pode adicionar ficheiros adicionais conforme necessário para suportar o seu código de processador de tarefas, dependendo da complexidade do trabalho separação da lógica.

O modelo também gera arquivos de projeto do .NET padrão, como um arquivo. csproj, App. config, Packages, etc.

O resto desta secção descreve os diferentes arquivos e suas estruturas de código e explica o que faz cada classe.

![Visual Studio Solution Explorer que mostra a solução de modelo de processador de tarefa][solution_explorer02]

**Ficheiros de estrutura**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de trabalho, como detalhes de conta do Batch, as credenciais da conta de armazenamento ligada, tarefa e informações de tarefas e parâmetros da tarefa. Ele também fornece acesso a variáveis de ambiente definidas pelo Batch (consulte as definições de ambiente para tarefas, na documentação do Batch) por meio da classe Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrai a implementação da classe de configuração, para que possa teste de unidade o divisor de tarefa com um objeto de configuração de falsos ou fictícios.
* `TaskProcessorException.cs`: Representa um erro que exige o Gerenciador de trabalhos terminar. TaskProcessorException é utilizado para encapsular erros 'esperados' onde as informações de diagnóstico específicas podem ser fornecidas como parte de terminação.

**Processador de tarefa**

* `TaskProcessor.cs`: É executada a tarefa. O framework invoca o método TaskProcessor.Run. Esta é a classe em que injetará a lógica de específicas da aplicação da sua tarefa. Implemente o método Run para:
  * Analisar e validar os parâmetros da tarefa
  * Compor a linha de comandos para qualquer programa externo que deseja invocar
  * Registar quaisquer informações de diagnóstico, poderá precisar para fins de depuração
  * Iniciar um processo com essa linha de comandos
  * Aguarde que o processo sair
  * Capturar o código de saída do processo para determinar se ele foi concluída com êxito ou falha
  * Guardar quaisquer ficheiros de saída que pretende manter no armazenamento persistente

**Arquivos de projeto de linha de comandos de .NET Standard**

* `App.config`: Arquivo de configuração do aplicativo de .NET padrão.
* `Packages.config`: Ficheiro de dependência do pacote de NuGet padrão.
* `Program.cs`: Contém o ponto de entrada de programa e manipulação de exceções de nível superior.

## <a name="implementing-the-task-processor"></a>Implementando o processador de tarefa
Quando abrir o projeto de modelo de processador de tarefa, o projeto terá o ficheiro de TaskProcessor.cs abrir por predefinição. Pode implementar a lógica de execução para as tarefas na sua carga de trabalho usando o método Run() mostrado abaixo:

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
> A seção anotada no método Run() é a única seção do código do modelo de processador de tarefas que pretende modificar ao adicionar a lógica de execução para as tarefas na sua carga de trabalho. Se quiser modificar uma secção diferente do modelo,. primeiro familiarize-se com o funcionamento de Batch ao rever a documentação do Batch e a experimentar alguns dos exemplos de código do Batch.
> 
> 

O método de Run() é responsável por iniciar a linha de comandos, a partir de um ou mais processos, aguardar pelo processo de todos os concluir, salvar os resultados e, finalmente retornar com um código de saída. O método de Run() é onde implementar a lógica de processamento para as suas tarefas. A estrutura de processador de tarefa invoca o método de Run() para; Não é necessário chamá-la.

A sua implementação de Run() tem acesso a:

* Os parâmetros da tarefa, via o `_parameters` campo.
* Os ids de trabalhos e tarefas, através da `_jobId` e `_taskId` campos.
* A configuração de tarefas, via o `_configuration` campo.

**Falha de tarefa**

Em caso de falha, pode sair do método Run() lançando uma exceção, mas tal deixa o manipulador de exceção de nível superior no controle do código de saída de tarefa. Se precisar de controlar o código de saída para que pode distinguir diferentes tipos de falha, por exemplo para fins de diagnóstico ou porque alguns modos de falha devem terminar o trabalho e outros não devem, em seguida, deverá ser encerrada o método Run() retornando um diferente de zero código de saída. Isso se torna o código de saída da tarefa.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Códigos de saída e as exceções no modelo de processador de tarefa
Códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e podem ajudar a identificar quaisquer problemas com a execução do programa. O modelo de processador de tarefa implementa os códigos de saída e exceções descritas nesta secção.

Uma tarefa do processador de tarefa que é implementada com o modelo de processador de tarefas pode retornar códigos de saída possível três:

| Código | Descrição |
| --- | --- |
| [Process.ExitCode][process_exitcode] |O processador de tarefa foi executada até à conclusão. Tenha em atenção que isto não significa que o programa que invocou foi concluída com êxito – apenas que o processador de tarefa invocou com êxito e realizado qualquer processamento posterior sem exceções. O significado do código de saída depende do programa invocado – normalmente, o código de saída 0 significa que o programa foi concluída com êxito e qualquer outro código de saída significa que o programa com falhas. |
| 1 |O processador de tarefa falhou com uma exceção numa parte 'esperada' do programa. A exceção foi traduzida para um `TaskProcessorException` com informações de diagnóstico e, sempre que possível, sugestões para resolver a falha. |
| 2 |O processador de tarefa falhou com uma exceção 'inesperada'. A exceção foi registada para a saída padrão, mas o processador de tarefa não foi possível adicionar as informações adicionais de diagnóstico ou de remediação. |

> [!NOTE]
> Se o programa, invoca utiliza códigos de saída 1 e 2 para indicar os modos de falha específico, em seguida, utilizar códigos de saída 1 e 2 para erros de processador de tarefa é ambíguo. Pode alterar esses códigos de erro do processador de tarefas para códigos de saída distintas ao editar os casos de exceção no ficheiro Program.cs.
> 
> 

Todas as informações retornadas pela exceções são escritas em arquivos stdout.txt e stderr. txt. Para obter mais informações, consulte o processamento de erros, na documentação do Batch.

### <a name="client-considerations"></a>Considerações de cliente
**Credenciais de armazenamento**

Se o processador de tarefas utiliza o armazenamento de Blobs do Azure para manter saídas, por exemplo usando a biblioteca de programa auxiliar de convenções de ficheiros, em seguida, ele precisa de aceder a *ambos* as credenciais de conta de armazenamento na cloud *ou* um blob URL de contentor que inclui uma assinatura de acesso partilhado (SAS). O modelo inclui suporte para fornecer credenciais através de variáveis de ambiente comuns. O cliente pode passar as credenciais de armazenamento da seguinte forma:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A conta de armazenamento, em seguida, está disponível na classe TaskProcessor via o `_configuration.StorageAccount` propriedade.

Se preferir usar uma URL de contentor com o SAS, pode também passar isso por meio de uma definição de ambiente comuns de tarefa, mas o modelo de processador de tarefas não inclui atualmente suporte interno para isso.

**Configuração de armazenamento**

Recomenda-se que o cliente ou a tarefa do Gestor de tarefas crie quaisquer contentores necessárias para tarefas antes de adicionar tarefas ao trabalho. Isso é obrigatório que se utilizar um URL de contentor com o SAS, como tal, um URL não inclui permissão para criar o contentor. É recomendado, mesmo se passar credenciais de conta de armazenamento, como ele salva cada tarefa ter de chamar CloudBlobContainer.CreateIfNotExistsAsync no contentor.

## <a name="pass-parameters-and-environment-variables"></a>Transmita os parâmetros e variáveis de ambiente
### <a name="pass-environment-settings"></a>Definições de ambiente de passagem
Um cliente pode passar informações para a tarefa de gestão na forma de definições de ambiente. Estas informações, em seguida, podem ser utilizadas pela tarefa de gestão ao gerar as tarefas de processador de tarefas que serão executado como parte do trabalho de computação. Exemplos de informações que pode passar como definições de ambiente são:

* Chaves de conta e o nome de conta de armazenamento
* URL da conta do batch
* Chave de conta do batch

O serviço Batch tem um mecanismo simple para passar definições de ambiente para uma tarefa de gestão, utilizando o `EnvironmentSettings` propriedade na [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Por exemplo, para obter o `BatchClient` instância para uma conta do Batch, pode passar como variáveis de ambiente do cliente de código o URL e as credenciais da chave partilhadas para a conta do Batch. Da mesma forma, para aceder à conta de armazenamento que está ligada à conta do Batch, pode passar o nome da conta de armazenamento e a chave de conta de armazenamento como variáveis de ambiente.

### <a name="pass-parameters-to-the-job-manager-template"></a>Transmita os parâmetros para o modelo de Gestor de tarefas
Em muitos casos, é útil passar parâmetros por tarefa para a tarefa de gestão, para controlar a tarefa de divisão de processo ou para configurar as tarefas para a tarefa. Pode fazê-lo ao carregar um ficheiro JSON com o nome Parameters. JSON como um arquivo de recursos para a tarefa de gestão. Os parâmetros, em seguida, podem se tornar disponíveis na `JobSplitter._parameters` campo no modelo do Gestor de tarefas.

> [!NOTE]
> O manipulador de parâmetro interno oferece suporte apenas a cadeia-cadeia dicionários. Se quiser passar valores JSON complexos como valores de parâmetros, será preciso passá-los como cadeias de caracteres e analisá-las no divisor de tarefa ou modificar a estrutura `Configuration.GetJobParameters` método.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Transmita os parâmetros para o modelo de processador de tarefa
Pode também passar parâmetros para tarefas individuais implementadas usando o modelo de processador de tarefa. Assim como acontece com o modelo de Gestor de trabalho, o modelo de processador de tarefa procura um arquivo de recursos com o nome

Parameters. JSON e se Achei carrega-o como o dicionário de parâmetros. Existem duas opções para como passar parâmetros para as tarefas de processador de tarefa:

* Reutilize os parâmetros da tarefa JSON. Isso funciona bem se os únicos parâmetros são aqueles em todo o trabalho (por exemplo, uma altura de composição e a largura). Para fazer isso, ao criar um CloudTask no divisor de tarefa, adicionar uma referência para o objeto de ficheiro de recurso Parameters. JSON da ResourceFiles a tarefa de Gestor de trabalho (`JobSplitter._jobManagerTask.ResourceFiles`) à coleção de ResourceFiles o CloudTask.
* Gerar e carregar um documento de Parameters. JSON de tarefa específica como parte da execução da tarefa splitter para fazer referência a esse blob na coleção de ficheiros de recursos da tarefa. Isso é necessário se diferentes tarefas possuem parâmetros diferentes. Por exemplo, poderá um cenário de composição 3D em que o índice do quadro é passado para a tarefa como um parâmetro.

> [!NOTE]
> O manipulador de parâmetro interno oferece suporte apenas a cadeia-cadeia dicionários. Se quiser passar valores JSON complexos como valores de parâmetros, será preciso passá-los como cadeias de caracteres e analisá-las no processador de tarefas ou modificar a estrutura `Configuration.GetTaskParameters` método.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
### <a name="persist-job-and-task-output-to-azure-storage"></a>Manter a saída de trabalhos e tarefas para o armazenamento do Azure
Outra ferramenta útil no desenvolvimento de soluções do Batch é [convenções de ficheiros do Azure Batch][nuget_package]. Utilize esta biblioteca de classes do .NET (atualmente em pré-visualização) nas suas aplicações de .NET do Batch para facilmente armazenam e obtêm saídas de tarefas para e do armazenamento do Azure. [Manter a saída de trabalhos e tarefas do Azure Batch](batch-task-output.md) contém uma discussão completa sobre a biblioteca e respetiva utilização.


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

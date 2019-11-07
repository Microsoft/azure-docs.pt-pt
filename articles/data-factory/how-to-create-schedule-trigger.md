---
title: Criar gatilhos de agendamento no Azure Data Factory
description: Saiba como criar um gatilho no Azure Data Factory que executa um pipeline em um agendamento.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2018
ms.openlocfilehash: 127db8a484b9624586dea70c44af3bc84b3fc84e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673778"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Criar um gatilho que executa um pipeline em um agendamento
Este artigo fornece informações sobre o gatilho de agendamento e as etapas para criar, iniciar e monitorar um gatilho de agendamento. Para outros tipos de gatilhos, consulte [execução de pipeline e gatilhos](concepts-pipeline-execution-triggers.md).

Ao criar um gatilho de agendamento, especifique uma agenda (data de início, recorrência, data de término etc.) para o gatilho e associe a um pipeline. Os pipelines e os acionadores têm uma relação muitos para muitos. Múltiplos acionadores podem arrancar um pipeline individual. Um acionador único pode arrancar vários pipelines.

As seções a seguir fornecem etapas para criar um gatilho de agendamento de diferentes maneiras. 

## <a name="data-factory-ui"></a>IU do Data Factory
Você pode criar um **gatilho de agendamento** para agendar um pipeline para ser executado periodicamente (por hora, diariamente, etc.). 

> [!NOTE]
> Para obter uma explicação completa de como criar um pipeline e um gatilho de agendamento, associar o gatilho ao pipeline e executar e monitorar o pipeline, consulte [início rápido: criar um data Factory usando a interface do usuário do data Factory](quickstart-create-data-factory-portal.md).

1. Mude para o separador **Editar**. 

    ![Mudar para o separador Editar](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Clique em **Acionador**, no menu, e clique em **Novo/Editar**. 

    ![Menu Novo acionador](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. Na página **Adicionar Acionadores**, clique em **Escolher acionador...** e clique em **Novo**. 

    ![Adicionar acionadores - acionador novo](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. Na página **novo gatilho** , execute as seguintes etapas: 

    1. Confirme se a **agenda** está selecionada para o **tipo**. 
    2. Especifique o DateTime inicial do gatilho para **data de início (UTC)** . Ele é definido como o DateTime atual por padrão. 
    3. Especifique a **recorrência** para o gatilho. Selecione um dos valores na lista suspensa (a cada minuto, por hora, diariamente, semanalmente e mensalmente). Insira o multiplicador na caixa de texto. Por exemplo, se você quiser que o gatilho seja executado uma vez a cada 15 minutos, selecione a **cada minuto**e digite **15** na caixa de texto. 
    4. Para o campo **final** , se você não quiser especificar um DateTime final para o gatilho, selecione **sem fim**. Para especificar uma data e hora de término, selecione **na data**, especifique end DateTime e clique em **aplicar**. Nenhuma execução de pipeline tem custos associados. Se você estiver testando, talvez queira garantir que o pipeline seja disparado apenas algumas vezes. No entanto, certifique-se de que há tempo suficiente para o pipeline ser executado entre a hora de publicação e a hora de fim. O acionador só entra em vigor depois de publicar a solução no Data Factory e não quando guarda o acionador na IU.

        ![Definições do acionador](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. Na janela **novo gatilho** , marque a opção **ativado** e clique em **Avançar**. Você pode usar essa caixa de seleção para desativar o gatilho mais tarde. 

    ![Definições do acionador - botão seguinte](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. Na página **Novo Acionador**, reveja a mensagem de aviso e clique em **Concluir**.

    ![Definições do acionador - botão concluir](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Clique em **Publicar** para publicar as alterações no Data Factory. Até que você publique alterações no Data Factory, o gatilho não iniciará o disparo das execuções de pipeline. 

    ![Botão Publicar](./media/how-to-create-schedule-trigger/publish-2.png)
8. Mude para o separador **Monitorizar**, no lado esquerdo. Clique em **Atualizar** para atualizar a lista. Você vê as execuções de pipeline disparadas pelo gatilho agendado. Repare nos valores na coluna **Acionado Por**. Se você usar a opção **disparar agora** , verá a execução do gatilho manual na lista. 

    ![Monitorizar execuções acionadas](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Clique na seta para baixo junto a **Execuções do Pipeline** para mudar para a vista **Execuções Acionadas**. 

    ![Monitorizar execuções acionadas](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Esta seção mostra como usar Azure PowerShell para criar, iniciar e monitorar um gatilho de agendamento. Para ver este exemplo funcionando, primeiro Percorra o [início rápido: criar um data Factory usando Azure PowerShell](quickstart-create-data-factory-powershell.md). Em seguida, adicione o seguinte código ao método Main, que cria e inicia um gatilho de agendamento que é executado a cada 15 minutos. O gatilho é associado a um pipeline denominado **Adfv2QuickStartPipeline** que você cria como parte do guia de início rápido.

1. Crie um arquivo JSON chamado **mytrigger. JSON** na pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

    > [!IMPORTANT]
    > Antes de salvar o arquivo JSON, defina o valor do elemento **StartTime** como a hora UTC atual. Defina o valor do elemento **EndTime** para uma hora após a hora UTC atual.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    No trecho de JSON:
    - O elemento **Type** do gatilho é definido como "ScheduleTrigger".
    - O elemento **Frequency** é definido como "Minute" e o elemento **Interval** é definido como 15. Portanto, o gatilho executa o pipeline a cada 15 minutos entre as horas de início e término.
    - O elemento **EndTime** é uma hora após o valor do elemento **StartTime** . Portanto, o gatilho executa o pipeline 15 minutos, 30 minutos e 45 minutos após a hora de início. Não se esqueça de atualizar a hora de início para a hora UTC atual e a hora de término para uma hora após a hora de início. 
    - O gatilho é associado ao pipeline **Adfv2QuickStartPipeline** . Para associar vários pipelines a um gatilho, adicione mais seções **pipelineReference** .
    - O pipeline no início rápido usa dois valores de **parâmetros** : **inputPath** e **outputPath**. Portanto, você passa valores para esses parâmetros do gatilho.

2. Crie um gatilho usando o cmdlet **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Confirme se o status do gatilho é **interrompido** usando o cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Inicie o gatilho usando o cmdlet **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confirme se o status do gatilho é **iniciado** usando o cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6.  Obtenha as execuções do gatilho em Azure PowerShell usando o cmdlet **Get-AzDataFactoryV2TriggerRun** . Para obter as informações sobre as execuções do gatilho, execute o comando a seguir periodicamente. Atualize os valores de **TriggerRunStartedAfter** e **TriggerRunStartedBefore** para corresponder aos valores em sua definição de gatilho:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Para monitorar as execuções de gatilho e as execuções de pipeline no portal do Azure, consulte [monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>SDK .NET
Esta seção mostra como usar o SDK do .NET para criar, iniciar e monitorar um gatilho. Para ver este exemplo funcionando, primeiro Percorra o [início rápido: criar um data Factory usando o SDK do .net](quickstart-create-data-factory-dot-net.md). Em seguida, adicione o seguinte código ao método Main, que cria e inicia um gatilho de agendamento que é executado a cada 15 minutos. O gatilho é associado a um pipeline denominado **Adfv2QuickStartPipeline** que você cria como parte do guia de início rápido.

Para criar e iniciar um gatilho de agendamento que é executado a cada 15 minutos, adicione o seguinte código ao método principal:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Para monitorar uma execução de gatilho, adicione o código a seguir antes da última instrução `Console.WriteLine` no exemplo:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Para monitorar as execuções de gatilho e as execuções de pipeline no portal do Azure, consulte [monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Python SDK
Esta seção mostra como usar o SDK do Python para criar, iniciar e monitorar um gatilho. Para ver este exemplo funcionando, primeiro Percorra o [início rápido: criar um data Factory usando o SDK do Python](quickstart-create-data-factory-python.md). Em seguida, adicione o bloco de código a seguir após o bloco de código "monitorar o pipeline de execução" no script do Python. Esse código cria um gatilho de agendamento que é executado a cada 15 minutos entre as horas de início e término especificadas. Atualize a variável **start_time** para a hora UTC atual e a variável **end_time** para uma hora após a hora UTC atual.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Para monitorar as execuções de gatilho e as execuções de pipeline no portal do Azure, consulte [monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Você pode usar um modelo de Azure Resource Manager para criar um gatilho. Para obter instruções detalhadas, consulte [criar um data Factory do Azure usando um modelo do Resource Manager](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Passar a hora de início do gatilho para um pipeline
Azure Data Factory versão 1 dá suporte à leitura ou gravação de dados particionados usando as variáveis do sistema: **SliceStart**, **SliceEnd**, **WindowStart**e **WindowEnd**. Na versão atual do Azure Data Factory, você pode obter esse comportamento usando um parâmetro de pipeline. A hora de início e a hora agendada para o gatilho são definidas como o valor para o parâmetro de pipeline. No exemplo a seguir, a hora agendada para o gatilho é passada como um valor para o parâmetro pipeline **scheduledRunTime** :

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>Esquema JSON
A definição JSON a seguir mostra como criar um gatilho de agendamento com agendamento e recorrência:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  A propriedade **parameters** é uma propriedade obrigatória do elemento **pipelines**. Se o seu pipeline não utiliza parâmetros, deve incluir uma definição JSON vazia para a propriedade **parameters**.


### <a name="schema-overview"></a>Descrição geral do esquema
A tabela que se segue fornece uma descrição geral de alto nível dos principais elementos do esquema relacionados com a periodicidade e o agendamento de um acionador:

| Propriedade JSON | Descrição |
|:--- |:--- |
| **startTime** | Um valor de data/hora. Para agendamentos simples, o valor da propriedade **startTime** aplica-se à primeira ocorrência. Para agendamentos complexos, o acionador é iniciado imediatamente a seguir ao valor especificado em **startTime**. |
| **endTime** | A data e hora de fim do acionador. O acionador não é executado após a data e hora de fim especificadas. O valor da propriedade não pode situar-se no passado. Esta propriedade é opcional. |
| **timeZone** | O fuso horário. Atualmente, o fuso horário UTC é o único suportado. |
| **recurrence** | Um objeto de periodicidade que especifica as regras de periodicidade do acionador. O objeto de periodicidade suporta os elementos **frequency**, **interval**, **endTime**, **count** e **schedule**. Quando um objeto de periodicidade é definido, o elemento **frequency** é obrigatório. Os outros elementos do objeto de periodicidade são opcionais. |
| **frequency** | A unidade que se refere à frequência com que o acionador voltar a ocorrer. Os valores suportados incluem "minute", "hour", "day", "week" e "month". |
| **interval** | Um valor inteiro positivo que indica o intervalo do valor **frequency**, que determina o número de vezes que o acionador é executado. Por exemplo, se o valor de **interval** for 3 e o de **frequency** for "week", o acionador repete-se de três em três semanas. |
| **schedule** | A agenda de periodicidade do acionador. Um acionador que tenha um valor **frequency** especificado modifica a respetiva periodicidade com base numa agenda de periodicidade. A propriedade **schedule** contém modificações da periodicidade baseadas em minutos, horas, dias de semana, dias do mês e número da semana.


### <a name="schema-defaults-limits-and-examples"></a>Predefinições, limites e exemplos do esquema

| Propriedade JSON | Tipo | Necessário | Valor predefinido | Valores válidos | Exemplo |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | String | Sim | Nenhum | Datas-Horas ISO 8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Object | Sim | Nenhum | Objeto de periodicidade | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Número | Não | 1 | 1 a 1000 | `"interval":10` |
| **endTime** | String | Sim | Nenhum | Um valor de data/hora que representa uma hora no futuro. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Object | Não | Nenhum | Objeto da agenda | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Propriedade startTime
A tabela que se segue mostra o modo como a propriedade **startTime** controla a execução de um acionador:

| valor de startTime | Periodicidade sem agenda | Periodicidade com agenda |
|:--- |:--- |:--- |
| Hora de início no passado | Calcula a primeira hora de execução no futuro após a hora de início e é executada nessa hora.<br/><br/>Executa as execuções subsequentes com base no cálculo da última hora de execução.<br/><br/>Veja o exemplo a seguir à tabela. | O acionador é iniciado _imediatamente a seguir_  à hora de início especificada. A primeira ocorrência tem por base a agenda calculada a partir da hora de início.<br/><br/>Executa as execuções subsequentes com base na agenda de periodicidade. |
| Hora de início no futuro ou no presente | É executada uma vez na hora de início especificada.<br/><br/>Executa as execuções subsequentes com base no cálculo da última hora de execução. | O acionador é iniciado _imediatamente a seguir_  à hora de início especificada. A primeira ocorrência tem por base a agenda calculada a partir da hora de início.<br/><br/>Executa as execuções subsequentes com base na agenda de periodicidade. |

Vejamos um exemplo do que acontece quando a hora de início (startTime) se situa no passado, com periodicidade, mas sem agenda. Parta do princípio de que a hora atual é `2017-04-08 13:00`, a hora de início é `2017-04-07 14:00` e a periodicidade é de dois em dois dias. (O valor de **recorrência** é definido definindo a propriedade **Frequency** como "Day" e a propriedade **Interval** como 2.) Observe que o valor **StartTime** está no passado e ocorre antes da hora atual.

Nestas condições, a primeira execução é em `2017-04-09 at 14:00`. O motor do Scheduler calcula as ocorrências de execução a partir da hora de início. Quaisquer instâncias no passado são eliminadas. O motor utiliza a instância seguinte que ocorre no futuro. Neste cenário, a hora de início é `2017-04-07 at 2:00pm`, pelo que a instância seguinte é dali a dois dias a contar dessa hora, ou seja, `2017-04-09 at 2:00pm`.

A primeira hora de execução é igual, mesmo que o valor de **startTime** seja `2017-04-05 14:00` ou `2017-04-01 14:00`. Após a primeira execução, as execuções subsequentes são calculadas com base na agenda. Por conseguinte, as execuções subsequentes são a `2017-04-11 at 2:00pm`, depois a `2017-04-13 at 2:00pm`, a seguir a `2017-04-15 at 2:00pm` e assim sucessivamente.

Por último, quando as horas ou os minutos não se encontram definidos na agenda para um acionador, as horas ou minutos da primeira execução são utilizados como as predefinições.

### <a name="schedule-property"></a>Propriedade schedule
Por um lado, a utilização de agendas pode limitar o número de execuções de acionadores. Por exemplo, se um acionador com uma frequência mensal estiver agendado de modo a ser executado apenas no dia 31, o acionador só é executado nos meses que têm 31 dias.

Por outro lado, as agendas também podem expandir o número de execuções de acionadores. Por exemplo, um acionador com uma frequência mensal que esteja agendado de maneira a ser executado nos dias 1 e 2 do mês, é executado nos 1º e 2º dias do mês, em vez de uma vez por mês.

Se forem especificados vários elementos **schedule**, a ordem de avaliação é feita da definição de agenda maior para a mais pequena. A avaliação começa pelo número da semana, seguindo-se o dia do mês, o dia da semana, a hora e, por último, o minuto.

A tabela seguinte descreve os elementos de **schedule** de forma detalhada:


| Elemento JSON | Descrição | Valores válidos |
|:--- |:--- |:--- |
| **minutes** | Minutos da hora em que o acionador é executado. | <ul><li>Número inteiro</li><li>Matriz de números inteiros</li></ul>
| **hours** | Horas do dia em que o acionador é executado. | <ul><li>Número inteiro</li><li>Matriz de números inteiros</li></ul> |
| **weekDays** | Dias da semana em que o acionador é executado. O valor pode ser especificado com uma frequência semanal apenas. | <ul><li>Segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira, sábado, domingo</li><li>Matriz de valores de dia (o tamanho máximo da matriz é 7)</li><li>Os valores de dia não são sensíveis às maiúsculas e minúsculas</li></ul> |
| **monthlyOccurrences** | Dias do mês em que o acionador é executado. O valor pode ser especificado com uma frequência mensal apenas. | <ul><li>Matriz de objetos **monthlyOccurrence** : `{ "day": day,  "occurrence": occurrence }`.</li><li>O atributo **day** é o dia da semana em que o acionador é executado. Por exemplo, uma propriedade **monthlyOccurrences** com um valor **day** igual a `{Sunday}` significa todos os domingos do mês. O atributo **day** é obrigatório.</li><li>O atributo **occurrence** é a ocorrência do valor **day** especificado durante o mês. Por exemplo, uma propriedade **monthlyOccurrences** com os valores **day** e **occurrence** iguais a `{Sunday, -1}` significa o último domingo do mês. O atributo **occurrence** é opcional.</li></ul> |
| **monthDays** | Dia do mês em que o acionador é executado. O valor pode ser especificado com uma frequência mensal apenas. | <ul><li>Qualquer valor <= -1 e >= -31</li><li>Qualquer valor >= 1 e <= 31</li><li>Matriz de valores</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Exemplos de agendas de periodicidade do acionador
Esta secção mostra exemplos de agendas de periodicidade e centra-se no objeto **schedule** e respetivos elementos.

Os exemplos partem do princípio de que o valor **interval** é igual a 1 e que o valor **frequency** está correto de acordo com a definição da agenda. Por exemplo, não pode ter um valor **frequency** igual a "day" e ter simultaneamente uma modificação "monthDays" no objeto **schedule**. Este género de restrições é mencionado na tabela da secção anterior.

| Exemplo | Descrição |
|:--- |:--- |
| `{"hours":[5]}` | Executar todos os dias às 5:00. |
| `{"minutes":[15], "hours":[5]}` | Executar todos os dias às 5:15. |
| `{"minutes":[15], "hours":[5,17]}` | Executar todos os dias às 5:15 e 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` | Executar todos os dias às 5:15, 5:45, 17:15 e 17:45. |
| `{"minutes":[0,15,30,45]}` | Executar de 15 em 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Executar hora a hora. Este acionador é executado de hora em hora. Os minutos são controlados pelo valor de **startTime**, quando é especificado um valor. Se não for especificado qualquer valor, os minutos são controlados pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (a que se aplicar) for 12:25, o acionador é executado às 00:25, 01:25, 02:25 e assim sucessivamente até às 23:25.<br/><br/>Esta agenda equivale a ter um acionador com um valor **frequency** igual a "hour", um valor **interval** igual a 1 e zero propriedades **schedule**.  Esta agenda pode ser utilizada com valores **frequency** e **interval** diferentes para criar outros acionadores. Por exemplo, quando o valor **frequency** é igual a "month", a agenda é executada apenas uma vez por mês, em vez de todos os dias, como acontece quando o valor **frequency** é igual a "day". |
| `{"minutes":[0]}` | Executar de hora a hora, à hora certa. Este acionador é executado de hora a hora, à hora certa, começando às 00:00, 1:00, 2:00 e assim sucessivamente.<br/><br/>Esta agenda equivale a ter um acionador com um valor **frequency** igual a "hour" e um valor **startTime** de zero minutos, ou zero propriedades **schedule**, mas um valor **frequency** igual a "day". Se o valor **frequency** for igual a "week" ou "month", a agenda é executada um dia por semana ou um dia por mês apenas, respetivamente. |
| `{"minutes":[15]}` | Executar de hora a hora, 15 minutos após a hora. Este acionador é executado de hora a hora, 15 minutos após a hora, começando às 00:15, 1:15, 2:15 e assim sucessivamente até terminar às 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Executar às 17:00 de sábado todas as semanas. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17:00 de segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17:15 e 17:45 de segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar de 15 em 15 minutos nos dias de semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar de 15 em 15 minutos nos dias de semana, entre as 9:00 e as 16:45. |
| `{"weekDays":["tuesday", "thursday"]}` | Executar às terças-feiras e quintas-feiras à hora de início especificada. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Executar às 6:00 do dia 28 de cada mês (pressupondo um valor **frequency** igual a "month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Executar às 6:00 no último dia do mês. Para executar um acionador no último dia de um mês, utilize -1 em vez de dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Executar às 6:00 no primeiro e último dias de cada mês. |
| `{monthDays":[1,14]}` | Executar no primeiro e 14.º dias de cada mês à hora de início especificada. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês às 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês à hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Executar na terceira sexta-feira do fim do mês, todos os meses, à hora de início especificada. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sextas-feiras de cada mês às 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sextas-feiras de cada mês à hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Executar na quinta sexta-feira de cada mês à hora de início especificada. Quando um mês não tiver cinco sextas-feiras, o pipeline não é executado, uma vez que está agendado para ser executado apenas à quinta sexta-feira. Para executar o acionador na última sexta-feira do mês, considere utilizar -1 em vez de 5 para o valor **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Executar de 15 em 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Executar às 5:15, 5:45, 17:15 e 17:45 na terceira quarta-feira de cada mês. |


## <a name="next-steps"></a>Passos seguintes
Para obter informações detalhadas sobre gatilhos, consulte [execução de pipeline e gatilhos](concepts-pipeline-execution-triggers.md#triggers).

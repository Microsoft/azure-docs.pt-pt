---
title: Publicar oleodutos ML
titleSuffix: Azure Machine Learning
description: Executar fluxos de trabalho de aprendizagem automática com oleodutos de aprendizagem automática e o Azure Machine Learning SDK para Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: efedb21a1ec1ed53a8c6bfadf337d23a89c04383
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520181"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publicar e rastrear gasodutos de aprendizagem de máquinas



Este artigo irá mostrar-lhe como partilhar um oleoduto de aprendizagem automática com os seus colegas ou clientes.

Os gasodutos de aprendizagem automática são fluxos de trabalho reutilizáveis para tarefas de aprendizagem automática. Um dos benefícios dos gasodutos é o aumento da colaboração. Também pode verr os oleodutos, permitindo que os clientes utilizem o modelo atual enquanto está a trabalhar numa nova versão. 

## <a name="prerequisites"></a>Pré-requisitos

* Crie um espaço de trabalho para [aprendizagem automática Azure](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline

* [Configure o seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK, ou use uma instância [computacional de aprendizagem automática Azure](concept-compute-instance.md) com o SDK já instalado

* Crie e executar um pipeline de aprendizagem automática, como por exemplo seguindo [Tutorial: Construa um oleoduto Azure Machine Learning para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md). Para outras opções, consulte [Criar e executar pipelines de aprendizagem automática com Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)

## <a name="publish-a-pipeline"></a>Publicar um oleoduto

Uma vez que tenha um oleoduto em funcionamento, pode publicar um oleoduto para que este corra com diferentes entradas. Para que o ponto final do ME de um oleoduto já publicado aceite parâmetros, deve configurar o seu oleoduto para utilizar `PipelineParameter` objetos para os argumentos que variarão.

1. Para criar um parâmetro de pipeline, utilize um objeto [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter) com um valor predefinido.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adicione este `PipelineParameter` objeto como parâmetro a qualquer um dos passos na tubagem da seguinte forma:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publique este oleoduto que aceitará um parâmetro quando invocado.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Executar um oleoduto publicado

Todos os oleodutos publicados têm um ponto final REST. Com o ponto final do gasoduto, pode desencadear uma corrida do oleoduto a partir de quaisquer sistemas externos, incluindo clientes não-Python. Este ponto final permite a "repetibilidade gerida" em cenários de pontuação e reconversão de lotes.

> [!IMPORTANT]
> Se estiver a utilizar o controlo de acesso baseado em funções (Azure RBAC) para gerir o acesso ao seu pipeline, [desajeitar as permissões para o seu cenário de pipeline (treino ou pontuação)](how-to-assign-roles.md#common-scenarios).

Para invocar o funcionamento do oleoduto anterior, precisa de um sinal de cabeçalho de autenticação Azure Ative. Obter tal token é descrito na referência da [classe AzureCliAuthentication](/python/api/azureml-core/azureml.core.authentication.azurecliauthentication) e na autenticação no caderno [Azure Machine Learning.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

O `json` argumento do pedido DOM deve conter, para a `ParameterAssignments` chave, um dicionário que contenha os parâmetros do gasoduto e os seus valores. Além disso, o `json` argumento pode conter as seguintes teclas:

| Chave | Descrição |
| --- | --- | 
| `ExperimentName` | O nome da experiência associada a este ponto final |
| `Description` | Texto freeform descrevendo o ponto final | 
| `Tags` | Pares de valor-chave freeform que podem ser usados para rotular e anotar pedidos  |
| `DataSetDefinitionValueAssignments` | Dicionário utilizado para alterar conjuntos de dados sem requalificação (ver discussão abaixo) | 
| `DataPathAssignments` | Dicionário utilizado para alterar datapaths sem requalificação (ver discussão abaixo) | 

### <a name="run-a-published-pipeline-using-c"></a>Executar um oleoduto publicado usando C # 

O seguinte código mostra como chamar um gasoduto assíncronamente de C#. O corte parcial do código apenas mostra a estrutura de chamada e não faz parte de uma amostra da Microsoft. Não mostra aulas completas ou manipulação de erros. 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>Executar um oleoduto publicado usando Java

O código que se segue mostra uma chamada para um gasoduto que requer autenticação (ver [Configurar a autenticação para os recursos de aprendizagem automática Azure e fluxos de trabalho).](how-to-setup-authentication.md) Se o seu oleoduto for implantado publicamente, não precisa das chamadas que `authKey` produzem. O corte parcial do código não mostra a classe Java e a placa de tratamento de exceções. O código utiliza `Optional.flatMap` para acorrentar funções que podem devolver um vazio `Optional` . O uso de `flatMap` encurtamentos e clarifica o código, mas note que `getRequestBody()` engole exceções.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>Alteração de conjuntos de dados e datapats sem reconversão

Pode querer treinar e inferir diferentes conjuntos de dados e datapats. Por exemplo, pode desejar treinar num conjunto de dados mais pequeno, mas inferência no conjunto de dados completo. Troque os conjuntos de dados com `DataSetDefinitionValueAssignments` a chave no argumento do `json` pedido. Muda-se os caminhos de dados com `DataPathAssignments` . A técnica para ambos é semelhante:

1. No seu script de definição de pipeline, crie um `PipelineParameter` para o conjunto de dados. Criar a `DatasetConsumptionConfig` ou `DataPath` a partir `PipelineParameter` do:

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. No seu script ML, aceda ao conjunto de dados especificado dinamicamente utilizando `Run.get_context().input_datasets` :

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    Note que o script ML acede ao valor especificado para o `DatasetConsumptionConfig` ( ) e não o valor do ( `tabular_dataset` `PipelineParameter` `tabular_ds_param` ).

1. No seu roteiro de definição de pipeline, desafine o `DatasetConsumptionConfig` parâmetro `PipelineScriptStep` para:

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. Para mudar os conjuntos de dados de forma dinâmica na sua chamada DE DESCANSO inferencionante, `DataSetDefinitionValueAssignments` utilize:
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

Os cadernos [que mostram dataset e PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) e [Apresentando DataPath e PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) têm exemplos completos desta técnica.

## <a name="create-a-versioned-pipeline-endpoint"></a>Criar um ponto final de pipeline em versão

Pode criar um Pipeline Endpoint com vários oleodutos publicados por trás. Esta técnica dá-lhe um ponto final de REST fixo à medida que se itera e atualiza os seus gasodutos ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Submeter um trabalho a um ponto final de oleoduto

Pode submeter um trabalho à versão padrão de um ponto final do gasoduto:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Também pode submeter um trabalho a uma versão específica:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

O mesmo pode ser feito utilizando a API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Use oleodutos publicados no estúdio

Também pode executar um oleoduto publicado a partir do estúdio:

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. [Veja o seu espaço de trabalho.](how-to-manage-workspace.md#view)

1. À esquerda, selecione **Endpoints**.

1. Por cima, selecione **os pontos finais do Pipeline**.
 ![lista de oleodutos publicados machine learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Selecione um gasoduto específico para executar, consumir ou rever os resultados de execuções anteriores do ponto final do gasoduto.

## <a name="disable-a-published-pipeline"></a>Desativar um oleoduto publicado

Para esconder um oleoduto da sua lista de oleodutos publicados, desativa-o, quer no estúdio, quer no SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Pode voltar a ative-lo com `p.enable()` . Para mais informações, consulte a referência [da classe PublishedPipeline.](/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline)

## <a name="next-steps"></a>Passos seguintes

- Use [estes cadernos Jupyter no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais os oleodutos de aprendizagem automática.
- Consulte a ajuda de referência SDK para o pacote [de núcleo de gasodutos azureml](/python/api/azureml-pipeline-core/) e o pacote [de passos de gasodutos azureml.](/python/api/azureml-pipeline-steps/)
- Consulte o ["how-to"](how-to-debug-pipelines.md) para obter dicas sobre depuração e resolução de problemas.
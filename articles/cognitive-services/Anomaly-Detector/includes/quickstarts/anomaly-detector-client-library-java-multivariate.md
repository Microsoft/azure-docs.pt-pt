---
title: Início rápido da biblioteca de clientes java multivariado de detetor de anomalias
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: f2e227b2a589955191a2e602495cf0ffbb3f6d8b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732451"
---
Começa com a biblioteca multivariada do Detetor de Anomalias para a Java. Siga estes passos para instalar o pacote comece a usar os algoritmos fornecidos pelo serviço. As novas APIs de deteção de anomalias multivariadas permitem aos desenvolvedores integrar facilmente a IA avançada para detetar anomalias de grupos de métricas, sem necessidade de conhecimentos de aprendizagem automática ou dados rotulados. As dependências e as inter-correlações entre diferentes sinais são automaticamente contabilizadas como factores-chave. Isto ajuda-o a proteger proativamente os seus sistemas complexos de falhas.

Utilize a biblioteca multivariada do Detetor de Anomalias para a Java para:

* Detete anomalias de nível do sistema de um grupo de séries temporais.
* Quando qualquer série de tempo individual não lhe dirá muito e tem que olhar todos os sinais para detetar um problema.
* Manutenção pré-ativa de ativos físicos dispendiosos com dezenas a centenas de diferentes tipos de sensores medindo vários aspetos da saúde do sistema.

[Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Pacote (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle,](https://gradle.org/install/)ou outro gestor de dependência.
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de </a> Anomalias no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Este quickstart usa o gestor de dependência gradle. Pode encontrar mais informações sobre a biblioteca de clientes no [Repositório Central de Maven.](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o comando do seu diretório de `gradle init` trabalho. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts* que é usado em tempo de execução para criar e configurar a sua aplicação.

```console
gradle init --type basic
```

Quando solicitado para escolher um **DSL,** selecione **Kotlin**.

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Localize *build.gradle.kts* e abra-o com o seu IDE ou editor de texto preferido. Em seguida, copie nesta configuração de construção. Certifique-se de incluir as dependências do projeto.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Criar um ficheiro Java

Crie uma pasta para a sua aplicação de amostra. Do seu diretório de trabalho, executar o seguinte comando:

```console
mkdir -p src/main/java
```

Navegue para a nova pasta e crie um ficheiro chamado *MetricsAdvisorQuickstarts.java*. Abra-o no seu editor preferido ou IDE e adicione as `import` seguintes declarações:

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Crie variáveis no ponto final e chave Azure do seu recurso. Crie outra variável para o ficheiro de dados de exemplo.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Para usar as APIs multivariadas do Detetor de Anomalias, precisamos de treinar o nosso próprio modelo antes de usarmos a deteção. Os dados utilizados para o treino são um lote de séries de tempo, cada série de tempo deve estar em formato CSV com duas colunas, relógio e valor. Todas as séries horadas devem ser fechadas num ficheiro zip e ser enviadas para [o armazenamento da Azure Blob](../../../../storage/blobs/storage-blobs-introduction.md). Por predefinição, o nome do ficheiro será utilizado para representar a variável para a série de tempo. Alternativamente, um meta.jsextra no ficheiro pode ser incluído no ficheiro zip se desejar que o nome da variável seja diferente do nome de ficheiro .zip. Assim que gerarmos [URL BLOB SAS (Assinaturas de acesso partilhado), podemos](../../../../storage/common/storage-sas-overview.md)usar o url para o ficheiro zip para o treino.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes do Detetor de Anomalias para Node.js:

* [Autenticar o cliente](#authenticate-the-client)
* [Preparar um modelo](#train-a-model)
* [Detetar anomalias](#detect-anomalies)
* [Modelo de exportação](#export-model)
* [Eliminar modelo](#delete-model)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiar um `anomalyDetectorClient` objeto com o seu ponto final e credenciais.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Preparar um modelo

### <a name="construct-a-model-result-and-train-model"></a>Construa um modelo de resultado e modelo de comboio

Primeiro, temos de construir um pedido de modelo. Certifique-se de que o tempo de início e fim se alinha com a sua fonte de dados.

 Para usar as APIs multivariadas do Detetor de Anomalias, precisamos de treinar o nosso próprio modelo antes de usarmos a deteção. Os dados utilizados para o treino são um lote de séries de tempo, cada série de tempo deve estar em formato CSV com duas colunas, relógio e valor. Todas as séries horadas devem ser fechadas num ficheiro zip e ser enviadas para [o armazenamento da Azure Blob](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Por predefinição, o nome do ficheiro será utilizado para representar a variável para a série de tempo. Alternativamente, um meta.jsextra no ficheiro pode ser incluído no ficheiro zip se desejar que o nome da variável seja diferente do nome de ficheiro .zip. Assim que gerarmos [URL BLOB SAS (Assinaturas de acesso partilhado), podemos](../../../../storage/common/storage-sas-overview.md)usar o url para o ficheiro zip para o treino.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>Detetar anomalias

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>Modelo de exportação

Para exportar o seu modelo treinado use o `exportModelWithResponse` .

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Eliminar modelo

Para eliminar um modelo existente que esteja disponível para o recurso atual, utilize a `deleteMultivariateModelWithResponse` função.

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Executar a aplicação

Pode construir a aplicação com:

```console
gradle build
```
### <a name="run-the-application"></a>Executar a aplicação

Executar a aplicação com o `run` objetivo:

```console
gradle run
```

## <a name="next-steps"></a>Passos seguintes

* [As melhores práticas do Detetor de Anomalias](../../concepts/best-practices-multivariate.md)

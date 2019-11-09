---
title: 'Tutorial: seu primeiro experimento do ML com R'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você aprende os padrões de design básico em Azure Machine Learning e treina um modelo de modelo de regressão logística usando pacotes R azuremlsdk e cursor para prever a probabilidade de uma fatalização em um acidente de automóvel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 11/04/2019
ms.openlocfilehash: 9073893ebfcfea1a7f4e2a00bf82babcb8841122
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838526"
---
# <a name="tutorial-train-and-deploy-your-first-model-in-r-with-azure-machine-learning"></a>Tutorial: treinar e implantar seu primeiro modelo em R com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você aprende os padrões de design básico em Azure Machine Learning.  Você treinará e implantará um modelo de **cursor** para prever a probabilidade de uma fatalização em um acidente de automóvel. Depois de concluir este tutorial, você terá o conhecimento prático do SDK do R para escalar verticalmente até o desenvolvimento de experimentos e fluxos de trabalho mais complexos.

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Conectar seu espaço de trabalho
> * Carregue dados e prepare-se para treinamento
> * Carregar dados para o armazenamento do datastore para que ele esteja disponível para treinamento remoto
> * Criar um recurso de computação
> * Treinar um modelo de cursor para prever a probabilidade de fatalização
> * Implantar um ponto de extremidade de previsão
> * Testar o modelo do R

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

## <a name="prerequisites"></a>Pré-requisitos

1. Siga as [instruções de instalação](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) para:
    + Instalar o Anaconda
    + Instalar `azuremlsdk`
    + Instalar o SDK do Azure Machine Learning para Python

1. Obtenha os três arquivos de tutorial do [GitHub](https://github.com/Azure/azureml-sdk-for-r/tree/master/vignettes/train-and-deploy-with-caret).  Salve-os em um diretório de **tutoriais** .

2. Crie um espaço de trabalho Azure Machine Learning e baixe seu arquivo de configuração usando as etapas abaixo.


### <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de aprendizado de máquina. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no SDK. Se você já tiver um espaço de trabalho Azure Machine Learning, [pule para a próxima seção](#config). Caso contrário, crie um agora.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="config"></a>Baixar arquivo config. JSON

1. Na parte superior da página do espaço de trabalho, selecione **baixar config. JSON**.

    ![Baixar arquivo config. JSON](media/tutorial-1st-r-experiment/download-config.png)  

1. Adicione este arquivo ao seu diretório de **tutoriais** .

Agora você está pronto para executar o tutorial.

É recomendável usar o RStudio para executar este tutorial. Em RStudio, escolha Arquivo > novo projeto > diretório existente e selecione o diretório **tutoriais** que você criou acima.

> [!Important]
> O restante deste artigo contém o mesmo conteúdo que você vê no **Train-and-Deploy-to-ACI. Arquivo RMD** .  
> Se você tiver experiência com o RMarkdown, sinta-se à vontade para usar o código desse arquivo.  Ou você pode copiar/colar os trechos de código a partir daí, ou deste artigo em um script R ou na linha de comando.


## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento
A configuração do seu trabalho de desenvolvimento neste tutorial inclui as seguintes ações:

* Instalar pacotes necessários
* Conectar-se a um espaço de trabalho, para que seu computador local possa se comunicar com recursos remotos
* Criar um experimento para acompanhar suas execuções
* Criar um destino de computação remota a ser usado para treinamento

### <a name="install-required-packages"></a>Instalar pacotes necessários
Este tutorial pressupõe que você já tenha o SDK do Azure ML instalado. Vá em frente e importe o pacote **azuremlsdk** .

```R
library(azuremlsdk)
```

O tutorial usa dados do pacote [ **DAAG** ](https://cran.r-project.org/package=DAAG). Instale o pacote se você não o tiver.

```R
install.packages("DAAG")
```

Os scripts de treinamento e pontuação (`accidents.R` e `accident_predict.R`) têm algumas dependências adicionais. Se você planeja executar esses scripts localmente, verifique se você tem esses pacotes necessários também.

### <a name="load-your-workspace"></a>Carregar seu espaço de trabalho
Crie uma instância de um objeto de espaço de trabalho do espaço de trabalho existente. O código a seguir carregará os detalhes do espaço de trabalho do arquivo **config. JSON** . Você também pode recuperar um espaço de trabalho usando [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html).

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Criar uma experimentação
Um experimento do Azure ML rastreia um agrupamento de execuções, normalmente do mesmo script de treinamento. Crie um experimento para acompanhar as execuções para treinar o modelo de cursor nos dados de acidentes.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Criar um destino de computação
Usando o AmlCompute (computação Azure Machine Learning), um serviço gerenciado, cientistas de dados podem treinar modelos de aprendizado de máquina em clusters de máquinas virtuais do Azure. Exemplos incluem VMs com suporte a GPU. Neste tutorial, você cria um cluster AmlCompute de nó único como seu ambiente de treinamento. O código a seguir criará o cluster de computação para você se ele ainda não existir no seu espaço de trabalho.

Talvez seja necessário aguardar alguns minutos para que o cluster de computação seja provisionado se ele ainda não existir.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Preparar dados para treinamento
Este tutorial usa dados do pacote **DAAG** . Esse DataSet inclui dados de mais de 25.000 de falhas de carros nos EUA, com variáveis que você pode usar para prever a probabilidade de uma fatalidade. Primeiro, importe os dados para o R e transforme-os em um novo data frame `accidents` para análise e exporte-os para um arquivo `Rdata`.

```R
library(DAAG)
data(nassCDS)

accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Carregar dados no repositório de armazenamento
Carregue dados na nuvem para que ele possa ser acessado pelo seu ambiente de treinamento remoto. Cada espaço de trabalho do Azure ML é fornecido com um armazenamento de dados padrão que armazena as informações de conexão para o contêiner de blob do Azure que é provisionado na conta de armazenamento anexada ao espaço de trabalho. O código a seguir carregará os dados de acidentes que você criou acima para esse armazenamento.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Preparar um modelo

Para este tutorial, ajuste um modelo de regressão logística em seus dados carregados usando o cluster de computação remota. Para enviar um trabalho, você precisa:

* Preparar o script de treinamento
* Criar simulador
* Submeter o trabalho

### <a name="prepare-the-training-script"></a>Preparar o script de treinamento
Um script de treinamento chamado `accidents.R` foi fornecido para você no mesmo diretório que este tutorial. Observe os seguintes detalhes **dentro do script de treinamento** que foi feito para aproveitar o serviço do Azure ml para treinamento:

* O script de treinamento usa um argumento `-d` para localizar o diretório que contém os dados de treinamento. Quando você definir e enviar seu trabalho mais tarde, aponte para o repositório de armazenamento para esse argumento. O Azure ML montará a pasta de armazenamento para o cluster remoto para o trabalho de treinamento.
* O script de treinamento registra a precisão final como uma métrica para o registro de execução no Azure ML usando `log_metric_to_run()`. O SDK do Azure ML fornece um conjunto de APIs de log para registrar várias métricas durante execuções de treinamento. Essas métricas são registradas e mantidas no registro de execução do experimento. As métricas podem então ser acessadas a qualquer momento ou exibidas na página de detalhes de execução no [Azure Machine Learning Studio](https://ml.azure.com). Consulte a [referência](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) para obter o conjunto completo de métodos de registro em log `log_*()`.
* O script de treinamento salva seu modelo em um diretório chamado **Outputs**. A pasta `./outputs` recebe tratamento especial pelo Azure ML. Durante o treinamento, os arquivos gravados em `./outputs` são carregados automaticamente para o registro de execução pelo Azure ML e mantidos como artefatos. Ao salvar o modelo treinado em `./outputs`, você poderá acessar e recuperar o arquivo de modelo mesmo depois que a execução terminar e você não tiver mais acesso ao seu ambiente de treinamento remoto.

### <a name="create-an-estimator"></a>Criar simulador

Um estimador do Azure ML encapsula as informações de configuração de execução necessárias para executar um script de treinamento no destino de computação. As execuções do ML do Azure são executadas como trabalhos em contêineres no destino de computação especificado. Por padrão, a imagem do Docker criada para seu trabalho de treinamento incluirá R, o SDK do ML do Azure e um conjunto de pacotes R comumente usados. Consulte a lista completa de pacotes padrão incluídos aqui.

Para criar o estimador, defina:

* O diretório que contém seus scripts necessários para treinamento (`source_directory`). Todos os arquivos nesse diretório são carregados para os nós de cluster para execução. O diretório deve conter o script de treinamento e todos os scripts adicionais necessários.
* O script de treinamento que será executado (`entry_script`).
* O destino de computação (`compute_target`), nesse caso, o cluster AmlCompute que você criou anteriormente.
* Os parâmetros necessários do script de treinamento (`script_params`). O Azure ML executará o script de treinamento como um script de linha de comando com `Rscript`. Neste tutorial, você especifica um argumento para o script, o ponto de montagem do diretório de dados, que pode ser acessado com `ds$path(target_path)`.
* Quaisquer dependências de ambiente necessárias para treinamento. A imagem padrão do Docker criada para treinamento já contém os três pacotes (`caret`, `e1071`e `optparse`) necessários no script de treinamento.  Portanto, você não precisa especificar informações adicionais. Se você estiver usando pacotes de R que não estão incluídos por padrão, use o parâmetro `cran_packages` do estimador para adicionar pacotes CRAN adicionais. Consulte a referência de [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) para obter o conjunto completo de opções configuráveis.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Enviar o trabalho no cluster remoto

Por fim, envie o trabalho para ser executado no cluster. `submit_experiment()` retorna um objeto de execução que você usa para fazer a interface com a execução. No total, a primeira execução leva **cerca de 10 minutos**. Mas para execuções posteriores, a mesma imagem do Docker é reutilizada, desde que as dependências do script não sejam alteradas.  Nesse caso, a imagem é armazenada em cache e o tempo de inicialização do contêiner é muito mais rápido.

```R
run <- submit_experiment(exp, est)
```

Você pode exibir os detalhes da execução no Visualizador do RStudio. Clicar no link "exibição da Web" fornecido levará você ao Azure Machine Learning Studio, onde você pode monitorar a execução na interface do usuário.

```R
view_run_details(run)
```

O treinamento do modelo ocorre em segundo plano. Aguarde até que o modelo tenha concluído o treinamento antes de executar mais código.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Você-e os colegas com acesso ao espaço de trabalho – podem enviar várias experiências em paralelo e o Azure ML demorará agendar as tarefas no cluster de computação. Você pode até mesmo configurar o cluster para escalar verticalmente para vários nós automaticamente e reduzir de volta quando não houver mais tarefas de computação na fila. Essa configuração é uma maneira econômica para que as equipes compartilhem recursos de computação.

## <a name="retrieve-training-results"></a>Recuperar resultados de treinamento
Depois que o modelo tiver terminado o treinamento, você poderá acessar os artefatos do trabalho que foram persistidos no registro de execução, incluindo quaisquer métricas registradas e o modelo treinado final.

### <a name="get-the-logged-metrics"></a>Obter as métricas registradas
No script de treinamento `accidents.R`, você registrou uma métrica de seu modelo: a precisão das previsões nos dados de treinamento. Você pode ver as métricas no [estúdio](https://ml.azure.com)ou extraí-las para a sessão local como uma lista de R da seguinte maneira:

```R
metrics <- get_run_metrics(run)
metrics
```

Se você tiver executado vários experimentos (digamos, usando variáveis diferentes, algoritmos ou hiperparamrs), poderá usar as métricas de cada execução para comparar e escolher o modelo que será usado na produção.

### <a name="get-the-trained-model"></a>Obter o modelo treinado
Você pode recuperar o modelo treinado e examinar os resultados em sua sessão do R local. O código a seguir baixará o conteúdo do diretório `./outputs`, que inclui o arquivo de modelo.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Você vê alguns fatores que contribuem para um aumento na probabilidade estimada da morte:

* maior velocidade de impacto 
* Driver masculino
* occupant mais antigo
* passageiros

Você vê as probabilidades menores de morte com:

* presença de bolsas de Tróia
* seatbelts de presença
* colisão de frontais 

O ano do veículo de fabricação não tem um efeito significativo.

Você pode usar esse modelo para fazer novas previsões:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Implantar como um serviço Web

Com seu modelo, você pode prever o perigo de morte de uma colisão. Use o Azure ML para implantar seu modelo como um serviço de previsão. Neste tutorial, você implantará o serviço Web em ACI ( [instâncias de contêiner do Azure](https://docs.microsoft.com/en-us/azure/container-instances/) ).

### <a name="register-the-model"></a>Registrar o modelo

Primeiro, registre o modelo que você baixou em seu espaço de trabalho com [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html). Um modelo registrado pode ser qualquer coleção de arquivos, mas, nesse caso, o objeto de modelo R é suficiente. O Azure ML usará o modelo registrado para implantação.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definir as dependências de inferência
Para criar um serviço Web para seu modelo, primeiro você precisa criar um script de Pontuação (`entry_script`), um script R que utilizará como valores de variável de entrada (no formato JSON) e produzirá uma previsão de seu modelo. Para este tutorial, use o arquivo de Pontuação fornecido `accident_predict.R`. O script de Pontuação deve conter um método `init()` que carrega seu modelo e retorna uma função que usa o modelo para fazer uma previsão com base nos dados de entrada. Consulte a [documentação](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) para obter mais detalhes.

Em seguida, defina um **ambiente** do Azure ml para as dependências de pacote do script. Com um ambiente, você especifica pacotes de R (de CRAN ou outro lugar) que são necessários para que o script seja executado. Você também pode fornecer os valores das variáveis de ambiente que seu script pode referenciar para modificar seu comportamento. Por padrão, o Azure ML criará a mesma imagem padrão do Docker usada com o estimador para treinamento. Como o tutorial não tem requisitos especiais, crie um ambiente sem atributos especiais.

```R
r_env <- r_environment(name = "basic_env")
```

Se você quiser usar sua própria imagem do Docker para implantação, especifique o parâmetro `custom_docker_image`. Consulte a referência de [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) para obter o conjunto completo de opções configuráveis para definir um ambiente.

Agora você tem tudo o que precisa para criar uma **configuração de inferência** para encapsular seu script de Pontuação e dependências de ambiente.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Implementar para ACI
Neste tutorial, você implantará seu serviço no ACI. Esse código provisiona um único contêiner para responder às solicitações de entrada, o que é adequado para testes e cargas leves. Consulte [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) para obter opções adicionais configuráveis. (Para implantações em escala de produção, você também pode [implantar no serviço kubernetes do Azure](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html).)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Agora você implanta seu modelo como um serviço Web. A implantação **pode levar vários minutos**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testar o serviço implantado

Agora que seu modelo está implantado como um serviço, você pode testar o serviço do R usando [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html).  Forneça um novo conjunto de dados para prever, converta-o em JSON e envie-o para o serviço.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

Você também pode obter o ponto de extremidade HTTP do serviço Web, que aceita chamadas de cliente REST. Você pode compartilhar esse ponto de extremidade com qualquer pessoa que queira testar o serviço Web ou integrá-lo a um aplicativo.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Limpar recursos

Exclua os recursos quando não precisar mais deles. Não exclua nenhum recurso que você planeja ainda usar. 

Exclua o serviço Web:
```{r delete_service, eval=FALSE}
delete_webservice(aci_service)
```

Exclua o modelo registrado:
```{r delete_model, eval=FALSE}
delete_model(model)
```

Exclua o cluster de computação:
```{r delete_compute, eval=FALSE}
delete_compute(compute)
```

### <a name="delete-everything"></a>Excluir tudo

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único espaço de trabalho. Exiba as propriedades do espaço de trabalho e selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

Agora que você concluiu seu primeiro experimento Azure Machine Learning em R, saiba mais sobre o [SDK do Azure Machine Learning para R](https://azure.github.io/azureml-sdk-for-r/index.html).


---
title: 'Tutorial: Use R para criar um modelo de aprendizagem automática'
titleSuffix: Azure Machine Learning
description: Neste tutorial você usará o Azure Machine Learning R SDK para criar um modelo de regressão logística que preveja a probabilidade de uma fatalidade num acidente de carro.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 5b1c6561519bc25c2b7ac77f0a25eff89413a07a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81256489"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model"></a>Tutorial: Use R para criar um modelo de aprendizagem automática
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial você usará o Azure Machine Learning R SDK para criar um modelo de regressão logística que preveja a probabilidade de uma fatalidade num acidente de carro. Você verá como os recursos da nuvem Azure Machine Learning funcionam com R para proporcionar um ambiente escalável para a formação e implementação de um modelo.  

Neste tutorial, vai realizar as seguintes tarefas:
> [!div class="checklist"]
> * Criar um espaço de trabalho azure machine learning
> * Clone uma pasta de caderno com os ficheiros necessários para executar este tutorial no seu espaço de trabalho
> * Open RStudio do seu espaço de trabalho
> * Carregue dados e prepare-se para o treino
> * Faça upload de dados para uma loja de dados para que esteja disponível para treino remoto
> * Criar um recurso computacional para treinar o modelo remotamente
> * Treine um `caret` modelo para prever a probabilidade de fatalidade
> * Implementar um ponto final de previsão
> * Teste o modelo de R

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)


## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implementar modelos de aprendizagem automática. Liga o seu grupo de subscrição e recursos Azure a um objeto facilmente consumido no serviço. 

Cria um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Tome nota do seu espaço de **trabalho** e **subscrição.** Vai precisar disto para garantir que cria a sua experiência no lugar certo. 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>Clone uma pasta de caderno

Este exemplo utiliza o servidor de portátil em nuvem no seu espaço de trabalho para uma experiência sem instalação e pré-configurada. Use [o seu próprio ambiente](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) se preferir ter controlo sobre o seu ambiente, pacotes e dependências.

Completa as seguintes etapas de experimentação no estúdio Azure Machine Learning, uma interface consolidada que inclui ferramentas de machine learning para realizar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade.

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. Selecione **Cadernos** à esquerda.

1. Abra a pasta **Amostras.**

1. Abra a pasta **R.**

1. Abra a pasta com um número de versão.  Este número representa a versão atual para o R SDK.

1. Selecione o **"..."** à direita da pasta de **vinhetas** e, em seguida, selecione **Clone**.

    ![Pasta clone](media/tutorial-1st-r-experiment/clone-folder.png)

1. Uma lista de exibições de pastas mostrando cada utilizador que acede ao espaço de trabalho.  Selecione a pasta para clonar a pasta de **vinhetas.**

## <a name="a-nameopenopen-rstudio"></a><a name="open">Open RStudio

Use o RStudio numa instância de cálculo ou VM portátil para executar este tutorial.  

1. Selecione **Compute** à esquerda.

1. Adicione um recurso computacional se não existir.

1. Uma vez que o computado esteja em execução, use o link **RStudio** para abrir o RStudio.

1. No RStudio, a pasta de *vinhetas* está a alguns níveis abaixo dos *Utilizadores* na secção **Ficheiros** na parte inferior direita.  Em *vinhetas,* selecione a pasta *train-and-deploy-to-aci* para encontrar os ficheiros necessários neste tutorial.

> [!Important]
> O resto deste artigo contém o mesmo conteúdo que se vê no *comboio-e-deploy-to-aci. Ficheiro RMD.* Se tiver experiência com RMarkdown, sinta-se à vontade para usar o código desse ficheiro.  Ou pode copiar/colar os fragmentos de código a partir daí, ou a partir deste artigo para um script R ou a linha de comando.  


## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento
A configuração para o seu trabalho de desenvolvimento neste tutorial inclui as seguintes ações:

* Instalar pacotes necessários
* Ligue-se a um espaço de trabalho, para que a sua instância de cálculo possa comunicar com recursos remotos
* Crie uma experiência para rastrear as suas corridas
* Criar um alvo de computação remota para usar para o treino

### <a name="install-required-packages"></a>Instalar pacotes necessários

 * Instale a versão mais recente a partir de CRAN.

    ```R
    # install the latest version from CRAN
    install.packages("azuremlsdk")
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```
    
* Ou instale a versão de desenvolvimento do GitHub.

    ```R
    # or install the development version from GitHub
    remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```

Agora vá em frente e importe o pacote **azuremlsdk.**

```R
library(azuremlsdk)
```

Os guiões de treino`accidents.R` `accident_predict.R`e pontuação (e ) têm algumas dependências adicionais. Se planeia executar esses scripts localmente, certifique-se de que também tem os pacotes necessários.

### <a name="load-your-workspace"></a>Carregue o seu espaço de trabalho
Instanteire um objeto espaço de trabalho do seu espaço de trabalho existente. O código seguinte carregará os detalhes do espaço de trabalho a partir do ficheiro **config.json.** Também pode recuperar um [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html)espaço de trabalho utilizando .

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Criar uma experimentação
Uma experiência Azure ML rastreia um agrupamento de corridas, tipicamente a partir do mesmo roteiro de treino. Crie uma experiência para acompanhar as corridas para treinar o modelo de cuidados nos dados dos acidentes.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Criar um alvo de computação
Ao utilizar a Azure Machine Learning Compute (AmlCompute), um serviço gerido, os cientistas de dados podem treinar modelos de aprendizagem automática em clusters de máquinas virtuais Azure. Exemplos incluem VMs com suporte de GPU. Neste tutorial, você cria um cluster AmlCompute de nó único como seu ambiente de treino. O código abaixo cria o cluster de cálculo para si se já não existir no seu espaço de trabalho.

Poderá ter de esperar alguns minutos para que o seu cluster de cálculo seja provisionado se já não existir.

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

## <a name="prepare-data-for-training"></a>Preparar dados para a formação
Este tutorial utiliza dados da [Administração Nacional](https://cdan.nhtsa.gov/tsftables/tsfar.htm) de Segurança rodoviária dos EUA (graças a [Mary C. Meyer e Tremika Finney).](https://www.stat.colostate.edu/~meyer/airbags.htm)
Este conjunto de dados inclui dados de mais de 25.000 acidentes de carro nos EUA, com variáveis que pode usar para prever a probabilidade de uma fatalidade. Em primeiro lugar, importe os dados em `accidents` R e transforme-os num novo quadro de dados para análise, e exporte-os para um `Rdata` ficheiro.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Upload de dados para a loja de dados
Faça o upload dos dados para a nuvem para que possa ter acesso pelo seu ambiente de treino remoto. Cada espaço de trabalho azure machine learning vem com uma loja de dados padrão que armazena as informações de ligação ao recipiente de blob Azure que está aprovisionado na conta de armazenamento anexada ao espaço de trabalho. O código seguinte irá enviar os dados de acidentes que criou acima para aquela loja de dados.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Preparar um modelo

Para este tutorial, coloque um modelo de regressão logística nos seus dados carregados utilizando o seu cluster de computação remota. Para submeter um emprego, precisa de:

* Prepare o roteiro de treino
* Criar simulador
* Submeter o trabalho

### <a name="prepare-the-training-script"></a>Prepare o roteiro de treino
Foi-lhe `accidents.R` fornecido um guião de treino no mesmo diretório que este tutorial. Note os seguintes detalhes **dentro do roteiro** de treino que foram feitos para alavancar o Azure Machine Learning para formação:

* O roteiro de `-d` treino requer um argumento para encontrar o diretório que contém os dados de treino. Quando define e submete o seu trabalho mais tarde, aponta para a loja de dados para este argumento. O Azure ML montará a pasta de armazenamento no cluster remoto para o trabalho de formação.
* O script de treino regista a precisão final como métrica `log_metric_to_run()`para o registo de execução em Azure ML utilizando . O Azure ML SDK fornece um conjunto de APIs de exploração de madeira para registar várias métricas durante os treinos. Estas métricas são registadas e persistidas no registo de execução da experiência. As métricas podem então ser acedidas a qualquer momento ou vistas na página de detalhes de execução no [estúdio](https://ml.azure.com). Consulte a [referência](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) para o `log_*()`conjunto completo de métodos de exploração madeireira .
* O guião de treino salva o seu modelo num diretório chamado **outputs**. A `./outputs` pasta recebe tratamento especial pelo Azure ML. Durante o treino, `./outputs` os ficheiros escritos são automaticamente enviados para o seu registo de execução pelo Azure ML e persistiram como artefactos. Ao guardar o `./outputs`modelo treinado para, poderá aceder e recuperar o seu ficheiro modelo mesmo depois de terminar a corrida e já não ter acesso ao seu ambiente de treino remoto.

### <a name="create-an-estimator"></a>Criar simulador

Um estimador Azure ML encapsula as informações de configuração de execução necessárias para executar um script de treino no alvo da computação. As corridas do Azure ML são executadas como trabalhos contentorizados no objetivo de cálculo especificado. Por padrão, a imagem do Docker construída para o seu trabalho de formação incluirá R, o Azure ML SDK, e um conjunto de pacotes R comumente usados. Consulte a lista completa dos pacotes por defeito incluídos aqui.

Para criar o estimador, defina:

* O diretório que contém os seus`source_directory`scripts necessários para o treino ( ). Todos os ficheiros deste diretório são enviados para o nó do cluster para execução. O diretório deve conter o seu roteiro de treino e quaisquer scripts adicionais necessários.
* O roteiro de treino que`entry_script`será executado .
* O alvo computacional (`compute_target`), neste caso, o cluster AmlCompute que criou anteriormente.
* Os parâmetros exigidos a`script_params`partir do script de treino ( ). O Azure ML executará o seu guião de treino como um guião de linha de comando com `Rscript`. Neste tutorial especifica-se um argumento para o script, o ponto `ds$path(target_path)`de montagem do diretório de dados, ao qual pode aceder .
* Quaisquer dependências ambientais necessárias para o treino. A imagem padrão do Docker construída para`caret`o `e1071`treino `optparse`já contém os três pacotes ( , e ) necessários no roteiro de treino.  Então não precisa especificar informações adicionais. Se estiver a utilizar pacotes R que não estejam incluídos `cran_packages` por defeito, utilize o parâmetro do estimador para adicionar pacotes CRAN adicionais. Consulte [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) a referência para o conjunto completo de opções configuráveis.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Submeta o trabalho no aglomerado remoto

Finalmente submeta o trabalho para correr no seu cluster. `submit_experiment()`devolve um objeto Executar que depois utiliza para interagir com o executado. No total, a primeira corrida leva cerca de **10 minutos.** Mas para mais tarde, a mesma imagem do Docker é reutilizada desde que as dependências do guião não mudem.  Neste caso, a imagem está em cache e o tempo de arranque do recipiente é muito mais rápido.

```R
run <- submit_experiment(exp, est)
```

Pode ver os detalhes da corrida no RStudio Viewer. Clicar no link "Web View" fornecido irá levá-lo ao estúdio Azure Machine Learning, onde poderá monitorizar a execução na UI.

```R
view_run_details(run)
```

O treino de modelos acontece em segundo plano. Espere até que o modelo termine o treino antes de executar mais código.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Você e colegas com acesso ao espaço de trabalho podem submeter múltiplas experiências paralelas, e o Azure ML assumirá o agendamento das tarefas no cluster de computação. Pode até configurar o cluster para escalar automaticamente até vários nós, e escalar para trás quando não houver mais tarefas computadas na fila. Esta configuração é uma forma rentável para as equipas partilharem recursos computacionais.

## <a name="retrieve-training-results"></a>Recuperar resultados de treino
Uma vez que o seu modelo tenha terminado o treino, você pode aceder aos artefactos do seu trabalho que foram persistidos para o recorde de corrida, incluindo quaisquer métricas registadas e o modelo treinado final.

### <a name="get-the-logged-metrics"></a>Obtenha as métricas registadas
No roteiro `accidents.R`de treino, registou uma métrica do seu modelo: a precisão das previsões nos dados de treino. Pode ver métricas no [estúdio,](https://ml.azure.com)ou extraí-las para a sessão local como uma lista R da seguinte forma:

```R
metrics <- get_run_metrics(run)
metrics
```

Se tiver feito várias experiências (por exemplo, usando diferentes variáveis, algoritmos ou hiperparamers), pode utilizar as métricas de cada corrida para comparar e escolher o modelo que utilizará na produção.

### <a name="get-the-trained-model"></a>Obtenha o modelo treinado
Pode recuperar o modelo treinado e ver os resultados da sua sessão R local. O código seguinte irá descarregar `./outputs` os conteúdos do diretório, que inclui o ficheiro modelo.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Você vê alguns fatores que contribuem para um aumento na probabilidade estimada de morte:

* maior velocidade de impacto 
* condutor masculino
* ocupante mais velho
* passageiro

Vê-se menores probabilidades de morte com:

* presença de airbags
* cintos de segurança de presença
* colisão frontal 

A duração do fabrico do veículo não tem um efeito significativo.

Pode utilizar este modelo para fazer novas previsões:

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

## <a name="deploy-as-a-web-service"></a>Implementar como um serviço web

Com o seu modelo, pode prever o perigo de morte de uma colisão. Utilize o Azure ML para implementar o seu modelo como serviço de previsão. Neste tutorial, irá implementar o serviço web em Casos de [Contentores Azure](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Registar o modelo

Primeiro, registe o modelo que descarregou para o seu espaço de trabalho com [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html). Um modelo registado pode ser qualquer coleção de ficheiros, mas neste caso o objeto modelo R é suficiente. O Azure ML utilizará o modelo registado para a implantação.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definir as dependências de inferência
Para criar um serviço web para o seu modelo,`entry_script`primeiro precisa de criar um script de pontuação ( ), um script R que terá como valores variáveis de entrada (em formato JSON) e produz uma previsão do seu modelo. Para este tutorial, utilize o `accident_predict.R`ficheiro de pontuação fornecido . O script de pontuação deve conter um `init()` método que carregue o seu modelo e retorne uma função que utiliza o modelo para fazer uma previsão com base nos dados de entrada. Consulte a [documentação](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) para mais detalhes.

Em seguida, defina um **ambiente** Azure ML para as dependências do seu script. Com um ambiente, especifice pacotes R (de CRAN ou em qualquer outro lugar) que são necessários para que o seu script seja executado. Também pode fornecer os valores das variáveis ambientais que o seu script pode referir para modificar o seu comportamento. Por padrão, o Azure ML construirá a mesma imagem padrão do Docker utilizada com o estimador para treino. Uma vez que o tutorial não tem requisitos especiais, crie um ambiente sem atributos especiais.

```R
r_env <- r_environment(name = "basic_env")
```

Se quiser utilizar a sua própria imagem Docker `custom_docker_image` para ser implantada, especifique o parâmetro. Consulte [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) a referência para o conjunto completo de opções configuráveis para definir um ambiente.

Agora tens tudo o que precisas para criar uma **inferência** para encapsular o teu guião de pontuação e dependências ambientais.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Implementar para ACI
Neste tutorial, irá implementar o seu serviço para a ACI. Este código prevê um único recipiente para responder aos pedidos de entrada, adequados para testes e cargas leves. Consulte [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) opções configuradas adicionais. (Para implantações à escala de produção, também pode ser implantado para o [Serviço Azure Kubernetes](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html).)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Agora implementa o seu modelo como um serviço web. A implantação **pode demorar vários minutos**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testar o serviço implantado

Agora que o seu modelo está implantado como um serviço, pode testar o serviço a partir de R usando [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html).  Forneça um novo conjunto de dados para prever, convertê-lo para JSON, e enviá-lo para o serviço.

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

Também pode obter o ponto final http do serviço web, que aceita chamadas de clientes REST. Pode partilhar este ponto final com qualquer pessoa que queira testar o serviço web ou integrá-lo numa aplicação.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Limpar recursos

Apague os recursos uma vez que já não precise deles. Não apague nenhum recurso que planeie ainda utilizar. 

Eliminar o serviço web:
```R
delete_webservice(aci_service)
```

Eliminar o modelo registado:
```R
delete_model(model)
```

Eliminar o cluster de cálculo:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Apagar tudo

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Também pode manter o grupo de recursos, mas eliminar um único espaço de trabalho. Mostrar as propriedades do espaço de trabalho e selecionar **Apagar**.

## <a name="next-steps"></a>Passos seguintes

* Agora que completou a sua primeira experiência de Aprendizagem automática Azure em R, saiba mais sobre o [Azure Machine Learning SDK para R](https://azure.github.io/azureml-sdk-for-r/index.html).

* Saiba mais sobre o Azure Machine Learning com R a partir dos exemplos nas outras pastas de *vinhetas.*

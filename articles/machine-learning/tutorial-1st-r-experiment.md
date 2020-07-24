---
title: 'Tutorial: Use R para criar um modelo de aprendizagem automática (pré-visualização)'
titleSuffix: Azure Machine Learning
description: Neste tutorial você usará o Azure Machine Learning R SDK para criar um modelo de regressão logística que prevê a probabilidade de uma fatalidade em um acidente de carro.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 53b1c22e93c342373cae2bbb14958f4810a79630
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092325"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model-preview"></a>Tutorial: Use R para criar um modelo de aprendizagem automática (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!IMPORTANT]
> O Azure Machine Learning R SDK está atualmente em pré-visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Neste tutorial você usará o Azure Machine Learning R SDK (pré-visualização) para criar um modelo de regressão logística que prevê a probabilidade de uma fatalidade num acidente de carro. Você verá como os recursos em nuvem de aprendizagem da máquina Azure funcionam com R para fornecer um ambiente escalável para treinar e implementar um modelo.  

Neste tutorial, vai realizar as seguintes tarefas:
> [!div class="checklist"]
> * Criar um espaço de trabalho para aprendizagem de máquinas Azure
> * Clone uma pasta de caderno com os ficheiros necessários para executar este tutorial no seu espaço de trabalho
> * Abra o RStudio do seu espaço de trabalho
> * Carregar dados e preparar-se para a formação
> * Faça o upload de dados para uma datastore para que esteja disponível para treino remoto
> * Criar um recurso computacional para treinar o modelo remotamente
> * Treine um `caret` modelo para prever a probabilidade de fatalidade
> * Implementar um ponto final de previsão
> * Teste o modelo de R

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)


## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implementar modelos de machine learning. Liga a sua subscrição e grupo de recursos Azure a um objeto facilmente consumido no serviço. 

Cria um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Tome nota do seu espaço de **trabalho** e **subscrição.** Vai precisar disto para garantir que cria a sua experiência no lugar certo. 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>Clone uma pasta de caderno

Este exemplo utiliza o servidor de cadernos em nuvem no seu espaço de trabalho para uma experiência sem instalação e pré-configurada. Use [o seu próprio ambiente](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) se preferir ter controlo sobre o seu ambiente, pacotes e dependências.

Você completa os seguintes passos de configuração de experiências no estúdio Azure Machine Learning, uma interface consolidada que inclui ferramentas de machine learning para executar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/).

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. Selecione **Cadernos** à esquerda.

1. Abra a pasta **Samples.**

1. Abra a pasta **R.**

1. Abra a pasta com um número de versão.  Este número representa a versão atual para o R SDK.

1. Selecione o **"..."** à direita da pasta de **vinhetas** e, em seguida, selecione **Clone**.

    ![Pasta clone](media/tutorial-1st-r-experiment/clone-folder.png)

1. Uma lista de pastas mostra cada utilizador que acede ao espaço de trabalho.  Selecione a sua pasta para clonar a pasta **de vinhetas** lá.

## <a name="open-rstudio"></a><a name="open"></a>Open RStudio

Utilize o RStudio numa instância de computação ou VM de portátil para executar este tutorial.  

1. **Selecione Compute** à esquerda.

1. Adicione um recurso de cálculo se já não existir.

1. Uma vez que o cálculo esteja em funcionamento, use a ligação **RStudio** para abrir o RStudio.

1. No RStudio, a pasta das *vinhetas* está a alguns níveis abaixo dos *Utilizadores* na secção **Ficheiros** no direito inferior.  Sob *vinhetas,* selecione a pasta *train-and-deploy-to-aci* para encontrar os ficheiros necessários neste tutorial.

> [!Important]
> O resto deste artigo contém o mesmo conteúdo que se vê no *comboio-e-deploy-to-aci. Arquivo RMD.* Se tiver experiência com o RMarkdown, sinta-se à vontade para utilizar o código a partir desse ficheiro.  Ou pode copiar/colar os cortes de código de lá, ou deste artigo para um script R ou para a linha de comando.  


## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento
A configuração para o seu trabalho de desenvolvimento neste tutorial inclui as seguintes ações:

* Instalar pacotes necessários
* Conecte-se a um espaço de trabalho, para que a sua instância de computação possa comunicar com recursos remotos
* Crie uma experiência para acompanhar as suas corridas
* Criar um alvo de computação remota para usar para o treino

### <a name="install-required-packages"></a>Instalar pacotes necessários

 * Instale a versão mais recente da CRAN.

    ```R
    # install the latest version from CRAN
    install.packages("azuremlsdk")
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```
    
* Ou instalar a versão de desenvolvimento do GitHub.

    ```R
    # or install the development version from GitHub
    remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```

Agora vá em frente e importe o pacote **de azuremlsdk.**

```R
library(azuremlsdk)
```

Os scripts de treino e pontuação `accidents.R` `accident_predict.R` (e) têm algumas dependências adicionais. Se planeia executar esses scripts localmente, certifique-se de que também tem os pacotes necessários.

### <a name="load-your-workspace"></a>Carregue o seu espaço de trabalho
Instantiizar um objeto de espaço de trabalho do seu espaço de trabalho existente. O seguinte código carregará os detalhes do espaço de trabalho a partir do **config.jsarquivado.** Também pode recuperar um espaço de trabalho utilizando [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html) .

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Criar uma experimentação
Uma experiência Azure ML rastreia um agrupamento de corridas, tipicamente a partir do mesmo script de treino. Crie uma experiência para acompanhar as corridas para treinar o modelo de cuidado nos dados dos acidentes.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Criar um alvo de computação
Ao utilizar o Azure Machine Learning Compute (AmlCompute), um serviço gerido, os cientistas de dados podem treinar modelos de aprendizagem automática em grupos de máquinas virtuais Azure. Exemplos incluem VMs com suporte GPU. Neste tutorial, você cria um cluster AmlCompute de nó único como seu ambiente de treino. O código abaixo cria o cluster de cálculo para si se já não existir no seu espaço de trabalho.

Pode ter de esperar alguns minutos para que o seu cluster de cálculo seja a provisionado se já não existir.

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
Este conjunto de dados inclui dados de mais de 25.000 acidentes de carro nos EUA, com variáveis que pode usar para prever a probabilidade de uma fatalidade. Em primeiro lugar, importe os dados em R e transforme-os num novo dataframe `accidents` para análise, e exporte-os para um `Rdata` ficheiro.

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

### <a name="upload-data-to-the-datastore"></a>Faça upload de dados para a datastore
Faça upload de dados para a nuvem para que possa ser acededed by your remote training environment. Cada espaço de trabalho Azure Machine Learning vem com uma datastore padrão que armazena a informação de conexão ao recipiente de blob Azure que é alojada na conta de armazenamento anexa ao espaço de trabalho. O código seguinte irá enviar os dados de acidentes que criou acima para a datastore.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Preparar um modelo

Para este tutorial, coloque um modelo de regressão logística nos seus dados carregados utilizando o seu cluster de computação remota. Para submeter um emprego, precisa:

* Preparar o roteiro de treino
* Criar simulador
* Submeter o trabalho

### <a name="prepare-the-training-script"></a>Preparar o roteiro de treino
Um roteiro de treino chamado `accidents.R` foi fornecido para si no mesmo diretório que este tutorial. Note os seguintes detalhes **dentro do roteiro de treino** que foram feitos para alavancar a Azure Machine Learning para a formação:

* O roteiro de formação requer um argumento `-d` para encontrar o diretório que contém os dados de treino. Quando definir e submeter o seu trabalho mais tarde, você aponta para a datastore para este argumento. A Azure ML montará a pasta de armazenamento no cluster remoto para o trabalho de treino.
* O script de treino regista a precisão final como uma métrica ao registo de execução em Azure ML usando `log_metric_to_run()` . O Azure ML SDK fornece um conjunto de APIs de registo para registar várias métricas durante os treinos. Estas métricas são registadas e persistiu no registo de experiências. As métricas podem então ser acedidas a qualquer momento ou visualizadas na página de detalhes de execução em [estúdio](https://ml.azure.com). Consulte a [referência](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) para o conjunto completo de métodos de registo `log_*()` .
* O script de treino guarda o seu modelo num diretório chamado **outputs**. A `./outputs` pasta recebe tratamento especial por Azure ML. Durante o treino, os ficheiros escritos `./outputs` são automaticamente enviados para o seu registo de execução pela Azure ML e persistidos como artefactos. Ao guardar o modelo treinado `./outputs` para, poderá aceder e recuperar o seu ficheiro de modelo mesmo depois de terminado o percurso e já não tem acesso ao seu ambiente de treino remoto.

### <a name="create-an-estimator"></a>Criar simulador

Um estimador Azure ML encapsula as informações de configuração de execução necessárias para executar um script de treino no alvo do cálculo. As corridas de Azure ML são executadas como trabalhos contentorizados no objetivo de computação especificado. Por padrão, a imagem docker construída para o seu trabalho de treino incluirá R, o Azure ML SDK, e um conjunto de pacotes R comumente usados. Consulte a lista completa de pacotes predefinidos incluídos aqui.

Para criar o estimador, defina:

* O diretório que contém os seus scripts necessários para a formação `source_directory` (). Todos os ficheiros deste diretório são enviados para o nó(s) do cluster para execução. O diretório deve conter o seu roteiro de treino e quaisquer scripts adicionais necessários.
* O roteiro de treino que será executado `entry_script` ().
* O alvo do cálculo ( `compute_target` ), neste caso, o cluster AmlCompute que criou anteriormente.
* Os parâmetros exigidos a partir do roteiro de treino `script_params` (). A Azure ML executará o seu script de treino como um script de linha de comando com `Rscript` . Neste tutorial especifica-se um argumento para o script, o ponto de montagem do diretório de dados, ao qual pode aceder `ds$path(target_path)` .
* Quaisquer dependências ambientais necessárias para o treino. A imagem padrão de Docker construída para o treino já contém os três pacotes `caret` `e1071` (, e `optparse` ) necessários no script de treino.  Então não precisa de especificar informações adicionais. Se estiver a utilizar pacotes R que não estejam incluídos por predefinição, utilize o parâmetro do estimador `cran_packages` para adicionar pacotes CRAN adicionais. Consulte a [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) referência para o conjunto completo de opções configuráveis.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Submeta o trabalho no cluster remoto

Finalmente submeta o trabalho para correr no seu agrupamento. `submit_experiment()`retorna um objeto Run que depois utiliza para interagir com a execução. No total, a primeira corrida demora **cerca de 10 minutos.** Mas para execuções posteriores, a mesma imagem do Docker é reutilizada desde que as dependências do guião não mudem.  Neste caso, a imagem é em cache e o tempo de arranque do contentor é muito mais rápido.

```R
run <- submit_experiment(exp, est)
```

Pode ver os detalhes da execução no RStudio Viewer. Clicar no link "Web View" fornecido irá levá-lo ao estúdio Azure Machine Learning, onde pode monitorizar a execução na UI.

```R
view_run_details(run)
```

O treino de modelo acontece em segundo plano. Espere até que o modelo termine o treino antes de executar mais código.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Você e colegas com acesso ao espaço de trabalho podem submeter múltiplas experiências em paralelo, e Azure ML vai assumir o agendamento das tarefas no cluster compute. Pode até configurar o cluster para escalar automaticamente até múltiplos nós e reduzir a escala quando não houver mais tarefas de computação na fila. Esta configuração é uma forma rentável para as equipas partilharem recursos computativos.

## <a name="retrieve-training-results"></a>Recuperar resultados de formação
Uma vez terminado o treino, o seu modelo pode aceder aos artefactos do seu trabalho que foram persistidos ao registo de execução, incluindo quaisquer métricas registadas e o modelo treinado final.

### <a name="get-the-logged-metrics"></a>Obtenha as métricas registadas
No roteiro de `accidents.R` treino, registou uma métrica do seu modelo: a precisão das previsões nos dados de treino. Pode ver métricas no [estúdio,](https://ml.azure.com)ou extraí-las para a sessão local como uma lista R da seguinte forma:

```R
metrics <- get_run_metrics(run)
metrics
```

Se tiver feito várias experiências (por exemplo, usando variáveis diferentes, algoritmos ou hiperparameros), pode usar as métricas de cada corrida para comparar e escolher o modelo que vai usar na produção.

### <a name="get-the-trained-model"></a>Obtenha o modelo treinado
Pode recuperar o modelo treinado e ver os resultados na sua sessão R local. O seguinte código irá descarregar o conteúdo do `./outputs` diretório, que inclui o ficheiro modelo.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Vê-se alguns fatores que contribuem para um aumento da probabilidade estimada de morte:

* maior velocidade de impacto 
* condutor masculino
* ocupante mais velho
* passageiro

Vê-se menores probabilidades de morte com:

* presença de airbags
* cintos de segurança presença
* colisão frontal 

O ano de fabrico do veículo não tem um efeito significativo.

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

Com o seu modelo, pode prever o perigo de morte por uma colisão. Utilize a Azure ML para implementar o seu modelo como um serviço de previsão. Neste tutorial, irá implementar o serviço web em [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Registar o modelo

Primeiro, registe o modelo com o quais descarregou para o seu espaço de [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html) trabalho. Um modelo registado pode ser qualquer recolha de ficheiros, mas neste caso o objeto do modelo R é suficiente. A Azure ML utilizará o modelo registado para implantação.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definir as dependências de inferência
Para criar um serviço web para o seu modelo, primeiro precisa de criar um script de pontuação ( `entry_script` ), um script R que irá tomar como valores variáveis de entrada (em formato JSON) e produzir uma previsão do seu modelo. Para este tutorial, utilize o ficheiro de pontuação fornecido `accident_predict.R` . O script de pontuação deve conter um `init()` método que carrega o seu modelo e devolve uma função que usa o modelo para fazer uma previsão com base nos dados de entrada. Consulte a [documentação](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) para mais detalhes.

Em seguida, defina um **ambiente** Azure ML para as dependências do pacote do seu script. Com um ambiente, você especifica pacotes R (de CRAN ou em qualquer outro lugar) que são necessários para que o seu script seja executado. Também pode fornecer os valores de variáveis ambientais que o seu script pode referenciar para modificar o seu comportamento. Por padrão, a Azure ML construirá a mesma imagem padrão do Docker usada com o estimador para o treino. Uma vez que o tutorial não tem requisitos especiais, crie um ambiente sem atributos especiais.

```R
r_env <- r_environment(name = "basic_env")
```

Se quiser utilizar a sua própria imagem Docker para implantação, especifique o `custom_docker_image` parâmetro. Consulte a [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) referência para o conjunto completo de opções configuráveis para definir um ambiente.

Agora tens tudo o que precisas para criar uma **inferência config** para encapsular o teu script de pontuação e dependências ambientais.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Implementar para ACI
Neste tutorial, irá utilizar o seu serviço para o ACI. Este código prevê um único recipiente para responder aos pedidos de entrada, adequado para testes e cargas ligeiras. Consulte [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) opções configuráveis adicionais. (Para implantações em escala de produção, também pode [ser implantado no Serviço Azure Kubernetes](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html).)

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

## <a name="test-the-deployed-service"></a>Teste o serviço implantado

Agora que o seu modelo é implantado como um serviço, pode testar o serviço a partir de R utilizando [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html) .  Forneça um novo conjunto de dados para prever, convertê-lo para JSON, e enviá-lo para o serviço.

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

Também pode obter o ponto final HTTP do serviço web, que aceita chamadas de cliente REST. Pode partilhar este ponto final com qualquer pessoa que queira testar o serviço web ou integrá-lo numa aplicação.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Limpar recursos

Apague os recursos uma vez que já não precise deles. Não elimine nenhum recurso que pretenda utilizar. 

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

Também pode manter o grupo de recursos, mas eliminar um único espaço de trabalho. Mostrar as propriedades do espaço de trabalho e selecionar **Delete**.

## <a name="next-steps"></a>Passos seguintes

* Agora que completou a sua primeira experiência de Azure Machine Learning em R, saiba mais sobre o [Azure Machine Learning SDK para R](https://azure.github.io/azureml-sdk-for-r/index.html).

* Saiba mais sobre Azure Machine Learning com R a partir dos exemplos nas *outras pastas de vinhetas.*

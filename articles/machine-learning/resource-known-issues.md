---
title: Questões conhecidas & resolução de problemas
titleSuffix: Azure Machine Learning
description: Obtenha uma lista dos problemas conhecidos, saroundaround sques e resolução de problemas para Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: eb8e06370ecbe2b104a19c4e420b5d3ae013a00e
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116320"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Questões conhecidas e resolução de problemas Azure Machine Learning

Este artigo ajuda-o a encontrar e corrigir erros ou falhas que poderá encontrar ao utilizar o Azure Machine Learning.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

Às vezes pode ser útil se puder fornecer informações de diagnóstico ao pedir ajuda. Para ver alguns troncos: 
1. Visite o [estúdio Azure Machine Learning.](https://ml.azure.com) 
1. No lado esquerdo, selecione **Experiment** 
1. Selecione uma experiência.
1. Selecione uma corrida.
1. Em cima, selecione **Saídas + registos**.

> [!NOTE]
> O Azure Machine Learning regista informações de várias fontes durante o treino, como o AutoML ou o contentor Docker que executa o trabalho de formação. Muitos destes troncos não estão documentados. Se encontrar problemas e contactar o suporte da Microsoft, poderão utilizar estes registos durante a resolução de problemas.


## <a name="resource-quotas"></a>Quotas de recursos

Conheça as quotas de [recursos](how-to-manage-quotas.md) que pode encontrar ao trabalhar com a Azure Machine Learning.

## <a name="installation-and-import"></a>Instalação e importação

* **Mensagem de erro: Não pode desinstalar 'PyYAML'**

    Azure Machine Learning SDK para Python: `distutils` PyYAML é um projeto instalado. Portanto, não podemos determinar com precisão quais os ficheiros que lhe pertencem se existe uma desinstalação parcial. Para continuar a instalar o SDK ignorando este erro, utilize:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Falha de tijolos de dados na instalação de pacotes**

    A instalação SDK de Aprendizagem automática Azure falha nos Tijolos de Dados Azure quando mais pacotes são instalados. Alguns pacotes, `psutil`como, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão da biblioteca. Esta questão está relacionada com os Databricks e não com o Azure Machine Learning SDK. Pode experimentar este problema com outras bibliotecas, também. Exemplo:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Em alternativa, pode utilizar scripts de init e de te colocar esforçadamente a instalar problemas com bibliotecas Python. Esta abordagem não é oficialmente apoiada. Para mais informações, consulte [scripts init](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)com mira de Cluster .

* Erro de importação de **databricks: não pode importar o nome 'Timedelta' de 'pandas._libs.tslibs'**: Se vir este erro quando utilizar machine learning automatizado, faça as duas seguintes linhas no seu caderno:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* Erro de importação de **databricks: Nenhum módulo chamado 'pandas.core.indexes':** Se vir este erro quando utilizar machine learning automatizado:

    1. Execute este comando para instalar dois pacotes no seu cluster De Tijolos De Dados Azure:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Desaque e, em seguida, recoloque o cluster ao seu caderno.
    
    Se estes passos não resolverem o problema, tente reiniciar o cluster.

* **Databricks FailToSendFeather**: Se `FailToSendFeather` vir um erro ao ler dados sobre o cluster DeTijolos De Dados Azure, consulte as seguintes soluções:
    
    * Atualizar `azureml-sdk[automl]` pacote para a versão mais recente.
    * Adicione `azureml-dataprep` a versão 1.1.8 ou superior.
    * Adicione `pyarrow` a versão 0.11 ou superior.
    
* **Instalação pip: Dependecies não é garantido ser consistente com a instalação**de uma única linha : Esta é uma limitação conhecida do pip, uma vez que não tem uma dependência funcional quando instala como uma única linha. A primeira dependência única é a única que olha. Por exemplo, se estiver a instalar a deriva de dados azure-ml que requer a versão > 1.0 e o azureml-train-automl que requer versão < 1.2 e se a versão mais recente for 1.3, quando o utilizador instala as embalagens numa única linha, como mostrado abaixo, tudo é atualizado para 1.3, mesmo que o pacote azureml-train-automl exija uma versão mais antiga. 

    * Verá dependecies inconsistentes com instalação de linha única.
    ```python
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   
    * Para garantir que as versões apropriadas estão instaladas para as suas embalagens, instale utilizando várias linhas como no seguinte código. A ordem não importa aqui.
    
     ```python
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
## <a name="create-and-manage-workspaces"></a>Criar e gerir espaços de trabalho

> [!WARNING]
> A mudança do seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou a mudança da subscrição própria para um novo inquilino, não é suportada. Fazê-lo pode causar erros.

* **Portal Azure**: Se for diretamente ver o seu espaço de trabalho a partir de um link de partilha do SDK ou do portal, não poderá visualizar a página normal de **visão geral** com informações de subscrição na extensão. Também não poderá mudar para outro espaço de trabalho. Se precisar de ver outro espaço de trabalho, dirija-se diretamente ao [estúdio Azure Machine Learning](https://ml.azure.com) e procure o nome do espaço de trabalho.

## <a name="set-up-your-environment"></a>Configurar o ambiente

* **Problemas na criação do AmlCompute**: Existe uma rara hipótese de alguns utilizadores que criaram o seu espaço de trabalho Azure Machine Learning a partir do portal Azure antes do lançamento da GA não conseguirem criar amlCompute nesse espaço de trabalho. Pode levantar um pedido de apoio contra o serviço ou criar um novo espaço de trabalho através do portal ou do SDK para desbloquear imediatamente.

## <a name="work-with-data"></a>Trabalhar com dados

### <a name="overloaded-azurefile-storage"></a>Armazenamento AzureFile sobrecarregado

Se receber um `Unable to upload project files to working directory in AzureFile because the storage is overloaded`erro, aplique as seguintes suposições.

Se estiver a utilizar a partilha de ficheiros para outras cargas de trabalho, como a transferência de dados, a recomendação é utilizar bolhas para que a partilha de ficheiros seja livre de ser utilizada para a apresentação de execuções. Também pode dividir a carga de trabalho entre dois espaços de trabalho diferentes.

### <a name="passing-data-as-input"></a>Passar dados como entrada

*  **Erro de tipo: FileNotFound: Não existe tal ficheiro ou diretório**: Este erro ocorre se o caminho de ficheiro que fornece não estiver onde o ficheiro está localizado. Tem de se certificar de que a forma como se refere ao ficheiro é consistente com a forma como montou o seu conjunto de dados no seu alvo de cálculo. Para garantir um estado determinista, recomendamos a utilização do caminho abstrato ao montar um conjunto de dados para um alvo de cálculo. Por exemplo, no seguinte código montamos o conjunto de dados sob `/tmp`a raiz do sistema de ficheiros do alvo computacional, . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Se não incluir o principal corte dianteiro, '/', terá de pré-fixar o `/mnt/batch/.../tmp/dataset` diretório de trabalho, por exemplo, no alvo do cálculo para indicar onde pretende que o conjunto de dados seja montado.

### <a name="data-labeling-projects"></a>Projetos de rotulagem de dados

|Problema  |Resolução  |
|---------|---------|
|Apenas conjuntos de dados criados em lojas de dados blob podem ser usados     |  esta é uma limitação conhecida da versão atual.       |
|Após a criação, o projeto mostra "Inicialização" por muito tempo     | Refresque manualmente a página. A inicialização deve prosseguir em cerca de 20 pontos de dados por segundo. A falta de auto-actualização é uma questão conhecida.         |
|Ao rever as imagens, as imagens recentemente rotuladas não são mostradas     |   Para carregar todas as imagens etiquetadas, escolha **o** primeiro botão. O **primeiro** botão irá levá-lo de volta para a frente da lista, mas carrega todos os dados rotulados.      |
|Premir a tecla Esc enquanto se rotula para deteção de objetos cria uma etiqueta de tamanho zero no canto superior esquerdo. A apresentação de rótulos neste estado falha.     |   Elimine a etiqueta clicando na marca transversal ao lado.  |

## <a name="azure-machine-learning-designer"></a>Designer de aprendizagem automática Azure

Problemas conhecidos:

* Longo tempo de **preparação do cálculo**: Pode demorar alguns minutos ou até mais quando se conecta pela primeira vez ou cria um alvo de cálculo. 

## <a name="train-models"></a>Preparar modelos

* **MóduloErrors (Nenhum módulo nomeado)**: Se estiver a encontrar MóduloSErrors ao submeter experiências no Azure ML, significa que o script de treino está à espera de uma embalagem para ser instalada, mas não é adicionado. Assim que fornecer o nome do pacote, o Azure ML instalará o pacote no ambiente utilizado para o seu treino. 

    Se estiver a utilizar os Estimadores para submeter [experiências,](concept-azure-machine-learning-architecture.md#estimators) pode especificar um nome de pacote através `pip_packages` ou `conda_packages` parâmetro no estimador com base na fonte que pretende instalar a embalagem. Também pode especificar um ficheiro yml com `conda_dependencies_file`todas as suas dependências usando ou `pip_requirements_file` listando todos os seus requisitos de pip num ficheiro txt usando parâmetro. Se tiver o seu próprio objeto De Ambiente Azure ML que pretenda anular a imagem predefinida utilizada pelo estimador, pode especificar esse ambiente através do `environment` parâmetro do construtor estimador.

    O Azure ML também fornece estimativas específicas para a tensorflow, pyTorch, chainer e SKLearn. A utilização destes estimadores certificar-se-á de que as dependências do núcleo do quadro são instaladas em seu nome no ambiente utilizado para a formação. Tem a opção de especificar dependências extras acima descritas. 
 
    O Azure ML manteve imagens de estivadores e o seu conteúdo pode ser visto em [contentores AzureML](https://github.com/Azure/AzureML-Containers).
    As dependências específicas do quadro estão listadas na respetiva documentação-quadro - [Chainer,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks) [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks) [TensorFlow,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks) [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Se você acha que um pacote específico é comum o suficiente para ser adicionado em Imagens e ambientes mantidos Azure ML, por favor levante uma edição GitHub em [contentores AzureML](https://github.com/Azure/AzureML-Containers). 
 
* **Error name (Nome não definido), AttributeError (Objeto não tem atributo)**: Esta exceção deve vir dos seus scripts de treino. Pode ver os ficheiros de registo do portal Azure para obter mais informações sobre o nome específico não definido ou erro de atributo. A partir do SDK, pode usar `run.get_details()` para ver a mensagem de erro. Isto também listará todos os ficheiros de registo gerados para a sua execução. Por favor, certifique-se de dar uma olhada no seu script de treino e corrigir o erro antes de reenviar a sua execução. 

* **Horovod foi encerrado**: Na maioria dos casos, se encontrar "AbortedError: Horovod foi encerrado" esta exceção significa que houve uma exceção subjacente num dos processos que fez com que Horovod fosse encerrado. Cada posto no trabalho de MPI obtém o seu próprio ficheiro de log dedicado no Azure ML. Estes registos `70_driver_logs`são nomeados. Em caso de treino distribuído, os nomes `_rank` de registo são sufixos para facilitar a diferenciação dos registos. Para encontrar o erro exato que fez com que horovod desligasse, veja todos os ficheiros de registo e procure `Traceback` no final dos ficheiros driver_log. Um destes ficheiros vai dar-lhe a exceção subjacente real. 

* **Eliminação**de executar ou experimentar : As experiências podem ser arquivadas utilizando o método [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) ou a partir da vista do separador Experiment no cliente do estúdio Azure Machine Learning através do botão "Experiência de Arquivo". Esta ação esconde a experiência de consultas e pontos de vista da lista, mas não a apaga.

    A eliminação permanente de experiências ou execuções individuais não é atualmente suportada. Para obter mais informações sobre a eliminação de ativos do Workspace, consulte Exportar ou eliminar os dados do espaço de trabalho do [serviço de aprendizagem automática](how-to-export-delete-data.md).

* **O Documento Métrico é demasiado grande:** o Azure Machine Learning tem limites internos no tamanho de objetos métricos que podem ser registados ao mesmo tempo a partir de uma corrida de treino. Se encontrar um erro "Metric Document is too large" ao registar uma métrica avaliada pela lista, tente dividir a lista em pedaços menores, por exemplo:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internamente, o Azure ML concatena os blocos com o mesmo nome métrico numa lista contígua.

## <a name="automated-machine-learning"></a>Aprendizagem automática automatizada

* **Tensor Flow**: A aprendizagem automática de máquinas não suporta atualmente a versão de fluxo de tensores 1.13. A instalação desta versão fará com que as dependências dos pacotes deixem de funcionar. Estamos a trabalhar para resolver esta questão num futuro lançamento.

* **Gráficos de experiências**: Gráficos de classificação binários (recolha de precisão, ROC, curva de ganho, etc.) mostrados em iterações automatizadas de experiências ML não estão a renderizar corretamente na interface do utilizador desde 4/12. Os lotes de gráficos estão atualmente a mostrar resultados inversos, onde modelos de melhor desempenho são mostrados com resultados mais baixos. Uma resolução está sob investigação.

* **Os Databricks cancelam uma execução automatizada**de machine learning : Quando utiliza capacidades automatizadas de aprendizagem automática em Pinos De Dados, para cancelar uma corrida e iniciar uma nova experiência, reiniciar o cluster De Bricks Azure.

* **Os databricks >10 iterações para aprendizagem automática**de máquinas : Em `show_output` `False` configurações automatizadas de aprendizagem automática de máquinas, se tiver mais de 10 iterações, definida para quando submeter a execução.

* **Widget databricks para o Azure Machine Learning SDK e machine learning automatizado**: O widget SDK de aprendizagem automática azure não é suportado num caderno databricks porque os cadernos não podem analisar widgets HTML. Pode ver o widget no portal utilizando este código Python na sua célula de caderno Azure Databricks:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Implementar e fornecer modelos

Tome estas ações para os seguintes erros:

|Erro  | Resolução  |
|---------|---------|
|Falha na construção de imagem ao implementar o serviço web     |  Adicione "pynacl==1.2.1" como uma dependência de pip ao ficheiro Conda para configuração de imagem       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Mude o SKU para VMs usado na sua implantação para um que tenha mais memória. |
|Falha da FPGA     |  Não poderá implementar modelos em FPGAs até que tenha solicitado e sido aprovado para a quota FPGA. Para solicitar o acesso, preencha o formulário de pedido de quota:https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Atualizar componentes de Machine Learning Azure no cluster AKS

As atualizações dos componentes de Machine Learning Azure instaladas num cluster de serviço Azure Kubernetes devem ser aplicadas manualmente. 

Pode aplicar estas atualizações desmontando o cluster do espaço de trabalho Azure Machine Learning e, em seguida, recolocando o cluster no espaço de trabalho. Se o TLS estiver ativado no cluster, terá de fornecer o certificado TLS/SSL e a chave privada ao voltar a ligar o cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Se já não tiver o certificado TLS/SSL e a chave privada, ou estiver a utilizar um certificado gerado pela Azure Machine Learning, pode recuperar os ficheiros antes de desmontar o cluster ligando-se ao cluster utilizando `kubectl` e recuperando o segredo `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos em formato codificado base-64. Terá de descodificar os `cert.pem` componentes `key.pem` e componentes dos segredos antes `attach_config.enable_ssl`de os fornecer. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices in Azure Kubernetes Service falha

Muitas falhas de serviço web no Serviço Azure Kubernetes podem `kubectl`ser depuradas ligando-se ao cluster utilizando . Você pode `kubeconfig.json` obter o para um Cluster de Serviço Azure Kubernetes executando

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Erros de autenticação

Se executar uma operação de gestão num alvo de cálculo a partir de um trabalho remoto, receberá um dos seguintes erros:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, receberá um erro se tentar criar ou anexar um alvo de cálculo a partir de um Pipeline ML que é submetido para execução remota.

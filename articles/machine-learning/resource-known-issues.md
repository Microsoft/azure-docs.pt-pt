---
title: Questões conhecidas & resolução de problemas
titleSuffix: Azure Machine Learning
description: Obtenha ajuda para encontrar e corrigir erros ou falhas no Azure Machine Learning. Conheça questões conhecidas, resolução de problemas e soluções alternativas.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4
ms.date: 08/13/2020
ms.openlocfilehash: cd9b015c292d262430d3fd845e06e38866bc6239
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018727"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Questões conhecidas e resolução de problemas em Azure Machine Learning

Este artigo ajuda-o a resolver problemas conhecidos que pode encontrar ao utilizar a Azure Machine Learning. 

Para obter mais informações sobre a resolução de problemas, consulte os [próximos passos](#next-steps) no final deste artigo.

> [!TIP]
> Erros ou outros problemas podem ser o resultado de quotas de [recursos](how-to-manage-quotas.md) que encontra ao trabalhar com a Azure Machine Learning. 

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

Às vezes pode ser útil se você pode fornecer informações de diagnóstico quando pede ajuda. Para ver alguns troncos: 
1. Visite [o estúdio Azure Machine Learning.](https://ml.azure.com) 
1. No lado esquerdo, selecione **Experiment** 
1. Selecione uma experiência.
1. Selecione uma corrida.
1. Em cima, selecione **Outputs + logs**.

> [!NOTE]
> A Azure Machine Learning regista informações de várias fontes durante o treino, tais como AutoML ou o contentor Docker que gere o trabalho de formação. Muitos destes registos não estão documentados. Se encontrar problemas e contactar o suporte da Microsoft, poderão utilizar estes registos durante a resolução de problemas.


## <a name="installation-and-import"></a>Instalação e importação
                           
* **Instalação pip: As dependências não são garantidas para serem consistentes com a instalação de linha única:** 

   Esta é uma limitação conhecida do pip, uma vez que não tem uma dependência funcional quando se instala como uma única linha. A primeira dependência única é a única que olha. 

   No código seguinte `azureml-datadrift` e `azureml-train-automl` ambos são instalados utilizando uma instalação de pip de linha única. 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   Para este exemplo, digamos que `azureml-datadrift` requer versão > 1.0 e `azureml-train-automl` requer versão < 1.2. Se a versão mais recente `azureml-datadrift` for 1.3, então ambos os pacotes serão atualizados para 1.3, independentemente da exigência do `azureml-train-automl` pacote para uma versão mais antiga. 

   Para garantir que as versões apropriadas são instaladas para as suas embalagens, instale usando várias linhas como no código seguinte. A ordem não é um problema aqui, uma vez que pip explicitamente desvaloriza como parte da próxima chamada de linha. E assim, as dependências de versão apropriadas são aplicadas.
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Pacote de explicação não garantido para ser instalado ao instalar o azureml-train-automl-cliente:** 
   
   Ao executar uma execução remota de AutoML com a explicação do modelo ativada, verá uma mensagem de erro "Por favor instale o pacote azureml-explain-model para explicações do modelo." Este é um problema conhecido. Como uma solução alternativa siga um dos passos abaixo:
  
  1. Instale localmente o modelo azureml-explain-explain.
   ```
      pip install azureml-explain-model
   ```
  2. Desative totalmente a função de explicação passando model_explainability=Falso na configuração AutoML.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Erros panda: Tipicamente vistos durante a Experiência AutoML:**
   
   Ao configurar manualmente o seu ambiente utilizando pip, poderá notar erros de atributos (especialmente de pandas) devido à instalação de versões de pacote não suportadas. Para evitar tais erros, [instale o AutoML SDK utilizando o automl_setup.cmd:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md)
   
    1. Abra um pedido de Anaconda e clone o repositório GitHub para um conjunto de cadernos de amostras.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. cd para a pasta de como usar-azureml/automatizada-aprendizagem de máquinas onde os cadernos de amostra foram extraídos e depois executados:
    
    ```bash
    automl_setup
    ```
    
* **KeyError: 'marca' ao executar AutoML em computação local ou cluster Azure Databricks**

    Se um novo ambiente for criado após 10 de junho de 2020, utilizando o SDK 1.7.0 ou mais cedo, o treino poderá falhar com este erro devido a uma atualização no pacote py-cpuinfo. (Ambientes criados em ou antes de 10 de junho de 2020, não são afetados, assim como as experiências executadas em computação remota porque são usadas imagens de treino em cache.) Para contornar esta questão, tome um dos dois passos seguintes:
    
    * Atualizar a versão SDK para 1.8.0 ou mais tarde (isto também reduz py-cpuinfo para 5.0.0):
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * Desclasse a versão instalada do py-cpuinfo para 5.0.0:
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **Error message: Can't desinstalar 'PyYAML'**

    Azure Machine Learning SDK para Python: PyYAML é um `distutils` projeto instalado. Portanto, não podemos determinar com precisão quais os ficheiros que lhe pertencem se houver uma desinstalação parcial. Para continuar a instalar o SDK ignorando este erro, utilize:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Instalação Azure Machine Learning SDK falhando com uma exceção: MóduloNotFoundError: Nenhum módulo chamado 'ruamel' ou 'ImportError: Nenhum módulo chamado ruamel.yaml'**
   
   Esta questão está a ser encontrada com a instalação do Azure Machine Learning SDK para Python no mais recente pip (>20.1.1) no ambiente base da Conda para todas as versões lançadas do Azure Machine Learning SDK para Python. Consulte as seguintes soluções:

    * Evite instalar o Python SDK no ambiente de base conda, antes crie o seu ambiente conda e instale o SDK nesse ambiente de utilizador recém-criado. O último pip deve trabalhar naquele novo ambiente conda.

    * Para criar imagens em estivador, onde não é possível desligar do ambiente base conda, por favor pin pip pip<=20.1.1 no ficheiro estivador.

    ```Python
    conda install -c r -y conda python=3.6.2 pip=20.1.1
    ```
    
* **Falha de dados ao instalar pacotes**

    A instalação Azure Machine Learning SDK falha nas databricks Azure quando mais pacotes são instalados. Alguns pacotes, como, por `psutil` exemplo, podem causar conflitos. Para evitar erros de instalação, instale as embalagens congelando a versão da biblioteca. Esta questão está relacionada com databricks e não com o Azure Machine Learning SDK. Você pode experimentar este problema com outras bibliotecas, também. Exemplo:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Em alternativa, pode utilizar scripts init se continuar a enfrentar problemas de instalação com bibliotecas Python. Esta abordagem não é oficialmente apoiada. Para obter mais informações, consulte [scripts init com âmbito de cluster.](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)

* **Erro de importação de dados: não pode importar nome `Timedelta` a partir `pandas._libs.tslibs` de: **Se vir este erro quando utilizar machine learning automatizado, execute as duas seguintes linhas no seu caderno:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Erro de importação de dados: Nenhum módulo denominado 'pandas.core.indexes'**: Se vir este erro quando utilizar machine learning automatizado:

    1. Executar este comando para instalar dois pacotes no seu cluster Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Retire e, em seguida, recoloque o cluster no seu caderno.
    
    Se estes passos não resolverem o problema, tente reiniciar o cluster.

* **Databricks FailToSendFeather**: Se vir um `FailToSendFeather` erro ao ler dados no cluster Azure Databricks, consulte as seguintes soluções:
    
    * Atualizar `azureml-sdk[automl]` o pacote para a versão mais recente.
    * Adicione `azureml-dataprep` a versão 1.1.8 ou superior.
    * Adicione `pyarrow` a versão 0.11 ou superior.
    
## <a name="create-and-manage-workspaces"></a>Criar e gerir espaços de trabalho

> [!WARNING]
> Mover o seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou mover a subscrição proprietária para um novo inquilino, não é suportado. Fazê-lo pode causar erros.

* **Portal Azure**: Se for diretamente ver o seu espaço de trabalho a partir de um link de partilha a partir do SDK ou do portal, não poderá ver a página **geral** normal com informações de subscrição na extensão. Também não poderá mudar para outro espaço de trabalho. Se precisar de ver outro espaço de trabalho, vá diretamente ao [estúdio Azure Machine Learning](https://ml.azure.com) e procure o nome do espaço de trabalho.

* **Browsers suportados no portal web do estúdio Azure Machine Learning**: Recomendamos que utilize o navegador mais atualizado que seja compatível com o seu sistema operativo. Os seguintes navegadores são suportados:
  * Microsoft Edge (O novo Microsoft Edge, versão mais recente. Não o legado da Microsoft Edge)
  * Safari (versão mais recente, apenas Mac)
  * Chrome (versão mais recente)
  * Firefox (versão mais recente)

## <a name="set-up-your-environment"></a>Configurar o seu ambiente

* **Problemas na criação do AmlCompute**: Existe uma rara possibilidade de alguns utilizadores que criaram o seu espaço de trabalho Azure Machine Learning a partir do portal Azure antes do lançamento do GA não serem capazes de criar AmlCompute nesse espaço de trabalho. Pode levantar um pedido de apoio contra o serviço ou criar um novo espaço de trabalho através do portal ou o SDK para se desbloquear imediatamente.

* **Atualmente, o Registo de Contentores Azure não suporta caracteres unicode em nomes do Grupo de Recursos**: É possível que os pedidos de ACR falhem porque o seu nome de grupo de recursos contém caracteres unicódigo. Para mitigar este problema, recomendamos a criação de um ACR num grupo de recursos com nome diferente.

## <a name="work-with-data"></a>Trabalhar com dados

### <a name="overloaded-azurefile-storage"></a>Armazenamento AzureFile sobrecarregado

Se receber um `Unable to upload project files to working directory in AzureFile because the storage is overloaded` erro, aplique após soluções alternativas.

Se estiver a utilizar a partilha de ficheiros para outras cargas de trabalho, como a transferência de dados, a recomendação é utilizar blobs para que a partilha de ficheiros seja gratuita para ser utilizada para a apresentação de execuções. Também pode dividir a carga de trabalho entre dois espaços de trabalho diferentes.

### <a name="passing-data-as-input"></a>Passar dados como entrada

*  **TypeError: FileNotFound: Não existe tal ficheiro ou diretório**: Este erro ocorre se o caminho do ficheiro que fornece não estiver onde o ficheiro está localizado. Tem de se certificar de que a forma como se refere ao ficheiro é consistente com o local onde montou o conjunto de dados no seu alvo de computação. Para garantir um estado determinístico, recomendamos a utilização do caminho abstrato ao montar um conjunto de dados para um alvo computacional. Por exemplo, no seguinte código montamos o conjunto de dados sob a raiz do sistema de ficheiros do alvo do computação, `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Se não incluir o corte dianteiro principal, '/', terá de pré-fixar o diretório de trabalho, por exemplo, `/mnt/batch/.../tmp/dataset` no alvo do cálculo para indicar onde pretende que o conjunto de dados seja montado.

### <a name="data-labeling-projects"></a>Projetos de rotulagem de dados

|Problema  |Resolução  |
|---------|---------|
|Apenas podem ser utilizados conjuntos de dados criados nas datas-do-canal blob.     |  Esta é uma limitação conhecida da libertação atual.       |
|Após a criação, o projeto mostra "Inicialização" durante muito tempo.     | Refresque manualmente a página. A inicialização deve prosseguir em cerca de 20 pontos de dados por segundo. A falta de auto-defesa é uma questão conhecida.         |
|Ao rever imagens, imagens recentemente rotuladas não são mostradas.     |   Para carregar todas as imagens etiquetadas, escolha o botão **First.** O botão **First** irá levá-lo de volta para a frente da lista, mas carrega todos os dados rotulados.      |
|Premir a tecla Esc durante a rotulagem para deteção de objetos cria uma etiqueta de tamanho zero no canto superior esquerdo. Enviar etiquetas neste estado falha.     |   Elimine a etiqueta clicando na marca transversal ao lado.  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> Monitores de deriva de dados

Limitações e questões conhecidas para monitores de deriva de dados:

* O intervalo de tempo ao analisar dados históricos limita-se a 31 intervalos da definição de frequência do monitor. 
* Limitação de 200 funcionalidades, a menos que não seja especificada uma lista de recursos (todas as funcionalidades utilizadas).
* O tamanho do cálculo deve ser grande o suficiente para lidar com os dados.
* Certifique-se de que o seu conjunto de dados tem dados dentro da data de início e de fim para uma determinada execução do monitor.
* Os monitores do Dataset só funcionarão em conjuntos de dados que contenham 50 linhas ou mais.
* Colunas ou características, no conjunto de dados são classificadas como categóricas ou numéricas com base nas condições do quadro seguinte. Se a funcionalidade não corresponder a estas condições - por exemplo, uma coluna de tipo string com >100 valores únicos - a funcionalidade é retirada do nosso algoritmo de deriva de dados, mas ainda está perfilada. 

    | Tipo de recurso | Tipo de dados | Condição | Limitações | 
    | ------------ | --------- | --------- | ----------- |
    | Categórico | corda, bool, int, flutuar | O número de valores únicos na funcionalidade é inferior a 100 e menos de 5% do número de linhas. | O nulo é tratado como a sua própria categoria. | 
    | Numérico | int, flutuar | Os valores na funcionalidade são de um tipo de dado numérico e não correspondem à condição de uma característica categórica. | A função caiu se >15% dos valores forem nulos. | 

* Quando tiver [criado um monitor de deriva de dados](how-to-monitor-datasets.md) mas não conseguir ver dados na página de monitores do **Dataset** no estúdio Azure Machine Learning, experimente o seguinte.

    1. Verifique se selecionou o intervalo de datas certo no topo da página.  
    1. No **separador Dataset Monitors,** selecione o link de experiência para verificar o estado da execução.  Esta ligação está na extrema direita da mesa.
    1. Se o funcionado for concluído com sucesso, verifique os registos do controlador para ver quantas métricas foram geradas ou se há alguma mensagem de aviso.  Encontre registos de controlador no **separador Saída + registos** depois de clicar numa experiência.

* Se a função SDK `backfill()` não gerar a saída esperada, poderá ser devido a um problema de autenticação.  Quando criar o cálculo para passar para esta função, não utilize `Run.get_context().experiment.workspace.compute_targets` .  Em vez disso, utilize [a Concessão de ServiçoPrincipal,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) como a seguinte, para criar o cálculo que passa para essa `backfill()` função: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Designer de aprendizagem de máquinas Azure

* **Tempo de preparação de computação longa:**

Pode demorar alguns minutos ou até mais quando ligar ou criar um alvo de computação. 

A partir do Colecionador de Dados do Modelo, pode levar até (mas normalmente menos de) 10 minutos para os dados chegarem à sua conta de armazenamento de bolhas. Aguarde 10 minutos para garantir que as células abaixo irão funcionar.

```python
import time
time.sleep(600)
```

* **Faça login para pontos finais em tempo real:**

Os registos dos pontos finais em tempo real são dados do cliente. Para a resolução de problemas em tempo real, pode utilizar o código seguinte para ativar registos. 

Consulte mais detalhes sobre a monitorização dos pontos finais do serviço web [neste artigo](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights#query-logs-for-deployed-models).

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
Se tiver vários Inquilinos, poderá ter de adicionar o seguinte código autenticado antes `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>Preparar modelos

* **MóduloErrors (Nenhum módulo nomeado)**: Se estiver a correr para o ModuleErrors enquanto submete experiências em Azure ML, significa que o script de treino está à espera de um pacote para ser instalado, mas não é adicionado. Assim que fornecer o nome do pacote, a Azure ML instala a embalagem no ambiente utilizado para o seu treino. 

    Se estiver a utilizar [os Estimadores](concept-azure-machine-learning-architecture.md#estimators) para submeter experiências, pode especificar um nome de pacote através `pip_packages` ou parâmetro no `conda_packages` estimador com base na fonte que pretende instalar a embalagem. Também pode especificar um ficheiro yml com todas as suas dependências usando `conda_dependencies_file` ou listar todos os seus requisitos de pip num ficheiro TXT usando `pip_requirements_file` parâmetro. Se tiver o seu próprio objeto Azure ML Environment que pretende sobrepor a imagem padrão utilizada pelo estimador, pode especificar esse ambiente através `environment` do parâmetro do construtor estimador.

    A Azure ML também fornece estimativas específicas do quadro para TensorFlow, PyTorch, Chainer e SKLearn. A utilização destes estimadores certificar-se-á de que as dependências de quadros fundamentais são instaladas em seu nome no ambiente utilizado para a formação. Tem a opção de especificar dependências extras como descrito acima. 
 
    A Azure ML manteve imagens de estivadores e o seu conteúdo pode ser visto em [Recipientes AzureML](https://github.com/Azure/AzureML-Containers).
    As dependências específicas do quadro estão listadas na respetiva documentação-quadro - [Chainer,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks) [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks) [TensorFlow,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks) [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Se você acha que um determinado pacote é comum o suficiente para ser adicionado em imagens e ambientes mantidos Azure ML por favor levante um problema gitHub em [recipientes AzureML](https://github.com/Azure/AzureML-Containers). 
 
* **NomeError (Nome não definido), AttributeError (Objeto não tem atributo)**: Esta exceção deve vir dos seus scripts de treino. Pode ver os ficheiros de registo do portal Azure para obter mais informações sobre o nome específico não definido ou o erro de atributo. A partir do SDK, pode usar `run.get_details()` para ver a mensagem de erro. Isto também listará todos os ficheiros de registo gerados para a sua execução. Por favor, certifique-se de dar uma olhada no seu script de treino e corrigir o erro antes de voltar a enviar a sua execução. 

* **Horovod foi encerrado**: Na maioria dos casos, se encontrar "AbortedError: Horovod foi encerrado" esta exceção significa que houve uma exceção subjacente num dos processos que fez Com que Horovod fosse encerrado. Cada posto no trabalho de MPI obtém-no próprio ficheiro de registo dedicado em Azure ML. Estes registos são `70_driver_logs` nomeados. Em caso de treino distribuído, os nomes de registo são sufixados `_rank` para facilitar a diferenciação dos registos. Para encontrar o erro exato que fez a Horovod desligar, procure todos os ficheiros de registo e procure `Traceback` no final dos ficheiros driver_log. Um destes ficheiros vai dar-lhe a verdadeira exceção subjacente. 

* **Execução ou eliminação de experiências**: As experiências podem ser arquivadas utilizando o método [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) ou a partir da vista do separador Experiment em Azure Machine Learning cliente através do botão "Archive experiment". Esta ação esconde a experiência de consultas e pontos de vista de listas, mas não a apaga.

    A eliminação permanente de experiências ou execuções individuais não é suportada atualmente. Para obter mais informações sobre a eliminação dos ativos do Espaço de Trabalho, consulte [exportação ou elimine os dados do seu espaço de trabalho do serviço Machine Learning](how-to-export-delete-data.md).

* **Documento métrico é muito grande**: A Azure Machine Learning tem limites internos no tamanho de objetos métricos que podem ser registados de uma só vez a partir de uma corrida de treino. Se encontrar um erro "Documento Métrico é demasiado grande" ao registar uma métrica avaliada em listas, tente dividir a lista em pedaços menores, por exemplo:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internamente, Azure ML concatena os blocos com o mesmo nome métrico numa lista contígua.

## <a name="automated-machine-learning"></a>Aprendizagem automática automatizada

* **TensorFlow**: A partir da versão 1.5.0 do SDK, a aprendizagem automática de máquinas não instala modelos TensorFlow por predefinição. Para instalar o TensorFlow e utilizá-lo com as suas experiências automatizadas de ML, instale tensorflow==1.12.0 via CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **Gráficos de experiências**: Os gráficos de classificação binária (recolha de precisão, ROC, curva de ganho, etc.) mostrados em iterações automatizadas de experiências ML não estão a renderizar corretamente na interface do utilizador desde 4/12. Os enredos de gráficos estão atualmente a mostrar resultados inversos, onde os modelos de melhor desempenho são apresentados com resultados mais baixos. Uma resolução está sob investigação.

* **Databricks cancelam uma corrida automatizada de aprendizagem automática**de máquinas : Quando utilizar capacidades automatizadas de aprendizagem automática de máquinas em Azure Databricks, para cancelar uma corrida e iniciar uma nova experiência, reinicie o seu cluster Azure Databricks.

* **Databricks >10 iterações para aprendizagem automática de máquinas**: Em configurações automatizadas de aprendizagem automática, se tiver mais de 10 iterações, definidas `show_output` para quando submeter a `False` execução.

* **Databricks widget para o Azure Machine Learning SDK e machine learning automatizado**: O widget SDK de aprendizagem de máquinas Azure não é suportado num caderno databricks porque os cadernos não podem analisar widgets HTML. Pode ver o widget no portal utilizando este código Python na sua célula de portátil Azure Databricks:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup falha:** 
    * No Windows, executar automl_setup a partir de um Pedido de Anaconda. Para instalar o Miniconda clique [aqui.](https://docs.conda.io/en/latest/miniconda.html)
    * Certifique-se de que o conda 64-bit está instalado, em vez de 32 bits executando o `conda info` comando. O `platform` deve ser para Windows ou `win-64` `osx-64` mac.
    * Certifique-se de que a conda 4.4.10 ou posterior é instalada. Pode verificar a versão com o comando `conda -V` . Se tiver uma versão anterior instalada, pode atualizá-la utilizando o comando: `conda update conda` .
    * Linux - `gcc: error trying to exec 'cc1plus'`
      *  Se o `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` erro for encontrado, instale o essencial da construção utilizando o comando do termino `sudo apt-get install build-essential` .
      * Passe um novo nome como o primeiro parâmetro para automl_setup para criar um novo ambiente conda. Ver ambientes conda existentes utilizando `conda env list` e remova-os com `conda env remove -n <environmentname>` .
      
* **automl_setup_linux.sh falha:** Se automl_setup_linus.sh falhar em Ubuntu Linux com o erro: `unable to execute 'gcc': No such file or directory`-
  1. Certifique-se de que as portas de saída 53 e 80 estão ativadas. Num VM Azure, pode fazê-lo a partir do Portal Azure selecionando o VM e clicando em Networking.
  2. Executar o comando: `sudo apt-get update`
  3. Executar o comando: `sudo apt-get install build-essential --fix-missing`
  4. Corra `automl_setup_linux.sh` de novo

* **configuration.ipynb falha:**
  * Para a conda local, primeiro certifique-se de que automl_setup tem funcionamento susccessfully.
  * Certifique-se de que o subscription_id está correto. Encontre o subscription_id no Portal Azure selecionando Todos os Serviços e, em seguida, Subscrições. Os caracteres "<" e ">" não devem ser incluídos no valor subscription_id. Por exemplo, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` tem o formato válido.
  * Garantir ao Contribuinte ou ao Proprietário o acesso à Subscrição.
  * Verifique se a região é uma das regiões apoiadas: `eastus2` , , , , , , , , `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` . `southcentralus`
  * Garantir o acesso à região através do Portal Azure.
  
* **falha no AutoMLConfig**de importação : Houve alterações de pacotes na versão automatizada de machine learning 1.0.76, que exigem que a versão anterior seja desinstalada antes da atualização para a nova versão. Se o for `ImportError: cannot import name AutoMLConfig` encontrado após a atualização de uma versão SDK antes de v1.0.76 para v1.0.76 ou posterior, resolva o erro correndo: `pip uninstall azureml-train automl` e depois `pip install azureml-train-auotml` . O automl_setup.cmd script faz isto automaticamente. 

* **workspace.from_config falha:** Se as chamadas ws = Workspace.from_config()' falharem -
  1. Certifique-se de que o portátil configuração.ipynb foi executado com sucesso.
  2. Se o portátil estiver a ser executado a partir de uma pasta que não esteja por baixo da pasta onde `configuration.ipynb` foi executada, copie a pasta aml_config e o ficheiro config.jsque contém para a nova pasta. Workspace.from_config lê o config.jsligado para a pasta do portátil ou para a pasta dos pais.
  3. Se estiver a ser utilizada uma nova subscrição, grupo de recursos, espaço de trabalho ou região, certifique-se de que volta a executar o `configuration.ipynb` caderno. A alteração config.jsem direto só funcionará se o espaço de trabalho já existir no grupo de recursos especificado ao abrigo da subscrição especificada.
  4. Se quiser alterar a região, por favor altere o espaço de trabalho, grupo de recursos ou subscrição. `Workspace.create` não criará nem atualizará um espaço de trabalho se já existir, mesmo que a região especificada seja diferente.
  
* **O caderno da amostra falha:** Se um caderno de amostras falhar com um erro que a preperty, o método ou a biblioteca não existem:
  * Certifique-se de que o núcleo correto foi selecionado no caderno do jupyter. O núcleo é apresentado no lado superior direito da página do caderno. O padrão é azure_automl. Note que o núcleo é guardado como parte do caderno. Por isso, se mudar para um novo ambiente conda, terá de selecionar o novo núcleo no caderno.
      * Para os Cadernos Azure, deve ser Python 3.6. 
      * Para ambientes conda locais, deve ser o nome de envior conda que especificou em automl_setup.
  * Certifique-se de que o caderno é para a versão SDK que está a utilizar. Pode verificar a versão SDK executando `azureml.core.VERSION` numa cela de caderno jupyter. Pode descarregar a versão anterior dos cadernos de amostras do GitHub clicando no `Branch` botão, selecionando o `Tags` separador e selecionando a versão.

* **A importação de numpy falha no Windows**: Alguns ambientes windows vêem um erro de carregamento numpy com a versão python mais recente 3.6.8. Se vir esta edição, experimente com a versão Python 3.6.7.

* **Falha na importação de numpy**: Verifique a versão de tensorflow no ambiente automatizado ml conda. As versões suportadas são < 1.13. Desinstalar o tensorfluor do ambiente se a versão for >= 1.13 Pode verificar a versão do tensorflow e desinstalar da seguinte forma -
  1. Inicie uma concha de comando, ative o ambiente conda onde são instalados pacotes ml automatizados.
  2. `pip freeze`Insira e `tensorflow` procure, se encontrado, a versão listada deve ser < 1.13
  3. Se a versão listada não for uma versão suportada, `pip uninstall tensorflow` na concha de comando e insira y para confirmação.

## <a name="deploy--serve-models"></a>Implementar e fornecer modelos

Tome estas ações para os seguintes erros:

|Erro  | Resolução  |
|---------|---------|
|Falha na construção de imagem ao implementar o serviço web     |  Adicione "pynacl==1.2.1" como uma dependência de pip ao ficheiro Conda para configuração de imagem       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Altere o SKU para VMs utilizados na sua implantação para um que tenha mais memória. |
|Falha da FPGA     |  Não poderá implementar modelos em FPGAs até que tenha solicitado e aprovado para a quota FPGA. Para solicitar o acesso, preencha o formulário de pedido de quota: https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Atualizar componentes de aprendizagem automática Azure no cluster AKS

As atualizações dos componentes de Aprendizagem automática Azure instalados num cluster de serviço Azure Kubernetes devem ser aplicadas manualmente. 

Pode aplicar estas atualizações desprendendo o cluster do espaço de trabalho Azure Machine Learning e, em seguida, recolocar o cluster no espaço de trabalho. Se o TLS estiver ativado no cluster, terá de fornecer o certificado TLS/SSL e a chave privada ao voltar a ligar o cluster. 

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

Se já não tiver o certificado TLS/SSL e a chave privada, ou estiver a utilizar um certificado gerado pela Azure Machine Learning, poderá recuperar os ficheiros antes de desvincular o cluster, ligando-se ao cluster utilizando `kubectl` e recuperando o `azuremlfessl` segredo.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos em formato codificado base-64. Terá de descodificar os `cert.pem` `key.pem` e componentes dos segredos antes de os fornecer `attach_config.enable_ssl` a . 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices em falhas de serviço Azure Kubernetes

Muitas falhas de serviço web no Serviço Azure Kubernetes podem ser depuradas ligando-se ao cluster utilizando `kubectl` . Você pode obter o `kubeconfig.json` para um Cluster de ServiçoS Azure Kubernetes executando

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Erros de autenticação

Se efetuar uma operação de gestão num alvo de computação a partir de um trabalho remoto, receberá um dos seguintes erros: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, receberá um erro se tentar criar ou anexar um alvo de cálculo a partir de um Pipeline ML que é submetido para execução remota.

## <a name="missing-user-interface-items-in-studio"></a>Itens de interface de utilizador em falta no estúdio

O controlo de acesso baseado em funções Azure pode ser usado para restringir as ações que pode realizar com a Azure Machine Learning. Estas restrições podem impedir que os itens de interface do utilizador apareçam no estúdio Azure Machine Learning. Por exemplo, se lhe for atribuída uma função que não pode criar uma instância de computação, a opção de criar uma instância de computação não aparecerá no estúdio.

Para obter mais informações, consulte [Gerir os utilizadores e as funções.](how-to-assign-roles.md)

## <a name="next-steps"></a>Passos seguintes

Ver mais artigos de resolução de problemas para Azure Machine Learning:

* [Resolução de problemas de implementação de Docker com Azure Machine Learning](how-to-troubleshoot-deployment.md)
* [Oleodutos de aprendizagem automática de depuragem de depurações](how-to-debug-pipelines.md)
* [Depurar a aula ParallelRunStep do Azure Machine Learning SDK](how-to-debug-parallel-run-step.md)
* [Depuragem interativa de uma instância computacional de aprendizagem automática com código VS](how-to-debug-visual-studio-code.md)
* [Utilizar insights de aplicações para depurar oleodutos de aprendizagem automática](how-to-debug-pipelines-application-insights.md)

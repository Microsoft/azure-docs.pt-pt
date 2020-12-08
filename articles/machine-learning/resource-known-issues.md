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
ms.topic: troubleshooting
ms.custom: troubleshooting, contperfq4
ms.date: 11/09/2020
ms.openlocfilehash: 55ac11b7888a8e351b52554f76fb44af35633c16
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780982"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Problemas conhecidos e resolução de problemas no Azure Machine Learning

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

* **Erro de importação de dados: não pode importar nome `Timedelta` a partir `pandas._libs.tslibs` de:** Se vir este erro quando utilizar machine learning automatizado, execute as duas seguintes linhas no seu caderno:
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

* **Portal Azure:** 
  * Se for diretamente para o seu espaço de trabalho a partir de um link de partilha do SDK ou do portal Azure, não pode ver a página **geral** padrão que tem informações de subscrição na extensão. Neste cenário, também não pode mudar para outro espaço de trabalho. Para ver outro espaço de trabalho, vá diretamente ao [estúdio Azure Machine Learning](https://ml.azure.com) e procure o nome do espaço de trabalho.
  * Todos os ativos (Datasets, Experiments, Computes, e assim por diante) estão disponíveis apenas no [estúdio Azure Machine Learning](https://ml.azure.com). Não *estão* disponíveis no portal Azure.

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

### <a name="mount-dataset"></a>Conjunto de dados de montagem
* **A inicialização do conjunto de dados falhou: Esperar que o ponto de montagem esteja pronto tem tempo esgotado**: 
  * Se não tiver nenhuma regra do [grupo de segurança](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview) de rede de saída e estiver a usar , por favor `azureml-sdk>=1.12.0` atualize e as suas `azureml-dataset-runtime` dependências sejam as mais recentes para a versão menor específica, ou se estiver a usá-la numa corrida, por favor recrie o seu ambiente para que possa ter o patch mais recente com a correção. 
  * Se estiver a `azureml-sdk<1.12.0` utilizar, faça upgrade para a versão mais recente.
  * Se tiver regras NSG de saída, certifique-se de que existe uma regra de saída que permite todo o tráfego para a etiqueta de serviço `AzureResourceMonitor` .

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

* Se a função SDK `backfill()` não gerar a saída esperada, poderá ser devido a um problema de autenticação.  Quando criar o cálculo para passar para esta função, não utilize `Run.get_context().experiment.workspace.compute_targets` .  Em vez disso, utilize [a Concessão de ServiçoPrincipal,](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py) como a seguinte, para criar o cálculo que passa para essa `backfill()` função: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Estruturador do Azure Machine Learning

### <a name="dataset-visualization-in-the-designer"></a>Visualização do conjunto de dados no designer

Depois de registar um conjunto de dados na página de ativos **datasets** ou utilizar o SDK, pode encontrá-lo na categoria **Datasets** na lista deixada para a tela do designer.

No entanto, quando arrasta o conjunto de dados para a tela e visualiza, pode não ser capaz de visualizar devido a algumas das seguintes razões:

- Atualmente só é possível visualizar o conjunto de dados tabulares no designer. Se registar um conjunto de dados de ficheiros fora do designer, não poderá visualizá-lo na tela do designer.
- O seu conjunto de dados é armazenado em rede virtual (VNet). Se quiser visualizar, tem de ativar a identidade gerida pelo espaço de trabalho da datastore.
    1. Vá à loja de dados relacionada e clique em **Atualização de** 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="Credenciais De Atualização Credenciais":::
    1. Selecione **Sim** para ativar a identidade gerida pelo espaço de trabalho.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Ativar identidade gerida do espaço de trabalho":::

### <a name="long-compute-preparation-time"></a>Tempo de preparação de computação longa

Pode demorar alguns minutos ou até mais quando ligar ou criar um alvo de computação. 

A partir do Colecionador de Dados do Modelo, pode levar até (mas normalmente menos de) 10 minutos para os dados chegarem à sua conta de armazenamento de bolhas. Aguarde 10 minutos para garantir que as células abaixo irão funcionar.

```python
import time
time.sleep(600)
```

### <a name="log-for-real-time-endpoints"></a>Faça login para pontos finais em tempo real

Os registos dos pontos finais em tempo real são dados do cliente. Para a resolução de problemas em tempo real, pode utilizar o código seguinte para ativar registos. 

Consulte mais detalhes sobre a monitorização dos pontos finais do serviço web [neste artigo](./how-to-enable-app-insights.md#query-logs-for-deployed-models).

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

    Se estiver a utilizar os Estimadores para submeter experiências, pode especificar um nome de pacote através `pip_packages` ou `conda_packages` parâmetro no estimador com base na fonte que pretende instalar a embalagem. Também pode especificar um ficheiro yml com todas as suas dependências usando `conda_dependencies_file` ou listar todos os seus requisitos de pip num ficheiro TXT usando `pip_requirements_file` parâmetro. Se tiver o seu próprio objeto Azure ML Environment que pretende sobrepor a imagem padrão utilizada pelo estimador, pode especificar esse ambiente através `environment` do parâmetro do construtor estimador.

    A Azure ML também fornece estimativas específicas do quadro para TensorFlow, PyTorch, Chainer e SKLearn. A utilização destes estimadores certificar-se-á de que as dependências de quadros fundamentais são instaladas em seu nome no ambiente utilizado para a formação. Tem a opção de especificar dependências extras como descrito acima. 
 
    A Azure ML manteve imagens de estivadores e o seu conteúdo pode ser visto em [Recipientes AzureML](https://github.com/Azure/AzureML-Containers).
    As dependências específicas do quadro estão listadas na respetiva documentação-quadro - [Chainer,](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks) [PyTorch,](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks) [TensorFlow,](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks) [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks).

    > [!Note]
    > Se você acha que um determinado pacote é comum o suficiente para ser adicionado em imagens e ambientes mantidos Azure ML por favor levante um problema gitHub em [recipientes AzureML](https://github.com/Azure/AzureML-Containers). 
 
* **NomeError (Nome não definido), AttributeError (Objeto não tem atributo)**: Esta exceção deve vir dos seus scripts de treino. Pode ver os ficheiros de registo do portal Azure para obter mais informações sobre o nome específico não definido ou o erro de atributo. A partir do SDK, pode usar `run.get_details()` para ver a mensagem de erro. Isto também listará todos os ficheiros de registo gerados para a sua execução. Por favor, certifique-se de dar uma olhada no seu script de treino e corrigir o erro antes de voltar a enviar a sua execução. 

* **Horovod foi encerrado**: Na maioria dos casos, se encontrar "AbortedError: Horovod foi encerrado" esta exceção significa que houve uma exceção subjacente num dos processos que fez Com que Horovod fosse encerrado. Cada classificação no trabalho MPI tem o seu próprio ficheiro de registo dedicado no Azure ML. Estes registos são designados `70_driver_logs`. No caso da preparação distribuída, os nomes dos registos têm o sufixo `_rank` para facilitar a diferenciação dos mesmos. Para encontrar o erro exato que fez a Horovod desligar, procure todos os ficheiros de registo e procure `Traceback` no final dos ficheiros driver_log. Um destes ficheiros vai dar-lhe a verdadeira exceção subjacente. 

* **Execução ou eliminação de experiências**: As experiências podem ser arquivadas utilizando o método [Experiment.archive](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) ou a partir da vista do separador Experiment em Azure Machine Learning cliente através do botão "Archive experiment". Esta ação esconde a experiência de consultas e pontos de vista de listas, mas não a apaga.

    Atualmente, a eliminação permanente de experimentações ou execuções individuais não é suportada. Para obter mais informações sobre a eliminação dos ativos do Espaço de Trabalho, consulte [exportação ou elimine os dados do seu espaço de trabalho do serviço Machine Learning](how-to-export-delete-data.md).

* **Documento métrico é muito grande**: A Azure Machine Learning tem limites internos no tamanho de objetos métricos que podem ser registados de uma só vez a partir de uma corrida de treino. Caso se depare com um erro “O Documento de Métricas é demasiado grande” ao registar uma métrica com valores de lista, experimente dividir a lista em segmentos menores, por exemplo:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internamente, o Azure ML concatena os blocos com o mesmo nome de métrica numa lista contígua.

## <a name="automated-machine-learning"></a>Aprendizagem automática automatizada

* **A recente atualização das dependências automl para versões mais recentes será quebrar a compatibilidade**: A partir da versão 1.13.0 do SDK, os modelos não serão carregados em SDKs mais antigos devido à incompatibilidade entre as versões mais antigas que fixamos nos nossos pacotes anteriores, e as versões mais recentes que agora fixamos. Verá erros como:
  * Módulo não encontrado: Ex. `No module named 'sklearn.decomposition._truncated_svd` ,
  * Erros de importação: Ex. `ImportError: cannot import name 'RollingOriginValidator'` ,
  * Erros de atributo: Ex. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  Para contornar esta questão, dê um dos dois passos seguintes dependendo da sua versão de treino AutoML SDK:
  1. Se a sua versão de treino AutoML SDK for superior a 1.13.0, precisa `pandas == 0.25.1` e `sckit-learn==0.22.1` . Se houver uma incompatibilidade da versão, atualize scikit-learn e/ou pandas para corrigir a versão como mostrado abaixo:
  
  ```bash
     pip install --upgrade pandas==0.25.1
     pip install --upgrade scikit-learn==0.22.1
  ```
  
  2. Se a sua versão de treino AutoML SDK for inferior ou igual a 1.12.0, precisa `pandas == 0.23.4` e `sckit-learn==0.20.3` . Se houver uma incompatibilidade da versão, desclasse scikit-learn e/ou pandas para corrigir a versão como mostrado abaixo:
  
  ```bash
    pip install --upgrade pandas==0.23.4
    pip install --upgrade scikit-learn==0.20.3
  ```
 
* **Previsão da pontuação R2 é sempre zero**: Esta questão surge se os dados de formação fornecidos mentem séries temporárias que contêm o mesmo valor para os `n_cv_splits`  +  `forecasting_horizon` últimos pontos de dados. Se este padrão for esperado na sua série de tempo, pode mudar a sua métrica primária para erro quadrado de raiz normalizado.
 
* **TensorFlow**: A partir da versão 1.5.0 do SDK, a aprendizagem automática de máquinas não instala modelos TensorFlow por predefinição. Para instalar o TensorFlow e utilizá-lo com as suas experiências automatizadas de ML, instale tensorflow==1.12.0 via CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **Gráficos de experiências**: Os gráficos de classificação binária (recolha de precisão, ROC, curva de ganho, etc.) mostrados em iterações automatizadas de experiências ML não estão a renderizar corretamente na interface do utilizador desde 4/12. Os enredos de gráficos estão atualmente a mostrar resultados inversos, onde os modelos de melhor desempenho são apresentados com resultados mais baixos. Uma resolução está sob investigação.

* **Databricks cancelam uma corrida automatizada de aprendizagem automática** de máquinas : Quando utilizar capacidades automatizadas de aprendizagem automática de máquinas em Azure Databricks, para cancelar uma corrida e iniciar uma nova experiência, reinicie o seu cluster Azure Databricks.

* **Databricks >10 iterações para aprendizagem automática de máquinas**: Em configurações automatizadas de aprendizagem automática, se tiver mais de 10 iterações, definidas `show_output` para quando submeter a `False` execução.

* **Databricks widget para o Azure Machine Learning SDK e machine learning automatizado**: O widget SDK de aprendizagem de máquinas Azure não é suportado num caderno databricks porque os cadernos não podem analisar widgets HTML. Pode ver o widget no portal utilizando este código Python na sua célula de portátil Azure Databricks:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup falha:** 
    * No Windows, executar automl_setup a partir de um Pedido de Anaconda. Utilize este link para [instalar o Miniconda.](https://docs.conda.io/en/latest/miniconda.html)
    * Certifique-se de que o conda 64-bit está instalado, em vez de 32 bits executando o `conda info` comando. O `platform` deve ser para Windows ou `win-64` `osx-64` mac.
    * Certifique-se de que a conda 4.4.10 ou posterior é instalada. Pode verificar a versão com o comando `conda -V` . Se tiver uma versão anterior instalada, pode atualizá-la utilizando o comando: `conda update conda` .
    * Linux - `gcc: error trying to exec 'cc1plus'`
      *  Se o `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` erro for encontrado, instale o essencial da construção utilizando o comando `sudo apt-get install build-essential` .
      * Passe um novo nome como o primeiro parâmetro para automl_setup para criar um novo ambiente conda. Ver ambientes conda existentes utilizando `conda env list` e remova-os com `conda env remove -n <environmentname>` .
      
* **automl_setup_linux.sh falha:** Se automl_setup_linus.sh falhar em Ubuntu Linux com o erro: `unable to execute 'gcc': No such file or directory`-
  1. Certifique-se de que as portas de saída 53 e 80 estão ativadas. Num Azure VM, pode fazê-lo a partir do portal Azure selecionando o VM e clicando em Networking.
  2. Executar o comando: `sudo apt-get update`
  3. Executar o comando: `sudo apt-get install build-essential --fix-missing`
  4. Corra `automl_setup_linux.sh` de novo

* **configuration.ipynb falha:**
  * Para a conda local, primeiro certifique-se de que automl_setup tem funcionada com sucesso.
  * Certifique-se de que o subscription_id está correto. Encontre o subscription_id no portal Azure selecionando Todos os Serviços e, em seguida, Subscrições. Os caracteres "<" e ">" não devem ser incluídos no valor subscription_id. Por exemplo, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` tem o formato válido.
  * Garantir ao Contribuinte ou ao Proprietário o acesso à Subscrição.
  * Verifique se a região é uma das regiões apoiadas: `eastus2` , , , , , , , , `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` . `southcentralus`
  * Garantir o acesso à região através do portal Azure.
  
* **falha no AutoMLConfig** de importação : Houve alterações de pacotes na versão automatizada de machine learning 1.0.76, que exigem que a versão anterior seja desinstalada antes da atualização para a nova versão. Se o for `ImportError: cannot import name AutoMLConfig` encontrado após a atualização de uma versão SDK antes de v1.0.76 para v1.0.76 ou posterior, resolva o erro correndo: `pip uninstall azureml-train automl` e depois `pip install azureml-train-auotml` . O automl_setup.cmd script faz isto automaticamente. 

* **workspace.from_config falha:** Se as chamadas ws = Workspace.from_config()' falharem -
  1. Certifique-se de que o portátil configuração.ipynb foi executado com sucesso.
  2. Se o portátil estiver a ser executado a partir de uma pasta que não esteja por baixo da pasta onde `configuration.ipynb` foi executada, copie a pasta aml_config e o ficheiro config.jsque contém para a nova pasta. Workspace.from_config lê o config.jsligado para a pasta do portátil ou para a pasta dos pais.
  3. Se estiver a ser utilizada uma nova subscrição, grupo de recursos, espaço de trabalho ou região, certifique-se de que volta a executar o `configuration.ipynb` caderno. A alteração config.jsem direto só funcionará se o espaço de trabalho já existir no grupo de recursos especificado ao abrigo da subscrição especificada.
  4. Se quiser alterar a região, por favor altere o espaço de trabalho, grupo de recursos ou subscrição. `Workspace.create` não criará nem atualizará um espaço de trabalho se já existir, mesmo que a região especificada seja diferente.
  
* **O caderno da amostra falha:** Se um caderno de amostras falhar com um erro que a propriedade, o método ou a biblioteca não existem:
  * Certifique-se de que o núcleo correto foi selecionado no caderno jupyter. O núcleo é apresentado no lado superior direito da página do caderno. O padrão é azure_automl. Note que o núcleo é guardado como parte do caderno. Por isso, se mudar para um novo ambiente conda, terá de selecionar o novo núcleo no caderno.
      * Para os Cadernos Azure, deve ser Python 3.6. 
      * Para ambientes conda locais, deve ser o nome ambiente conda que especificou em automl_setup.
  * Certifique-se de que o caderno é para a versão SDK que está a utilizar. Pode verificar a versão SDK executando `azureml.core.VERSION` numa cela de caderno jupyter. Pode descarregar a versão anterior dos cadernos de amostras do GitHub clicando no `Branch` botão, selecionando o `Tags` separador e selecionando a versão.

* **A importação de numpy falha no Windows**: Alguns ambientes windows vêem um erro de carregamento numpy com a versão python mais recente 3.6.8. Se vir esta edição, experimente com a versão Python 3.6.7.

* **Falha na importação de numpy**: Verifique a versão TensorFlow no ambiente automatizado ml conda. As versões suportadas são < 1.13. Desinstalar o TensorFlow do ambiente se a versão for >= 1.13 Pode verificar a versão do TensorFlow e desinstalar da seguinte forma -
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

### <a name="detaching-azure-kubernetes-service"></a>Destacamento serviço Azure Kubernetes

A utilização do estúdio Azure Machine Learning, SDK ou a extensão Azure CLI para aprendizagem automática para separar um cluster AKS não elimina o cluster AKS. Para eliminar o cluster, consulte [utilizar o CLI Azure com AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster).

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices em falhas de serviço Azure Kubernetes

Muitas das falhas de Webservices no Azure Kubernetes Service podem ser depuradas ao estabelecer ligação ao cluster com `kubectl`. Você pode obter o `kubeconfig.json` para um Cluster de ServiçoS Azure Kubernetes executando

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

## <a name="compute-cluster-wont-resize"></a>O cluster computacional não vai redimensionar

Se o seu cluster de cálculo Azure Machine Learning aparecer preso na redimensionamento (0-> 0) para o estado do nó, este pode ser causado por bloqueios de recursos Azure.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Passos seguintes

Ver mais artigos de resolução de problemas para Azure Machine Learning:

* [Resolução de problemas de implementação de Docker com Azure Machine Learning](how-to-troubleshoot-deployment.md)
* [Oleodutos de aprendizagem automática de depuragem de depurações](how-to-debug-pipelines.md)
* [Depurar a aula ParallelRunStep do Azure Machine Learning SDK](how-to-debug-parallel-run-step.md)
* [Depuragem interativa de uma instância computacional de aprendizagem automática com código VS](how-to-debug-visual-studio-code.md)
* [Utilizar insights de aplicações para depurar oleodutos de aprendizagem automática](./how-to-log-pipelines-application-insights.md)

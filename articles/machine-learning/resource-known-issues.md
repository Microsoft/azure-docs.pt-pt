---
title: Questões conhecidas e resolução de problemas
titleSuffix: Azure Machine Learning
description: Obtenha uma lista dos problemas conhecidos, saroundaround sques e resolução de problemas para Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 40749a80d99782a1ea84b27e68376ea2870e8eb7
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138013"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Questões conhecidas e resolução de problemas Azure Machine Learning

Este artigo ajuda-o a encontrar e corrigir erros ou falhas encontrados ao utilizar o Azure Machine Learning.

## <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Interrupção: Upgrade SR-IOV para máquinas NCv3 em AmlCompute

A Azure Compute vai atualizar os NCv3 SKUs a partir do início de novembro de 2019 para apoiar todas as implementações e versões de MPI, e verbos RDMA para máquinas virtuais equipadas com InfiniBand. Isto exigirá um curto período de inatividade - [leia mais sobre a atualização SR-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Como cliente da oferta de cálculo gerida pela Azure Machine Learning (AmlCompute), não é obrigado a fazer quaisquer alterações neste momento. Com base no calendário de [atualização,](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) teria de planear para uma pequena pausa no seu treino. O serviço assumirá a responsabilidade de atualizar as imagens VM nos seus nós de cluster e aumentar automaticamente o seu cluster. Uma vez concluída a atualização, poderá utilizar todas as outras distribuições de MPI (como OpenMPI com Pytorch) além de obter maior largura de banda InfiniBand, latências mais baixas e melhor desempenho de aplicação distribuído.

## <a name="azure-machine-learning-designer-issues"></a>Edições de designer de aprendizagem automática azure

Problemas conhecidos com o designer.

### <a name="long-compute-preparation-time"></a>Longo tempo de preparação de cálculo

Criar uma nova computação ou evocar a saída de cálculo leva tempo, pode ser alguns minutos ou até mais. A equipa está a trabalhar para a otimização.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Não pode executar uma experiência apenas contém um conjunto de dados 

Pode querer executar uma experiência apenas contém conjunto de dados para visualizar o conjunto de dados. No entanto, não é permitido executar uma experiência apenas contém conjunto de dados hoje. Estamos a resolver ativamente esta questão.
 
Antes da correção, pode ligar o conjunto de dados a qualquer módulo de transformação de dados (Selecione Colunas em Dataset, Editar Metadados, Dados Divididos, etc.) e executar a experiência. Depois pode visualizar o conjunto de dados. 

Abaixo a imagem mostra como: ![](./media/resource-known-issues/aml-visualize-data.png) de dados visulize

## <a name="sdk-installation-issues"></a>Problemas de instalação do SDK

**Mensagem de erro: Não pode desinstalar 'PyYAML'**

SDK de Aprendizado de máquina do Azure para Python: PyYAML é um projeto de distutils instalado. Portanto, não podemos determinar com precisão quais os ficheiros que lhe pertencem se existe uma desinstalação parcial. Para continuar a instalação do SDK ao ignorar este erro, utilize:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Mensagem de erro: `ERROR: No matching distribution found for azureml-dataprep-native`**

A distribuição Python 3.7.4 da Anaconda tem um bug que quebra a instalação azureml-sdk. Esta questão é discutida nesta edição do [GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195) Esta pode ser trabalhada através da criação de um novo Conda Environment usando este comando:
```bash
conda create -n <env-name> python=3.7.3
```
O que cria um Ambiente Conda usando python 3.7.3, que não tem o problema de instalação presente em 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemas ao criar a computação do Azure Machine Learning

Há uma chance de rara que alguns usuários que criou a sua área de trabalho do Azure Machine Learning do portal do Azure antes do lançamento de DG poderão não conseguir criar a computação do Azure Machine Learning nessa área de trabalho. Pode emitir um pedido de suporte com o serviço ou criar uma nova área de trabalho através do Portal ou o SDK para desbloquear-se imediatamente.

## <a name="image-building-failure"></a>Falha de criação de imagem

Imagem de criação Falha ao implementar o serviço web. Solução alternativa é adicionar "pynacl = = 1.2.1" como uma dependência de pip Conda ficheiro para a configuração de imagem.

## <a name="deployment-failure"></a>Falha de implantação

Se observar `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, mude o SKU para VMs utilizado na sua implantação para um que tenha mais memória.

## <a name="fpgas"></a>FPGAs

Não será capaz de implementar os modelos no FPGAs até que tiver solicitado e foi aprovada para a quota FPGA. Para solicitar acesso, preencha o formulário de pedido de quota: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Aprendizagem automática automatizada

Tensor Flow A aprendizagem automática de máquinas não suporta atualmente a versão de fluxo de tensores 1.13. A instalação desta versão fará com que as dependências dos pacotes deixem de funcionar. Estamos a trabalhar para resolver esta questão num futuro lançamento. 

### <a name="experiment-charts"></a>Gráficos de experiências

Os gráficos de classificação binários (precisão-recall, ROC, gain curve etc.) mostrados em iterações automatizadas de experiênciaml não estão a renderizar corretamente na interface do utilizador desde 4/12. Os lotes de gráficos estão atualmente a mostrar resultados inversos, onde modelos de melhor desempenho são mostrados com resultados mais baixos. Uma resolução está sob investigação.

## <a name="datasets-and-data-preparation"></a>Conjuntos de dados e preparação de dados

Estas são questões conhecidas para conjuntos de dados de aprendizagem automática Azure.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>Erro de tipo: FileNotFound: Nenhum ficheiro ou diretório deste tipo

Este erro ocorre se o caminho de ficheiro que fornece não estiver onde o ficheiro está localizado. Tem de se certificar de que a forma como se refere ao ficheiro é consistente com a forma como montou o seu conjunto de dados no seu alvo de cálculo. Para garantir um estado determinista, recomendamos a utilização do caminho abstrato ao montar um conjunto de dados para um alvo de cálculo. Por exemplo, no seguinte código montamos o conjunto de dados sob a raiz do sistema de ficheiros do alvo da computação, `/tmp`. 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Se não incluir o principal corte dianteiro, '/', terá de pré-fixar o diretório de trabalho, por exemplo, `/mnt/batch/.../tmp/dataset` no alvo do cálculo para indicar onde pretende que o conjunto de dados seja montado. 

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Não ler ficheiro Parquet do HTTP ou DaALS Gen 2

Existe um problema conhecido na versão 1.1.25 do AzureML DataPrep SDK que causa uma falha ao criar um conjunto de dados lendo ficheiros Parquet a partir de HTTP ou ADLS Gen 2. Falhará com `Cannot seek once reading started.`. Para corrigir este problema, por favor atualize `azureml-dataprep` para uma versão superior a 1.1.26, ou desagrade para uma versão inferior a 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: mount() tem um argumento inesperado de palavra-chave 'invocation_id'

Este erro ocorre se tiver uma versão incompatível entre `azureml-core` e `azureml-dataprep`. Se vir este erro, atualize `azureml-dataprep` pacote para uma versão mais recente (maior ou igual a 1.1.29).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Problemas de Databricks e o Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Falha na instalação de pacotes

A instalação SDK de Aprendizagem automática Azure falha nos Tijolos de Dados Azure quando mais pacotes são instalados. Alguns pacotes, como `psutil`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão da biblioteca. Esta questão está relacionada com os Databricks e não com o Azure Machine Learning SDK. Pode experimentar este problema com outras bibliotecas, também. Exemplo:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Em alternativa, pode utilizar scripts de init e de te colocar esforçadamente a instalar problemas com bibliotecas Python. Esta abordagem não é oficialmente apoiada. Para mais informações, consulte [scripts init](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)com mira de Cluster .

### <a name="cancel-an-automated-machine-learning-run"></a>Cancele uma corrida automatizada de aprendizagem automática de máquinas

Quando utilizar capacidades automatizadas de aprendizagem automática em Pinos de Dados, para cancelar uma corrida e iniciar uma nova experiência, reinicie o cluster DeBricks Azure.

### <a name="10-iterations-for-automated-machine-learning"></a>>10 iterações para aprendizagem automática de máquinas

Em configurações automatizadas de aprendizagem automática, se tiver mais de 10 iterações, detete `show_output` para `False` quando submeter a execução.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Widget para o Azure Machine Learning SDK e machine learning automatizado

O widget SDK de aprendizagem automática Azure não é suportado num caderno databricks porque os cadernos não podem analisar widgets HTML. Pode ver o widget no portal utilizando este código Python na sua célula de caderno Azure Databricks:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Erro de importação: Nenhum módulo chamado 'pandas.core.indexes'

Se vir este erro quando utilizar machine learning automatizado:

1. Execute este comando para instalar dois pacotes no seu cluster De Tijolos De Dados Azure: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Desaque e, em seguida, recoloque o cluster ao seu caderno. 

Se estes passos não resolverem o problema, tente reiniciar o cluster.

### <a name="failtosendfeather"></a>FalhatoEnviarpena

Se vir um erro `FailToSendFeather` ao ler dados sobre o cluster Azure Databricks, consulte as seguintes soluções:

* Atualize `azureml-sdk[automl]` pacote para a versão mais recente.
* Adicione `azureml-dataprep` versão 1.1.8 ou superior.
* Adicione `pyarrow` versão 0.11 ou superior.

## <a name="azure-portal"></a>Portal do Azure

Se vá diretamente para ver a sua área de trabalho a partir de uma ligação de partilha do SDK ou o portal, não será capaz de exibir a página de descrição geral normal com informações de subscrição na extensão. Também não será capaz de alternar para outra área de trabalho. Se precisar de ver outro espaço de trabalho, a soposição é ir diretamente ao [estúdio Azure Machine Learning](https://ml.azure.com) e procurar o nome do espaço de trabalho.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

Por vezes, pode ser útil se pode fornecer informações de diagnóstico quando pedir ajuda. Para ver alguns registos, visite o [estúdio Azure Machine Learning](https://ml.azure.com) e vá ao seu espaço de trabalho e selecione **Workspace > Experiment > Run > Logs**.  

> [!NOTE]
> O Azure Machine Learning regista informações de várias fontes durante o treino, como o AutoML ou o contentor Docker que executa o trabalho de formação. Muitos destes troncos não estão documentados. Se encontrar problemas e contactar o suporte da Microsoft, poderão utilizar estes registos durante a resolução de problemas.

## <a name="activity-logs"></a>Registos de atividade

Algumas ações no espaço de trabalho do Azure Machine Learning não registam informações no __registo de Atividades.__ Por exemplo, iniciar uma corrida de formação ou registar um modelo.

Algumas destas ações aparecem na área de __Atividades__ do seu espaço de trabalho, no entanto não indicam quem iniciou a atividade.

## <a name="resource-quotas"></a>Quotas de recursos

Conheça as quotas de [recursos](how-to-manage-quotas.md) que pode encontrar ao trabalhar com a Azure Machine Learning.

## <a name="authentication-errors"></a>Erros de autenticação

Se executar uma operação de gestão num alvo de cálculo a partir de um trabalho remoto, receberá um dos seguintes erros:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, receberá um erro se tentar criar ou anexar um alvo de cálculo a partir de um Pipeline ML que é submetido para execução remota.

## <a name="overloaded-azurefile-storage"></a>Armazenamento AzureFile sobrecarregado

Se receber um erro `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, aplique as seguintes sintetções.

Se estiver a utilizar a partilha de ficheiros para outras cargas de trabalho, como a transferência de dados, a recomendação é utilizar bolhas para que a partilha de ficheiros seja livre de ser utilizada para a apresentação de execuções. Também pode dividir a carga de trabalho entre dois espaços de trabalho diferentes.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices in Azure Kubernetes Service falha 

Muitas falhas de serviço web no Serviço Azure Kubernetes podem ser depuradas ligando-se ao cluster usando `kubectl`. Você pode obter o `kubeconfig.json` para um Cluster de Serviço Azure Kubernetes executando

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Atualizar componentes de Machine Learning Azure no cluster AKS

As atualizações dos componentes de Machine Learning Azure instaladas num cluster de serviço Azure Kubernetes devem ser aplicadas manualmente. 

Pode aplicar estas atualizações desmontando o cluster do espaço de trabalho Azure Machine Learning e, em seguida, recolocando o cluster no espaço de trabalho. Se o SSL estiver ativado no cluster, terá de fornecer o certificado SSL e a chave privada ao voltar a ligar o cluster. 

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

Se já não tiver o certificado SSL e a chave privada, ou estiver a utilizar um certificado gerado pelo Azure Machine Learning, pode recuperar os ficheiros antes de desmontar o cluster ligando-se ao cluster utilizando `kubectl` e recuperando o `azuremlfessl`secreto .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos em formato codificado base-64. Terá de descodificar os componentes `cert.pem` e `key.pem` dos segredos antes de os fornecer para `attach_config.enable_ssl`. 

## <a name="recommendations-for-error-fix"></a>Recomendações para correção de erros
Com base na observação geral, aqui estão as recomendações do Azure ML para corrigir alguns dos erros comuns no Azure ML.

### <a name="metric-document-is-too-large"></a>O documento métrico é muito grande
O Azure Machine Learning tem limites internos no tamanho de objetos métricos que podem ser registados ao mesmo tempo a partir de um treino. Se encontrar um erro "Metric Document is too large" ao registar uma métrica avaliada pela lista, tente dividir a lista em pedaços menores, por exemplo:

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

 Internamente, o serviço de história de execução concatena os blocos com o mesmo nome métrico numa lista contígua.

### <a name="moduleerrors-no-module-named"></a>MóduloErros (Nenhum módulo nomeado)
Se estiver a encontrar MóduloSErrors enquanto submete experiências no Azure ML, significa que o script de treino está à espera de um pacote para ser instalado, mas não é adicionado. Assim que fornecer o nome do pacote, o Azure ML instalará o pacote no ambiente utilizado para o seu treino. 

Se estiver a utilizar estimadores para submeter [experiências,](concept-azure-machine-learning-architecture.md#estimators) pode especificar um nome de pacote através de `pip_packages` ou `conda_packages` parâmetro no estimador com base na fonte que pretende instalar a embalagem. Também pode especificar um ficheiro yml com todas as suas dependências usando `conda_dependencies_file`ou listar todos os seus requisitos de pip num ficheiro txt usando `pip_requirements_file` parâmetro.

O Azure ML também fornece estimativas específicas para a tensorflow, pyTorch, chainer e SKLearn. A utilização destes estimadores certificar-se-á de que as dependências-quadro são instaladas em seu nome no ambiente utilizado para a formação. Tem a opção de especificar dependências extras acima descritas. 
 
O Azure ML manteve imagens de estivadores e o seu conteúdo pode ser visto em [contentores AzureML](https://github.com/Azure/AzureML-Containers).
As dependências específicas do quadro estão listadas na respetiva documentação-quadro - [Chainer,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks) [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks) [TensorFlow,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks) [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

> [!Note]
> Se você acha que um pacote específico é comum o suficiente para ser adicionado em Imagens e ambientes mantidos Azure ML, por favor levante uma edição GitHub em [contentores AzureML](https://github.com/Azure/AzureML-Containers). 
 
 ### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>Error de nome (Nome não definido), AttributeError (Objeto não tem atributo)
Esta exceção deve vir dos seus guiões de treino. Pode ver os ficheiros de registo do portal Azure para obter mais informações sobre o nome específico não definido ou erro de atributo. A partir do SDK, pode utilizar `run.get_details()` para ver a mensagem de erro. Isto também listará todos os ficheiros de registo gerados para a sua execução. Por favor, certifique-se de dar uma olhada no seu script de treino, corrigir o erro antes de tentar novamente. 

### <a name="horovod-is-shut-down"></a>Horovod é desligado
Na maioria dos casos, esta exceção significa que houve uma exceção subjacente num dos processos que fez com que o horovod fosse encerrado. Cada posto no trabalho de MPI obtém o seu próprio ficheiro de log dedicado no Azure ML. Estes registos são chamados `70_driver_logs`. Em caso de treino distribuído, os nomes de registo são sufixos com `_rank` para facilitar a diferenciação dos registos. Para encontrar o erro exato que causou o encerramento de Horovod, passe por todos os ficheiros de registo e procure `Traceback` no final dos ficheiros driver_log. Um destes ficheiros vai dar-lhe a exceção subjacente real. 

## <a name="labeling-projects-issues"></a>Questões de projetos de rotulagem

Questões conhecidas com projetos de rotulagem.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Apenas conjuntos de dados criados em lojas de dados blob podem ser usados

Esta é uma limitação conhecida da versão atual. 

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>Após a criação, o projeto mostra "Inicialização" por muito tempo

Refresque manualmente a página. A inicialização deve prosseguir em cerca de 20 pontos de dados por segundo. A falta de auto-actualização é uma questão conhecida. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>Ao rever as imagens, as imagens recentemente rotuladas não são mostradas

Para carregar todas as imagens etiquetadas, escolha **o** primeiro botão. O **primeiro** botão irá levá-lo de volta para a frente da lista, mas carrega todos os dados rotulados.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>Premir a tecla Esc enquanto se rotula para deteção de objetos cria uma etiqueta de tamanho zero no canto superior esquerdo. A apresentação de rótulos neste estado falha.

Elimine a etiqueta clicando na marca transversal ao lado.

## <a name="run-or-experiment-deletion"></a>Executar ou experimentar a eliminação

As experiências podem ser arquivadas utilizando o método [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) ou a partir da visualização do separador Experiment no cliente do estúdio Azure Machine Learning. Esta ação esconde a experiência de consultas e pontos de vista da lista, mas não a apaga.

A eliminação permanente de experiências ou execuções individuais não é atualmente suportada. Para obter mais informações sobre a eliminação de ativos do Workspace, consulte Exportar ou eliminar os dados do espaço de trabalho do [serviço de aprendizagem automática](how-to-export-delete-data.md).

## <a name="moving-the-workspace"></a>Movendo o espaço de trabalho

> [!WARNING]
> A mudança do seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou a mudança da subscrição própria para um novo inquilino, não é suportada. Fazê-lo pode causar erros.
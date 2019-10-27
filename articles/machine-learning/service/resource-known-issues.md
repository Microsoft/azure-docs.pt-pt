---
title: Problemas conhecidos & solução de problemas
titleSuffix: Azure Machine Learning
description: Obtenha uma lista dos problemas conhecidos, soluções alternativas e solução de problemas para Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 0dd0b8cf39da8039b3a59bf243284e0d5062bd78
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965602"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Problemas conhecidos e solução de problemas Azure Machine Learning

Este artigo ajuda você a encontrar e corrigir erros ou falhas encontrados ao usar Azure Machine Learning.

## <a name="upcoming-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Próxima atualização de SR-IOV para computadores NCv3 no AmlCompute

A computação do Azure atualizará os SKUs do NCv3 a partir de novembro para dar suporte a todas as implementações e versões de MPI e verbos RDMA para máquinas virtuais equipados com InfiniBand. Isso exigirá um breve tempo de inatividade- [Leia mais sobre a atualização de Sr-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Como cliente da AmlCompute (oferta de computação gerenciada) de Azure Machine Learning, não é necessário fazer nenhuma alteração no momento. Com base na [agenda de atualização](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) , você precisaria planejar um pequeno intervalo de treinamento. O serviço assumirá a responsabilidade de atualizar as imagens de VM em seus nós de cluster e escalonar automaticamente o cluster. Depois que a atualização for concluída, você poderá usar todos os outros discibutions MPI (como o OpenMPi com Pytorch) Além de obter largura de banda InfiniBand mais alta, latências menores e melhor desempenho de aplicativo distribuído.

## <a name="visual-interface-issues"></a>Problemas de interface visual

Interface visual para problemas do serviço de Machine Learning.

### <a name="long-compute-preparation-time"></a>Tempo de preparação de computação longa

Criar nova computação ou evocar deixar computação leva tempo, pode ser de alguns minutos ou ainda mais. A equipe está trabalhando para otimização.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Não é possível executar um experimento somente contém DataSet 

Talvez você queira executar um experimento que contenha apenas o conjunto de um para visualizar o conjunto de um. No entanto, não é permitido executar um experimento apenas no momento. Estamos corrigindo ativamente esse problema.
 
Antes da correção, você pode conectar o conjunto de dados a qualquer módulo de Data Transformation (selecione as colunas no DataSet, editar metadados, dividir dados etc.) e executar o experimento. Em seguida, você pode visualizar o conjunto de os. 

A imagem abaixo mostra como: ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemas de instalação do SDK

**Mensagem de erro: não é possível desinstalar ' PyYAML '**

Azure Machine Learning SDK para Python: PyYAML é um projeto distutils instalado. Portanto, não podemos determinar com precisão quais arquivos pertencem a ele se houver uma desinstalação parcial. Para continuar a instalação do SDK ao ignorar esse erro, use:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Mensagem de erro: `ERROR: No matching distribution found for azureml-dataprep-native`**

A distribuição do Python 3.7.4 do Anaconda tem um bug que interrompe a instalação do azureml-SDK. Esse problema é abordado neste [problema do GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195) , que pode ser solucionado criando um novo ambiente Conda usando este comando:
```bash
conda create -n <env-name> python=3.7.3
```
Que cria um ambiente Conda usando o Python 3.7.3, que não tem o problema de instalação presente no 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemas ao criar Azure Machine Learning computação

Há uma rara chance de que alguns usuários que criaram seus Azure Machine Learning espaço de trabalho do portal do Azure antes da versão GA não possam criar Azure Machine Learning computação nesse espaço de trabalho. Você pode gerar uma solicitação de suporte em relação ao serviço ou criar um novo espaço de trabalho por meio do portal ou do SDK para desbloquear-se imediatamente.

## <a name="image-building-failure"></a>Falha na criação da imagem

Falha na criação da imagem ao implantar o serviço Web. A solução alternativa é adicionar "pynacl = = 1.2.1" como uma dependência Pip ao arquivo Conda para configuração de imagem.

## <a name="deployment-failure"></a>Falha na implantação

Se você observar `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, altere a SKU para VMs usadas em sua implantação para uma que tenha mais memória.

## <a name="fpgas"></a>FPGAs

Você não poderá implantar modelos no FPGAs até ter solicitado e aprovado para a cota de FPGA. Para solicitar acesso, preencha o formulário de solicitação de cota: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Aprendizagem automática automatizada

O aprendizado de máquina automatizado do tensor Flow atualmente não dá suporte à versão 1,13 do fluxo do tensor. A instalação desta versão fará com que as dependências do pacote parem de funcionar. Estamos trabalhando para corrigir esse problema em uma versão futura. 

### <a name="experiment-charts"></a>Gráficos de experimento

Os gráficos de classificação binária (recall de precisão, ROC, curva de lucro etc.) mostrados em iterações de experimento de ML automatizadas não são renderizados corretamente na interface do usuário desde 4/12. Atualmente, as plotagens de gráfico estão mostrando resultados inversos, onde os modelos de melhor desempenho são mostrados com resultados mais baixos. Uma resolução está sob investigação.

## <a name="datasets-and-data-preparation"></a>Conjuntos de dados e preparo

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Falha ao ler o arquivo parquet do HTTP ou ADLS Gen 2

Há um problema conhecido na versão 1.1.25 do AzureML dataprep SDK que causa uma falha ao criar um conjunto de uma leitura de arquivos parquet de HTTP ou ADLS Gen 2. Para corrigir esse problema, atualize para uma versão superior a 1.1.26 ou faça downgrade para uma versão inferior a 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Databricks e problemas de Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Falha ao instalar pacotes

A instalação do SDK do Azure Machine Learning falha em Azure Databricks quando mais pacotes são instalados. Alguns pacotes, como `psutil`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão da biblioteca. Esse problema está relacionado ao databricks e não ao SDK do Azure Machine Learning. Você também pode experimentar esse problema com outras bibliotecas. Exemplo:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Como alternativa, você pode usar scripts de inicialização se continuar enfrentando problemas de instalação com bibliotecas do Python. Essa abordagem não tem suporte oficial. Para obter mais informações, consulte [scripts de inicialização no escopo do cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Cancelar uma execução de Machine Learning automatizada

Quando você usa recursos automatizados de aprendizado de máquina no Azure Databricks, para cancelar uma execução e iniciar uma nova execução de experimento, reinicie o cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterações para o aprendizado de máquina automatizado

Em configurações automatizadas do Machine Learning, se você tiver mais de 10 iterações, defina `show_output` como `False` ao enviar a execução.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget para o SDK do Azure Machine Learning/Machine Learning automatizado

O widget SDK do Azure Machine Learning não tem suporte em um bloco de anotações do databricks porque os notebooks não podem analisar widgets HTML. Você pode exibir o widget no portal usando este código Python na célula Azure Databricks notebook:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Erro de importação: nenhum módulo chamado ' pandas. Core. Indexes '

Se você vir esse erro ao usar o aprendizado de máquina automatizado:

1. Execute este comando para instalar dois pacotes no cluster de Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Desanexe e anexe novamente o cluster ao seu bloco de anotações. 

Se essas etapas não resolverem o problema, tente reiniciar o cluster.

### <a name="failtosendfeather"></a>FailToSendFeather

Se você vir um erro `FailToSendFeather` ao ler dados no cluster Azure Databricks, consulte as seguintes soluções:

* Atualize `azureml-sdk[automl]` pacote para a versão mais recente.
* Adicione `azure-dataprep` versão 1.1.8 ou superior.
* Adicione `pyarrow` versão 0,11 ou superior.

## <a name="azure-portal"></a>Portal do Azure

Se você for diretamente para exibir o espaço de trabalho de um link de compartilhamento no SDK ou no portal, não será possível exibir a página de visão geral normal com as informações de assinatura na extensão. Você também não será capaz de alternar para outro espaço de trabalho. Se você precisar exibir outro espaço de trabalho, a solução alternativa será ir diretamente para a [portal do Azure](https://portal.azure.com) e procurar o nome do espaço de trabalho.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

Às vezes, pode ser útil se você puder fornecer informações de diagnóstico ao solicitar ajuda. Para ver alguns logs, visite [portal do Azure](https://portal.azure.com) e vá para seu espaço de trabalho e selecione **espaço de trabalho > teste > executar logs do >** .  Você também pode encontrar essas informações na seção **experimentos** da [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com).

> [!NOTE]
> Azure Machine Learning registra as informações de uma variedade de fontes durante o treinamento, como AutoML ou o contêiner do Docker que executa o trabalho de treinamento. Muitos desses logs não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles podem ser capazes de usar esses logs durante a solução de problemas.

## <a name="activity-logs"></a>Registos de atividade

Algumas ações dentro do espaço de trabalho Azure Machine Learning não registram informações no __log de atividades__. Por exemplo, iniciar uma execução de treinamento ou registrar um modelo.

Algumas dessas ações aparecem na área __atividades__ do seu espaço de trabalho, no entanto, elas não indicam quem iniciou a atividade.

## <a name="resource-quotas"></a>Quotas de recursos

Saiba mais sobre as [cotas de recursos](how-to-manage-quotas.md) que você pode encontrar ao trabalhar com Azure Machine Learning.

## <a name="authentication-errors"></a>Erros de autenticação

Se você executar uma operação de gerenciamento em um destino de computação de um trabalho remoto, receberá um dos seguintes erros:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, você receberá um erro se tentar criar ou anexar um destino de computação de um pipeline de ML que é enviado para execução remota.

## <a name="overloaded-azurefile-storage"></a>Armazenamento do Azurefile sobrecarregado

Se você receber um erro `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, aplique as seguintes soluções alternativas.

Se você estiver usando o compartilhamento de arquivos para outras cargas de trabalho, como a transferência de dados, a recomendação será usar BLOBs para que o compartilhamento de arquivos esteja livre para ser usado para o envio de execuções. Você também pode dividir a carga de trabalho entre dois espaços diferentes.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>WebServices em falhas do serviço kubernetes do Azure 

Muitas falhas de WebService no serviço kubernetes do Azure podem ser depuradas conectando-se ao cluster usando `kubectl`. Você pode obter o `kubeconfig.json` para um cluster do serviço kubernetes do Azure executando

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Atualizando Azure Machine Learning componentes no cluster AKS

As atualizações para Azure Machine Learning componentes instalados em um cluster do serviço kubernetes do Azure devem ser aplicadas manualmente. 

> [!WARNING]
> Antes de executar as seguintes ações, verifique a versão do seu cluster do serviço kubernetes do Azure. Se a versão do cluster for igual ou maior que 1,14, você não poderá anexar novamente o cluster ao espaço de trabalho Azure Machine Learning.

Você pode aplicar essas atualizações desanexando o cluster do espaço de trabalho Azure Machine Learning e anexando novamente o cluster ao espaço de trabalho. Se o SSL estiver habilitado no cluster, será necessário fornecer o certificado SSL e a chave privada ao anexar novamente o cluster. 

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

Se você não tiver mais o certificado SSL e a chave privada, ou se estiver usando um certificado gerado pelo Azure Machine Learning, poderá recuperar os arquivos antes de desanexar o cluster conectando-se ao cluster usando `kubectl` e recuperando o segredo `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos no formato codificado em base-64. Você precisará definir-64 decodificar os `cert.pem` e `key.pem` os componentes dos segredos antes de fornecer a eles `attach_config.enable_ssl`. 

## <a name="recommendations-for-error-fix"></a>Recomendações para correção de erro
Com base na observação geral, aqui estão as recomendações do Azure ML para corrigir alguns dos erros comuns no Azure ML.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (nenhum módulo chamado)
Se você estiver executando o ModuleErrors enquanto envia experimentos no Azure ML, significa que o script de treinamento está esperando que um pacote seja instalado, mas não adicionado. Depois de fornecer o nome do pacote, o Azure ML instalará o pacote no ambiente usado para seu treinamento. 

Se você estiver usando [estimadores](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#estimators) para enviar experimentos, poderá especificar um nome de pacote por meio de `pip_packages` ou `conda_packages` parâmetro no estimador com base em qual fonte você deseja instalar o pacote. Você também pode especificar um arquivo yml com todas as suas dependências usando `conda_dependencies_file`ou listar todos os seus requisitos de Pip em um arquivo txt usando `pip_requirements_file` parâmetro.

O Azure ML também fornece estimadores específicos da estrutura para Tensorflow, PyTorch, chainer e SKLearn. Usar esses estimadores garantirá que as dependências de estrutura sejam instaladas em seu nome no ambiente usado para treinamento. Você tem a opção de especificar dependências extras, conforme descrito acima. 
 
 As imagens do Docker mantidas pelo Azure ML e seu conteúdo podem ser vistos em [contêineres do AzureML](https://github.com/Azure/AzureML-Containers).
As dependências específicas à estrutura são listadas no respectivo Framework Documentation- [Chainr](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

>[Observação!] Se você considerar que um pacote específico é comum o suficiente para ser adicionado em ambientes e imagens mantidas do Azure ML, gere um problema do GitHub nos [contêineres do AzureML](https://github.com/Azure/AzureML-Containers). 
 
 ### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (nome não definido), AttributeError (objeto sem atributo)
Essa exceção deve vir de seus scripts de treinamento. Você pode examinar os arquivos de log de portal do Azure para obter mais informações sobre o nome específico não definido ou erro de atributo. No SDK, você pode usar `run.get_details()` para examinar a mensagem de erro. Isso também listará todos os arquivos de log gerados para sua execução. Certifique-se de dar uma olhada no script de treinamento, corrija o erro antes de tentar novamente. 

### <a name="horovod-is-shutdown"></a>Horovod está desligado
Na maioria dos casos, essa exceção significa que houve uma exceção subjacente em um dos processos que causaram o desligamento de horovod. Cada classificação no trabalho MPI obtém seu próprio arquivo de log dedicado no Azure ML. Esses logs são nomeados `70_driver_logs`. No caso de treinamento distribuído, os nomes de log têm o sufixo `_rank` para facilitar a diferenciação dos logs. Para localizar o erro exato que causou o desligamento de horovod, percorra todos os arquivos de log e procure `Traceback` no final dos arquivos driver_log. Um desses arquivos fornecerá a exceção subjacente real. 

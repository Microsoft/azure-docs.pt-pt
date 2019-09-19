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
ms.openlocfilehash: 81eabadba70a2d5334fab43157f17d24c41d97ec
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103421"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Problemas conhecidos e solução de problemas Azure Machine Learning

Este artigo ajuda você a encontrar e corrigir erros ou falhas encontrados ao usar Azure Machine Learning.

## <a name="visual-interface-issues"></a>Problemas de interface visual

Interface visual para problemas do serviço de Machine Learning.

### <a name="long-compute-preparation-time"></a>Tempo de preparação de computação longa

Criar nova computação ou evocar deixar computação leva tempo, pode ser de alguns minutos ou ainda mais. A equipe está trabalhando para otimização.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Não é possível executar um experimento somente contém DataSet 

Talvez você queira executar um experimento que contenha apenas o conjunto de um para visualizar o conjunto de um. No entanto, não é permitido executar um experimento apenas no momento. Estamos corrigindo ativamente esse problema.
 
Antes da correção, você pode conectar o conjunto de dados a qualquer módulo de Data Transformation (selecione as colunas no DataSet, editar metadados, dividir dados etc.) e executar o experimento. Em seguida, você pode visualizar o conjunto de os. 

A imagem abaixo mostra como ![: visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemas de instalação do SDK

**Mensagem de erro: Não é possível desinstalar ' PyYAML '**

SDK do Azure Machine Learning para Python: PyYAML é um projeto do distutils instalado. Portanto, não podemos determinar com precisão quais arquivos pertencem a ele se houver uma desinstalação parcial. Para continuar a instalação do SDK ao ignorar este erro, utilize:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Mensagem de erro: `ERROR: No matching distribution found for azureml-dataprep-native`**

A distribuição do Python 3.7.4 do Anaconda tem um bug que interrompe a instalação do azureml-SDK. Esse problema é abordado neste [problema do GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195) , que pode ser solucionado criando um novo ambiente Conda usando este comando:
```bash
conda create -n <env-name> python=3.7.3
```
Que cria um ambiente Conda usando o Python 3.7.3, que não tem o problema de instalação presente no 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemas ao criar a computação do Azure Machine Learning

Há uma chance de rara que alguns usuários que criou a sua área de trabalho do Azure Machine Learning do portal do Azure antes do lançamento de DG poderão não conseguir criar a computação do Azure Machine Learning nessa área de trabalho. Pode emitir um pedido de suporte com o serviço ou criar uma nova área de trabalho através do Portal ou o SDK para desbloquear-se imediatamente.

## <a name="image-building-failure"></a>Falha de criação de imagem

Imagem de criação Falha ao implementar o serviço web. Solução alternativa é adicionar "pynacl = = 1.2.1" como uma dependência de pip Conda ficheiro para a configuração de imagem.

## <a name="deployment-failure"></a>Falha na implantação

Se você observar `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, altere a SKU para VMs usadas em sua implantação para uma que tenha mais memória.

## <a name="fpgas"></a>FPGAs

Não será capaz de implementar os modelos no FPGAs até que tiver solicitado e foi aprovada para a quota FPGA. Para pedir acesso, preencha o formulário de pedido de quota: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Aprendizagem automática automatizada

O aprendizado de máquina automatizado do tensor Flow atualmente não dá suporte à versão 1,13 do fluxo do tensor. A instalação desta versão fará com que as dependências do pacote parem de funcionar. Estamos trabalhando para corrigir esse problema em uma versão futura. 

### <a name="experiment-charts"></a>Gráficos de experimento

Os gráficos de classificação binária (recall de precisão, ROC, curva de lucro etc.) mostrados em iterações de experimento de ML automatizadas não são renderizados corretamente na interface do usuário desde 4/12. Atualmente, as plotagens de gráfico estão mostrando resultados inversos, onde os modelos de melhor desempenho são mostrados com resultados mais baixos. Uma resolução está sob investigação.

## <a name="databricks"></a>Databricks

Problemas de Databricks e o Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Falha ao instalar pacotes

A instalação do SDK do Azure Machine Learning falha em Azure Databricks quando mais pacotes são instalados. Alguns pacotes e, por exemplo, `psutil`, pode causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão da biblioteca. Esse problema está relacionado ao databricks e não ao SDK do Azure Machine Learning. Você também pode experimentar esse problema com outras bibliotecas. Exemplo:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Como alternativa, você pode usar scripts de inicialização se continuar enfrentando problemas de instalação com bibliotecas do Python. Essa abordagem não tem suporte oficial. Para obter mais informações, consulte [scripts de inicialização no escopo do cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Cancelar uma execução de Machine Learning automatizada

Quando você usa recursos automatizados de aprendizado de máquina no Azure Databricks, para cancelar uma execução e iniciar uma nova execução de experimento, reinicie o cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterações para o aprendizado de máquina automatizado

Em configurações automatizadas do Machine Learning, se você tiver mais de 10 iterações `show_output` , `False` defina como quando enviar a execução.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget para o SDK do Azure Machine Learning/Machine Learning automatizado

O widget SDK do Azure Machine Learning não tem suporte em um bloco de anotações do databricks porque os notebooks não podem analisar widgets HTML. Você pode exibir o widget no portal usando este código Python na célula Azure Databricks notebook:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Erro de importação: Nenhum módulo chamado ' pandas. Core. Indexes '

Se você vir esse erro ao usar o aprendizado de máquina automatizado:

1. Execute este comando para instalar dois pacotes no cluster de Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Desanexe e anexe novamente o cluster ao seu bloco de anotações. 

Se essas etapas não resolverem o problema, tente reiniciar o cluster.

### <a name="failtosendfeather"></a>FailToSendFeather

Se você vir um `FailToSendFeather` erro ao ler dados no cluster Azure Databricks, consulte as seguintes soluções:

* Atualize `azureml-sdk[automl_databricks]` o pacote para a versão mais recente.
* Adicione `azure-dataprep` a versão 1.1.8 ou superior.
* Adicione `pyarrow` a versão 0,11 ou superior.

## <a name="azure-portal"></a>Portal do Azure

Se vá diretamente para ver a sua área de trabalho a partir de uma ligação de partilha do SDK ou o portal, não será capaz de exibir a página de descrição geral normal com informações de subscrição na extensão. Também não será capaz de alternar para outra área de trabalho. Se você precisar exibir outro espaço de trabalho, a solução alternativa será ir diretamente para a [portal do Azure](https://portal.azure.com) e procurar o nome do espaço de trabalho.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

Por vezes, pode ser útil se pode fornecer informações de diagnóstico quando pedir ajuda. Para ver alguns logs, visite [portal do Azure](https://portal.azure.com) e vá para seu espaço de trabalho e selecione **espaço de trabalho > teste > executar logs do >** .  Você também pode encontrar essas informações na seção **experimentos** da [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com).

> [!NOTE]
> Azure Machine Learning registra as informações de uma variedade de fontes durante o treinamento, como AutoML ou o contêiner do Docker que executa o trabalho de treinamento. Muitos desses logs não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles podem ser capazes de usar esses logs durante a solução de problemas.

## <a name="activity-logs"></a>Registos de atividade

Algumas ações dentro do espaço de trabalho Azure Machine Learning não registram informações no __log de atividades__. Por exemplo, iniciar uma execução de treinamento ou registrar um modelo.

Algumas dessas ações aparecem na área __atividades__ do seu espaço de trabalho, no entanto, elas não indicam quem iniciou a atividade.

## <a name="resource-quotas"></a>Quotas de recursos

Saiba mais sobre o [quotas de recursos](how-to-manage-quotas.md) que poderá encontrar ao trabalhar com o Azure Machine Learning.

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

Muitas falhas de WebService no serviço kubernetes do Azure podem ser depuradas conectando-se `kubectl`ao cluster usando. Você pode obter o `kubeconfig.json` para um cluster do serviço kubernetes do Azure executando

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Atualizando Azure Machine Learning componentes no cluster AKS

As atualizações para Azure Machine Learning componentes instalados em um cluster do serviço kubernetes do Azure devem ser aplicadas manualmente. Você pode aplicar esses clusters desanexando o cluster do espaço de trabalho Azure Machine Learning e anexando novamente o cluster ao espaço de trabalho. Se o SSL estiver habilitado no cluster, será necessário fornecer o certificado SSL e a chave privada ao anexar novamente o cluster. 

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

Se você não tiver mais o certificado SSL e a chave privada, ou se estiver usando um certificado gerado pelo Azure Machine Learning, poderá recuperar os arquivos antes de desanexar o cluster conectando-se ao cluster `kubectl` usando e recuperando o segredo `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos no formato codificado em base-64. Você precisará de base-64 decodificar `cert.pem` os `key.pem` componentes e dos segredos antes de fornecer a `attach_config.enable_ssl`eles. 
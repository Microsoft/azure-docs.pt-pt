---
title: Gestão do ciclo de vida das aplicações
titleSuffix: ML Studio (classic) - Azure
description: Aplicar as práticas recomendadas de gerenciamento do ciclo de vida do aplicativo no Azure Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 10/27/2016
ms.openlocfilehash: 3cc659bf633c74dab785b696cafb364974926aec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432231"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Gerenciamento do ciclo de vida do aplicativo no Azure Machine Learning Studio (clássico)
Azure Machine Learning Studio (clássico) é uma ferramenta para o desenvolvimento de experimentos de aprendizado de máquina que estão operacionais na plataforma de nuvem do Azure. É como o IDE do Visual Studio e o serviço de nuvem escalonável mesclados em uma única plataforma. Você pode incorporar as práticas de ALM (gerenciamento do ciclo de vida do aplicativo) padrão de controle de versão de vários ativos à execução e implantação automatizadas, em Azure Machine Learning Studio (clássico). Este artigo discute algumas das opções e abordagens.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Experimento de controle de versão
Há duas maneiras recomendadas de fazer a versão de seus experimentos. Você pode contar com o histórico de execução interno ou exportar o experimento em um formato JSON para gerenciá-lo externamente. Cada abordagem vem com seus prós e contras.

### <a name="experiment-snapshots-using-run-history"></a>Testar instantâneos usando o histórico de execuções
No modelo de execução do experimento de aprendizado Azure Machine Learning Studio (clássico), um instantâneo imutável do experimento é enviado para o Agendador de trabalhos sempre que você clica em **executar** no editor de experimento. Para exibir essa lista de instantâneos, clique em **histórico de execução** na barra de comandos na exibição do editor de experimento.

![Botão histórico de execuções](./media/version-control/runhistory.png)

Em seguida, você pode abrir o instantâneo no modo bloqueado clicando no nome do experimento no momento em que o experimento foi enviado para ser executado e o instantâneo foi tirado. Observe que apenas o primeiro item da lista, que representa o experimento atual, está em um estado editável. Observe também que cada instantâneo pode estar em vários Estados de status também, incluindo concluído (execução parcial), falha, falha (execução parcial) ou rascunho.

![Lista de histórico de execuções](./media/version-control/runhistorylist.png)

Depois de aberto, você pode salvar o teste de instantâneo como um novo experimento e, em seguida, modificá-lo. Se o seu instantâneo de experimento contiver ativos como modelos treinados, transformações ou conjuntos de valores que tenham versões atualizadas, o instantâneo manterá as referências à versão original quando o instantâneo foi tirado. Se você salvar o instantâneo bloqueado como um novo experimento, Azure Machine Learning Studio (clássico) detectará a existência de uma versão mais recente desses ativos e os atualizará automaticamente no novo experimento.

Se você excluir o experimento, todos os instantâneos desse experimento serão excluídos.

### <a name="exportimport-experiment-in-json-format"></a>Exportar/importar experimento no formato JSON
Os instantâneos de histórico de execução mantêm uma versão imutável do experimento em Azure Machine Learning Studio (clássico) sempre que é enviado para execução. Você também pode salvar uma cópia local do experimento e verificá-la em seu sistema de controle do código-fonte favorito, como Team Foundation Server e, posteriormente, recriar um experimento a partir desse arquivo local. Você pode usar o [Azure Machine Learning](https://aka.ms/amlps) commandlets do PowerShell [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) para fazer isso.

O arquivo JSON é uma representação textual do grafo experimento, que pode incluir uma referência a ativos no espaço de trabalho, como um conjunto de um ou um modelo treinado. Ele não contém uma versão serializada do ativo. Se você tentar importar o documento JSON de volta para o espaço de trabalho, os ativos referenciados já deverão existir com as mesmas IDs de ativos que são referenciadas no experimento. Caso contrário, não será possível acessar o experimento importado.

## <a name="versioning-trained-model"></a>Modelo treinado de controle de versão
Um modelo treinado no Azure Machine Learning Studio (clássico) é serializado em um formato conhecido como um arquivo iLearner (`.iLearner`) e é armazenado na conta de armazenamento de BLOBs do Azure associada ao espaço de trabalho. Uma maneira de obter uma cópia do arquivo iLearner é por meio da API de novo treinamento. [Este artigo](/azure/machine-learning/studio/retrain-machine-learning-model) explica como a API de novo treinamento funciona. As etapas de alto nível:

1. Configure seu teste de treinamento.
2. Adicione uma porta de saída do serviço Web ao módulo modelo de treinamento ou o módulo que produz o modelo treinado, como ajustar modelo de hiperparâmetro ou criar modelo R.
3. Execute o teste de treinamento e implante-o como um serviço Web de treinamento de modelo.
4. Chame o ponto de extremidade BES do serviço Web de treinamento e especifique o nome do arquivo iLearner desejado e o local da conta de armazenamento de BLOBs onde ele será armazenado.
5. Colete o arquivo iLearner produzido após a conclusão da chamada BES.

Outra maneira de recuperar o arquivo iLearner é por meio do cmdlet do PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Isso pode ser mais fácil se você quiser apenas obter uma cópia do arquivo iLearner sem a necessidade de treinar novamente o modelo de forma programática.

Depois de ter o arquivo iLearner que contém o modelo treinado, você poderá empregar sua própria estratégia de controle de versão. A estratégia pode ser tão simples quanto aplicar um pré/sufixo como uma Convenção de nomenclatura e simplesmente deixar o arquivo iLearner no armazenamento de BLOBs ou copiá-lo/importá-lo em seu sistema de controle de versão.

O arquivo iLearner salvo pode então ser usado para pontuação por meio de serviços Web implantados.

## <a name="versioning-web-service"></a>Controle de versão do serviço Web
Você pode implantar dois tipos de serviços Web de um experimento Azure Machine Learning Studio (clássico). O serviço Web clássico está rigidamente acoplado ao experimento, bem como ao espaço de trabalho. O novo serviço Web usa o Azure Resource Manager Framework e não está mais associado ao experimento original ou ao espaço de trabalho.

### <a name="classic-web-service"></a>Serviço Web clássico
Para obter a versão de um serviço Web clássico, você pode aproveitar a construção do ponto de extremidade do serviço Web. Este é um fluxo típico:

1. Do seu experimento de previsão, você implanta um novo serviço Web clássico, que contém um ponto de extremidade padrão.
2. Você cria um novo ponto de extremidade chamado EP2, que expõe a versão atual do experimento/modelo treinado.
3. Volte e atualize seu experimento de previsão e seu modelo treinado.
4. Reimplante o experimento de previsão, que atualizará o ponto de extremidade padrão. Mas isso não alterará EP2.
5. Você cria um ponto de extremidade adicional chamado EP3, que expõe a nova versão do experimento e do modelo treinado.
6. Volte para a etapa 3, se necessário.

Ao longo do tempo, você pode ter muitos pontos de extremidade criados no mesmo serviço Web. Cada ponto de extremidade representa uma cópia pontual do experimento, que contém a versão pontual do modelo treinado. Você pode usar a lógica externa para determinar qual ponto de extremidade chamar, o que significa efetivamente a seleção de uma versão do modelo treinado para a execução da pontuação.

Você também pode criar vários pontos de extremidade de serviço Web idênticos e, em seguida, corrigir versões diferentes do arquivo iLearner para o ponto de extremidade para obter um efeito semelhante. [Este artigo](create-models-and-endpoints-with-powershell.md) explica em mais detalhes como fazer isso.

### <a name="new-web-service"></a>Novo serviço Web
Se você criar um novo serviço Web baseado em Azure Resource Manager, a construção do ponto de extremidade não estará mais disponível. Em vez disso, você pode gerar arquivos de definição de serviço Web (WSD), no formato JSON, do teste de previsão usando o commandlet do PowerShell [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) ou usando o commandlet do PowerShell [*Export-AzMlWebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) de um serviço Web baseado no Resource Manager implantado.

Depois de ter o arquivo WSD exportado e o controle de versão, você também pode implantar o WSD como um novo serviço Web em um plano de serviço Web diferente em uma região diferente do Azure. Apenas certifique-se de fornecer a configuração de conta de armazenamento apropriada, bem como a nova ID do plano de serviço Web. Para aplicar patch em arquivos iLearner diferentes, você pode modificar o arquivo WSD e atualizar a referência de local do modelo treinado e implantá-lo como um novo serviço Web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizar a execução e a implantação de experimentos
Um aspecto importante do ALM é poder automatizar o processo de execução e implantação do aplicativo. No Azure Machine Learning Studio (clássico), você pode fazer isso usando o [módulo do PowerShell](https://aka.ms/amlps). Aqui está um exemplo de etapas de ponta a ponta que são relevantes para um processo de execução/implantação automatizado de ALM padrão usando o [módulo do PowerShell Azure Machine Learning Studio (clássico)](https://aka.ms/amlps). Cada etapa é vinculada a um ou mais commandlets do PowerShell que você pode usar para realizar essa etapa.

1. [Carregar um conjunto de uma](https://github.com/hning86/azuremlps#upload-amldataset).
2. Copie um teste de treinamento no espaço de trabalho de um [espaço de trabalho](https://github.com/hning86/azuremlps#copy-amlexperiment) ou da [Galeria](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)ou [importe](https://github.com/hning86/azuremlps#import-amlexperimentgraph) um experimento [exportado](https://github.com/hning86/azuremlps#export-amlexperimentgraph) do disco local.
3. [Atualize o conjunto](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) de testes no teste de treinamento.
4. [Execute o teste de treinamento](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Promova o modelo treinado](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Copie um teste de previsão](https://github.com/hning86/azuremlps#copy-amlexperiment) no espaço de trabalho.
7. [Atualize o modelo treinado](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) no teste de previsão.
8. [Execute o experimento de previsão](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Implante um serviço Web](https://github.com/hning86/azuremlps#new-amlwebservice) do experimento de previsão.
10. Teste o ponto de extremidade [RRs](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) ou [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) do serviço Web.

## <a name="next-steps"></a>Passos seguintes
* Baixe o módulo do [PowerShell Azure Machine Learning Studio (clássico)](https://aka.ms/amlps) e comece a automatizar suas tarefas de ALM.
* Saiba como [criar e gerenciar um grande número de modelos de ml usando apenas um único experimento](create-models-and-endpoints-with-powershell.md) por meio do PowerShell e retreinando a API.
* Saiba mais sobre a [implantação de serviços Web do Azure Machine Learning](deploy-a-machine-learning-web-service.md).

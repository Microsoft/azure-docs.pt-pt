---
title: Gestão do ciclo de vida das aplicações
titleSuffix: Azure Machine Learning Studio
description: Aplicar as práticas recomendadas de gerenciamento do ciclo de vida de aplicativos no Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=haining, previous-author=hning86
ms.date: 10/27/2016
ms.openlocfilehash: 1b57fefad726f8fb21f23fa9eef9e71643a3f51b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588403"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Gestão de ciclo de vida de aplicações no Azure Machine Learning Studio
O Azure Machine Learning Studio é uma ferramenta para o desenvolvimento de experiências de machine learning são operacionalizadas na plataforma cloud do Azure. Ele é, como o IDE do Visual Studio e o serviço em nuvem dimensionáveis mesclado numa única plataforma. Pode incorporar práticas de gerenciamento de ciclo de vida de aplicativos (ALM) padrão do controle de versão diversos recursos para execução automática e a implantação, Azure Machine Learning Studio. Este artigo aborda algumas das opções e abordagens.

## <a name="versioning-experiment"></a>Experimentação de controle de versão
Existem duas formas recomendadas para a versão suas experimentações. Pode contar com o histórico de execuções incorporado ou exportar a experimentação no formato JSON de modo a geri-lo externamente. Cada abordagem vem com seus prós e contras.

### <a name="experiment-snapshots-using-run-history"></a>Instantâneos de experimentação com o histórico de execuções
No modelo de execução do Azure Machine Learning Studio experimentação de aprendizado, um imutável instantâneo da experimentação foi submetido para o Programador de tarefas, sempre que clicar **executar** no editor de experimentação. Para ver esta lista de instantâneos, clique em **histórico de execuções** na barra de comandos na vista de editor de experimentações.

![Botão de histórico de execução](./media/version-control/runhistory.png)

Pode, em seguida, abra o instantâneo no modo bloqueado ao clicar no nome da experimentação no momento que a experimentação foi submetida à execução e o instantâneo foi tirada. Tenha em atenção que apenas o primeiro item na lista, que representa a experimentação atual, se encontra num estado editável. Também observe que cada instantâneo pode estar no estado de vários Estados, incluindo concluído (parcial executar), falhou, com falhas (parcial executar), ou de rascunho.

![Lista de histórico de execuções](./media/version-control/runhistorylist.png)

Depois de o abrir, pode guardar a experimentação de instantâneo como uma nova experimentação e, em seguida, modificá-lo. Se o instantâneo de experimentação contém recursos como modelos de formação, transformações ou conjuntos de dados que tenham versões atualizadas, o instantâneo retém as referências para a versão original quando o instantâneo foi tirado. Se salvar o instantâneo bloqueado como uma nova experimentação, o Azure Machine Learning Studio Deteta a existência de uma versão mais recente desses ativos e atualiza automaticamente na nova experimentação.

Se eliminar a experimentação, são eliminados todos os instantâneos desse experimento.

### <a name="exportimport-experiment-in-json-format"></a>Experimentação de exportação/importação no formato JSON
Os instantâneos do histórico de execuções mantenha uma versão imutável da experimentação no Azure Machine Learning Studio sempre que ele é enviado para ser executado. Pode também guardar uma cópia local da experimentação e verificá-lo no seu sistema de controle de origem favorito, como o Team Foundation Server e mais tarde voltar a criar uma experimentação desse ficheiro local. Pode utilizar o [PowerShell do Azure Machine Learning](https://aka.ms/amlps) commandlets [ *Export-AmlExperimentGraph* ](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [  *Import-AmlExperimentGraph* ](https://github.com/hning86/azuremlps#import-amlexperimentgraph) fazer.

O ficheiro JSON é uma representação textual do gráfico de experimentação, que pode incluir uma referência a recursos na área de trabalho como um conjunto de dados ou um modelo preparado. Ele não contém uma versão serializada do ativo. Se tentar importar o documento JSON novamente para a área de trabalho, os ativos referenciados já devem existir com o mesmo elemento IDs que são referenciados na experimentação. Caso contrário, não é possível aceder a experimentação importada.

## <a name="versioning-trained-model"></a>Modelo treinado do controle de versão
Um modelo preparado no Azure Machine Learning Studio é serializado num formato conhecido como um ficheiro iLearner (`.iLearner`) e é armazenado na conta de armazenamento de Blobs do Azure associada à área de trabalho. Uma forma de obter uma cópia do ficheiro iLearner é por meio da API de reparametrização. [Este artigo](retrain-models-programmatically.md) explica como funciona a API de reparametrização. As etapas de alto nível:

1. Configure a sua experimentação de preparação.
2. Adicione uma porta de saída do serviço web para o módulo preparar modelo ou o módulo que produz o modelo preparado, como otimizar o modelo de hiper-parâmetros ou criar modelo de R.
3. Execute a experimentação de preparação e, em seguida, implementá-la como um serviço de web de treinamento de modelo.
4. Chamar o ponto de extremidade BES do serviço da web de treinamento e especifique o nome de ficheiro iLearner desejada e a localização da conta de armazenamento de BLOBs onde serão armazenado.
5. Após a conclusão da chamada BES, coletar o ficheiro iLearner produzidos.

Outra forma de obter o ficheiro iLearner é através do commandlet do PowerShell [ *Download AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Isso pode ser mais fácil se só quer obter uma cópia do ficheiro iLearner sem a necessidade de voltar a preparar programaticamente o modelo.

Depois de ter o ficheiro iLearner que contém o modelo preparado, pode, em seguida, utilizar a sua própria estratégia de controle de versão. A estratégia pode ser tão simples como aplicar um pré/sufixo como uma convenção de nomenclatura e deixando apenas o ficheiro iLearner no armazenamento de BLOBs ou copiar/importá-los para o sistema de controle de versão.

O ficheiro iLearner guardada, em seguida, pode ser utilizado para a classificação por meio de serviços web implementados.

## <a name="versioning-web-service"></a>Serviço web de controle de versão
Pode implantar dois tipos de web, experimente os serviços do Azure Machine Learning Studio. O serviço web clássico está intimamente ligado a experimentação, bem como a área de trabalho. O novo serviço web usa a estrutura do Azure Resource Manager e já não é acoplada com a experimentação original ou a área de trabalho.

### <a name="classic-web-service"></a>Serviço web clássico
A versão de um serviço web clássico, pode aproveitar a construção de ponto final de serviço web. Este é um fluxo típico:

1. Da sua experimentação preditiva, implemente um novo serviço web clássico, que contém um ponto final predefinido.
2. Criar um novo ponto final com o nome ep2, que expõe a versão atual do modelo treinado/experimentação.
3. Volte atrás e atualize a sua experimentação preditiva e o modelo preparado.
4. Implementar novamente a experimentação preditiva, o qual, em seguida, irá atualizar o ponto final predefinido. Mas isso não irá alterar ep2.
5. Criar um ponto de extremidade adicional com o nome ep3, que expõe a nova versão da experimentação e modelo preparado.
6. Volte ao passo 3, se necessário.

Ao longo do tempo, poderá ter muitos pontos de extremidade criados no mesmo serviço da web. Cada ponto de extremidade representa uma cópia de ponto no tempo da experimentação que contém a versão de ponto no tempo do modelo preparado. Em seguida, pode usar a lógica externa para determinar qual ponto de extremidade para chamar, que significa efetivamente selecionando a versão do modelo preparado para a execução de classificação.

Pode também criar muitos pontos de extremidade de serviço de web idênticos e, em seguida, aplicar patches diferentes versões do ficheiro iLearner para o ponto final para obter efeito semelhante. [Este artigo](create-models-and-endpoints-with-powershell.md) explica mais detalhadamente como fazer.

### <a name="new-web-service"></a>Novo serviço web
Se criar um novo serviço web baseado no Azure Resource Manager, a construção de ponto final já não está disponível. Em vez disso, pode gerar ficheiros de definição (WSD) do serviço web, no formato JSON, da sua experimentação preditiva utilizando o [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) commandlet do PowerShell, ou utilizando o [ *Export-AzureRmMlWebservice* ](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice?view=azurermps-6.6.0) commandlet do PowerShell de um serviço web implementado baseados no Resource Manager.

Depois de ter o ficheiro WSD de exportado e a versão controlá-lo, também pode implementar o WSD como um novo serviço web num plano do serviço web diferente numa região diferente do Azure. Apenas Certifique-se de que fornecer a configuração de conta de armazenamento adequados, bem como o novo ID de plano de serviço web Para aplicar o patch em arquivos de iLearner diferentes, pode modificar o ficheiro WSD e atualizar a referência de localização do modelo preparado e implementá-lo como um novo serviço web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizar a execução de experimentação e implementação
Um aspecto importante do ALM é ser capaz de automatizar a execução e o processo de implantação do aplicativo. No Azure Machine Learning Studio, pode fazer isso usando o [módulo do PowerShell](https://aka.ms/amlps). Eis um exemplo dos passos de ponto-a-ponto que são relevantes para um padrão ALM automatizada do processo de implementação/execução utilizando o [módulo do PowerShell do Azure Machine Learning Studio](https://aka.ms/amlps). Cada passo está ligado a um ou mais commandlets do PowerShell que pode utilizar para realizar esse passo.

1. [Carregar um conjunto de dados](https://github.com/hning86/azuremlps#upload-amldataset).
2. Copiar uma experimentação de preparação para a área de trabalho de um [área de trabalho](https://github.com/hning86/azuremlps#copy-amlexperiment) ou a partir de [galeria](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), ou [importar](https://github.com/hning86/azuremlps#import-amlexperimentgraph) um [exportados](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experimentação do local disco.
3. [Atualizar o conjunto de dados](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) na experimentação de preparação.
4. [Execute a experimentação de preparação](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Promover o modelo treinado](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Copiar uma experimentação preditiva](https://github.com/hning86/azuremlps#copy-amlexperiment) para a área de trabalho.
7. [Atualizar o modelo treinado](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) na experimentação preditiva.
8. [Execute a experimentação preditiva](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Implementar um serviço web](https://github.com/hning86/azuremlps#new-amlwebservice) da experimentação preditiva.
10. Testar o serviço web [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) ou [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) ponto final.

## <a name="next-steps"></a>Passos Seguintes
* Transfira o [PowerShell do Azure Machine Learning Studio](https://aka.ms/amlps) módulo e começar a automatizar as tarefas ALM.
* Saiba como [criar e gerir o grande número de modelos de ML utilizando apenas uma única experiência](create-models-and-endpoints-with-powershell.md) através do PowerShell e a API de reparametrização.
* Saiba mais sobre [implementar serviços web Azure Machine Learning](publish-a-machine-learning-web-service.md).

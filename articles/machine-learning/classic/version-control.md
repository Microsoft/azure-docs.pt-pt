---
title: 'Estúdio ML (clássico): Gestão do ciclo de vida da aplicação - Azure'
description: Aplicar aplicações Lifecycle Management boas práticas no Azure Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: f5c9e27e894541d71986fe929cbc5d6fde31bc18
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308804"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Gestão de ciclo de vida de aplicação no Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a. ](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


O Azure Machine Learning Studio (clássico) é uma ferramenta para desenvolver experiências de machine learning que são operacionalizadas na plataforma cloud Azure. É como o Visual Studio IDE e o serviço de nuvem escalável fundido numa única plataforma. Pode incorporar práticas padrão de Gestão de Ciclo de Vida (ALM) desde a versão de vários ativos até à execução e implementação automatizada, no Azure Machine Learning Studio (clássico). Este artigo discute algumas das opções e abordagens.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Experiência de versão
Existem duas formas recomendadas de ver as suas experiências. Pode confiar na história incorporada ou exportar a experiência num formato JSON de modo a geri-la externamente. Cada abordagem vem com os seus prós e contras.

### <a name="experiment-snapshots-using-run-history"></a>Experimente instantâneos usando o Histórico de Execução
No modelo de execução do Azure Machine Learning Studio (clássico) experiência de aprendizagem, uma imagem imutável da experiência é submetida ao agendador de trabalho sempre que clica em **Executar** no editor de experiências. Para ver esta lista de instantâneos, clique em **Executar Histórico** na barra de comando na vista do editor de experiências.

![Executar botão História](./media/version-control/runhistory.png)

Em seguida, pode abrir a imagem no modo Locked clicando no nome da experiência no momento em que a experiência foi submetida a ser executada e a fotografia foi tirada. Note que apenas o primeiro item da lista, que representa a experiência atual, está em estado editável. Note também que cada instantâneo também pode estar em vários estados de Estado, incluindo Terminado (Execução Parcial), Falhado, Falhado (Execução Parcial) ou Rascunho.

![Lista de História de Execução](./media/version-control/runhistorylist.png)

Depois de aberto, pode guardar a experiência instantânea como uma nova experiência e depois modificá-la. Se o seu snapshot de experiência contiver ativos como modelos treinados, transformações ou conjuntos de dados que tenham versões atualizadas, o instantâneo mantém as referências à versão original quando o instantâneo foi tirado. Se guardar o instantâneo bloqueado como uma nova experiência, o Azure Machine Learning Studio (clássico) deteta a existência de uma versão mais recente destes ativos e atualiza-os automaticamente na nova experiência.

Se apagar a experiência, todas as imagens dessa experiência são eliminadas.

### <a name="exportimport-experiment-in-json-format"></a>Experiência de exportação/importação no formato JSON
Os instantâneos de história da execução mantêm uma versão imutável da experiência no Azure Machine Learning Studio (clássico) sempre que é submetida a execução. Também pode guardar uma cópia local da experiência e verificar no seu sistema de controlo de origem favorito, como o Team Foundation Server, e mais tarde recriar uma experiência a partir desse ficheiro local. Pode utilizar os comandos [Azure Machine Learning PowerShell](https://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) para o conseguir.

O ficheiro JSON é uma representação textual do gráfico de experiência, que pode incluir uma referência a ativos no espaço de trabalho, como um conjunto de dados ou um modelo treinado. Não contém uma versão serializada do ativo. Se tentar importar o documento JSON de volta para o espaço de trabalho, os ativos referenciados já devem existir com os mesmos IDs de ativos que são referenciados na experiência. Caso contrário, não poderá aceder à experiência importada.

## <a name="versioning-trained-model"></a>Modelo treinado de versão
Um modelo treinado no Azure Machine Learning Studio (clássico) é serializado num formato conhecido como um ficheiro iLearner ( `.iLearner` ), e está armazenado na conta de armazenamento Azure Blob associada ao espaço de trabalho. Uma forma de obter uma cópia do ficheiro iLearner é através da API de reciclagem. [Este artigo](./retrain-machine-learning-model.md) explica como funciona a API de reconversão. Os passos de alto nível:

1. Prepara a tua experiência de treino.
2. Adicione uma porta de saída de serviço web ao módulo Train Model, ou ao módulo que produz o modelo treinado, como o Hyperparameter Tune Model ou o Create R Model.
3. Executar a sua experiência de treino e, em seguida, implantá-lo como um serviço web de formação modelo.
4. Ligue para o ponto final do bes do serviço web de formação e especifique o nome de ficheiro iLearner pretendido e o local da conta de armazenamento Blob onde será armazenado.
5. Recolha o ficheiro iLearner produzido após o fim da chamada do BES.

Outra forma de recuperar o ficheiro iLearner é através do comando PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Isto pode ser mais fácil se quiser apenas obter uma cópia do ficheiro iLearner sem a necessidade de treinar o modelo programáticamente.

Depois de ter o ficheiro iLearner que contém o modelo treinado, pode então utilizar a sua própria estratégia de versão. A estratégia pode ser tão simples como aplicar um pré/postfix como uma convenção de nomeação e apenas deixar o ficheiro iLearner no armazenamento Blob, ou copiá-lo/importá-lo no seu sistema de controlo de versão.

O ficheiro iLearner guardado pode então ser utilizado para pontuar através de serviços web implantados.

## <a name="versioning-web-service"></a>Serviço web de versão
Você pode implementar dois tipos de serviços web de uma experiência Azure Machine Learning Studio (clássico). O serviço web clássico está muito associado à experiência, bem como ao espaço de trabalho. O novo serviço web utiliza o quadro do Azure Resource Manager, e já não está associado à experiência original ou ao espaço de trabalho.

### <a name="classic-web-service"></a>Serviço web clássico
Para ver um serviço web clássico, você pode aproveitar a construção de ponto final do serviço web. Aqui está um fluxo típico:

1. A partir da sua experiência preditiva, implementa um novo serviço web clássico, que contém um ponto final padrão.
2. Cria-se um novo ponto final chamado ep2, que expõe a versão atual do modelo experimentado/treinado.
3. Volte e atualize a sua experiência preditiva e modelo treinado.
4. Reimplantia a experiência preditiva, que irá então atualizar o ponto final predefinido. Mas isto não vai alterar o EP2.
5. Cria-se um ponto final adicional chamado ep3, que expõe a nova versão da experiência e do modelo treinado.
6. Volte para o passo 3, se necessário.

Com o tempo, poderá ter muitos pontos finais criados no mesmo serviço web. Cada ponto final representa uma cópia pontual da experiência que contém a versão pontual do modelo treinado. Em seguida, pode utilizar a lógica externa para determinar qual o ponto final a chamar, o que significa, efetivamente, selecionar uma versão do modelo treinado para a corrida de pontuação.

Também pode criar muitos pontos finais de serviço web idênticos e, em seguida, corrigir diferentes versões do ficheiro iLearner para o ponto final para obter um efeito semelhante. [Este artigo](create-models-and-endpoints-with-powershell.md) explica com mais pormenor como fazê-lo.

### <a name="new-web-service"></a>Novo serviço web
Se criar um novo serviço web baseado em Azure Resource Manager, a construção de ponto final já não está disponível. Em vez disso, pode gerar ficheiros de definição de serviço web (WSD), no formato JSON, a partir da sua experiência preditiva utilizando o comando [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell, ou utilizando o comando [*PowerShell do Export-AzMlWebservice*](/powershell/module/az.machinelearning/export-azmlwebservice) powerShell a partir de um serviço web baseado em Gestor de Recursos implantado.

Depois de ter o ficheiro ESD exportado e a versão controlá-lo, também pode implementar o WSD como um novo serviço web num plano de serviço web diferente numa região de Azure diferente. Certifique-se apenas de que fornece a configuração adequada da conta de armazenamento, bem como o novo ID do plano de serviço web. Para corrigir diferentes ficheiros iLearner, pode modificar o ficheiro WSD e atualizar a referência de localização do modelo treinado e implantá-lo como um novo serviço web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizar a execução e implantação de experiências
Um aspeto importante da ALM é ser capaz de automatizar o processo de execução e implementação da aplicação. No Azure Machine Learning Studio (clássico), pode fazê-lo utilizando o [módulo PowerShell.](https://aka.ms/amlps) Aqui está um exemplo de passos de ponta a ponta que são relevantes para um processo de execução/implementação automatizado alm padrão, utilizando o [módulo PowerShell (clássico) do Azure Machine Learning Studio (clássico).](https://aka.ms/amlps) Cada passo está ligado a um ou mais comandos PowerShell que podes usar para realizar esse passo.

1. [Faça o upload de um conjunto de dados](https://github.com/hning86/azuremlps#upload-amldataset).
2. Copie uma experiência de formação no espaço de trabalho a partir de um [espaço](https://github.com/hning86/azuremlps#copy-amlexperiment) de trabalho ou da [Galeria,](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)ou [importe](https://github.com/hning86/azuremlps#import-amlexperimentgraph) uma experiência [exportada](https://github.com/hning86/azuremlps#export-amlexperimentgraph) a partir de disco local.
3. [Atualize o conjunto de dados](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) na experiência de treino.
4. [Executar a experiência de treino.](https://github.com/hning86/azuremlps#start-amlexperiment)
5. [Promover o modelo treinado.](https://github.com/hning86/azuremlps#promote-amltrainedmodel)
6. [Copie uma experiência preditiva](https://github.com/hning86/azuremlps#copy-amlexperiment) para o espaço de trabalho.
7. [Atualize o modelo treinado](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) na experiência preditiva.
8. [Executar a experiência preditiva.](https://github.com/hning86/azuremlps#start-amlexperiment)
9. [Implemente um serviço web](https://github.com/hning86/azuremlps#new-amlwebservice) a partir da experiência preditiva.
10. Teste o serviço web [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) ou o ponto final [do BES.](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)

## <a name="next-steps"></a>Passos seguintes
* Descarregue o módulo [Azure Machine Learning Studio (clássico) PowerShell](https://aka.ms/amlps) e comece a automatizar as suas tarefas ALM.
* Aprenda a [criar e gerir um grande número de modelos ML utilizando apenas uma experiência](create-models-and-endpoints-with-powershell.md) através da PowerShell e retreinando a API.
* Saiba mais sobre [a implementação dos serviços web Azure Machine Learning](deploy-a-machine-learning-web-service.md).
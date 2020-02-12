---
title: Gestão do ciclo de vida das aplicações
titleSuffix: ML Studio (classic) - Azure
description: Aplicar aplicações Lifecycle Management boas práticas no Azure Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 10/27/2016
ms.openlocfilehash: 8dd120731ca7eeb98fafe230b79ac50668cdc4e8
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152742"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Gestão de Ciclo de Vida de Aplicação no Estúdio de Aprendizagem automática Azure (clássico)
O Azure Machine Learning Studio (clássico) é uma ferramenta para desenvolver experiências de machine learning que estão operacionalizadas na plataforma cloud Azure. É como o Visual Studio IDE e o serviço de nuvem escalável fundido numa única plataforma. Pode incorporar práticas padrão de Gestão de Ciclo de Vida de Aplicações (ALM) desde a versão de vários ativos até à execução e implementação automatizadas, no Azure Machine Learning Studio (clássico). Este artigo discute algumas das opções e abordagens.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Experiência de versão
Existem duas formas recomendadas de verver as suas experiências. Pode contar com a história de execução incorporada ou exportar a experiência num formato JSON de modo a geri-la externamente. Cada abordagem vem com os seus prós e contras.

### <a name="experiment-snapshots-using-run-history"></a>Fotos de experimentação usando histórico de execução
No modelo de execução da experiência de aprendizagem do Azure Machine Learning Studio (clássico), uma imagem imutável da experiência é submetida ao programador de trabalho sempre que clica em **Run** no editor de experiências. Para ver esta lista de instantâneos, clique em **Executar Histórico** na barra de comando na visão do editor da experiência.

![Botão História de Execução](./media/version-control/runhistory.png)

Em seguida, pode abrir o instantâneo no modo Locked clicando no nome da experiência no momento em que a experiência foi submetida para executar e a foto foi tirada. Note que apenas o primeiro item da lista, que representa a experiência atual, está em estado editável. Note também que cada instantâneo pode estar em vários estados de Status também, incluindo Terminado (execução parcial), Failed, Failed (Corrida Parcial) ou Projeto.

![Lista de Histórico de Execução](./media/version-control/runhistorylist.png)

Depois de abrir, pode guardar a experiência instantânea como uma nova experiência e depois modificá-la. Se o seu instantâneo de experiência sintetiza ativos como modelos treinados, transformações ou conjuntos de dados que tenham versões atualizadas, o instantâneo mantém as referências à versão original quando o instantâneo foi tirado. Se guardar o instantâneo bloqueado como uma nova experiência, o Azure Machine Learning Studio (clássico) deteta a existência de uma versão mais recente destes ativos e atualiza-os automaticamente na nova experiência.

Se apagar a experiência, todas as imagens dessa experiência são eliminadas.

### <a name="exportimport-experiment-in-json-format"></a>Experiência de exportação/importação em formato JSON
Os instantâneos de história de execução mantêm uma versão imutável da experiência no Azure Machine Learning Studio (clássico) sempre que é submetida a execução. Também pode guardar uma cópia local da experiência e registrá-la no seu sistema de controlo de fontes favorito, como o Team Foundation Server, e mais tarde recriar uma experiência a partir desse ficheiro local. Pode utilizar os comandos [Azure Machine Learning PowerShell](https://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) para o conseguir.

O ficheiro JSON é uma representação textual do gráfico de experiência, que pode incluir uma referência a ativos no espaço de trabalho, como um conjunto de dados ou um modelo treinado. Não contém uma versão serializada do ativo. Se tentar importar o documento JSON de volta para o espaço de trabalho, os ativos referenciados já devem existir com as mesmas Identificações de Ativos referenciadas na experiência. Caso contrário, não pode aceder à experiência importada.

## <a name="versioning-trained-model"></a>Modelo treinado para veredição
Um modelo treinado no Azure Machine Learning Studio (clássico) é serializado num formato conhecido como um ficheiro iLearner (`.iLearner`), e está armazenado na conta de armazenamento Azure Blob associada ao espaço de trabalho. Uma maneira de obter uma cópia do ficheiro iLearner é através da API de reconversão. [Este artigo](/azure/machine-learning/studio/retrain-machine-learning-model) explica como funciona a API de reconversão. Os passos de alto nível:

1. Prepara a tua experiência de treino.
2. Adicione uma porta de saída de serviço web ao módulo Train Model, ou o módulo que produz o modelo treinado, como o Tune Model Hyperparameter ou o Create R Model.
3. Execute a sua experiência de treino e, em seguida, implemente-a como um serviço web de treino de modelo.
4. Ligue para o ponto final do BES do serviço web de formação e especifique o nome de ficheiro iLearner desejado e a localização da conta de armazenamento Blob onde será armazenado.
5. Colher o ficheiro iLearner produzido após o acabamento da chamada DO BES.

Outra forma de recuperar o ficheiro iLearner é através do comando PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Isto pode ser mais fácil se quiser apenas obter uma cópia do ficheiro iLearner sem a necessidade de retreinar o modelo programáticamente.

Depois de ter o ficheiro iLearner contendo o modelo treinado, pode então utilizar a sua própria estratégia de versão. A estratégia pode ser tão simples como aplicar um pré/postfix como uma convenção de nomeação e apenas deixar o ficheiro iLearner no armazenamento Blob, ou copiá-lo/importar no seu sistema de controlo de versão.

O ficheiro iLearner guardado pode então ser utilizado para pontuar através de serviços web implantados.

## <a name="versioning-web-service"></a>Serviço web de versão
Você pode implementar dois tipos de serviços web a partir de uma experiência Azure Machine Learning Studio (clássico). O serviço web clássico está fortemente associado à experiência, bem como ao espaço de trabalho. O novo serviço web utiliza o quadro do Gestor de Recursos Azure, e já não está associado à experiência original ou ao espaço de trabalho.

### <a name="classic-web-service"></a>Serviço web clássico
Para ver um serviço web clássico, pode aproveitar a construção do ponto final do serviço web. Aqui está um fluxo típico:

1. A partir da sua experiência preditiva, implementa um novo serviço web clássico, que contém um ponto final predefinido.
2. Cria-se um novo ponto final chamado Ep2, que expõe a versão atual do modelo experimentado/treinado.
3. Volte e atualize a sua experiência preditiva e o modelo treinado.
4. Reimplemente a experiência preditiva, que irá então atualizar o ponto final predefinido. Mas isto não vai alterar o EP2.
5. Cria-se um ponto final adicional chamado Ep3, que expõe a nova versão da experiência e modelo treinado.
6. Volte ao passo 3, se necessário.

Com o tempo, você pode ter muitos pontos finais criados no mesmo serviço web. Cada ponto final representa uma cópia ponto-a-tempo da experiência que contém a versão ponto-a-tempo do modelo treinado. Em seguida, pode utilizar a lógica externa para determinar qual o ponto final a ligar, o que significa efetivamente selecionar uma versão do modelo treinado para a execução de pontuação.

Também pode criar muitos pontos finais de serviço web idênticos e, em seguida, corrigir diferentes versões do ficheiro iLearner para o ponto final para obter um efeito semelhante. [Este artigo](create-models-and-endpoints-with-powershell.md) explica mais detalhadamente como o conseguir.

### <a name="new-web-service"></a>Novo serviço web
Se criar um novo serviço web baseado em Recursos Azure, a construção de ponto final já não está disponível. Em vez disso, pode gerar ficheiros de definição de serviço web (WSD), em formato JSON, a partir da sua experiência preditiva utilizando o comando [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell, ou utilizando o comando PowerShell [*export-AzMlWebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) de um serviço web baseado em Recursos implementados.

Depois de ter o ficheiro WSD exportado e o controlo da versão, também pode implementar o WSD como um novo serviço web num plano de serviço web diferente numa região azure diferente. Apenas certifique-se de fornecer a configuração adequada da conta de armazenamento, bem como o novo ID do plano de serviço web. Para corrigir diferentes ficheiros iLearner, pode modificar o ficheiro WSD e atualizar a referência de localização do modelo treinado e implantá-lo como um novo serviço web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizar execução e implantação de experiências
Um aspeto importante da ALM é poder automatizar o processo de execução e implementação da aplicação. No Azure Machine Learning Studio (clássico), pode fazê-lo utilizando o [módulo PowerShell](https://aka.ms/amlps). Aqui está um exemplo de passos de ponta a ponta que são relevantes para um processo de execução/implementação automatizado ALM padrão utilizando o módulo PowerShell Studio de [Aprendizagem automática (clássico) Do Azure Machine Learning Studio .](https://aka.ms/amlps) Cada passo está ligado a um ou mais comandos PowerShell que pode usar para realizar esse passo.

1. [Faça upload de um conjunto de dados](https://github.com/hning86/azuremlps#upload-amldataset).
2. Copie uma experiência de formação no espaço de trabalho a partir de um espaço de [trabalho](https://github.com/hning86/azuremlps#copy-amlexperiment) ou da [Galeria,](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)ou [importe](https://github.com/hning86/azuremlps#import-amlexperimentgraph) uma experiência [exportada](https://github.com/hning86/azuremlps#export-amlexperimentgraph) a partir de disco local.
3. [Atualize o conjunto de dados](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) na experiência de treino.
4. [Execute a experiência de treino.](https://github.com/hning86/azuremlps#start-amlexperiment)
5. [Promover o modelo treinado.](https://github.com/hning86/azuremlps#promote-amltrainedmodel)
6. [Copie uma experiência preditiva](https://github.com/hning86/azuremlps#copy-amlexperiment) para o espaço de trabalho.
7. [Atualize o modelo treinado](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) na experiência preditiva.
8. [Executar a experiência preditiva.](https://github.com/hning86/azuremlps#start-amlexperiment)
9. [Implementar um serviço web](https://github.com/hning86/azuremlps#new-amlwebservice) a partir da experiência preditiva.
10. Teste o ponto final do serviço web [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) ou [BES.](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)

## <a name="next-steps"></a>Passos seguintes
* Descarregue o módulo [PowerShell Studio (clássico) PowerShell](https://aka.ms/amlps) e comece a automatizar as suas tarefas ALM.
* Aprenda a [criar e gerir um grande número de modelos ML utilizando apenas uma experiência](create-models-and-endpoints-with-powershell.md) através da PowerShell e retreinando a API.
* Saiba mais sobre [a implementação de serviços web Azure Machine Learning.](deploy-a-machine-learning-web-service.md)

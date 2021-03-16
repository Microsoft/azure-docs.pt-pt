---
title: Utilize o AutoML para criar modelos & implementar
titleSuffix: Azure Machine Learning
description: Crie, reveja e implemente modelos automatizados de aprendizagem automática com o estúdio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: aniththa
ms.reviewer: nibaccam
ms.date: 12/20/2020
ms.topic: conceptual
ms.custom: how-to, automl
ms.openlocfilehash: 2e06375441d6540d6630cfe9d4d8c3beec558879
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562727"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Criar, rever e implementar modelos automatizados de aprendizagem automática com Azure Machine Learning


Neste artigo, aprende-se a criar, explorar e implementar modelos automatizados de aprendizagem automática de máquinas sem uma única linha de código no estúdio Azure Machine Learning.

O machine learning automatizado é um processo no qual é selecionado para si o melhor algoritmo de aprendizagem automática para os seus dados específicos. Este processo permite-lhe gerar modelos de aprendizagem automática rapidamente. [Saiba mais sobre aprendizagem automática de máquinas.](concept-automated-ml.md)
 
Para um exemplo final, experimente o [tutorial para criar um modelo de classificação com a interface ML automatizada da Azure Machine Learning.](tutorial-first-experiment-automated-ml.md) 

Para uma experiência baseada em código Python, [configuure as suas experiências automatizadas](how-to-configure-auto-train.md) de aprendizagem automática com o Azure Machine Learning SDK.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Uma área de trabalho do Azure Machine Learning. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md). 

## <a name="get-started"></a>Introdução

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com). 

1. Selecione a sua subscrição e espaço de trabalho. 

1. Navegue para o painel esquerdo. Selecione **ML automatizado** na secção **Autor.**

[![Painel de navegação do estúdio Azure Machine Learning](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Se esta for a sua primeira vez a fazer experiências, verá uma lista vazia e liga-se à documentação. 

Caso contrário, verá uma lista das suas recentes experiências automatizadas de aprendizagem automática de máquinas, incluindo as criadas com o SDK. 

## <a name="create-and-run-experiment"></a>Criar e executar experiência

1. Selecione **+ Nova execução de ML automatizada** e povoe o formulário.

1. Selecione um conjunto de dados do seu recipiente de armazenamento ou crie um novo conjunto de dados. Os conjuntos de dados podem ser criados a partir de ficheiros locais, urls web, datastores ou conjuntos de dados abertos Azure. Saiba mais sobre [a criação do conjunto de dados.](how-to-create-register-datasets.md)  

    >[!Important]
    > Requisitos dos dados de preparação:
    >* Os dados devem estar em forma tabular.
    >* O valor que pretende prever (coluna-alvo) deve estar presente nos dados.

    1. Para criar um novo conjunto de dados a partir de um ficheiro no seu computador local, selecione **+Criar conjunto de dados** e, em seguida, selecione **A partir do ficheiro local**. 

    1. No formulário **de informações Básicas,** forneça ao seu conjunto de dados um nome único e forneça uma descrição opcional. 

    1. Selecione **Seguinte** para abrir o **formulário de seleção de datastore e ficheiros**. Neste formulário seleciona para onde carregar o conjunto de dados; o recipiente de armazenamento predefinido que é automaticamente criado com o seu espaço de trabalho, ou escolha um recipiente de armazenamento que deseja usar para a experiência. 
    
        1. Se os seus dados estiverem por detrás de uma rede virtual, tem de ativar **a função de validação** para garantir que o espaço de trabalho pode aceder aos seus dados. Para obter mais informações, consulte [o estúdio Use Azure Machine Learning numa rede virtual Azure.](how-to-enable-studio-virtual-network.md) 
    
    1. **Selecione Procurar** para carregar o ficheiro de dados para o seu conjunto de dados. 

    1. Reveja as Definições e o formulário **de pré-visualização** para obter uma precisão. O formulário é inteligentemente povoado com base no tipo de ficheiro. 

        Campo| Descrição
        ----|----
        Formato do ficheiro| Define o layout e o tipo de dados armazenados num ficheiro.
        Delimitador| Um ou mais caracteres para especificar a fronteira entre regiões separadas e independentes em texto simples ou outros fluxos de dados.
        Encoding| Identifica o bit da tabela de esquemas de caracteres para usar para ler o seu conjunto de dados.
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.
        Saltar filas | Indica quantas, se houver, são ignoradas no conjunto de dados.
    
        Selecione **Seguinte**.

    1. O **formulário Schema** é inteligentemente povoado com base nas seleções nas Definições e na forma **de pré-visualização.** Aqui configurar o tipo de dados para cada coluna, rever os nomes das colunas e selecionar quais as colunas a **não incluir** para a sua experiência. 
            
        Selecione **Seguinte.**

    1. O formulário **de detalhes confirme** é um resumo das informações anteriormente povoadas nas **informações** básicas e **definições e formulários de pré-visualização.** Também tem a opção de criar um perfil de dados para o seu conjunto de dados utilizando um cálculo habilitado para perfis. Saiba mais sobre [perfis de dados.](how-to-connect-data-ui.md#profile)

        Selecione **Seguinte**.
1. Selecione o conjunto de dados recém-criado assim que aparecer. Também é possível visualizar uma pré-visualização do conjunto de dados e estatísticas da amostra. 

1. No formulário **de execução configurar,** selecione **Criar novo** e introduza **tutorial-automl-deploy** para o nome da experiência.

1. Selecione uma coluna-alvo; esta é a coluna sobre a qual gostaria de fazer previsões.

1. Selecione um compute para o trabalho de perfis de dados e formação. Uma lista dos seus cálculos existentes está disponível no dropdown. Para criar um novo cálculo, siga as instruções no passo 7.

1. Selecione **Crie um novo computamento** para configurar o seu contexto de computação para esta experiência.

    Campo|Descrição
    ---|---
    Nome da computação| Insira um nome único que identifique o seu contexto de computação.
    Prioridade da máquina virtual| As máquinas virtuais de baixa prioridade são mais baratas, mas não garantem os nós de computação. 
    Tipo de máquina virtual| Selecione CPU ou GPU para o tipo de máquina virtual.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para o seu cálculo.
    Nós min / max| Para perfilar os dados, tem de especificar 1 ou mais nós. Introduza o número máximo de nós para o seu cálculo. O padrão é de 6 nós para um AML Compute.
    Definições avançadas | Estas definições permitem configurar uma conta de utilizador e uma rede virtual existente para a sua experiência. 
    
    Selecione **Criar**. A criação de um novo cálculo pode demorar alguns minutos.

    >[!NOTE]
    > O seu nome de cálculo indicará se o cálculo que seleciona/criar está *ativado no perfil*. (Consulte o perfil de [dados](how-to-connect-data-ui.md#profile) da secção para obter mais detalhes).

    Selecione **Seguinte**.

1. No tipo de Tarefa e no formulário **de definições,** selecione o tipo de tarefa: classificação, regressão ou previsão. Consulte [os tipos de tarefas suportados](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) para obter mais informações.

    1. Para **classificação,** também pode permitir uma aprendizagem profunda.
    
        Se a aprendizagem profunda estiver ativada, a validação limita-se a _train_validation divisão_. [Saiba mais sobre opções de validação.](how-to-configure-cross-validation-data-splits.md)


    1. Para **a previsão** que pode, 
    
        1. Permitir uma aprendizagem profunda.
    
        1. Selecione *coluna de tempo*: Esta coluna contém os dados de tempo a utilizar.

        1. Selecione *o horizonte de previsão*: Indique quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) poderão prever para o futuro. Quanto mais o modelo for necessário para prever o futuro, menos preciso se tornará. [Saiba mais sobre previsão e previsão do horizonte.](how-to-auto-train-forecast.md)

1. (Opcional) Ver definições de configuração de adição: definições adicionais que pode utilizar para controlar melhor o trabalho de treino. Caso contrário, os padrão são aplicados com base na seleção de experiências e dados. 

    Configurações adicionais|Descrição
    ------|------
    Métrica primária| Métrica principal usada para marcar o seu modelo. [Saiba mais sobre as métricas dos modelos.](how-to-configure-auto-train.md#primary-metric)
    Explicar o melhor modelo | Selecione para ativar ou desativar, de modo a mostrar explicações para o melhor modelo recomendado. <br> Esta funcionalidade não está atualmente disponível para [certos algoritmos de previsão.](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model) 
    Algoritmo bloqueado| Selecione algoritmos que pretende excluir do trabalho de treino. <br><br> Permitir algoritmos só está disponível para [experiências SDK.](how-to-configure-auto-train.md#supported-models) <br> Consulte os [modelos suportados para cada tipo de tarefa](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels).
    Critério de saída| Quando qualquer um destes critérios é cumprido, o trabalho de formação é interrompido. <br> *Tempo de formação (horas)*: Quanto tempo para permitir que o trabalho de formação corra. <br> *Limiar de pontuação métrica*: Pontuação métrica mínima para todos os oleodutos. Isto garante que se tiver uma métrica de destino definida que deseja alcançar, não gasta mais tempo no trabalho de formação do que o necessário.
    Validação| Selecione uma das opções de validação cruzada para utilizar no trabalho de treino. <br> [Saiba mais sobre validação cruzada.](how-to-configure-cross-validation-data-splits.md#prerequisites)<br> <br>A previsão suporta apenas a validação cruzada k-fold.
    Simultaneidade| *Iterações máximas simultâneas*: Número máximo de condutas (iterações) para testar no trabalho de formação. O trabalho não funcionará mais do que o número especificado de iterações. Saiba mais sobre como a ML automatizada executa [várias corridas de crianças em clusters](how-to-configure-auto-train.md#multiple-child-runs-on-clusters).

1. (Opcional) Ver definições de visualização: se optar por ativar a **caracterização automática** no formulário **configurações de configuração adicionais,** aplicam-se as técnicas de apri metragem de atenção predefinidos. Nas **definições** de visualização do View pode alterar estas padrão e personalizar em conformidade. Saiba como [personalizar as ações.](#customize-featurization) 

    ![A screenshot mostra a caixa de diálogo do tipo de tarefa Select com as definições de visualização chamadas.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>Personalizar a exibição

No formulário **Desativação,** pode ativar/desativar a caracterização automática e personalizar as definições automáticas de exibição para a sua experiência. Para abrir este formulário, consulte o passo 10 na secção [Criar e executar experiências.](#create-and-run-experiment) 

A tabela seguinte resume as personalizações atualmente disponíveis através do estúdio. 

Coluna| Personalização
---|---
Incluídos | Especifica quais as colunas a incluir para o treino.
Tipo de recurso| Altere o tipo de valor para a coluna selecionada.
Impute com| Selecione com que valor imputar valores em falta nos seus dados.

![Azure Machine Learning estúdio personalizado](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Executar experiência e ver resultados

**Selecione Finish** para executar a sua experiência. O processo de preparação da experimentação pode demorar até 10 minutos. As tarefas de preparação podem demorar mais 2 ou 3 minutos para cada pipeline concluir a execução.

> [!NOTE]
> Os algoritmos que a ML automatizada emprega têm aleatoriedade inerente que pode causar uma ligeira variação na pontuação final das métricas de um modelo recomendado, como precisão. A ML automatizada também realiza operações em dados como divisão de ensaios de comboio, divisão de validação de comboios ou validação cruzada quando necessário. Portanto, se executar uma experiência com as mesmas configurações e métrica primária várias vezes, provavelmente verá variação em cada experiência métricas pontuadas devido a estes fatores. 

### <a name="view-experiment-details"></a>Ver detalhes da experimentação

O ecrã **'Detalhes'** abre-se para o separador **Detalhes.** Este ecrã mostra-lhe um resumo da experiência, incluindo uma barra de estado na parte superior ao lado do número de execução. 

O separador **Modelos** contém uma lista dos modelos criados encomendados pela pontuação de métrica. Por predefinição, o modelo com a classificação mais alta com base na métrica escolhida está no topo da lista. À medida que a tarefa de preparação experimenta mais modelos, estes são adicionados à lista. Utilize-a para obter uma comparação rápida das métricas dos modelos produzidos até agora.

![Detalhe de execução](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Ver detalhes da execução de treino

Desa cosar em qualquer um dos modelos concluídos para ver detalhes de treino, como [](how-to-understand-automated-ml.md)um resumo do modelo no separador **Modelo** ou gráficos de métrica de desempenho no separador **Métricas.**

[![Detalhes da iteração](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="model-explanations"></a>Explicações do modelo

Para melhor compreender o seu modelo, veja quais as características de dados (cruas ou concebidas) que influenciaram as previsões do modelo com o painel de explicações do modelo. 

O painel de explicações do modelo fornece uma análise global do modelo treinado juntamente com as suas previsões e explicações. Também permite perfurar um ponto de dados individual e as suas importâncias individuais. [Saiba mais sobre as visualizações do painel de explicações e parcelas específicas.](how-to-machine-learning-interpretability-aml.md#visualizations)

Para obter explicações para um modelo em particular, 

1. No separador **Modelos,** selecione o modelo que pretende utilizar. 
1. Selecione o botão **do modelo Explicar** e forneça um cálculo que possa ser usado para gerar as explicações.
1. Verifique se a **criança executa** o separador para obter o estado. 
1. Uma vez concluído, navegue para o separador **Explicações (pré-visualização)** que contém o painel de explicações. 

    ![Painel de explicação do modelo](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>Implemente o seu modelo

Quando tem o melhor modelo em mãos, é o momento de o implementar como um serviço Web para prever novos dados.

>[!TIP]
> Se procura implementar um modelo que foi gerado através do `automl` pacote com o Python SDK, tem de registar o seu [modelo](how-to-deploy-and-where.md?tabs=python#register-a-model-from-an-azure-ml-training-run-1) no espaço de trabalho. 
>
> Uma vez registado o modelo, encontre-o no estúdio selecionando **Modelos** no painel esquerdo. Assim que abrir o modelo, pode selecionar o botão **Desdobrar** na parte superior do ecrã e, em seguida, seguir as instruções descritas como descrito no **passo 2** da secção **'Implementar'.**

O ML automatizado ajuda-o a implementar o modelo sem escrever código:

1. Tem algumas opções para a implantação. 

    + Opção 1: Implementar o melhor modelo, de acordo com os critérios métricos definidos. 
        1. Depois de concluída a experiência, navegue para a página de execução dos pais selecionando **Run 1** na parte superior do ecrã. 
        1.  Selecione o modelo listado na secção **de resumo do modelo Best.** 
        1. **Selecione Desdobre-se** na parte superior esquerda da janela. 

    + Opção 2: Implementar uma iteração específica deste modelo a partir desta experiência.
        1. Selecione o modelo desejado no separador **Modelos**
        1. **Selecione Desdobre-se** na parte superior esquerda da janela.

1. Povoar o **painel de modelos de implantação.**

    Campo| Valor
    ----|----
    Nome| Insira um nome único para a sua implantação.
    Descrição| Introduza uma descrição para identificar melhor para que é esta implantação.
    Tipo de computação| Selecione o tipo de ponto final que pretende implantar: *Serviço Azure Kubernetes (AKS)* ou *Instância de Contentores Azure (ACI)*.
    Nome da computação| *Aplica-se apenas a AKS:* Selecione o nome do cluster AKS para o quais pretende implementar.
    Ative a autenticação | Selecione para permitir a autenticação baseada em símbolos ou em teclas.
    Utilize ativos de implantação personalizados| Ative esta funcionalidade se quiser carregar o seu próprio script de pontuação e ficheiro ambiente. [Saiba mais sobre os scripts de classificação](how-to-deploy-and-where.md).

    >[!Important]
    > Os nomes dos ficheiros devem ter menos de 32 caracteres e devem começar e terminar com alfanuméricos. Podem incluir traços, carateres de sublinhado, pontos e carateres alfanuméricos. Não são permitidos espaços.

    O menu *Avançado* oferece funcionalidades de implementação predefinidas, como definições de utilização de recursos e [recolha de dados](how-to-enable-app-insights.md). Se quiser substituir estas predefinições, poderá fazê-lo neste menu.

1. Selecione **Implementar**. A conclusão da implementação pode demorar cerca de 20 minutos.
    Assim que a implementação for iniciada, o separador **Resumo do modelo** é apresentado. Veja o progresso da implementação na secção **Estado da implementação**. 

Agora, tem um serviço Web operacional para gerar predições! Pode testar as predições ao consultar o serviço no [Suporte do Azure Machine Learning integrado no Power BI](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context).

## <a name="next-steps"></a>Passos seguintes

* [Saiba como consumir um serviço web.](how-to-consume-web-service.md)
* [Compreenda os resultados automatizados de aprendizagem automática de máquinas.](how-to-understand-automated-ml.md)
* [Saiba mais sobre machine learning automatizado](concept-automated-ml.md) e aprendizagem automática Azure.
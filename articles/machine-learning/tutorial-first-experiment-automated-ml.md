---
title: Criar modelos automatizados de classificação ML
titleSuffix: Azure Machine Learning
description: Aprenda a treinar & implementar modelos de classificação com a interface automatizada de aprendizagem automática de máquinas (ML) da Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: ad8a9f7af9ddabe969d090f80378ba5ff891d7f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691948"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Tutorial: Criar um modelo de classificação com ML automatizado em Aprendizagem automática de máquinas


Neste tutorial, aprende-se a criar um modelo de classificação simples sem escrever uma única linha de código utilizando machine learning automatizado no estúdio Azure Machine Learning. Este modelo de classificação prevê se um cliente subscreverá um depósito a prazo fixo com uma instituição financeira.

Com machine learning automatizado, pode automatizar tarefas intensivas de tempo. A aprendizagem automática de máquinas rapidamente itera sobre muitas combinações de algoritmos e hiperparímetros para ajudá-lo a encontrar o melhor modelo baseado numa métrica de sucesso à sua escolha.

Para um exemplo de previsão de séries de tempo, consulte [Tutorial: Previsão da procura & AutoML](tutorial-automated-ml-forecast.md).

Neste tutorial, aprende-se a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar um espaço de trabalho de aprendizagem automática Azure.
> * Executar uma experimentação de machine learning automatizado.
> * Veja os detalhes da experiência.
> * Implementar o modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Descarregue o ficheiro de dados [**bankmarketing_train.csv.**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) A coluna **y** indica se um cliente subscreveu um depósito a prazo fixo, que é mais tarde identificado como a coluna-alvo para previsões neste tutorial. 

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implementar modelos de machine learning. Liga a sua subscrição e grupo de recursos Azure a um objeto facilmente consumido no serviço. 

Existem muitas [formas de criar um espaço de trabalho.](how-to-manage-workspace.md) Neste tutorial, cria-se um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Tome nota do seu espaço de **trabalho** e **subscrição.** Vai precisar disto para garantir que cria a sua experiência no lugar certo. 

## <a name="get-started-in-azure-machine-learning-studio"></a>Começar no estúdio Azure Machine Learning

Você completa os seguintes passos de configuração de experiências através do estúdio Azure Machine Learning em https://ml.azure.com , uma interface web consolidada que inclui ferramentas de machine learning para executar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade. O estúdio não é suportado nos navegadores do Internet Explorer.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. **Selecione Começar**.

1. No painel esquerdo, selecione **ML automatizado** na secção **Autor.**

   Uma vez que esta é a sua primeira experiência automatizada de ML, você verá uma lista vazia e ligações à documentação.

   ![Página Introdução](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Selecione **+Nova execução ML automatizada**. 

## <a name="create-and-load-dataset"></a>Criar e carregar conjunto de dados

Antes de configurar a sua experiência, faça o upload do seu ficheiro de dados para o seu espaço de trabalho sob a forma de um conjunto de dados Azure Machine Learning. Ao fazê-lo, permite-lhe garantir que os seus dados são formatados adequadamente para a sua experiência.

1. Crie um novo conjunto de dados selecionando **ficheiros locais** a partir do **drop-down +Create dataset.** 

    1. No formulário **de informações Básicas,** forneça um nome ao seu conjunto de dados e forneça uma descrição opcional. A interface ML automatizada suporta atualmente apenas OsDatasets Tabular, pelo que o tipo de conjunto de dados deve predefinir-se ao *Tabular*.

    1. **Selecione** Em seguida, no canto inferior esquerdo

    1. No formulário **de datastore e seleção de ficheiros,** selecione a loja de dados predefinida que foi configurada automaticamente durante a criação do seu espaço de **trabalho, workspaceblobstore (Azure Blob Storage)**. É aqui que irá fazer o upload do seu ficheiro de dados para o disponibilizar ao seu espaço de trabalho.

    1. Selecione **Procurar**.
    
    1. Escolha o ficheiro **bankmarketing_train.csv** no seu computador local. Este é o ficheiro que descarregou como [pré-requisito.](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)

    1. Dê ao seu conjunto de dados um nome único e forneça uma descrição opcional. 

    1. Selecione **Seguinte** no canto inferior esquerdo, para carregá-lo para o recipiente predefinido que foi configurado automaticamente durante a criação do seu espaço de trabalho.  
    
       Quando o upload estiver concluído, o formulário de definições e pré-visualização é pré-povoado com base no tipo de ficheiro. 
       
    1. Verifique se as Definições e o formulário **de pré-visualização são** preenchidos da seguinte forma e selecione **Next**.
        
        Campo|Descrição| Valor para tutorial
        ---|---|---
        Formato do ficheiro|Define o layout e o tipo de dados armazenados num ficheiro.| Delimitado
        Delimitador|Um ou mais caracteres para especificar a fronteira entre &nbsp; regiões separadas e independentes em texto simples ou outros fluxos de dados. |Ponto
        Encoding|Identifica o bit da tabela de esquemas de caracteres para usar para ler o seu conjunto de dados.| UTF-8
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.| Todos os ficheiros têm os mesmos cabeçalhos
        Saltar filas | Indica quantas, se houver, são ignoradas no conjunto de dados.| Nenhum

    1. O **formulário Schema** permite uma configuração adicional dos seus dados para esta experiência. Para este exemplo, selecione o interruptor de alternação para o **day_of_week**, de modo a não incluí-lo. Selecione **Seguinte**.
         ![Formulário de esquema](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)
    1. No formulário **de detalhes confirme,** verifique as informações correspondem ao que foi previamente preenchido na **informação Básica, datastore e seleção de ficheiros** e **configurações e formulários de pré-visualização.**
    
    1. Selecione **Criar** para completar a criação do seu conjunto de dados.
    
    1. Selecione o seu conjunto de dados assim que aparecer na lista.
    
    1. Reveja a **pré-visualização de Dados**  para garantir que não incluiu **day_of_week** então, selecione **Close**.

    1. Selecione  **Seguinte**.

## <a name="configure-run"></a>Configure corrida

Depois de carregar e configurar os seus dados, pode configurar a sua experiência. Esta configuração inclui tarefas de design de experiências como, selecionar o tamanho do seu ambiente de computação e especificar qual a coluna que pretende prever. 

1. Selecione o novo botão de rádio **Criar.**

1. Povoar o formulário **Configure Run** da seguinte forma:
    1. Insira este nome de experiência: `my-1st-automl-experiment`

    1. Selecione **y** como a coluna-alvo, o que quer prever. Esta coluna indica se o cliente subscreveu ou não um depósito a prazo.
    
    1. Selecione **+Crie um novo cálculo** e configuure o seu alvo de computação. Um alvo de cálculo é um ambiente de recursos local ou baseado na nuvem usado para executar o seu script de treino ou hospedar a sua implementação de serviço. Para esta experiência, usamos uma computação baseada em nuvem. 
        1. Preencha o formulário **Máquina Virtual** para configurar o seu cálculo.

            Campo | Descrição | Valor para tutorial
            ----|---|---
            Prioridade da máquina virtual &nbsp; &nbsp; |Selecione qual a prioridade que a sua experiência deve ter| Dedicada
            Tipo de máquina virtual &nbsp; &nbsp;| Selecione o tipo de máquina virtual para o seu cálculo.|CPU (Unidade Central de Processamento)
            Tamanho da máquina virtual &nbsp; &nbsp;| Selecione o tamanho da máquina virtual para o seu cálculo. Uma lista de tamanhos recomendados é fornecida com base nos seus dados e tipo de experiência. |Standard_DS12_V2
        
        1. Selecione **Seguinte** para preencher o **formulário configurações configurações de configuração**.
        
            Campo | Descrição | Valor para tutorial
            ----|---|---
            Nome da computação |  Um nome único que identifica o seu contexto computacional. | automl-compute
            Nós min / max| Para perfilar os dados, tem de especificar 1 ou mais nós.|Min nosdes: 1<br>Nó máximo: 6
            Ocioso segundos antes de baixar a escala | Tempo de marcha lenta antes do cluster ser automaticamente reduzido à contagem mínima de nós.|120 (padrão)
            Definições avançadas | Configurações para configurar e autorizar uma rede virtual para a sua experiência.| Nenhum               

        1. Selecione **Criar** para criar o seu alvo de computação. 

            **Isto leva alguns minutos para ser concluído.** 

             ![Página de definições](./media/tutorial-first-experiment-automated-ml/compute-settings.png)

        1. Após a criação, selecione o seu novo alvo de computação a partir da lista de drop-down.

    1. Selecione **Seguinte**.

1. No **formulário 'Tipo de Tarefa' e definições,** preencha a configuração para a sua experiência automatizada de ML especificando as definições de tipo de tarefa e configuração de aprendizagem automática.
    
    1.  Selecione **a Classificação** como tipo de tarefa de aprendizagem automática.

    1. Selecione **Ver as definições de configuração adicionais** e povoar os campos da seguinte forma. Estas configurações são para controlar melhor o trabalho de treino. Caso contrário, os padrão são aplicados com base na seleção de experiências e dados.

        &nbsp;Configurações adicionais|Descrição|Valor &nbsp; para &nbsp; tutorial
        ------|---------|---
        Métrica primária| Métrica de avaliação pela que o algoritmo de aprendizagem automática será medido.|AUC_weighted
        Explicar o melhor modelo| Mostra automaticamente a explicabilidade do melhor modelo criado pela ML automatizada.| Ativar
        Algoritmos bloqueados | Algoritmos que pretende excluir do trabalho de formação| Nenhum
        Critério de saída| Se um critério for cumprido, o trabalho de formação é interrompido. |Tempo &nbsp; de trabalho de &nbsp; formação (horas): 1 <br> Limiar de &nbsp; pontuação &nbsp; métrica: Nenhum
        Validação | Escolha um tipo de validação cruzada e número de testes.|Tipo de validação:<br>&nbsp;k-dobrar &nbsp; validação cruzada <br> <br> Número de validações: 2
        Simultaneidade| O número máximo de iterações paralelas executadas por iteração| &nbsp;Iterações &nbsp; máximas simultâneas: 5
        
        Selecione **Guardar**.
    
1. **Selecione Acabamento** para executar a experiência. O ecrã **'Run Detail'**  abre-se com o **estado de Execução** no topo à medida que a preparação da experiência começa. Este estado atualiza à medida que a experiência progride. As notificações também aparecem no canto superior direito do estúdio, para informá-lo sobre o estado da sua experiência.

>[!IMPORTANT]
> A preparação leva **10 a 15 minutos** para preparar a experiência.
> Uma vez em funcionamento, demora **mais 2 a 3 minutos para cada iteração**.  <br> <br>
> Na produção, provavelmente afastar-se-ia um pouco. Mas para este tutorial, sugerimos que comece a explorar os algoritmos testados no separador **Modelos** à medida que eles completam enquanto os outros ainda estão em execução. 

##  <a name="explore-models"></a>Explore modelos

Navegue no separador **Modelos** para ver os algoritmos (modelos) testados. Por predefinição, os modelos são encomendados por pontuação métrica à medida que completam. Para este tutorial, o modelo que obtém a maior pontuação com base na métrica **AUC_weighted** escolhida está no topo da lista.

Enquanto espera que todos os modelos de experiência terminem, selecione o **nome Algoritmo** de um modelo completo para explorar os seus detalhes de desempenho. 

Os seguintes navegam através dos separadores **Detalhes** e **Métricas** para ver as propriedades, métricas e gráficos de desempenho do modelo selecionado. 

![Executar detalhes de iteração](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="model-explanations"></a>Explicações do modelo

Enquanto espera que os modelos sejam concluídos, também pode olhar para as explicações do modelo e ver quais as características de dados (cruas ou concebidas) que influenciaram as previsões de um determinado modelo. 

Estas explicações do modelo podem ser geradas a pedido, e são resumidas no painel de explicações do modelo que faz parte do separador **Explicações (pré-visualização).**

Para gerar explicações de modelos, 
 
1. Selecione **Executar 1** na parte superior para navegar de volta para o ecrã **dos Modelos.** 
1. Selecione o **separador Modelos.**
1. Para este tutorial, selecione o primeiro modelo **MaxAbsScaler, LightGBM.**
1. Selecione o botão **do modelo Explicar** na parte superior. À direita, aparece o painel **de modelos Explain.** 
1. Selecione o **cálculo deml automático** que criou anteriormente. Este cluster computacional inicia uma corrida infantil para gerar as explicações do modelo.
1. **Selecione Criar** na parte inferior. Uma mensagem de sucesso verde aparece no topo do seu ecrã. 
    >[!NOTE]
    > A execução de explicabilidade leva cerca de 2-5 minutos para ser concluída.
1. Selecione o botão **Explicações (pré-visualização).** Este separador povoa uma vez que o funcionamento da explicação esteja concluído.
1. Do lado esquerdo, expanda o painel e selecione a linha que diz **cru** em **Funcionalidades**. 
1. Selecione o **separador de importância de característica agregada** à direita. Este gráfico mostra quais as características dos dados que influenciaram as previsões do modelo selecionado. 

    Neste exemplo, a *duração* parece ter mais influência nas previsões deste modelo.
    
    ![Painel de explicação do modelo](media/tutorial-first-experiment-automated-ml/model-explanation-dashboard.png)

## <a name="deploy-the-best-model"></a>Implementar o melhor modelo

A interface automatizada de machine learning permite-lhe implementar o melhor modelo como serviço web em alguns passos. A implementação é a integração do modelo para que possa prever novos dados e identificar potenciais áreas de oportunidade. 

Para esta experiência, a implantação num serviço web significa que a instituição financeira dispõe agora de uma solução web iterativa e escalável para identificar potenciais clientes de depósitos a prazo fixo. 

Verifique se a sua experiência está completa. Para tal, volte a navegar para a página de execução dos pais selecionando **Run 1** na parte superior do ecrã. Um **estado completo** é mostrado na parte superior esquerda do ecrã. 

Uma vez concluída a experiência, a página **Detalhes** é preenchida com uma secção **de resumo do melhor modelo.** Neste contexto de experiência, **o VoteingEnsemble** é considerado o melhor modelo, com base na **métrica AUC_weighted.**  

Implementamos este modelo, mas seja avisado, a implantação leva cerca de 20 minutos para ser concluída. O processo de implementação implica várias etapas, incluindo registar o modelo, gerar recursos e configurá-los para o serviço web.

1. Selecione **VotingEnsemble** para abrir a página específica do modelo.

1. Selecione o botão **'Implementar'** no topo esquerdo.

1. Povoar o **painel de modelos** da seguinte forma:

    Campo| Valor
    ----|----
    Nome de implantação| my-automl-implementar
    Descrição da implantação| A minha primeira implementação automatizada de experiências de aprendizagem automática
    Tipo de computação | Selecione Azure Compute Instance (ACI)
    Ative a autenticação| Desativar. 
    Use implementações personalizadas| Desativar. Permite que o ficheiro do controlador predefinido (script de pontuação) e o ficheiro ambiente sejam gerados automaticamente. 
    
    Para este exemplo, utilizamos os predefinidos fornecidos no menu *Advanced.* 

1. Selecione **Implementar**.  

    Uma mensagem de sucesso verde aparece na parte superior do ecrã **Run** e no painel de resumo do **Modelo,** uma mensagem de estado aparece sob **o estado de Implementação**. Selecione **Refresh** periodicamente para verificar o estado de implantação.
    
Agora tem um serviço web operacional para gerar previsões. 

Dirija-se aos [**Próximos Passos**](#next-steps) para saber mais sobre como consumir o seu novo serviço web e teste as suas previsões utilizando o suporte de Power BI construído no suporte Azure Machine Learning.

## <a name="clean-up-resources"></a>Limpar os recursos

Os ficheiros de implantação são maiores do que os ficheiros de dados e experiências, pelo que custam mais para armazenar. Elimine apenas os ficheiros de implementação para minimizar os custos da sua conta, ou se pretender manter o seu espaço de trabalho e experimentar ficheiros. Caso contrário, elimine todo o grupo de recursos, se não pretender utilizar nenhum dos ficheiros.  

### <a name="delete-the-deployment-instance"></a>Eliminar a instância de implantação

Elimine apenas a instância de implantação do Azure Machine Learning em https: \/ /ml.azure.com/, se quiser manter o grupo de recursos e espaço de trabalho para outros tutoriais e exploração. 

1. Ir ao [Azure Machine Learning.](https://ml.azure.com/) Navegue para o seu espaço de trabalho e à esquerda sob o painel **De ativos,** selecione **Endpoints**. 

1. Selecione a implementação deseja eliminar e selecione **Eliminar**. 

1. **Selecione Proceder**.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial automatizado de machine learning, usou a interface ML automatizada da Azure Machine Learning para criar e implementar um modelo de classificação. Consulte estes artigos para obter mais informações e próximos passos:

> [!div class="nextstepaction"]
> [Consumir serviços Web](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ Saiba mais sobre [aprendizagem automática de máquinas.](concept-automated-ml.md)
+ Para obter mais informações sobre métricas e gráficos de classificação, consulte o artigo [de resultados automatizados de machine learning da Máquina.](how-to-understand-automated-ml.md)
+ Saiba mais sobre [a exibição.](how-to-configure-auto-features.md#featurization)
+ Saiba mais sobre [perfis de dados.](how-to-connect-data-ui.md#profile)


>[!NOTE]
> Este conjunto de dados de Marketing Bancário é disponibilizado ao abrigo da [Licença Creative Commons (CCO: Domínio Público).](https://creativecommons.org/publicdomain/zero/1.0/) Quaisquer direitos em conteúdos individuais da base de dados são licenciados ao abrigo da [Licença de Conteúdos da Base de Dados](https://creativecommons.org/publicdomain/zero/1.0/) e disponíveis no [Kaggle.](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) Este conjunto de dados estava originalmente disponível na [Base de Dados de Aprendizagem automática](https://archive.ics.uci.edu/ml/datasets/bank+marketing)da UCI.<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez e P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, junho de 2014.

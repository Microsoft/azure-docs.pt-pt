---
title: Implementar um serviço web do Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Como converter uma experimentação de preparação para uma experimentação preditiva, prepará-lo para a implementação, em seguida, implementá-lo como um serviço web do Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: b77c21b75147a9bb5203effb20379dc208122d05
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819152"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service"></a>Implementar um serviço web do Azure Machine Learning Studio

O Azure Machine Learning Studio permite-lhe criar e testar uma solução de Análise Preditiva. Em seguida, pode implementar a solução como um serviço web.

Os serviços web do Machine Learning Studio fornecem uma interface entre uma aplicação e um modelo de classificação de fluxo de trabalho de Machine Learning Studio. Uma aplicação externa comunica com um modelo de classificação de fluxo de trabalho de Machine Learning Studio em tempo real. Uma chamada para um serviço web do Machine Learning Studio devolve resultados de predição para uma aplicação externa. Para fazer uma chamada para um serviço Web, tem de transmitir uma chave de API que foi criada quando implementou esse serviço. Um serviço web do Machine Learning Studio baseia-se em REST, uma opção de arquitetura popular para projetos de programação web.

O Azure Machine Learning Studio tem dois tipos de serviços da web:

* Serviço de solicitação-resposta (RRS): Uma baixa latência, serviço altamente escalável que pontua um registo de dados individual.
* Serviço de execução de lote (BES): Um serviço assíncrono que pontua um lote de registos de dados.

A entrada para BES é como a entrada de dados que o RRS utiliza. A principal diferença é que BES lê um bloco de registos a partir de várias origens, como o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure, a Base de Dados SQL do Azure, o HDInsight (consulta do Hive) e origens HTTP.

A partir de uma ponto de-de-visão de alto nível, implementar o seu modelo em três passos:

* **[Criar uma experimentação de preparação]**  -no Studio, pode treinar e testar um modelo de Análise Preditiva com dados de treinamento que fornecer, utilizando um grande conjunto de algoritmos de aprendizagem automática incorporado.
* **[Convertê-lo para uma experimentação preditiva]**  -assim que o seu modelo foi treinado com os dados existentes e está pronto para usá-lo para pontuar novos dados, preparar e simplifique a sua experimentação para previsões.
* **Implementar** -o como um **[novo serviço web]** ou um **[serviço web clássico]** - quando implementa sua experimentação preditiva como um Serviço web do Azure, os utilizadores podem enviar dados para o seu modelo e receber previsões de seu modelo.

## <a name="create-a-training-experiment"></a>Criar uma experimentação de preparação

Para preparar um modelo de Análise Preditiva, use Azure Machine Learning Studio para criar uma experimentação de preparação onde inclui vários módulos para carregar dados de treinamento, preparar os dados conforme necessário, aplicar algoritmos de machine learning e avaliar os resultados. Pode repetir uma experimentação e tentar algoritmos de aprendizagem de máquina diferentes para comparar e avaliar os resultados.

O processo de criação e gestão de experimentações de treinamento é abordado mais a fundo em outro lugar. Para obter mais informações, veja estes artigos:

* [Criar uma experimentação simple no Azure Machine Learning Studio](create-experiment.md)
* [Desenvolver uma solução Preditiva com o Azure Machine Learning Studio](tutorial-part1-credit-risk.md)
* [Importar os dados de treinamento para o Azure Machine Learning Studio](import-data.md)
* [Gerir iterações de experimentação no Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experimentação de preparação para uma experimentação preditiva

Assim que Treinou seu modelo, está pronto para converter a sua experimentação de preparação para uma experimentação preditiva para classificar dados novos.

Ao converter para uma experimentação preditiva, esteja tirando o modelo preparado pronta para ser implementada como um serviço web de pontuação. Os utilizadores do web service podem enviar dados de entrada ao seu modelo e seu modelo retornará os resultados da predição. Como converter uma experimentação preditiva, tenha em mente como espera que seu modelo a ser utilizado por outras pessoas.

Para converter a sua experimentação de preparação para uma experimentação preditiva, clique em **execute** na parte inferior da tela de experimentação, clique em **no serviço Web**, em seguida, selecione **serviço da Web preditiva**.

![Converter para classificação de experimentação](./media/publish-a-machine-learning-web-service/figure-1.png)

Para obter mais informações sobre como realizar essa conversão, consulte [como preparar o seu modelo para a implementação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Os passos seguintes descrevem a implementar uma experimentação preditiva como um novo serviço web. Também pode implementar a experimentação como serviço web clássico.

## <a name="deploy-it-as-a-new-web-service"></a>Implementá-lo como um serviço web novo

Agora que a experimentação preditiva tenha sido preparada, pode implementá-lo como um novo serviço web do Azure (baseados no Resource Manager). Usando o web service, os utilizadores podem enviar dados para o seu modelo e o modelo retornará seu previsões.

Para implementar a sua experimentação preditiva, clique em **executar** na parte inferior da tela de experimentação. Quando tiver terminado de executar a experimentação, clique em **implementar serviço Web** e selecione **implementar serviço Web [New]**.  É aberta a página de implementação do portal do serviço Web do Machine Learning Studio.

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição para a qual estiver a implementar o serviço web. Para obter mais informações, consulte [gerir um serviço Web através do portal do Azure Machine Learning Web Services](manage-new-webservice.md). 

### <a name="machine-learning-studio-web-service-portal-deploy-experiment-page"></a>Portal de serviço do Learning Studio Web Deploy página de experimentação do Machine

Na página de experimentação de implementar, introduza um nome para o serviço web.
Selecione um plano de preços. Se tiver um plano de preços existente, que pode selecioná-lo, caso contrário, tem de criar um novo plano de preços para o serviço.

1. Na **plano de preços** menu pendente, selecione um plano existente ou selecione o **selecione novo plano** opção.
2. Na **nome do plano**, escreva um nome que identifique o plano na sua fatura.
3. Selecione uma da **camadas de plano mensal**. A predefinição de escalões do plano para os planos para a sua região predefinida e o seu serviço web é implementada nessa região.

Clique em **Deploy** e o **guia de introdução** é aberta a página do web Service.

A página de início rápido de serviço da web fornece-lhe acesso e orientações sobre as tarefas mais comuns, que realizará depois de criar um serviço web. A partir daqui, pode facilmente acessar a página de teste e a página consumo.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testar o seu novo serviço web

Para testar o seu novo serviço web, clique em **testar o serviço web** em tarefas comuns. Na página de teste, pode testar o serviço da web como um serviço de solicitação-resposta (RRS) ou um serviço de execução de lotes (BES).

A página de teste RRS apresenta as entradas, saídas e quaisquer parâmetros de globais que definiu para a experimentação. Para testar o serviço web, pode introduzir os valores adequados para as entradas ou fornecer um ficheiro formatado de separados por vírgulas (CSV) de valor que contém os valores de teste manualmente.

Para testar com RRS, do modo de exibição de lista, introduza os valores adequados para as entradas e clique em **solicitação-resposta testar**. Apresentam os resultados de predição na coluna de saída à esquerda.

![Implementar o serviço web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para testar sua BES, clique em **Batch**. Na página de teste do Batch, clique em Procurar em sua entrada e selecione um ficheiro CSV que contém os valores de exemplo apropriado. Se não tiver um ficheiro CSV e criou sua experimentação Preditiva com o Machine Learning Studio, pode transferir o conjunto de dados para a experimentação preditiva e utilizá-lo.

Para transferir o conjunto de dados, abra o Machine Learning Studio. Abra a sua experimentação preditiva e clique com o botão direito do rato na entrada para a experimentação. No menu de contexto, selecione **conjunto de dados** e, em seguida, selecione **transferir**.

![Implementar o serviço web](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Clique em **teste**. O estado da sua tarefa de execução de lotes é apresentado à direita em **tarefas de lote de teste**.

![Implementar o serviço web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Sobre o **configuração** página, pode alterar a descrição, title, atualizar a chave de conta de armazenamento e ativar os dados de exemplo para o seu serviço web.

![Configurar o serviço web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Aceda ao seu serviço web novo

Depois de implementar o serviço web do Machine Learning Studio, pode enviar dados para o serviço e receber respostas por meio de programação.

O **Consume** página fornece todas as informações necessárias acessar o serviço web. Por exemplo, a chave de API é fornecida para permitir o acesso autorizado ao serviço.

Para obter mais informações sobre como acessar um serviço web do Machine Learning Studio, consulte [como consumir um serviço Web do Azure Machine Learning Studio](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gerir o seu novo serviço web

Pode gerenciar o novo portal de serviços da Web do Machine Learning Studio de serviços web. Partir do [página principal do portal](https://services.azureml-test.net/), clique em **serviços da Web**. Da página de serviços da web, pode eliminar ou copiar um serviço. Para monitorizar um serviço específico, clique no serviço e, em seguida, clique em **Dashboard**. Para monitorizar trabalhos de lote associados ao serviço web, clique em **registo de pedido de lote**.

### <a id="multi-region"></a> Implementar o seu novo serviço web em várias regiões

Pode implantar facilmente um novo serviço web em várias regiões sem a necessidade de várias subscrições ou áreas de trabalho.

Os preços são a região específico, por isso terá de definir um plano de faturação para cada região na qual irá implementar o serviço web.

#### <a name="create-a-plan-in-another-region"></a>Criar um plano noutra região

1. Inicie sessão no [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).
2. Clique nas **planos** opção de menu.
3. Nos planos pela página de exibição de mensagens em fila, clique em **New**.
4. Partir do **subscrição** lista pendente, selecione a subscrição na qual o novo plano irá residir.
5. Partir do **região** lista pendente, selecione uma região para o novo plano. As opções de plano de mensagens em fila para a região selecionada serão apresentados no **opções de plano** secção da página.
6. Partir do **grupo de recursos** lista pendente, selecione um recurso de grupo para o plano. A partir de mais informações sobre grupos de recursos, veja [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. Na **nome do plano** escreva o nome do plano.
8. Sob **as opções do plano**, clique em nível de faturação para o novo plano.
9. Clique em **Criar**.

#### <a name="deploy-the-web-service-to-another-region"></a>Implementar o serviço web para outra região

1. Na página Serviços de Web do Microsoft Azure Machine Learning, clique a **serviços da Web** opção de menu.
2. Selecione o serviço Web que está a implementar uma nova região.
3. Clique em **cópia**.
4. Na **nome do serviço Web**, escreva um novo nome para o serviço web.
5. Na **descrição do serviço da Web**, escreva uma descrição para o serviço web.
6. Do **subscrição** lista pendente, selecione a subscrição na qual o novo serviço web irá residir.
7. Partir do **grupo de recursos** lista pendente, selecione um recurso de grupo para o serviço web. A partir de mais informações sobre grupos de recursos, veja [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Partir do **região** lista pendente, selecione a região na qual implementar o serviço web.
9. Partir do **conta de armazenamento** lista pendente, selecione um armazenamento de conta na qual armazenar o serviço web.
10. Partir do **plano de preços** lista pendente, selecione um plano na região que selecionou no passo 8.
11. Clique em **cópia**.

## <a name="deploy-it-as-a-classic-web-service"></a>Implementá-lo como um serviço web clássico

Agora que a experimentação preditiva foi suficientemente preparada, pode implementá-lo como um serviço web do Azure clássico. Usando o web service, os utilizadores podem enviar dados para o seu modelo e o modelo retornará seu previsões.

Para implementar a sua experimentação preditiva, clique em **execute** na parte inferior da experimentação baseadas em telas e, em seguida, clique em **implementar serviço Web**. O serviço web está configurado e é colocadas no dashboard do serviço web.

![Implementar o serviço web](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testar o serviço da web clássico

Pode testar o serviço web no portal de serviços da Web do Machine Learning Studio ou no Machine Learning Studio.

Para testar o serviço da web de resposta a um pedido, clique nas **testar** botão no dashboard do serviço web. Exibe uma caixa de diálogo a pedir-lhe para os dados de entrada para o serviço. Estas são as colunas esperadas pela experimentação de classificação. Introduza um conjunto de dados e, em seguida, clique em **OK**. Os resultados gerados pelo serviço da web são apresentados na parte inferior do dashboard.

Pode clicar a **testar** ligação de pré-visualização para testar seu serviço no portal do serviços de Web do Azure Machine Learning Studio, conforme mostrado anteriormente na secção de serviço web novo.

Para testar o serviço de execução do Batch, clique em **testar** link de pré-visualização. Na página de teste do Batch, clique em Procurar em sua entrada e selecione um ficheiro CSV que contém os valores de exemplo apropriado. Se não tiver um ficheiro CSV e criou sua experimentação Preditiva com o Machine Learning Studio, pode transferir o conjunto de dados para a experimentação preditiva e utilizá-lo.

![Testar o serviço web](./media/publish-a-machine-learning-web-service/figure-3.png)

Sobre o **configuração** página, pode alterar o nome a apresentar do serviço e fornecer uma descrição. O nome e descrição é apresentada no [portal do Azure](https://portal.azure.com/) onde pode gerir os seus serviços web.

Pode fornecer uma descrição para os dados de entrada, dados de saída e web parâmetros de serviço ao introduzir uma cadeia de caracteres para cada coluna em **esquema de entrada**, **esquema de saída**, e **serviço Web PARÂMETRO**. Estas descrições são usadas na documentação do código de exemplo fornecida para o serviço web.

Pode ativar o registo diagnosticar quaisquer falhas que está vendo quando seu serviço web é acessado. Para obter mais informações, consulte [ativar o registo de serviços web Machine Learning Studio](web-services-logging.md).

![Configurar o serviço web](./media/publish-a-machine-learning-web-service/figure-4.png)

Também pode configurar os pontos finais para o serviço web no portal do Azure Machine Learning Web Services semelhante ao procedimento mostrado anteriormente na secção de serviço web novo. As opções são diferentes, pode adicionar ou alterar a descrição do serviço, ative o registo e ativar dados de exemplo para fins de teste.

### <a name="access-your-classic-web-service"></a>Aceda ao seu serviço de web clássico

Depois de implementar o serviço web do Machine Learning Studio, pode enviar dados para o serviço e receber respostas por meio de programação.

O dashboard fornece todas as informações necessárias acessar o serviço web. Por exemplo, a chave de API é fornecida para permitir o acesso autorizado ao serviço e páginas de ajuda da API são fornecidas para o ajudar a começar a escrever seu código.

Para obter mais informações sobre como acessar um serviço web do Machine Learning Studio, consulte [como consumir um serviço Web do Azure Machine Learning Studio](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Gerir o seu serviço de web clássico

Existem várias ações que pode efetuar para monitorizar um serviço web. Pode atualizá-la e eliminá-lo. Também pode adicionar pontos finais adicionais para um serviço da web clássico, além do ponto final predefinido que é criado quando a implementá-la.

Para obter mais informações, consulte [gerir uma área de trabalho do Azure Machine Learning Studio](manage-workspace.md) e [gerir um serviço web com o portal de serviços Web do Azure Machine Learning Studio](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Atualizar o serviço web
Pode efetuar alterações ao seu serviço web, como atualizar o modelo com dados de treinamento adicional e implementá-la novamente, substituindo o web service original.

Para atualizar o serviço web, abra a experimentação preditiva original utilizada para implementar o serviço web e fazer uma cópia de editável clicando **guardar como**. Faça as alterações e, em seguida, clique em **implementar serviço Web**.

Uma vez que implantou esta experiência antes, é-lhe perguntado se pretende substituir (serviço Web clássico) ou atualizar (novo serviço web) o serviço existente. Clicar **Sim** ou **atualização** para o serviço web existente e implementa a nova experimentação preditiva é implementada no seu lugar.

> [!NOTE]
> Se tiver efetuado alterações de configuração no original web service, por exemplo, introduzir um novo nome a apresentar ou a descrição, terá de introduzir novamente esses valores.

Uma opção para atualizar o seu serviço web é para voltar a preparar programaticamente o modelo. Para obter mais informações, consulte [Retrain Machine Learning Studio dos modelos](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais detalhes técnicos sobre como funciona a implantação, consulte [progride de modelo de como um Machine Learning Studio de uma experimentação para um serviço Web operacionalizado](model-progression-experiment-to-web-service.md).

* Para obter detalhes sobre como obter o seu modelo pronto para implementar, veja [como preparar o seu modelo para a implementação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

* Existem várias formas de utilizar a API REST e aceder ao serviço Web. Ver [como consumir um serviço web do Azure Machine Learning Studio](consume-web-services.md).


<!-- internal links -->
[Criar uma experimentação de preparação]: #create-a-training-experiment
[Convertê-lo para uma experimentação preditiva]: #convert-the-training-experiment-to-a-predictive-experiment
[Novo serviço web]: #deploy-it-as-a-new-web-service
[Serviço web clássico]: #deploy-it-as-a-classic-web-service
[new]: #deploy-the-predictive-experiment-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

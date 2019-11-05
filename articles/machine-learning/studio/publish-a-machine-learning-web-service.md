---
title: Implantar um serviço Web Machine Learning Studio (clássico)
titleSuffix: Azure Machine Learning Studio (classic)
description: Como converter um teste de treinamento em um experimento de previsão, prepará-lo para implantação e implantá-lo como um serviço Web Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: e3d8ccebf31a16ecb39a6d5eb1c1f4071d746ea0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496790"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Implantar um serviço Web Azure Machine Learning Studio (clássico)

Azure Machine Learning Studio (clássico) permite que você crie e teste uma solução analítica preditiva. Em seguida, você pode implantar a solução como um serviço Web.

Machine Learning Studio serviços Web (clássicos) fornecem uma interface entre um aplicativo e um modelo de Pontuação de fluxo de trabalho Machine Learning Studio (clássico). Um aplicativo externo pode se comunicar com um modelo de Pontuação de fluxo de trabalho Machine Learning Studio (clássico) em tempo real. Uma chamada para um serviço Web Machine Learning Studio (clássico) retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada para um serviço Web, tem de transmitir uma chave de API que foi criada quando implementou esse serviço. Um serviço Web Machine Learning Studio (clássico) é baseado em REST, uma opção de arquitetura popular para projetos de programação da Web.

A versão clássica do Azure Machine Learning Studio tem dois tipos de serviços Web:

* RRS (serviço de solicitação-resposta): um serviço de baixa latência e altamente escalonável que classifica um único registro de dados.
* BES (serviço de execução em lote): um serviço assíncrono que classifica um lote de registros de dados.

A entrada para BES é como a entrada de dados que o RRS utiliza. A principal diferença é que BES lê um bloco de registos a partir de várias origens, como o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure, a Base de Dados SQL do Azure, o HDInsight (consulta do Hive) e origens HTTP.

De um ponto de vista de alto nível, você implanta seu modelo em três etapas:

* **[Criar um teste de treinamento]** -na versão clássica do Studio, você pode treinar e testar um modelo de análise preditiva usando dados de treinamento fornecidos por você, usando um grande conjunto de algoritmos de aprendizado de máquina internos.
* **[Convertê-lo em um experimento de previsão]** -depois que seu modelo tiver sido treinado com dados existentes e você estiver pronto para usá-lo para pontuar novos dados, prepare e simplifique seu experimento para previsões.
* **Implantá** -lo como um **[novo serviço Web]** ou um **[serviço Web clássico]** -quando você implanta seu teste de previsão como um serviço Web do Azure, os usuários podem enviar dados para seu modelo e receber as previsões do modelo.

## <a name="create-a-training-experiment"></a>Criar um teste de treinamento

Para treinar um modelo de análise preditiva, você usa a versão clássica do Azure Machine Learning Studio para criar um teste de treinamento no qual você inclui vários módulos para carregar dados de treinamento, preparar os dados conforme necessário, aplicar algoritmos de aprendizado de máquina e Avalie os resultados. Você pode iterar em um experimento e tentar diferentes algoritmos de aprendizado de máquina para comparar e avaliar os resultados.

O processo de criação e gerenciamento de experimentos de treinamento é abordado mais detalhadamente em qualquer lugar. Para obter mais informações, veja estes artigos:

* [Criar um experimento simples no Azure Machine Learning Studio (clássico)](create-experiment.md)
* [Desenvolver uma solução preditiva com Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md)
* [Importar seus dados de treinamento para Azure Machine Learning Studio (clássico)](import-data.md)
* [Gerenciar iterações de experimento no Azure Machine Learning Studio (clássico)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o teste de treinamento em um experimento de previsão

Depois de treinar seu modelo, você estará pronto para converter seu teste de treinamento em um experimento de previsão para pontuar novos dados.

Ao converter em um experimento de previsão, você estará preparando seu modelo treinado para ser implantado como um serviço Web de pontuação. Os usuários do serviço Web podem enviar dados de entrada para seu modelo e seu modelo enviará de volta os resultados da previsão. Ao converter em um experimento de previsão, tenha em mente como você espera que seu modelo seja usado por outras pessoas.

Para converter seu teste de treinamento em um experimento de previsão, clique em **executar** na parte inferior da tela do experimento, clique em **configurar serviço Web**e selecione **serviço Web de previsão**.

![Converter em experimento de Pontuação](./media/publish-a-machine-learning-web-service/figure-1.png)

Para obter mais informações sobre como executar essa conversão, consulte [como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).

As etapas a seguir descrevem a implantação de um teste de previsão como um novo serviço Web. Você também pode implantar o experimento como um serviço Web clássico.

## <a name="deploy-it-as-a-new-web-service"></a>Implantá-lo como um novo serviço Web

Agora que o experimento de previsão foi preparado, você pode implantá-lo como um novo serviço Web do Azure (baseado no Gerenciador de recursos). Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para implantar seu experimento de previsão, clique em **executar** na parte inferior da tela do experimento. Quando o experimento terminar de ser executado, clique em **implantar serviço Web** e selecione **implantar serviço Web [novo]** .  A página implantação do portal do serviço Web Machine Learning Studio (clássico) é aberta.

> [!NOTE] 
> Para implantar um novo serviço Web, você deve ter permissões suficientes na assinatura na qual você está implantando o serviço Web. Para obter mais informações, consulte [gerenciar um serviço Web usando o Azure Machine Learning Portal de serviços Web](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Página de experimento de implantação do portal de serviço Web

Na página implantar experimento, insira um nome para o serviço Web.
Selecione um plano de preços. Se você tiver um plano de preços existente, poderá selecioná-lo, caso contrário, deverá criar um novo plano de preços para o serviço.

1. Na lista suspensa **plano de preços** , selecione um plano existente ou selecione a opção **selecionar novo plano** .
2. Em **nome do plano**, digite um nome que identifique o plano em sua fatura.
3. Selecione uma das **camadas de plano mensal**. As camadas de plano assumem como padrão os planos para sua região padrão e seu serviço Web é implantado nessa região.

Clique em **implantar** e a página **início rápido** para o serviço Web é aberta.

A página de início rápido do serviço Web fornece acesso e diretrizes sobre as tarefas mais comuns que você executará depois de criar um serviço Web. A partir daqui, você pode acessar facilmente a página de teste e a página de consumo.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testar seu novo serviço Web

Para testar o novo serviço Web, clique em **testar serviço Web** em tarefas comuns. Na página de teste, você pode testar seu serviço Web como um serviço de solicitação-resposta (RRS) ou um serviço de execução em lote (BES).

A página de teste RRS exibe as entradas, as saídas e os parâmetros globais que você definiu para o experimento. Para testar o serviço Web, você pode inserir manualmente os valores apropriados para as entradas ou fornecer um arquivo formatado de CSV (valor separado por vírgula) que contém os valores de teste.

Para testar usando RRS, no modo de exibição de lista, insira os valores apropriados para as entradas e clique em **testar solicitação-resposta**. Os resultados da previsão são exibidos na coluna de saída à esquerda.

![Insira os valores apropriados para testar seu serviço Web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para testar seu BES, clique em **lote**. Na página teste do lote, clique em procurar em sua entrada e selecione um arquivo CSV que contém os valores de exemplo apropriados. Se você não tiver um arquivo CSV e tiver criado seu experimento de previsão usando a versão clássica do Machine Learning Studio, poderá baixar o conjunto de dados para seu experimento de previsão e usá-lo.

Para baixar o conjunto de dados, abra a versão clássica do Machine Learning Studio. Abra seu experimento de previsão e clique com o botão direito do mouse na entrada de seu experimento. No menu de contexto, selecione **conjunto** de conteúdo e, em seguida, selecione **baixar**.

![Baixar o conjunto de dados da tela do estúdio (clássico)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Clique em **testar**. O status do seu trabalho de execução em lote é exibido à direita em **trabalhos do lote de teste**.

![Testar seu trabalho de execução em lote com o portal de serviço Web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na página **configuração** , você pode alterar a descrição, o título, atualizar a chave da conta de armazenamento e habilitar os dados de exemplo para o serviço Web.

![Configurar seu serviço Web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Acessar seu novo serviço Web

Depois de implantar o serviço Web da versão clássica do Machine Learning Studio, você pode enviar dados para o serviço e receber respostas programaticamente.

A página **consumir** fornece todas as informações necessárias para acessar o serviço Web. Por exemplo, a chave de API é fornecida para permitir o acesso autorizado ao serviço.

Para obter mais informações sobre como acessar um serviço Web Machine Learning Studio (clássico), consulte [como consumir um serviço web Azure Machine Learning Studio (clássico)](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gerenciar seu novo serviço Web

Você pode gerenciar seus novos serviços Web Machine Learning Studio portal de serviços Web (clássico). Na [página principal do portal](https://services.azureml-test.net/), clique em **Serviços Web**. Na página serviços Web, você pode excluir ou copiar um serviço. Para monitorar um serviço específico, clique no serviço e, em seguida, clique em **painel**. Para monitorar trabalhos em lotes associados ao serviço Web, clique em **log de solicitações em lote**.

### <a id="multi-region"></a>Implantar seu novo serviço Web em várias regiões

Você pode facilmente implantar um novo serviço Web em várias regiões sem precisar de várias assinaturas ou espaços de trabalho.

O preço é específico da região, portanto, você precisa definir um plano de cobrança para cada região em que você implantará o serviço Web.

#### <a name="create-a-plan-in-another-region"></a>Criar um plano em outra região

1. Entre no [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).
2. Clique na opção de menu **planos** .
3. Na página planos sobre a exibição, clique em **novo**.
4. Na lista suspensa **assinatura** , selecione a assinatura na qual o novo plano residirá.
5. Na lista suspensa **região** , selecione uma região para o novo plano. As opções de plano para a região selecionada serão exibidas na seção **Opções de plano** da página.
6. Na lista suspensa **grupo de recursos** , selecione um grupo de recursos para o plano. Para obter mais informações sobre grupos de recursos, consulte [Azure Resource Manager visão geral](../../azure-resource-manager/resource-group-overview.md).
7. Em **nome do plano** , digite o nome do plano.
8. Em **Opções de plano**, clique no nível de cobrança para o novo plano.
9. Clique em **Criar**.

#### <a name="deploy-the-web-service-to-another-region"></a>Implantar o serviço Web em outra região

1. Na página Microsoft Azure Machine Learning Web Services, clique na opção de menu **Serviços Web** .
2. Selecione o serviço Web que você está implantando em uma nova região.
3. Clique em **copiar**.
4. Em **nome do serviço Web**, digite um novo nome para o serviço Web.
5. Em **Descrição do serviço Web**, digite uma descrição para o serviço Web.
6. Na lista suspensa **assinatura** , selecione a assinatura em que o novo serviço Web residirá.
7. Na lista suspensa **grupo de recursos** , selecione um grupo de recursos para o serviço Web. Para obter mais informações sobre grupos de recursos, consulte [Azure Resource Manager visão geral](../../azure-resource-manager/resource-group-overview.md).
8. Na lista suspensa **região** , selecione a região na qual o serviço Web será implantado.
9. Na lista suspensa **conta de armazenamento** , selecione uma conta de armazenamento na qual armazenar o serviço Web.
10. Na lista suspensa **plano de preços** , selecione um plano na região que você selecionou na etapa 8.
11. Clique em **copiar**.

## <a name="deploy-it-as-a-classic-web-service"></a>Implantá-lo como um serviço Web clássico

Agora que o experimento de previsão foi suficientemente preparado, você pode implantá-lo como um serviço Web do Azure clássico. Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para implantar seu experimento de previsão, clique em **executar** na parte inferior da tela do experimento e clique em **implantar serviço Web**. O serviço Web é configurado e você é colocado no painel do serviço Web.

![Implantar o serviço Web do estúdio (clássico)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testar seu serviço Web clássico

Você pode testar o serviço Web no portal de serviços Web Machine Learning Studio (clássico) ou Machine Learning Studio (clássico).

Para testar o serviço Web de resposta de solicitação, clique no botão **testar** no painel do serviço Web. Uma caixa de diálogo é exibida para solicitar os dados de entrada para o serviço. Essas são as colunas esperadas pelo experimento de pontuação. Insira um conjunto de dados e clique em **OK**. Os resultados gerados pelo serviço Web são exibidos na parte inferior do painel.

Você pode clicar no link visualização de **teste** para testar seu serviço na versão clássica do Azure Machine Learning Studio portal de serviços Web, conforme mostrado anteriormente na seção novo serviço Web.

Para testar o serviço de execução em lote, clique no link Visualizar **teste** . Na página teste do lote, clique em procurar em sua entrada e selecione um arquivo CSV que contém os valores de exemplo apropriados. Se você não tiver um arquivo CSV e tiver criado seu experimento de previsão usando a versão clássica do Machine Learning Studio, poderá baixar o conjunto de dados para seu experimento de previsão e usá-lo.

![Testar o serviço Web](./media/publish-a-machine-learning-web-service/figure-3.png)

Na página **configuração** , você pode alterar o nome de exibição do serviço e dar a ele uma descrição. O nome e a descrição são exibidos no [portal do Azure](https://portal.azure.com/) em que você gerencia seus serviços Web.

Você pode fornecer uma descrição para os dados de entrada, dados de saída e parâmetros de serviço Web inserindo uma cadeia de caracteres para cada coluna em **esquema de entrada**, **esquema de saída**e **parâmetro de serviço Web**. Essas descrições são usadas na documentação de código de exemplo fornecida para o serviço Web.

Você pode habilitar o log para diagnosticar as falhas que você está vendo quando o serviço Web é acessado. Para obter mais informações, consulte [habilitar o registro em log para serviços web Machine Learning Studio (clássico)](web-services-logging.md).

![Habilitar o registro em log no portal de serviços Web](./media/publish-a-machine-learning-web-service/figure-4.png)

Você também pode configurar os pontos de extremidade para o serviço Web no portal do Azure Machine Learning Web Services semelhante ao procedimento mostrado anteriormente na seção novo serviço Web. As opções são diferentes, você pode adicionar ou alterar a descrição do serviço, habilitar o registro em log e habilitar os dados de exemplo para teste.

### <a name="access-your-classic-web-service"></a>Acessar seu serviço Web clássico

Depois de implantar o serviço Web da versão clássica do Machine Learning Studio, você pode enviar dados para o serviço e receber respostas programaticamente.

O painel fornece todas as informações necessárias para acessar o serviço Web. Por exemplo, a chave de API é fornecida para permitir acesso autorizado ao serviço, e as páginas de ajuda da API são fornecidas para ajudá-lo a começar a escrever seu código.

Para obter mais informações sobre como acessar um serviço Web Machine Learning Studio (clássico), consulte [como consumir um serviço web Azure Machine Learning Studio (clássico)](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Gerenciar seu serviço Web clássico

Há várias ações que você pode executar para monitorar um serviço Web. Você pode atualizá-lo e excluí-lo. Você também pode adicionar pontos de extremidade adicionais a um serviço Web clássico, além do ponto de extremidades padrão que é criado quando você o implanta.

Para obter mais informações, consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)](manage-workspace.md) e [gerenciar um serviço Web usando o portal de serviços Web Azure Machine Learning Studio (clássico)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Atualizar o serviço Web
Você pode fazer alterações no serviço Web, como atualizar o modelo com dados de treinamento adicionais e implantá-lo novamente, substituindo o serviço Web original.

Para atualizar o serviço Web, abra o teste de previsão original que você usou para implantar o serviço Web e faça uma cópia editável clicando em **salvar como**. Faça as alterações e clique em **implantar serviço Web**.

Como você implantou esse experimento antes, será perguntado se deseja substituir (serviço Web clássico) ou atualizar (novo serviço Web) o serviço existente. Clicar em **Sim** ou **Atualizar** interrompe o serviço Web existente e implanta o novo teste de previsão que é implantado em seu lugar.

> [!NOTE]
> Se você fez alterações de configuração no serviço Web original, por exemplo, inserindo um novo nome de exibição ou descrição, será necessário inserir esses valores novamente.

Uma opção para atualizar seu serviço Web é treinar novamente o modelo de forma programática. Para obter mais informações, consulte [readaptação de modelos de Machine Learning Studio (clássico) programaticamente](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais detalhes técnicos sobre como funciona a implantação, consulte [como um modelo de Machine Learning Studio (clássico) progride de um experimento para um serviço Web operacional](model-progression-experiment-to-web-service.md).

* Para obter detalhes sobre como preparar seu modelo para implantação, consulte [como Prepare seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).

* Existem várias formas de utilizar a API REST e aceder ao serviço Web. Consulte [como consumir um serviço web Azure Machine Learning Studio (clássico)](consume-web-services.md).

<!-- internal links -->
[Criar um teste de treinamento]: #create-a-training-experiment
[Convertê-lo em um experimento de previsão]: #convert-the-training-experiment-to-a-predictive-experiment
[Novo serviço Web]: #deploy-it-as-a-new-web-service
[Serviço Web clássico]: #deploy-it-as-a-classic-web-service
[Novo]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

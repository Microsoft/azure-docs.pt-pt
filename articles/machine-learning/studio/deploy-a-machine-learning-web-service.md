---
title: Implementar serviços Web
titleSuffix: ML Studio (classic) - Azure
description: Como converter uma experiência de treino numa experiência preditiva, prepará-la para implantação e, em seguida, implantá-la como um serviço web Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 6c81e50560de69f7702e852d4602680fde7f01f3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218132"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Implementar um serviço web Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

O Azure Machine Learning Studio (clássico) permite-lhe construir e testar uma solução analítica preditiva. Depois pode implementar a solução como um serviço web.

Os serviços web machine learning studio (clássicos) fornecem uma interface entre uma aplicação e um modelo de pontuação de fluxo de trabalho do Machine Learning Studio (clássico). Uma aplicação externa pode comunicar com um modelo de pontuação de fluxo de trabalho do Machine Learning Studio (clássico) em tempo real. Uma chamada para um serviço web machine learning (clássico) devolve os resultados da previsão a uma aplicação externa. Para fazer uma chamada para um serviço Web, tem de transmitir uma chave de API que foi criada quando implementou esse serviço. Um serviço web Machine Learning Studio (clássico) baseia-se no REST, uma escolha de arquitetura popular para projetos de programação web.

O Azure Machine Learning Studio (clássico) tem dois tipos de serviços web:

* Serviço de Resposta a Pedidos (RRS): Um serviço de latência baixa e altamente escalável que marca um único registo de dados.
* Serviço de Execução de Lotes (BES): um serviço assíncrono que marca um lote de registos de dados.

A entrada para BES é como a entrada de dados que o RRS utiliza. A principal diferença é que BES lê um bloco de registos a partir de várias origens, como o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure, a Base de Dados SQL do Azure, o HDInsight (consulta do Hive) e origens HTTP.

De um ponto de vista de alto nível, você implanta o seu modelo em três passos:

* **[Criar uma experiência de treino]** de formação - Em Estúdio (clássico), pode treinar e testar um modelo de análise preditiva utilizando dados de treino que fornece, utilizando um grande conjunto de algoritmos de aprendizagem automática incorporados.
* **[Convertê-lo numa experiência preditiva]** - Uma vez treinado o seu modelo com dados existentes e está pronto a usá-lo para obter novos dados, prepare e agilize a sua experiência para previsões.
* **Implemente-o** como um **[novo serviço web]** ou um serviço web **[Serviço web clássico]** - Quando implementa a sua experiência preditiva como um serviço web Azure, os utilizadores podem enviar dados para o seu modelo e receber as previsões do seu modelo.

## <a name="create-a-training-experiment"></a>Criar uma experimentação de preparação

Para treinar um modelo de análise preditiva, você usa o Azure Machine Learning Studio (clássico) para criar uma experiência de treino onde você inclui vários módulos para carregar dados de treino, preparar os dados conforme necessário, aplicar algoritmos de aprendizagem automática, e avaliar o resultados. Pode iterar numa experiência e experimentar diferentes algoritmos de aprendizagem automática para comparar e avaliar os resultados.

O processo de criação e gestão de experiências de formação é coberto mais profundamente noutros locais. Para obter mais informações, veja estes artigos:

* [Crie uma experiência simples no Azure Machine Learning Studio (clássico)](create-experiment.md)
* [Desenvolver uma solução preditiva com o Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md)
* [Importe os seus dados de formação para o Azure Machine Learning Studio (clássico)](import-data.md)
* [Gerir iterações de experimentação no Azure Machine Learning Studio (clássico)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experimentação de preparação para uma experimentação preditiva

Depois de treinar o seu modelo, está pronto para converter a sua experiência de treino numa experiência preditiva para obter novos dados.

Ao converter-se numa experiência preditiva, está a preparar o seu modelo treinado para ser implantado como um serviço web de pontuação. Os utilizadores do serviço web podem enviar dados de entrada para o seu modelo e o seu modelo irá enviar de volta os resultados da previsão. À medida que se converte a uma experiência preditiva, lembre-se de como espera que o seu modelo seja usado pelos outros.

Para converter a sua experiência de treino numa experiência preditiva, clique em **Executar** na parte inferior da tela de experiência, clique em **Configurar o Web Service**e, em seguida, selecione **Predictive Web Service**.

![Converter-se em experiência de pontuação](./media/publish-a-machine-learning-web-service/figure-1.png)

Para obter mais informações sobre como realizar esta conversão, consulte [como preparar o seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).

Os seguintes passos descrevem a implementação de uma experiência preditiva como um novo serviço web. Também pode implementar a experiência como serviço web Classic.

## <a name="deploy-it-as-a-new-web-service"></a>Implementá-lo como um novo serviço web

Agora que a experiência preditiva foi preparada, pode implantá-la como um novo serviço web Azure (baseado em Recursos). Usando o web service, os utilizadores podem enviar dados para o seu modelo e o modelo retornará seu previsões.

Para implementar a sua experiência preditiva, clique em **Correr** na parte inferior da tela da experiência. Uma vez que a experiência termine de funcionar, clique em **Implementar o Serviço Web** e selecione Implementar o Serviço Web  **[Novo]** .  Abre a página de implementação do portal de serviço web do Machine Learning Studio (clássico).

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição para a qual estiver a implementar o serviço web. Para mais informações consulte, [Gerencie um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Página de experiências do portal do serviço web

Na página Deploy Experiment, introduza um nome para o serviço web.
Selecione um plano de preços. Se tiver um plano de preços existente, pode selecioná-lo, caso contrário terá de criar um novo plano de preços para o serviço.

1. No **Plano** de Preços desça, selecione um plano existente ou selecione a nova opção **de plano Select.**
2. Em **Nome do Plano**, escreva um nome que identifique o plano na sua conta.
3. Selecione um dos Níveis de **Plano Mensal**. A predefinição de escalões do plano para os planos para a sua região predefinida e o seu serviço web é implementada nessa região.

Clique em **Implementar** e a página **Quickstart** para o seu serviço web abre.

A página quickstart do serviço web dá-lhe acesso e orientação sobre as tarefas mais comuns que irá executar após a criação de um serviço web. A partir daqui, pode aceder facilmente tanto à página de Teste como à página Consumir.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Teste o seu novo serviço web

Para testar o seu novo serviço web, clique em **Testar o serviço web** em tarefas comuns. Na página de Teste, pode testar o seu serviço web como um Serviço de Resposta a Pedidos (RRS) ou um serviço de execução de lotes (BES).

A página de teste RRS exibe as inputs, saídas e quaisquer parâmetros globais que tenha definido para a experiência. Para testar o serviço web, pode introduzir manualmente valores apropriados para as entradas ou fornecer um ficheiro formado de valor separado de vírvia (CSV) contendo os valores de teste.

Para testar a utilização de RRS, do modo de visualização da lista, introduza os valores adequados para as inputs e clique em **Test Request-Response**. Os resultados da sua previsão mostram na coluna de saída à esquerda.

![Introduza os valores apropriados para testar o seu serviço web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para testar o seu BES, clique **em Batch**. Na página de teste do Lote, clique em Navegar sob a sua entrada e selecione um ficheiro CSV contendo valores de amostra apropriados. Se não tiver um ficheiro CSV e tiver criado a sua experiência preditiva utilizando o Machine Learning Studio (clássico), pode descarregar o conjunto de dados para a sua experiência preditiva e usá-lo.

Para descarregar o conjunto de dados, abra o Machine Learning Studio (clássico). Abra a sua experiência preditiva e clique corretamente na entrada para a sua experiência. A partir do menu de contexto, selecione **dataset** e, em seguida, **selecione Download**.

![Descarregue o seu conjunto de dados da tela do Studio (clássico)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Clique no **Teste**. O estado do seu trabalho de execução de lote mostra à direita sob **o test batch jobs**.

![Teste o seu trabalho de execução de lote com o portal de serviço web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na página **DE CONFIGURAÇÃO,** pode alterar a descrição, o título, atualizar a chave da conta de armazenamento e ativar os dados da amostra para o seu serviço web.

![Configure o seu serviço web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Aceda ao seu Novo serviço web

Assim que implementar o seu serviço web a partir do Machine Learning Studio (clássico), pode enviar dados para o serviço e receber respostas programáticamente.

A página **Consumir** fornece todas as informações necessárias para aceder ao seu serviço web. Por exemplo, a chave API é fornecida para permitir o acesso autorizado ao serviço.

Para obter mais informações sobre o acesso a um serviço web machine learning studio (clássico), consulte [Como consumir um serviço Web Azure Machine Learning Studio (clássico).](consume-web-services.md)

### <a name="manage-your-new-web-service"></a>Gerencie o seu Novo serviço web

Você pode gerir os seus novos serviços web usando o portal de Serviços Web Machine Learning Studio (clássico). Na página principal do [portal,](https://services.azureml.net/)clique em **Web Services**. A partir da página de serviços web, pode eliminar ou copiar um serviço. Para monitorizar um serviço específico, clique no serviço e, em seguida, clique no **Dashboard**. Para monitorizar os trabalhos de lote associados ao serviço web, clique no **Registo de Pedidos**de Lote .

### <a id="multi-region"></a>Implemente o seu Novo serviço web para várias regiões

Você pode facilmente implementar um novo serviço web para várias regiões sem precisar de múltiplas subscrições ou espaços de trabalho.

Os preços são específicos da região, por isso é necessário definir um plano de faturação para cada região em que irá implementar o serviço web.

#### <a name="create-a-plan-in-another-region"></a>Criar um plano noutra região

1. Inscreva-se nos Serviços Web de [Aprendizagem automática do Microsoft Azure](https://services.azureml.net/).
2. Clique na opção de menu **Planos.**
3. Na página de planos sobre visualização, clique em **New**.
4. A partir da queda por **Subscrição,** selecione a subscrição na qual o novo plano irá residir.
5. A partir da **região,** selecione uma região para o novo plano. As Opções de Plano para a região selecionada serão exibidas na secção **Opções** de Plano da página.
6. A partir do dropdown do **Grupo de Recursos,** selecione um grupo de recursos para o plano. De mais informações sobre grupos de recursos, consulte a visão geral do Gestor de [Recursos do Azure.](../../azure-resource-manager/management/overview.md)
7. No **Nome do Plano** digite o nome do plano.
8. Em **'Plan Options',** clique no nível de faturação do novo plano.
9. Clique em **Criar**.

#### <a name="deploy-the-web-service-to-another-region"></a>Implementar o serviço web para outra região

1. Na página da Microsoft Azure Machine Learning Web Services, clique na opção de menu **dos Serviços Web.**
2. Selecione o serviço Web que está a implementar uma nova região.
3. Clique em **Copiar**.
4. No **Nome do Serviço Web,** escreva um novo nome para o serviço web.
5. Na **descrição do serviço Web,** escreva uma descrição para o serviço web.
6. A partir da queda por **Subscrição,** selecione a subscrição na qual o novo serviço web irá residir.
7. A partir do dropdown do **Grupo de Recursos,** selecione um grupo de recursos para o serviço web. De mais informações sobre grupos de recursos, consulte a visão geral do Gestor de [Recursos do Azure.](../../azure-resource-manager/management/overview.md)
8. A partir da **região,** selecione a região para implantar o serviço web.
9. A partir da **conta de depósito,** selecione uma conta de armazenamento para armazenar o serviço web.
10. A partir do dropdown do **Plano de Preços,** selecione um plano na região que selecionou no passo 8.
11. Clique em **Copiar**.

## <a name="deploy-it-as-a-classic-web-service"></a>Implementá-lo como um serviço web clássico

Agora que a experiência preditiva foi suficientemente preparada, pode implantá-la como um serviço web Classic Azure. Usando o web service, os utilizadores podem enviar dados para o seu modelo e o modelo retornará seu previsões.

Para implementar a sua experiência preditiva, clique em **Executar** na parte inferior da tela de experimentação e, em seguida, clique em **implementar o Serviço Web**. O serviço web está configurado e você é colocado no painel de instrumentos do serviço web.

![Implemente o seu serviço web a partir do Studio (clássico)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Teste o seu serviço web Clássico

Pode testar o serviço web no portal machine learning studio (clássico) web services ou no Machine Learning Studio (clássico).

Para testar o serviço web de resposta a pedidos, clique no botão **Teste** no painel de instrumentos do serviço web. Aparece um diálogo para lhe pedir os dados de entrada para o serviço. Estas são as colunas esperadas pela experiência de pontuação. Introduza um conjunto de dados e, em seguida, clique **OK**. Os resultados gerados pelo serviço web são apresentados na parte inferior do painel de instrumentos.

Pode clicar no link de pré-visualização **do Teste** para testar o seu serviço no portal de Serviços Web Azure Machine Learning Studio (clássico), como mostrado anteriormente na secção de serviço web New.

Para testar o Serviço de Execução do Lote, clique no link de pré-visualização **do teste** . Na página de teste do Lote, clique em Navegar sob a sua entrada e selecione um ficheiro CSV contendo valores de amostra apropriados. Se não tiver um ficheiro CSV e tiver criado a sua experiência preditiva utilizando o Machine Learning Studio (clássico), pode descarregar o conjunto de dados para a sua experiência preditiva e usá-lo.

![Testar o serviço web](./media/publish-a-machine-learning-web-service/figure-3.png)

Na página **DE CONFIGURAÇÃO,** pode alterar o nome de exibição do serviço e dar-lhe uma descrição. O nome e descrição estão expostos no [portal Azure](https://portal.azure.com/) onde gere os seus serviços web.

Pode fornecer uma descrição dos seus dados de entrada, dados de saída e parâmetros de serviço web, inserindo uma cadeia para cada coluna em **INPUT SCHEMA,** **OUTPUT SCHEMA**e **Web SERVICE PARAMETER**. Estas descrições são utilizadas na documentação do código da amostra fornecida para o serviço web.

Pode permitir que o registo diagnostice quaisquer falhas que esteja a ver quando o seu serviço web é acedido. Para mais informações, consulte [Enable logging for Machine Learning Studio (clássico) web services](web-services-logging.md).

![Ativar o login no portal de serviços web](./media/publish-a-machine-learning-web-service/figure-4.png)

Também pode configurar os pontos finais do serviço web no portal Azure Machine Learning Web Services semelhante ao procedimento anteriormente mostrado na secção de serviço web New. As opções são diferentes, pode adicionar ou alterar a descrição do serviço, ativar o registo e ativar os dados da amostra para testes.

### <a name="access-your-classic-web-service"></a>Aceda ao seu serviço web Clássico

Assim que implementar o seu serviço web a partir do Azure Machine Learning Studio (clássico), pode enviar dados para o serviço e receber respostas programáticamente.

O painel de instrumentos fornece toda a informação necessária para aceder ao seu serviço web. Por exemplo, a chave API é fornecida para permitir o acesso autorizado ao serviço, e as páginas de ajuda da API são fornecidas para ajudá-lo a começar a escrever o seu código.

Para obter mais informações sobre o acesso a um serviço web machine learning studio (clássico), consulte [Como consumir um serviço Web Azure Machine Learning Studio (clássico).](consume-web-services.md)

### <a name="manage-your-classic-web-service"></a>Gerencie o seu serviço web Clássico

Existem várias ações que pode realizar para monitorizar um serviço web. Pode atualizá-lo e apagá-lo. Também pode adicionar pontos finais adicionais a um serviço web Clássico, além do ponto final padrão que é criado quando o implementa.

Para mais informações, consulte Gerir um espaço de [trabalho do Azure Machine Learning Studio (clássico)](manage-workspace.md) e [gerir um serviço web utilizando o portal Azure Machine Learning Studio (clássico) Web Services](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Atualizar o serviço web
Pode efazer alterações no seu serviço web, como atualizar o modelo com dados de treino adicionais, e implantá-lo novamente, sobrepor-se ao serviço web original.

Para atualizar o serviço web, abra a experiência preditiva original que usou para implementar o serviço web e fazer uma cópia editável clicando em **SAVE AS**. Faça as suas alterações e, em seguida, clique em **Implementar o Serviço Web**.

Como já implementou esta experiência antes, é-lhe perguntado se deseja substituir (Classic Web Service) ou atualizar (Novo serviço web) o serviço existente. Clicar **EM SIM** ou **Atualizar** para o serviço web existente e implementa a nova experiência preditiva que está implantada no seu lugar.

> [!NOTE]
> Se tiver feito alterações de configuração no serviço web original, por exemplo, ao introduzir um novo nome ou descrição do ecrã, terá de voltar a introduzir esses valores.

Uma opção para atualizar o seu serviço web é retreinar o modelo programáticamente. Para mais informações, consulte os [modelos Retrain Machine Learning Studio (clássicos) programáticamente](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais detalhes técnicos sobre como funciona a implementação, veja [como um modelo de Machine Learning Studio (clássico) evolui de uma experiência para um serviço Web operacionalizado.](model-progression-experiment-to-web-service.md)

* Para mais detalhes sobre como preparar o seu modelo, consulte [como preparar o seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).

* Existem várias formas de utilizar a API REST e aceder ao serviço Web. Veja [como consumir um serviço web Azure Machine Learning Studio (clássico).](consume-web-services.md)

<!-- internal links -->
[Criar uma experiência de treino]: #create-a-training-experiment
[Convertê-lo numa experiência preditiva]: #convert-the-training-experiment-to-a-predictive-experiment
[Novo serviço web]: #deploy-it-as-a-new-web-service
[Serviço web clássico]: #deploy-it-as-a-classic-web-service
[Novo]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

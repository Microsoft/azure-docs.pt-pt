---
title: 'Estúdio ML (clássico): Implementar um serviço web - Azure'
description: Como converter uma experiência de treino para uma experiência preditiva, prepará-la para a implementação e, em seguida, implantá-la como um serviço web Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 018e292abc7050f5d71c6bf9065618fecf8b5668
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520447"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Implementar um serviço web Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Esta é uma marca de verificação, o que significa que este artigo se aplica ao Machine Learning Studio (clássico). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Este é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


O Azure Machine Learning Studio (clássico) permite-lhe construir e testar uma solução analítica preditiva. Em seguida, pode implementar a solução como um serviço web.

Os serviços web do Machine Learning Studio (clássico) fornecem uma interface entre uma aplicação e um modelo de pontuação de fluxo de trabalho machine learning (clássico). Uma aplicação externa pode comunicar com um modelo de pontuação de fluxo de trabalho machine learning (clássico) em tempo real. Uma chamada para um estúdio de machine learning (clássico) retorna os resultados da previsão a uma aplicação externa. Para fazer uma chamada para um serviço Web, tem de transmitir uma chave de API que foi criada quando implementou esse serviço. Um serviço web do Machine Learning Studio (clássico) é baseado no REST, uma escolha popular de arquitetura para projetos de programação web.

O Azure Machine Learning Studio (clássico) tem dois tipos de serviços web:

* serviço Request-Response (RRS): um serviço de baixa latência, altamente escalável que marca um único registo de dados.
* Serviço de Execução de Lotes (BES): Um serviço assíncronos que regista um lote de registos de dados.

A entrada para BES é como a entrada de dados que o RRS utiliza. A principal diferença é que BES lê um bloco de registos a partir de várias origens, como o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure, a Base de Dados SQL do Azure, o HDInsight (consulta do Hive) e origens HTTP.

Do ponto de vista de alto nível, implementa o seu modelo em três passos:

* **[Crie uma experiência de formação]** - Em Studio (clássico), pode treinar e testar um modelo de análise preditiva usando dados de treino que fornece, usando um grande conjunto de algoritmos de aprendizagem automática incorporados.
* **[Converta-o numa experiência preditiva]** - Uma vez que o seu modelo tenha sido treinado com dados existentes e esteja pronto a usá-lo para obter novos dados, prepara e agiliza a sua experiência para previsões.
* **Implemente-o** como um **[novo serviço web]** ou um serviço web **[clássico]** - Quando implementar a sua experiência preditiva como um serviço web Azure, os utilizadores podem enviar dados para o seu modelo e receber as previsões do seu modelo.

## <a name="create-a-training-experiment"></a>Criar uma experiência de treino

Para treinar um modelo de análise preditiva, utiliza-se o Azure Machine Learning Studio (clássico) para criar uma experiência de formação onde se inclui vários módulos para carregar dados de treino, preparar os dados conforme necessário, aplicar algoritmos de aprendizagem automática e avaliar os resultados. Você pode iterar em uma experiência e experimentar diferentes algoritmos de aprendizagem automática para comparar e avaliar os resultados.

O processo de criação e gestão de experiências de formação é coberto mais profundamente noutros locais. Para obter mais informações, veja estes artigos:

* [Crie uma experiência simples no Azure Machine Learning Studio (clássico)](create-experiment.md)
* [Desenvolver uma solução preditiva com o Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md)
* [Importe os seus dados de formação no Azure Machine Learning Studio (clássico)](import-data.md)
* [Gerir iterações experimentais no Azure Machine Learning Studio (clássico)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experiência de treino numa experiência preditiva

Assim que treinas o teu modelo, estás pronto para converter a tua experiência de treino numa experiência preditiva para obter novos dados.

Ao converter-se a uma experiência preditiva, está a preparar o seu modelo treinado para ser implantado como um serviço web de pontuação. Os utilizadores do serviço web podem enviar dados de entrada para o seu modelo e o seu modelo enviará de volta os resultados da previsão. À medida que se converte a uma experiência preditiva, lembre-se de como espera que o seu modelo seja usado por outros.

O processo de conversão de uma experiência de treino para uma experiência preditiva envolve três etapas:

1. Substitua os módulos de algoritmo de aprendizagem automática pelo seu modelo treinado.
2. Corte a experiência apenas para os módulos que são necessários para a pontuação. Uma experiência de treino inclui uma série de módulos que são necessários para a formação, mas não são necessários uma vez que o modelo é treinado.
3. Defina como o seu modelo aceitará dados do utilizador do serviço web e quais os dados que serão devolvidos.

> [!TIP]
> Na sua experiência de treino, tem-se preocupado em treinar e marcar o seu modelo usando os seus próprios dados. Mas uma vez implementados, os utilizadores enviarão novos dados para o seu modelo e devolverão os resultados da previsão. Assim, ao converter a sua experiência de treino numa experiência preditiva para a preparar para a implantação, lembre-se de como o modelo será usado por outros.

![Converter para experiência de pontuação](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>Configurar o botão de serviço web
Depois de executar a sua experiência (clique em **RUN** na parte inferior da tela de experiência), clique no botão **'Configurar o Serviço Web'** (selecione a opção **Serviço Web Preditivo).** **A Configuração do Serviço Web** executa para si os três passos de conversão da sua experiência de treino para uma experiência preditiva:

1. Guarda o seu modelo treinado na secção **Modelos Treinados** da paleta de módulos (à esquerda da tela experimentada). Em seguida, substitui o algoritmo de aprendizagem automática e os módulos [Train Model][train-model] pelo modelo treinado guardado.
2. Analisa a sua experiência e remove módulos que foram claramente utilizados apenas para o treino e que já não são necessários.
3. Insere módulos de entrada e _saída_ _de serviço Web_ em locais predefinidos na sua experiência (estes módulos aceitam e devolvem os dados do utilizador).

Por exemplo, a seguinte experiência treina um modelo de árvore de decisão reforçada de duas classes utilizando dados de recenseamento da amostra:

![Experiência de treino](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Os módulos desta experiência executam basicamente quatro funções diferentes:

![Funções do módulo](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Quando converte esta experiência de treino numa experiência preditiva, alguns destes módulos já não são necessários, ou agora servem um propósito diferente:

* **Dados** - Os dados deste conjunto de dados da amostra não são utilizados durante a pontuação - o utilizador do serviço web fornecerá os dados a serem pontuados. No entanto, os metadados deste conjunto de dados, como tipos de dados, são utilizados pelo modelo treinado. Por isso, é necessário manter o conjunto de dados na experiência preditiva para que possa fornecer estes metadados.

* **Preparação** - Dependendo dos dados do utilizador que serão submetidos para pontuação, estes módulos podem ou não ser necessários para processar os dados recebidos. O botão **De Configurar o Serviço Web** não toca neles - tem de decidir como pretende manuseá-los.
  
    Por exemplo, neste exemplo, o conjunto de dados da amostra pode ter valores em falta, pelo que foi incluído um módulo [de Dados Desaparecidos Limpos][clean-missing-data] para lidar com eles. Além disso, o conjunto de dados da amostra inclui colunas que não são necessárias para treinar o modelo. Assim, um [módulo Select Columns in Dataset][select-columns] foi incluído para excluir essas colunas extra do fluxo de dados. Se souber que os dados que serão submetidos para pontuação através do serviço web não terão valores em falta, então pode remover o módulo [De Dados Desaparecidos Limpos.][clean-missing-data] No entanto, uma vez que o módulo [Select Columns in Dataset][select-columns] ajuda a definir as colunas de dados que o modelo treinado espera, esse módulo precisa de permanecer.

* **Comboio** - Estes módulos são utilizados para treinar o modelo. Ao clicar em **Configurar o Serviço Web,** estes módulos são substituídos por um único módulo que contém o modelo que treinou. Este novo módulo é guardado na secção **Modelos Treinados** da paleta de módulos.

* **Pontuação** - Neste exemplo, o módulo [Dados Divididos][split] é utilizado para dividir o fluxo de dados em dados de teste e dados de treino. Na experiência preditiva, já não estamos a treinar, por isso os [dados divididos][split] podem ser removidos. Da mesma forma, o segundo módulo [do Modelo de Pontuação][score-model] e o módulo [De Modelo de Avaliação][evaluate-model] são utilizados para comparar os resultados dos dados do teste, pelo que estes módulos não são necessários na experiência preditiva. No entanto, o módulo [restante do Modelo de Pontuação][score-model] é necessário para devolver um resultado de pontuação através do serviço web.

Aqui está como o nosso exemplo fica depois de clicar em **Configurar o Serviço Web**:

![Experiência preditiva convertida](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

O trabalho realizado pela **set Up Web Service** pode ser suficiente para preparar a sua experiência para ser implementado como um serviço web. No entanto, pode querer fazer algum trabalho adicional específico da sua experiência.

#### <a name="adjust-input-and-output-modules"></a>Ajustar módulos de entrada e saída
Na sua experiência de treino, usou um conjunto de dados de treino e depois fez algum processamento para obter os dados de uma forma que o algoritmo de aprendizagem automática precisava. Se os dados que espera receber através do serviço web não precisarem deste tratamento, pode contorná-lo: conecte a saída do módulo de entrada de **serviço Web** a um módulo diferente na sua experiência. Os dados do utilizador chegarão agora ao modelo neste local.

Por exemplo, por padrão **Configurar o Serviço Web** coloca o módulo de entrada de **serviço Web** no topo do seu fluxo de dados, como mostrado na figura acima. Mas podemos posicionar manualmente a entrada do **serviço Web** para além dos módulos de processamento de dados:

![Mover a entrada do serviço web](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Os dados de entrada fornecidos através do serviço web passarão agora diretamente para o módulo 'Modelo de Pontuação' sem qualquer pré-processamento.

Da mesma forma, por **padrão, o Serviço Web de Configuração** coloca o módulo de saída do serviço Web na parte inferior do fluxo de dados. Neste exemplo, o serviço web devolverá ao utilizador a saída do módulo ['Modelo de Pontuação',][score-model] que inclui o vetor completo de dados de entrada e os resultados de pontuação.
No entanto, se preferir devolver algo diferente, então pode adicionar módulos adicionais antes do módulo de saída do **serviço Web.** 

Por exemplo, para devolver apenas os resultados de pontuação e não todo o vetor de dados de entrada, adicione um [módulo Select Columns in Dataset][select-columns] para excluir todas as colunas, exceto os resultados de pontuação. Em seguida, mova o módulo **de saída do serviço Web** para a saída do módulo ['Colunas Selecionais' no módulo Dataset.][select-columns] A experiência é assim:

![Mover a saída do serviço web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>Adicionar ou remover módulos adicionais de processamento de dados
Se houver mais módulos na sua experiência que sabe que não serão necessários durante a pontuação, estes podem ser removidos. Por exemplo, como mudamos o módulo de entrada de **serviço Web** para um ponto após os módulos de processamento de dados, podemos remover o módulo de [Dados Desaparecidos Limpos][clean-missing-data] da experiência preditiva.

A nossa experiência preditiva agora é assim:

![Remoção de módulos adicionais](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>Adicionar parâmetros de serviço web opcionais
Em alguns casos, poderá permitir que o utilizador do seu serviço web altere o comportamento dos módulos quando o serviço é acedido. *Os parâmetros do Serviço Web* permitem-lhe fazê-lo.

Um exemplo comum é a criação de um módulo [de Dados de Importação][import-data] para que o utilizador do serviço web implantado possa especificar uma fonte de dados diferente quando o serviço web é acedido. Ou configurar um módulo [de Dados de Exportação][export-data] para que um destino diferente possa ser especificado.

Pode definir parâmetros de Serviço Web e associá-los a um ou mais parâmetros do módulo, e pode especificar se são necessários ou opcionais. O utilizador do serviço web fornece valores para estes parâmetros quando o serviço é acedido, e as ações do módulo são modificadas em conformidade.

Para obter mais informações sobre quais são os parâmetros do Serviço Web e como utilizá-los, consulte [usando parâmetros de serviço web de aprendizagem de máquinas Azure][webserviceparameters].

Os passos seguintes descrevem a implementação de uma experiência preditiva como um novo serviço web. Também pode implementar a experiência como serviço web clássico.

## <a name="deploy-it-as-a-new-web-service"></a>Implemente-o como um novo serviço web

Agora que a experiência preditiva foi preparada, pode implantá-la como um novo serviço web Azure (baseado em Gestor de Recursos). Utilizando o serviço web, os utilizadores podem enviar dados para o seu modelo e o modelo irá devolver as suas previsões.

Para implementar a sua experiência preditiva, clique em **Executar** na parte inferior da tela de experiência. Uma vez terminada a experiência, clique em **Implementar o Serviço Web** e selecione Implementar o Serviço Web **[Novo]**.  Abre a página de implementação do portal de Serviço Web Machine Learning Studio (clássico).

> [!NOTE] 
> Para implementar um Novo serviço web tem de ter permissões suficientes na subscrição à qual implementa o serviço web. Para obter mais informações, [gerencie um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Portal do Serviço Web implementar página de experiência

Na página 'Implementar experiência', insira um nome para o serviço web.
Selecione um plano de preços. Se tiver um plano de preços existente, pode selecioná-lo, caso contrário terá de criar um novo plano de preços para o serviço.

1. Na queda do **Plano de Preços,** selecione um plano existente ou selecione a opção **Select new plan.**
2. Em **Plan Name,** escreva um nome que identifique o plano na sua conta.
3. Selecione um dos **Níveis de Plano Mensal**. Os níveis de plano não são padrão para os planos para a sua região padrão e o seu serviço web é implantado nessa região.

Clique **em Implementar** e a página **Quickstart** para o seu serviço web abre.

A página quickstart do serviço web dá-lhe acesso e orientação sobre as tarefas mais comuns que irá executar após a criação de um serviço web. A partir daqui, pode aceder facilmente à página Teste e Consumir.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Teste o seu novo serviço web

Para testar o seu novo serviço web, clique em **Testar o serviço web** em tarefas comuns. Na página Teste, pode testar o seu serviço web como um Serviço de Request-Response (RRS) ou um serviço de execução de lote (BES).

A página de teste RRS exibe as entradas, saídas e quaisquer parâmetros globais que definiu para a experiência. Para testar o serviço web, pode introduzir manualmente os valores apropriados para as entradas ou fornecer um ficheiro formatado de valor separado (CSV) de valor separado (CSV) que contenha os valores de teste.

Para testar a utilização de RRS, a partir do modo de visualização da lista, introduza os valores apropriados para as entradas e clique em **Test Request-Response**. Os resultados da previsão são apresentados na coluna de saída à esquerda.

![Introduza os valores apropriados para testar o seu serviço web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para testar o seu BES, clique em **Batch**. Na página de teste do Lote, clique em Navegar sob a sua entrada e selecione um ficheiro CSV contendo valores de amostra apropriados. Se não tiver um ficheiro CSV e tiver criado a sua experiência preditiva usando o Machine Learning Studio (clássico), pode descarregar o conjunto de dados para a sua experiência preditiva e usá-lo.

Para descarregar o conjunto de dados, abra o Machine Learning Studio (clássico). Abra a sua experiência preditiva e clique bem na entrada para a sua experiência. A partir do menu de contexto, selecione **conjunto de dados** e, em seguida, selecione **Download**.

![Baixe o seu conjunto de dados a partir da tela studio (clássica)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Clique **em Teste**. O estado do seu trabalho de execução de lote apresenta à direita em **Test Batch Jobs**.

![Teste o seu trabalho de execução de lote com o portal de serviço web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na página **configuração,** pode alterar a descrição, o título, atualizar a chave da conta de armazenamento e ativar os dados da amostra para o seu serviço web.

![Configure o seu serviço web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Aceda ao seu novo serviço web

Assim que implementar o seu serviço web a partir do Machine Learning Studio (clássico), pode enviar dados para o serviço e receber respostas programáticamente.

A página **Consumir** fornece toda a informação necessária para aceder ao seu serviço web. Por exemplo, a chave API é fornecida para permitir o acesso autorizado ao serviço.

Para obter mais informações sobre o acesso a um serviço web do Machine Learning Studio (clássico), consulte [Como consumir um serviço web Azure Machine Learning Studio (clássico).](consume-web-services.md)

### <a name="manage-your-new-web-service"></a>Gerencie o seu novo serviço web

Pode gerir os seus novos serviços web utilizando o portal Machine Learning Studio (clássico) web Services. A partir da [página do portal principal,](https://services.azureml.net/)clique em **Serviços Web.** A partir da página de serviços web, pode excluir ou copiar um serviço. Para monitorizar um serviço específico, clique no serviço e, em seguida, clique no **Dashboard**. Para monitorizar os trabalhos de lote associados ao serviço web, clique em **'Diário de Pedido de Lote'.**

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> Implemente o seu novo serviço web em várias regiões

Pode facilmente implementar um Novo serviço web em várias regiões sem precisar de múltiplas subscrições ou espaços de trabalho.

Os preços são específicos da região, pelo que é necessário definir um plano de faturação para cada região em que irá implementar o serviço web.

#### <a name="create-a-plan-in-another-region"></a>Criar um plano noutra região

1. Inscreva-se nos [Serviços Web de Aprendizagem automática do Microsoft Azure.](https://services.azureml.net/)
2. Clique na opção **menu Planos.**
3. Na página Planos sobre visualização, clique em **Novo**.
4. A partir do dropdown **de Subscrição,** selecione a subscrição na qual o novo plano irá residir.
5. Da **região,** selecione uma região para o novo plano. As Opções de Plano para a região selecionada serão exibidas na secção **Opções de Plano** da página.
6. A partir do **dropdown do Grupo de Recursos,** selecione um grupo de recursos para o plano. De mais informações sobre grupos de recursos, consulte [a visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
7. No **Nome do Plano** escreva o nome do plano.
8. Em **Opções de Plano,** clique no nível de faturação para o novo plano.
9. Clique em **Criar**.

#### <a name="deploy-the-web-service-to-another-region"></a>Implementar o serviço web para outra região

1. Na página Microsoft Azure Machine Learning Web Services, clique na opção do menu **Serviços Web.**
2. Selecione o Serviço Web que está a implementar para uma nova região.
3. Clique **em Copiar**.
4. No **Nome do Serviço Web,** digite um novo nome para o serviço web.
5. Na **descrição do serviço web,** escreva uma descrição para o serviço web.
6. A partir do dropdown **de Subscrição,** selecione a subscrição na qual o novo serviço web irá residir.
7. A partir do dropdown **do Grupo de Recursos,** selecione um grupo de recursos para o serviço web. De mais informações sobre grupos de recursos, consulte [a visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
8. A partir do dropdown **da Região,** selecione a região em que para implementar o serviço web.
9. A partir da entrega da **conta de Armazenamento,** selecione uma conta de armazenamento para armazenar o serviço web.
10. A partir da queda do **Plano de Preços,** selecione um plano na região que selecionou no passo 8.
11. Clique **em Copiar**.

## <a name="deploy-it-as-a-classic-web-service"></a>Implemente-o como um serviço web clássico

Agora que a experiência preditiva foi suficientemente preparada, pode implantá-la como um serviço web Classic Azure. Utilizando o serviço web, os utilizadores podem enviar dados para o seu modelo e o modelo irá devolver as suas previsões.

Para implementar a sua experiência preditiva, clique em **Executar** na parte inferior da tela de experiência e, em seguida, clique em **Implementar Serviço Web**. O serviço web está configurado e você é colocado no painel de serviço web.

![Implemente o seu serviço web a partir do Studio (clássico)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Teste o seu serviço web clássico

Pode testar o serviço web no portal Machine Learning Studio (clássico) ou no Machine Learning Studio (clássico).

Para testar o serviço web 'Resposta solicitada', clique no botão **Teste** no painel de instrumentos de serviço web. Surge um diálogo para lhe pedir os dados de entrada do serviço. Estas são as colunas esperadas pela experiência de pontuação. Introduza um conjunto de dados e, em seguida, clique **em OK**. Os resultados gerados pelo serviço web são apresentados na parte inferior do painel de instrumentos.

Pode clicar no link de pré-visualização do **Teste** para testar o seu serviço no portal Azure Machine Learning Studio (clássico) dos Serviços Web, como mostrado anteriormente na secção New web service.

Para testar o Serviço de Execução de Lotes, clique no link de pré-visualização **do teste** . Na página de teste do Lote, clique em Navegar sob a sua entrada e selecione um ficheiro CSV contendo valores de amostra apropriados. Se não tiver um ficheiro CSV e tiver criado a sua experiência preditiva usando o Machine Learning Studio (clássico), pode descarregar o conjunto de dados para a sua experiência preditiva e usá-lo.

![Testar o serviço Web](./media/publish-a-machine-learning-web-service/figure-3.png)

Na página **CONFIGURAÇÃO,** pode alterar o nome de visualização do serviço e dar-lhe uma descrição. O nome e descrição são apresentados no [portal Azure](https://portal.azure.com/) onde gere os seus serviços web.

Pode fornecer uma descrição dos seus dados de entrada, dados de saída e parâmetros de serviço web, introduzindo uma cadeia para cada coluna sob **o ESQUEMA DE ENTRADA,** **OUTPUT SCHEMA,** e **O PARÂMETRO DO Serviço Web**. Estas descrições são utilizadas na documentação do código de amostra fornecida para o serviço web.

Pode permitir o registo de registos diagnosticar quaisquer falhas que esteja a ver quando o seu serviço web é acedido. Para obter mais informações, consulte [Ativar o registo de serviços web do Machine Learning Studio (clássico).](web-services-logging.md)

![Ativar o registo no portal de serviços web](./media/publish-a-machine-learning-web-service/figure-4.png)

Também pode configurar os pontos finais do serviço web no portal Azure Machine Learning Web Services semelhante ao procedimento mostrado anteriormente na secção New web service. As opções são diferentes, pode adicionar ou alterar a descrição do serviço, ativar o registo e ativar dados de amostra para testes.

### <a name="access-your-classic-web-service"></a>Aceda ao seu serviço web Clássico

Assim que implementar o seu serviço web a partir do Azure Machine Learning Studio (clássico), pode enviar dados para o serviço e receber respostas programáticamente.

O painel de instrumentos fornece toda a informação necessária para aceder ao seu serviço web. Por exemplo, a chave API é fornecida para permitir o acesso autorizado ao serviço, e as páginas de ajuda da API são fornecidas para ajudá-lo a começar a escrever o seu código.

Para obter mais informações sobre o acesso a um serviço web do Machine Learning Studio (clássico), consulte [Como consumir um serviço web Azure Machine Learning Studio (clássico).](consume-web-services.md)

### <a name="manage-your-classic-web-service"></a>Gerencie o seu serviço web Clássico

Existem várias ações que pode realizar para monitorizar um serviço web. Pode atualizá-lo e eliminá-lo. Também pode adicionar pontos finais adicionais a um serviço web Clássico, além do ponto final padrão que é criado quando o implementa.

Para obter mais informações, consulte [Gerir um espaço de trabalho do Azure Machine Learning Studio (clássico)](manage-workspace.md) e gerir um serviço web utilizando o portal [Azure Machine Learning Studio (clássico).](manage-new-webservice.md)

## <a name="update-the-web-service"></a>Atualizar o serviço web
Pode efecizar alterações no seu serviço web, como atualizar o modelo com dados de treino adicionais, e implementá-lo novamente, sobreescrita do serviço web original.

Para atualizar o serviço web, abra a experiência preditiva original que usou para implementar o serviço web e fazer uma cópia editável clicando **em SAVE AS**. Faça as alterações e, em seguida, clique em **Implementar Serviço Web**.

Como já implementou esta experiência antes, é-lhe perguntado se pretende substituir (Classic Web Service) ou atualizar (Novo serviço web) o serviço existente. Clicar **em SIM** ou **Atualização** para o serviço web existente e implementa a nova experiência preditiva é implementada no seu lugar.

> [!NOTE]
> Se esteiu alterações de configuração no serviço web original, por exemplo, introduzindo um novo nome de visualização ou descrição, terá de introduzir novamente esses valores.

Uma opção para atualizar o seu serviço web é reforçá-lo programaticamente. Para obter mais informações, consulte [os modelos do Retrain Machine Learning Studio (clássico) programáticamente.](./retrain-machine-learning-model.md)

## <a name="next-steps"></a>Passos seguintes

* Para obter mais detalhes técnicos sobre como funciona a implementação, consulte [como um modelo de Machine Learning Studio (clássico) progride de uma experiência para um serviço Web operacionalizado.](model-progression-experiment-to-web-service.md)

* Para obter detalhes sobre como preparar o seu modelo, consulte [Como preparar o seu modelo para implementação no Azure Machine Learning Studio (clássico)](deploy-a-machine-learning-web-service.md).

* Existem várias formas de utilizar a API REST e aceder ao serviço Web. Veja [como consumir um serviço web Azure Machine Learning Studio (clássico).](consume-web-services.md)

<!-- internal links -->
[Criar uma experiência de treino]: #create-a-training-experiment
[Converta-o numa experiência preditiva]: #convert-the-training-experiment-to-a-predictive-experiment
[Novo serviço web]: #deploy-it-as-a-new-web-service
[Serviço web clássico]: #deploy-it-as-a-classic-web-service
[Novo]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data
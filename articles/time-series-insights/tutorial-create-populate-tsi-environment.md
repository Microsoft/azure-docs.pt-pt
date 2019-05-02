---
title: 'Tutorial: Criar um ambiente do Azure Time Series Insights | Microsoft Docs'
description: Saiba como criar um ambiente do Time Series Insights, preenchido com dados de dispositivos simulados.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: c974e011e6f101eab617a370b24306f80c249b90
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718416"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Tutorial: Criar um ambiente do Azure Time Series Insights

Este tutorial vai ajudá-lo durante o processo de criação de um ambiente do Time Series Insights (TSI), preenchido com dados de dispositivos simulados. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um ambiente TSI 
> * Criar uma solução de simulação de dispositivos com um Hub IoT
> * Ligar o ambiente TSI ao hub IoT
> * Executar uma simulação de dispositivos para transmitir dados para o ambiente TSI
> * Verificar os dados de telemetria simulados

## <a name="video"></a>Vídeo

### <a name="in-this-video-we-show-you-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-that-can-be-used-to-get-started-with-time-series-insights-br"></a>Neste vídeo, vamos mostrar como utilizar um Acelerador de Soluções de IoT do Azure para gerar dados que podem ser utilizados para começar a utilizar o Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

A conta de início de sessão do Azure também tem de ser um membro da função Proprietário da subscrição. Para obter detalhes, veja [Gerir o acesso através do RBAC e do portal do Azure](/azure/role-based-access-control/role-assignments-portal)

## <a name="overview"></a>Descrição geral

O ambiente TSI é onde os dados do dispositivo são recolhidos e armazenados. Depois de armazenados no ambiente TSI, pode utilizar o [Explorador do TSI ](time-series-quickstart.md) e a [API de Consulta do TSI](/rest/api/time-series-insights/ga-query-api) para consultar e analisar os dados.

Tal como acontece com todos os dispositivos, simulados ou físicos, o Hub IoT é o ponto de ligação utilizado pelos dispositivos para ligar e transmitir dados para a cloud do Azure de forma segura. Tal como foi explicado na [Descrição geral do TSI](time-series-insights-overview.md), o Hub IoT também funciona como uma origem de evento, para transmitir fluxos de dados para o ambiente do TSI. 

Este tutorial utiliza também um [acelerador de soluções IoT](/azure/iot-accelerators/), para gerar e transmitir fluxos de dados de telemetria de exemplo para o Hub IoT. Os aceleradores de soluções IoT fornecem soluções pré-configuradas de nível empresarial, que lhe permitem acelerar o desenvolvimento de soluções IoT personalizadas. 

## <a name="create-a-tsi-environment"></a>Criar um ambiente TSI

Comece por criar um ambiente do TSI na sua subscrição do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta de subscrição do Azure.  
2. Selecione **+ Criar um recurso** no canto superior esquerdo.  
3. Selecione a categoria **Internet das Coisas** e, em seguida, **Time Series Insights**.  
   
   [![Selecionar o recurso de ambiente do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. Na página **Ambiente do Time Series Insights**, preencha os parâmetros necessários:
   
   Parâmetro|Descrição
   ---|---
   **Nome do ambiente** | Escolha um nome exclusivo para o ambiente do TSI. O nome é utilizado pelo Explorador do TSI e pela API de Consulta.
   **Subscrição** | As subscrições são contentores de recursos do Azure. Escolha a subscrição onde pretende criar o ambiente do TSI.
   **Grupo de recursos** | Um grupo de recursos é um contentor de recursos do Azure. Escolha um grupo de recursos existente ou crie um novo, para o recurso de ambiente do TSI.
   **Localização** | Escolha a região do centro de dados para o ambiente do TSI. Para evitar custos de largura de banda adicionais e latência, é melhor manter o ambiente do TSI na mesma região que os outros recursos IoT.
   **SKUs de Preços** | Escolha o débito necessário. Para o custo mais baixo e a capacidade de arranque, selecione S1.
   **Capacidade** | A capacidade é o multiplicador aplicado à taxa de entrada, à capacidade de armazenamento e ao custo associado ao SKU selecionado.  Pode alterar a capacidade de um ambiente após a criação. Para o custo mais baixo, selecione uma capacidade de 1. 

   Quando tiver terminado, clique em **Criar** para iniciar o processo de aprovisionamento.

   ![Criar o recurso de ambiente do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. Pode consultar o painel **Notificações** para monitorizar a conclusão da implementação, que deve demorar menos de um minuto:  

   ![Implementação do ambiente do Time Series Insights concluída com êxito](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Em seguida, crie a solução de simulação de dispositivos, o que irá gerar dados de teste para preencher o ambiente do TSI:

1. Num separador/janela separado, aceda a https://www.azureiotsolutions.com, inicie sessão com a mesma conta de subscrição do Azure e selecione o acelerador “Simulação de Dispositivos”:

   ![Executar o acelerador de Simulação de Dispositivos](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. Introduza os parâmetros necessários na página **Criar solução de Simulação de Dispositivos**:

   Parâmetro|Descrição
   ---|---
   **Nome da solução** | Um valor exclusivo, utilizado para a criação de um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Subscrição** | Especifique a mesma subscrição utilizada para a criação do ambiente do TSI, na secção anterior.
   **Região** | Especifique a mesma região utilizada para a criação do ambiente do TSI, na secção anterior. 
   **Implementar Recursos do Azure opcionais** | Deixe a opção **Hub IoT** marcada, dado que os dispositivos simulados irão utilizá-la para ligar/transmitir dados.

   Quando terminar, clique em **Criar solução** para aprovisionar os recursos do Azure da solução (este processo demora cerca de 6 a 7 minutos):

   ![Aprovisionar a solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. Quando o aprovisionamento estiver concluído, o texto por cima da nova solução mudará de “A aprovisionar...” para “Pronto”:

   >[!IMPORTANT]
   > Não clique ainda no botão **Iniciar**! Mas mantenha esta página Web aberta, pois regressará à ela mais tarde.

   ![Aprovisionamento da solução de simulação de dispositivos concluída](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. Agora, volte ao portal do Azure e inspecione os recursos que acabou de criar na subscrição. No portal da página **Grupos de recursos**, verá um novo grupo de recursos que foi criado com o **Nome da solução** fornecida no último passo. Repare também em todos os recursos criados para suportar a solução de simulação de dispositivos:

   [![Recursos da solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Ligar o ambiente TSI ao hub IoT 

Até aqui, aprendeu como criar dois conjuntos de recursos, cada um no seu próprio grupo de recursos: 
- Um ambiente do TSI vazio 
- Recursos da solução de simulação de dispositivos, incluindo um hub IoT, gerado por um acelerador de soluções

Lembre-se que os dispositivos simulados precisam de estar ligados a um hub IoT para transmitirem dados dos dispositivos. Para transmitir dados para o ambiente do TSI, terá de alterar algumas configuração no hub IoT e no ambiente do TSI. 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Configuração do hub IoT: definir um grupo de consumidores

O hub IoT fornece vários pontos finais para partilhar a funcionalidade com outros atores. O ponto final “Eventos” fornece uma forma para outras aplicações consumirem dados, dado que é transmitida para uma instância do hub IoT. Em particular, os “grupos de consumidores” fornecem um mecanismo para as aplicações escutarem e extraírem dados do Hub IoT. 

Em seguida, define uma nova propriedade de “grupo de consumidores”, no ponto final de “Eventos” do hub IoT da solução de simulação de dispositivos. 

1. No portal do Azure, aceda à página **Descrição geral** do grupo de recursos que criou para a solução de simulação de dispositivos e, em seguida, selecione o recurso do Hub IoT:

   [![Grupo de recursos da solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Anote também o **Nome** do recurso do Hub IoT gerado para a solução, pois precisará dele posteriormente.

2. Desloque-se para baixo e selecione a página **Pontos finais** e, em seguida, selecione o ponto final **Eventos**. No página **Propriedades** do ponto final, introduza um nome exclusivo para o ponto final, sob o grupo de consumidores “$Default”, e clique em **Guardar**:

   [![Pontos finais do hub IoT da solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>Configuração do ambiente do TSI: definir uma origem de evento

Agora, ligue o novo ponto final de evento “grupo de consumidores” do hub IoT ao ambiente do TSI, como uma “origem de evento”.

1. Aceda à página **Descrição geral** do grupo de recursos que criou para o ambiente do TSI e, em seguida, selecione o ambiente do TSI:

   [![Ambiente e grupo de recursos do ambiente do TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. Na página do ambiente do TSI, selecione **Origens de Evento** e, em seguida, clique em **+ Adicionar**:

   ![Descrição geral do ambiente do TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. Introduza os parâmetros necessários na página **Nova origem de evento**: 

   Parâmetro|Descrição
   ---|---
   **Nome da origem de evento** | Requer um valor exclusivo, utilizado para o nome da origem de evento.
   **Origem** | Selecione “Hub IoT”.
   **Opção de importação** | Utilize a predefinição “Utilizar hub IoT das subscrições disponíveis”. Esta opção permitirá que a próxima lista pendente seja preenchida com as subscrições disponíveis.
   **Subscrição** | Selecione a mesma subscrição onde criou o ambiente do TSI e os recursos de Simulação de Dispositivos.
   **Nome do hub IoT** | Deve ser predefinido para o nome do hub IoT que anotou anteriormente. Caso contrário, selecione o hub IoT correto.
   **Nome da política do hub IoT** | Deixe com o valor predefinido de “iothubowner”.
   **Grupo de consumidores do hut IoT** | Deve ser predefinido para o nome do grupo de consumidores do hub IoT que criou anteriormente. Caso contrário, selecione o nome do grupo de consumidores correto. 
   **Formato de serialização de eventos** | Deixe com o valor predefinido de “JSON”.
   **Nome da propriedade Carimbo de data/hora** | Especificar como “carimbo de data/hora”.

   Quando terminar, clique em **Criar** para adicionar a origem de evento. Quando regressar à página **Descrição geral** do grupo de recursos, juntamente com o recurso do ambiente do TSI, verá um novo recurso “Origem de evento do Time Series Insights”.

   ![Nova origem de evento do ambiente do TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>Executar uma simulação de dispositivos para transmitir dados para o ambiente TSI

Agora que todo o trabalho de configuração está concluído, está na altura de preencher o ambiente do TSI com dados de exemplo dos dispositivos simulados.

Talvez se lembre que, na secção [Criar uma simulação de dispositivos](#create-a-device-simulation), vários recursos do Azure foram criados pelo acelerador para suportar a solução. Juntamente com o hub IoT, abordado anteriormente, foi gerada uma aplicação Web do Serviço de Aplicações do Azure para criar e transmitir telemetria dos dispositivos simulados.

1. Volte ao [Dashboard dos aceleradores de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard). Inicie sessão novamente se necessário, com a mesma conta do Azure que tem estado a utilizar neste tutorial. Agora, pode clicar no botão **Iniciar** na solução “Simulação de Dispositivos”:

     ![Dashboard dos aceleradores de soluções](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. A aplicação Web de simulação de dispositivos será iniciada neste momento e poderá demorar vários segundos após o carregamento inicial. Também lhe pedirá consentimento, para dar permissão à aplicação Web para “Iniciar sessão e ler o seu perfil”. Esta permissão permite que a aplicação obtenha as informações do perfil de utilizador necessárias para suportar o funcionamento da aplicação:

     ![Consentimento da aplicação Web de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. Uma vez carregada a página **Configuração de simulação**, introduza os parâmetros necessários: 

   Parâmetro|Descrição
   ---|---
   **Hub IoT de destino** | Selecione “Utilizar Hub IoT pré-aprovisionado”
   **Modelo do dispositivo** | Selecione “Chiller”.
   **Número de dispositivos**  | Em **Quantidade**, introduza 1000.
   **Frequência da telemetria** | Introduza 10 segundos.
   **Duração da simulação** | Selecione **Terminar em:** e introduza 5 minutos.

   Quando terminar, clique em **Iniciar Simulação**. A simulação será executada durante um total de 5 minutos e vai gerar dados a partir de 1000 dispositivos simulados, de 10 em 10 segundos.  

   ![Configuração da simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. Enquanto a simulação é executada, verá a atualização dos campos **Total de mensagens** e **Mensagens por segundo**, aproximadamente a cada 10 segundos. A simulação terminará depois de aproximadamente 5 minutos e poderá regressar a **Configuração da simulação**.

   ![Execução da simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>Verificar os dados de telemetria

Nesta secção final, vai verificar se os dados de telemetria foram gerados e armazenados no ambiente do TSI. Para verificar os dados, pode utilizar o explorador do Time Series Insights, que serve para consultar e analisar os dados de telemetria.

1. Aceda à página **Descrição geral** do grupo de recursos do ambiente do TSI e, em seguida, selecione novamente o ambiente do TSI:

   [![Ambiente e grupo de recursos do ambiente do TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. Na página **Descrição geral** do ambiente do TSI, clique em **URL do explorador do Time Series Insights** para abrir o explorador:

   [![Explorador do TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. O explorador do TSI será carregado e autenticado com a sua conta no portal do Azure. Na vista inicial, pode ver na área de gráfico que o ambiente do TSI foi realmente preenchido com os dados telemétricos simulados. Para filtrar um intervalo de tempo mais curto, selecione a lista pendente no canto superior esquerdo. Introduza um intervalo de tempo suficientemente grande para abranger a duração da simulação de dispositivos. Em seguida, clique na lupa de pesquisa:

   [![Filtro do intervalo de tempo do explorador do TSI](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. Encurtar o intervalo de tempo permite que o gráfico aplique zoom nas rajadas da transferência de dados distintas, para o hub IoT e para o ambiente do TSI. Repare também no texto **Transmissão concluída**, no canto superior direito, que mostra o número total de eventos encontrado. Também pode arrastar o controlo de deslize **Tamanho do intervalo** para controlar a granularidade de desenho no gráfico:

   [![Vista filtrada do intervalo de tempo do explorador do TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. Por último, também pode clicar com o botão esquerdo do rato numa região para filtrar um intervalo e, em seguida, clicar com o botão direito do rato e utilizar **Explorar eventos** para apresentar os detalhes do evento na vista de tabela **Eventos**:

   [![Eventos e vista filtrada do intervalo de tempo do explorador do TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução, para suportar a solução de simulação de dispositivos e o ambiente do TSI. Se quiser abandonar e/ou retardar a conclusão desta série de tutoriais, recomendamos eliminar todos os recursos para evitar incorrer em custos desnecessários. 

No menu esquerdo do portal do Azure:

1. Clique no ícone **Grupos de recursos** e, em seguida, selecione o grupo de recursos que criou para o Ambiente do TSI. Na parte superior da página, clique em **Eliminar grupo de recursos**, escreva o nome do grupo de recurso e, em seguida, clique em **Eliminar**. 
2. Clique no ícone **Grupos de recursos** e, em seguida, selecione o grupo de recursos que foi criado pelo acelerador de soluções da simulação de dispositivos. Na parte superior da página, clique em **Eliminar grupo de recursos**, escreva o nome do grupo de recurso e, em seguida, clique em **Eliminar**. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um ambiente TSI 
> * Criar uma solução de simulação de dispositivos com um Hub IoT
> * Ligar o ambiente TSI ao hub IoT
> * Executar uma simulação de dispositivos para transmitir dados para o ambiente TSI
> * Verificar os dados de telemetria simulados

Agora que sabe como criar o seu próprio ambiente TSI, aprenda a criar uma aplicação Web que consome dados a partir de um ambiente TSI:

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única do Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)



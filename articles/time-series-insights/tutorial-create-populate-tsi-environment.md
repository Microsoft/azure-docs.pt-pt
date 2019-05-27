---
title: 'Tutorial: Criar um ambiente do Azure Time Series Insights | Microsoft Docs'
description: Saiba como criar um ambiente de Time Series Insights, que é preenchido com dados a partir de dispositivos simulados.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: b8b46db043113f29f559ad44855d19f0d6ca73c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244165"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Tutorial: Criar um ambiente do Azure Time Series Insights

Este tutorial orienta-o ao longo do processo de criação de um ambiente do Azure Time Series Insights que é preenchido com dados a partir de dispositivos simulados. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um ambiente do Time Series Insights.
> * Crie uma solução de simulação de dispositivo que contém um hub IoT.
> * Ligar o ambiente do Time Series Insights para o hub IoT.
> * Execute uma simulação de dispositivo para transmitir dados para o ambiente do Time Series Insights.
> * Verifique se os dados de telemetria simulada.

## <a name="video"></a>Vídeo

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Aprenda a usar um acelerador de solução de IoT do Azure para gerar dados e começar a utilizar com o Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Sua conta de início de sessão do Azure também tem de ser um membro da subscrição do **proprietário** função. Para obter mais informações, consulte [gerir o acesso com controlo de acesso baseado em funções e o portal do Azure](/azure/role-based-access-control/role-assignments-portal).

## <a name="overview"></a>Descrição geral

O ambiente do Time Series Insights é onde os dados de dispositivo são recolhidos e armazenados. Depois dos dados são armazenados, o [Explorador do Azure Time Series Insights](time-series-quickstart.md) e [API de consulta do Time Series Insights](/rest/api/time-series-insights/ga-query-api) pode ser usado para consultar e analisar os dados. O IoT Hub do Azure é o ponto de ligação utilizado por todos os dispositivos (simulados ou físicos) para se ligar e transmitir dados para a cloud do Azure de forma segura. O [descrição geral do Time Series Insights](time-series-insights-overview.md) notas a que o IoT Hub do Azure também serve como uma origem de evento para transmissão em fluxo de dados num ambiente do Time Series Insights. Este tutorial utiliza uma [acelerador de solução de IoT](/azure/iot-accelerators/) para gerar e transmitir dados de telemetria de exemplo para o IoT Hub.

>[!TIP]
> Aceleradores de solução de IoT fornecem soluções pré-configuradas de nível empresarial que pode utilizar para acelerar o desenvolvimento de soluções de IoT personalizadas.

## <a name="create-an-environment"></a>Criar um ambiente

Primeiro, crie um ambiente do Time Series Insights na sua subscrição do Azure.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) com a sua conta de subscrição do Azure. 
1. Selecione **+ Criar um recurso** no canto superior esquerdo. 
1. Selecione o **Internet das coisas** categoria e, em seguida, selecione **Time Series Insights**. 

   [![Selecionar o recurso de ambiente do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Sobre o **ambiente do Time Series Insights** página, preencha os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Nome do ambiente** | Escolha um nome exclusivo para o ambiente do Time Series Insights. Os nomes são utilizados pelo Explorador do Time Series Insights e as APIs de consulta.
   **Subscrição** | As subscrições são contentores de recursos do Azure. Escolha uma subscrição para criar o ambiente do Time Series Insights.
   **Grupo de recursos** | Um grupo de recursos é um contentor de recursos do Azure. Escolha um grupo de recursos existente ou crie um novo para o recurso de ambiente do Time Series Insights.
   **Localização** | Escolha uma região do Datacenter para o seu ambiente do Time Series Insights. Para evitar os custos de largura de banda adicional e latência, manter o ambiente do Time Series Insights na mesma região que outros recursos de IoT.
   **SKUs de Preços** | Escolha o débito necessário. Custo mais baixo e a capacidade de arranque, selecione `S1`.
   **Capacidade** | A capacidade é o multiplicador aplicado à taxa de entrada, à capacidade de armazenamento e ao custo associado ao SKU selecionado. Pode alterar a capacidade após a criação. O custo mais baixo, selecione uma capacidade de 1.

   Quando terminar, selecione **criar** para iniciar o processo de aprovisionamento.

   [![Criar um recurso de ambiente do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Verifique os **notificações** painel para monitorizar a conclusão da implantação. 

   [![Implementação do ambiente do Time Series Insights foi concluída com êxito](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Em seguida, crie a solução de simulação do dispositivo, que gera dados de teste para preencher o seu ambiente do Time Series Insights.

1. Num separador ou janela separada, aceda a [azureiotsolutions.com](https://www.azureiotsolutions.com). Inicie sessão com a mesma conta de subscrição do Azure e selecione o **simulação do dispositivo** accelerator.

   [![Execute o acelerador de simulação do dispositivo](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Introduza os parâmetros necessários **solução de simulação do dispositivo criar** página.

   Parâmetro|Descrição
   ---|---
   **Nome da solução** | Este valor exclusivo é utilizado para criar um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Subscrição** | Especifique a mesma subscrição que foi utilizada para criar o seu ambiente do Time Series Insights na secção anterior.
   **Região** | Especifique a mesma região que utilizou para criar o seu ambiente do Time Series Insights na secção anterior.
   **Implementar Recursos do Azure opcionais** | Deixe **IoT Hub** marcada. Os dispositivos simulados utilizá-lo para ligar ou transmitir dados em fluxo.

   Quando tiver terminado, selecione **criar solução** aprovisionar de recursos do Azure a solução. Poderá demorar 6 a 7 minutos para concluir este processo.

   [![Aprovisionar a solução de simulação do dispositivo](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Após concluir o aprovisionamento, o texto acima a nova solução é alterado de **aprovisionamento** ao **pronto**.

   >[!IMPORTANT]
   > Não selecione **inicie** ainda! Essa página da web mantenha aberto porque irá retornar a ela mais tarde.

   [![Solução de simulação de dispositivo de aprovisionamento concluída](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Agora, regresse ao portal do Azure e inspecionar os recursos criados recentemente na sua subscrição. No portal **grupos de recursos** página, tenha em atenção que um novo grupo de recursos foi criado utilizando o **nome da solução** fornecido no último passo. Observe também todos os recursos que foram criados para suportar a solução de simulação do dispositivo.

   [![Recursos da solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>Ligar o ambiente para o hub IoT

Até aqui, aprendeu como criar dois conjuntos de recursos, cada um no seu próprio grupo de recursos:

- Um ambiente do Time Series Insights vazio.
- Recursos de solução de simulação do dispositivo, que inclui um hub IoT, gerado por um acelerador de solução.

Lembre-se que os dispositivos simulados precisam de estar ligados a um hub IoT para transmitirem dados dos dispositivos. Para os dados de fluxo no ambiente do Time Series Insights, terá de efetuar alterações de configuração para o seu hub IoT e o ambiente do Time Series Insights.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Configuração do hub IoT: Definir um grupo de consumidores

O IoT Hub fornece vários pontos de extremidade para compartilhar a funcionalidade para outros atores. O ponto final "Eventos" fornece uma forma para outras aplicações para consumir dados, porque ele é transmitido para uma instância do hub IoT. Especificamente, "grupos de consumidores" fornecem um mecanismo para aplicações escutar e extrair dados do IoT hub.

Em seguida, defina uma nova **grupo de consumidores** propriedade no hub de IoT da solução de simulação de dispositivo **ponto final eventos**.

1. No portal do Azure, vá para o **descrição geral** página do grupo de recursos que criou para a solução de simulação do dispositivo. Selecione o recurso do hub IoT.

   [![Grupo de recursos da solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Anote o **nome** do recurso do IoT Hub gerado para a solução. Vai fazer referência a ele mais tarde.

1. Role para baixo e selecione o **pontos de extremidade** página e, em seguida, selecione a **eventos** ponto final. No ponto final **propriedades** página, introduza um nome exclusivo para o ponto final sob o grupo de consumidores "$Default". Selecione **Guardar**.

   [![Pontos finais do hub IoT da solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>Configuração do ambiente: definir uma origem de evento

Agora, ligue o novo hub IoT **grupo de consumidores** ponto final de eventos no ambiente do Time Series Insights que um **origem do evento**.

1. Vá para o **descrição geral** página do grupo de recursos que criou para o ambiente do Time Series Insights. Selecione o ambiente do Time Series Insights.

   [![Grupo de recursos de ambiente do Time Series Insights e o ambiente](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Na página de ambiente do Time Series Insights, selecione **origens de eventos**. Em seguida, selecione **+ adicionar**.

   [![Descrição geral do ambiente do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Introduza os parâmetros necessários **nova origem de evento** página.

   Parâmetro|Descrição
   ---|---
   **Nome da origem de evento** | Requer um valor exclusivo, utilizado para o nome da origem de evento.
   **Origem** | Selecione **IoT Hub**.
   **Opção de importação** | Selecione a predefinição `Use IoT hub from available subscriptions`. Esta opção faz com que o seguinte na lista pendente deve ser preenchida com as subscrições disponíveis.
   **Subscrição** | Selecione a mesma subscrição que criou o ambiente do Time Series Insights e recursos de simulação do dispositivo.
   **Nome do hub IoT** | Deve ser predefinido para o nome do hub IoT que anotou anteriormente. Caso contrário, selecione o hub IoT correto.
   **Nome da política do hub IoT** | Selecione **iothubowner**.
   **Grupo de consumidores do hut IoT** | Deve ser predefinido para o nome do grupo de consumidores do hub IoT que criou anteriormente. Caso contrário, selecione o nome do grupo de consumidores correto.
   **Formato de serialização de eventos** | Deixe como o valor padrão de `JSON`.
   **Nome da propriedade Carimbo de data/hora** | Especificar como `timestamp`.

   Quando tiver terminado, selecione **criar** para adicionar a origem do evento. Quando regressar ao grupo de recursos **descrição geral** página, juntamente com o seu recurso de ambiente do Time Series Insights, verá um novo recurso de "Origem de eventos do Time Series Insights".

   [![Time Series Insights ambiente nova fonte de eventos](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Executar a simulação de dispositivo para transmitir dados

Agora que todo o trabalho de configuração está concluída, chegou a hora para preencher o ambiente do Time Series Insights com dados de exemplo dos dispositivos simulados.

Pode se lembrar do [crie uma seção de simulação de dispositivo](#create-a-device-simulation), vários recursos do Azure foram criados pelo acelerador para suportar a solução. Juntamente com o hub IoT, abordado anteriormente, foi gerada uma aplicação Web do Serviço de Aplicações do Azure para criar e transmitir telemetria dos dispositivos simulados.

1. Volte ao [Dashboard dos aceleradores de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard). Inicie sessão novamente, se necessário, com a mesma conta do Azure que tem utilizado neste tutorial. Agora, pode selecionar **inicie** sob a sua solução de "Simulação do dispositivo".

     [![Dashboard de Aceleradores de solução](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. A dispositivo simulação web aplicação é iniciada a este ponto e ele poderá demorar vários segundos após o carregamento inicial. Também será avisado para consentimento conceder ao aplicativo web, o "iniciar sessão e ler o seu perfil" permissão. Esta permissão permite que o aplicativo recupere as informações de perfil de utilizador necessárias para suportar o funcionamento da aplicação.

     [![Consentimento de aplicativo de web de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Depois do **programa de configuração de simulação** carregamentos de página, introduza os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Hub IoT de destino** | Selecione **utilização previamente aprovisionadas IoT Hub**.
   **Modelo do dispositivo** | Selecione **Chiller**.
   **Número de dispositivos**  | Introduza `1000` sob **quantidade**.
   **Frequência da telemetria** | Introduza `10` segundos.
   **Duração da simulação** | Selecione **terminar:** e introduza `5` minutos.

   Quando tiver terminado, selecione **iniciar simulação**. A simulação é executada para um total de 5 minutos. Gera dados de 1000 dispositivos simulados cada 10 segundos. 

   [![Configuração de simulação do dispositivo](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Enquanto a simulação é executada, tenha em atenção que o **Total de mensagens** e **mensagens por segundo** campos atualizar, aproximadamente a cada 10 segundos. A simulação termina depois de aproximadamente 5 minutos e devolve-lhe **programa de configuração de simulação**.

   [![Simulação de dispositivo em execução](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Verificar os dados de telemetria

Nesta seção final, verifique se os dados de telemetria foi gerados e armazenados no ambiente de Time Series Insights. Para verificar os dados, pode utilizar o explorador do Time Series Insights, que serve para consultar e analisar os dados de telemetria.

1. Voltar ao grupo de recursos do ambiente de Time Series Insights **descrição geral** página. Selecione o ambiente do Time Series Insights.

   [![Grupo de recursos de ambiente do Time Series Insights e o ambiente](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. No ambiente do Time Series Insights **descrição geral** página, selecione a **URL de Explorador do Time Series Insights** para abrir o Explorador do Time Series Insights.

   [![Explorador do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. O Explorador do Time Series Insights carrega e autentica utilizando a sua conta do portal do Azure. Após a exibição inicial, pode ver na área de gráfico que o ambiente do Time Series Insights foi preenchido com dados de telemetria simulada. Para filtrar um intervalo mais estreito de tempo, selecione a menu pendente no canto superior esquerdo. Introduza um intervalo de tempo grande o suficiente para abranger a duração da simulação de dispositivo. Em seguida, selecione a Lupa de pesquisa.

   [![Filtro do intervalo de tempo de explorer do tempo Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Restrição o intervalo de tempo, permite que o gráfico para ampliar para a expansão distinta de transferência de dados para o hub IoT e o ambiente do Time Series Insights. Observe também o **transmissão em fluxo concluída** texto no canto superior direito, que mostra o número total de eventos encontrados. Também pode arrastar o **tamanho do intervalo** controlo de deslize para controlar a granularidade de desenho no gráfico.

   [![Vista filtrada de intervalo de tempo de Explorador do Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Por último, pode também left-click uma região para filtrar um intervalo. Em seguida, clique com botão direito e utilizar **explorar eventos** para mostrar detalhes do evento na tabela **eventos** vista.

   [![Intervalo de tempo de Explorador do Time Series Insights filtrado vista e eventos](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução para suportar a solução de simulação de dispositivo e ambiente de Time Series Insights. Se quiser abandonar ou adiar o trabalho nesta série de tutoriais, elimine todos os recursos para evitar incorrer em custos desnecessários.

No menu à esquerda no portal do Azure:

1. Selecione o **grupos de recursos** ícone. Em seguida, selecione o grupo de recursos que criou para o ambiente do Time Series Insights. Na parte superior da página, selecione **eliminar grupo de recursos**, introduza o nome do grupo de recursos e selecione **eliminar**.

1. Selecione o **grupos de recursos** ícone. Em seguida, selecione o grupo de recursos que foi criado com o solution accelerator de simulação do dispositivo. Na parte superior da página, selecione **eliminar grupo de recursos**, introduza o nome do grupo de recursos e selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Crie um ambiente do Time Series Insights.
> * Crie uma solução de simulação de dispositivo que contém um hub IoT.
> * Ligar o ambiente do Time Series Insights para o hub IoT.
> * Execute uma simulação de dispositivo para transmitir dados para o ambiente do Time Series Insights.
> * Verifique se os dados de telemetria simulada.

Agora que sabe como criar seu próprio ambiente do Time Series Insights, saiba como criar uma aplicação web que consome dados de um ambiente do Time Series Insights:

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única do Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)

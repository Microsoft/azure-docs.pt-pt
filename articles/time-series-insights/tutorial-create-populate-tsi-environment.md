---
title: 'Tutorial: Criar um ambiente do Azure Time Series Insights | Microsoft Docs'
description: Saiba como criar um ambiente do Time Series Insights, preenchido com dados de dispositivos simulados.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 42a7ba0c66bd603b19d60c7b3407ae5ca80db28e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209936"
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

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Saiba como usar um acelerador de solução de IoT do Azure para gerar dados e começar a utilizar com o Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Sua conta de início de sessão do Azure também tem de ser um membro da subscrição do **proprietário** função. Ver [gerir o acesso com RBAC e o portal do Azure](/azure/role-based-access-control/role-assignments-portal) para obter mais detalhes.

## <a name="overview"></a>Descrição geral

O ambiente TSI é onde os dados do dispositivo são recolhidos e armazenados. Uma vez armazenados no ambiente do TSI, o [Explorador de TSI](time-series-quickstart.md) e [API de consulta do TSI](/rest/api/time-series-insights/ga-query-api) pode ser usado para consultar e analisar os dados. IoT Hub é o ponto de ligação utilizado por todos os dispositivos (simulados ou físicos) para se ligar e transmitir dados para a cloud do Azure de forma segura. O [descrição geral do TSI](time-series-insights-overview.md) notas a que o IoT Hub do Azure também serve como uma origem de evento para transmissão em fluxo de dados num ambiente do TSI. Este tutorial utiliza uma [acelerador de solução de IoT](/azure/iot-accelerators/) para gerar e transmitir dados de telemetria de exemplo para o IoT Hub.

>[!TIP]
> Aceleradores de solução de IoT fornecem soluções de nível empresarial pré-configuradas que lhe permite acelerar o desenvolvimento de soluções de IoT personalizadas.

## <a name="create-a-tsi-environment"></a>Criar um ambiente TSI

Comece por criar um ambiente do TSI na sua subscrição do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta de subscrição do Azure.  
1. Selecione **+ Criar um recurso** no canto superior esquerdo.  
1. Selecione a categoria **Internet das Coisas** e, em seguida, **Time Series Insights**.  

   [![Selecionar o recurso de ambiente do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na página **Ambiente do Time Series Insights**, preencha os parâmetros necessários:

   Parâmetro|Descrição
   ---|---
   **Nome do ambiente** | Escolha um nome exclusivo para o ambiente do TSI. O nome do utilizado pelo Explorador de TSI e APIs de consulta.
   **Subscrição** | As subscrições são contentores de recursos do Azure. Escolha uma subscrição para criar o ambiente do TSI para.
   **Grupo de recursos** | Um grupo de recursos é um contentor de recursos do Azure. Escolha um grupo de recursos existente ou crie um novo para o recurso de ambiente do TSI.
   **Localização** | Escolha a região do centro de dados para o ambiente do TSI. Para evitar custos de largura de banda adicionais e latência, é melhor manter o ambiente do TSI na mesma região que os outros recursos IoT.
   **SKUs de Preços** | Escolha o débito necessário. Custo mais baixo e a capacidade de arranque, selecione `S1`.
   **Capacidade** | A capacidade é o multiplicador aplicado à taxa de entrada, à capacidade de armazenamento e ao custo associado ao SKU selecionado.  Pode alterar a capacidade após a criação. O custo mais baixo, selecione uma capacidade de 1.

   Quando tiver terminado, clique em **Criar** para iniciar o processo de aprovisionamento.

   [![Criar um recurso de ambiente do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Verifique os **notificações** painel para monitorizar a conclusão da implantação.  

   [![Implementação do ambiente do Time Series Insights foi concluída com êxito](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Em seguida, crie a solução de simulação de dispositivos, o que irá gerar dados de teste para preencher o ambiente do TSI:

1. Na janela/separador à parte, aceda a [azureiotsolutions.com](https://www.azureiotsolutions.com). Inicie sessão com a mesma conta de subscrição do Azure e selecione o **simulação do dispositivo** accelerator.

   [![Execute o acelerador de simulação do dispositivo](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Introduza os parâmetros necessários **solução de simulação do dispositivo criar** página.

   Parâmetro|Descrição
   ---|---
   **Nome da solução** | Um valor exclusivo, utilizado para a criação de um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Subscrição** | Especifique a mesma subscrição utilizada para a criação do ambiente do TSI, na secção anterior.
   **Região** | Especifique a mesma região utilizada para a criação do ambiente do TSI, na secção anterior.
   **Implementar Recursos do Azure opcionais** | Deixe a opção **Hub IoT** marcada, dado que os dispositivos simulados irão utilizá-la para ligar/transmitir dados.

   Quando terminar, clique em **criar solução** aprovisionar de recursos do Azure a solução. Pode demorar 6 a 7 minutos para concluir este processo.

   [![Aprovisionar a solução de simulação do dispositivo](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Quando tiver terminado de aprovisionamento, o texto acima a nova solução será alterado de **aprovisionamento...**  para **pronto**:

   >[!IMPORTANT]
   > Não clique ainda no botão **Iniciar**! Mas mantenha esta página Web aberta, pois regressará à ela mais tarde.

   [![Solução de simulação de dispositivo de aprovisionamento concluída](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Agora, volte ao portal do Azure e inspecione os recursos que acabou de criar na subscrição. No portal da página **Grupos de recursos**, verá um novo grupo de recursos que foi criado com o **Nome da solução** fornecida no último passo. Repare também em todos os recursos criados para suportar a solução de simulação de dispositivos:

   [![Recursos da solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Ligar o ambiente TSI ao hub IoT

Até aqui, aprendeu como criar dois conjuntos de recursos, cada um no seu próprio grupo de recursos:

- Um ambiente de TSI vazio.
- Dispositivo simulação recursos de solução, incluindo um hub IoT, gerado por um acelerador de solução.

Lembre-se que os dispositivos simulados precisam de estar ligados a um hub IoT para transmitirem dados dos dispositivos. Para transmitir dados para o ambiente do TSI, terá de alterar algumas configuração no hub IoT e no ambiente do TSI.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Configuração do hub IoT: definir um grupo de consumidores

O hub IoT fornece vários pontos finais para partilhar a funcionalidade com outros atores. O ponto final “Eventos” fornece uma forma para outras aplicações consumirem dados, dado que é transmitida para uma instância do hub IoT. Em particular, os “grupos de consumidores” fornecem um mecanismo para as aplicações escutarem e extraírem dados do Hub IoT.

Em seguida, define uma nova **grupo de consumidores** propriedade, no hub de IoT da solução de simulação de dispositivo **ponto final eventos**.

1. No portal do Azure, aceda à página **Descrição geral** do grupo de recursos que criou para a solução de simulação de dispositivos e, em seguida, selecione o recurso do Hub IoT:

   [![Grupo de recursos da solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Anote também o **Nome** do recurso do Hub IoT gerado para a solução, pois precisará dele posteriormente.

1. Desloque-se para baixo e selecione a página **Pontos finais** e, em seguida, selecione o ponto final **Eventos**. No página **Propriedades** do ponto final, introduza um nome exclusivo para o ponto final, sob o grupo de consumidores “$Default”, e clique em **Guardar**:

   [![Pontos finais do hub IoT da solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-configuration-define-an-event-source"></a>Configuração do TSI: definir uma origem de evento

Agora ligar o novo hub IoT **grupo de consumidores** ponto final de eventos, para o ambiente de TSI, como um **origem do evento**.

1. Aceda à página **Descrição geral** do grupo de recursos que criou para o ambiente do TSI e, em seguida, selecione o ambiente do TSI:

   [![Ambiente e grupo de recursos do ambiente do TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Na página de ambiente de TSI, selecione **origens de eventos**, em seguida, clique em **+ adicionar**.

   [![Descrição geral do ambiente de TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Introduza os parâmetros necessários **nova origem de evento** página.

   Parâmetro|Descrição
   ---|---
   **Nome da origem de evento** | Requer um valor exclusivo, utilizado para o nome da origem de evento.
   **Origem** | Selecione **IoT Hub**.
   **Opção de importação** | Selecione a predefinição `Use IoT hub from available subscriptions`. Esta opção permitirá que a próxima lista pendente seja preenchida com as subscrições disponíveis.
   **Subscrição** | Selecione a mesma subscrição onde criou o ambiente do TSI e os recursos de Simulação de Dispositivos.
   **Nome do hub IoT** | Deve ser predefinido para o nome do hub IoT que anotou anteriormente. Caso contrário, selecione o hub IoT correto.
   **Nome da política do hub IoT** | Selecione **iothubowner**.
   **Grupo de consumidores do hut IoT** | Deve ser predefinido para o nome do grupo de consumidores do hub IoT que criou anteriormente. Caso contrário, selecione o nome do grupo de consumidores correto.
   **Formato de serialização de eventos** | Deixe como o valor padrão de `JSON`.
   **Nome da propriedade Carimbo de data/hora** | Especificar como `timestamp`.

   Quando terminar, clique em **Criar** para adicionar a origem de evento. Quando regressar à página **Descrição geral** do grupo de recursos, juntamente com o recurso do ambiente do TSI, verá um novo recurso “Origem de evento do Time Series Insights”.

   [![Origem de evento novo do TSI ambiente](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data-into-tsi"></a>Executar a simulação do dispositivo para transmitir dados em TSI

Agora que todo o trabalho de configuração está concluído, está na altura de preencher o ambiente do TSI com dados de exemplo dos dispositivos simulados.

Talvez se lembre que, na secção [Criar uma simulação de dispositivos](#create-a-device-simulation), vários recursos do Azure foram criados pelo acelerador para suportar a solução. Juntamente com o hub IoT, abordado anteriormente, foi gerada uma aplicação Web do Serviço de Aplicações do Azure para criar e transmitir telemetria dos dispositivos simulados.

1. Volte ao [Dashboard dos aceleradores de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard). Inicie sessão novamente se necessário, com a mesma conta do Azure que tem estado a utilizar neste tutorial. Agora, pode clicar a **inicie** botão sob a sua solução de "Simulação do dispositivo".

     [![Dashboard de Aceleradores de solução](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. A aplicação Web de simulação de dispositivos será iniciada neste momento e poderá demorar vários segundos após o carregamento inicial. Também lhe pedirá consentimento, para dar permissão à aplicação Web para “Iniciar sessão e ler o seu perfil”. Esta permissão permite que o aplicativo recupere as informações de perfil de utilizador necessárias para suportar o funcionamento da aplicação.

     [![Consentimento de aplicativo de web de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Uma vez a **programa de configuração de simulação** carregamentos de página, introduza os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Hub IoT de destino** | Selecione **utilização previamente aprovisionadas IoT Hub**.
   **Modelo do dispositivo** | Selecione **Chiller**.
   **Número de dispositivos**  | Introduza `1000` sob **quantidade**.
   **Frequência da telemetria** | Introduza `10` segundos.
   **Duração da simulação** | Selecione **terminar:** e introduza `5` minutos.

   Quando terminar, clique em **Iniciar Simulação**. A simulação será executada durante um total de 5 minutos e vai gerar dados a partir de 1000 dispositivos simulados, de 10 em 10 segundos.  

   [![Configuração de simulação do dispositivo](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Enquanto a simulação é executada, verá a atualização dos campos **Total de mensagens** e **Mensagens por segundo**, aproximadamente a cada 10 segundos. A simulação terminará depois de aproximadamente 5 minutos e poderá regressar a **Configuração da simulação**.

   [![Simulação de dispositivo em execução](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Verificar os dados de telemetria

Nesta secção final, vai verificar se os dados de telemetria foram gerados e armazenados no ambiente do TSI. Para verificar os dados, pode utilizar o explorador do Time Series Insights, que serve para consultar e analisar os dados de telemetria.

1. Voltar ao grupo de recursos do ambiente de TSI **descrição geral** página. Selecione o ambiente do TSI.

   [![Ambiente e grupo de recursos do ambiente do TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. No ambiente do TSI **descrição geral** página, clique nas **URL de Explorador do Time Series Insights** para abrir o Explorador TSI.

   [![Explorador do TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. O explorador do TSI será carregado e autenticado com a sua conta no portal do Azure. Na vista inicial, pode ver na área de gráfico que o ambiente do TSI foi realmente preenchido com os dados telemétricos simulados. Para filtrar um intervalo de tempo mais curto, selecione a lista pendente no canto superior esquerdo. Introduza um intervalo de tempo suficientemente grande para abranger a duração da simulação de dispositivos. Em seguida, clique em pesquisa ampliar a classe.

   [![Filtro do intervalo de tempo do explorador do TSI](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Encurtar o intervalo de tempo permite que o gráfico aplique zoom nas rajadas da transferência de dados distintas, para o hub IoT e para o ambiente do TSI. Repare também no texto **Transmissão concluída**, no canto superior direito, que mostra o número total de eventos encontrado. Também pode arrastar o **tamanho do intervalo** controlo de deslize para controlar a granularidade de desenho no gráfico.

   [![Vista filtrada do intervalo de tempo do explorador do TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Por último, também pode left-click uma região filtrar um intervalo, em seguida, clique com botão direito e utilizar **explorar eventos** para mostrar detalhes do evento na tabela **eventos** vista.

   [![Eventos e vista filtrada do intervalo de tempo do explorador do TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução, para suportar a solução de simulação de dispositivos e o ambiente do TSI. Se quiser abandonar e/ou retardar a conclusão desta série de tutoriais, recomendamos eliminar todos os recursos para evitar incorrer em custos desnecessários.

No menu esquerdo do portal do Azure:

1. Clique no ícone **Grupos de recursos** e, em seguida, selecione o grupo de recursos que criou para o Ambiente do TSI. Na parte superior da página, clique em **Eliminar grupo de recursos**, escreva o nome do grupo de recurso e, em seguida, clique em **Eliminar**.

1. Clique no ícone **Grupos de recursos** e, em seguida, selecione o grupo de recursos que foi criado pelo acelerador de soluções da simulação de dispositivos. Na parte superior da página, clique em **eliminar grupo de recursos**, introduza o nome do grupo de recursos, em seguida, clique em **eliminar**

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
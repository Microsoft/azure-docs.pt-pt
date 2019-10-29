---
title: 'Tutorial: criar um ambiente de Azure Time Series Insights | Microsoft Docs'
description: Tutorial para aprender a criar um ambiente de Time Series Insights que é populado com dados de dispositivos simulados.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: 9ca60b876272df15d306ac7fba2dc61875db6d06
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989655"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Tutorial: Criar um ambiente do Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de um ambiente de Azure Time Series Insights que é populado com dados de dispositivos simulados. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um ambiente de Time Series Insights.
> * Crie uma solução de simulação de dispositivo que contenha um hub IoT.
> * Conecte o ambiente de Time Series Insights ao Hub IoT.
> * Execute uma simulação de dispositivo para transmitir dados para o ambiente de Time Series Insights.
> * Verifique os dados de telemetria simulados.

> [!IMPORTANT]
> Inscreva-se para obter uma [assinatura gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Sua conta de entrada do Azure também deve ser um membro da função de **proprietário** da assinatura. Para obter mais informações, consulte [gerenciar o acesso usando o controle de acesso baseado em função e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Examinar vídeo

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Saiba como usar um acelerador de solução do Azure IoT para gerar dados e introdução ao Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Visão geral

O ambiente de Time Series Insights é onde os dados do dispositivo são coletados e armazenados. Uma vez armazenado, a API de [consulta](/rest/api/time-series-insights/ga-query-api) do [Azure Time Series insights explorer](time-series-quickstart.md) e do time Series insights pode ser usada para consultar e analisar os dados.

O Hub IoT do Azure é a origem do evento usada por todos os dispositivos (simulados ou físicos) no tutorial para conectar e transmitir dados com segurança para sua nuvem do Azure.

Este tutorial também usa um [acelerador de solução de IOT](https://www.azureiotsolutions.com) para gerar e transmitir dados de telemetria de exemplo para o Hub IOT.

>[!TIP]
> Os [aceleradores de solução de IOT](https://www.azureiotsolutions.com) fornecem soluções pré-configuradas de nível empresarial que você pode usar para acelerar o desenvolvimento de soluções de IOT personalizadas.

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Primeiro, crie a solução de simulação de dispositivo, que gera dados de teste para popular seu ambiente de Time Series Insights.

1. Em uma janela ou guia separada, acesse [azureiotsolutions.com](https://www.azureiotsolutions.com). Entre usando a mesma conta de assinatura do Azure e selecione o acelerador de **simulação de dispositivo** . Selecione **experimentar agora**.

   [![executar o acelerador de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Insira os parâmetros necessários na página **criar solução de simulação de dispositivo** .

   Parâmetro|Descrição
   ---|---
   **Nome da implantação** | Esse valor exclusivo é usado para criar um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Subscrição do Azure** | Especifique a mesma assinatura que foi usada para criar seu ambiente de Time Series Insights na seção anterior.
   **Opções de implantação** | Selecione **provisionar novo hub IOT** para criar um novo hub IOT específico para este tutorial.
   **Local do Azure** | Especifique a mesma região que foi usada para criar seu ambiente de Time Series Insights na seção anterior.

   Quando tiver terminado, selecione **criar** para provisionar os recursos do Azure da solução. Pode levar até 20 minutos para concluir esse processo.

   [![provisionar a solução de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Após a conclusão do provisionamento, o texto acima da nova solução muda de **provisionamento** para **pronto**.

   >[!IMPORTANT]
   > Não selecione **Iniciar** ainda! Mantenha essa página da Web aberta, pois você retornará a ela mais tarde.

   [provisionamento da solução de simulação de dispositivo![concluído](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Agora, inspecione os recursos recém-criados no portal do Azure. Na página **grupos de recursos** , observe que um novo grupo de recursos foi criado usando o **nome da solução** fornecido na última etapa. Anote os recursos que foram criados para a simulação do dispositivo.

   [![recursos de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Criar um ambiente

Em segundo lugar, crie um ambiente de Time Series Insights em sua assinatura do Azure.

1. Entre no [portal do Azure](https://portal.azure.com) usando sua conta de assinatura do Azure. 
1. Selecione **+ Criar um recurso** no canto superior esquerdo. 
1. Selecione a categoria **Internet das coisas** e, em seguida, selecione **Time Series insights**. 

   [![Selecionar o recurso de ambiente do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na página **Time Series insights ambiente** , preencha os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Nome do ambiente** | Escolha um nome exclusivo para o ambiente de Time Series Insights. Os nomes são usados pelo Time Series Insights Explorer e pelas [APIs de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Subscrição** | As subscrições são contentores de recursos do Azure. Escolha uma assinatura para criar o ambiente de Time Series Insights.
   **Grupo de recursos** | Um grupo de recursos é um contentor de recursos do Azure. Escolha um grupo de recursos existente ou crie um novo para o recurso de ambiente Time Series Insights.
   **Localização** | Escolha uma região de data center para seu ambiente de Time Series Insights. Para evitar latência adicional, crie o ambiente de Time Series Insights na mesma região que outros recursos de IoT.
   **Preço** | Escolha o débito necessário. Selecione **S1**.
   **Capacidade** | A capacidade é o multiplicador aplicado à taxa de entrada e à capacidade de armazenamento associada à SKU selecionada. Você pode alterar a capacidade após a criação. Selecione uma capacidade de **1**.

   Quando terminar, selecione **Avançar: origem do evento** para prosseguir para a próxima etapa.

   [![criar um recurso de ambiente de Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Agora, conecte o ambiente de Time Series Insights ao Hub IoT criado pelo Solution Accelerator. Defina **selecionar um hub** para `Select existing`. Em seguida, escolha o Hub IoT criado pelo Solution Accelerator ao definir o **nome do Hub IOT**.

   [![conectar o ambiente de Time Series Insights ao Hub IoT criado](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

   Por fim, selecione **revisar + criar**.

1. Verifique o painel **notificações** para monitorar a conclusão da implantação. 

   [implantação de ambiente de Time Series Insights de![bem-sucedida](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Executar simulação de dispositivo

Agora que a implantação e a configuração inicial estão concluídas, preencha o ambiente de Time Series Insights com dados de exemplo de [dispositivos simulados criados pelo acelerador](#create-a-device-simulation).

Junto com o Hub IoT, um aplicativo Web de serviço de Azure App foi gerado para criar e transmitir telemetria de dispositivo simulado.

1. Volte ao [Dashboard dos aceleradores de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard). Entre novamente, se necessário, usando a mesma conta do Azure que você esteve usando neste tutorial. Selecione a "solução de dispositivo" e **vá para o Solution Accelerator** para iniciar a solução implantada.

     [painel![Solution Accelerators](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. O aplicativo Web de simulação de dispositivo começa solicitando que você conceda ao aplicativo Web a permissão "entrar e ler seu perfil". Essa permissão permite que o aplicativo recupere as informações de perfil do usuário necessárias para dar suporte ao funcionamento do aplicativo.

     [consentimento do aplicativo Web de simulação de dispositivo![](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Depois que a página **instalação da simulação** for carregada, insira os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Hub IoT de destino** | Selecione **usar Hub IOT pré-provisionado**.
   **Modelo do dispositivo** | Selecione **resfriador**.
   **Número de dispositivos**  | Insira `1000` em **valor**.
   **Frequência da telemetria** | Insira `10` segundos.
   **Duração da simulação** | Selecione **terminar em:** e insira `5` minutos.

   Quando tiver terminado, selecione **Iniciar simulação**. A simulação é executada por um total de 5 minutos. Ele gera dados de 1.000 dispositivos simulados a cada 10 segundos. 

   [configuração da simulação de dispositivo![](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Durante a execução da simulação, observe que os campos **total de mensagens** e **mensagens por segundo** são atualizados, aproximadamente a cada 10 segundos. A simulação termina após aproximadamente 5 minutos e retorna à **configuração de simulação**.

   [simulação de dispositivo![em execução](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Verificar os dados de telemetria

Nesta seção final, você verifica se os dados de telemetria foram gerados e armazenados no ambiente de Time Series Insights. Para verificar os dados, pode utilizar o explorador do Time Series Insights, que serve para consultar e analisar os dados de telemetria.

1. Retorne à página **visão geral** do grupo de recursos do ambiente de time Series insights. Selecione o ambiente de Time Series Insights.

   [ambiente e grupo de recursos de ambiente de Time Series Insights![](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na página **visão geral** do ambiente de time Series insights, selecione a **URL do time Series insights Explorer** para abrir o time Series insights Explorer.

   [![o Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. O Time Series Insights Explorer carrega e autentica usando sua conta do portal do Azure. Na exibição inicial, você pode ver na área do gráfico que o ambiente de Time Series Insights foi populado com dados de telemetria simulados. Para filtrar um intervalo mais estreito de tempo, selecione a lista suspensa no canto superior esquerdo. Insira um intervalo de tempo grande o suficiente para abranger a duração da simulação do dispositivo. Em seguida, selecione a lupa Pesquisar.

   [filtro de intervalo de tempo do![Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Restringir o intervalo de tempo permite que o gráfico Amplie as intermitências distintas de transferência de dados para o Hub IoT e o ambiente de Time Series Insights. Observe também o texto de **streaming completo** no canto superior direito, que mostra o número total de eventos encontrados. Você também pode arrastar o controle deslizante de **tamanho do intervalo** para controlar a granularidade da plotagem no gráfico.

   [exibição filtrada do intervalo de tempo do![Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Por fim, também é possível clicar com o botão esquerdo do mouse em uma região para filtrar um intervalo. Em seguida, clique com o botão direito do mouse e use **explorar eventos** para mostrar os detalhes do evento na exibição de **eventos** tabulares.

   [![exibição e eventos filtrados do intervalo de tempo do Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução para dar suporte ao ambiente de Time Series Insights e à solução de simulação de dispositivo. Para removê-los, navegue de volta para a portal do Azure.

No menu à esquerda no portal do Azure:

1. Selecione o ícone **grupos de recursos** . Em seguida, selecione o grupo de recursos que você criou para o ambiente de Time Series Insights. Na parte superior da página, selecione **excluir grupo de recursos**, insira o nome do grupo de recursos e selecione **excluir**.

1. Selecione o ícone **grupos de recursos** . Em seguida, selecione o grupo de recursos que foi criado pelo acelerador de solução de simulação de dispositivo. Na parte superior da página, selecione **excluir grupo de recursos**, insira o nome do grupo de recursos e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Crie um ambiente de Time Series Insights.
> * Crie uma solução de simulação de dispositivo que contenha um hub IoT.
> * Conecte o ambiente de Time Series Insights ao Hub IoT.
> * Execute uma simulação de dispositivo para transmitir dados para o ambiente de Time Series Insights.
> * Verifique os dados de telemetria simulados.

Agora que você sabe como criar seu próprio ambiente de Time Series Insights, saiba como criar um aplicativo Web que consome dados de um ambiente de Time Series Insights:

> [!div class="nextstepaction"]
> [Consulte amostras de visualização do SDK de cliente hospedado](https://tsiclientsample.azurewebsites.net/)

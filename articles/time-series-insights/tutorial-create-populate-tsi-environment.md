---
title: 'Tutorial: Criar um ambiente - Azure Time Series Insights [ Microsoft Docs'
description: Aprenda a criar um ambiente time series insights que é povoado com dados de dispositivos simulados.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: 47cee660114ba0b19b952015b1fecff8c85d2c25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189220"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Tutorial: Criar um ambiente do Azure Time Series Insights

Este tutorial guia-o através do processo de criação de um ambiente Azure Time Series Insights que é povoado com dados de dispositivos simulados. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um ambiente time series insights.
> * Crie uma solução de simulação de dispositivo que contenha um hub IoT.
> * Ligue o ambiente Time Series Insights ao centro IoT.
> * Executar uma simulação de dispositivo para transmitir dados para o ambiente Time Series Insights.
> * Verifique os dados simulados de telemetria.

> [!IMPORTANT]
> Inscreva-se para uma [subscrição Azure gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* A sua conta de entrada azure também deve ser membro do papel de **Proprietário** da subscrição. Para mais informações, leia [Gerir o acesso utilizando o controlo de acesso baseado em papéis e o portal Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Rever vídeo

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Aprenda a usar um acelerador de solução Azure IoT para gerar dados e começar com time series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Descrição geral

O ambiente Time Series Insights é onde os dados do dispositivo são recolhidos e armazenados. Uma vez armazenado, o explorador de Insights da [Série De Tempo Azure](time-series-quickstart.md) e a [Time Series Insights Query API](/rest/api/time-series-insights/ga-query-api) podem ser usadas para consultar e analisar os dados.

O Azure IoT Hub é a fonte do evento que é usada por todos os dispositivos (simulados ou físicos) no tutorial para ligar e transmitir dados de forma segura à sua nuvem Azure.

Este tutorial também usa um acelerador de [solução IoT](https://www.azureiotsolutions.com) para gerar e transmitir dados de telemetria de amostra para IoT Hub.

>[!TIP]
> [Os aceleradores de soluções IoT](https://www.azureiotsolutions.com) fornecem soluções pré-reconfiguradas de nível empresarial que pode usar para acelerar o desenvolvimento de soluções IoT personalizadas.

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Em primeiro lugar, crie a solução de simulação do dispositivo, que gera dados de teste para povoar o seu ambiente Time Series Insights.

1. Numa janela ou separado, vá para [azureiotsolutions.com](https://www.azureiotsolutions.com). Inscreva-se utilizando a mesma conta de subscrição Do Azure e selecione o acelerador **de simulação** de dispositivo.

   [![Executar o acelerador de Simulação de Dispositivos](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Selecione **Tente Agora**. Em seguida, introduza os parâmetros necessários na página de **solução de simulação** de dispositivo criar.

   Parâmetro|Descrição
   ---|---
   **Nome de implantação** | Este valor único é usado para criar um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Subscrição do Azure** | Especifique a mesma subscrição que foi usada para criar o seu ambiente Time Series Insights na secção anterior.
   **Opções de implementação** | Selecione **O novo Hub IoT** para criar um novo hub IoT específico para este tutorial.
   **Localização azure** | Especifique a mesma região que foi usada para criar o seu ambiente Time Series Insights na secção anterior.

   Quando terminar, selecione **Create** para fornecer os recursos Azure da solução. Pode levar até 20 minutos para concluir este processo.

   [![Aprovisionar a solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Depois de concluído o fornecimento, duas atualizações serão apresentadas notificando-o de que o estado de implantação passou de **Provisioning** para **Ready**.

   >[!IMPORTANT]
   > Não entre no acelerador de soluções ainda! Mantenha esta página aberta porque voltará mais tarde.

   [![Aprovisionamento da solução de simulação de dispositivos concluída](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Agora, inspecione os recursos recém-criados no portal Azure. Na página dos **grupos de Recursos,** note que foi criado um novo grupo de recursos utilizando o **nome Solution** fornecido no último passo. Tome nota dos recursos que foram criados para a simulação do dispositivo.

   [![Recursos de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Criar um ambiente

Em segundo lugar, crie um ambiente Time Series Insights na sua subscrição Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a sua conta de subscrição Azure.
1. Selecione **+ Criar um recurso** no canto superior esquerdo.
1. Selecione a categoria **Internet das Coisas** e, em seguida, selecione Time Series **Insights**.

   [![Selecione o recurso ambiental Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Na página ambiente **Time Series Insights,** preencha os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Nome do ambiente** | Escolha um nome único para o ambiente Time Series Insights. Os nomes são usados pelo explorador time series Insights e pela [Consulta APIs](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Subscrição** | As subscrições são contentores de recursos do Azure. Escolha uma subscrição para criar o ambiente Time Series Insights.
   **Grupo de recursos** | Um grupo de recursos é um contentor de recursos do Azure. Escolha um grupo de recursos existente ou crie um novo para o recurso ambiental Time Series Insights.
   **Localização** | Escolha uma região de data center para o seu ambiente Time Series Insights. Para evitar latência adicional, crie o ambiente Time Series Insights na mesma região que outros recursos IoT.
   **Escalão** | Escolha o débito necessário. Selecione **S1**.
   **Capacidade** | Capacidade é o multiplicador aplicado à taxa de entrada e capacidade de armazenamento associada ao SKU selecionado. Pode mudar a capacidade após a criação. Selecione uma capacidade de **1**.

   Quando terminar, selecione **Next: Event Source** para passar ao passo seguinte.

   [![Criar o recurso de ambiente do Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Agora, ligue o ambiente Time Series Insights ao hub IoT criado pelo Acelerador de Soluções. Definir **Selecione um hub** para `Select existing`. Em seguida, escolha o hub IoT criado pelo Acelerador de Soluções ao definir o **nome IoT Hub**.

   [![Ligue o ambiente Time Series Insights ao centro ioT criado](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Por último, selecione **Review + criar**.

1. Verifique o painel **de Notificações** para monitorizar a conclusão da implantação.

   [![Implementação do ambiente do Time Series Insights concluída com êxito](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Executar simulação de dispositivo

Agora que a implementação e a configuração inicial completam, povoam o ambiente Time Series Insights com dados de amostra [sintetizadores criados pelo acelerador.](#create-a-device-simulation)

Juntamente com o hub IoT, foi gerada uma aplicação web azur service Azure App Service para criar e transmitir telemetria simulada de dispositivos.

1. Volte ao [Dashboard dos aceleradores de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard). Faça o sinal de novo, se necessário, utilizando a mesma conta Azure que tem usado neste tutorial. Selecione a sua "Solução de Dispositivo" e, em seguida, **vá ao acelerador de soluções** para lançar a sua solução implantada.

   [![Dashboard dos aceleradores de soluções](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. A aplicação web de simulação do dispositivo começa por lhe pedir que conceda à aplicação web o **Sinte e leia** a sua permissão de perfil. Esta permissão permite que a aplicação recupere as informações de perfil do utilizador necessárias para suportar o funcionamento da aplicação.

   [![Consentimento da aplicação Web de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Selecione **+ Nova simulação**. Depois de a página de configuração de **simulação** ser carregada, introduza os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Hub IoT de destino** | Selecione Utilize o **Hub IoT pré-provisionado**.
   **Modelo do dispositivo** | Selecione **Chiller**.
   **Número de dispositivos**  | Insira `10` em **valor**.
   **Frequência da telemetria** | Entra `10` segundos.
   **Duração da simulação** | Selecione Terminar `5` dentro **e** insira minutos.

   Quando terminar, selecione **Iniciar simulação**. A simulação dura um total de 5 minutos. Gera dados de 1.000 dispositivos simulados a cada 10 segundos.

   [![Configuração da simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Enquanto a simulação corre, note que as **mensagens totais** e **mensagens por segundo** campos atualizam, aproximadamente a cada 10 segundos. A simulação termina após aproximadamente 5 minutos e devolve-o à **configuração de Simulação**.

   [![Execução da simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Verificar os dados de telemetria

Nesta secção final, verifica-se que os dados da telemetria foram gerados e armazenados no ambiente Time Series Insights. Para verificar os dados, pode utilizar o explorador do Time Series Insights, que serve para consultar e analisar os dados de telemetria.

1. Volte à página de **visão geral** do grupo de recursos do grupo time Series Insights. Selecione o ambiente Time Series Insights.

   [![Grupo e ambiente de recursos ambientais da Série Time Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na página de visão **geral** do ambiente time series Insights, selecione o URL do explorador time **series Insights** para abrir o explorador de Insights da Série De Tempo.

   [![Explorador do Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. O explorador Time Series Insights carrega e autentica utilizando a sua conta de portal Azure. Inicialmente, aparecerá a área do gráfico com a que o ambiente Time Series Insights foi povoado juntamente com os seus dados simulados de telemetria. Para filtrar uma gama de tempo mais estreita, selecione a queda no canto superior esquerdo. Introduza uma gama de tempo suficientemente grande para abranger a duração da simulação do dispositivo. Em seguida, selecione a lupa de pesquisa.

   [![Filtro de intervalo de tempo do explorador de Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Reduzir o intervalo de tempo permite que o gráfico faça zoom para as explosões distintas de transferência de dados para o centro IoT e para o ambiente Time Series Insights. Note também o texto **completo do Streaming** no canto superior direito, que mostra o número total de eventos encontrados. Também pode arrastar o slider do **tamanho do intervalo** para controlar a granularidade do enredo na tabela.

   [![Time Series Insights intervalo de tempo filtrado](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Por último, também pode clicar à esquerda numa região para filtrar uma gama. Em seguida, clique à direita e use **explore eventos** para mostrar detalhes do evento na **vista** eventos tabular.

   [![Time Series Insights intervalo de tempo filtrado e eventos](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços Azure em execução para apoiar o ambiente time series Insights e a solução de simulação de dispositivos. Para removê-los, navegue de volta para o portal Azure.

Do menu à esquerda no portal Azure:

1. Selecione o ícone dos **grupos de recursos.** Em seguida, selecione o grupo de recursos que criou para o ambiente Time Series Insights. Na parte superior da página, selecione Eliminar o **grupo de recursos,** introduza o nome do grupo de recursos e selecione **Eliminar**.

1. Selecione o ícone dos **grupos de recursos.** Em seguida, selecione o grupo de recursos que foi criado pelo acelerador de solução de simulação do dispositivo. Na parte superior da página, selecione Eliminar o **grupo de recursos,** introduza o nome do grupo de recursos e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Crie um ambiente time series insights.
> * Crie uma solução de simulação de dispositivo que contenha um hub IoT.
> * Ligue o ambiente Time Series Insights ao centro IoT.
> * Executar uma simulação de dispositivo para transmitir dados para o ambiente Time Series Insights.
> * Verifique os dados simulados de telemetria.

Agora que sabe como criar o seu próprio ambiente time series Insights, aprenda a construir uma aplicação web que consuma dados a partir de um ambiente time series Insights:

> [!div class="nextstepaction"]
> [Ler amostras de visualização sdk do cliente hospedado](https://tsiclientsample.azurewebsites.net/)

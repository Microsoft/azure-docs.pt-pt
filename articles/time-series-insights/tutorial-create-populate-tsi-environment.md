---
title: 'Tutorial: Criar um ambiente - Azure Time Series Insights / Microsoft Docs'
description: Aprenda a criar um ambiente Azure Time Series Insights que seja preenchido com dados de dispositivos simulados.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: d504cbe9c2ac9046a34187d4a5576385b1f73bbe
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518114"
---
# <a name="tutorial-create-an-azure-time-series-insights-gen1-environment"></a>Tutorial: Criar um ambiente Azure Time Series Insights Gen1

> [!CAUTION]
> Este é um artigo da Gen1.

Este tutorial guia-o através do processo de criação de um ambiente Azure Time Series Insights que é preenchido com dados de dispositivos simulados. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um ambiente Azure Time Series Insights.
> * Crie uma solução de simulação de dispositivo que contenha um hub IoT.
> * Ligue o ambiente Azure Time Series Insights ao hub IoT.
> * Executar uma simulação do dispositivo para transmitir dados para o ambiente Azure Time Series Insights.
> * Verifique os dados de telemetria simulados.

> [!IMPORTANT]
> Inscreva-se para uma [subscrição gratuita do Azure](https://azure.microsoft.com/free/) se ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* A sua conta de inscrição Azure também deve ser membro da função **Proprietário** da subscrição. Para obter mais informações, leia [Adicionar ou remover atribuições de funções Azure utilizando o portal Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Rever vídeo

Aprenda a usar um acelerador de solução Azure IoT para gerar dados e começar com a Azure Time Series Insights.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Descrição geral

O ambiente Azure Time Series Insights é onde os dados do dispositivo são recolhidos e armazenados. Uma vez armazenados, o [Azure Time Series Insights Explorer](time-series-quickstart.md) e a [Azure Time Series Insights Query API](/rest/api/time-series-insights/gen1-query-api) podem ser utilizados para consultar e analisar os dados.

O Azure IoT Hub é a fonte de evento que é usada por todos os dispositivos (simulados ou físicos) no tutorial para ligar e transmitir dados de forma segura à sua nuvem Azure.

Este tutorial também usa um acelerador de [solução IoT](https://www.azureiotsolutions.com) para gerar e transmitir dados de telemetria de amostra para IoT Hub.

>[!TIP]
> [Os aceleradores de soluções IoT](https://www.azureiotsolutions.com) fornecem soluções pré-configuradas de nível empresarial que pode usar para acelerar o desenvolvimento de soluções IoT personalizadas.

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Em primeiro lugar, crie a solução de simulação do dispositivo, que gera dados de teste para povoar o ambiente Azure Time Series Insights.

1. Numa janela ou separador, vá a [azureiotsolutions.com](https://www.azureiotsolutions.com). Inscreva-se utilizando a mesma conta de subscrição Azure e selecione o acelerador **de Simulação** de Dispositivo.

   [![Executar o acelerador de Simulação de Dispositivos](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Selecione **Tente agora**. Em seguida, introduza os parâmetros necessários na página **de solução de simulação** do dispositivo.

   Parâmetro|Descrição
   ---|---
   **Nome de implantação** | Este valor único é usado para criar um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Subscrição do Azure** | Especifique a mesma subscrição que foi usada para criar o seu ambiente Azure Time Series Insights na secção anterior.
   **Opções de implementação** | Selecione **Provision novo IoT Hub** para criar um novo hub IoT específico para este tutorial.
   **Local azul** | Especifique a mesma região que foi usada para criar o seu ambiente Azure Time Series Insights na secção anterior.

   Quando terminar, **selecione Criar** para forragem dos recursos Azure da solução. Pode levar até 20 minutos para concluir este processo.

   [![Aprovisionar a solução de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Após o fornecimento ter terminado, duas atualizações serão apresentadas notificando-o de que o estado de implantação passou de **Provisioning** para **Ready**.

   >[!IMPORTANT]
   > Não entre no seu acelerador de solução ainda! Mantenha esta página web aberta porque voltará mais tarde.

   [![Aprovisionamento da solução de simulação de dispositivos concluída](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Agora, inspecione os recursos recém-criados no portal Azure. Na página **dos grupos de recursos,** note que um novo grupo de recursos foi criado utilizando o **nome Solução** fornecido no último passo. Tome nota dos recursos que foram criados para a simulação do dispositivo.

   [![Recursos de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Criar um ambiente

Em segundo lugar, crie um ambiente Azure Time Series Insights na sua subscrição Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a sua conta de subscrição Azure.
1. Selecione **+ Criar um recurso** no canto superior esquerdo.
1. Selecione a categoria **Internet of Things** e, em seguida, selecione Time Series **Insights**.

   [![Selecione o recurso ambiente Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Na página de **ambiente Time Series Insights,** preencha os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Nome do ambiente** | Escolha um nome único para o ambiente Azure Time Series Insights. Os nomes são utilizados pelo Azure Time Series Insights Explorer e pelas [APIs de Consulta](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query).
   **Subscrição** | As subscrições são contentores de recursos do Azure. Escolha uma subscrição para criar o ambiente Azure Time Series Insights.
   **Grupo de recursos** | Um grupo de recursos é um contentor de recursos do Azure. Escolha um grupo de recursos existente ou crie um novo para o recurso ambiente Azure Time Series Insights.
   **Localização** | Escolha uma região de data center para o seu ambiente Azure Time Series Insights. Para evitar latência adicional, crie o ambiente Azure Time Series Insights na mesma região que outros recursos IoT.
   **Escalão de serviço** | Escolha o débito necessário. Selecione **S1**.
   **Capacidade** | A capacidade é o multiplicador aplicado à taxa de entrada e capacidade de armazenamento associada ao SKU selecionado. Pode mudar a capacidade após a criação. Selecione uma capacidade de **1**.

   Quando terminar, selecione **Seguinte: Fonte de Evento** para avançar para o passo seguinte.

   [![Criar um recurso ambiental Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Agora, ligue o ambiente Azure Time Series Insights ao hub IoT criado pelo Acelerador de Solução. Definir **Selecione um hub** para `Select existing` . Em seguida, escolha o hub IoT criado pelo Acelerador de Solução ao definir o **nome IoT Hub**.

   [![Ligue o ambiente Azure Time Series Insights ao hub IoT criado](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Por último, selecione **Review + create**.

1. Consulte o painel **de Notificações** para monitorizar a conclusão da implementação.

   [![Azure Time Series Insights implementação ambiental conseguiu](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Simulação de dispositivo de execução

Agora que a configuração e a configuração inicial estão completas, povoam o ambiente Azure Time Series Insights com dados de amostra de [dispositivos simulados criados pelo acelerador](#create-a-device-simulation).

Juntamente com o hub IoT, foi gerada uma aplicação web do Azure App Service para criar e transmitir telemetria de dispositivo simulado.

1. Volte ao [Dashboard dos aceleradores de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard). Inscreva-se novamente, se necessário, usando a mesma conta Azure que tem usado neste tutorial. Selecione a sua "Solução de Dispositivo" e, em seguida, **vá ao acelerador da solução** para lançar a sua solução implementada.

   [![Dashboard dos aceleradores de soluções](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. A aplicação web de simulação do dispositivo começa por lhe pedir para conceder à aplicação web o **Signo-lo e ler a** permissão do seu perfil. Esta permissão permite que a aplicação recupere as informações necessárias para apoiar o funcionamento da aplicação.

   [![Consentimento da aplicação Web de simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Selecione **+ Nova simulação**. Após as cargas da página **de configuração de Simulação,** introduza os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Hub IoT de destino** | **Selecione Utilize o Hub IoT pré-a provisionado**.
   **Modelo do dispositivo** | Selecione **Chiller**.
   **Número de dispositivos**  | `10`Insira em **Valor**.
   **Frequência da telemetria** | Insira `10` segundos.
   **Duração da simulação** | Selecione **Terminar em:** e insira `5` minutos.

   Quando terminar, selecione **Iniciar a Simulação**. A simulação dura um total de 5 minutos. Gera dados de 1.000 dispositivos simulados a cada 10 segundos.

   [![Configuração da simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Enquanto a simulação é executado, note que as **mensagens** e **mensagens totais por segundo** desatualização, aproximadamente a cada 10 segundos. A simulação termina após aproximadamente 5 minutos e devolve-o à **configuração de Simulação**.

   [![Execução da simulação de dispositivos](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Verificar os dados de telemetria

Nesta secção final, verifique se os dados da telemetria foram gerados e armazenados no ambiente Azure Time Series Insights. Para verificar os dados, utiliza o Azure Time Series Insights Explorer, que é utilizado para consultar e analisar dados de telemetria.

1. Retorno à **página** geral do grupo de recursos do grupo de recursos Azure Time Insights. Selecione o ambiente Azure Time Series Insights.

   [![Azure Time Series Insights grupo de recursos ambientais e ambiente](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na página Azure Time Series Insights environment **Overview,** selecione o **URL do Explorador de Insights de Séries Tempos** para abrir o Azure Time Series Insights Explorer.

   [![Azure Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. O Azure Time Series Insights Explorer carrega e autentica utilizando a sua conta do portal Azure. Inicialmente, aparecerá a área de gráfico com a azure Time Series Insights, juntamente com os seus dados de telemetria simulados. Para filtrar um intervalo de tempo mais estreito, selecione a queda no canto superior esquerdo. Introduza um intervalo de tempo suficientemente grande para durar a duração da simulação do dispositivo. Em seguida, selecione a lupa de pesquisa.

   [![Filtro de intervalo de tempo Azure Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. A redução do intervalo de tempo permite que o gráfico amplie para as explosões distintas da transferência de dados para o hub IoT e para o ambiente Azure Time Series Insights. Note também o texto **completo streaming** no canto superior direito, que mostra o número total de eventos encontrados. Também pode arrastar o deslizador **de tamanho interval** para controlar a granularidade do enredo na tabela.

   [![Azure Time Series Insights Explorer vista filtrada do tempo](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Por último, também pode clicar à esquerda numa região para filtrar uma gama. Em seguida, clique à direita e use **Explore eventos** para mostrar detalhes do evento na vista de **Eventos tabulares.**

   [![Azure Time Series Insights Explorer tempo range visualização filtrada e eventos](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos

Este tutorial cria vários serviços Azure em execução para suportar o ambiente Azure Time Series Insights ambiente e solução de simulação de dispositivos. Para removê-los, navegue de volta para o portal Azure.

Do menu à esquerda no portal Azure:

1. Selecione o ícone **grupos de recursos.** Em seguida, selecione o grupo de recursos que criou para o ambiente Azure Time Series Insights. No topo da página, selecione **Delete resource group**, insira o nome do grupo de recursos e selecione **Delete**.

1. Selecione o ícone **grupos de recursos.** Em seguida, selecione o grupo de recursos que foi criado pelo acelerador de solução de simulação do dispositivo. No topo da página, selecione **Delete resource group**, insira o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Crie um ambiente Azure Time Series Insights.
> * Crie uma solução de simulação de dispositivo que contenha um hub IoT.
> * Ligue o ambiente Azure Time Series Insights ao hub IoT.
> * Executar uma simulação do dispositivo para transmitir dados para o ambiente Azure Time Series Insights.
> * Verifique os dados de telemetria simulados.

Agora que sabe como criar o seu próprio ambiente Azure Time Series Insights, aprenda a construir uma aplicação web que consome dados a partir de um ambiente Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Leia amostras de visualização SDK do cliente hospedado](https://tsiclientsample.azurewebsites.net/)

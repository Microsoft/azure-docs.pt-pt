---
title: 'Tutorial: Configurar um ambiente de pré-visualização do Azure Time Series Insights | Documentos da Microsoft'
description: Saiba como configurar o ambiente de pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: 824d24b97f192583a42192b3bb90eb1818e1aa18
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273060"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: Configurar um ambiente de pré-visualização do Azure Time Series Insights

Este tutorial orienta-o ao longo do processo de criação de um ambiente de pay as you go (PAYG) de pré-visualização do Azure Time Series Insights. 

Neste tutorial, ficará a saber como:

* Crie um ambiente de pré-visualização do Azure Time Series Insights.
* Ligar o ambiente de pré-visualização do Azure Time Series Insights para um hub de eventos nos Hubs de eventos do Azure.
* Execute um exemplo de acelerador de solução para transmitir dados para o ambiente de pré-visualização do Azure Time Series Insights.
* Execute análise básica nos dados.
* Definir um tipo de modelo de série de tempo e a hierarquia e associá-lo a suas instâncias.

>[!TIP]
> [Aceleradores de solução de IoT](https://www.azureiotsolutions.com/Accelerators) fornecem soluções pré-configuradas de nível empresarial que pode utilizar para acelerar o desenvolvimento de soluções de IoT personalizadas.

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Nesta secção, vai criar três dispositivos simulados que enviam dados para uma instância do IoT Hub do Azure.

1. Vá para o [página de Aceleradores de solução de IoT do Azure](https://www.azureiotsolutions.com/Accelerators). A página apresenta vários exemplos pré-criados. Inicie sessão com a sua conta do Azure. Em seguida, selecione **simulação do dispositivo**.

   [![Página de Aceleradores de solução de IoT do Azure](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Selecione **Experimente agora o**.

1. Sobre o **solução de simulação do dispositivo criar** página, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome da implementação** | Introduza um valor exclusivo para um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos. |
    | **Subscrição do Azure** | Selecione a subscrição que utilizou para criar o seu ambiente do Time Series Insights. |
    | **Localização do Azure** | Selecione a região que utilizou para criar o seu ambiente do Time Series Insights. |
    | **Opções de implementação** | Selecione **aprovisionar o novo IoT Hub**. |
 
    Selecione **criar solução**. Poderá demorar até 20 minutos para a solução concluir a implementação.

    [![Criar a página de solução de simulação do dispositivo](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Criar um ambiente de PAYG de pré-visualização do Time Series Insights

Esta secção descreve como criar um ambiente de pré-visualização do Azure Time Series Insights e ligá-la para o hub IoT criado o acelerador de solução de IoT com o [portal do Azure](https://portal.azure.com/).

1. Inicie sessão no portal do Azure com a sua conta de subscrição.

1. Selecione **criar um recurso** > **Internet das coisas** > **Time Series Insights**.

   [![Selecione a Internet das coisas e, em seguida, selecione o Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Na **ambiente criar o Time Series Insights** painel, na **Noções básicas** separador, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Introduza um nome exclusivo para o ambiente de pré-visualização do Azure Time Series Insights. |
    | **Subscrição** | Introduza a subscrição em que pretende criar o ambiente de pré-visualização do Azure Time Series Insights. Uma prática recomendada é utilizar a mesma subscrição que o resto dos recursos de IoT que são criados pelo simulador de dispositivos. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um novo grupo de recursos para o recurso de ambiente de pré-visualização do Azure Time Series Insights. Um grupo de recursos é um contentor de recursos do Azure. Uma prática recomendada é usar o mesmo grupo de recursos como os outros recursos de IoT que são criados pelo simulador de dispositivos. |
    | **Location** | Selecione uma região de datacenter para o seu ambiente de pré-visualização do Azure Time Series Insights. Para evitar a latência adicional, é melhor criar o seu ambiente de pré-visualização do Azure Time Series Insights na mesma região que os outros recursos de IoT. |
    | **Escalão** |  Selecione **PAYG** (*pay as you go*). Este é o SKU para o produto de pré-visualização do Azure Time Series Insights. |
    | **ID de propriedade** | Introduza um valor que identifica exclusivamente a sua instância de série de tempo. O valor que introduzir na **ID de propriedade** caixa é imutável. Não é possível alterá-lo mais tarde. Para este tutorial, introduza **iothub-ligação-dispositivo-id**. Para saber mais sobre o ID de série de tempo, veja [melhores práticas para a escolha de um ID de série de tempo](./time-series-insights-update-how-to-id.md). |
    | **Nome da conta de armazenamento** | Introduza um nome globalmente exclusivo para uma nova conta de armazenamento para criar. |
   
   Selecione **seguinte: Origem do evento**.

   [![Painel para criar um ambiente do Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Sobre o **origem do evento** separador, defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | --- |
   | **Criar uma origem de evento?** | Selecione **Sim**.|
   | **Nome** | Introduza um valor exclusivo para o nome de origem do evento. |
   | **Tipo de origem** | Selecione **IoT Hub**. |
   | **Selecione um hub** | Escolher **selecionar existente**. |
   | **Subscrição** | Selecione a subscrição que utilizou para o simulador de dispositivos. |
   | **Nome do IoT Hub** | Selecione o nome do hub IoT que criou para o simulador de dispositivos. |
   | **Política de acesso do IoT Hub** | Selecione **iothubowner**. |
   | **Grupo de consumidores do IoT Hub** | Selecione **New**, introduza um nome exclusivo e, em seguida, selecione **Add**. O grupo de consumidores tem de ser um valor exclusivo na pré-visualização do Azure Time Series Insights. |
   | **Propriedade Timestamp** | Este valor é utilizado para identificar os **Timestamp** propriedade nos seus dados de telemetria de entrada. Para este tutorial, deixe esta caixa em branco. Este simulador utiliza o carimbo de hora de entrada do IoT Hub, que assume a predefinição Time Series Insights. |

   Selecione **Rever + criar**.

   [![Configurar uma origem de evento](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Sobre o **rever + criar** separador reveja as seleções e, em seguida, selecione **criar**.

    [![Rever + criar página, com o botão Criar](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Pode ver o estado da implementação:

    [![Notificação de que a implementação estiver concluída](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Terá acesso ao seu ambiente de pré-visualização do Azure Time Series Insights, se for o proprietário do inquilino. Para se certificar-se de que tem acesso:

   1. Procure o grupo de recursos e, em seguida, selecione o seu ambiente de pré-visualização do Azure Time Series Insights:

      [![Ambiente selecionado](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na página de pré-visualização do Azure Time Series Insights, selecione **políticas de acesso de dados**:

      [![Políticas de acesso de dados](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Certifique-se de que as suas credenciais estão listadas:

      [![Credenciais listadas](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Se as suas credenciais não estão listadas, tem de conceder por conta própria permissão para aceder ao ambiente. Para saber mais sobre como definir permissões, leia [conceder acesso a dados](./time-series-insights-data-access.md).

## <a name="stream-data-into-your-environment"></a>Dados de Stream no seu ambiente

1. Navegue de volta para o [página de Aceleradores de solução de IoT do Azure](https://www.azureiotsolutions.com/Accelerators). Localize a sua solução no seu dashboard de acelerador de solução. Em seguida, selecione **inicie**:

    [![Iniciar a solução de simulação do dispositivo](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Está redirecionado para o **simulação de dispositivo do Microsoft Azure IoT** página. No canto superior direito da página, selecione **simulação novo**.

    [![Página de simulação de IoT do Azure](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Na **programa de configuração de simulação** painel, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Introduza um nome exclusivo para um simulador. |
    | **Descrição** | Introduza uma definição. |
    | **Duração da simulação** | Defina como **forem executados indefinidamente**. |
    | **Modelo do dispositivo** | **Nome**: Enter **Chiller**. <br />**Quantidade**: Enter **3**. |
    | **Hub IoT de destino** | Defina como **utilização previamente aprovisionadas IoT Hub**. |

    [![Parâmetros para definir](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Selecione **iniciar simulação**.

    No painel de simulação de dispositivo, tenha em atenção as informações apresentadas para **dispositivos ativos** e **mensagens por segundo**.

    [![Painel de simulação de IoT do Azure](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data-in-your-environment"></a>Analisar dados no seu ambiente

Nesta seção, efetuar análise básica em seus dados de séries de tempo ao utilizar o [Explorador de pré-visualização do Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Aceda ao seu Explorador de pré-visualização do Azure Time Series Insights, ao selecionar o URL da página de recursos no [portal do Azure](https://portal.azure.com/).

    [![O URL de Explorador do Time Series Insights pré-visualização](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. No explorer, selecione o **instâncias de série de tempo** nó para ver todas as instâncias no ambiente de pré-visualização do Azure Time Series Insights.

    [![Lista de instâncias unparented](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Selecione a primeira instância de série de tempo. Em seguida, selecione **Mostrar pressão**.

    [![Selecionado a instância de série de tempo com o comando de menu para mostrar a pressão média](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    É apresentado um gráfico de série de tempo. Alteração da **intervalo** ao **15s**.

    [![Gráfico de série de tempo](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Repita os passos 3 com as outras duas instâncias da série de tempo. Pode ver todas as instâncias de série de tempo, conforme mostrado neste gráfico:

    [![Gráfico para todas as séries de tempo](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Na **período de tempo** opção caixa, modifique o intervalo de tempo para ver as tendências de séries de tempo na última hora:

    [![Defina o intervalo de tempo para uma hora](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta seção, aplica um modelo para estruturar os dados. Para concluir o modelo, define os tipos, hierarquias e instâncias. Para saber mais sobre a modelação de dados, veja [modelo de série de tempo](./time-series-insights-update-tsm.md).

1. No explorer, selecione o **modelo** separador:

   [![Separador de modelo no explorer](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Selecione **adicionar** para adicionar um tipo:

   [![Botão Adicionar para tipos](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Em seguida, define três variáveis para o tipo: *pressão*, *temperatura*, e *humidade*. Na **adicionar um tipo de** painel, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome** | Introduza **Chiller**. |
    | **Descrição** | Introduza **esta é uma definição de tipo de Chiller**. |

   * Para definir *pressão*, em **variáveis**, defina os seguintes parâmetros:

     | Parâmetro | Ação |
     | --- | ---|
     | **Nome** | Introduza **pressão média**. |
     | **Valor** | Selecione **pressão (Double)** . Poderá demorar alguns minutos **valor** para ser preenchido automaticamente depois de pré-visualização do Azure Time Series Insights começa a receber eventos. |
     | **Operação de agregação** | Selecione **média**. |

      [![Seleções para a definição de pressão](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Para adicionar a seguinte variável, selecione **adicionar variável**.

   * Definir *temperatura*:

     | Parâmetro | Ação |
     | --- | ---|
     | **Nome** | Introduza **temperatura média**. |
     | **Valor** | Selecione **temperatura (Double)** . Poderá demorar alguns minutos **valor** para ser preenchido automaticamente depois de pré-visualização do Azure Time Series Insights começa a receber eventos. |
     | **Operação de agregação** | Selecione **média**.|

      [![Seleções para definir a temperatura](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Para adicionar a seguinte variável, selecione **adicionar variável**.

   * Definir *humidade*:

      | | |
      | --- | ---|
      | **Nome** | Introduza **máximo de humidade** |
      | **Valor** | Selecione **humidade (Double)** . Poderá demorar alguns minutos **valor** para ser preenchido automaticamente depois de pré-visualização do Azure Time Series Insights começa a receber eventos. |
      | **Operação de agregação** | Selecione **MAX**.|

      [![Seleções para definir a temperatura](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Selecione **Criar**.

    Pode ver o tipo que adicionou:

    [![Informações sobre o tipo foi adicionado](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. A próxima etapa é adicionar uma hierarquia. Sob **hierarquias**, selecione **Add**:

    [![Separador de hierarquias com o botão Adicionar](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Na **Editar hierarquia** painel, defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | ---|
   | **Nome** | Introduza **hierarquia de localização**. |
   | **Nível 1** | Introduza **país**. |
   | **Nível 2** | Introduza **Cidade**. |
   | **Nível 3** | Introduza **modulares**. |

   Selecione **Guardar**.

    [![Campos de hierarquia com o botão Criar](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Pode ver a hierarquia que criou:

    [![Informações sobre a hierarquia](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Selecione **instâncias**. Selecione a primeira instância e, em seguida, selecione **editar**:

    [![Selecionar o botão Editar para uma instância](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. Na **editar instâncias** painel, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Chiller**. |
    | **Descrição** | Introduza **instância para Chiller 01.1**. |
    | **Hierarquias** | Selecione **hierarquia de localização**. |
    | **País** | Introduza **EUA**. |
    | **Cidade** | Introduza **Seattle**. |
    | **Criando** | Introduza **espaço agulha**. |

    [![Campos de instância com o botão salvar](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Selecione **Guardar**.

1. Repita o passo anterior para os outros sensores. Atualize os valores seguintes:

   * Para Chiller 01.2:

     | Parâmetro | Ação |
     | --- | --- |
     | **Tipo** | Selecione **Chiller**. |
     | **Descrição** | Introduza **instância para Chiller 01.2**. |
     | **Hierarquias** | Selecione **hierarquia de localização**. |
     | **País** | Introduza **EUA**. |
     | **Cidade** | Introduza **Seattle**. |
     | **Criando** | Introduza **Pacífico ciência Center**. |

   * Para Chiller 01.3:

     | Parâmetro | Ação |
     | --- | --- |
     | **Tipo** | Selecione **Chiller**. |
     | **Descrição** | Introduza **instância para Chiller 01.3**. |
     | **Hierarquias** | Selecione **hierarquia de localização**. |
     | **País** | Introduza **EUA**. |
     | **Cidade** | Introduza **Nova Iorque**. |
     | **Criando** | Introduza **modulares de estado do Império**. |

1. Selecione o **Analyze** separador e, em seguida, atualize a página. Sob **hierarquia de localização**, expanda a todos os níveis de hierarquia para apresentar as instâncias de série de tempo:

   [![O separador de analisar](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Para explorar as instâncias de série de tempo na última hora, altere **vezes rápidas** ao **última hora**:

    [![A caixa de horas rápidas, com a última hora selecionada](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Sob **Centro de ciência do Pacífico**, selecione a instância de série de tempo e, em seguida, selecione **Mostrar humidade de Max**.

    [![Instância de série de tempo selecionado e a seleção de menu Mostrar humidade de máx.](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. A série de tempo para **humidade máxima** com um tamanho do intervalo de **1 minuto** abre. Para filtrar um intervalo, selecione uma região. Para analisar eventos no período de tempo, com o botão direito do gráfico e, em seguida, selecione **Zoom**:

   [![Intervalo selecionado com o comando de Zoom num menu de atalho](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Para ver detalhes do evento, selecione uma região e, em seguida, clique com botão direito do gráfico:

   [![Lista detalhada de eventos](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:  

> [!div class="checklist"]
> * Criar e utilizar um acelerador de simulação do dispositivo.
> * Crie um ambiente do Azure Time Series Insights pré-visualização PAYG.
> * Ligar o ambiente de pré-visualização do Azure Time Series Insights para um hub de eventos.
> * Execute um exemplo de acelerador de solução para transmitir dados para o ambiente de pré-visualização do Azure Time Series Insights.
> * Execute uma análise básica dos dados.
> * Definir um tipo de modelo de série de tempo e a hierarquia e associá-las com suas instâncias.

Agora que sabe como criar seu próprio ambiente de pré-visualização do Azure Time Series Insights, saiba mais sobre os conceitos fundamentais do Azure Time Series Insights.

Leia sobre a configuração de armazenamento do Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Armazenamento de pré-visualização do Time Series Insights do Azure e de entrada](./time-series-insights-update-storage-ingress.md)

Saiba mais sobre os modelos de série de tempo:

> [!div class="nextstepaction"]
> [Modelação de dados do Time Series Insights pré-visualização do Azure](./time-series-insights-update-tsm.md)

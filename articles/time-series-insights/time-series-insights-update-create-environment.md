---
title: 'Tutorial: Configurar um ambiente de pré-visualização do Azure Time Series Insights | Documentos da Microsoft'
description: Saiba como configurar o ambiente de pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: f83063a88207f51f9d481447923fd8a8498692a2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713905"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: Configurar um ambiente de pré-visualização do Azure Time Series Insights

Este tutorial orienta-o ao longo do processo de criação de um ambiente de pay as you go (PAYG) de pré-visualização do Azure Time Series Insights. Neste tutorial, ficará a saber como:

* Crie um ambiente de pré-visualização do Azure Time Series Insights.
* Ligar o ambiente de pré-visualização do Azure Time Series Insights para um hub de eventos nos Hubs de eventos do Azure.
* Execute um exemplo de acelerador de solução para transmitir dados para o ambiente de pré-visualização do Azure Time Series Insights.
* Execute análise básica nos dados.
* Definir um tipo de modelo de série de tempo e a hierarquia e associá-lo a suas instâncias.

# <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Nesta secção, vai criar três dispositivos simulados que irão enviar dados para um hub IoT.

1. Vá para o [página de Aceleradores de solução de IoT do Azure](https://www.azureiotsolutions.com/Accelerators). A página apresenta vários exemplos pré-criados. Inicie sessão com a sua conta do Azure. Em seguida, selecione **simulação do dispositivo**.

   [![Página de Aceleradores de solução de IoT do Azure](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Selecione **Experimente agora o**.

1. Introduza os parâmetros necessários na página **Criar solução de Simulação de Dispositivos**:

   | Parâmetro | Descrição |
   | --- | --- |
   | **Nome da solução** |    Introduza um valor exclusivo para a criação de um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos. |
   | **Subscrição** | Especifique a mesma subscrição utilizada para a criação de seu ambiente do Time Series Insights. |
   | **Região** |   Especifica a mesma região utilizada para a criação de seu ambiente do Time Series Insights. |
   | **Implementar Recursos do Azure opcionais**    | Deixe o Hub IoT selecionado, porque os dispositivos simulados irão utilizá-lo para se ligar e transmitir dados em fluxo. |

   Em seguida, selecione **criar solução**. Aguarde 10 a 15 minutos para a sua solução a serem implantados.

   [![Criar a página de solução de simulação do dispositivo](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. No seu dashboard de acelerador de solução, selecione o **inicie** botão:

   [![Iniciar a solução de simulação do dispositivo](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Está redirecionado para o **simulação de dispositivo do Microsoft Azure IoT** página. Selecione **+ nova simulação** no canto superior direito da página.

   [![Página de simulação de IoT do Azure](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Preencha os parâmetros necessários da seguinte forma:

    [![Parâmetros para preencher](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    |||
    | --- | --- |
    | **Nome** | Introduza um nome exclusivo para um simulador. |
    | **Descrição** | Introduza uma definição. |
    | **Duração da simulação** | Defina como **forem executados indefinidamente**. |
    | **Modelo do dispositivo** | **Nome**: Introduza `Chiller`. </br>**Quantidade**: Introduza `3`. |
    | **Hub IoT de destino** | Defina como **utilização previamente aprovisionadas IoT Hub**. |

    Em seguida, selecione **iniciar simulação**.

1. No painel de simulação de dispositivo, consulte **dispositivos ativos** e **mensagens por segundo**.

    [![Painel de simulação de IoT do Azure](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>Propriedades de simulação do dispositivo de lista

Antes de criar um ambiente do Azure Time Series Insights, terá dos nomes do hub IoT, subscrição e grupo de recursos.

1. Vá para o dashboard de acelerador de solução e inicie sessão com a mesma conta de subscrição do Azure. Encontre a simulação de dispositivo que criou nos passos anteriores.

1. Selecione o simulador de dispositivos e selecione **inicie**. Selecione o **Portal de gestão do Azure** ligação no lado direito.

    [![Listagens de simulador](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. Tome nota do hub IoT, subscrição e os nomes de grupo de recursos.

    [![Portal do Azure](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Criar um ambiente de PAYG de pré-visualização do Time Series Insights

Esta secção descreve como criar um ambiente de pré-visualização do Azure Time Series Insights com o [portal do Azure](https://portal.azure.com/).

1. Inicie sessão no portal do Azure com a sua conta de subscrição.

1. Selecione **criar um recurso**.

1. Selecione o **Internet das coisas** categoria e, em seguida, selecione **Time Series Insights**.

   [![Selecione a Internet das coisas e, em seguida, selecione o Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Preencha os campos na página da seguinte forma:

   | | |
   | --- | ---|
   | **Nome do ambiente** | Escolha um nome exclusivo para o ambiente de pré-visualização do Azure Time Series Insights. |
   | **Subscrição** | Introduza a sua subscrição em que pretende criar o ambiente de pré-visualização do Azure Time Series Insights. É melhor prática para utilizar a mesma subscrição que o resto dos seus recursos de IoT criados pelo simulador de dispositivos. |
   | **Grupo de recursos** | Um grupo de recursos é um contentor de recursos do Azure. Escolha um grupo de recursos existente ou crie um novo, para o recurso de ambiente de pré-visualização do Azure Time Series Insights. É uma prática recomendada usar o mesmo grupo de recursos que o resto dos seus recursos de IoT criados pelo simulador de dispositivos. |
   | **Localização** | Escolha uma região de datacenter para o seu ambiente de pré-visualização do Azure Time Series Insights. Para evitar os custos de largura de banda adicional e latência, é melhor manter o ambiente de pré-visualização do Azure Time Series Insights na mesma região que outros recursos de IoT. |
   | **Escalão** |  Selecione **PAYG**, que significa pay as you go. Este é o SKU para o produto de pré-visualização do Azure Time Series Insights. |
   | **ID de propriedade** | Digite algo que identifica exclusivamente a sua série de tempo. Tenha em atenção que este campo é imutável e não é possível alterar mais tarde. Neste tutorial, utilize `iothub-connection-device-id`. Para saber mais sobre o ID de série de tempo, leia [como escolher um ID de série de tempo](./time-series-insights-update-how-to-id.md). |
   | **Nome da conta de armazenamento** | Introduza um nome globalmente exclusivo para uma nova conta de armazenamento a ser criada. |

   Em seguida, selecione **seguinte: Origem do evento**.

   [![Página para a criação de um ambiente do Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na página para a origem do evento, preencha os campos da seguinte forma:

   | | |
   | --- | --- |
   | **Criar uma origem de evento?** | Introduza `Yes`.|
   | **Nome** | Introduza um valor exclusivo que é usado para nomear a origem do evento.|
   | **Tipo de origem** | Selecione **IoT Hub**. |
   | **Selecione um hub?** | Selecione **selecionar existente**. |
   | **Subscrição** | Selecione a subscrição utilizada para o simulador de dispositivos. |
   | **Nome do IoT Hub** | Selecione o nome do hub IoT que criou para o simulador de dispositivos. |
   | **Política de acesso do IoT Hub** | Selecione **iothubowner**. |
   | **Grupo de consumidores do IOT Hub** | Precisa de um grupo de consumidores exclusivo para a pré-visualização do Azure Time Series Insights. Selecione **New**, introduza um nome exclusivo e, em seguida, selecione **Add**. |
   | **Propriedade Timestamp** | Este campo é utilizado para identificar a propriedade timestamp nos seus dados de telemetria de entrada. Para este tutorial, não preencha o campo. Este simulador utiliza o carimbo de hora de entrada do IoT Hub, que assume a predefinição Time Series Insights.|

   Em seguida, selecione **rever + criar**.

   [![Configurar uma origem de evento](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Reveja todos os campos na página de revisão e selecione **criar**.

   [![Rever + criar página, com o botão Criar](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

1. Pode ver o estado da implementação.

   [![Notificação de que a implementação estiver concluída](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Deve receber acesso ao seu ambiente de pré-visualização do Azure Time Series Insights, se for o proprietário do inquilino. Para se certificar-se de que tem acesso:

   a. Procure o grupo de recursos e selecione o seu ambiente de pré-visualização do Azure Time Series Insights:

      [![Ambiente selecionado](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   b. Na página de pré-visualização do Azure Time Series Insights, vá para **políticas de acesso de dados**.

     [![Políticas de acesso de dados](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   c. Certifique-se de que as suas credenciais estão listadas.

     [![Credenciais listadas](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Se as suas credenciais não estão listadas, terá de dar permissão para aceder ao ambiente. Para saber mais sobre como definir permissões, leia [conceder acesso a dados](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analisar dados no seu ambiente

Nesta seção, efetuar análise básica em seus dados de séries de tempo ao utilizar o [Explorador de pré-visualização do Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Aceda ao seu Explorador de pré-visualização do Azure Time Series Insights, ao selecionar o URL da página de recursos no [portal do Azure](https://portal.azure.com/).

   [![O URL de Explorador do Time Series Insights pré-visualização](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. No explorer, selecione o **instâncias de série de tempo** nó para ver todas as instâncias no ambiente de pré-visualização do Azure Time Series Insights.

   [![Lista de instâncias unparented](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Na série de tempo apresentada, selecione a primeira instância. Em seguida, selecione **pressão média mostrar**.

   [![Instância selecionada com o comando de menu para mostrar a pressão média](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

   Um gráfico de série de tempo deve aparecer no lado direito. Ajustar a **intervalo** para `15s`.

   [![Gráfico de série de tempo](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Repita **passo 3** com as outras duas séries de tempo. Em seguida, pode ver todas as séries de tempo, conforme mostrado neste gráfico:

   [![Gráfico para todas as séries de tempo](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Modificar o intervalo de tempo para ver as tendências de séries de tempo na última hora.

   a. Selecione o **período de tempo** caixa da opção:

      [![Defina o intervalo de tempo para uma hora](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta seção, aplica um modelo para estruturar os dados. Para concluir o modelo, define os tipos, hierarquias e instâncias. Para saber mais sobre a modelação de dados, aceda a [modelo de série de tempo](./time-series-insights-update-tsm.md).

1. No explorer, selecione o **modelo** separador:

   [![Separador de modelo no explorer](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Selecione **+ adicionar** para adicionar um tipo. No lado direito, abre-se um editor de tipos.

   [![Botão Adicionar para tipos](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Definir três variáveis para o tipo: pressão, temperatura e humidade. Introduza as seguintes informações:

   | | |
   | --- | ---|
   | **Nome** | Introduza `Chiller`. |
   | **Descrição** | Introduza `This is a type definition of Chiller`. |

   * Defina pressão com três variáveis:

      | | |
      | --- | ---|
      | **Nome** | Introduza `Avg Pressure`. |
      | **Valor** | Selecione **pressão (Double)**. Tenha em atenção que este campo poderá demorar alguns minutos a ser preenchido depois de pré-visualização do Azure Time Series Insights começa a receber eventos. |
      | **Operação de agregação** | Selecione **média**. |

      [![Seleções para a definição de pressão](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Selecione **+ adicionar variável** para adicionar a seguinte variável.

   * Defina a temperatura:

      | | |
      | --- | ---|
      | **Nome** | Introduza `Avg Temperature`. |
      | **Valor** | Selecione **temperatura (Double)**. Tenha em atenção que este campo poderá demorar alguns minutos a ser preenchido depois de pré-visualização do Azure Time Series Insights começa a receber eventos. |
      | **Operação de agregação** | Selecione **média**.|

      [![Seleções para definir a temperatura](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

   * Defina humidade:

      | | |
      | --- | ---|
      | **Nome** | Enter `Max Humidity` |
      | **Valor** | Selecione **humidade (Double)**. Tenha em atenção que este campo poderá demorar alguns minutos a ser preenchido depois de pré-visualização do Azure Time Series Insights começa a receber eventos. |
      | **Operação de agregação** | Selecione **MAX**.|

      [![Seleções para definir a temperatura](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

   Em seguida, selecione **Criar**.

1. Pode ver o seu tipo adicionado:

   [![Informações sobre o tipo foi adicionado](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. A próxima etapa é adicionar uma hierarquia. Na **hierarquias** secção, selecione **+ adicionar**:

   [![Separador de hierarquias com o botão Adicionar](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Defina a hierarquia. Preencha os campos da seguinte forma:

   | | |
   | --- | ---|
   | **Nome** | Introduza `Location Hierarchy`. |
   | **Nível 1** | Introduza `Country`. |
   | **Nível 2** | Introduza `City`. |
   | **Nível 3** | Introduza `Building`. |

   Em seguida, selecione **Criar**.

   [![Campos de hierarquia com o botão Criar](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

1. Pode ver a hierarquia que criou:

   [![Informações sobre a hierarquia](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Selecione **instâncias** à esquerda. Depois das instâncias de aparecer, selecione a primeira instância e, em seguida, selecione **editar**:

   [![Selecionar o botão Editar para uma instância](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. No lado direito, é apresentado um editor de texto. Adicione as seguintes informações:

   | | |
   | --- | --- |
   | **Tipo** | Selecione **Chiller**. |
   | **Descrição** | Introduza `Instance for Chiller-01.1`. |
   | **Hierarquias** | Selecione **hierarquia de localização**. |
   | **País** | Introduza `USA`. |
   | **Cidade** | Introduza `Seattle`. |
   | **Criando** | Introduza `Space Needle`. |

    Em seguida, selecione **Guardar**.

   [![Campos de instância com o botão Save](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

1. Repita o passo anterior para os outros sensores. Utilize os seguintes campos:

   * Para Chiller 01.2:

     | | |
     | --- | --- |
     | **Tipo** | Selecione **Chiller**. |
     | **Descrição** | Introduza `Instance for Chiller-01.2`. |
     | **Hierarquias** | Selecione **hierarquia de localização**. |
     | **País** | Introduza `USA`. |
     | **Cidade** | Introduza `Seattle`. |
     | **Criando** | Introduza `Pacific Science Center`. |

   * Para Chiller 01.3:

     | | |
     | --- | --- |
     | **Tipo** | Selecione **Chiller**. |
     | **Descrição** | Introduza `Instance for Chiller-01.3`. |
     | **Hierarquias** | Selecione **hierarquia de localização**. |
     | **País** | Introduza `USA`. |
     | **Cidade** | Introduza `New York`. |
     | **Criando** | Introduza `Empire State Building`. |

1. Vá para o **Analyze** separador e atualize a página. Expanda todos os níveis de hierarquia para localizar a série de tempo.

   [![O separador de analisar](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Para explorar a série de tempo na última hora, altere **vezes rápidas** ao **última hora**:

    [![A caixa de horas rápidas, com a última hora selecionada](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Selecione a série de tempo em **Centro de ciência do Pacífico** e selecione **Mostrar humidade de Max**.

    [![Série de tempo selecionado com a seleção de menu Mostrar humidade de máx.](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. A série de tempo para **humidade máxima** com um tamanho do intervalo de **1 minuto** abre. Selecione uma região para filtrar um intervalo. Em seguida, clique com botão direito e selecione **Zoom** para analisar eventos no período de tempo:

   [![Intervalo selecionado com o comando de Zoom num menu de atalho](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Pode também selecionar uma região e, em seguida, clique com botão direito para ver detalhes do evento:

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
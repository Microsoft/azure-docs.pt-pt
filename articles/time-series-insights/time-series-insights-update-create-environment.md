---
title: 'Tutorial: configurar um ambiente de visualização de Azure Time Series Insights | Microsoft Docs'
description: Saiba como configurar seu ambiente no Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/02/2019
ms.custom: seodec18
ms.openlocfilehash: e42f6d7c5e3deff3eb5851f3ea192b4756d2fe04
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553468"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: configurar um ambiente de visualização de Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de um ambiente PAYG (pré-pago) de visualização de Azure Time Series Insights. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um ambiente de visualização de Azure Time Series Insights.
> * Conecte o ambiente de visualização de Azure Time Series Insights a um hub de eventos nos hubs de eventos do Azure.
> * Execute um exemplo do Solution Accelerator para transmitir dados para o ambiente de visualização de Azure Time Series Insights.
> * Execute a análise básica nos dados.
> * Defina um tipo de modelo de série temporal e uma hierarquia e associe-o às suas instâncias.

>[!TIP]
> Os [aceleradores de solução de IOT](https://www.azureiotsolutions.com/Accelerators) fornecem soluções pré-configuradas de nível empresarial que você pode usar para acelerar o desenvolvimento de soluções de IOT personalizadas.

Inscreva-se para obter uma [assinatura gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Sua conta de entrada do Azure também deve ser um membro da função de **proprietário** da assinatura. Para obter mais informações, consulte [gerenciar o acesso usando o controle de acesso baseado em função e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Nesta seção, você cria três dispositivos simulados que enviam dados para uma instância do Hub IoT do Azure.

1. Vá para a [página aceleradores de soluções do Azure IOT](https://www.azureiotsolutions.com/Accelerators). A página exibe vários exemplos predefinidos. Entre usando sua conta do Azure. Em seguida, selecione **simulação de dispositivo**.

   [página de aceleradores de solução ![Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Selecione **experimentar agora**.

1. Na página **criar solução de simulação de dispositivo** , defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome da implantação** | Insira um valor exclusivo para um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos. |
    | **Subscrição do Azure** | Selecione a assinatura que você usou para criar seu ambiente de Time Series Insights. |
    | **Local do Azure** | Selecione a região que você usou para criar seu ambiente de Time Series Insights. |
    | **Opções de implantação** | Selecione **provisionar novo hub IOT**. |
 
    Selecione **criar solução**. Pode levar até 20 minutos para que a solução conclua a implantação.

    [página de solução de simulação de dispositivo ![Create](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Criar um ambiente de PAYG de visualização

Esta seção descreve como criar um ambiente de visualização Azure Time Series Insights e conectá-lo ao Hub IoT criado pelo acelerador de solução IoT usando o [portal do Azure](https://portal.azure.com/).

1. Entre no portal do Azure usando sua conta de assinatura.

1. Selecione **criar um recurso**  > **Internet das coisas**  > **Time Series insights**.

   [![Select Internet das Coisas e, em seguida, selecione Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. No painel **criar Time Series insights ambiente** , na guia **noções básicas** , defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Insira um nome exclusivo para o ambiente de visualização de Azure Time Series Insights. |
    | **Subscrição** | Insira a assinatura na qual você deseja criar o ambiente de visualização de Azure Time Series Insights. Uma prática recomendada é usar a mesma assinatura que o restante dos recursos de IoT criados pelo Device Simulator. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um novo grupo de recursos para o Azure Time Series Insights recurso de ambiente de visualização. Um grupo de recursos é um contentor de recursos do Azure. Uma prática recomendada é usar o mesmo grupo de recursos que os outros recursos de IoT criados pelo Device Simulator. |
    | **Localização** | Selecione uma região de datacenter para seu ambiente de Azure Time Series Insights visualização. Para evitar latência adicional, é melhor criar seu Azure Time Series Insights ambiente de visualização na mesma região que os outros recursos de IoT. |
    | **Preço** |  Selecione **PAYG** (pré-*pago*). Este é o SKU para o produto de visualização de Azure Time Series Insights. |
    | **ID da propriedade** | Insira um valor que identifique exclusivamente sua instância de série temporal. O valor que você insere na caixa **ID da propriedade** é imutável. Você não pode alterá-lo mais tarde. Para este tutorial, digite **iothub-Connection-Device-ID**. Para saber mais sobre a ID da série temporal, consulte [práticas recomendadas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md). |
    | **Nome da conta de armazenamento** | Insira um nome globalmente exclusivo para criar uma nova conta de armazenamento. |
   
   Selecione **Avançar: origem do evento**.

   [![Pane para criar um ambiente de Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na guia **origem do evento** , defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | --- |
   | **Criar uma origem do evento?** | Selecione **Sim**.|
   | **Nome** | Insira um valor exclusivo para o nome de origem do evento. |
   | **Tipo de origem** | Selecione **Hub IOT**. |
   | **Selecionar um hub** | Escolha **selecionar existente**. |
   | **Subscrição** | Selecione a assinatura que você usou para o simulador de dispositivo. |
   | **Nome do Hub IoT** | Selecione o nome do Hub IoT que você criou para o simulador de dispositivo. |
   | **Política de acesso do Hub IoT** | Selecione **iothubowner**. |
   | **Grupo de consumidores do Hub IoT** | Selecione **novo**, insira um nome exclusivo e, em seguida, selecione **Adicionar**. O grupo de consumidores deve ser um valor exclusivo na visualização Azure Time Series Insights. |
   | **Propriedade timestamp** | Esse valor é usado para identificar a propriedade **timestamp** em seus dados de telemetria recebidos. Para este tutorial, deixe essa caixa vazia. Esse simulador usa o carimbo de data/hora de entrada do Hub IoT, que Time Series Insights usa como padrão. |

   Selecione **Rever + criar**.

   [![Configure uma origem do evento](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Na guia **revisar + criar** , revise suas seleções e, em seguida, selecione **criar**.

    [![Review + criar página, com o botão criar](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Você pode ver o status da sua implantação:

    [![Notification que a implantação foi concluída](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Você tem acesso ao ambiente de visualização de Azure Time Series Insights se possui o locatário. Para certificar-se de que você tem acesso:

   1. Pesquise seu grupo de recursos e selecione seu Azure Time Series Insights ambiente de visualização:

      [ambiente de ![Selected](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na página Azure Time Series Insights visualização, selecione **políticas de acesso a dados**:

      [políticas de acesso de ![Data](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Verifique se suas credenciais estão listadas:

      [![Listed credenciais](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Se suas credenciais não estiverem listadas, você deverá conceder permissão a si mesmo para acessar o ambiente. Para saber mais sobre como definir permissões, leia [conceder acesso a dados](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Dados de transmissão em fluxo

Agora que você implantou seu ambiente de Time Series Insights, transmita dados para ele para análise.

1. Navegue de volta para a [página aceleradores de solução do Azure IOT](https://www.azureiotsolutions.com/Accelerators). Localize sua solução no seu painel do Solution Accelerator. Em seguida, selecione **Iniciar**:

    [![Launch a solução de simulação de dispositivo](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Você será redirecionado para a página de **simulação de dispositivo do Microsoft Azure IOT** . No canto superior direito da página, selecione **nova simulação**.

    [página de simulação de IoT ![Azure](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. No painel **configuração de simulação** , defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira um nome exclusivo para um simulador. |
    | **Descrição** | Insira uma definição. |
    | **Duração da simulação** | Defina para **executar indefinidamente**. |
    | **Modelo do dispositivo** | **Nome**: Insira o **resfriador**. <br />**Valor**: insira **3**. |
    | **Hub IoT de destino** | Defina para **usar o Hub IOT provisionado previamente**. |

    [![Parameters para definir](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Selecione **Iniciar simulação**.

    No painel simulação de dispositivo, observe as informações mostradas para **dispositivos** e **mensagens ativos por segundo**.

    [painel de simulação de IoT ![Azure](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analise os dados

Nesta seção, você executará a análise básica nos dados de série temporal usando o [Gerenciador de visualização Azure Time Series insights](./time-series-insights-update-explorer.md).

1. Acesse o Azure Time Series Insights Explorer Preview selecionando a URL na página de recursos no [portal do Azure](https://portal.azure.com/).

    [![The Time Series Insights URL do Gerenciador de visualização](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. No Gerenciador, selecione o nó **instâncias de série temporal** para ver todas as instâncias de visualização de Azure Time Series insights no ambiente.

    [![List de instâncias não pai](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Selecione a primeira instância de série temporal. Em seguida, selecione **Mostrar pressão**.

    [instância de série temporal ![Selected com comando de menu para mostrar a pressão média](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Um gráfico de série temporal é exibido. Altere o **intervalo** para **15s**.

    [gráfico da série ![Time](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Repita a etapa 3 com as outras duas instâncias de série temporal. Você pode exibir todas as instâncias de série temporal, conforme mostrado neste gráfico:

    [![Chart para todas as séries temporais](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Na caixa de opção **período** , modifique o intervalo de tempo para ver as tendências de série temporal na última hora:

    [![Set o intervalo de tempo para uma hora](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta seção, você aplica um modelo para estruturar seus dados. Para concluir o modelo, você define tipos, hierarquias e instâncias. Para saber mais sobre modelagem de dados, consulte [modelo de série temporal](./time-series-insights-update-tsm.md).

1. No Gerenciador, selecione a guia **modelo** :

   [guia ![Model no Gerenciador](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Selecione **Adicionar** para adicionar um tipo:

   [![The botão Adicionar para tipos](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Em seguida, você define três variáveis para o tipo: *pressão*, *temperatura*e *umidade*. No painel **Adicionar um tipo** , defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome** | Insira o **resfriador**. |
    | **Descrição** | Insira **essa é uma definição de tipo de resfriador**. |

   * Para definir a *pressão*, em **variáveis**, defina os seguintes parâmetros:

     | Parâmetro | Ação |
     | --- | ---|
     | **Nome** | Insira a **pressão Méd**. |
     | **Valor** | Selecione **pressão (dupla)** . Pode levar alguns minutos para que o **valor** seja preenchido automaticamente depois que Azure Time Series insights visualização começar a receber eventos. |
     | **Operação de agregação** | Selecione **Méd**. |

      [![Selections para definir a pressão](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Para adicionar a próxima variável, selecione **Adicionar variável**.

   * Definir *temperatura*:

     | Parâmetro | Ação |
     | --- | ---|
     | **Nome** | Insira a **temperatura média**. |
     | **Valor** | Selecione **temperatura (dupla)** . Pode levar alguns minutos para que o **valor** seja preenchido automaticamente depois que Azure Time Series insights visualização começar a receber eventos. |
     | **Operação de agregação** | Selecione **Méd**.|

      [![Selections para definir a temperatura](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Para adicionar a próxima variável, selecione **Adicionar variável**.

   * Definir *umidade*:

      | | |
      | --- | ---|
      | **Nome** | Inserir **umidade máxima** |
      | **Valor** | Selecione **umidade (duplo)** . Pode levar alguns minutos para que o **valor** seja preenchido automaticamente depois que Azure Time Series insights visualização começar a receber eventos. |
      | **Operação de agregação** | Selecione **máximo**.|

      [![Selections para definir a temperatura](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Selecione **Criar**.

    Você pode ver o tipo adicionado:

    [![Information sobre o tipo adicionado](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. A próxima etapa é adicionar uma hierarquia. Em **hierarquias**, selecione **Adicionar**:

    [guia ![Hierarchies com o botão Adicionar](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. No painel **Editar hierarquia** , defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | ---|
   | **Nome** | Insira a **hierarquia de localização**. |
   | **Nível 1** | Insira o **país**. |
   | **Nível 2** | Insira a **cidade**. |
   | **Nível 3** | Insira a **compilação**. |

   Selecione **Guardar**.

    [![Hierarchy campos com o botão criar](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Você pode ver a hierarquia que você criou:

    [![Information sobre a hierarquia](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Selecione **instâncias**. Selecione a primeira instância e, em seguida, selecione **Editar**:

    [![Selecting o botão Editar para uma instância](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. No painel **Editar instâncias** , defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **resfriador**. |
    | **Descrição** | Insira a **instância para resfriador-1,1**. |
    | **Hierarquias** | Selecione **hierarquia de local**. |
    | **País** | Insira os **EUA**. |
    | **Azul** | Digite **Seattle**. |
    | **Elaboração** | Inserir a **agulha de espaço**. |

    [![Instance campos com o botão salvar](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Selecione **Guardar**.

1. Repita a etapa anterior para os outros sensores. Atualize os seguintes valores:

   * Para resfriador 1,2:

     | Parâmetro | Ação |
     | --- | --- |
     | **Tipo** | Selecione **resfriador**. |
     | **Descrição** | Insira a **instância para resfriador-1,2**. |
     | **Hierarquias** | Selecione **hierarquia de local**. |
     | **País** | Insira os **EUA**. |
     | **Azul** | Digite **Seattle**. |
     | **Elaboração** | Insira o **Pacífico Science Center**. |

   * Para resfriador 1,3:

     | Parâmetro | Ação |
     | --- | --- |
     | **Tipo** | Selecione **resfriador**. |
     | **Descrição** | Insira a **instância para resfriador-1,3**. |
     | **Hierarquias** | Selecione **hierarquia de local**. |
     | **País** | Insira os **EUA**. |
     | **Azul** | Insira **Nova York**. |
     | **Elaboração** | Insira a **criação do estado do Empire**. |

1. Selecione a guia **analisar** e, em seguida, atualize a página. Em **hierarquia de local**, expanda todos os níveis de hierarquia para exibir as instâncias de série temporal:

   [guia ![The analisar](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Para explorar as instâncias de série temporal na última hora, altere as **horas rápidas** para a **última hora**:

    [![The caixa de horários rápidos, com a última hora selecionada](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. No **Pacífico Science Center**, selecione a instância série temporal e, em seguida, selecione **Mostrar umidade máxima**.

    [![Selected instância de série temporal e a seleção de menu Mostrar umidade máxima](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. A série temporal para o **umidade máxima** com um tamanho de intervalo de **1 minuto** é aberta. Para filtrar um intervalo, selecione uma região. Para analisar eventos no período de tempo, clique com o botão direito do mouse no gráfico e selecione **zoom**:

   [![Selected intervalo com o comando de zoom em um menu de atalho](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Para ver os detalhes do evento, selecione uma região e clique com o botão direito do mouse no gráfico:

   [![Detailed lista de eventos](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)


## <a name="clean-up-resources"></a>Limpar recursos

Agora que você concluiu o tutorial, limpe os recursos que criou:

1. No menu à esquerda na [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, localize o grupo de recursos Azure Time Series insights.
1. Exclua todo o grupo de recursos (e todos os recursos contidos nele) selecionando **excluir** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:  

> [!div class="checklist"]
> * Crie e use um acelerador de simulação de dispositivo.
> * Crie um ambiente de PAYG de visualização de Azure Time Series Insights.
> * Conecte o ambiente de visualização de Azure Time Series Insights a um hub de eventos.
> * Execute um exemplo do Solution Accelerator para transmitir dados para o ambiente de visualização de Azure Time Series Insights.
> * Execute uma análise básica dos dados.
> * Defina um tipo de modelo de série temporal e uma hierarquia e associe-os às suas instâncias.

Agora que você sabe como criar seu próprio ambiente de visualização Azure Time Series Insights, saiba mais sobre os principais conceitos em Azure Time Series Insights.

Leia sobre a configuração de armazenamento Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Visualizar o armazenamento e a entrada](./time-series-insights-update-storage-ingress.md)

Saiba mais sobre os modelos de série temporal:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Visualizar a modelagem de dados](./time-series-insights-update-tsm.md)

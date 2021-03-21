---
title: 'Tutorial: Criar um ambiente Gen2 - Azure Time Series Insights Gen2| Microsoft Docs'
description: 'Tutorial: Saiba como configurar um ambiente em Azure Time Series Insights Gen2.'
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/25/2021
ms.custom: seodec18
ms.openlocfilehash: 76a33bdb773645c9e8f97a47b1378d813b165631
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464675"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Tutorial: Criar um ambiente Azure Time Series Insights Gen2

Este tutorial guia-o através do processo de criação de um ambiente Azure Time Series Insights Gen2 *pay-as-you-go* (PAYG).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um ambiente Azure Time Series Insights Gen2.
> * Ligue o ambiente Azure Time Series Insights Gen2 a um hub IoT.
> * Executar uma amostra de acelerador de solução para transmitir dados para o ambiente Azure Time Series Insights Gen2.
> * Realizar análises básicas sobre os dados.
> * Defina um tipo e hierarquia de modelo de séries de tempo e associe-o às suas instâncias.

>[!TIP]
> [Os aceleradores de soluções IoT](https://www.azureiotsolutions.com/Accelerators) fornecem soluções pré-configuradas de nível empresarial que pode usar para acelerar o desenvolvimento de soluções IoT personalizadas.

Inscreva-se para uma [subscrição gratuita do Azure](https://azure.microsoft.com/free/) se ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* No mínimo, deve ter o **papel de Contribuinte** para a subscrição do Azure. Para mais informações, leia [as funções De Atribuir Azure utilizando o portal Azure](../role-based-access-control/role-assignments-portal.md).

* Crie um ambiente utilizando o [portal Azure](#create-an-azure-time-series-insights-gen2-environment) ou [o CLI](how-to-create-environment-using-cli.md).

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Nesta secção, irá criar três dispositivos simulados que enviam dados para uma instância do Azure IoT Hub.

1. Aceda à página de [aceleradores de solução Azure IoT](https://www.azureiotsolutions.com/Accelerators). Inscreva-se utilizando a sua conta Azure e, em seguida, selecione **Simulação do Dispositivo**.

   [![Página de aceleradores de solução Azure IoT.](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Desloque-se para baixo para ler as secções [Overview](https://github.com/Azure/azure-iot-pcs-device-simulation#overview) e [Getting start.](https://github.com/Azure/azure-iot-pcs-device-simulation#getting-started)

1. Siga as [instruções de implantação](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/deployment/README.md) na secção Iniciar.

   Pode levar até 20 minutos para concluir este processo.

1. Quando a implementação terminar, será fornecido o URL à sua simulação. Mantenha esta página aberta porque voltará mais tarde.

   >[!IMPORTANT]
   > Não entre no seu acelerador de solução ainda! Mantenha esta página web aberta porque voltará mais tarde.

   [![Solução de simulação do dispositivo completa.](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png)](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png#lightbox)

1. Agora, inspecione os recursos recém-criados no portal Azure. Na página **dos grupos de recursos,** note que um novo grupo de recursos foi criado utilizando o `solutionName` que forneceu no seu ficheiro de parâmetros do modelo ARM. Tome nota dos recursos que foram criados para a simulação do dispositivo.

   [![Recursos de simulação de dispositivo.](media/tutorial-set-up-environment/device-sim-solution-resources.png)](media/tutorial-set-up-environment/device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Criar um ambiente Azure Time Series Insights Gen2

Esta secção descreve como criar um ambiente Azure Time Series Insights Gen2 e conectá-lo ao hub IoT criado pelo Acelerador de Solução IoT utilizando o [portal Azure](https://portal.azure.com/).

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a sua conta de subscrição Azure.
1. Selecione **+ Criar um recurso** no canto superior esquerdo.
1. Selecione a categoria **Internet of Things** e, em seguida, selecione Time Series **Insights**.

   [![Selecione o recurso ambiente Time Series Insights.](media/tutorial-set-up-environment/create-new-environment.png)](media/tutorial-set-up-environment/create-new-environment.png#lightbox)

1. No painel **de ambiente Create Time Series Insights,** no **separador Básicos,** definir os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Insira um nome único para o ambiente Azure Time Series Insights Gen2. |
    | **Subscrição** | Insira a subscrição onde pretende criar o ambiente Azure Time Series Insights Gen2. Uma boa prática é utilizar a mesma subscrição que os restantes recursos IoT que são criados pelo simulador do dispositivo. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um novo grupo de recursos para o recurso ambiente Azure Time Series Insights Gen2. Um grupo de recursos é um contentor de recursos do Azure. Uma boa prática é utilizar o mesmo grupo de recursos que os outros recursos IoT que são criados pelo simulador do dispositivo. |
    | **Localização** | Selecione uma região de data center para o seu ambiente Azure Time Series Insights Gen2. Para evitar latência adicional, o melhor é criar o seu ambiente Azure Time Series Insights Gen2 na mesma região que o seu hub IoT criado pelo simulador de dispositivos. |
    | **Escalão de serviço** |  Selecione **Gen2(L1)**. Este é o SKU para o produto Azure Time Series Insights Gen2. |
    | **Nome de propriedade de ID série de tempo** | Insira um nome de uma propriedade que contenha valores que identifiquem exclusivamente as suas instâncias de séries horárias. O valor que introduz na caixa **de nomes da Propriedade** como ID da Série De Tempo não pode ser alterado mais tarde. Para este tutorial, insira **_id iothub-connection-connection-device_**. Para saber mais sobre o ID da Série De Tempo, incluindo o ID composto da Série De Tempo, leia [as melhores práticas para escolher um ID da Série De Tempo](./how-to-select-tsid.md). |
    | **Nome da conta de armazenamento** | Insira um nome globalmente único para uma nova conta de armazenamento.|
    | **Tipo de conta de armazenamento** | Selecione o tipo de armazenamento para uma nova conta de armazenamento. Recomendamos armazenamentoV2|
    | **Replicação da conta de armazenamento** | Selecione o tipo de armazenamento para uma nova conta de armazenamento. Com base na sua seleção de localização, pode escolher entre LRS, GRS e ZRS. Para este tutorial, pode selecionar LRS|
    | **Espaço de nomes hierárquico** |Esta opção é selecionável, uma vez que selecione o tipo de armazenamento para ser StorageV2. Por predefinição, está desativado. Para este tutorial, pode deixá-lo no seu estado de *desativação* padrão|
    |**Ativar loja quente**|Selecione **Sim** para ativar a loja quente. Esta definição pode ser desativada e reativada após a criação do ambiente. |
    |**Retenção de dados (em dias)**|Escolha a opção padrão de 7 dias. |

    [![Nova configuração do ambiente da Série De Tempo Azure Insights.](media/tutorial-set-up-environment/environment-configuration.png)](media/tutorial-set-up-environment/environment-configuration.png#lightbox)
    [![A nova configuração ambiental da Azure Time Series Insights, continuou.](media/tutorial-set-up-environment/environment-configuration2.png)](media/tutorial-set-up-environment/environment-configuration2.png#lightbox)

1. Selecione **Seguinte: Fonte de eventos**.

   [![Configurar o ID da Série De Tempo para o ambiente.](media/tutorial-set-up-environment/time-series-id-selection.png)](media/tutorial-set-up-environment/time-series-id-selection.png#lightbox)

1. No **separador Fonte** de Evento, desa um conjunto dos seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | --- |
   | **Criar uma fonte de evento?** | Selecione **Yes** (Sim).|
   | **Tipo de origem** | **Selecione IoT Hub**. |
   | **Nome** | Insira um valor único para o nome de origem do evento. |
   | **Selecione um hub** | Escolha **Selecionar a existência.** |
   | **Subscrição** | Selecione a subscrição que utilizou para o simulador do dispositivo. |
   | **Nome do Hub IoT** | Selecione o nome do hub IoT que criou para o simulador do dispositivo. |
   | **Política de acesso ao IoT Hub** | Selecione **iothubowner**. |
   | **Grupo de consumidores IoT Hub** | Selecione **Novo,** introduza um nome único e, em seguida, selecione **+ Adicionar**. O grupo de consumidores deve ser um valor único na Azure Time Series Insights Gen2. |
   | **Propriedade de timetamp** | Este valor é utilizado para identificar a propriedade **Timestamp** nos dados de telemetria que chegam. Para este tutorial, deixe esta caixa vazia. Este simulador utiliza a hora de entrada do IoT Hub, ao qual a Azure Time Series Insights Gen2 não tem defeitos. |

1. Selecione **Rever + Criar**.

   [![Configurar o hub IoT criado como fonte de evento.](media/tutorial-set-up-environment/configure-event-source.png)](media/tutorial-set-up-environment/configure-event-source.png#lightbox)

1. Selecione **Rever + Criar**.

    [![Rever + Criar página, com o botão Criar.](media/tutorial-set-up-environment/environment-confirmation.png)](media/tutorial-set-up-environment/environment-confirmation.png#lightbox)

    Pode rever o estado da sua implantação:

    [![Notificação de que a implementação está completa.](media/tutorial-set-up-environment/deployment-notification.png)](media/tutorial-set-up-environment/deployment-notification.png#lightbox)

1. Expandir detalhes de implementação.

## <a name="stream-data"></a>Dados de transmissão em fluxo

Agora que implementou o seu ambiente Azure Time Series Insights Gen2, comece a transmitir dados para análise.

1. Receberá um URL assim que a implementação do acelerador de solução estiver completa.

1. Clique no URL para lançar a simulação do dispositivo.

1. Selecione **+ Nova simulação**.

    1. Após as cargas da página **de configuração de Simulação,** introduza os parâmetros necessários.

        | Parâmetro | Ação |
        | --- | --- |
        | **Nome** | Insira um nome único para um simulador. |
        | **Descrição** | Insira uma definição. |
        | **Duração da simulação** | Definir para **executar indefinidamente**. |
        | **Modelo do dispositivo** | Clique + **Adicionar um tipo de dispositivo** <br />**Nome**: Enter **Elevator**. <br />**Quantidade**: Inserir **3**. <br /> Deixe os valores predefinidos restantes |
        | **Hub IoT de destino** | Definir para utilizar o **hub IoT pré-a provisionado**. |

        [![Configure parâmetros e lance.](media/tutorial-set-up-environment/launch-solution-accelerator.png)](media/tutorial-set-up-environment/launch-solution-accelerator.png#lightbox)

    1. Selecione **Iniciar a simulação**. No painel de simulação do dispositivo, são **apresentados dispositivos Ativos** e **mensagens totais.**

        [![Painel de simulação Azure IoT.](media/tutorial-set-up-environment/see-active-devices-and-messages.png)](media/tutorial-set-up-environment/see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analisar dados

Nesta secção, executa análises básicas nos dados da série de tempo utilizando o [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

1. Aceda ao seu Azure Time Series Insights Gen2 Explorer selecionando o URL a partir da página de recursos no [portal Azure](https://portal.azure.com/).

    [![A série de tempo Azure Insights Gen2 Explorer URL.](media/tutorial-set-up-environment/select-explorer-url.png)](media/tutorial-set-up-environment/select-explorer-url.png#lightbox)

1. No Azure Time Series Insights Gen2 Explorer, aparecerá uma barra que se estende por cima do ecrã. Este é o seu selecionador de disponibilidade. Certifique-se de que tem pelo menos dois 2 m selecionados e, se necessário, expanda o prazo selecionando e arrastando as pegas do apanhador para a esquerda e para a direita.

1. **Séries de tempo As instâncias** serão apresentadas no lado esquerdo.

    [![Lista de casos não enparados.](media/tutorial-set-up-environment/explorer-unparented-instances.png)](media/tutorial-set-up-environment/explorer-unparented-instances.png#lightbox)

1. Selecione a primeira instância da série. Em seguida, **selecione Mostrar a temperatura**.

    [![Instância de séries de tempo selecionada com comando de menu para mostrar a temperatura média.](media/tutorial-set-up-environment/select-instance-and-temperature.png)](media/tutorial-set-up-environment/select-instance-and-temperature.png#lightbox)

    Aparece um gráfico de séries temporentais. Altere o **Intervalo** para **30s**.

1. Repita o passo anterior com as outras duas instâncias da série de tempo para que esteja a ver as três, como mostra este gráfico:

    [![Gráfico para todas as séries de tempo.](media/tutorial-set-up-environment/explorer-add-three-instances.png)](media/tutorial-set-up-environment/explorer-add-three-instances.png#lightbox)

1. Selecione o seletor de tempo no canto superior direito. Aqui pode selecionar os tempos de início e de fim específicos até ao milissegundo, ou escolher entre opções pré-configuradas, tais como **os últimos 30 minutos**. Também pode alterar o fuso horário predefinido.

    [![Desa um intervalo de tempo para os últimos 30 minutos.](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png)](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png#lightbox)

    O progresso do acelerador de solução nos **últimos 30 minutos** é agora exibido no Azure Time Series Insights Gen2 Explorer.

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta secção, aplica-se um modelo para estruturar os seus dados. Para completar o modelo, define-se tipos, hierarquias e instâncias. Para saber mais sobre modelação de dados, leia [o Modelo série de tempo.](./concepts-model-overview.md)

1. No Explorer, selecione o **separador Modelo:**

   [![Ver o separador Modelo no Explorer.](media/tutorial-set-up-environment/select-model-view.png)](media/tutorial-set-up-environment/select-model-view.png#lightbox)

   No **separador 'Tipos',** selecione **+ Adicionar**.

1. Introduza os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome** | Entrar **elevador** |
    | **Descrição** | Insira **Esta é uma definição tipo para Elevador** |

1. Em seguida, selecione o **separador Variáveis.**

    1. **Selecione + Adicionar Variável** e preencha os seguintes valores para a primeira variável do tipo Elevador. Irás autorizar três variáveis no total.

        | Parâmetro | Ação |
        | --- | --- |
        | **Nome** | Insira **a temperatura de Avg**. |
        | **Tipo** | Selecione **numérico** |
        | **Valor** | Selecione a partir da pré-sintonia: Selecione **a temperatura (Duplo)**. <br /> Nota: Pode levar alguns minutos para **que o Valor** seja automaticamente povoado após o Azure Time Series Insights Gen2 começar a receber eventos.|
        | **Operação agregação** | Expandir **Opções Avançadas**. <br /> Selecione **AVG**. |

    1. Selecione **Aplicar**. Em seguida, **+ Adicionar Variáve novamente,** e definir os seguintes valores:

        | Parâmetro | Ação |
        | --- | --- |
        | **Nome** | Insira **a Vibração Avg**. |
        | **Tipo** | Selecione **numérico** |
        | **Valor** | Selecione a partir da pré-sintonia: Selecione **vibração (Duplo)**. <br /> Nota: Pode levar alguns minutos para **que o Valor** seja automaticamente povoado após o Azure Time Series Insights Gen2 começar a receber eventos.|
        | **Operação agregação** | Expandir **Opções Avançadas**. <br /> Selecione **AVG**. |

    1. Selecione **Aplicar**. Em seguida, **+ Adicionar Variável** novamente, e definir os seguintes valores para a terceira e última variável:

        | Parâmetro | Ação |
        | --- | --- |
        | **Nome** | Entrar **no piso**. |
        | **Tipo** | Selecione **Categórico** |
        | **Valor** | Selecione a partir da pré-sintonia: **Select Floor (Duplo)**. <br /> Nota: Pode levar alguns minutos para **que o Valor** seja automaticamente povoado após o Azure Time Series Insights Gen2 começar a receber eventos.|
        | **Categorias** | <span style="text-decoration: underline">Etiqueta</span>   -  <span style="text-decoration: underline">Valores</span> <br /> Menor: 1,2,3,4 <br /> Meio: 5,6,7,8,9 <br /> Superior: 10,11,12,13,14,15 |
        | **Categoria Padrão** | Entrar **Desconhecido** |

        [![Adicione variáveis tipo.](media/tutorial-set-up-environment/add-type-variables.png)](media/tutorial-set-up-environment/add-type-variables.png#lightbox)

    1. Selecione **Aplicar**.
    1. Selecione **Guardar**. Três variáveis são criadas e exibidas.

        [![Depois de adicionar o tipo, reveja-o na vista Modelo.](media/tutorial-set-up-environment/add-type-and-view.png)](media/tutorial-set-up-environment/add-type-and-view.png#lightbox)

1. Selecione o separador **Hierarquias.** Em seguida, selecione **+ Adicionar**.

   1. No painel **de hierarquia de edição,** definir os seguintes parâmetros:

        | Parâmetro | Ação |
        | --- | ---|
        | **Nome** | Insira **a Hierarquia de Localização**. |
        |**Níveis**| Entre **no País** como o nome do primeiro nível<br />Selecione **+ Adicionar Nível**<br />Insira **o City** para o segundo nível e, em seguida, selecione **+ Adicionar Nível**<br />**Insira o Edifício** como o nome do terceiro e último nível |

   1. Selecione **Guardar**.

        [![Exiba a sua nova hierarquia na vista Modelo.](media/tutorial-set-up-environment/add-hierarchy-and-view.png)](media/tutorial-set-up-environment/add-hierarchy-and-view.png#lightbox)

1. Navegar para **instâncias**.

    1. Em **Ações** de extrema-direita, e selecione o ícone do lápis para editar a primeira instância com os seguintes valores:

        | Parâmetro | Ação |
        | --- | --- |
        | **Tipo** | Selecione **Elevador**. |
        | **Nome** | Insira **elevador 1**|
        | **Descrição** | **Insira a instância para o elevador 1** |

    1. Navegue em **Campos de Instância** e introduza os seguintes valores:

        | Parâmetro | Ação |
        | --- | --- |
        | **Hierarquias** | Selecione **hierarquia de localização** |
        | **País** | Entrar **nos EUA** |
        | **City** | Entrar em **Seattle** |
        | **Edifício** | Entrar **na agulha do espaço** |

    1. Selecione **Guardar**.

1. Repita o passo anterior com as outras duas instâncias utilizando os seguintes valores:

    **Para elevador 2:**

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Entrar **elevador 2**|
    | **Descrição** | **Insira a instância para o elevador 2** |
    | **Hierarquias** | Selecione **hierarquia de localização** |
    | **País** | Entrar **nos EUA** |
    | **City** | Entrar em **Seattle** |
    | **Edifício** | Insira **o Centro de Ciência do Pacífico** |

    **Para elevador 3:**

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Entrar **elevador 3**|
    | **Descrição** | **Insira a instância para o elevador 3** |
    | **Hierarquias** | Selecione **hierarquia de localização** |
    | **País** | Entrar **nos EUA** |
    | **City** | Entra em **Nova Iorque** |
    | **Edifício** | Entrar no **Empire State Building** |

    [![Ver as instâncias atualizadas.](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png)](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png#lightbox)

1. Volte ao separador **Analisar** para ver o painel de gráficos. Sob **a Hierarquia de Localização**, expanda todos os níveis de hierarquia para exibir as instâncias das séries horárias:

    [![Veja todas as hierarquias na vista de gráficos.](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png)](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. No **Centro de Ciência do Pacífico,** selecione o Elevador de Instância de Séries **Hortemístos 2** e, em seguida, selecione **Mostrar Temperatura Média**.

1. No mesmo caso, **Elevador 2**, selecione **Show Floor**.

    Com a sua variável categórica, pode determinar quanto tempo o elevador passou nos andares superior, inferior e médio.

    [![Visualizar o Elevador 2 com hierarquia e dados.](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png)](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos

Agora que completou o tutorial, limpe os recursos que criou:

1. A partir do menu esquerdo no [portal Azure](https://portal.azure.com), selecione **Todos os recursos**, localize o seu grupo de recursos Azure Time Series Insights Gen2.
1. Ou elimina todo o grupo de recursos (e todos os recursos contidos no mesmo) selecionando **Eliminar** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar e utilizar um acelerador de simulação de dispositivo.
* Crie um ambiente Azure Time Series Insights Gen2 PAYG.
* Ligue o ambiente Azure Time Series Insights Gen2 a um hub iot.
* Executar uma amostra de acelerador de solução para transmitir dados para o ambiente Azure Time Series Insights Gen2.
* Realizar uma análise básica dos dados.
* Defina um tipo e hierarquia de modelo de séries de tempo e associe-os às suas instâncias.

Agora que sabe como criar o seu próprio ambiente Azure Time Series Insights Gen2, saiba mais sobre os conceitos-chave da Azure Time Series Insights Gen2.

Leia sobre a ingestão da Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 data ingestion visão geral](./concepts-ingestion-overview.md)

Leia sobre o armazenamento da Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 armazenamento de dados](./concepts-storage.md)

Saiba mais sobre modelos de séries de tempo:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 modelagem de dados](./concepts-model-overview.md)

Saiba mais sobre a ligação do seu ambiente ao Power BI:

> [!div class="nextstepaction"]
> [Visualizar dados da Azure Time Series Insights Gen2 em Power BI](./how-to-connect-power-bi.md)

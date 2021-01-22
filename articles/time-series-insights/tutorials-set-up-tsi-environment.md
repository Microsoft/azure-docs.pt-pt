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
ms.date: 01/19/2021
ms.custom: seodec18
ms.openlocfilehash: 912dbf48254a8d68b9bf76db04734fd791a3edb9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677058"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Tutorial: Criar um ambiente Azure Time Series Insights Gen2

Este tutorial guia-o através do processo de criação de um ambiente Azure Time Series Insights Gen2 *pay-as-you-go* (PAYG).

Neste tutorial, vai aprender a:

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

* No mínimo, deve ter o **papel de Contribuinte** para a subscrição do Azure. Para obter mais informações, leia [Adicionar ou remover atribuições de funções Azure utilizando o portal Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Nesta secção, irá criar três dispositivos simulados que enviam dados para uma instância do Azure IoT Hub.

1. Aceda à página de [aceleradores de solução Azure IoT](https://www.azureiotsolutions.com/Accelerators). A página apresenta vários exemplos pré-construídos. Inscreva-se utilizando a sua conta Azure. Em seguida, selecione **Simulação de Dispositivo**.

   [![Página de aceleradores de solução Azure IoT.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Introduza a subscrição onde pretende criar a simulação do dispositivo.

   Parâmetro|Descrição
   ---|---
   **Nome de implantação** | Este valor único é usado para criar um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Subscrição do Azure** | Especifique a mesma subscrição que foi usada para criar o seu ambiente Azure Time Series Insights Gen2 na secção anterior.
   **Opções de implementação** | Selecione **Provision novo IoT Hub** para criar um novo hub IoT específico para este tutorial.
   **Local azul** | Especifique a mesma região que foi usada para criar o seu ambiente Azure Time Series Insights Gen2 na secção anterior.

   Quando terminar, **selecione Criar** para forragem dos recursos Azure da solução. Pode levar até 20 minutos para concluir este processo.

   [![Forrá a solução de simulação do dispositivo.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Após o fornecimento ter terminado, duas notificações aparecerão anunciando que o estado de implantação passou de **Provisioning** para **Ready**.

   >[!IMPORTANT]
   > Não entre no seu acelerador de solução ainda! Mantenha esta página web aberta porque voltará mais tarde.

   [![Provisões de solução de simulação do dispositivo completas.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Agora, inspecione os recursos recém-criados no portal Azure. Na página **dos grupos de recursos,** note que um novo grupo de recursos foi criado utilizando o **nome Solução** fornecido no último passo. Tome nota dos recursos que foram criados para a simulação do dispositivo.

   [![Recursos de simulação de dispositivo.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Criar um ambiente Azure Time Series Insights Gen2

Esta secção descreve como criar um ambiente Azure Time Series Insights Gen2 e conectá-lo ao hub IoT criado pelo Acelerador de Solução IoT utilizando o [portal Azure](https://portal.azure.com/).

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a sua conta de subscrição Azure.
1. Selecione **+ Criar um recurso** no canto superior esquerdo.
1. Selecione a categoria **Internet of Things** e, em seguida, selecione Time Series **Insights**.

   [![Selecione o recurso ambiente Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. No painel **de ambiente Create Time Series Insights,** no **separador Básicos,** definir os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Insira um nome único para o ambiente Azure Time Series Insights Gen2. |
    | **Subscrição** | Insira a subscrição onde pretende criar o ambiente Azure Time Series Insights Gen2. Uma boa prática é utilizar a mesma subscrição que os restantes recursos IoT que são criados pelo simulador do dispositivo. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um novo grupo de recursos para o recurso ambiente Azure Time Series Insights Gen2. Um grupo de recursos é um contentor de recursos do Azure. Uma boa prática é utilizar o mesmo grupo de recursos que os outros recursos IoT que são criados pelo simulador do dispositivo. |
    | **Localização** | Selecione uma região de data center para o seu ambiente Azure Time Series Insights Gen2. Para evitar latência adicional, o melhor é criar o seu ambiente Azure Time Series Insights Gen2 na mesma região que o seu hub IoT criado pelo simulador de dispositivos. |
    | **Escalão de serviço** |  Selecione **Gen2(L1)**. Este é o SKU para o produto Azure Time Series Insights Gen2. |
    | **Nome de propriedade de ID série de tempo** | Insira um nome de uma propriedade que contenha valores que identifiquem exclusivamente as suas instâncias de séries horárias. O valor que introduz na caixa **de nomes da Propriedade** como ID da Série De Tempo não pode ser alterado mais tarde. Para este tutorial,*_insira * iothub-connection-device-id_* _. Para saber mais sobre o ID da Série De Tempo, incluindo o ID composto da Série De Tempo, leia [as melhores práticas para escolher um ID da Série De Tempo](./how-to-select-tsid.md). |
    | _ *Nome da conta de armazenamento** | Insira um nome globalmente único para uma nova conta de armazenamento.|
    | **Tipo de conta de armazenamento** | Selecione o tipo de armazenamento para uma nova conta de armazenamento. Recomendamos armazenamentoV2|
    | **Replicação da conta de armazenamento** | Selecione o tipo de armazenamento para uma nova conta de armazenamento. Com base na sua seleção de localização, pode escolher entre LRS, GRS e ZRS. Para este tutorial, pode selecionar LRS|
    | **Espaço de nomes hierárquico** |Esta opção é selecionável, uma vez que selecione o tipo de armazenamento para ser StorageV2. Por predefinição, está desativado. Para este tutorial, pode deixá-lo no seu estado de *desativação* padrão|
    |**Ativar loja quente**|Selecione **Sim** para ativar a loja quente. Esta definição pode ser desativada e reativada após a criação do ambiente. |
    |**Retenção de dados (em dias)**|Escolha a opção padrão de 7 dias. |

    [![Nova configuração do ambiente da Série Temporal Insights.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

1. Selecione **Seguinte: Fonte de eventos**.

   [![Configurar o ID da Série De Tempo para o ambiente.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. No **separador Fonte** de Evento, desa um conjunto dos seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | --- |
   | **Criar uma fonte de evento?** | Selecione **Yes** (Sim).|
   | **Nome** | Insira um valor único para o nome de origem do evento. |
   | **Tipo de origem** | **Selecione IoT Hub**. |
   | **Selecione um hub** | Escolha **Selecionar a existência.** |
   | **Subscrição** | Selecione a subscrição que utilizou para o simulador do dispositivo. |
   | **Nome do Hub IoT** | Selecione o nome do hub IoT que criou para o simulador do dispositivo. |
   | **Política de acesso ao IoT Hub** | Selecione **iothubowner**. |
   | **Grupo de consumidores IoT Hub** | Selecione **Novo,** introduza um nome único e, em seguida, selecione **+ Adicionar**. O grupo de consumidores deve ser um valor único na Azure Time Series Insights Gen2. |
   | **Propriedade de timetamp** | Este valor é utilizado para identificar a propriedade **Timestamp** nos dados de telemetria que chegam. Para este tutorial, deixe esta caixa vazia. Este simulador utiliza a hora de entrada do IoT Hub, ao qual a Azure Time Series Insights Gen2 não tem defeitos. |

1. Selecione **Rever + Criar**.

   [![Configurar o hub IoT criado como fonte de evento.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Selecione **Criar**.

    [![Rever + Criar página, com o botão Criar.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Pode rever o estado da sua implantação:

    [![Notificação de que a implementação está completa.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Tem acesso ao ambiente Azure Time Series Insights Gen2 por padrão se for proprietário da subscrição Azure. Verifique se tem acesso:

   1. Procure o seu grupo de recursos e, em seguida, selecione o seu recém-criado ambiente Azure Time Series Insights Gen2.

      [![Selecione e veja o seu ambiente.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Na página Azure Time Series Insights Gen2, selecione Políticas de **Acesso a Dados**:

      [![Verifique as políticas de acesso a dados.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Verifique se as suas credenciais estão listadas:

      Se as suas credenciais não estiverem listadas, deve conceder-se permissão para aceder ao ambiente selecionando Add e procurando as suas credenciais. Para saber mais sobre a definição de permissões, leia [o acesso aos dados do Grant](./concepts-access-policies.md).

## <a name="stream-data"></a>Dados de transmissão em fluxo

Agora que implementou o seu ambiente Azure Time Series Insights Gen2, comece a transmitir dados para análise.

1. Volte ao [Dashboard dos aceleradores de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard). Inscreva-se novamente, se necessário, usando a mesma conta Azure que tem usado neste tutorial. Selecione a sua "Solução de Dispositivo" e, em seguida, **vá ao acelerador da solução** para lançar a sua solução implementada.

   [![Painel de aceleração de solução.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. A aplicação web de simulação do dispositivo começa por lhe pedir para conceder à aplicação web o **Signo-lo e ler a** permissão do seu perfil. Esta permissão permite que a aplicação recupere as informações necessárias para apoiar o funcionamento da aplicação.

   [![Simulação de dispositivo consentimento da aplicação web.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Selecione **+ Nova simulação**.

    1. Após as cargas da página **de configuração de Simulação,** introduza os parâmetros necessários.

        | Parâmetro | Ação |
        | --- | --- |
        | **Nome** | Insira um nome único para um simulador. |
        | **Descrição** | Insira uma definição. |
        | **Duração da simulação** | Definir para **executar indefinidamente**. |
        | **Modelo do dispositivo** | Clique + **Adicionar um tipo de dispositivo** <br />**Nome**: Enter **Elevator**. <br />**Quantidade**: Inserir **3**. <br /> Deixe os valores predefinidos restantes |
        | **Hub IoT de destino** | Definir para utilizar o **hub IoT pré-a provisionado**. |

        [![Configure parâmetros e lance.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    1. Selecione **Iniciar a simulação**. No painel de simulação do dispositivo, são **apresentados dispositivos Ativos** e **mensagens totais.**

        [![Painel de simulação Azure IoT.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analisar dados

Nesta secção, executa análises básicas nos dados da série de tempo utilizando o [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

1. Aceda ao seu Azure Time Series Insights Gen2 Explorer selecionando o URL a partir da página de recursos no [portal Azure](https://portal.azure.com/).

    [![A série de tempo Azure Insights Gen2 Explorer URL.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. No Azure Time Series Insights Gen2 Explorer, aparecerá uma barra que se estende por cima do ecrã. Este é o seu selecionador de disponibilidade. Certifique-se de que tem pelo menos dois 2 m selecionados e, se necessário, expanda o prazo selecionando e arrastando as pegas do apanhador para a esquerda e para a direita.

1. **Séries de tempo As instâncias** serão apresentadas no lado esquerdo.

    [![Lista de casos não enparados.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Selecione a primeira instância da série. Em seguida, **selecione Mostrar a temperatura**.

    [![Instância de séries de tempo selecionada com comando de menu para mostrar a temperatura média.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Aparece um gráfico de séries temporentais. Altere o **Intervalo** para **30s**.

1. Repita o passo anterior com as outras duas instâncias da série de tempo para que esteja a ver as três, como mostra este gráfico:

    [![Gráfico para todas as séries de tempo.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Selecione o seletor de tempo no canto superior direito. Aqui pode selecionar os tempos de início e de fim específicos até ao milissegundo, ou escolher entre opções pré-configuradas, tais como **os últimos 30 minutos**. Também pode alterar o fuso horário predefinido.

    [![Desa um intervalo de tempo para os últimos 30 minutos.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    O progresso do acelerador de solução nos **últimos 30 minutos** é agora exibido no Azure Time Series Insights Gen2 Explorer.

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta secção, aplica-se um modelo para estruturar os seus dados. Para completar o modelo, define-se tipos, hierarquias e instâncias. Para saber mais sobre modelação de dados, leia [o Modelo série de tempo.](./concepts-model-overview.md)

1. No Explorer, selecione o **separador Modelo:**

   [![Ver o separador Modelo no Explorer.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

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

        [![Adicione variáveis tipo.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    1. Selecione **Aplicar**. 
    1. Selecione **Guardar**. Três variáveis são criadas e exibidas.

        [![Depois de adicionar o tipo, reveja-o na vista Modelo.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Selecione o separador **Hierarquias.** Em seguida, selecione **+ Adicionar**.

   1. No painel **de hierarquia de edição,** definir os seguintes parâmetros:

        | Parâmetro | Ação |
        | --- | ---|
        | **Nome** | Insira **a Hierarquia de Localização**. |
        |**Níveis**| Entre **no País** como o nome do primeiro nível <br /> Selecione **+ Adicionar Nível** <br /> Insira **o City** para o segundo nível e, em seguida, selecione **+ Adicionar Nível** <br /> **Insira o Edifício** como o nome do terceiro e último nível |

   1. Selecione **Guardar**.

        [![Exiba a sua nova hierarquia na vista Modelo.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

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
        | **Cidade** | Entrar em **Seattle** |
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
    | **Cidade** | Entrar em **Seattle** |
    | **Edifício** | Insira **o Centro de Ciência do Pacífico** |

    **Para elevador 3:**

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Entrar **elevador 3**|
    | **Descrição** | **Insira a instância para o elevador 3** |
    | **Hierarquias** | Selecione **hierarquia de localização** |
    | **País** | Entrar **nos EUA** |
    | **Cidade** | Entra em **Nova Iorque** |
    | **Edifício** | Entrar no **Empire State Building** |

    [![Ver as instâncias atualizadas.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Volte ao separador **Analisar** para ver o painel de gráficos. Sob **a Hierarquia de Localização**, expanda todos os níveis de hierarquia para exibir as instâncias das séries horárias:

    [![Veja todas as hierarquias na vista de gráficos.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. No **Centro de Ciência do Pacífico,** selecione o Elevador de Instância de Séries **Hortemístos 2** e, em seguida, selecione **Mostrar Temperatura Média**.

1. No mesmo caso, **Elevador 2**, selecione **Show Floor**.

    Com a sua variável categórica, pode determinar quanto tempo o elevador passou nos andares superior, inferior e médio.

    [![Visualizar o Elevador 2 com hierarquia e dados.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

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

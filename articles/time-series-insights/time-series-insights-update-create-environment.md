---
title: 'Tutorial: configurar um ambiente de visualização-Azure Time Series Insights | Microsoft Docs'
description: 'Tutorial: saiba como configurar um ambiente no Azure Time Series Insights Preview.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/26/2019
ms.custom: seodec18
ms.openlocfilehash: bebc7dde30dad57157d0abee7f2294d9da58fd5c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861817"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: Configurar um ambiente de pré-visualização do Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de um ambiente PAYG (pré- *pago* ) de visualização de Azure Time Series insights.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um ambiente de visualização de Azure Time Series Insights.
> * Conecte o ambiente de visualização de Azure Time Series Insights a um hub IoT.
> * Execute um exemplo do Solution Accelerator para transmitir dados para o ambiente de visualização de Azure Time Series Insights.
> * Execute análise básica nos dados.
> * Defina um tipo de modelo de série temporal e uma hierarquia e associe-o às suas instâncias.
> * Use o conector de Power BI e visualize dados no Power BI.

>[!TIP]
> Os [aceleradores de solução de IOT](https://www.azureiotsolutions.com/Accelerators) fornecem soluções pré-configuradas de nível empresarial que você pode usar para acelerar o desenvolvimento de soluções de IOT personalizadas.

Inscreva-se para obter uma [assinatura gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* No mínimo, você deve ter a função de **colaborador** para a assinatura do Azure. Para obter mais informações, leia [gerenciar o acesso usando o controle de acesso baseado em função e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Nesta seção, você criará três dispositivos simulados que enviam dados para uma instância do Hub IoT do Azure.

1. Vá para a [página aceleradores de soluções do Azure IOT](https://www.azureiotsolutions.com/Accelerators). A página exibe vários exemplos predefinidos. Entre usando sua conta do Azure. Em seguida, selecione **simulação de dispositivo**.

   [![página aceleradores de soluções do Azure IoT.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Na página seguinte, selecione **experimentar agora**. Em seguida, insira os parâmetros necessários na página **criar solução de simulação de dispositivo** .

   Parâmetro|Descrição
   ---|---
   **Nome da implantação** | Esse valor exclusivo é usado para criar um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Subscrição do Azure** | Especifique a mesma assinatura que foi usada para criar seu ambiente de Time Series Insights na seção anterior.
   **Opções de implantação** | Selecione **provisionar novo hub IOT** para criar um novo hub IOT específico para este tutorial.
   **Local do Azure** | Especifique a mesma região que foi usada para criar seu ambiente de Time Series Insights na seção anterior.

   Quando tiver terminado, selecione **criar** para provisionar os recursos do Azure da solução. Pode levar até 20 minutos para concluir esse processo.

   [![provisionar a solução de simulação de dispositivo.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Após a conclusão do provisionamento, duas notificações serão exibidas anunciando que o estado da implantação foi movido do **provisionamento** para **pronto**. 

   >[!IMPORTANT]
   > Não insira seu acelerador de solução ainda! Mantenha essa página da Web aberta, pois você retornará a ela mais tarde.

   [provisionamento de solução de simulação de dispositivo ![concluído.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Agora, inspecione os recursos recém-criados no portal do Azure. Na página **grupos de recursos** , observe que um novo grupo de recursos foi criado usando o **nome da solução** fornecido na última etapa. Anote os recursos que foram criados para a simulação do dispositivo.

   [![recursos de simulação de dispositivo.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Criar um ambiente de PAYG de visualização

Esta seção descreve como criar um ambiente de visualização Azure Time Series Insights e conectá-lo ao Hub IoT criado pelo acelerador de solução IoT usando o [portal do Azure](https://portal.azure.com/).

1. Entre no [portal do Azure](https://portal.azure.com) usando sua conta de assinatura do Azure. 
1. Selecione **+ Criar um recurso** no canto superior esquerdo. 
1. Selecione o **Internet das coisas** categoria e, em seguida, selecione **Time Series Insights**. 

   [![selecione o recurso de ambiente de Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. No painel **criar Time Series insights ambiente** , na guia **noções básicas** , defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Insira um nome exclusivo para o ambiente de visualização de Azure Time Series Insights. |
    | **Subscrição** | Insira a assinatura na qual você deseja criar o ambiente de visualização de Azure Time Series Insights. Uma prática recomendada é usar a mesma assinatura que o restante dos recursos de IoT criados pelo Device Simulator. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um novo grupo de recursos para o Azure Time Series Insights recurso de ambiente de visualização. Um grupo de recursos é um contentor de recursos do Azure. Uma prática recomendada é usar o mesmo grupo de recursos que os outros recursos de IoT criados pelo Device Simulator. |
    | **Localização** | Selecione uma região de data center para seu ambiente de Azure Time Series Insights visualização. Para evitar latência adicional, é melhor criar seu Azure Time Series Insights ambiente de visualização na mesma região que o Hub IoT criado pelo Device Simulator. |
    | **Preço** |  Selecione **PAYG** (pré-*pago*). Este é o SKU para o produto de visualização de Azure Time Series Insights. |
    | **Nome da propriedade** | Insira um valor que identifique exclusivamente sua instância de série temporal. O valor que você inserir na caixa **ID da propriedade** não poderá ser alterado posteriormente. Para este tutorial, digite ***iothub-Connection-Device-ID***. Para saber mais sobre a ID da série temporal, leia [as práticas recomendadas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md). |
    | **Nome da conta de armazenamento** | Insira um nome globalmente exclusivo para uma nova conta de armazenamento.|
    |**Habilitar armazenamento quente**|Selecione **Sim** para habilitar o armazenamento quente. Você pode voltar mais tarde e habilitar essa configuração. |
    |**Retenção de dados (em dias)**|Escolha a opção padrão de 7 dias. |

    Selecione **Avançar: origem do evento**.

   [![nova configuração de ambiente de Time Series Insights.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![configurar a ID da série temporal para o ambiente.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

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
   | **Grupo de consumidores do Hub IoT** | Selecione **novo**, insira um nome exclusivo e, em seguida, selecione **+ Adicionar**. O grupo de consumidores deve ser um valor exclusivo na visualização Azure Time Series Insights. |
   | **Propriedade timestamp** | Esse valor é usado para identificar a propriedade **timestamp** em seus dados de telemetria recebidos. Para este tutorial, deixe essa caixa vazia. Esse simulador usa o carimbo de data/hora de entrada do Hub IoT, que Time Series Insights usa como padrão. |

   Selecione **revisão + criar**.

   [![configurar o Hub IoT criado como uma origem do evento.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Selecione **Criar**.

    [![revisar + criar página, com o botão criar.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Você pode examinar o status da sua implantação:

    [![notificação de que a implantação foi concluída.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Você tem acesso ao ambiente de visualização do Azure Time Series Insights por padrão se for um proprietário da assinatura do Azure. Verifique se você tem acesso:

   1. Pesquise seu grupo de recursos e, em seguida, selecione o ambiente de visualização do Azure Time Series Insights criado recentemente. 

      [![selecionar e exibir seu ambiente.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Na página Azure Time Series Insights visualização, selecione **políticas de acesso a dados**:

      [![verificar as políticas de acesso a dados.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Verifique se suas credenciais estão listadas:

      Se suas credenciais não estiverem listadas, você deverá conceder permissão para acessar o ambiente selecionando Adicionar e pesquisando suas credenciais. Para saber mais sobre como definir permissões, leia [conceder acesso a dados](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Dados de transmissão em fluxo

Agora que você implantou seu ambiente de Time Series Insights, comece a transmitir dados para análise.

1. Volte ao [Dashboard dos aceleradores de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard). Entre novamente, se necessário, usando a mesma conta do Azure que você esteve usando neste tutorial. Selecione a "solução de dispositivo" e **vá para o Solution Accelerator** para iniciar a solução implantada.

   [painel ![Solution Accelerators.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. O aplicativo Web de simulação de dispositivo começa solicitando que você conceda o aplicativo Web para **conectá-lo e ler a permissão de seu perfil** . Essa permissão permite que o aplicativo recupere as informações de perfil do usuário necessárias para dar suporte ao funcionamento do aplicativo.

   [![o consentimento do aplicativo Web de simulação de dispositivo.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Selecione **+ nova simulação**. Depois que a página **instalação da simulação** for carregada, insira os parâmetros necessários.

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira um nome exclusivo para um simulador. |
    | **Descrição** | Insira uma definição. |
    | **Duração da simulação** | Defina para **executar indefinidamente**. |
    | **Modelo do dispositivo** | Clique em + **Adicionar um tipo de dispositivo** <br />**Nome**: Insira o **elevador**. <br />**Valor**: insira **3**. <br /> Deixar os valores padrão restantes |
    | **Hub IoT de destino** | Defina para **usar o Hub IOT provisionado previamente**. |

    [![configurar parâmetros e iniciar.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Selecione **Iniciar simulação**.

    No painel de simulação de dispositivo, **dispositivos ativos** e **total de mensagens** são exibidos.

    [![painel de simulação do Azure IoT.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analise os dados

Nesta seção, você executará a análise básica nos dados de série temporal usando o [Gerenciador de visualização Azure Time Series insights](./time-series-insights-update-explorer.md).

1. Acesse o Azure Time Series Insights Explorer Preview selecionando a URL na página de recursos no [portal do Azure](https://portal.azure.com/).

    [![a URL do Gerenciador de visualização Time Series Insights.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. No Time Series Insights Explorer, uma barra que abrange a parte superior da tela será exibida. Este é seu seletor de disponibilidade. Verifique se você tem pelo menos dois 2 m selecionados e, se necessário, expanda o intervalo de tempo selecionando e arrastando os identificadores do seletor para a esquerda e para a direita.

1. As **instâncias de série temporal** serão exibidas no lado esquerdo.

    [![lista de instâncias não pai.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Selecione a primeira instância da série temporal. Em seguida, selecione **Mostrar temperatura**.

    [![instância de série temporal selecionada com comando de menu para mostrar a temperatura média.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Um gráfico de série temporal é exibido. Altere o **intervalo** para **30s**.

1. Repita a etapa anterior com as outras duas instâncias de série temporal para que você esteja exibindo todas as três, conforme mostrado neste gráfico:

    [![gráfico para todas as séries temporais.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Selecione o seletor de intervalo de tempo no canto superior direito. Aqui você pode selecionar horários de início e término específicos até o milissegundo ou escolher entre as opções pré-configuradas, como os **últimos 30 minutos**. Você também pode alterar o fuso horário padrão.

    [![definir o intervalo de tempo para os últimos 30 minutos.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    O progresso do Solution Accelerator nos **últimos 30 minutos** agora é exibido no time Series insights Explorer.

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta seção, você aplica um modelo para estruturar seus dados. Para concluir o modelo, você define tipos, hierarquias e instâncias. Para saber mais sobre modelagem de dados, leia [modelo de série temporal](./time-series-insights-update-tsm.md).

1. No Gerenciador, selecione a guia **modelo** :

   [![exibir a guia Modelo no Gerenciador.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Na guia **tipos** , selecione **+ Adicionar**.

1. Insira os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome** | Inserir **elevador** |
    | **Descrição** | Insira **esta é uma definição de tipo para o elevador** |

1. Em seguida, selecione a guia **variáveis** . 

   Selecione **+ Adicionar variável** e preencha os valores a seguir para a primeira variável do tipo de elevador. Você criará três variáveis no total.

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira a **temperatura média**. |
    | **Quase** | Selecionar **numérico** |
    | **Valor** | Selecione de predefinição: selecione **temperatura (dupla)** . <br /> Observação: pode levar alguns minutos para que o **valor** seja preenchido automaticamente depois que Azure Time Series insights visualização começar a receber eventos.|
    | **Operação de agregação** | Expanda **Opções avançadas**. <br /> Selecione **Méd**. |

    Selecione **Aplicar**. Em seguida, **+ Adicionar variável** novamente e defina os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira a **média de vibração**. |
    | **Quase** | Selecionar **numérico** |
    | **Valor** | Selecione de predefinição: selecione **vibração (dupla)** . <br /> Observação: pode levar alguns minutos para que o **valor** seja preenchido automaticamente depois que Azure Time Series insights visualização começar a receber eventos.|
    | **Operação de agregação** | Expanda **Opções avançadas**. <br /> Selecione **Méd**. |

    Selecione **Aplicar**. Em seguida, **+ Adicionar variável** novamente e defina os seguintes valores para a terceira e última variável:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira o **piso**. |
    | **Quase** | Selecionar **categórico** |
    | **Valor** | Selecione de predefinição: selecione **andar (duplo)** . <br /> Observação: pode levar alguns minutos para que o **valor** seja preenchido automaticamente depois que Azure Time Series insights visualização começar a receber eventos.|
    | **Às** | <span style="text-decoration: underline">Valores</span> de  - de <span style="text-decoration: underline">rótulo</span> <br /> Inferior: 1, 2, 3, 4 <br /> Meio: 5, 6, 7, 8, 9 <br /> Upper: 10, 11, 12, 13, 14, 15 |
    | **Categoria padrão** | Inserir **desconhecido** |

    [![adicionar variáveis de tipo.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Selecione **Aplicar**.

1. Selecione **Guardar**. Três variáveis são criadas e exibidas.

    [![depois de adicionar o tipo, examine-o no modo de exibição de modelo.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Selecione a guia **hierarquias** . Em seguida, selecione **+ Adicionar**.
   
   No painel **Editar hierarquia** , defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | ---|
   | **Nome** | Insira a **hierarquia de localização**. |
   |**Alcançar**| Insira o **país** como o nome do primeiro nível <br> Selecionar **+ Adicionar nível** <br> Insira **cidade** para o segundo nível e, em seguida, selecione **+ Adicionar nível** <br> Insira a **compilação** como o nome do terceiro e último nível |

   Selecione **Guardar**.

   [![exibir sua nova hierarquia no modo de exibição de modelo.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navegue até **instâncias**. Em **ações** à extrema direita e selecione o ícone de lápis para editar a primeira instância com os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **elevador**. |
    | **Nome** | Inserir **elevador 1**|
    | **Descrição** | Inserir **instância para o elevador 1** |

    Navegue até **campos de instância** e insira os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Hierarquias** | Selecionar **hierarquia de local** |
    | **País** | Insira os **EUA** |
    | **Azul** | Digite **Seattle** |
    | **Elaboração** | Inserir a **agulha de espaço** |

    Selecione **Guardar**.

1. Repita a etapa anterior com as outras duas instâncias ao usar os seguintes valores:

    **Para o elevador 2:**

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **elevador**. |
    | **Nome** | Inserir **elevador 2**|
    | **Descrição** | Inserir **instância para o elevador 2** |
    | **Hierarquias** | Selecionar **hierarquia de local** |
    | **País** | Insira os **EUA** |
    | **Azul** | Digite **Seattle** |
    | **Elaboração** | Insira o **Pacífico Science Center** |

    **Para o elevador 3:**

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **elevador**. |
    | **Nome** | Inserir **elevador 3**|
    | **Descrição** | Inserir **instância para o elevador 3** |
    | **Hierarquias** | Selecionar **hierarquia de local** |
    | **País** | Insira os **EUA** |
    | **Azul** | Inserir **Nova York** |
    | **Elaboração** | **Criar criação de estado de Empires** |

    [![exibir as instâncias atualizadas.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Navegue de volta para a guia **analisar** para exibir o painel gráfico. Em **hierarquia de local**, expanda todos os níveis de hierarquia para exibir as instâncias de série temporal:

    [![exibir todas as hierarquias na exibição de gráfico.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. Em **centro de Ciências do Pacífico**, selecione o elevador de instância de série temporal **2**e selecione **Mostrar temperatura média**.

1. Para a mesma instância, **elevador 2**, selecione **Mostrar piso**.

    Com sua variável categórica, você pode determinar quanto tempo o elevador gastou nos andares superiores, inferiores e intermediários.

    [![Visualizar o elevador 2 com a hierarquia e os dados.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Agora que você concluiu o tutorial, limpe os recursos que criou:

1. No menu à esquerda na [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, localize o grupo de recursos Azure Time Series insights.
1. Exclua todo o grupo de recursos (e todos os recursos contidos nele) selecionando **excluir** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:  

* Crie e use um acelerador de simulação de dispositivo.
* Crie um ambiente de PAYG de visualização de Azure Time Series Insights.
* Conecte o ambiente de visualização de Azure Time Series Insights a um hub IOT.
* Execute um exemplo do Solution Accelerator para transmitir dados para o ambiente de visualização de Azure Time Series Insights.
* Execute uma análise básica dos dados.
* Defina um tipo de modelo de série temporal e uma hierarquia e associe-os às suas instâncias.

Agora que você sabe como criar seu próprio ambiente de visualização Azure Time Series Insights, saiba mais sobre os principais conceitos em Azure Time Series Insights.

Leia sobre a configuração de armazenamento Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Armazenamento de pré-visualização do Time Series Insights do Azure e de entrada](./time-series-insights-update-storage-ingress.md)

Saiba mais sobre os modelos de série de tempo:

> [!div class="nextstepaction"]
> [Modelação de dados do Time Series Insights pré-visualização do Azure](./time-series-insights-update-tsm.md)

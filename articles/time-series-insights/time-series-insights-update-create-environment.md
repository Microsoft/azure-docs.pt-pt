---
title: 'Tutorial: Criar um ambiente de pré-visualização - Azure Time Series Insights  Microsoft Docs'
description: 'Tutorial: Aprenda a criar um ambiente na Pré-visualização de Insights da Série De Tempo Azure.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: af15a7366fd07cecb376ff76ad383f784202a887
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526824"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: Configurar um ambiente de pré-visualização do Azure Time Series Insights

Este tutorial guia-o através do processo de criação de um ambiente de *pré-visualização* de insights da Série De Tempo Azure (PAYG).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um ambiente de pré-visualização de Insights da Série De Tempo Azure.
> * Ligue o ambiente de pré-visualização da Série de Tempo Azure a um Hub IoT.
> * Execute uma amostra de acelerador de soluções para transmitir dados para o ambiente de pré-visualização da Série de Tempo Azure Insights.
> * Execute análise básica nos dados.
> * Defina um modelo e hierarquia do Modelo série de tempo e associe-o aos seus casos.
> * Utilize o conector Power BI e visualize os dados no Power BI.

>[!TIP]
> [Os aceleradores de soluções IoT](https://www.azureiotsolutions.com/Accelerators) fornecem soluções pré-reconfiguradas de nível empresarial que pode usar para acelerar o desenvolvimento de soluções IoT personalizadas.

Inscreva-se para uma [subscrição Azure gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* No mínimo, deve ter o papel **de Colaborador** para a subscrição do Azure. Para mais informações, leia [Gerir o acesso utilizando o controlo de acesso baseado em papéis e o portal Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Nesta secção, irá criar três dispositivos simulados que enviam dados para uma instância do Hub Azure IoT.

1. Vá à página de aceleradores de [soluções Azure IoT](https://www.azureiotsolutions.com/Accelerators). A página apresenta vários exemplos pré-construídos. Inscreva-se utilizando a sua conta Azure. Em seguida, selecione **Simulação de dispositivo**.

   [![página de aceleradores de soluções Azure IoT.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Na página seguinte, selecione **Try Now**. Em seguida, introduza os parâmetros necessários na página de **solução de simulação** de dispositivo criar.

   Parâmetro|Descrição
   ---|---
   **Nome de implantação** | Este valor único é usado para criar um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Subscrição do Azure** | Especifique a mesma subscrição que foi usada para criar o seu ambiente Time Series Insights na secção anterior.
   **Opções de implementação** | Selecione **O novo Hub IoT** para criar um novo hub IoT específico para este tutorial.
   **Localização azure** | Especifique a mesma região que foi usada para criar o seu ambiente Time Series Insights na secção anterior.

   Quando terminar, selecione **Create** para fornecer os recursos Azure da solução. Pode levar até 20 minutos para concluir este processo.

   [![Fornecer a solução de simulação do dispositivo.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Após o fornecimento ter terminado, aparecerão duas notificações anunciando que o estado de implantação passou de **Provisioning** para **Ready**. 

   >[!IMPORTANT]
   > Não entre no acelerador de soluções ainda! Mantenha esta página aberta porque voltará mais tarde.

   [![os fornecimentos de solução de simulação do dispositivo completos.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Agora, inspecione os recursos recém-criados no portal Azure. Na página dos **grupos de Recursos,** note que foi criado um novo grupo de recursos utilizando o **nome Solution** fornecido no último passo. Tome nota dos recursos que foram criados para a simulação do dispositivo.

   [![recursos de simulação do dispositivo.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Criar um ambiente PAYG de pré-visualização

Esta secção descreve como criar um ambiente de pré-visualização da Série De Tempo Azure insights e ligá-lo ao hub IoT criado pelo Acelerador de Soluções IoT utilizando o [portal Azure](https://portal.azure.com/).

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a sua conta de subscrição Azure. 
1. Selecione **+ Criar um recurso** no canto superior esquerdo. 
1. Selecione a categoria **Internet das Coisas** e, em seguida, selecione Time Series **Insights**. 

   [![Selecione o recurso ambiental Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. No painel de **ambiente Create Time Series Insights,** no separador Basics, defina os **seguintes parâmetros:**

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Introduza um nome único para o ambiente de pré-visualização da Série de Tempo Azure Insights. |
    | **Subscrição** | Insira a subscrição onde pretende criar o ambiente de pré-visualização da Série de Tempo Azure Insights. A melhor prática é utilizar a mesma subscrição que os restantes recursos IoT que são criados pelo simulador do dispositivo. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um novo grupo de recursos para o recurso ambiente de pré-visualização da Série De Tempo Azure Insights. Um grupo de recursos é um contentor de recursos do Azure. A melhor prática é usar o mesmo grupo de recursos que os outros recursos IoT que são criados pelo simulador do dispositivo. |
    | **Localização** | Selecione uma região de data center para o seu ambiente de pré-visualização da Série de Tempo Azure Insights. Para evitar uma latência adicional, o melhor é criar o seu ambiente de pré-visualização de Insights da Série De Tempo Azure na mesma região que o seu hub IoT criado pelo simulador do dispositivo. |
    | **Nível** |  Selecione **PAYG** *(pay-as-you-go).* Este é o SKU para o produto de pré-visualização da Série De Tempo Azure Insights. |
    | **Nome da propriedade** | Introduza um valor que identifique exclusivamente a sua instância de série de horários. O valor que introduz na caixa de ID da **propriedade** não pode ser alterado mais tarde. Para este tutorial, ***introduza iothub-connection-device-id***. Para saber mais sobre id da Série Tempo, leia [as melhores práticas para escolher um ID](./time-series-insights-update-how-to-id.md)da Série De Tempo . |
    | **Nome da conta de armazenamento** | Introduza um nome globalmente único para uma nova conta de armazenamento.|
    |**Ativar loja quente**|Selecione **Sim** para ativar a loja quente. Pode voltar mais tarde e ativar esta definição. |
    |**Retenção de dados (em dias)**|Escolha a opção por defeito de 7 dias. |

    Selecione **Seguinte: Fonte de evento**.

   [![configuração ambiente new Time Series Insights.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![Configure id da série de tempo para o ambiente.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. No separador Origem do **Evento,** defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | --- |
   | **Criar uma fonte de evento?** | Selecione **Sim**.|
   | **Nome** | Introduza um valor único para o nome de origem do evento. |
   | **Tipo de fonte** | Selecione **IoT Hub**. |
   | **Selecione um hub** | Escolha **Selecione existente**. |
   | **Subscrição** | Selecione a subscrição que utilizou para o simulador do dispositivo. |
   | **Nome Do Hub IoT** | Selecione o nome do hub IoT que criou para o simulador do dispositivo. |
   | **Política de acesso ao Hub IoT** | Selecione **iothubowner**. |
   | **Grupo de consumidores IoT Hub** | Selecione **Novo,** introduza um nome único e, em seguida, selecione **+ Adicionar**. O grupo de consumidores deve ser um valor único na Pré-visualização da Série de Tempo Azure Insights. |
   | **Propriedade de carimbo de tempo** | Este valor é usado para identificar a propriedade **Timestamp** nos seus dados de telemetria. Para este tutorial, deixe esta caixa vazia. Este simulador utiliza o carimbo de tempo de entrada do IoT Hub, a que os Insights da Time Series não se adequem. |

   Selecione **Rever + Criar**.

   [![Configure o centro ioT criado como fonte de evento.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Selecione **Criar**.

    [![Rever + Criar página, com botão Criar.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Pode rever o estado da sua implantação:

    [![Notificação de que a implantação está completa.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Tem acesso ao seu ambiente de pré-visualização da Série De Tempo Azure Por padrão se for proprietário da subscrição Azure. Verifique se tem acesso:

   1. Procure o seu grupo de recursos e, em seguida, selecione o seu ambiente de pré-visualização da Série de Tempo Azure, recém-criado. 

      [![Selecione e veja o seu ambiente.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Na página de pré-visualização da Série de Tempo Azure Insights, selecione Políticas de Acesso a **Dados:**

      [![Verificar as políticas de acesso a dados.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Verifique se as suas credenciais estão listadas:

      Se as suas credenciais não estiverem listadas, deve conceder-se permissão para aceder ao ambiente selecionando Add e procurando as suas credenciais. Para saber mais sobre a definição de permissões, leia [o acesso aos dados do Grant.](./time-series-insights-data-access.md)

## <a name="stream-data"></a>Dados de transmissão em fluxo

Agora que implementou o seu ambiente time series Insights, comece a transmitir dados para análise.

1. Volte ao [Dashboard dos aceleradores de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard). Faça o sinal de novo, se necessário, utilizando a mesma conta Azure que tem usado neste tutorial. Selecione a sua "Solução de Dispositivo" e, em seguida, **vá ao acelerador de soluções** para lançar a sua solução implantada.

   [painel de aceleradores ![Solution.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. A aplicação web de simulação do dispositivo começa por lhe pedir que conceda à aplicação web o **Sinte e leia** a sua permissão de perfil. Esta permissão permite que a aplicação recupere as informações de perfil do utilizador necessárias para suportar o funcionamento da aplicação.

   [![consentimento de aplicação web de simulação do Dispositivo.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Selecione **+ Nova simulação**. Depois de a página de configuração de **simulação** ser carregada, introduza os parâmetros necessários.

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Introduza um nome único para um simulador. |
    | **Descrição** | Introduza uma definição. |
    | **Duração da simulação** | Definido para **correr indefinidamente**. |
    | **Modelo do dispositivo** | Clique + **Adicione um tipo de dispositivo** <br />**Nome**: Entrar **elevador**. <br />**Valor**: Insira **3**. <br /> Deixe os restantes valores predefinidos |
    | **Hub IoT de destino** | Definido para utilizar o **Hub IoT pré-provisionado**. |

    [![Configurar os parâmetros e lançar.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Selecione **Simulação Iniciar**.

    No painel de simulação do dispositivo, são **apresentados dispositivos Ativos** e **mensagens Totais.**

    [![painel de simulação Azure IoT.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analisar dados

Nesta secção, executa análises básicas nos dados da série de tempo utilizando o explorador de [pré-visualização](./time-series-insights-update-explorer.md)de Insights da Série De Tempo Azure .

1. Vá ao seu explorador de pré-visualização da Série de Tempo Azure Insights selecionando o URL da página de recursos no [portal Azure](https://portal.azure.com/).

    [![The Time Series Insights Preview explorer URL.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. No explorador Time Series Insights, aparecerá um bar que se estende pela parte superior do ecrã. Este é o seu apanhador de disponibilidade. Certifique-se de que tem pelo menos 2 m selecionados e, se necessário, expanda o período de tempo selecionando e arrastando as pegas do picker para a esquerda e para a direita.

1. **As instâncias** da Série time serão exibidas no lado esquerdo.

    [![Lista de casos não parentais.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Selecione a primeira vez da série. Em seguida, selecione **Mostrar temperatura**.

    [![Instância de série seleto com comando de menu para mostrar temperatura média.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Aparece um gráfico de séries de tempo. Mude o **intervalo** para **30.**

1. Repita o passo anterior com as outras duas instâncias da série time para que esteja a ver os três, como mostra este gráfico:

    [![Chart para séries de todos os tempos.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Selecione o marcador de tempo no canto superior direito. Aqui pode selecionar tempos de início e fim específicos até ao milissegundo, ou escolher entre opções pré-configuradas, como **Last 30 minutos**. Também pode alterar o fuso horário predefinido.

    [![Definir o intervalo de tempo para os últimos 30 minutos.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    O progresso do acelerador de solução ao longo dos **últimos 30 minutos** está agora apresentado no explorador time series Insights.

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta secção, aplica-se um modelo para estruturar os seus dados. Para completar o modelo, define tipos, hierarquias e instâncias. Para saber mais sobre modelação de dados, leia o [Modelo série de tempo.](./time-series-insights-update-tsm.md)

1. No explorador, selecione o separador **Modelo:**

   [![Ver o separador Modelo no explorador.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   No separador **Tipos,** **selecione + Adicionar**.

1. Introduza os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome** | Entrar **elevador** |
    | **Descrição** | Insira **Esta é uma definição de tipo para elevador** |

1. Em seguida, selecione o separador **Variáveis.** 

   Selecione **+ Adicione Variável** e preencha os seguintes valores para a primeira variável do tipo Elevador. Será autor de três variáveis no total.

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Introduza a **temperatura Avg**. |
    | **Tipo** | Selecione **numérico** |
    | **Valor** | Selecione a partir do predefinido: Selecione **temperatura (Duplo)** . <br /> Nota: Pode levar alguns minutos para o **Valor** ser automaticamente povoado após a Pré-visualização da Série de Tempo Azure começar a receber eventos.|
    | **Operação de Agregação** | Expandir **opções avançadas.** <br /> Selecione **AVG**. |

    Selecione **Aplicar**. Em seguida, **+ Adicionar Variável** novamente, e definir os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Introduza **avg Vibração**. |
    | **Tipo** | Selecione **numérico** |
    | **Valor** | Selecione a partir do predefinido: Selecione **vibração (Duplo)** . <br /> Nota: Pode levar alguns minutos para o **Valor** ser automaticamente povoado após a Pré-visualização da Série de Tempo Azure começar a receber eventos.|
    | **Operação de Agregação** | Expandir **opções avançadas.** <br /> Selecione **AVG**. |

    Selecione **Aplicar**. Em seguida, **+ Adicionar Variável** novamente, e definir os seguintes valores para a terceira e última variável:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Entrar **no chão.** |
    | **Tipo** | Selecione **Categórico** |
    | **Valor** | Selecione a partir do predefinido: Selecione **O Piso (Duplo)** . <br /> Nota: Pode levar alguns minutos para o **Valor** ser automaticamente povoado após a Pré-visualização da Série de Tempo Azure começar a receber eventos.|
    | **Categorias** | <span style="text-decoration: underline">Rótulo</span>  - <span style="text-decoration: underline">Valores</span> <br /> Menor: 1,2,3,4 <br /> Médio: 5,6,7,8,9 <br /> Superior: 10,11,12,13,14,15 |
    | **Categoria Padrão** | Entrar **desconhecido** |

    [![Adicionar variáveis do tipo.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Selecione **Aplicar**.

1. Selecione **Guardar**. Três variáveis são criadas e exibidas.

    [![Depois de adicionar o tipo, reveja-o na vista Modelo.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Selecione o separador **Hierárquica.** Em seguida, selecione **+ Adicionar**.
   
   No painel da Hierarquia de **Edição,** defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | ---|
   | **Nome** | Insira a Hierarquia de **Localização.** |
   |**Níveis**| Entrar **no País** como o nome do primeiro nível <br> Selecione **+ Adicionar Nível** <br> Entrar **na Cidade** para o segundo nível, em seguida, selecione + Adicionar **Nível** <br> Insira **edifício** como o nome do terceiro e último nível |

   Selecione **Guardar**.

   [![Mostrar a sua nova hierarquia na vista modelo.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navegar para **Instâncias.** No âmbito **de Ações** da extrema-direita, e selecione o ícone do lápis para editar a primeira instância com os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Entrar **no Elevador 1**|
    | **Descrição** | Insira **a instância para o elevador 1** |

    Navegue para **Campos de Instância** e introduza os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Hierarquias** | **Selecione Hierarquia de Localização** |
    | **País** | Entrar **nos EUA** |
    | **Cidade** | Entrar em **Seattle** |
    | **Edifício** | Insira **a agulha espacial** |

    Selecione **Guardar**.

1. Repita o passo anterior com as outras duas instâncias utilizando os seguintes valores:

    **Para o Elevador 2:**

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Entrar **no Elevador 2**|
    | **Descrição** | Insira **a instância para o elevador 2** |
    | **Hierarquias** | **Selecione Hierarquia de Localização** |
    | **País** | Entrar **nos EUA** |
    | **Cidade** | Entrar em **Seattle** |
    | **Edifício** | Entrar **no Centro de Ciência do Pacífico** |

    **Para o Elevador 3:**

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Entrar **no Elevador 3**|
    | **Descrição** | Insira **a instância para o elevador 3** |
    | **Hierarquias** | **Selecione Hierarquia de Localização** |
    | **País** | Entrar **nos EUA** |
    | **Cidade** | Entra **Nova Iorque** |
    | **Edifício** | Entrar no **Empire State Building** |

    [![Ver as instâncias atualizadas.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Navegue de volta ao separador **Analisar** para ver o painel de gráficos. Sob a Hierarquia de **Localização,** expanda todos os níveis de hierarquia para exibir as instâncias da série de tempo:

    [![Ver todas as hierarquias à vista de gráficos.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. No **Pacific Science Center,** selecione o Time Series Instance **Elevator 2**, e, em seguida, selecione Mostrar **temperatura média**.

1. Para o mesmo caso, **Elevador 2**, selecione **Show Floor**.

    Com a sua variável categórica, você pode determinar quanto tempo o elevador gastou nos andares superiores, inferiores e médios.

    [![Visualizar o Elevador 2 com hierarquia e dados.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Agora que completou o tutorial, limpe os recursos que criou:

1. A partir do menu esquerdo no [portal Azure](https://portal.azure.com), selecione **Todos os recursos,** localize o seu grupo de recursos Azure Time Series Insights.
1. Ou elimina todo o grupo de recursos (e todos os recursos contidos nele) selecionando **Apagar** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:  

* Crie e utilize um acelerador de simulação do dispositivo.
* Crie um ambiente PAYG da Série De Tempo Azure.
* Ligue o ambiente de pré-visualização da Série de Tempo Azure a um hub iot.
* Execute uma amostra de acelerador de soluções para transmitir dados para o ambiente de pré-visualização da Série de Tempo Azure Insights.
* Execute uma análise básica dos dados.
* Defina um modelo e hierarquia do Modelo série de tempo e associe-os aos seus casos.

Agora que sabe como criar o seu próprio ambiente de pré-visualização da Série De Tempo Azure Insights, saiba mais sobre os conceitos-chave em Azure Time Series Insights.

Leia sobre a configuração de armazenamento da Série de Tempo Azure Insights:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Preview armazenamento e ingresso](./time-series-insights-update-storage-ingress.md)

Saiba mais sobre os modelos de série de tempo:

> [!div class="nextstepaction"]
> [Modelação de dados de pré-visualização da Série De Tempo Azure Insights](./time-series-insights-update-tsm.md)

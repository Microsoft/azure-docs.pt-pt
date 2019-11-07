---
title: 'Tutorial: configurar um ambiente de visualização de Azure Time Series Insights | Microsoft Docs'
description: Saiba como configurar seu ambiente no Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: 25571dbd87e4d01645a3a7a991588a3a943b3e4d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719766"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: configurar um ambiente de visualização de Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de um ambiente PAYG (pré-pago) de visualização de Azure Time Series Insights.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um ambiente de visualização de Azure Time Series Insights.
> * Conecte o ambiente de visualização de Azure Time Series Insights a um hub IoT.
> * Execute um exemplo do Solution Accelerator para transmitir dados para o ambiente de visualização de Azure Time Series Insights.
> * Execute a análise básica nos dados.
> * Defina um tipo de modelo de série temporal e uma hierarquia e associe-o às suas instâncias.
> * Use o conector de Power BI e visualize dados no Power BI.

>[!TIP]
> Os [aceleradores de solução de IOT](https://www.azureiotsolutions.com/Accelerators) fornecem soluções pré-configuradas de nível empresarial que você pode usar para acelerar o desenvolvimento de soluções de IOT personalizadas.

Inscreva-se para obter uma [assinatura gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* No mínimo, você deve ter a função de **colaborador** para a assinatura do Azure. Para obter mais informações, consulte [gerenciar o acesso usando o controle de acesso baseado em função e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivos

Nesta seção, você criará três dispositivos simulados que enviam dados para uma instância do Hub IoT do Azure.

1. Vá para a [página aceleradores de soluções do Azure IOT](https://www.azureiotsolutions.com/Accelerators). A página exibe vários exemplos predefinidos. Entre usando sua conta do Azure. Em seguida, selecione **simulação de dispositivo**.

   [página de aceleradores de solução do Azure IoT ![](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. Na página seguinte, selecione **experimentar agora**.

   [página de simulação de dispositivo ![](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. Na página **criar solução de simulação de dispositivo** , defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome da implantação** | Insira um valor exclusivo para um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos. |
    | **Subscrição do Azure** | Selecione a assinatura na qual você criará seu ambiente de Time Series Insights. |
    | **Local do Azure** | Selecione a região em que você pretende armazenar seu ambiente de Time Series Insights. Observe que o simulador de dispositivo só é oferecido em um número limitado de regiões, portanto, se você não vir a região desejada, poderá optar por selecionar um local exclusivamente para o tutorial e, em seguida, criar um novo ambiente de TSI quando estiver pronto para passar para a próxima fase de on-b oarding.  |
    | **Opções de implantação** | Selecione **provisionar novo hub IOT**. |

    1. Selecione **Criar**.
    [página de simulação de dispositivo ![](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Após cerca de 20 minutos, o Solution Accelerator estará pronto.

    [página de simulação de dispositivo ![](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Criar um ambiente de PAYG de visualização

Esta seção descreve como criar um ambiente de visualização Azure Time Series Insights e conectá-lo ao Hub IoT criado pelo acelerador de solução IoT usando o [portal do Azure](https://portal.azure.com/).

1. Entre no portal do Azure usando sua conta de assinatura.

1. Selecione **criar um recurso** > **Internet das coisas** > **Time Series insights**.

   [![selecione Internet das Coisas e, em seguida, selecione Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. No painel **criar Time Series insights ambiente** , na guia **noções básicas** , defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Insira um nome exclusivo para o ambiente de visualização de Azure Time Series Insights. |
    | **Subscrição** | Insira a assinatura na qual você deseja criar o ambiente de visualização de Azure Time Series Insights. Uma prática recomendada é usar a mesma assinatura que o restante dos recursos de IoT criados pelo Device Simulator. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um novo grupo de recursos para o Azure Time Series Insights recurso de ambiente de visualização. Um grupo de recursos é um contentor de recursos do Azure. Uma prática recomendada é usar o mesmo grupo de recursos que os outros recursos de IoT criados pelo Device Simulator. |
    | **Localização** | Selecione uma região de data center para seu ambiente de Azure Time Series Insights visualização. Para evitar latência adicional, é melhor criar seu Azure Time Series Insights ambiente de visualização na mesma região que o Hub IoT criado pelo Device Simulator. |
    | **Preço** |  Selecione **PAYG** (pré-*pago*). Este é o SKU para o produto de visualização de Azure Time Series Insights. |
    | **ID da propriedade** | Insira um valor que identifique exclusivamente sua instância de série temporal. O valor que você inserir na caixa **ID da propriedade** não poderá ser alterado posteriormente. Para este tutorial, digite **iothub-Connection-Device-ID**. Para saber mais sobre a ID da série temporal, consulte [práticas recomendadas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md). |
    | **Nome da conta de armazenamento** | Insira um nome globalmente exclusivo para uma nova conta de armazenamento.|
    |**Habilitar armazenamento quente**|Selecione **Sim** para habilitar o armazenamento quente.|
    |**Retenção de dados (em dias)**|Escolha a opção padrão de 7 dias. |

    Selecione **Avançar: origem do evento**.

   [painel de![para criar um ambiente de Time Series Insights](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [painel de![para criar um ambiente de time Series insights](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

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

   [![configurar uma origem do evento](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Selecione **Criar**.

    [![revisar + criar página, com o botão criar](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Você pode ver o status da sua implantação:

    [![notificação de que a implantação foi concluída](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Você tem acesso ao ambiente de visualização do Azure Time Series Insights por padrão se for um proprietário da assinatura do Azure. Verifique se você tem acesso:

   1. Pesquise seu grupo de recursos e, em seguida, selecione o ambiente de visualização do Azure Time Series Insights criado recentemente. 
      [![ambiente selecionado](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na página Azure Time Series Insights visualização, selecione **políticas de acesso a dados**: políticas de acesso a [dados![](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Verifique se suas credenciais estão listadas:

      [![as credenciais listadas](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Se suas credenciais não estiverem listadas, você deverá conceder permissão para acessar o ambiente selecionando Adicionar e pesquisando suas credenciais. Para saber mais sobre como definir permissões, leia [conceder acesso a dados](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Dados de transmissão em fluxo

Agora que você implantou seu ambiente de Time Series Insights, comece a transmitir dados para análise.

1. Navegue de volta para a [página aceleradores de solução do Azure IOT](https://www.azureiotsolutions.com/Accelerators). Localize sua solução no seu painel do Solution Accelerator e, em seguida, selecione **Iniciar**:

    [![iniciar a solução de simulação de dispositivo](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Selecione **ir para o Solution Accelerator**.

    [![iniciar a solução de simulação de dispositivo](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. Você será redirecionado para a página de **simulação de dispositivo do Microsoft Azure IOT** . No canto superior direito da página, selecione **nova simulação**.

    [página de simulação do Azure IoT ![](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. No painel **configuração de simulação** , defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira um nome exclusivo para um simulador. |
    | **Descrição** | Insira uma definição. |
    | **Duração da simulação** | Defina para **executar indefinidamente**. |
    | **Modelo do dispositivo** | Clique em + **Adicionar um tipo de dispositivo** <br />**Nome**: Insira o **elevador**. <br />**Valor**: insira **3**. <br /> Deixar os valores padrão restantes |
    | **Hub IoT de destino** | Defina para **usar o Hub IOT provisionado previamente**. |

    [![parâmetros a serem definidos](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Selecione **Iniciar simulação**.

    No painel de simulação de dispositivo, você verá **dispositivos ativos** e **total de mensagens**.

    [painel de simulação do Azure IoT ![](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analisar dados

Nesta seção, você executará a análise básica nos dados de série temporal usando o [Gerenciador de visualização Azure Time Series insights](./time-series-insights-update-explorer.md).

1. Acesse o Azure Time Series Insights Explorer Preview selecionando a URL na página de recursos no [portal do Azure](https://portal.azure.com/).

    [![a URL do Gerenciador de visualização Time Series Insights](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. No Time Series Insights Explorer, você verá uma barra abrangendo a parte superior da tela. Este é seu seletor de disponibilidade. Verifique se você tem pelo menos dois 2m selecionados e, se necessário, expanda o intervalo de tempo selecionando e arrastando os identificadores do seletor para a esquerda e para a direita.

1. Você poderá ver as instâncias de **série temporal** à esquerda.


    [Lista de ![de instâncias não pai](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Selecione a primeira instância de série temporal. Em seguida, selecione **Mostrar pressão**.

    [![instância de série temporal selecionada com comando de menu para mostrar a pressão média](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Um gráfico de série temporal é exibido. Altere o **intervalo** para **30s**.

    [gráfico de série temporal ![](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Repita a etapa 3 com as outras duas instâncias de série temporal para que você esteja exibindo todas as três, conforme mostrado neste gráfico:

    [Gráfico de ![para todas as séries temporais](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Selecione o seletor de intervalo de tempo no canto superior direito. Aqui você pode selecionar horários de início e término específicos até o milissegundo ou escolher entre as opções pré-configuradas, como a última hora. Você também pode alterar o fuso horário padrão.

    [![definir o intervalo de tempo para uma hora](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Abaixo está uma captura de tela do painel de gráfico depois de executar a simulação por uma hora:

    [![painel de gráfico](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta seção, você aplica um modelo para estruturar seus dados. Para concluir o modelo, você define tipos, hierarquias e instâncias. Para saber mais sobre modelagem de dados, consulte [modelo de série temporal](./time-series-insights-update-tsm.md).

1. No Gerenciador, selecione a guia **modelo** :

   [guia modelo de ![no Gerenciador](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Na guia **tipos** , selecione **Adicionar**.

   [![o botão Adicionar para tipos](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Insira os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome** | Inserir **elevador** |
    | **Descrição** | Insira **esta é uma definição de tipo para o elevador** |

    [![o botão Adicionar para tipos](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Em seguida, selecione a guia **variáveis** . [![guia Selecionar variáveis](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. Selecione **+ Adicionar variável** e preencha os valores a seguir para a primeira variável do tipo de elevador. Você criará três variáveis no total.

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira a **temperatura média**. |
    | **Quase** | Selecionar **numérico** |
    | **Valor** | Selecione de predefinição: selecione **temperatura (dupla)** . <br /> Observação: pode levar alguns minutos para que o **valor** seja preenchido automaticamente depois que Azure Time Series insights visualização começar a receber eventos.|
    | **Operação de agregação** | Expanda **Opções avançadas**. <br /> Selecione **Méd**. |

    [![seleções para definir a temperatura](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    Selecione **Aplicar**.

    Selecione **+ Adicionar variável** novamente e defina os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira a **média de vibração**. |
    | **Quase** | Selecionar **numérico** |
    | **Valor** | Selecione de predefinição: selecione **vibração (dupla)** . <br /> Observação: pode levar alguns minutos para que o **valor** seja preenchido automaticamente depois que Azure Time Series insights visualização começar a receber eventos.|
    | **Operação de agregação** | Expanda **Opções avançadas**. <br /> Selecione **Méd**. |

    [![seleções para definir a vibração](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    Selecione **Aplicar**.

    Selecione **+ Adicionar variável** novamente e defina os seguintes valores para a terceira e última variável:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira o **piso**. |
    | **Quase** | Selecionar **categórico** |
    | **Valor** | Selecione de predefinição: selecione **andar (duplo)** . <br /> Observação: pode levar alguns minutos para que o **valor** seja preenchido automaticamente depois que Azure Time Series insights visualização começar a receber eventos.|
    | **Às** | <span style="text-decoration: underline">Valores</span> de  - de <span style="text-decoration: underline">rótulo</span> <br /> Inferior: 1, 2, 3, 4 <br /> Meio: 5, 6, 7, 8, 9 <br /> Upper: 10, 11, 12, 13, 14, 15 |
    | **Categoria padrão** | Inserir **desconhecido** |

    [![seleções para definir a vibração](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    Selecione **Aplicar**. Você deve ver três variáveis criadas:

    [![seleções para definir a vibração](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   Selecione **Guardar**. Você verá o **tipo** criado:

    [![seleções para definir a vibração](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. Selecione a guia **hierarquias** . Selecione **+ Adicionar**.

    [guia hierarquias ![com o botão Adicionar](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. No painel **Editar hierarquia** , defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | ---|
   | **Nome** | Insira a **hierarquia de localização**. |
   |**Alcançar**| Insira o **país** como o nome do primeiro nível <br> Selecionar **+ Adicionar nível** <br> Insira **cidade** para o segundo nível e, em seguida, selecione **+ Adicionar nível** <br> Insira a **compilação** como o nome do terceiro e último nível |

   Selecione **Guardar**.

    [![campos de hierarquia com o botão criar](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Você pode ver a hierarquia que você criou:

    [![informações sobre a hierarquia](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Navegue até **instâncias**. Em **ações** à extrema direita e selecione o ícone de lápis para editar a primeira instância com os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **elevador**. |
    | **Nome** | Inserir **elevador 1**|
    | **Descrição** | Inserir **instância para o elevador 1** |

    [![selecionando o botão Editar para uma instância](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    Navegue até os **campos de instância** e insira o seguinte:

    | Parâmetro | Ação |
    | --- | --- |
    | **Hierarquias** | Selecionar **hierarquia de local** |
    | **País** | Insira os **EUA** |
    | **Azul** | Digite **Seattle** |
    | **Elaboração** | Inserir a **agulha de espaço** |

    [![selecionando o botão Editar para uma instância](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    Selecione **Guardar**.

1. Repita a etapa 8 com as outras duas instâncias com os seguintes valores:

    <span style="text-decoration: underline;">Para o elevador 2</span>:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **elevador**. |
    | **Nome** | Inserir **elevador 2**|
    | **Descrição** | Inserir **instância para o elevador 2** |
    | **Hierarquias** | Selecionar **hierarquia de local** |
    | **País** | Insira os **EUA** |
    | **Azul** | Digite **Seattle** |
    | **Elaboração** | Insira o **Pacífico Science Center** |

    <span style="text-decoration: underline;">Para o elevador 3</span>:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **elevador**. |
    | **Nome** | Inserir **elevador 3**|
    | **Descrição** | Inserir **instância para o elevador 3** |
    | **Hierarquias** | Selecionar **hierarquia de local** |
    | **País** | Insira os **EUA** |
    | **Azul** | Inserir **Nova York** |
    | **Elaboração** | **Criar criação de estado de Empires** |

1. Navegue de volta para a guia **analisar** para exibir o painel gráfico. Em **hierarquia de local**, expanda todos os níveis de hierarquia para exibir as instâncias de série temporal:

   [![a guia analisar](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Em **centro de Ciências do Pacífico**, selecione o elevador de instância de série temporal **2**e selecione **Mostrar temperatura média**.

    [Instância de série temporal ![mostrar temperatura média](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. Para a mesma instância, **elevador 2**, selecione **Mostrar piso**.

    [![instância de série temporal mostrar piso](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    Com sua variável categórica, você pode determinar quanto tempo o elevador gastou nos andares superiores, inferiores e intermediários.

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
> [Azure Time Series Insights Visualizar o armazenamento e a entrada](./time-series-insights-update-storage-ingress.md)

Saiba mais sobre os modelos de série temporal:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Visualizar a modelagem de dados](./time-series-insights-update-tsm.md)

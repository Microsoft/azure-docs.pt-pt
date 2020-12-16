---
title: 'Tutorial: Criar uma app de monitorização do consumo de água com a Azure IoT Central'
description: 'Tutorial: Aprenda a criar uma aplicação de monitorização do consumo de água utilizando modelos de aplicação Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 476a0c2d75dcbee5c1ed4d758e0cbc9d4726ff95
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587201"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Tutorial: Criar uma aplicação de monitorização do consumo de água com a Azure IoT Central

Este tutorial mostra-lhe como criar uma aplicação de monitorização do consumo de água Azure IoT Central utilizando o modelo de aplicação de monitorização do consumo de água Azure IoT Central.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Utilize o modelo de monitorização do consumo de água Azure IoT Central para criar a sua aplicação de monitorização do consumo de água.
> * Explore e personalize o painel de instrumentos do operador.
> * Explore os modelos do dispositivo.
> * Explore dispositivos simulados.
> * Explore e configuure regras.
> * Criar trabalhos.
> * Personalize a sua marca de aplicação utilizando a rotulagem branca.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma na página de inscrição do [Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Criar uma app de monitorização do consumo de água com a Azure IoT Central

Nesta secção, utilize o modelo de monitorização do consumo de água Azure IoT Central para criar a sua aplicação de monitorização do consumo de água no Azure IoT Central.

Para criar uma nova aplicação de monitorização do consumo de água Azure IoT Central:

1. Vá ao site da página inicial do [Azure IoT Central.](https://aka.ms/iotcentral)

    Se tiver uma assinatura Azure, inscreva-se com as credenciais que utiliza para aceder à sua base. Caso contrário, inscreva-se utilizando uma conta Microsoft.

    ![Introduza a sua conta da organização](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. **Selecione Construir** no painel esquerdo e selecione o separador **Governo.** A página **do Governo** exibe vários modelos de candidatura do governo.

   ![Construa modelos de aplicativos do governo](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selecione o modelo de aplicação **de monitorização do consumo de água.**
Este modelo inclui um modelo de dispositivo de consumo de água de amostra, um dispositivo simulado, um painel de instrumentos do operador e regras de monitorização pré-configuradas.

1. Selecione **Criar app** para abrir o novo formulário de criação **de aplicações** com os seguintes campos:
    * **Nome da aplicação**: Por padrão, a aplicação utiliza *a monitorização do consumo de água* seguida de uma cadeia de ID única que a Azure IoT Central gera. Opcionalmente, escolha um nome de aplicação amigável. Também pode alterar o nome da candidatura mais tarde.
    * **URL**: Azure IoT Central autogere um URL baseado no nome da aplicação. Pode optar por atualizar o URL ao seu gosto. Também pode alterar a URL mais tarde.
    * Se tiver uma subscrição do Azure, insira o seu **Diretório,** **subscrição Azure** e informações **de Localização.** Se não tiver uma subscrição, pode selecionar a opção **de teste gratuito de 7 dias** e completar as informações de contacto necessárias.

    Para obter mais informações sobre diretórios e subscrições, consulte [Criar um quickstart de aplicação](../core/quick-deploy-iot-central.md).

1. Selecione **Criar** na parte inferior da página.

    ![Página de aplicação Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Página de informações de Faturação Central Azure IoT](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Criou agora uma aplicação de monitorização do consumo de água utilizando o modelo de monitorização do consumo de água Azure IoT Central.

A aplicação de monitorização do consumo de água vem com pré-configuração:

* Paineles de operador de amostra.
* Prove os modelos de fluxo de água pré-definido e de dispositivos de válvula.
* Fluxo de água simulado e dispositivos de válvula inteligente.
* Regras e empregos.
* Marca de amostra utilizando a rotulagem branca.

É a sua candidatura, e pode modificá-la a qualquer momento. Agora vamos explorar a aplicação e fazer algumas personalizações.

## <a name="explore-and-customize-the-operator-dashboard"></a>Explore e personalize o painel de instrumentos do operador

Depois de criar a aplicação, abre-se o **painel de consumo de água Wide World.**

   ![Painel de monitorização do consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Como construtor, pode criar e personalizar vistas no painel de instrumentos para os operadores. Vamos explorar o painel antes de tentar personalizá-lo.

> [!NOTE]
> Todos os dados apresentados no painel de instrumentos são baseados em dados simulados do dispositivo, que iremos explorar na próxima secção.
  
O painel é composto por diferentes tipos de azulejos:

* **Azulejo de imagem de utilidade de água em todo** o mundo : O primeiro azulejo no tablier é um azulejo de imagem do utilitário de água fictício Wide World Water. Pode personalizar o azulejo inserindo a sua própria imagem ou removendo-a.
* **Caudal médio de água KPI**: O azulejo KPI está configurado para mostrar como exemplo *a média dos últimos 30 minutos*. Pode personalizar o azulejo KPI e defini-lo para um tipo e intervalo de tempo diferentes.
* **Telhas de comando do dispositivo**: Estes azulejos incluem a válvula de **fecho,** **a válvula aberta** e os azulejos da válvula de **fixação.** A seleção dos comandos leva-o à página de comando do dispositivo simulado. No Azure IoT Central, um *comando* é um tipo *de capacidade de dispositivo.* Vamos explorar este conceito mais tarde na secção "Modelo de Dispositivo" deste tutorial.
* **Mapa da área de distribuição de água**: O mapa utiliza mapas Azure, que pode configurar diretamente no Azure IoT Central. O azulejo do mapa mostra a localização do dispositivo. Passe por cima do mapa e experimente os controlos sobre o mapa, como *zoom in,* *zoom out*, ou *expandir*.

    ![Mapa do painel de monitorização do consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Gráfico médio da linha de fluxo de água** e gráfico de linha de condição **ambiental**: Pode visualizar uma ou múltiplas telemetrias de dispositivos traçadas como um gráfico de linha sobre um intervalo de tempo desejado.
* **Gráfico de mapas de calor de pressão média** da válvula : Pode escolher o tipo de visualização de mapas de calor dos dados de telemetria do dispositivo que pretende ver distribuídos por um intervalo de tempo com um índice de cor.
* **Redefinir limiares de alerta em azulejo de conteúdo**: Pode incluir azulejos de conteúdo call-to-action e incorporar um link para uma página de ação. Neste caso, o limiar de alerta de reset leva-o à aplicação **Jobs,** onde pode executar atualizações para as propriedades do dispositivo. Vamos explorar esta opção mais tarde na secção "Configure jobs" deste tutorial.
* **Azulejos de propriedade**: O painel de instrumentos exibe **informações operacionais da Valve,** **limiares de alerta de fluxo** e azulejos de **informação de manutenção.**

### <a name="customize-the-dashboard"></a>Personalizar o dashboard

Como construtor, pode personalizar vistas no painel de instrumentos para os operadores.

1. **Selecione Editar** para personalizar o **painel de consumo de água wide world**. Pode personalizar o painel de instrumentos selecionando o menu **Editar.** Depois de o painel de instrumentos estar em modo **de edição,** pode adicionar novos azulejos ou configurá-lo.

     ![Editar dashboard](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Selecione **+ Novo** para criar um novo painel de instrumentos e configurá-lo do zero. Pode ter vários dashboards e pode mover-se entre os painéis de instrumentos no menu do painel de instrumentos.

## <a name="explore-the-device-template"></a>Explore o modelo do dispositivo

Um modelo de dispositivo no Azure IoT Central define a capacidade de um dispositivo, que pode ser telemetria, propriedade ou comando. Como construtor, pode definir um ou mais modelos de dispositivo no Azure IoT Central que representam a capacidade dos dispositivos que irá ligar.

A aplicação de monitorização do consumo de água vem com dois modelos de dispositivo de referência que representam um *medidor de fluxo* e um dispositivo *de válvula inteligente.*

Para ver o modelo do dispositivo:

1. Selecione **os modelos** do dispositivo no painel esquerdo da sua aplicação no Azure IoT Central. Na lista de modelos do **dispositivo,** verá dois modelos de dispositivo, **Smart Valve** e Flow **meter**.

   ![Modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Selecione o modelo do dispositivo **do medidor de fluxo** e familiarize-se com as capacidades do dispositivo.

     ![Medidor de fluxo de modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Personalize o modelo do dispositivo

Para personalizar o modelo do dispositivo:

1. Vá para **Personalizar** no menu **de modelos de dispositivo.**
1. Encontre o `Temperature` tipo de telemetria.
1. Atualize o **Nome do Visor** de `Temperature` `Reported temperature` .
1. Atualize a unidade de medição ou desave o **valor Min** e **Max**.
1. **Selecione Guardar** para guardar quaisquer alterações.

### <a name="add-a-cloud-property"></a>Adicione uma propriedade em nuvem

1. Aceda às **propriedades da nuvem** no menu de **modelos do dispositivo.**
1. Adicione uma nova propriedade em nuvem selecionando **+ Add Cloud Property**.
    No Azure IoT Central, pode adicionar uma propriedade relevante para o dispositivo. Como exemplo, uma propriedade em nuvem pode ser um limiar de alerta específico para uma área de instalação, informações de bens ou outras informações de manutenção.
1. **Selecione Guardar** para guardar quaisquer alterações.

### <a name="views"></a>Vistas

O modelo do dispositivo do monitor de consumo de água vem com vistas predefinidas. Explore as vistas e poderá fazer atualizações. As vistas definem como os operadores vêem os dados do dispositivo, mas também introduzem propriedades na nuvem.

  ![Vistas do modelo do dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicar

Se escoar alguma alteração, certifique-se de **publicar** o modelo do dispositivo.

### <a name="create-a-new-device-template"></a>Criar um novo modelo do dispositivo

Selecione **+ Novo** para criar um novo modelo de dispositivo e siga o processo de criação.
Pode criar um modelo de dispositivo personalizado de raiz ou pode escolher um modelo de dispositivo a partir do Catálogo de Dispositivos Azure.

## <a name="explore-simulated-devices"></a>Explore dispositivos simulados

No Azure IoT Central, pode criar dispositivos simulados para testar o modelo e aplicação do seu dispositivo. A aplicação de monitorização do consumo de água tem dois dispositivos simulados mapeados para o **medidor de fluxo** e modelos de dispositivos **Smart Valve.**

### <a name="view-the-devices"></a>Ver os dispositivos

1. **Selecione Dispositivos**  >  **Todos os dispositivos** no painel esquerdo.

   ![Painel de todos os dispositivos](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Selecione **Smart Valve 1**.

    ![Válvula Inteligente 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. No separador **Comandos,** pode ver os três comandos do dispositivo (**Válvula de fecho,** **válvula aberta** e **posição da válvula definida**) que são capacidades definidas no modelo do dispositivo Smart **Valve.**

1. Explore o separador Propriedades do **Dispositivo** e o **separador dashboard do dispositivo.**

> [!NOTE]
> Note que todos os separadores estão configurados a partir das vistas do modelo do dispositivo.

### <a name="add-new-devices"></a>Adicionar novos dispositivos

Adicione novos dispositivos selecionando **+ Novo** no **separador Dispositivos.**

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, pode criar regras para monitorizar automaticamente a telemetria do dispositivo e desencadear ações quando uma ou mais condições forem satisfeitas. As ações podem incluir o envio de notificações por e-mail ou o desencadear de uma ação do Microsoft Power Automamate ou uma ação webhook para enviar dados para outros serviços.

A aplicação de monitorização do consumo de água que criou tem três regras pré-configuradas.

### <a name="view-rules"></a>Ver regras

1. Selecione **Regras** no painel esquerdo.

   ![Painel de regras](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Selecione **alerta de pH elevado,** que é uma das regras pré-configuradas na aplicação.

     ![Alerta de pH elevado](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    A `High flow alert` regra está configurada para verificar contra a condição `Acidity (pH)` é a `greater than` `Max flow threshold` . O limiar de fluxo max é uma propriedade em nuvem definida no modelo do dispositivo **Smart Valve.** O valor `Max flow threshold` é definido por instância do dispositivo.

Agora vamos criar uma ação por e-mail.

Para adicionar uma ação à regra:

1. Selecione **+ E-mail**.
1. Introduza **o alerta de pH alto** como o nome de **exibição** amigável para a ação.
1. Insira o endereço de e-mail associado à sua conta Azure IoT Central em **To**.
1. Opcionalmente, insira uma nota para incluir no texto do e-mail.
1. Selecione **Feito** para completar a ação.
1. **Selecione Guardar** para guardar e ativar a nova regra.

Dentro de poucos minutos, deverá receber um e-mail após o encontro da condição configurada.

> [!NOTE]
> O pedido envia um e-mail cada vez que uma condição é satisfeita. **Selecione Desativar** para desativar a regra para deixar de receber e-mail a partir da regra automatizada.
  
Para criar uma nova regra:

* Selecione **+ Novo** no separador **Regras** no painel esquerdo.

## <a name="configure-jobs"></a>Configurar tarefas

No Azure IoT Central, os trabalhos permitem-lhe ativar atualizações de propriedades de dispositivos ou nuvem em vários dispositivos. Além das propriedades, também pode usar trabalhos para ativar comandos de dispositivos em vários dispositivos. A Azure IoT Central automatiza o fluxo de trabalho para si.

1. Selecione **Jobs** no painel esquerdo.
1. Selecione **+ Novos**, e configuure um ou mais empregos.

## <a name="customize-your-application"></a>Personalize a sua aplicação

Como construtor, pode alterar várias definições para personalizar a experiência do utilizador na sua aplicação.

1. **Selecione Administração**  >  **Personalize a sua aplicação.**
1. Para escolher uma imagem para carregar como **o logótipo 'Aplicação',** selecione o botão **Alterar.**
1. Para escolher uma imagem **de ícone do Navegador** que aparecerá nos separadores do navegador, selecione o botão **Alterar.**
1. Também pode substituir as cores padrão do Navegador adicionando **códigos** de cores hexadémicas HTML.

   ![Seleções para logotipo de aplicação, ícone de navegador e cores do navegador](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Também pode alterar imagens de aplicações selecionando definições de  >  **Aplicação de** Administração . Para escolher uma imagem para carregar como imagem de aplicação, selecione o botão **de imagem Select.**
1. Por fim, também pode alterar o **Tema** selecionando o ícone **Definições** no canto superior direito da aplicação.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a utilizar esta aplicação, elimine-a.

1. Selecione **Administração** no painel esquerdo da sua aplicação Azure IoT Central.
1. Selecione **as definições de Aplicação** e, em seguida, selecione **Eliminar** na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

* Mais informações sobre 

> [!div class="nextstepaction"]
> [Conceitos de monitorização do consumo de água.](./concepts-waterconsumptionmonitoring-architecture.md)

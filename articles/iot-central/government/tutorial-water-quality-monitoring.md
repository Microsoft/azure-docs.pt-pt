---
title: 'Tutorial: Criar uma app de monitorização da qualidade da água com a Azure IoT Central'
description: 'Tutorial: Aprenda a criar uma aplicação de monitorização da qualidade da água utilizando modelos de aplicação Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 77ec999d63175f63c1de6e31fdb3f72c963d228c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82024477"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Tutorial: Criar uma aplicação de monitorização da qualidade da água na Central Azure IoT



Este tutorial guia-o através da criação de uma aplicação de monitorização da qualidade da água no Azure IoT Central. Cria a aplicação a partir do modelo de aplicação de **monitorização** da qualidade da água central Azure IoT.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Utilize o modelo de **monitorização** da qualidade da água para criar uma aplicação de monitorização da qualidade da água.
> * Explore e personalize um painel de instrumentos de operador.
> * Explore um modelo de dispositivo de monitorização da qualidade da água.
> * Explore dispositivos simulados.
> * Explore e configure regras.
> * Configure empregos.
> * Personalize a marca de aplicação utilizando a rotulagem branca.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que tenha uma subscrição Azure para completar este tutorial. Se não tiver uma subscrição Azure, pode criar uma na [página de inscrição](https://aka.ms/createazuresubscription)do Azure .

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Criar uma aplicação de monitorização da qualidade da água no Azure IoT Central

Nesta secção, utiliza o modelo de **monitorização** da qualidade da água central Azure IoT para criar uma aplicação de monitorização da qualidade da água.

1. Vá à página inicial do [Azure IoT Central.](https://aka.ms/iotcentral)

    Se tiver uma assinatura Azure, inscreva-se nas credenciais que utiliza para aceder ao mesmo. Caso contrário, inscreva-se numa conta microsoft:

    ![Inscreva-se na sua conta de organização](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Selecione **Construir** no painel mais à esquerda do Azure IoT Central e selecione o separador **Governo.** O painel do governo mostra vários modelos de candidatura do governo.

    ![Modelos de aplicação do governo](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Selecione o modelo de aplicação de monitorização da **qualidade da água.** Este modelo de aplicação inclui um modelo de dispositivo de qualidade da água, dispositivos simulados, um painel de instrumentos do operador e regras de monitorização pré-configuradas.

1. Selecione **Criar aplicação**. O novo painel de **aplicações** abre e mostra os seguintes elementos:

    * Nome da **aplicação**: Por padrão, o nome da aplicação é **monitorização** da qualidade da água seguida de uma cadeia de ID única que o Azure IoT Central gera. Se quiser, pode introduzir um nome de exibição ou alterar o nome da aplicação mais tarde.
    * **URL**: Pode introduzir qualquer URL que queira ou alterar o valor url mais tarde.
    * Se tiver uma assinatura Azure, introduza valores para **O Diretório,** **subscrição Azure**e **Região.** Se não tiver uma subscrição, pode ativar o **teste gratuito de 7** dias e completar as informações de contacto necessárias.

    Para obter mais informações sobre diretórios e subscrições, consulte o [Create a application](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) quickstart.

1. Selecione o botão **Criar** na parte inferior esquerda da página.

    ![A página de novas candidaturas do Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![A informação de faturação de novas aplicações azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Criou agora uma aplicação de monitorização da qualidade da água utilizando o modelo de **monitorização** da qualidade da água central Azure IoT.

A sua nova aplicação vem com estes componentes pré-configurados:

* Dashboards do operador
* Modelos de dispositivos de monitorização da qualidade da água
* Dispositivos de monitorização simulados da qualidade da água
* Regras e empregos
* Marca que usa rotulagem branca

Pode modificar a sua aplicação a qualquer momento.

Em seguida, explore a aplicação e faça algumas personalizações.

## <a name="explore-and-customize-the-operator-dashboard"></a>Explore e personalize o painel de instrumentos do operador

Depois de criar a aplicação, abre-se o painel de instrumentos de **qualidade da água do Mundo Largo.**

   ![O painel de monitorização da qualidade da água](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Como construtor, pode criar e personalizar vistas no painel de instrumentos para utilização pelos operadores. Mas antes de tentar personalizar, primeiro explore o painel de instrumentos.

Todos os dados mostrados no painel de instrumentos baseiam-se em dados simulados do dispositivo, que são discutidos na secção seguinte.

O painel inclui os seguintes tipos de azulejos:

* **Azulejo**de imagem de utilidade de água do Mundo Inteiro : O primeiro azulejo no canto superior esquerdo do tablier é uma imagem que mostra a utilidade fictícia chamada Wide World. Pode personalizar o azulejo para usar a sua própria imagem, ou pode remover o azulejo.

* **Azulejos kpi médios**: Azulejos KPI como **o pH médio nos últimos 30 minutos** estão no topo do painel do painel de tablier. Pode personalizar os azulejos KPI e definir cada um para um tipo e intervalo de tempo diferentes.

* Mapa da área de **monitorização da água**: Azure IoT Central utiliza o Azure Maps, que pode definir diretamente na sua aplicação para mostrar a localização do dispositivo. Também pode mapear informações de localização da sua aplicação para o seu dispositivo e, em seguida, utilizar o Azure Maps para mostrar a informação num mapa. Passe sobre o mapa e tente os controlos.

* Gráfico médio de mapa de calor de distribuição de **pH:** Pode selecionar diferentes gráficos de visualização para mostrar a telemetria do dispositivo da forma mais adequada para a sua aplicação.

* Gráfico de linha de indicadores de **qualidade crítica:** Pode visualizar a telemetria do dispositivo traçada como um gráfico de linha ao longo de um intervalo de tempo.  

* **Concentração de barras de agentes químicos:** Pode visualizar a telemetria do dispositivo num gráfico de barras.

* **Botão de ação**: O painel inclui um azulejo para ações que um operador pode iniciar diretamente a partir do painel de monitorização. Repor as propriedades de um dispositivo é um exemplo de tais ações.

* **Azulejos da lista**de propriedades : O painel tem vários azulejos de propriedade que representam informações de limiar, informações sobre saúde do dispositivo e informações de manutenção.

### <a name="customize-the-dashboard"></a>Personalizar o dashboard

Como construtor, pode personalizar vistas no painel de instrumentos para utilização pelos operadores.

1. Selecione **Editar** para personalizar o painel de instrumentos de qualidade da **água do Mundo Inteiro.** Pode personalizar o painel de instrumentos selecionando comandos no menu **Editar.** Depois de o painel de instrumentos estar em modo de edição, pode adicionar novos azulejos ou configurar os ficheiros existentes.

    ![Editar o seu painel de instrumentos](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Selecione **+ Novo** para criar um novo dashboard que possa configurar. Pode ter vários dashboards e navegar entre eles a partir do menu do painel de instrumentos.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Explore um modelo de dispositivo de monitorização da qualidade da água

Um modelo de dispositivo em Azure IoT Central define as capacidades de um dispositivo. As capacidades disponíveis são telemetria, propriedades e comandos. Como construtor, pode definir modelos de dispositivos em Azure IoT Central que representam as capacidades dos dispositivos conectados. Também pode criar dispositivos simulados para testar o modelo e aplicação do seu dispositivo.

A aplicação de monitorização da qualidade da água que criou vem com um modelo de dispositivo de monitorização da qualidade da água.

Para ver o modelo do dispositivo:

1. Selecione **os modelos** do dispositivo no painel mais à esquerda da sua aplicação no Azure IoT Central.
1. A partir da lista de modelos do dispositivo, selecione **Water Quality Monitor**. O modelo do dispositivo abre.o modelo.

    ![O modelo do dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Personalize o modelo do dispositivo

Pratique personalizar as seguintes definições do modelo do dispositivo:

1. A partir do menu do modelo do dispositivo, selecione **Personalizar**.
1. Vá ao tipo de telemetria **de temperatura.**
1. Altere o valor do **nome display** para a **temperatura reportada**.
1. Altere a unidade de medição ou desempor **o valor de Min** e **o valor máximo**.
1. Selecione **Guardar**.

#### <a name="add-a-cloud-property"></a>Adicione uma propriedade em nuvem

1. A partir do menu de modelo do dispositivo, selecione **a propriedade Cloud**.
1. Para adicionar uma nova propriedade em nuvem, selecione **+ Adicione a Propriedade cloud**. No Azure IoT Central, pode adicionar uma propriedade que seja relevante para um dispositivo, mas não espera que seja enviada pelo dispositivo. Um exemplo de tal propriedade é um limiar de alerta específico para a área de instalação, informação de ativos ou informações de manutenção.
1. Selecione **Guardar**.

### <a name="explore-views"></a>Explore vistas

O modelo do dispositivo de monitorização da qualidade da água vem com vistas predefinidas. As vistas definem como os operadores vêem os dados do dispositivo e definem propriedades na nuvem. Explore as vistas e a prática fazendo alterações.

  ![Vistas do modelo do dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publique o modelo do dispositivo

Se fizer alterações, certifique-se de selecionar **Publicar** para publicar o modelo do dispositivo.

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo

1. Selecione **+ Novo** para criar um novo modelo de dispositivo e siga o processo de criação.
1. Crie um modelo de dispositivo personalizado ou escolha um modelo de dispositivo do catálogo do dispositivo Azure IoT.

## <a name="explore-simulated-devices"></a>Explore dispositivos simulados

A aplicação de monitorização da qualidade da água que criou a partir do modelo de aplicação tem dois dispositivos simulados. Estes dispositivos mapeiam para o modelo do dispositivo de monitorização da qualidade da água.

### <a name="view-the-devices"></a>Ver os dispositivos

1. Selecione **Dispositivos** no painel mais à esquerda da sua aplicação.

   ![Dispositivos](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Selecione um dispositivo simulado.

    ![Selecione dispositivo 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. No separador **Cloud Properties,** altere o valor limiar da **Acidity (pH)** de **8** para **9**.
1. Explore o separador Propriedades do **Dispositivo** e o **separador Do Painel** de Instrumentos do Dispositivo.

> [!NOTE]
> Todos os separadores foram configurados a partir de vistas do modelo do **dispositivo**.

### <a name="add-new-devices"></a>Adicione novos dispositivos

No separador **Dispositivos,** selecione **+ Novo** para adicionar um novo dispositivo.

## <a name="explore-and-configure-rules"></a>Explore e configure regras

No Azure IoT Central, pode criar regras que monitorizem automaticamente a telemetria do dispositivo. Estas regras desencadeiam uma ação quando qualquer uma das suas condições são cumpridas. Uma das medidas possíveis é enviar notificações por e-mail. Outras possibilidades incluem uma ação do Microsoft Flow ou uma ação webhook para enviar dados para outros serviços.

A aplicação de monitorização da qualidade da água que criou tem duas regras pré-configuradas.

### <a name="view-rules"></a>Ver regras

1. Selecione **Regras** no painel mais à esquerda da sua aplicação.

   ![Regras](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Selecione **alerta de pH alto,** que é uma das regras pré-configuradas na aplicação.

   ![A regra de alerta de pH elevado](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   A regra de **alerta de pH elevado** está configurada para verificar se o estado de acidez (pH) é superior a 8.

Em seguida, adicione uma ação de e-mail à regra:

1. Selecione **+ E-mail**.
1. Na caixa de **nomes Display,** **introduza alerta de pH elevado**.
1. Na caixa **To,** insira o endereço de e-mail associado à sua conta Central Azure IoT.
1. Opcionalmente, insira uma nota para incluir no texto do e-mail.
1. Selecione **Feito** para completar a ação.
1. Selecione **Guardar** para guardar e ativar a nova regra.

Dentro de poucos minutos, deve receber e-mail quando a condição configurada for satisfeita.

> [!NOTE]
> O pedido envia e-mail sempre que uma condição é satisfeita. Selecione **Desativar** para uma regra para deixar de receber e-mail automatizado a partir dessa regra.
  
Para criar uma nova regra, selecione **Regras** no painel mais à esquerda da sua aplicação e, em seguida, selecione **+New**.

## <a name="configure-jobs"></a>Configurar empregos

Com os trabalhos da Central Azure IoT, pode desencadear atualizações para dispositivos ou propriedades na nuvem em vários dispositivos. Também pode utilizar trabalhos para acionar comandos de dispositivos em vários dispositivos. A Azure IoT Central automatiza o fluxo de trabalho para si.

1. Selecione **Jobs** no painel mais à esquerda da sua aplicação.
1. Selecione **+Novo** e configure um ou mais empregos.

## <a name="customize-your-application"></a>Personalize a sua aplicação

Como construtor, pode alterar várias definições para personalizar a experiência do utilizador na sua aplicação.

1. Selecione **Administração** > **Personalize a sua aplicação.**
1. No **logótipo da Aplicação,** selecione **Alterar** para escolher a imagem para carregar como logotipo.
1. No **ícone do Browser,** selecione **Alterar** para escolher a imagem que aparece nos separadores do navegador.
1. Sob as cores do **Browser,** pode substituir os valores predefinidos por códigos de cores hexadecimais HTML.
1. Selecione **Definições** para alterar o valor do **Tema**.

   ![Personalize a sua aplicação](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Atualizar a imagem da aplicação

1. Selecione**as definições**de aplicação **de administração** > .

1. Utilize o botão **de imagem Select** para escolher uma imagem para carregar como imagem de aplicação.

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar a sua aplicação, elimine a aplicação com os seguintes passos:

1. Abra o separador **Administração** no painel mais à esquerda da sua aplicação.
1. Selecione **as definições de Aplicação** e selecione o botão **Eliminar.**

    ![Eliminar a sua aplicação](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre conceitos de [monitorização da qualidade da água.](./concepts-waterqualitymonitoring-architecture.md)

---
title: 'Tutorial: Criar uma app de monitorização da qualidade da água com a Azure IoT Central'
description: 'Tutorial: Saiba como criar uma aplicação de monitorização da qualidade da água utilizando modelos de aplicação Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 3e55970b0603da9be9bf28a50d4c474e34a1cad3
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017244"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Tutorial: Criar uma aplicação de monitorização da qualidade da água no Azure IoT Central

Este tutorial guia-o através da criação de uma aplicação de monitorização da qualidade da água no Azure IoT Central. Você cria a aplicação a partir do modelo de aplicação **de monitorização** da qualidade da água central Azure IoT.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Utilize o modelo **de monitorização** da qualidade da água para criar uma aplicação de monitorização da qualidade da água.
> * Explore e personalize um painel de instrumentos do operador.
> * Explore um modelo de dispositivo de monitorização da qualidade da água.
> * Explore dispositivos simulados.
> * Explore e configuure regras.
> * Criar trabalhos.
> * Personalize a marca de aplicações utilizando a rotulagem branca.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que tenha uma assinatura Azure para completar este tutorial. Se não tiver uma subscrição do Azure, pode criar uma na página de inscrição do [Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Criar uma aplicação de monitorização da qualidade da água no Azure IoT Central

Nesta secção, utilize o modelo de **monitorização** da qualidade da água central Azure IoT para criar uma aplicação de monitorização da qualidade da água.

1. Aceda à página inicial do [Azure IoT Central](https://aka.ms/iotcentral).

    Se tiver uma assinatura Azure, inscreva-se com as credenciais que utiliza para aceder à sua base. Caso contrário, inscreva-se com uma conta Microsoft:

    ![Inscreva-se na sua conta de organização](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. **Selecione Construir** no painel mais à esquerda da Azure IoT Central e selecione o separador **Governo.** O painel do governo mostra vários modelos de candidatura do governo.

    ![Modelos de aplicação do governo](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Selecione o modelo de aplicação **de monitorização da qualidade da água.** Este modelo de aplicação inclui um modelo de dispositivo de qualidade de água, dispositivos simulados, um painel de instrumentos do operador e regras de monitorização pré-configuradas.

1. Selecione **Criar aplicação**. O painel de **aplicação Novo** abre e mostra os seguintes elementos:

    * **Nome da aplicação**: Por padrão, o nome da aplicação é **monitorização da qualidade da água** seguida de uma cadeia de ID única que a Azure IoT Central gera. Se quiser, pode introduzir um nome de exibição ou alterar o nome da aplicação mais tarde.
    * **URL**: Pode introduzir qualquer URL que desejar ou alterar o valor URL mais tarde.
    * Se tiver uma subscrição Azure, insira valores para **Diretório,** **subscrição Azure** e **Região**. Se não tiver uma subscrição, pode ligar o **teste gratuito de 7 dias** e completar as informações de contacto necessárias.

    Para obter mais informações sobre diretórios e subscrições, consulte o Criar um quickstart de [aplicação.](../core/quick-deploy-iot-central.md)

1. Selecione o botão **Criar** na parte inferior esquerda da página.

    ![A página de nova aplicação Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![A informação de faturação de nova aplicação Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Você agora criou uma aplicação de monitorização da qualidade da água usando o modelo de **monitorização** da qualidade da água central Azure IoT.

A sua nova aplicação vem com estes componentes pré-configurados:

* Dashboards do operador
* Modelos de dispositivos de monitorização da qualidade da água
* Dispositivos de monitorização simulado da qualidade da água
* Regras e empregos
* Marca que usa rotulagem branca

Pode modificar a sua aplicação a qualquer momento.

Em seguida, explore a aplicação e faça algumas personalizações.

## <a name="explore-and-customize-the-operator-dashboard"></a>Explore e personalize o painel de instrumentos do operador

Depois de criar a aplicação, abre-se o painel **de instrumentos de qualidade da água Wide World.**

   ![O painel de monitorização da qualidade da água](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Como construtor, pode criar e personalizar vistas no painel de instrumentos para utilização por parte dos operadores. Mas antes de tentar personalizar, primeiro explore o painel de instrumentos.

Todos os dados apresentados no painel de instrumentos baseiam-se em dados simulados do dispositivo, que são discutidos na secção seguinte.

O painel inclui os seguintes tipos de azulejos:

* **Azulejo** de imagem de utilidade de água em Mundo Largo : O primeiro azulejo no canto superior esquerdo do tablier é uma imagem que mostra o utilitário fictício chamado Wide World. Pode personalizar o azulejo para usar a sua própria imagem, ou pode remover o azulejo.

* **Azulejos PH KPI médios**: Azulejos KPI como **o pH médio nos últimos 30 minutos** estão na parte superior do painel de instrumentos. Pode personalizar azulejos KPI e definir cada um para um tipo e intervalo de tempo diferentes.

* **Mapa da área de monitorização da água**: Azure IoT Central utiliza mapas Azure, que pode definir diretamente na sua aplicação para mostrar a localização do dispositivo. Também pode mapear informações de localização da sua aplicação para o seu dispositivo e, em seguida, usar o Azure Maps para mostrar as informações num mapa. Passe por cima do mapa e tente os controlos.

* **Gráfico médio de mapa de calor de distribuição de pH**: Pode selecionar diferentes gráficos de visualização para mostrar telemetria do dispositivo da forma mais adequada para a sua aplicação.

* **Gráfico de linha de indicadores de qualidade críticos**: Pode visualizar a telemetria do dispositivo traçada como um gráfico de linha ao longo de um intervalo de tempo.  

* **Concentração de gráfico de barras de agentes químicos**: Pode visualizar a telemetria do dispositivo num gráfico de barras.

* **Botão de ação**: O painel de instrumentos inclui um azulejo para as ações que um operador pode iniciar diretamente a partir do painel de monitorização. A reposição das propriedades de um dispositivo é um exemplo de tais ações.

* **Azulejos da lista de propriedades**: O painel de instrumentos tem múltiplas telhas de propriedade que representam informações de limiar, informações de saúde do dispositivo e informações de manutenção.

### <a name="customize-the-dashboard"></a>Personalizar o dashboard

Como construtor, pode personalizar vistas no painel de instrumentos para utilização por parte dos operadores.

1. **Selecione Editar** para personalizar o painel de instrumentos de **qualidade da água wide world.** Pode personalizar o painel de instrumentos selecionando comandos no menu **Editar.** Depois de o painel de instrumentos estar em modo de edição, pode adicionar novos azulejos ou configurar os ficheiros existentes.

    ![Edite o seu painel de instrumentos](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. **Selecione + Novo** para criar um novo painel de instrumentos que pode configurar. Pode ter vários dashboards e pode navegar entre eles a partir do menu do painel de instrumentos.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Explore um modelo de dispositivo de monitorização da qualidade da água

Um modelo de dispositivo no Azure IoT Central define as capacidades de um dispositivo. As capacidades disponíveis são telemetria, propriedades e comandos. Como construtor, pode definir modelos de dispositivos no Azure IoT Central que representam as capacidades dos dispositivos conectados. Também pode criar dispositivos simulados para testar o modelo e aplicação do seu dispositivo.

A aplicação de monitorização da qualidade da água que criou vem com um modelo de dispositivo de monitorização da qualidade da água.

Para ver o modelo do dispositivo:

1. Selecione **os modelos** do dispositivo no painel mais à esquerda da sua aplicação no Azure IoT Central.
1. A partir da lista de modelos do dispositivo, selecione **Water Quality Monitor**. O modelo do dispositivo abre-se.

    ![O modelo do dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Personalize o modelo do dispositivo

Pratique a personalização das seguintes definições do modelo do dispositivo:

1. A partir do menu do modelo do dispositivo, **selecione Personalizar**.
1. Vá para o tipo de telemetria **temperatura.**
1. Altere o valor **do nome do visor** para **a temperatura reportada**.
1. Altere a unidade de medição, ou desa estale **o valor Min** e o valor **Máximo**.
1. Selecione **Guardar**.

#### <a name="add-a-cloud-property"></a>Adicione uma propriedade em nuvem

1. A partir do menu do modelo do dispositivo, selecione **Propriedade Cloud**.
1. Para adicionar uma nova propriedade em nuvem, selecione **+ Adicionar Propriedade Cloud**. No Azure IoT Central, pode adicionar um imóvel que seja relevante para um dispositivo mas que não seja esperado que seja enviado pelo dispositivo. Um exemplo de tal propriedade é um limiar de alerta específico para a área de instalação, informações de bens ou informações de manutenção.
1. Selecione **Guardar**.

### <a name="explore-views"></a>Explorar vistas

O modelo do dispositivo de monitorização da qualidade da água vem com vistas predefinidas. As vistas definem como os operadores vêem os dados do dispositivo e definem propriedades na nuvem. Explore as vistas e pratique alterações.

  ![Vistas do modelo do dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Se escoar alguma alteração, certifique-se de selecionar **Publicar** para publicar o modelo do dispositivo.

### <a name="create-a-new-device-template"></a>Criar um novo modelo do dispositivo

1. Selecione **+ Novo** para criar um novo modelo de dispositivo e siga o processo de criação.
1. Crie um modelo de dispositivo personalizado ou escolha um modelo de dispositivo a partir do catálogo de dispositivos Azure IoT.

## <a name="explore-simulated-devices"></a>Explore dispositivos simulados

A aplicação de monitorização da qualidade da água que criou a partir do modelo de aplicação tem dois dispositivos simulados. Estes dispositivos mapeiam para o modelo do dispositivo de monitorização da qualidade da água.

### <a name="view-the-devices"></a>Ver os dispositivos

1. Selecione **Dispositivos** no painel mais à esquerda da sua aplicação.

   ![Dispositivos](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Selecione um dispositivo simulado.

    ![Selecione dispositivo 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. No separador **Cloud Properties,** altere o valor do **limiar acidibilidade (pH)** de **8** para **9**.
1. Explore o separador Propriedades do **Dispositivo** e o **separador dashboard do dispositivo.**

> [!NOTE]
> Todos os separadores foram configurados a partir das vistas do modelo do **dispositivo.**

### <a name="add-new-devices"></a>Adicionar novos dispositivos

No **separador Dispositivos,** selecione **+ Novo** para adicionar um novo dispositivo.

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, pode criar regras que monitorizem automaticamente a telemetria do dispositivo. Estas regras desencadeiam uma ação quando qualquer uma das suas condições está reunida. Uma das medidas possíveis é enviar notificações por e-mail. Outras possibilidades incluem uma ação Power Automamate ou uma ação webhook para enviar dados para outros serviços.

A aplicação de monitorização da qualidade da água que criou tem duas regras pré-configuradas.

### <a name="view-rules"></a>Ver regras

1. Selecione **Regras** no painel mais à esquerda da sua aplicação.

   ![Regras](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Selecione **alerta de pH elevado,** que é uma das regras pré-configuradas na aplicação.

   ![A regra de alerta de pH alto](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   A regra **de alerta de pH elevado** está configurada para verificar se o estado de acidez (pH) é superior a 8.

Em seguida, adicione uma ação de e-mail à regra:

1. Selecione **+ E-mail**.
1. Na caixa de **nome do Visor,** insira **o alerta de pH alto**.
1. Na caixa **'Para'** inserir o endereço de e-mail associado à sua conta Azure IoT Central.
1. Opcionalmente, insira uma nota para incluir no texto do e-mail.
1. Selecione **Feito** para completar a ação.
1. **Selecione Guardar** para guardar e ativar a nova regra.

Dentro de poucos minutos, deverá receber e-mail quando a condição configurada for satisfeita.

> [!NOTE]
> A aplicação envia e-mail cada vez que uma condição é satisfeita. **Selecione Desativar** uma regra para deixar de receber e-mails automatizados dessa regra.
  
Para criar uma nova regra, selecione **Regras** no painel mais à esquerda da sua aplicação e, em seguida, selecione **+New**.

## <a name="configure-jobs"></a>Configurar tarefas

Com os trabalhos da Azure IoT Central, pode desencadear atualizações para propriedades de dispositivos ou nuvem em vários dispositivos. Também pode utilizar trabalhos para acionar comandos de dispositivos em vários dispositivos. A Azure IoT Central automatiza o fluxo de trabalho para si.

1. Selecione **Jobs** no painel mais à esquerda da sua aplicação.
1. Selecione **+Novos** e configuure um ou mais empregos.

## <a name="customize-your-application"></a>Personalize a sua aplicação

Como construtor, pode alterar várias definições para personalizar a experiência do utilizador na sua aplicação.

1. **Selecione Administração**  >  **Personalize a sua aplicação.**
1. No **logotipo da Aplicação,** selecione **Alterar** para escolher a imagem para carregar como o logótipo.
1. No **ícone do Navegador,** selecione **Alterar** para escolher a imagem que aparece nos separadores do navegador.
1. Sob **as cores do Navegador,** pode substituir os valores predefinidos por códigos de cores hexadémicas HTML.
1. Selecione **Definições** para alterar o valor do **tema**.

   ![Personalize a sua aplicação](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Atualizar a imagem da aplicação

1. Selecione   >  **as definições de Aplicação de** Administração .

1. Utilize o botão **de imagem Select** para escolher uma imagem para carregar como imagem de aplicação.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar a sua aplicação, elimine a aplicação com os seguintes passos:

1. Abra o separador **Administração** no painel mais à esquerda da sua candidatura.
1. Selecione **as definições de Aplicação** e selecione o botão **Eliminar.**

    ![Elimine a sua aplicação](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Passos seguintes

* Mais informações sobre 

> [!div class="nextstepaction"]
> [conceitos de monitorização da qualidade da água.](./concepts-waterqualitymonitoring-architecture.md)

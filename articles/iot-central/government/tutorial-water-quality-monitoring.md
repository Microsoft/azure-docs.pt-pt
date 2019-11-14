---
title: Criar um aplicativo de monitoramento de qualidade de água no Azure IoT Central
description: Saiba como criar um aplicativo de monitoramento de qualidade de água usando os modelos de aplicativo IoT Central do Azure.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: bfa5234d17db32c459b5ff1d0252a65a5ba99a72
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039518"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Tutorial: criar um aplicativo de monitoramento de qualidade de água no Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial orienta você pela criação de um aplicativo de monitoramento de qualidade de água no Azure IoT Central. Você cria o aplicativo no modelo de aplicativo de **monitoramento de qualidade de água** IOT central do Azure.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Use o modelo de **monitoramento de qualidade de água** para criar um aplicativo de monitoramento de qualidade de água.
> * Explore e personalize um painel do operador.
> * Explore um modelo de dispositivo de monitoramento de qualidade de água.
> * Explore os dispositivos simulados.
> * Explore e configure regras.
> * Configurar trabalhos.
> * Personalize a identidade visual do aplicativo usando o rótulo branco.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você tenha uma assinatura do Azure para concluir este tutorial. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de inscrição do Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Criar um aplicativo de monitoramento de qualidade de água no Azure IoT Central

Nesta seção, você usa o modelo de **monitoramento de qualidade de água** do Azure IOT central para criar um aplicativo de monitoramento de qualidade de água.

1. Vá para o [Home Page de IOT central do Azure](https://aka.ms/iotcentral).

    Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-la. Caso contrário, entre com uma conta Microsoft:

    ![Entre na sua conta da organização](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Selecione **criar** no painel da extrema esquerda da IOT central do Azure e selecione a guia **governo** . O painel governamental mostra vários modelos de aplicativos do governo.

    ![Modelos de aplicativos do governo](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Selecione o modelo de aplicativo de **monitoramento de qualidade de água** . Este modelo de aplicativo inclui um modelo de dispositivo de qualidade de água, dispositivos simulados, um painel de operador e regras de monitoramento pré-configuradas.

1. Selecione **criar aplicativo**. O **novo** painel do aplicativo é aberto e mostra os seguintes elementos:

    * **Nome do aplicativo**: por padrão, o nome do aplicativo é o **monitoramento de qualidade de água** seguido por uma cadeia de caracteres de ID exclusiva que o Azure IOT central gera. Se desejar, você pode inserir um nome de exibição ou alterar o nome do aplicativo mais tarde.
    * **URL**: você pode inserir qualquer URL que desejar ou alterar o valor da URL mais tarde.
    * Se você tiver uma assinatura do Azure, insira valores para **diretório**, **assinatura do Azure**e **região**. Se você não tiver uma assinatura, poderá ativar a **avaliação gratuita de 7 dias** e concluir as informações de contato necessárias.

    Para obter mais informações sobre diretórios e assinaturas, consulte o guia de início rápido [criar um aplicativo](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

1. Selecione o botão **criar** na parte inferior esquerda da página.

    ![A página novo aplicativo do IoT Central do Azure](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

Agora você criou um aplicativo de monitoramento de qualidade de água usando o modelo de **monitoramento de qualidade de água** do Azure IOT central.

Seu novo aplicativo vem com esses componentes pré-configurados:

* Painéis do operador
* Modelos de dispositivo de monitoramento de qualidade aquático
* Dispositivos de monitoramento de qualidade de água simulados
* Regras e trabalhos
* Identidade visual que usa rotulagem branca

Você pode modificar seu aplicativo a qualquer momento.

Em seguida, explore o aplicativo e faça algumas personalizações.

## <a name="explore-and-customize-the-operator-dashboard"></a>Explorar e personalizar o painel do operador

Depois de criar o aplicativo, o painel do **painel de qualidade de água da Wide World** é aberto.

   ![O painel de monitoramento de qualidade da água](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Como um construtor, você pode criar e personalizar modos de exibição no painel para uso por operadores. Mas antes de tentar personalizar o, primeiro Explore o painel.

Todos os dados mostrados no painel são baseados em dados de dispositivo simulados, que são discutidos na próxima seção.

O painel inclui os seguintes tipos de blocos:

* **Bloco de imagem do utilitário de água Wide World**: o primeiro bloco no canto superior esquerdo do painel é uma imagem que mostra o utilitário fictício chamado Wide World. Você pode personalizar o bloco para usar sua própria imagem ou pode remover o bloco.

* **Média de blocos de KPI pH**: blocos de KPI como **média pH nos últimos 30 minutos** estão na parte superior do painel do painel. Você pode personalizar blocos de KPI e definir cada um com um tipo e intervalo de tempo diferentes.

* **Mapa da área de monitoramento da água**: o Azure IOT central usa mapas do Azure, que você pode definir diretamente em seu aplicativo para mostrar a localização do dispositivo. Você também pode mapear informações de local de seu aplicativo para seu dispositivo e, em seguida, usar o Azure Maps para mostrar as informações em um mapa. Focalize o mapa e experimente os controles.

* **Gráfico de mapa de calor médio de distribuição pH**: você pode selecionar gráficos de visualização diferentes para mostrar a telemetria do dispositivo da maneira mais apropriada para seu aplicativo.

* **Gráfico de linhas dos indicadores de qualidade crítica**: você pode visualizar a telemetria do dispositivo plotada como um gráfico de linhas em um intervalo de tempo.  

* **Gráfico de barras de concentração de agentes químicos**: você pode visualizar a telemetria de dispositivo em um gráfico de barras.

* **Botão de ação**: o painel inclui um bloco para ações que um operador pode iniciar diretamente do painel de monitoramento. Redefinir as propriedades de um dispositivo é um exemplo dessas ações.

* **Blocos de lista de propriedades**: o painel tem vários blocos de propriedade que representam informações de limite, informações de integridade do dispositivo e informações de manutenção.

### <a name="customize-the-dashboard"></a>Personalizar o painel

Como um construtor, você pode personalizar modos de exibição no painel para uso por operadores.

1. Selecione **Editar** para personalizar o painel do **painel de qualidade de água Wide World** . Você pode personalizar o painel selecionando comandos no menu **Editar** . Depois que o painel estiver no modo de edição, você poderá adicionar novos blocos ou pode configurar os arquivos existentes.

    ![Editar seu painel](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Selecione **+ novo** para criar um novo painel que você pode configurar. Você pode ter vários painéis e pode navegar entre eles no menu painel.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Explorar um modelo de dispositivo de monitoramento de qualidade de água

Um modelo de dispositivo no Azure IoT Central define os recursos de um dispositivo. Os recursos disponíveis são telemetria, propriedades e comandos. Como um construtor, você pode definir modelos de dispositivo no Azure IoT Central que representam os recursos dos dispositivos conectados. Você também pode criar dispositivos simulados para testar seu aplicativo e modelo de dispositivo.

O aplicativo de monitoramento de qualidade de água criado vem com um modelo de dispositivo de monitoramento de qualidade de água.

Para exibir o modelo de dispositivo:

1. Selecione **modelos de dispositivo** no painel mais à esquerda do seu aplicativo no Azure IOT central.
1. Na lista de modelos de dispositivo, selecione **Monitor de qualidade de água**. Esse modelo de dispositivo é aberto.

    ![O modelo de dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Personalizar o modelo de dispositivo

Pratique Personalizando as seguintes configurações de modelo de dispositivo:

1. No menu modelo de dispositivo, selecione **Personalizar**.
1. Vá para o tipo de telemetria de **temperatura** .
1. Altere o valor do **nome de exibição** para **temperatura relatada**.
1. Altere a unidade de medida ou defina valor **mínimo** e **valor máximo**.
1. Selecione **Guardar**.

#### <a name="add-a-cloud-property"></a>Adicionar uma propriedade de nuvem

1. No menu modelo de dispositivo, selecione **propriedade de nuvem**.
1. Para adicionar uma nova propriedade de nuvem, selecione **+ Adicionar Propriedade de nuvem**. No Azure IoT Central, você pode adicionar uma propriedade que é relevante para um dispositivo, mas que não deveria ser enviada pelo dispositivo. Um exemplo de tal propriedade é um limite de alerta específico para a área de instalação, informações de ativo ou informações de manutenção.
1. Selecione **Guardar**.

### <a name="explore-views"></a>Explorar exibições

O modelo de dispositivo de monitoramento de qualidade de água vem com exibições predefinidas. As exibições definem como os operadores veem os dados do dispositivo e definem as propriedades da nuvem. Explore as exibições e pratique a realização de alterações.

  ![Exibições de modelo de dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Se você fizer alterações, certifique-se de selecionar **publicar** para publicar o modelo de dispositivo.

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo

1. Selecione **+ novo** para criar um novo modelo de dispositivo e siga o processo de criação.
1. Crie um modelo de dispositivo personalizado ou escolha um modelo de dispositivo no catálogo de dispositivos do Azure IoT.

## <a name="explore-simulated-devices"></a>Explorar dispositivos simulados

O aplicativo de monitoramento de qualidade de água criado no modelo de aplicativo tem dois dispositivos simulados. Esses dispositivos são mapeados para o modelo de dispositivo de monitoramento de qualidade de água.

### <a name="view-the-devices"></a>Exibir os dispositivos

1. Selecione **dispositivos** no painel mais à esquerda do seu aplicativo.

   ![Dispositivos](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Selecione um dispositivo simulado.

    ![Selecionar o dispositivo 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Na guia **Propriedades da nuvem** , altere o valor do **limite de Acid (pH)** de **8** para **9**.
1. Explore a guia **Propriedades do dispositivo** e a guia painel do **dispositivo** .

> [!NOTE]
> Todas as guias foram configuradas em **exibições de modelo de dispositivo**.

### <a name="add-new-devices"></a>Adicionar novos dispositivos

Na guia **dispositivos** , selecione **+ novo** para adicionar um novo dispositivo.

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, você pode criar regras que monitoram automaticamente a telemetria do dispositivo. Essas regras disparam uma ação quando qualquer uma de suas condições são atendidas. Uma ação possível é enviar notificações por email. Outras possibilidades incluem uma ação Microsoft Flow ou uma ação de webhook para enviar dados a outros serviços.

O aplicativo de monitoramento de qualidade de água criado tem duas regras pré-configuradas.

### <a name="view-rules"></a>Exibir regras

1. Selecione **regras** no painel mais à esquerda do seu aplicativo.

   ![Regras](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Selecione o **alerta pH alto**, que é uma das regras pré-configuradas no aplicativo.

   ![A regra de alerta pH alto](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   A regra de **alerta pH alto** está configurada para verificar se a pH (condição de Acid) é maior que 8.

Em seguida, adicione uma ação de email à regra:

1. Selecione **+ email**.
1. Na caixa **nome de exibição** , digite o **alerta pH alto**.
1. Na caixa **para** , insira o endereço de email associado à sua conta de IOT central do Azure.
1. Opcionalmente, insira uma observação para incluir no texto do email.
1. Selecione **concluído** para concluir a ação.
1. Selecione **salvar** para salvar e ativar a nova regra.

Em alguns minutos, você deverá receber emails quando a condição configurada for atendida.

> [!NOTE]
> O aplicativo envia email cada vez que uma condição é atendida. Selecione **desabilitar** para que uma regra pare de receber emails automatizados dessa regra.
  
Para criar uma nova regra, selecione **regras** no painel da extrema esquerda do seu aplicativo e, em seguida, selecione **+ novo**.

## <a name="configure-jobs"></a>Configurar trabalhos

Com os trabalhos de IoT Central do Azure, você pode disparar atualizações para propriedades de dispositivo ou de nuvem em vários dispositivos. Você também pode usar trabalhos para disparar comandos de dispositivo em vários dispositivos. O Azure IoT Central automatiza o fluxo de trabalho para você.

1. Selecione **trabalhos** no painel da extrema esquerda do seu aplicativo.
1. Selecione **+ novo** e configure um ou mais trabalhos.

## <a name="customize-your-application"></a>Personalizar seu aplicativo

Como um construtor, você pode alterar várias configurações para personalizar a experiência do usuário em seu aplicativo.

1. Selecione **administração** > **personalizar seu aplicativo**.
1. Em **logotipo do aplicativo**, selecione **alterar** para escolher a imagem a ser carregada como o logotipo.
1. Em **ícone do navegador**, selecione **alterar** para escolher a imagem que aparece nas guias do navegador.
1. Em **cores do navegador**, você pode substituir os valores padrão por códigos de cor hexadecimais HTML.
1. Selecione **configurações** para alterar o valor do **tema**.

   ![Personalizar seu aplicativo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Atualizar a imagem do aplicativo

1. Selecione **administração** > **configurações do aplicativo**.

1. Use o botão **selecionar imagem** para escolher uma imagem a ser carregada como a imagem do aplicativo.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não for continuar a usar seu aplicativo, exclua o aplicativo com as seguintes etapas:

1. Abra a guia **Administração** no painel mais à esquerda do seu aplicativo.
1. Selecione **configurações do aplicativo** e selecione o botão **excluir** .

    ![Excluir seu aplicativo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [conceitos de monitoramento de qualidade da água](./concepts-waterqualitymonitoring-architecture.md).

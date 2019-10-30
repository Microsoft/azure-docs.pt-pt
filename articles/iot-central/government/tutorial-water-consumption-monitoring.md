---
title: Criar um aplicativo de monitoramento de consumo de água com o Azure IoT Central | Microsoft Docs
description: Aprenda a criar um aplicativo de monitoramento de consumo de água usando os modelos de aplicativo IoT Central do Azure.
author: miriambrus
ms.author: miriamb
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 59dae65d32f1b816e59ee883cb4529c6f1fd212c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026554"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Tutorial: criar um aplicativo de monitoramento de consumo de água no IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial orienta você a criar um aplicativo de monitoramento de consumo de água IoT Central do Azure do modelo de aplicativo de monitoramento de consumo de água IoT Central. 

O tutorial aprenderá a: 

> [!div class="checklist"]
> * Use o modelo de **monitoramento de consumo de água** do Azure IOT central para criar seu aplicativo de monitoramento de consumo de água
> * Explorar e personalizar o painel do operador 
> * Explorar modelo de dispositivo
> * Explorar dispositivos simulados
> * Explorar e configurar regras
> * Configurar trabalhos
> * Personalizar a identidade visual do aplicativo usando o autorizações

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará de:
-  Uma assinatura do Azure é recomendada. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de inscrição do Azure](https://aka.ms/createazuresubscription).

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Criar aplicativo de monitoramento de consumo de água no IoT Central

Nesta seção, usaremos o **modelo de monitoramento de consumo de água** do Azure IOT central para criar seu aplicativo de monitoramento de consumo de água no IOT central.

Para criar um novo aplicativo de monitoramento de consumo de água do Azure IoT Central:  

1. Navegue até o site [do Azure IOT central Home Page](https://aka.ms/iotcentral) .

    Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-la, caso contrário, entre usando uma conta Microsoft:

    ![Introduza a sua conta da organização](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Clique em **Compilar** no painel esquerdo e selecione a guia **governo** . A página do governo exibe vários modelos de aplicativos do governo.

   ![Criar modelos de aplicativo do governo](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selecione o modelo de aplicativo de **monitoramento de consumo de água** . Este modelo inclui o modelo de dispositivo de consumo de água de exemplo, dispositivo simulado, painel de operador e regras de monitoramento pré-configuradas.    

2. Clique em **criar aplicativo**, que abrirá o formulário de criação de **novo aplicativo** com os seguintes campos:
    * **Nome do aplicativo**: por padrão, o aplicativo usa o *monitoramento de consumo de água* seguido por uma cadeia de caracteres de ID exclusiva que IOT central gera. Opcionalmente, escolha um nome de aplicativo amigável. Você pode alterar o nome do aplicativo mais tarde também.
    * **URL**: IOT central gerará uma URL para você com base no nome do aplicativo. Você pode optar por atualizar a URL de sua preferência. Você também pode alterar a URL mais tarde. 
    * Se você tiver uma assinatura do Azure, insira seu *diretório, a assinatura do Azure e a região*. Se você não tiver uma assinatura, poderá habilitar a **avaliação gratuita de 7 dias** e concluir as informações de contato necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

5. Clique no botão **criar** na parte inferior da página. 

    ![Página da Aplicação do Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

6. Agora você criou um aplicativo de monitoramento de consumo de água usando o modelo de **monitoramento de consumo de água** do Azure IOT central.

Parabéns! Você terminou de criar seu aplicativo de monitoramento de qualidade de água, que vem com pré-configurado:
* Painéis de exemplo de operador
* Modelos de dispositivo de fluxo e válvula d' água predefinidos de exemplo
* Fluxo de água simulado e dispositivos de válvula inteligente
* Regras e trabalhos pré-configurados
* Identidade visual de amostra usando o rótulo branco 

É seu aplicativo e você pode modificá-lo a qualquer momento. Agora vamos explorar o aplicativo e fazer algumas personalizações.  

## <a name="explore-and-customize-operator-dashboard"></a>Explorar e personalizar o painel do operador 
Depois de criar o aplicativo, você é o painel do operador de exemplo chamado **painel de monitoramento de consumo de água Wide World**.

   ![Painel de monitoramento de consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Como um construtor, você pode criar e personalizar modos de exibição no painel para operadores. Antes de tentar personalizar o, vamos explorar o painel. 

> [!NOTE]
> Todos os dados exibidos no painel são baseados em dados de dispositivo simulados, que serão explorados na próxima seção. 
  
O painel consiste em diferentes tipos de blocos:

* **Bloco de imagem do utilitário de água Wide World**: o primeiro bloco no painel é um bloco de imagem de um utilitário de água fictícia "Wide World aquático". Você pode personalizar o bloco e colocar sua própria imagem ou removê-la. 

* **Bloco de KPI de fluxo de água médio**: o bloco de KPI é configurado para ser exibido como um exemplo *da média nos últimos 30 minutos*. Você pode personalizar os blocos de KPI e definir como um tipo e intervalo de tempo diferentes.

* Em seguida, ele tem direito nos blocos de *comando do dispositivo* de painel para **fechar a válvula**, abrir a **válvula**ou definir a **posição da válvula**. Ao clicar nos comandos, você será levado para a página de comando do dispositivo de dispositivo simulado. No IoT Central um *comando* é um tipo de *funcionalidade de dispositivo* que iremos explorar mais tarde na **seção modelo de dispositivo** deste tutorial.

*  **Mapa da área de distribuição da água**: o mapa está usando mapas do Azure, que podem ser configurados diretamente no Azure IOT central. O bloco mapa está exibindo a localização do dispositivo. Tente focalizar o mapa e experimentar os controles sobre o mapa, como *ampliar*, *reduzir* ou *expandir*. 

    ![Mapa do painel de monitoramento de consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* Gráfico de linhas **média do fluxo de água** e condições de **ambiente**: você pode visualizar um ou vários dispositivos telemetrias plotados como um gráfico de linhas em um intervalo de tempo desejado.  

* **Gráfico de calor de pressão de válvula média**: você pode escolher o tipo de visualização calor de dados de telemetria do dispositivo que você está interessado em examinar a distribuição em um intervalo de tempo com um índice de cores.

* **Redefinir o bloco de conteúdo de limite de alerta**: você pode incluir blocos de conteúdo de chamada de ação inserindo o link para uma página de ação. Nesse caso, redefinir o limite de alerta levará você para os **trabalhos** do aplicativo, onde você pode executar atualizações nas propriedades de dispositivos, que exploraremos posteriormente na seção **configurar trabalhos** deste tutorial.

* **Blocos de propriedade**: o painel **exibe informações operacionais de válvula**, limites de alerta de **fluxo**e informações de **manutenção** que são propriedades do dispositivo.  


### <a name="customize-dashboard"></a>Personalizar o painel 

Como um construtor, você pode personalizar modos de exibição no Dashboard para operadores. Você pode tentar:
1. Clique em **Editar** para personalizar o **painel de monitoramento de consumo de água Wide World**. Você pode personalizar o painel clicando no menu **Editar** . Depois que o painel estiver no modo de **edição** , você poderá adicionar novos blocos ou pode configurar 

     ![Editar painel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Clique em **+ novo** para criar um novo painel e configurar do zero. Você pode ter vários painéis e pode navegar entre seus painéis no menu painel. 

## <a name="explore-device-template"></a>Explorar modelo de dispositivo
Um modelo de dispositivo no Azure IoT Central define a capacidade de um dispositivo, que pode ser telemetria, propriedade ou comando. Como um construtor, você pode definir um ou mais modelos de dispositivo no IoT Central que representam a capacidade dos dispositivos que você conectará. 
 

O aplicativo de **monitoramento de consumo de água** vem com dois modelos de dispositivo de referência que representam um medidor de *fluxo* e um dispositivo de *válvula inteligente* . 

Para exibir o modelo de dispositivo:

1. Clique em **modelos de dispositivo** no painel de navegação à esquerda do seu aplicativo no IOT central. 
    Na lista modelos de dispositivo, você verá dois **medidores de fluxo** de modelos de dispositivo e **válvula inteligente**

   ![Modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Clique no modelo de dispositivo de **medidor de fluxo** e familiarize-se com os recursos do dispositivo 

     ![Medidor de fluxo de modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Personalizando o modelo de dispositivo

Tente personalizar o seguinte:
1. Navegue até **Personalizar** no menu modelo de dispositivo
2. Localizar o tipo de telemetria `Temperature`
3. Atualizar o **nome de exibição** de `Temperature` para `Reported temperature`
4. Atualizar unidade de medida ou definir valor *mínimo* e *valor máximo*
5. **Salvar** as alterações 

### <a name="add-a-cloud-property"></a>Adicionar uma propriedade de nuvem 
1. Navegue até a **propriedade de nuvem** no menu modelo de dispositivo
2. Adicione uma nova propriedade de nuvem clicando em **+ Adicionar Propriedade de nuvem**. 
    No IoT Central, você pode adicionar uma propriedade que é relevante para o dispositivo. Por exemplo, uma propriedade de nuvem pode ser um limite de alerta específico para área de instalação, informações de ativo ou informações de manutenção etc. 
3. **Salvar** as alterações 
 
### <a name="views"></a>Vistas 
O modelo de dispositivo monitor de consumo de água é fornecido com exibições predefinidas. Explore as exibições e você pode fazer atualizações. As exibições definem como os operadores verão os dados do dispositivo, mas também inserindo as propriedades da nuvem. 

  ![Exibições de modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicar 
Se você fez alterações, certifique-se de **publicar** o modelo de dispositivo. 

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo 
- Selecione **+ novo** para criar um novo modelo de dispositivo e siga o processo de criação. Você poderá criar um modelo de dispositivo personalizado do zero ou escolher um modelo de dispositivo no catálogo de dispositivos do Azure. 

## <a name="explore-simulated-devices"></a>Explorar dispositivos simulados
No IoT Central, você pode criar dispositivos simulados para testar seu aplicativo e modelo de dispositivo. O aplicativo de **monitoramento de consumo de água** tem dois dispositivos simulados mapeados para o medidor de *fluxo* e os modelos de dispositivo de *válvula inteligente* . 

### <a name="to-view-the-devices"></a>Para exibir os dispositivos:
1. Navegue até o **dispositivo** no painel de navegação IOT central esquerdo. 

   ![Dispositivos](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Clique em uma **válvula inteligente 1** 

    ![Dispositivo 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  Nos **comandos do dispositivo** , você pode ver a *válvula aberta*de três comandos de dispositivo, a *válvula de fechamento*e a posição da *válvula definida* que são recursos definidos no modelo de dispositivo de *válvula inteligente* . 
4. Explore a guia **Propriedades do dispositivo** e a guia painel do **dispositivo** . 

> [!NOTE]
> Observe que todas as guias foram configuradas nos modos de exibição de modelo de dispositivo.

### <a name="add-new-devices"></a>Adicionar novos dispositivos
* Adicione novos dispositivos clicando em **+ novo** na guia **dispositivos** . 

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central você pode criar regras para monitorar automaticamente a telemetria do dispositivo e disparar ações quando uma ou mais condições forem atendidas. As ações podem incluir o envio de notificações por email ou o acionamento de uma ação Microsoft Flow ou uma ação de webhook para enviar dados a outros serviços.

O aplicativo de **monitoramento de consumo de água** que você criou modelo tem três regras pré-configuradas.

### <a name="to-view-rules"></a>Para exibir regras:
1. Navegue até **regras** no painel de navegação IOT central esquerdo. 

   ![Regras](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Selecione e clique no **alerta pH alto** , que é uma das regras pré-configuradas no aplicativo.

     ![Alerta pH alto](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    A regra de `High flow alert` está configurada para verificar a condição `Acidity (pH)` é `greater than` `Max flow threshold`. Limite máximo de fluxo é uma propriedade de nuvem definida no modelo de dispositivo de *válvula inteligente* de dispositivo. O valor de `Max flow threshold` é definido por instância de dispositivo. 

Agora, vamos criar uma ação de email.

Para adicionar uma ação à regra:

1. Selecione **+ email**. 
1. Insira o *alerta pH alto* como o **nome de exibição** amigável para a ação.
    * Insira o endereço de email associado à sua conta de IoT Central no **para**. 
1. Opcionalmente, insira uma observação a ser incluída no texto do email.
1. Selecione **concluído** para concluir a ação.
1. Selecione **salvar** para salvar e ativar a nova regra. 

Em alguns minutos, você deverá receber emails quando a **condição** configurada for atendida.

> [!NOTE]
> O aplicativo enviará um email toda vez que uma condição for atendida. **Desabilite** a regra para parar de receber emails da regra automatizada. 
  
Para criar uma nova regra: 
- Selecione **+ novo** nas **regras** do painel de navegação esquerdo.

## <a name="configure-jobs"></a>Configurar trabalhos

No IoT Central, os trabalhos permitem disparar atualizações de propriedade de dispositivo ou de nuvem em vários dispositivos. Além das propriedades, você também pode usar trabalhos para disparar comandos de dispositivo em vários dispositivos. IoT Central automatizará o fluxo de trabalho para você. 

1. Vá para **trabalhos** no painel de navegação à esquerda. 
2. Clique em **+ novo** e configure um ou mais trabalhos. 


## <a name="customize-your-application"></a>Personalizar seu aplicativo 
Como um construtor, você pode alterar várias configurações para personalizar a experiência do usuário em seu aplicativo.

1.  Vá para **administração > personalizar seu aplicativo**.
3. Use o botão **alterar** para escolher uma imagem a ser carregada como o **logotipo do aplicativo**.
4. Use o botão **alterar** para escolher uma imagem de **ícone do navegador** que será exibida nas guias do navegador.
5. Você também pode substituir as cores padrão do **navegador** adicionando códigos de cores hexadecimais HTML.

   ![O Azure IoT Central personalizar seu aplicativo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Você também pode alterar as imagens do aplicativo acessando o botão **administração > configurações do aplicativo** e **selecionar imagem** para escolher uma imagem a ser carregada como a imagem do aplicativo. 
2. Por fim, você também pode alterar o **tema** clicando em **configurações** na manchete do aplicativo. 

  
## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, exclua seu aplicativo com as seguintes etapas:

1. Abra a guia Administração no painel esquerdo do seu aplicativo IoT Central. 
2. Selecione Configurações do aplicativo e clique no botão excluir na parte inferior da página. 


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [conceitos de monitoramento de consumo de água](./concepts-waterconsumptionmonitoring-architecture.md)

---
title: Criar um aplicativo de monitoramento de qualidade de água com o Azure IoT Central
description: Saiba como criar um aplicativo de monitoramento de qualidade de água usando os modelos de aplicativo IoT Central do Azure.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 51c84410de39516312d2058eeda575023dbe32ab
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890773"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-iot-central"></a>Tutorial: criar um aplicativo de monitoramento de qualidade de água no IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial orienta você a criar um aplicativo de monitoramento de qualidade de água IoT Central do Azure do modelo de aplicativo de monitoramento de qualidade de água IoT Central. 

Neste tutorial, você aprenderá a: 

> [!div class="checklist"]
> * Use o modelo de **monitoramento de qualidade de água** do Azure IOT central para criar seu aplicativo de monitoramento de qualidade de água
> * Explorar e personalizar o painel do operador 
> * Explorar o modelo de dispositivo do monitor de qualidade de água
> * Explorar dispositivos simulados
> * Explorar e configurar regras
> * Configurar trabalhos
> * Personalizar a identidade visual do aplicativo usando o autorizações


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará de:
-  Uma assinatura do Azure é recomendada. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de inscrição do Azure](https://aka.ms/createazuresubscription).


## <a name="create-water-quality-monitoring-app-in-iot-central"></a>Criar aplicativo de monitoramento de qualidade de água no IoT Central

Nesta seção, usaremos o **modelo de monitoramento de qualidade de água** do Azure IOT central para criar seu aplicativo de monitoramento de qualidade de água no IOT central.


Para criar um novo aplicativo de monitoramento de qualidade de água do Azure IoT Central:  

1. Navegue até o site [do Azure IOT central Home Page](https://aka.ms/iotcentral) .

      Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-la, caso contrário, entre usando uma conta Microsoft:

    ![Introduza a sua conta da organização](./media/tutorial-waterqualitymonitoring/sign-in.png)

2. Clique em **Compilar** no painel esquerdo e selecione a guia **governo** . A página do governo exibe vários modelos de aplicativos do governo.

    ![Criar modelos de aplicativo do governo](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)


1. Selecione o modelo de aplicativo de **monitoramento de qualidade de água** . Este modelo inclui modelo de dispositivo de qualidade de água de exemplo, dispositivo simulado, painel de operador e regras de monitoramento pré-configuradas.    

2. Clique em **criar aplicativo**, que abrirá o formulário de criação de **novo aplicativo** com os seguintes campos:
    * **Nome do aplicativo**. Por padrão, o aplicativo usa o *monitoramento de qualidade de água* seguido por uma cadeia de caracteres de ID exclusiva que IOT central gera. Opcionalmente, escolha um nome de aplicativo amigável. Você pode alterar o nome do aplicativo mais tarde também.
    * **URL** – opcionalmente, você pode escolher a URL desejada. Você também pode alterar a URL mais tarde. 
    * Se você tiver uma assinatura do Azure, insira seu *diretório, a assinatura do Azure e a região*. Se você não tiver uma assinatura, poderá habilitar a **avaliação gratuita de 7 dias** e concluir as informações de contato necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../preview/quick-deploy-iot-central.md).

5. Clique no botão **criar** na parte inferior da página. 

    ![Página da Aplicação do Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)


6. Agora você criou um aplicativo de monitoramento de qualidade de água usando o **modelo de monitoramento de qualidade de água**IOT central do Azure. 

Seu aplicativo recém-criado vem com pré-configurado:
* Painéis de exemplo de operador
* Exemplo de modelos de dispositivo de monitor de qualidade de água predefinidos
* Dispositivos de monitor de qualidade de água simulados
* Regras e trabalhos pré-configurados
* Identidade visual de amostra usando o rótulo branco 

É seu aplicativo e você pode modificá-lo a qualquer momento. Agora vamos explorar o aplicativo e fazer algumas personalizações.  


## <a name="explore-and-customize-operator-dashboard"></a>Explorar e personalizar o painel do operador 
Depois de criar o aplicativo, você é levado para o **painel de monitoramento de qualidade de água de água larga**.


   ![Painel de monitoramento de qualidade de água](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Como um construtor, você pode criar e personalizar modos de exibição no painel para operadores. Antes de tentar personalizar o, vamos explorar o painel. 

Todos os dados exibidos no painel baseiam-se em dados de dispositivo simulados, que serão explorados na próxima seção. 

O painel consiste em diferentes tipos de blocos:

* **Bloco de imagem do utilitário de água Wide World**: o primeiro bloco no painel é um bloco de imagem de um utilitário de água fictícia "Wide World aquático". Você pode personalizar o bloco e colocar sua própria imagem ou removê-la. 

* **Bloco médio de KPI do pH**: você pode ver que há blocos de KPI na parte superior **média da pH nos últimos 30 minutos**. Você pode personalizar os blocos de KPI e definir como um tipo e intervalo de tempo diferentes.

*  **Mapa da área de monitoramento da água**: IOT central usa mapas do Azure, que você pode definir diretamente em seu aplicativo e exibir o local do dispositivo. Você também pode mapear informações de localização para seu dispositivo a partir do aplicativo e pode usar mapas do Azure para exibi-lo em um mapa. Tente focalizar o mapa e experimentar os controles sobre o mapa. 

* **Gráfico de calor de distribuição pH médio**: você pode escolher gráficos de visualização diferentes para exibir dados de telemetria do dispositivo da maneira mais apropriada para seu aplicativo. Calor 

* **Gráfico de linhas dos indicadores de qualidade crítica**: você pode visualizar um ou vários dispositivos telemetrias plotados como um gráfico de linhas em um intervalo de tempo desejado.  

* **Gráfico de barras de concentração de agentes químicos**: você pode visualizar um ou vários dados de telemetria de dispositivo em um gráfico de barras como no exemplo. 

* **Botão de ação**: o painel inclui um exemplo de um bloco de ação que um operador pode iniciar diretamente do painel de monitoramento, como iniciar uma ação para redefinir as propriedades do dispositivo. 

* **Blocos de lista de propriedades**: o painel tem vários blocos de propriedades representando informações de limite, informações de integridade do dispositivo e informações de manutenção. 


### <a name="customize-dashboard"></a>Personalizar o painel 

Como um construtor, você pode personalizar modos de exibição no Dashboard para operadores. 
1. Clique em **Editar** para personalizar o **painel de monitoramento de qualidade de água Wide World**. Você pode personalizar o painel clicando no menu **Editar** . Depois que o painel estiver no modo de **edição** , você poderá adicionar novos blocos ou pode configurar

    ![Editar painel](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

2. Clique em **+ novo** para criar um novo painel e configurar do zero. Você pode ter vários painéis e pode navegar entre seus painéis no menu painel.

## <a name="explore-water-quality-monitor-device-template"></a>Explorar o modelo de dispositivo do monitor de qualidade de água
Um modelo de dispositivo no Azure IoT Central define a capacidade de um dispositivo, que pode ser telemetria, propriedades e comandos. Como um construtor, você pode definir modelos de dispositivo no IoT Central que representam a capacidade dos dispositivos que serão conectados. No IoT Central, você também pode criar dispositivos simulados para testar seu aplicativo e modelo de dispositivo. 
 

O aplicativo de **monitoramento de qualidade aquático** criado a partir do modelo de aplicativo é fornecido com um modelo de dispositivo de monitor de qualidade de água de referência.

Para exibir o modelo de dispositivo:

1.  Clique em **modelos de dispositivo** no painel de navegação à esquerda do seu aplicativo no IOT central. 
2. Na lista modelos de dispositivo, você verá **Monitor de qualidade de água**. Abra clicando no nome.

    ![Modelo de dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customizing-the-device-template"></a>Personalizando o modelo de dispositivo

Tente personalizar o seguinte:
1. Navegue até **Personalizar** no menu modelo de dispositivo
2. Localizar o tipo de telemetria `Temperature`
3. Atualizar o **nome de exibição** de `Temperature` para `Reported temperature`
4. Atualizar unidade de medida ou definir valor *mínimo* e *valor máximo*
5. **Salvar** as alterações 

#### <a name="add-a-cloud-property"></a>Adicionar uma propriedade de nuvem 
1. Navegue até a **propriedade de nuvem** no menu modelo de dispositivo
2. Adicione uma nova propriedade de nuvem clicando em **+ Adicionar Propriedade de nuvem**. No IoT Central, você pode adicionar uma propriedade que é relevante para o dispositivo, mas que não se espera que seja enviada por um dispositivo. Por exemplo, uma propriedade de nuvem pode ser um limite de alerta específico para área de instalação, informações de ativo ou informações de manutenção etc. 
3. **Salvar** as alterações 
 
### <a name="views"></a>Vistas 
O modelo de dispositivo de monitor de qualidade de água vem com exibições predefinidas. Explore as exibições e você pode fazer atualizações. As exibições definem como os operadores verão os dados do dispositivo, mas também inserindo as propriedades da nuvem. 

  ![Exibições de modelo de dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicar 

Se você fez alterações, certifique-se de **publicar** o modelo de dispositivo. 


### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo 
1. Selecione **+ novo** para criar um novo modelo de dispositivo e siga o processo de criação. 
2. Crie um modelo de dispositivo personalizado do zero ou você pode escolher um modelo de dispositivo no catálogo de dispositivos do Azure. 


## <a name="explore-simulated-devices"></a>Explorar dispositivos simulados

O aplicativo de **monitoramento de qualidade aquático** criado a partir do modelo de aplicativo tem dois dispositivos simulados mapeados para o modelo de dispositivo de monitor de qualidade de água. 

### <a name="to-view-the-devices"></a>Para exibir os dispositivos:
1. Navegue até o **dispositivo** no painel de navegação IOT central esquerdo. 

   ![Dispositivos](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)


2. Selecione e clique em um dispositivo simulado 

    ![Dispositivo 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

3. Na guia **Propriedades de nuvem** , tente atualizar o valor de `Acidity (pH) Threshold` de `8` para `9`. 
4. Explore a guia **Propriedades do dispositivo** e a guia painel do **dispositivo** . 

> [!NOTE]
> Observe que todas as guias foram configuradas nos **modos de exibição de modelo de dispositivo**.


### <a name="add-new-devices"></a>Adicionar novos dispositivos
Você pode adicionar novos dispositivos clicando em **+ novo** na guia **dispositivos** . 


## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central você pode criar regras para monitorar automaticamente a telemetria do dispositivo e disparar uma ação quando uma ou mais condições forem atendidas. As ações podem incluir o envio de notificações por email ou o acionamento de uma ação Microsoft Flow ou uma ação de webhook para enviar dados a outros serviços.

O aplicativo de **monitoramento de qualidade de água** que você criou modelo tem duas regras pré-configuradas.

### <a name="to-view-rules"></a>Para exibir regras:
1. Navegue até **regras** no painel de navegação IOT central esquerdo. 

   ![Regras](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)


2. Selecione e clique no **alerta pH alto** , que é uma das regras pré-configuradas no aplicativo. 

     ![Alerta pH alto](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

    A regra de `High pH alert` está configurada para verificar a condição `Acidity (pH) is greater than 8`.

Agora, vamos criar uma ação de email.

Para adicionar uma ação à regra:

1. Selecione **+ email**. 
2.  Insira o *alerta pH alto* como o **nome de exibição** amigável para a ação.
3. Insira o endereço de email associado à sua conta de IoT Central no **para**. 
4. Opcionalmente, insira uma observação a ser incluída no texto do email.
5. Selecione **concluído** para concluir a ação.
6. Selecione **salvar** para salvar e ativar a nova regra. 

Em alguns minutos, você deverá receber emails quando a **condição** configurada for atendida.

> [!NOTE]
> O aplicativo enviará um email toda vez que uma condição for atendida. **Desabilite** a regra para parar de receber emails da regra automatizada. 
  
Para criar uma nova regra: 
1. Selecione **+ novo** nas **regras** do painel de navegação esquerdo.

## <a name="configure-jobs"></a>Configurar trabalhos

No IoT Central, os trabalhos permitem que você acione atualizações de propriedades de dispositivo ou de nuvem em vários dispositivos. Além das propriedades, você também pode usar trabalhos para disparar comandos de dispositivo em vários dispositivos. IoT Central automatizará o fluxo de trabalho para você. 

1. Vá para **trabalhos** no painel de navegação à esquerda. 
2. Clique em **+ novo** e configure um ou mais trabalhos. 


## <a name="customize-your-application"></a>Personalizar seu aplicativo 
Como um construtor, você pode alterar várias configurações para personalizar a experiência do usuário em seu aplicativo.

1. Selecione **administração > personalizar seu aplicativo**.
2. Use o botão **alterar** para escolher uma imagem a ser carregada como o **logotipo do aplicativo**.
3.  Use o botão **alterar** para escolher uma imagem de **ícone do navegador** que será exibida nas guias do navegador.
4. Você também pode substituir as cores padrão do **navegador** adicionando códigos de cores hexadecimais HTML.
5. Além disso, altere o **tema** clicando nas **configurações** na manchete.

   ![O Azure IoT Central personalizar seu aplicativo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="to-update-the-application-image"></a>Para atualizar a imagem do aplicativo:

6.  Selecione **administração > configurações do aplicativo**.

7. Use o botão **selecionar imagem** para escolher uma imagem a ser carregada como a imagem do aplicativo. 


  
## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, exclua seu aplicativo com as seguintes etapas:

1. Abra a guia Administração no painel esquerdo do seu aplicativo IoT Central.
2. Selecione Configurações do aplicativo e clique no botão excluir na parte inferior da página.

    ![Eliminar aplicação](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)        



## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [conceitos de monitoramento de qualidade de água](./concepts-waterqualitymonitoring-architecture.md)
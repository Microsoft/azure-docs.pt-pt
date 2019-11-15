---
title: 'Tutorial: criar um aplicativo de gerenciamento de resíduos conectado com o Azure IoT Central'
description: 'Tutorial: Aprenda a criar um aplicativo de gerenciamento de resíduos conectado usando o Azure IoT Central modelos de aplicativo.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 38da444779a56f39d4119f3797ddb5bd2f31aeae
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112634"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Tutorial: criar um aplicativo de gerenciamento de resíduos conectado no IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial orienta você a criar um aplicativo de gerenciamento de lixo conectado do Azure IoT Central do IoT Central modelo de aplicativo de **Gerenciamento de resíduos conectado** . 

O tutorial aprenderá a: 

> [!div class="checklist"]
> * Use o modelo de **Gerenciamento de lixo conectado** do Azure IOT central para criar seu aplicativo de gerenciamento de resíduos conectado
> * Explorar e personalizar o painel do operador 
> * Explorar o modelo de dispositivo de lixeira de lixo conectado
> * Explorar dispositivos simulados
> * Explorar e configurar regras
> * Configurar trabalhos
> * Personalizar a identidade visual do aplicativo usando o autorizações

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará de:
-  Uma assinatura do Azure é recomendada. Opcionalmente, você pode usar uma avaliação gratuita de 7 dias. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de inscrição do Azure](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Criar um aplicativo de gerenciamento de resíduos conectado no IoT Central

Nesta seção, usaremos o **modelo de gerenciamento de lixo conectado** do Azure IOT central para criar seu aplicativo de gerenciamento de resíduos conectado no IOT central.

Para criar um novo aplicativo de gerenciamento de lixo conectado do Azure IoT Central:  

1. Navegue até o site [do Azure IOT central Home Page](https://aka.ms/iotcentral) .

    Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-la, caso contrário, entre usando uma conta Microsoft:

    ![Introduza a sua conta da organização](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Clique em **Compilar** no painel esquerdo e selecione a guia **governo** . A página do governo exibe vários modelos de aplicativos do governo.

    ![Criar modelos de aplicativo do governo](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecione o modelo de aplicativo de **Gerenciamento de resíduos conectado** . Este modelo inclui exemplo de modelo de dispositivo de compartimento de lixo conectado, dispositivo simulado, painel de operador e regras de monitoramento pré-configuradas.    

2. Clique em **criar aplicativo**, que abrirá o formulário de criação de **novo aplicativo** com os seguintes campos:
    * **Nome do aplicativo**. Por padrão, o aplicativo usa o *Gerenciamento de resíduos conectado* seguido por uma cadeia de caracteres de ID exclusiva que IOT central gera. Opcionalmente, escolha um nome de aplicativo amigável. Você pode alterar o nome do aplicativo mais tarde também.
    * **URL** – opcionalmente, você pode escolher a URL desejada. Você também pode alterar a URL mais tarde. 
    * Se você tiver uma assinatura do Azure, insira seu *diretório, a assinatura do Azure e a região*. Se você não tiver uma assinatura, poderá habilitar a **avaliação gratuita de 7 dias** e concluir as informações de contato necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../preview/quick-deploy-iot-central.md).

5. Clique no botão **criar** na parte inferior da página. 

    ![Página Azure IoT Central criar aplicativo de lixo conectado](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)

6. Agora você criou um aplicativo de gerenciamento de resíduos conectado usando o **modelo de gerenciamento de resíduos**do Azure IOT central conectado. 

Parabéns! Seu aplicativo recém-criado vem com pré-configurado:
* Painéis de exemplo de operador
* Modelos de dispositivo de compartimento de lixo de exemplo conectados previamente definidos
* Dispositivos de compartimento de lixo conectados simulados
* Regras e trabalhos pré-configurados
* Identidade visual de amostra usando o rótulo branco 

É seu aplicativo e você pode modificá-lo a qualquer momento. Agora vamos explorar o aplicativo e fazer algumas personalizações.  

## <a name="explore-and-customize-operator-dashboard"></a>Explorar e personalizar o painel do operador 
Depois de criar o aplicativo, você poderá colocar o **painel de gerenciamento de resíduos de grande desperdício conectado**.

   ![Painel de gerenciamento de resíduos conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Como um construtor, você pode criar e personalizar modos de exibição no painel para operadores. Antes de tentar personalizar o, vamos explorar o painel. 

> [!NOTE]
> Todos os dados exibidos no painel são baseados em dados de dispositivo simulados, que serão explorados na próxima seção. 

O painel consiste em diferentes tipos de blocos:

* ***Bloco de imagem do utilitário de resíduos Wide World***: o primeiro bloco no painel é um bloco de imagem de um utilitário de lixo fictício "amplo mundo". Você pode personalizar o bloco e colocar sua própria imagem ou removê-la. 

* ***Bloco de imagem de lixeira de lixo***: você pode usar blocos de imagem e conteúdo para criar uma representação visual do dispositivo que está sendo monitorado junto com um texto descritivo. 

* ***Bloco KPI de nível de preenchimento***: o bloco exibe um valor relatado por um sensor de *nível de preenchimento* em uma lixeira de lixo. O *nível de preenchimento* e outros sensores, como o medidor de *cheiro* ou *peso* em uma lixeira de lixo, podem ser monitorados remotamente. Um operador pode tomar medidas, como distribuir o caminhão da coleção de lixo. 

*  ***Mapa da área de monitoramento de resíduos***: o mapa está usando mapas do Azure, que podem ser configurados diretamente no Azure IOT central. O bloco mapa está exibindo a localização do dispositivo. Tente focalizar o mapa e experimentar os controles sobre o mapa, como ampliar, reduzir ou expandir.

     ![Mapa do painel de gerenciamento de resíduos conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***preenchimento, cheiro, gráfico de barras de nível de peso**: é possível visualizar um ou vários dados de telemetria do dispositivo em um gráfico de barras. Você também pode expandir o gráfico de barras.  

  ![Gráfico de barras do painel de gerenciamento de resíduos conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Bloco de conteúdo de serviços de campo**: o painel inclui um link para como integrar com os serviços de campo do Dynamics 365 do aplicativo IOT central do Azure. Por exemplo, você pode usar os serviços de campo para criar tíquetes para expedição de serviços de coleta de lixo. 


### <a name="customize-dashboard"></a>Personalizar o painel 

Como um construtor, você pode personalizar modos de exibição no Dashboard para operadores. Você pode tentar:
1. Clique em **Editar** para personalizar o **painel de gerenciamento de resíduos do Wide World conectado**. Você pode personalizar o painel clicando no menu **Editar** . Depois que o painel estiver no modo de **edição** , você poderá adicionar novos blocos ou pode configurar 

    ![Editar painel](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Você também pode clicar em **+ novo** para criar um novo painel e configurar do zero. Você pode ter vários painéis e pode navegar entre seus painéis no menu painel. 

## <a name="explore-connected-waste-bin-device-template"></a>Explorar o modelo de dispositivo de lixeira de lixo conectado

Um modelo de dispositivo no Azure IoT Central define a capacidade de um dispositivo, que pode ser telemetria, propriedades ou comando. Como um construtor, você pode definir modelos de dispositivo que representam a capacidade dos dispositivos que você conectará. 
 

O aplicativo de **Gerenciamento de resíduos conectado** vem com um modelo de dispositivo de compartimento de lixo de exemplo conectado.

Para exibir o modelo de dispositivo:

1. Clique em **modelos de dispositivo** no painel de navegação à esquerda do seu aplicativo no IOT central. 

    ![Modelo de dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. Na lista modelos de dispositivo, você verá **Lixeira de lixo conectado**. Abra clicando no nome.

3. Familirize com os recursos do modelo de dispositivo. Você pode ver que ele define sensores como *nível de preenchimento*, medidor de cheiro de *queimado*, *peso*, *local* , etc.

   ![Modelo de dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Personalizando o modelo de dispositivo

Tente personalizar o seguinte:
1. Navegue até **Personalizar** no menu modelo de dispositivo
2. Localizar o tipo de telemetria `Odor meter`
3. Atualizar o **nome de exibição** de `Odor meter` para `Odor level`
4. Você também pode tentar atualizar a unidade de medida ou definir o valor *mínimo* e o *valor máximo*
5. **Salvar** as alterações 

### <a name="add-a-cloud-property"></a>Adicionar uma propriedade de nuvem 

1. Navegue até a **propriedade de nuvem** no menu modelo de dispositivo
2. Adicione uma nova propriedade de nuvem clicando em **+ Adicionar Propriedade de nuvem**. No IoT Central, você pode adicionar uma propriedade que é relevante para o dispositivo, mas que não se espera que seja enviada por um dispositivo. Por exemplo, uma propriedade de nuvem pode ser um limite de alerta específico para área de instalação, informações de ativo ou informações de manutenção etc. 
3. **Salvar** as alterações 
 
### <a name="views"></a>Vistas 
* O modelo de dispositivo de compartimento de lixo conectado vem com exibições predefinidas. Explore as exibições e você pode fazer atualizações. As exibições definem como os operadores verão os dados do dispositivo, mas também inserindo as propriedades da nuvem. 

  ![Exibições de modelo de dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publicar 

* Se você fez alterações, certifique-se de **publicar** o modelo de dispositivo. 

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo 

* Selecione **+ novo** para criar um novo modelo de dispositivo e siga o processo de criação. Você poderá criar um modelo de dispositivo personalizado do zero ou escolher um modelo de dispositivo no catálogo de dispositivos do Azure. 

## <a name="explore-simulated-devices"></a>Explorar dispositivos simulados

No IoT Central, você pode criar dispositivos simulados para testar seu aplicativo e modelo de dispositivo. 

O aplicativo de **Gerenciamento de resíduos conectado** tem dois dispositivos simulados mapeados para o modelo de dispositivo de compartimento de lixo conectado. 

### <a name="to-view-the-devices"></a>Para exibir os dispositivos:

1. Navegue até o **dispositivo** no painel de navegação IOT central esquerdo. 

   ![Dispositivos](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Selecione e clique em dispositivo de compartimento de lixo conectado.  

     ![Dispositivo 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Navegue até a guia **Propriedades de nuvem** tente atualizar o valor de `Bin full alert threshold` de `95` para `100`. 
* Explore a guia **Propriedades do dispositivo** e a guia painel do **dispositivo** . 

> [!NOTE]
> Observe que todas as guias foram configuradas nos **modos de exibição de modelo de dispositivo**.

### <a name="add-new-devices"></a>Adicionar novos dispositivos

* Você pode adicionar novos dispositivos clicando em **+ novo** na guia **dispositivos** . 

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, você pode criar regras para monitorar automaticamente a telemetria do dispositivo e disparar ações quando uma ou mais condições forem atendidas. As ações podem incluir o envio de notificações por email, o acionamento de uma ação de Microsoft Flow ou uma ação de webhook para enviar dados a outros serviços.

O aplicativo de **Gerenciamento de resíduos conectado** tem quatro regras de exemplo.

### <a name="to-view-rules"></a>Para exibir regras:
1. Navegue até **regras** no painel de navegação IOT central esquerdo

   ![Regras](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Selecione o **alerta de compartimento completo**

     ![Alerta de compartimento completo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. O `Bin full alert` verifica quando a **condição** `Fill level is greater than or equal to Bin full alert threshold`.

    O `Bin full alert threshold` é uma *propriedade de nuvem* definida no modelo de dispositivo de `Connected waste bin`. 

Agora, vamos criar uma ação de email.

### <a name="create-an-email-action"></a>Criar uma ação de email
Para configurar uma ação de email na lista de ações da regra:
1. Selecione **+ email**. 
2. Insira o *alerta pH alto* como o **nome de exibição** amigável para a ação.
3. Insira o endereço de email associado à sua conta de IoT Central no **para**. 
4. Opcionalmente, insira uma observação a ser incluída no texto do email.
5. Selecione **concluído** para concluir a ação.
6. Selecione **salvar** para salvar e ativar a nova regra. 

Você deve receber emails quando a **condição** configurada for atendida.

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

### <a name="to-change-the-application-theme"></a>Para alterar o tema do aplicativo:

1. Vá para **administração > personalizar seu aplicativo**.
3. Use o botão **alterar** para escolher uma imagem a ser carregada como o **logotipo do aplicativo**.
4. Use o botão **alterar** para escolher uma imagem de **ícone do navegador** que será exibida nas guias do navegador.
5. Você também pode substituir as cores padrão do **navegador** adicionando códigos de cores hexadecimais HTML.

   ![O Azure IoT Central personalizar seu aplicativo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Você também pode alterar as imagens do aplicativo acessando o botão **administração > configurações do aplicativo** e **selecionar imagem** para escolher uma imagem a ser carregada como a imagem do aplicativo.
7. Por fim, você também pode alterar o **tema** clicando em **configurações** na manchete do aplicativo.

  
## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, exclua seu aplicativo com as seguintes etapas:

1. Abra a guia Administração no painel esquerdo do seu aplicativo IoT Central.
2. Selecione Configurações do aplicativo e clique no botão excluir na parte inferior da página.

  

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [conceitos de gerenciamento de resíduos conectados](./concepts-connectedwastemanagement-architecture.md)

---
title: 'Tutorial: Criar uma app de gestão de resíduos conectada com a Azure IoT Central'
description: 'Tutorial: Aprenda a construir Crie uma aplicação de gestão de resíduos conectada utilizando modelos de aplicação Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5ecfa3e4c46c17a3b381e9743953c7d97309ea3b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587252"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Tutorial: Criar uma aplicação de gestão de resíduos conectada na IoT Central

Este tutorial guia-o a criar uma aplicação de gestão de resíduos conectada Azure IoT Central a partir do modelo de aplicação de **gestão de resíduos IoT** Central Connected. 

Neste tutorial, vai aprender a: 

* Utilize o modelo de gestão de **resíduos Azure** IoT Central Connected para criar a sua aplicação de gestão de resíduos conectada
* Explore e personalize o painel de instrumentos do operador 
* Explore o modelo de dispositivo de lixo conectado
* Explore dispositivos simulados
* Explorar e configurar regras
* Configurar tarefas
* Personalize a sua marca de aplicação usando a rotulagem branca

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:
*  Recomenda-se uma subscrição do Azure. Pode opcionalmente usar um teste gratuito de 7 dias. Se não tiver uma subscrição do Azure, pode criar uma na página de inscrição do [Azure](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Criar app de gestão de resíduos conectados na IoT Central

Nesta secção, utilize o modelo de gestão de **resíduos** Azure IoT Central Connected para criar a sua aplicação de gestão de resíduos conectada na IoT Central.

Para criar uma nova aplicação de gestão de resíduos conectada Azure IoT Central:  

1. Navegue para o website [da página inicial do Azure IoT Central.](https://aka.ms/iotcentral)

    Se tiver uma subscrição Azure, inscreva-se com as credenciais que utiliza para aceder a ela, caso contrário, inscreva-se através de uma conta Microsoft:

    ![Introduza a sua conta da organização](./media/tutorial-connectedwastemanagement/sign-in.png)

1. Clique em **Construir** a partir do painel esquerdo e selecione o separador **Governo.** A página do governo exibe vários modelos de aplicação do governo.

    ![Construa modelos de aplicativos do governo](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecione o modelo de aplicação **de gestão de resíduos conectado.** Este modelo inclui modelo de dispositivo de lixo conectado à amostra, dispositivo simulado, painel de instrumentos do operador e regras de monitorização pré-configuradas.    

1. Clique **em Criar app**, que abrirá novo formulário de criação de **aplicações** com os seguintes campos:
    * **Nome da aplicação**. Por predefinição, a aplicação utiliza *a gestão de resíduos conectados* seguido de uma cadeia de ID única que a IoT Central gera. Opcionalmente, escolha um nome de aplicação amigável. Pode alterar o nome da aplicação mais tarde também.
    * **URL** – Opcionalmente, pode escolher o URL pretendido. Pode alterar o URL mais tarde também. 
    * Se tiver uma subscrição Azure, insira o seu *Diretório, subscrição Azure e Região*. Se não tiver uma subscrição, pode ativar um **teste gratuito de 7 dias** e completar as informações de contacto necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../core/quick-deploy-iot-central.md).

1. Clique em **Criar** botão na parte inferior da página. 

    ![Azure IoT Central Criar Página de Aplicação de Resíduos Conectados](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central Criar Informações de Faturação Conectadas](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
1. Criou agora uma aplicação de gestão de resíduos conectada utilizando o modelo de **gestão de resíduos Azure** IoT Central Connected. 

Parabéns! A sua aplicação recém-criada vem com pré-configuração:
* Dashboards do operador da amostra
* Modelos de dispositivos de resíduos conectados pré-definidos pré-definidos
* Dispositivos de caixotes do lixo ligados simulados
* Regras e empregos pré-configurados
* Marcação de amostra usando rotulagem branca 

É a sua aplicação e pode modificá-la a qualquer momento. Vamos agora explorar a aplicação e fazer algumas personalizações.  

## <a name="explore-and-customize-operator-dashboard"></a>Explore e personalize o painel de instrumentos do operador 
Depois de criar a aplicação, aterra no **painel de gestão de resíduos conectados a grandes resíduos.**

   ![Painel de gestão de resíduos conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Como construtor, pode criar e personalizar vistas no painel de instrumentos para os operadores. Antes de tentar personalizar, vamos explorar o painel de instrumentos. 

>>[!NOTE]
>> Todos os dados apresentados no painel de instrumentos são baseados em dados simulados do dispositivo, que serão explorados na secção seguinte. 

O painel é composto por diferentes tipos de azulejos:

* *Azulejo de imagem de **utilidade de resíduos mundiais** de largura _: o primeiro azulejo no tablier é um azulejo de imagem de um utilitário fictício de resíduos "Wide World Waste". Pode personalizar o azulejo e colocar a sua própria imagem ou removê-la. 

_ ***Azulejo de imagem do caixote do lixo** _: pode utilizar azulejos de imagem e conteúdo para criar uma representação visual do dispositivo que está a ser monitorizado juntamente com um texto descritivo. 

_ ***Encha o azulejo KPI de nível** _: o azulejo apresenta um valor relatado por um sensor de nível _fill* num caixote do lixo. *O nível de enchimento* e outros sensores como *o medidor de odor* ou *peso* num caixote do lixo podem ser monitorizados remotamente. Um operador pode tomar medidas, como despachar um camião de recolha de lixo. 

* ***Mapa da área de monitorização de resíduos** _: o mapa está a usar o Azure Maps, que pode configurar diretamente no Azure IoT Central. O azulejo do mapa está a mostrar a localização do dispositivo. Tente pairar sobre o mapa e tente os controlos sobre o mapa, como zoom-in, zoom-out ou expansão.

     ![Mapa do painel de instrumentos de gestão de resíduos conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


_ ***Preencha, odor, gráfico de barras de nível de peso:** pode visualizar dados de telemetria de um ou vários dispositivos num gráfico de barras. Também pode expandir o gráfico de barras.  

  ![Gráfico de barra de painel de dashboard de gestão de resíduos conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Azulejo de conteúdo de Serviços de** Campo : o dashboard inclui ligação à forma de integração com os Serviços de Campo Dynamics 365 a partir da sua aplicação Azure IoT Central. Como exemplo, você pode usar os Serviços de Campo para criar bilhetes para despachar serviços de recolha de lixo. 


### <a name="customize-dashboard"></a>Personalizar painel de instrumentos 

Como construtor, pode personalizar vistas no painel de instrumentos para os operadores. Pode tentar:
1. Clique em **Editar** para personalizar o **painel de gestão de resíduos conectado** ao Mundo Largo. Pode personalizar o dashboard clicando no menu **Editar.** Uma vez que o painel de instrumentos esteja em modo **de edição,** pode adicionar novos azulejos, ou pode configurar 

    ![Editar Dashboard](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

1. Também pode clicar em **+ Novo** para criar um novo dashboard e configurar do zero. Pode ter vários dashboards e pode navegar entre os seus dashboards a partir do menu do painel de instrumentos. 

## <a name="explore-connected-waste-bin-device-template"></a>Explore o modelo de dispositivo de lixo conectado

Um modelo de dispositivo no Azure IoT Central define a capacidade de um dispositivo, que pode ser telemetria, propriedades ou comando. Como construtor, pode definir modelos de dispositivos que representam a capacidade dos dispositivos que irá ligar. 

A aplicação **de gestão de resíduos conectada** vem com um modelo de dispositivo de lixo ligado à amostra.

Para ver o modelo do dispositivo:

1. Clique nos **modelos** do Dispositivo a partir do painel esquerdo da sua aplicação no IoT Central. 

    ![Screenshot mostrando a lista de modelos de dispositivo na aplicação](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. Na lista de modelos do dispositivo, verá **o caixote do lixo conectado.** Abra clicando no nome.

1. Familiarize-se com as capacidades do modelo do dispositivo. Pode ver que define sensores como *nível de enchimento,* *medidor de odor,* *peso,* *localização,* e outros.

   ![Screenshot mostrando os detalhes do modelo de dispositivo de lixo conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customizing-the-device-template"></a>Personalizar o modelo do dispositivo

Tente personalizar o seguinte:
1. Navegue para **Personalizar** a partir do menu do modelo do dispositivo
1. Encontre o `Odor meter` tipo de telemetria
1. Atualizar o **nome do Display** `Odor meter` de `Odor level`
1. Também pode tentar a unidade de atualização de medição, ou definir *valor Min* e *valor Max*
1. **Guardar** quaisquer alterações 

### <a name="add-a-cloud-property"></a>Adicione uma propriedade em nuvem 

1. Navegue para a **propriedade Cloud** a partir do menu do modelo do dispositivo
1. Adicione uma nova propriedade em nuvem clicando **+ Add Cloud Property**. No IoT Central, pode adicionar um imóvel que seja relevante para o dispositivo, mas que não seja esperado que seja enviado por um dispositivo. Como exemplo, uma propriedade em nuvem pode ser um limiar de alerta específico para a área de instalação, informações de bens ou informações de manutenção, e outras informações. 
1. **Guardar** quaisquer alterações 
 
### <a name="views"></a>Vistas 
* O modelo do dispositivo do caixote do lixo conectado vem com vistas pré-definidas. Explore as vistas e poderá fazer atualizações. As vistas definem como os operadores vão ver os dados do dispositivo, mas também inserir propriedades na nuvem. 

  ![Vistas do modelo do dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publicar 

* Se escoar alguma alteração certifique-se de **publicar** o modelo do dispositivo. 

### <a name="create-a-new-device-template"></a>Criar um novo modelo do dispositivo 

* Selecione **+ Novo** para criar um novo modelo de dispositivo e siga o processo de criação. Poderá criar um modelo de dispositivo personalizado de raiz ou pode escolher um modelo de dispositivo a partir do Catálogo de Dispositivos Azure. 

## <a name="explore-simulated-devices"></a>Explore dispositivos simulados

No IoT Central, pode criar dispositivos simulados para testar o modelo e aplicação do dispositivo. 

A aplicação **de gestão de resíduos conectados** tem dois dispositivos simulados mapeados para o modelo do dispositivo do lixo conectado. 

### <a name="to-view-the-devices"></a>Para ver os dispositivos:

1. Navegue para **o dispositivo** a partir do painel esquerdo IoT Central. 

   ![Dispositivos](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Selecione e clique no dispositivo de caixote de lixo conectado.  

     ![Dispositivo 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Navegue no separador **Cloud Properties** tente atualizar o valor `Bin full alert threshold` de `95` `100` . 
* Explore o separador Propriedades do **Dispositivo** e o **separador dashboard do dispositivo.** 

>> [!NOTE]
>> Todos os separadores foram configurados a partir das **vistas** do modelo de dispositivo .

### <a name="add-new-devices"></a>Adicionar novos dispositivos

* Pode adicionar novos dispositivos clicando em **+ Novo** no **separador Dispositivos.** 

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, pode criar regras para monitorizar automaticamente a telemetria do dispositivo e desencadear ações quando uma ou mais condições forem satisfeitas. As ações podem incluir o envio de notificações por e-mail, o desencadear de uma ação Power Automamate ou uma ação webhook para enviar dados para outros serviços.

A aplicação **de gestão de resíduos conectados** tem quatro regras de amostragem.

### <a name="to-view-rules"></a>Para ver as regras:
1. Navegue para **as regras** do painel esquerdo IoT Central

   ![Regras](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Selecione o **alerta completo do Bin**

     ![Bin alerta completo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. O `Bin full alert` cheque quando **condição** `Fill level is greater than or equal to Bin full alert threshold` .

    É `Bin full alert threshold` uma propriedade em *nuvem* definida no modelo do `Connected waste bin` dispositivo. 

Agora vamos criar uma ação por e-mail.

### <a name="create-an-email-action"></a>Criar uma ação de e-mail
Para configurar uma ação de e-mail na lista de ações da Regra:
1. Selecione **+ E-mail**. 
1. Introduza *o alerta de pH alto* como o nome de **exibição** amigável para a ação.
1. Insira o endereço de e-mail associado à sua conta IoT Central em **To**. 
1. Opcionalmente, insira uma nota para incluir no texto do e-mail.
1. Selecione **Feito** para completar a ação.
1. **Selecione Guardar** para guardar e ativar a nova regra. 

Deve receber e-mail quando a **condição** configurada for satisfeita.

>[!NOTE]
>A aplicação enviará e-mail cada vez que uma condição é satisfeita. **Desative** a regra para deixar de receber e-mail da regra automatizada. 
  
Para criar uma nova regra: 
1. **Selecione +Novo** nas **Regras** a partir do painel esquerdo.

## <a name="configure-jobs"></a>Configure empregos

No IoT Central, os trabalhos permitem-lhe ativar atualizações de propriedades de dispositivos ou nuvem em vários dispositivos. Além das propriedades, também pode usar trabalhos para ativar comandos de dispositivos em vários dispositivos. A IoT Central automatizará o fluxo de trabalho para si. 

1. Vá para **Jobs** do painel esquerdo. 
1. Clique **em +Novos** e configuure um ou mais empregos. 


## <a name="customize-your-application"></a>Personalize a sua aplicação 

Como construtor, pode alterar várias definições para personalizar a experiência do utilizador na sua aplicação.

### <a name="to-change-the-application-theme"></a>Para alterar o tema da aplicação:

1. Vá à **Administração > personalize a sua aplicação.**
1. Utilize o botão **Alterar** para escolher uma imagem para carregar como **o logótipo da Aplicação**.
1. Utilize o botão **Alterar** para escolher uma imagem **de ícone do Navegador** que aparecerá nos separadores do navegador.
1. Também pode substituir as cores padrão do Navegador adicionando **códigos** de cores hexadémicas HTML.

   ![A azure IoT Central personaliza a sua aplicação](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Também pode alterar as imagens de aplicação indo para as **definições de Aplicação > administração** e **selecionar** o botão de imagem para escolher uma imagem para carregar como imagem da aplicação.
1. Finalmente, também pode alterar o **Tema** clicando em **Definições** no masthead da aplicação.

  
## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a sua aplicação com os seguintes passos:

1. Abra o separador Administração a partir do painel esquerdo da sua aplicação IoT Central.
1. Selecione as definições de Aplicação e clique em Eliminar o botão na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre
 
> [!div class="nextstepaction"]
> [Conceitos de gestão de resíduos conectados](./concepts-connectedwastemanagement-architecture.md)

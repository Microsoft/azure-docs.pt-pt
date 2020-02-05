---
title: 'Tutorial: Criar uma app de gestão de resíduos conectada com a Azure IoT Central'
description: 'Tutorial: Aprenda a construir Criar uma aplicação de gestão de resíduos conectada utilizando modelos de aplicação Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 771e4a822df3e7d8eeee53174d657426bf226372
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990041"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Tutorial: Criar uma aplicação de gestão de resíduos conectada na IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial guia-o a criar uma aplicação de gestão de resíduos conectada Azure IoT Central a partir do modelo de aplicação de gestão de **resíduos** Ligado seleções IoT. 

O tutorial aprenderá a: 

> [!div class="checklist"]
> * Utilize o modelo de **gestão** de resíduos ligado seleções Azure IoT para criar a sua aplicação de gestão de resíduos conectada
> * Explore e personalize o painel de instrumentos do operador 
> * Explore o modelo de dispositivo de lixo conectado
> * Explore dispositivos simulados
> * Explore e configure regras
> * Configurar empregos
> * Personalize a sua marca de aplicação usando etiquetagem branca

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial de que precisa:
-  Recomenda-se uma subscrição Azure. Você pode opcionalmente usar um teste gratuito de 7 dias. Se não tiver uma subscrição Azure, pode criar uma na [página de inscrição](https://aka.ms/createazuresubscription)do Azure .

## <a name="create-connected-waste-management-app-in-iot-central"></a>Criar aplicativo de Gestão de Resíduos Conectados na IoT Central

Nesta secção, utilizaremos o modelo de gestão de **resíduos** Azure IoT Central Connected para criar a sua aplicação de gestão de resíduos conectada na IoT Central.

Para criar uma nova aplicação de gestão de resíduos conectada Azure IoT Central:  

1. Navegue para o site da [página web central do Azure IoT.](https://aka.ms/iotcentral)

    Se tiver uma subscrição Azure, inscreva-se com as credenciais que utiliza para aceder ao mesmo, caso contrário, inscreva-se através de uma conta Microsoft:

    ![Introduza a sua conta da organização](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Clique em **Construir** a partir do painel esquerdo e selecione o separador **Governo.** A página do governo exibe vários modelos de aplicação do governo.

    ![Construir modelos de aplicativos do governo](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecione o modelo de aplicação de **gestão de resíduos conectados.** Este modelo inclui o modelo do dispositivo de resíduos ligado à amostra, dispositivo simulado, painel de instrumentos do operador e regras de monitorização pré-configuradas.    

2. Clique em **Criar app**, que abrirá novo formulário de criação de **aplicações** com os seguintes campos:
    * **Nome da candidatura**. Por padrão, a aplicação utiliza a *gestão de resíduos conectado,* seguida de uma cadeia de ID única que a IoT Central gera. Opcionalmente, escolha um nome de candidatura amigável. Também pode alterar o nome da candidatura.
    * **URL** – Opcionalmente, pode escolher o URL desejado. Também pode alterar o URL mais tarde. 
    * Se tiver uma assinatura Azure, insira o seu *Diretório, subscrição Azure e Região.* Se não tiver uma subscrição, pode ativar um **teste gratuito de 7** dias e completar as informações de contacto necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../preview/quick-deploy-iot-central.md).

5. Clique em **Criar** o botão na parte inferior da página. 

    ![Azure IoT Central Criar página de aplicação de resíduos conectados](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central Criar informações de faturação conectada](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. Criou agora uma aplicação de gestão de resíduos conectada utilizando o modelo de gestão de **resíduos**Azure IoT Central Connected . 

Parabéns! A sua aplicação recém-criada vem com pré-configuração:
* Painéis de exemplo de operador
* Modelos de dispositivos de resíduos pré-definidos pré-definidos
* Dispositivos de resíduos ligados simulados
* Regras e trabalhos pré-configurados
* Marca de amostra utilizando etiquetagem branca 

É a sua aplicação e pode modificá-la a qualquer momento. Vamos agora explorar a aplicação e fazer algumas personalizações.  

## <a name="explore-and-customize-operator-dashboard"></a>Explore e personalize o painel de instrumentos do operador 
Depois de criar a aplicação, aterra no painel de gestão de **resíduos conectados a Grandes Resíduos.**

   ![Painel de gestão de resíduos conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Como construtor, pode criar e personalizar vistas no painel de instrumentos para os operadores. Antes de tentar personalizar, vamos explorar o painel de instrumentos. 

> [!NOTE]
> Todos os dados apresentados no painel de instrumentos baseiam-se em dados simulados do dispositivo, que serão explorados na secção seguinte. 

O tablier é composto por diferentes tipos de azulejos:

* ***Azulejo***de imagem de utilidade do Wide World Waste : o primeiro azulejo do tablier é um azulejo de imagem de um utilitário fictício de resíduos "Wide World Waste". Pode personalizar o azulejo e colocar a sua própria imagem ou removê-lo. 

* ***Resíduos***de lixeira de imagem de azulejos : pode utilizar azulejos de imagem e conteúdo para criar uma representação visual do dispositivo que está a ser monitorizado juntamente com um texto descritivo. 

* Encha o nível de ***azulejo síPi:*** o azulejo apresenta um valor relatado por um sensor de *nível* de enchimento num caixote do lixo. *O nível de enchimento* e outros sensores, como *o medidor* de odor ou *o peso* num caixote do lixo, podem ser monitorizados remotamente. Um operador pode tomar medidas, como despachar um camião de recolha de lixo. 

*  Mapa da área de monitorização de ***resíduos***: o mapa está a utilizar o Azure Maps, que pode configurar diretamente no Azure IoT Central. O azulejo do mapa está a mostrar a localização do dispositivo. Tente pairar sobre o mapa e experimente os controlos sobre o mapa, como zoom-in, zoom-out ou expandir.

     ![Mapa do painel de instrumentos de gestão de resíduos conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* *Fill, odor, gráfico de barras de **nível de peso:** pode visualizar dados de telemetria de um ou vários dispositivos num gráfico de barras. Também pode expandir o gráfico de barras.  

  ![Gráfico de barras de painel de gestão de resíduos conectado](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* Azulejo de conteúdo de **Serviços**de Campo : o dashboard inclui ligação à forma de integrar com a Dynamics 365 Field Services a partir da sua aplicação Azure IoT Central. Como exemplo, você pode usar os Serviços de Campo para criar bilhetes para despachar serviços de recolha de lixo. 


### <a name="customize-dashboard"></a>Personalizar dashboard 

Como construtor, pode personalizar vistas no painel de instrumentos para operadores. Pode tentar:
1. Clique em **Editar** para personalizar o painel de gestão de resíduos conectado ao **Mundo Inteiro.** Pode personalizar o dashboard clicando no menu **Editar.** Uma vez que o painel de instrumentos esteja em modo **de edição,** pode adicionar novos azulejos, ou pode configurar 

    ![Editar dashboard](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Também pode clicar em **+ Novo** para criar um novo dashboard e configurar de raiz. Pode ter vários dashboards e navegar entre os seus dashboards a partir do menu do painel de instrumentos. 

## <a name="explore-connected-waste-bin-device-template"></a>Explore o modelo de dispositivo de lixo conectado

Um modelo de dispositivo em Azure IoT Central define a capacidade de um dispositivo, que pode ser telemetria, propriedades ou comando. Como construtor, pode definir modelos de dispositivo que representam a capacidade dos dispositivos que irá ligar. 
 

A aplicação de gestão de **resíduos conectado** saem com um modelo de dispositivo de resíduos conectado à amostra.

Para ver o modelo do dispositivo:

1. Clique nos **modelos** do dispositivo a partir do painel de navegação esquerdo da sua aplicação na IoT Central. 

    ![Modelo de dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. Na lista de modelos do Dispositivo, verá o caixote do **lixo conectado**. Abra clicando no nome.

3. Familiarize-se com as capacidades do modelo do dispositivo. Pode ver que define sensores como *o nível*de enchimento, *o medidor*de odor, *o peso,* *a localização,* etc.

   ![Modelo de dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Personalizando o modelo do dispositivo

Tente personalizar o seguinte:
1. Navegar para **personalizar** a partir do menu de modelo de dispositivo
2. Encontre o tipo de telemetria `Odor meter`
3. Atualize o **nome** do `Odor meter` para `Odor level`
4. Também pode experimentar a unidade de atualização de medição, ou definir *o valor de Min* e *o valor Max*
5. **Guardar** quaisquer alterações 

### <a name="add-a-cloud-property"></a>Adicione uma propriedade em nuvem 

1. Navegue para a **propriedade Cloud** a partir do menu de modelo do dispositivo
2. Adicione uma nova propriedade em nuvem clicando **+ Adicionar Propriedade cloud**. Na IoT Central, pode adicionar uma propriedade relevante para o dispositivo, mas não espera-se que seja enviada por um dispositivo. Como exemplo, uma propriedade em nuvem pode ser um limiar de alerta específico para a área de instalação, informações de ativos ou informações de manutenção, etc. 
3. **Guardar** quaisquer alterações 
 
### <a name="views"></a>Vistas 
* O modelo de dispositivo de resíduos ligado vem com vistas pré-definidas. Explore as vistas e poderá fazer atualizações. As vistas definem como os operadores verão os dados do dispositivo, mas também inserindo propriedades em nuvem. 

  ![Vistas do modelo do dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publicar 

* Se tiver feito alterações, certifique-se de **publicar** o modelo do dispositivo. 

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo 

* Selecione **+ Novo** para criar um novo modelo de dispositivo e siga o processo de criação. Poderá criar um modelo de dispositivo personalizado de raiz ou pode escolher um modelo de dispositivo do Catálogo de Dispositivos Azure. 

## <a name="explore-simulated-devices"></a>Explore dispositivos simulados

Na IoT Central, pode criar dispositivos simulados para testar o modelo e aplicação do seu dispositivo. 

A aplicação de gestão de **resíduos conectado** tem dois dispositivos simulados mapeados para o modelo de dispositivo de resíduos conectado. 

### <a name="to-view-the-devices"></a>Para visualizar os dispositivos:

1. Navegue para **dispositivo** a partir do painel de navegação esquerdo IoT Central. 

   ![Dispositivos](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Selecione e clique no dispositivo Connected Waste Bin.  

     ![Dispositivo 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Navegue para o separador **Cloud Properties** tente atualizar o valor `Bin full alert threshold` de `95` para `100`. 
* Explore o separador Propriedades do Dispositivo e o **separador Do Painel** de Instrumentos do **Dispositivo.** 

> [!NOTE]
> Note que todos os separadores foram configurados a partir do modelo de **dispositivo Vistas**.

### <a name="add-new-devices"></a>Adicione novos dispositivos

* Pode adicionar novos dispositivos clicando em **+ Novo** no separador **Dispositivos.** 

## <a name="explore-and-configure-rules"></a>Explore e configure regras

No Azure IoT Central, pode criar regras para monitorizar automaticamente a telemetria do dispositivo e desencadear ações quando uma ou mais condições são satisfeitas. As ações podem incluir o envio de notificações por e-mail, o desencadeamento de uma ação do Microsoft Flow ou uma ação de webhook para enviar dados para outros serviços.

A aplicação **connected waste management** tem quatro regras de amostra.

### <a name="to-view-rules"></a>Para ver regras:
1. Navegar para **regras** do painel de navegação esquerda IoT Central

   ![Regras](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Selecione o **alerta completo** do bin

     ![Alerta completo do caixote do lixo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. O `Bin full alert` verifica quando a **condição** `Fill level is greater than or equal to Bin full alert threshold`.

    O `Bin full alert threshold` é uma *propriedade em nuvem* definida no modelo de dispositivo `Connected waste bin`. 

Agora vamos criar uma ação por e-mail.

### <a name="create-an-email-action"></a>Criar uma ação de e-mail
Para configurar uma ação de e-mail na lista de ações da Regra:
1. Selecione **+ E-mail**. 
2. Insira *o alerta de pH elevado* como o nome de **exibição** amigável para a ação.
3. Insira o endereço de e-mail associado à sua conta IoT Central **em**. 
4. Opcionalmente, insira uma nota para incluir no texto do e-mail.
5. Selecione **Feito** para completar a ação.
6. Selecione **Guardar** para guardar e ativar a nova regra. 

Deve receber e-mail quando a **condição** configurada for satisfeita.

> [!NOTE]
> O pedido enviará e-mail sempre que uma condição for satisfeita. **Desative** a regra para deixar de receber e-mail da regra automatizada. 
  
Para criar uma nova regra: 
1. Selecione **+Novo** nas **Regras** do painel de navegação à esquerda.

## <a name="configure-jobs"></a>Configurar empregos

No IoT Central, os trabalhos permitem-lhe ativar atualizações de propriedades de dispositivos ou cloud em vários dispositivos. Além das propriedades, também pode utilizar trabalhos para acionar comandos de dispositivos em vários dispositivos. A IoT Central vai automatizar o fluxo de trabalho para si. 

1. Vá a **Jobs** a partir do painel de navegação esquerdo. 
2. Clique em **+Novo** e configure um ou mais empregos. 


## <a name="customize-your-application"></a>Personalize a sua aplicação 

Como construtor, pode alterar várias definições para personalizar a experiência do utilizador na sua aplicação.

### <a name="to-change-the-application-theme"></a>Para alterar o tema da aplicação:

1. Vá à **Administração > Personalize a sua aplicação.**
3. Utilize o botão **Alterar** para escolher uma imagem para carregar como logótipo da **Aplicação**.
4. Utilize o botão **'Alterar'** para escolher uma imagem de ícone do **Navegador** que aparecerá nos separadores do navegador.
5. Também pode substituir as cores padrão do **Navegador** adicionando códigos de cores hexadecimais HTML.

   ![Azure IoT Central personalize a sua aplicação](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Também pode alterar as imagens da aplicação indo para as definições de **'Administration > Application'** e **Selecione** um botão de imagem para escolher uma imagem para carregar como imagem da aplicação.
7. Por fim, também pode alterar o **Tema** clicando em **Definições** no masthead da aplicação.

  
## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine a sua aplicação com os seguintes passos:

1. Abra o separador Administração a partir do painel esquerdo da sua aplicação IoT Central.
2. Selecione as definições de Aplicação e clique em Eliminar o botão na parte inferior da página.

  

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [conceitos de gestão de resíduos conectados](./concepts-connectedwastemanagement-architecture.md)

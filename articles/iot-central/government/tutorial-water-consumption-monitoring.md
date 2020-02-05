---
title: 'Tutorial: Criar uma app de monitorização do consumo de água com a Azure IoT Central'
description: 'Tutorial: Aprenda a criar uma aplicação de monitorização do consumo de água utilizando modelos de aplicação Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 1bad4ab6320e757ac766776a95b8dbe6ebaa3259
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016430"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Tutorial: Criar uma aplicação de monitorização do consumo de água na IoT Central



Este tutorial guia-o a criar uma aplicação de monitorização do consumo de água Central Azure IoT a partir do modelo central de aplicação de monitorização do consumo de água IoT. 

Neste tutorial, vai aprender a: 

> [!div class="checklist"]
> * Utilize o modelo de **monitorização** do consumo de água central Azure IoT para criar a sua aplicação de monitorização do consumo de água
> * Explore e personalize o painel de instrumentos do operador 
> * Explore o modelo do dispositivo
> * Explore dispositivos simulados
> * Explore e configure regras
> * Configurar empregos
> * Personalize a sua marca de aplicação usando etiquetagem branca

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:
-  Recomenda-se uma subscrição Azure. Se não tiver uma subscrição Azure, pode criar uma na [página de inscrição](https://aka.ms/createazuresubscription)do Azure .

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Criar app de monitorização do consumo de água na IoT Central

Nesta secção, utiliza o modelo de **monitorização** do consumo de água central Azure IoT para criar a sua aplicação de monitorização do consumo de água na IoT Central.

Para criar uma nova aplicação de monitorização do consumo de água Azure IoT Central:  

1. Navegue para o site da [página web central do Azure IoT.](https://aka.ms/iotcentral)

    Se tiver uma subscrição Azure, inscreva-se com as credenciais que utiliza para aceder ao mesmo, caso contrário, inscreva-se através de uma conta Microsoft:

    ![Introduza a sua conta da organização](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Clique em **Construir** a partir do painel esquerdo e selecione o separador **Governo.** A página do governo exibe vários modelos de aplicação do governo.

   ![Construir modelos de aplicativos do governo](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selecione o modelo de aplicação de monitorização do consumo de **água.** Este modelo inclui o modelo do dispositivo de consumo de água da amostra, dispositivo simulado, painel de instrumentos do operador e regras de monitorização pré-configuradas.    

2. Clique em **Criar app**, que abrirá novo formulário de criação de **aplicações** com os seguintes campos:
    * Nome da **aplicação:** por padrão, a aplicação utiliza *monitorização* do consumo de água seguida de uma cadeia de ID única que a IoT Central gera. Opcionalmente, escolha um nome de candidatura amigável. Também pode alterar o nome da candidatura.
    * **URL**: IoT Central irá autogenizar um URL para si com base no nome da aplicação. Pode optar por atualizar o URL ao seu gosto. Também pode alterar o URL mais tarde. 
    * Se tiver uma assinatura Azure, insira o seu *Diretório, subscrição Azure e Região.* Se não tiver uma subscrição, pode ativar um **teste gratuito de 7** dias e completar as informações de contacto necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../core/quick-deploy-iot-central.md).

5. Clique em **Criar** o botão na parte inferior da página. 

    ![Página da Aplicação do Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central Criar Informação sobre Faturação de Aplicações](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

6. Criou agora uma aplicação de monitorização do consumo de água utilizando o modelo de **monitorização** do consumo de água central Do Azure IoT.

Parabéns! Terminou de criar a sua aplicação de monitorização da qualidade da água, que vem com pré-configuração:
* Painéis de exemplo de operador
* Amostra de fluxo de água pré-definida e modelos de dispositivo de válvula
* Fluxo de água simulado e dispositivos de válvula inteligente
* Regras e trabalhos pré-configurados
* Marca de amostra utilizando etiquetagem branca 

É a sua aplicação e pode modificá-la a qualquer momento. Vamos agora explorar a aplicação e fazer algumas personalizações.  

## <a name="explore-and-customize-operator-dashboard"></a>Explore e personalize o painel de instrumentos do operador 
Depois de criar a aplicação, aterra no painel de instrumentos do operador de amostras chamado Painel de **Monitorização**do Consumo de Água do Mundo Inteiro .

   ![Painel de monitorização do consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Como construtor, pode criar e personalizar vistas no painel de instrumentos para os operadores. Antes de tentar personalizar, vamos explorar o painel de instrumentos. 

> [!NOTE]
> Todos os dados apresentados no painel de instrumentos baseiam-se em dados simulados do dispositivo, que seremos explorados na secção seguinte. 
  
O tablier é composto por diferentes tipos de azulejos:

* **Azulejo**de imagem de utilidade da água do mundo largo : o primeiro azulejo no tablier é um azulejo de imagem de um utilitário de água fictício "Wide World Water". Pode personalizar o azulejo e colocar a sua própria imagem ou removê-lo. 

* **Azulejo KPI**de fluxo de água médio : o azulejo KPI está configurado para mostrar como exemplo *a média nos últimos 30 minutos*. Pode personalizar azulejos KPI e definir para um tipo e intervalo de tempo diferentes.

* Em seguida, tem direito no *painel* de comando de comando das telhas para **fechar**válvula, **válvula aberta,** ou **definir posição da válvula**. Clicar nos comandos irá levá-lo à página de comando do dispositivo simulado. Na IoT Central, um *Comando* é um tipo de capacidade de *dispositivo,* que pode explorar mais tarde na secção de modelo de **dispositivo** deste tutorial.

*  **Mapa**da área de distribuição de água : o mapa está a usar o Azure Maps, que pode configurar diretamente no Azure IoT Central. O azulejo do mapa está a mostrar a localização do dispositivo. Tente pairar sobre o mapa e experimente os controlos sobre o mapa, como *zoom-in,* *zoom-out* ou *expandir*. 

    ![Mapa do painel de monitorização do consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Gráfico médio** da linha de fluxo de água e gráfico de linha de **condições**ambientais: pode visualizar uma ou múltipla seleção de telemetrias traçadas como um gráfico de linha sobre uma faixa de tempo desejada.  

* Gráfico médio de mapa de calor da pressão da **válvula:** pode escolher o tipo de visualização do mapa térmico de dados de telemetria do dispositivo que está interessado em olhar para a distribuição ao longo de um intervalo de tempo com um índice de cores.

* **Redefinir**o conteúdo do limiar de alerta: pode incluir a chamada para os azulejos de conteúdo de ação que incorporam o link numa página de ação. Neste caso, o limiar de alerta de reset irá levá-lo à aplicação **Jobs** onde você pode executar atualizações para propriedades de dispositivos, que iremos explorar mais tarde na secção de **trabalhos configurados** deste tutorial.

* **Telhas de propriedade**: o painel de instrumentos exibe **informações operacionais da Valve,** **limiares de alerta de fluxo**e **informações de manutenção**. 


### <a name="customize-dashboard"></a>Personalizar dashboard 

Como construtor, pode personalizar vistas no painel de instrumentos para operadores. Pode tentar:
1. Clique em **Editar** para personalizar o painel de **monitorização**de consumo de água do Mundo Inteiro . Pode personalizar o dashboard clicando no menu **Editar.** Uma vez que o painel de instrumentos esteja em modo **de edição,** pode adicionar novos azulejos, ou pode configurar 

     ![Editar dashboard](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Clique em **+ Novo** para criar um novo dashboard e configurar de raiz. Pode ter vários dashboards e navegar entre os seus dashboards a partir do menu do painel de instrumentos. 

## <a name="explore-device-template"></a>Explore o modelo do dispositivo
Um modelo de dispositivo em Azure IoT Central define a capacidade de um dispositivo, que pode ser telemetria, propriedade ou comando. Como construtor, pode definir um ou mais modelos de dispositivo sintetizadores na IoT Central que representam a capacidade dos dispositivos que irá ligar. 
 

A aplicação de **monitorização** do consumo de água vem com dois modelos de dispositivo de referência que representam um *medidor* de fluxo e um dispositivo *de válvula inteligente.* 

Para ver o modelo do dispositivo:

1. Clique nos **modelos** do dispositivo a partir do painel de navegação esquerdo da sua aplicação na IoT Central. 
    Na lista de modelos do Dispositivo, você verá dois modelos de dispositivo **Flow meter** e **Smart Valve**

   ![Modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Clique no modelo do dispositivo **do contador de fluxo** e familiarize-se com as capacidades do dispositivo 

     ![Medidor de fluxo de modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Personalizando o modelo do dispositivo

Tente personalizar o seguinte:
1. Navegar para **personalizar** a partir do menu de modelo de dispositivo
2. Encontre o tipo de telemetria `Temperature`
3. Atualize o **nome** do `Temperature` para `Reported temperature`
4. Unidade de atualização de medição, ou definir *o valor de Min* e *o valor max*
5. **Guardar** quaisquer alterações 

### <a name="add-a-cloud-property"></a>Adicione uma propriedade em nuvem 
1. Navegue para a **propriedade Cloud** a partir do menu de modelo do dispositivo
2. Adicione uma nova propriedade em nuvem clicando **+ Adicionar Propriedade cloud**. 
    Na IoT Central, pode adicionar uma propriedade relevante para o dispositivo. Como exemplo, uma propriedade em nuvem pode ser um limiar de alerta específico para a área de instalação, informações de ativos ou outras informações de manutenção. 
3. **Guardar** quaisquer alterações 
 
### <a name="views"></a>Vistas 
O modelo do dispositivo de consumo de água vem com vistas pré-definidas. Explore as vistas e poderá fazer atualizações. As vistas definem como os operadores verão os dados do dispositivo, mas também inserindo propriedades em nuvem. 

  ![Vistas do modelo do dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicar 
Se tiver feito alterações, certifique-se de **publicar** o modelo do dispositivo. 

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo 
- Selecione **+ Novo** para criar um novo modelo de dispositivo e siga o processo de criação. Poderá criar um modelo de dispositivo personalizado de raiz ou pode escolher um modelo de dispositivo do Catálogo de Dispositivos Azure. 

## <a name="explore-simulated-devices"></a>Explore dispositivos simulados
Na IoT Central, pode criar dispositivos simulados para testar o modelo e aplicação do seu dispositivo. A aplicação de **monitorização** do consumo de água tem dois dispositivos simulados mapeados para o *medidor de fluxo* e modelos de dispositivo *selelé-ataca.* 

### <a name="to-view-the-devices"></a>Para visualizar os dispositivos:
1. Navegue para **dispositivo** a partir do painel de navegação esquerdo IoT Central. 

   ![Dispositivos](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Clique numa **Válvula Inteligente 1** 

    ![Dispositivo 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  Nos **Comandos** do Dispositivo pode ver os três comandos do dispositivo *Válvula Aberta,* *Válvula de Fecho*e Posição set *Valve* que são capacidades definidas no modelo do dispositivo *Smart Valve.* 
4. Explore o separador Propriedades do Dispositivo e o **separador Do Painel** de Instrumentos do **Dispositivo.** 

> [!NOTE]
> Note que todos os separadores foram configurados a partir do modelo de visualização do dispositivo.

### <a name="add-new-devices"></a>Adicione novos dispositivos
* Adicione novos dispositivos clicando em **+ Novo** no separador **Dispositivos.** 

## <a name="explore-and-configure-rules"></a>Explore e configure regras

No Azure IoT Central pode criar regras para monitorizar automaticamente a telemetria do dispositivo e desencadear ações quando uma ou mais condições são satisfeitas. As ações podem incluir o envio de notificações por e-mail ou o desencadeamento de uma ação do Microsoft Flow ou uma ação webhook para enviar dados para outros serviços.

A aplicação de **monitorização** do consumo de água que criou tem três regras pré-configuradas.

### <a name="to-view-rules"></a>Para ver regras:
1. Navegue para **regras** do painel de navegação esquerdo IoT Central. 

   ![Regras](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Selecione e clique em **alerta de pH elevado,** que é uma das regras pré-configuradas na aplicação.

     ![Alerta de pH elevado](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    A regra `High flow alert` está configurada para verificar a condição `Acidity (pH)` é `greater than` a `Max flow threshold`. O limiar de fluxo máximo é uma propriedade em nuvem definida no modelo do dispositivo *Smart Valve.* O valor da `Max flow threshold` é definido por instância do dispositivo. 

Agora vamos criar uma ação por e-mail.

Para adicionar uma ação à regra:

1. Selecione **+ E-mail**. 
1. Insira *o alerta de pH elevado* como o nome de **exibição** amigável para a ação.
    * Insira o endereço de e-mail associado à sua conta IoT Central **em**. 
1. Opcionalmente, insira uma nota para incluir no texto do e-mail.
1. Selecione **Feito** para completar a ação.
1. Selecione **Guardar** para guardar e ativar a nova regra. 

Dentro de poucos minutos, deve receber e-mail quando a **condição** configurada for satisfeita.

> [!NOTE]
> O pedido enviará e-mail sempre que uma condição for satisfeita. **Desative** a regra para deixar de receber e-mail da regra automatizada. 
  
Para criar uma nova regra: 
- Selecione **+Novo** nas **Regras** do painel de navegação à esquerda.

## <a name="configure-jobs"></a>Configurar empregos

Na IoT Central, os trabalhos permitem-lhe ativar atualizações de dispositivos ou propriedades na nuvem em vários dispositivos. Além das propriedades, também pode utilizar trabalhos para acionar comandos de dispositivos em vários dispositivos. A IoT Central vai automatizar o fluxo de trabalho para si. 

1. Vá a **Jobs** a partir do painel de navegação esquerdo. 
2. Clique em **+Novo** e configure um ou mais empregos. 


## <a name="customize-your-application"></a>Personalize a sua aplicação 
Como construtor, pode alterar várias definições para personalizar a experiência do utilizador na sua aplicação.

1.  Vá à **Administração > Personalize a sua aplicação.**
3. Utilize o botão **Alterar** para escolher uma imagem para carregar como logótipo da **Aplicação**.
4. Utilize o botão **'Alterar'** para escolher uma imagem de ícone do **Navegador** que aparecerá nos separadores do navegador.
5. Também pode substituir as cores padrão do **Navegador** adicionando códigos de cores hexadecimais HTML.

   ![Azure IoT Central personalize a sua aplicação](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Também pode alterar as imagens da aplicação indo para as definições de **'Administration > Application'** e **Selecione** um botão de imagem para escolher uma imagem para carregar como imagem da aplicação. 
2. Por fim, também pode alterar o **Tema** clicando em **Definições** no masthead da aplicação. 

  
## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine a sua aplicação com os seguintes passos:

1. Abra o separador Administração a partir do painel esquerdo da sua aplicação IoT Central. 
2. Selecione as definições de Aplicação e clique em Eliminar o botão na parte inferior da página. 


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre conceitos de [monitorização do consumo de água](./concepts-waterconsumptionmonitoring-architecture.md)

---
title: 'Tutorial: Criar uma app de gestão de resíduos conectada com a Azure IoT Central'
description: 'Tutorial: Aprenda a construir uma aplicação de gestão de resíduos conectada utilizando modelos de aplicação Azure IoT Central'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ff7cfb8c7aa8469111d4531da17c7cd184920f9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727571"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Tutorial: Criar uma app de gestão de resíduos conectada

Este tutorial mostra-lhe como usar a Azure IoT Central para criar uma aplicação de gestão de resíduos conectada. 

Especificamente, aprende-se a: 

> [!div class="checklist"]
> * Utilize o modelo de *gestão de resíduos Azure* IoT Central Connected para criar a sua aplicação.
> * Explore e personalize o painel de instrumentos do operador. 
> * Explore o modelo do dispositivo do lixo conectado.
> * Explore dispositivos simulados.
> * Explore e configuure regras.
> * Criar trabalhos.
> * Personalize a sua marca de aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Recomenda-se uma subscrição do Azure. Em alternativa, pode usar um teste gratuito de 7 dias. Se não tiver uma subscrição do Azure, pode criar uma na página de inscrição do [Azure](https://aka.ms/createazuresubscription).

## <a name="create-your-app-in-azure-iot-central"></a>Crie a sua app em Azure IoT Central

Nesta secção, utiliza o modelo de gestão de resíduos conectado para criar a sua aplicação no Azure IoT Central. Eis como:

1. Vá ao [Azure IoT Central.](https://aka.ms/iotcentral)

    Se tiver uma assinatura Azure, inscreva-se com as credenciais que utiliza para aceder à sua base. Caso contrário, inscreva-se utilizando uma conta Microsoft:

    ![Screenshot do Microsoft Iniciar S.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. A partir do painel esquerdo, **selecione Build**. Em seguida, selecione o separador **do Governo.** A página do governo exibe vários modelos de aplicação do governo.

    ![Screenshot da página Azure IoT Central Build.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecione o modelo de aplicação **de gestão de resíduos conectado.** Este modelo inclui um modelo de dispositivo de lixo conectado à amostra, um dispositivo simulado, um painel de instrumentos do operador e regras de monitorização pré-configuradas.    

1. **Selecione a aplicação Create**, que abre a nova caixa de diálogo de **aplicações.** Preencha as informações para os seguintes campos:
    * **Nome da aplicação**. Por predefinição, a aplicação utiliza **a gestão de resíduos conectados,** seguida de uma cadeia de ID única que a Azure IoT Central gera. Opcionalmente, pode escolher um nome de aplicação amigável. Também pode alterar o nome da candidatura mais tarde.
    * **URL**. Opcionalmente, pode escolher o URL pretendido. Pode alterar a URL mais tarde. 
    * **Plano de preços.** Se tiver uma subscrição Azure, insira o seu diretório, subscrição Azure e região nos campos apropriados da caixa de diálogo **de info Billing.** Se não tiver uma subscrição, selecione **Free** para ativar a subscrição experimental de 7 dias e preencha as informações de contacto necessárias.  

    Para obter mais informações sobre diretórios e subscrições, consulte [Quickstart - Criar uma aplicação Azure IoT Central](../core/quick-deploy-iot-central.md).

1. Na parte inferior da página, selecione **Criar**. 

    ![Screenshot de Azure IoT Central Criar nova caixa de diálogo de aplicações.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Screenshot da caixa de diálogo de info billing central de Azure IoT.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
A sua aplicação recém-criada vem com pré-configuração:
* Paineles de operador de amostra.
* Prove modelos de dispositivos de resíduos conectados pré-definados.
* Dispositivos de caixotes de lixo ligados simulados.
* Regras e empregos.
* Marca de amostras. 

É a sua candidatura, e pode modificá-la a qualquer momento. Vamos agora explorar a aplicação e fazer algumas personalizações.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Explore e personalize o painel de instrumentos do operador 

Veja o **painel de gestão de resíduos do Wide World,** que vê depois de criar a sua app.

   ![Screenshot do painel de gestão de resíduos do Wide World.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Como construtor, pode criar e personalizar vistas no painel de instrumentos para os operadores. Primeiro, vamos explorar o painel. 

>[!NOTE]
>Todos os dados apresentados no painel de instrumentos são baseados em dados simulados do dispositivo, que verá mais na secção seguinte. 

O painel é composto por diferentes azulejos:

* **Azulejo de imagem de utilidade de resíduos mundiais :** O primeiro azulejo no tablier é um azulejo de imagem de um utilitário de resíduos fictício, "Wide World Waste". Pode personalizar o azulejo e colocar na sua própria imagem, ou pode removê-lo. 

* **Azulejo de imagem do caixote do** lixo : Pode utilizar azulejos de imagem e conteúdo para criar uma representação visual do dispositivo que está a ser monitorizado, juntamente com uma descrição. 

* **Nível de enchimento KPI :** Este azulejo apresenta um valor relatado por um sensor *de nível de enchimento* num caixote do lixo. O nível de enchimento e outros sensores, como *o contador de odores* ou *o peso* num caixote do lixo, podem ser monitorizados remotamente. Um operador pode tomar medidas, como despachar um caminhão de recolha de lixo. 

* **Mapa da área de monitorização de resíduos**: Este azulejo utiliza mapas Azure, que pode configurar diretamente no Azure IoT Central. O azulejo do mapa mostra a localização do dispositivo. Tente pairar sobre o mapa e tente os controlos sobre o mapa, como zoom-in, zoom-out ou expansão.

     ![Screenshot do mapa do painel de instrumentos de gestão de resíduos conectado.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Preencha, odor, gráfico de barras de nível de peso**: Pode visualizar um ou vários tipos de dados de telemetria do dispositivo num gráfico de barras. Também pode expandir o gráfico de barras.  

  ![Screenshot do gráfico de barra de painel de painel de modelo de gestão de resíduos conectado.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Serviços de Campo**: O painel de instrumentos inclui um link para integrar-se com os Serviços de Campo Dynamics 365 a partir da sua aplicação Azure IoT Central. Por exemplo, você pode usar os Serviços de Campo para criar bilhetes para despachar serviços de recolha de lixo. 


### <a name="customize-the-dashboard"></a>Personalizar o dashboard 

Pode personalizar o painel de instrumentos selecionando o menu **Editar.** Em seguida, pode adicionar novos azulejos ou configurar os existentes. Aqui está o aspeto do painel de instrumentos no modo de edição: 

![Screenshot do painel de modelo de gestão de resíduos conectado no modo de edição.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

Também pode selecionar **+ Novo** para criar um novo dashboard e configurar do zero. Pode ter vários dashboards e alternar entre os painéis de instrumentos do menu do painel de instrumentos. 

## <a name="explore-the-device-template"></a>Explore o modelo do dispositivo

Um modelo de dispositivo no Azure IoT Central define as capacidades de um dispositivo, que pode incluir telemetria, propriedades ou comandos. Como construtor, pode definir modelos de dispositivos que representam as capacidades dos dispositivos que irá ligar. 

A aplicação de gestão de resíduos conectada vem com um modelo de amostra para um dispositivo de caixote de lixo conectado.

Para ver o modelo do dispositivo:

1. No Azure IoT Central, a partir do painel esquerdo da sua aplicação, selecione **modelos de Dispositivo .** 

    ![Screenshot mostrando a lista de modelos do dispositivo na aplicação.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. Na lista **de modelos** do dispositivo, selecione A Caixa de **Resíduos Conectada**.

1. Examine as capacidades do modelo do dispositivo. Pode ver que define sensores como **nível de enchimento,** **medidor de odor,** **peso** e **localização.**

   ![Screenshot mostrando os detalhes do modelo do dispositivo do lixo conectado.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Personalize o modelo do dispositivo

Tente personalizar o seguinte:
1. A partir do menu do modelo do dispositivo, **selecione Personalizar**.
1. Encontre o tipo de telemetria **do medidor de odor.**
1. Atualize o **nome do medidor** de **Odor** para **o nível de Odor**.
1. Tente atualizar a unidade de medição, ou definir **o valor Min** e o valor **Max**.
1. Selecione **Guardar**. 

### <a name="add-a-cloud-property"></a>Adicione uma propriedade em nuvem 

Eis como:
1. A partir do menu do modelo do dispositivo, selecione **Propriedade Cloud**.
1. Selecione **+ Adicionar Propriedade Cloud**. No Azure IoT Central, pode adicionar uma propriedade que seja relevante para o dispositivo, mas que não é esperado que seja enviada por um dispositivo. Por exemplo, uma propriedade em nuvem pode ser um limiar de alerta específico para a área de instalação, informações de bens ou informações de manutenção. 
1. Selecione **Guardar**. 
 
### <a name="views"></a>Vistas 
O modelo do dispositivo do caixote do lixo conectado vem com vistas predefinidas. Explore as vistas e atualize-as se quiser. As vistas definem como os operadores vêem os dados do dispositivo e as propriedades da nuvem de entrada. 

  ![Screenshot das vistas dos modelos de modelos de modelo de gestão de resíduos conectados.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publicar 

Se estoissar alguma alteração, lembre-se de publicar o modelo do dispositivo. 

### <a name="create-a-new-device-template"></a>Criar um novo modelo do dispositivo 

Para criar um novo modelo de dispositivo, selecione **+ Novo,** e siga os passos. Pode criar um modelo de dispositivo personalizado de raiz ou pode escolher um modelo de dispositivo a partir do catálogo de dispositivos Azure. 

## <a name="explore-simulated-devices"></a>Explore dispositivos simulados

No Azure IoT Central, pode criar dispositivos simulados para testar o modelo e aplicação do seu dispositivo. 

A aplicação de gestão de resíduos conectados tem dois dispositivos simulados associados ao modelo de dispositivo de resíduos conectado. 

### <a name="view-the-devices"></a>Ver os dispositivos

1. A partir do painel esquerdo do Azure IoT Central, selecione **Dispositivo**. 

   ![Screenshot dos dispositivos de modelo de gestão de resíduos conectados.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Selecione o dispositivo **de caixote de lixo conectado.**  

     ![Screenshot das propriedades do modelo de gestão de resíduos conectados.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Aceda ao separador **Cloud Properties.** Atualize o valor do limiar de **alerta total** do Bin de **95** para **100**. 

Explore as propriedades do **dispositivo** e os **separadores do painel de instrumentos** do dispositivo. 

> [!NOTE]
> Todos os separadores foram configurados a partir das vistas do modelo do dispositivo.

### <a name="add-new-devices"></a>Adicionar novos dispositivos

Pode adicionar novos dispositivos selecionando **+ Novo** no **separador Dispositivos.** 

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, pode criar regras para monitorizar automaticamente a telemetria do dispositivo e desencadear ações quando uma ou mais condições forem satisfeitas. As ações podem incluir o envio de notificações por e-mail, desencadear uma ação no Power Automamate ou iniciar uma ação webhook para enviar dados para outros serviços.

A aplicação de gestão de resíduos conectados tem quatro regras de amostragem.

### <a name="view-rules"></a>Ver regras
1. Do painel esquerdo do Azure IoT Central, selecione **Regras**.

   ![Screenshot das regras do modelo de gestão de resíduos conectados.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Selecione **Bin alerta completo**.

     ![Screenshot de Bin alerta completo.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. O **alerta completo do Bin** verifica a seguinte condição: O **nível de enchimento é superior ou igual ao limiar de alerta total do Bin**.

    O **limiar de alerta total do Bin** é uma propriedade em nuvem definida no modelo de dispositivo de lixo conectado. 

Agora vamos criar uma ação por e-mail.

### <a name="create-an-email-action"></a>Criar uma ação de e-mail

Na lista **de ações** da regra, pode configurar uma ação por e-mail:
1. Selecione **+ E-mail**. 
1. Para **o nome do visor,** introduza o alerta de **pH alto**.
1. Para , insira o endereço de e-mail associado à sua conta Azure IoT Central. 
1. Opcionalmente, insira uma nota para incluir no texto do e-mail.
1. Selecione   >  **'Guardar' Feito**. 

Receberá agora um e-mail quando a condição configurada for satisfeita.

>[!NOTE]
>A aplicação envia e-mail cada vez que uma condição é satisfeita. Desative a regra para deixar de receber e-mail da regra automatizada. 
  
Para criar uma nova regra, a partir do painel de **regras** à esquerda, selecione **+New**.

## <a name="configure-jobs"></a>Configurar tarefas

No Azure IoT Central, os trabalhos permitem-lhe ativar atualizações de propriedades de dispositivos ou nuvem em vários dispositivos. Também pode utilizar trabalhos para acionar comandos de dispositivos em vários dispositivos. A Azure IoT Central automatiza o fluxo de trabalho para si. 

1. Do painel esquerdo da Azure IoT Central, selecione **Jobs**. 
1. Selecione **+Novo**, e configuure um ou mais empregos. 

## <a name="customize-your-application"></a>Personalize a sua aplicação 

Como construtor, pode alterar várias definições para personalizar a experiência do utilizador na sua aplicação.

### <a name="change-the-application-theme"></a>Alterar o tema da aplicação

Eis como:
1. Vá à **Administração**  >  **Personalize a sua aplicação.**
1. Selecione **Alterar** para escolher uma imagem para carregar para o **logótipo da Aplicação**.
1. Selecione **Alterar** para escolher uma imagem para carregar para o **ícone do Navegador** (uma imagem que aparecerá nos separadores do navegador).
1. Também pode substituir as cores do navegador predefinido adicionando códigos de cores hexadémicas HTML. Utilize os campos **cabeçalho** e **sotaque** para este fim.

   ![Screenshot do modelo de gestão de wast conectado personaliza a sua aplicação.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Também pode alterar imagens de aplicações. Selecione   >  **definições de aplicação de** administração  >  **Selecione imagem** para escolher uma imagem para carregar como imagem de aplicação.
1. Finalmente, também pode alterar o tema selecionando **Definições** no masthead da aplicação.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a sua aplicação com os seguintes passos:

1. A partir do painel esquerdo da sua aplicação Azure IoT Central, selecione **Administração**.
1. Selecione **as definições de aplicação**  >  **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conceitos de gestão de resíduos conectados](./concepts-connectedwastemanagement-architecture.md)

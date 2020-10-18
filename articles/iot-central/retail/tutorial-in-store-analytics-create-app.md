---
title: Tutorial - Criar uma aplicação de análise na loja no Azure IoT Central
description: Este tutorial mostra como criar uma aplicação de retalho de análise na loja na IoT Central. Vai criá-lo, personalizá-lo e adicionar dispositivos de sensor.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: a15ccd6d67b4819ba5dd6233a2a001015037c404
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165677"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Tutorial: Criar uma aplicação de análise na loja no Azure IoT Central



O tutorial mostra aos construtores de soluções como criar uma aplicação de análise Azure IoT Central na loja. O pedido de amostra é para uma loja de retalho. É uma solução para a necessidade de monitorizar e adaptar-se às condições de ocupação e ambiente.

A aplicação de amostra que constrói inclui três dispositivos reais: um Portal Rigado Cascade 500 e dois sensores RuuviTag. O tutorial também mostra como usar o sensor de ocupação simulado incluído no modelo de aplicação para fins de teste. O portal Rigado C500 serve como o centro de comunicação da sua aplicação. Comunica com sensores na sua loja e gere as suas ligações com a nuvem. O RuuviTag é um sensor ambiental que fornece telemetria, incluindo temperatura, humidade e pressão. O sensor de ocupação simulado fornece uma forma de rastrear o movimento e a presença nas áreas de check-out de uma loja. 

Este tutorial inclui direções para ligar os dispositivos Rigado e RuuviTag à sua aplicação. Se tiver outro gateway e sensores, ainda pode seguir os passos para construir a sua aplicação. O tutorial também mostra como criar sensores RuuviTag simulados. Os sensores simulados permitem-lhe construir a aplicação se não tiver dispositivos reais. 

Desenvolve a solução de check-out e monitorização de condições em três partes:

* Criar a aplicação e ligar dispositivos para monitorizar as condições
* Personalize o painel de instrumentos para permitir aos operadores monitorizarem e gerirem dispositivos
* Configure a exportação de dados para permitir aos gestores de lojas executar analítica e visualizar insights

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Use o Azure IoT Central **In-store analytics - modelo de check-out** para criar uma aplicação de loja de retalho
> * Personalize as definições de aplicação
> * Criar e personalizar modelos de dispositivos IoT
> * Conecte dispositivos à sua aplicação
> * Adicionar regras e ações para monitorizar as condições

## <a name="prerequisites"></a>Pré-requisitos

Para completar esta série tutorial, precisa:
* Recomenda-se uma subscrição do Azure. Pode opcionalmente usar um teste gratuito de 7 dias. Se não tiver uma subscrição do Azure, pode criar uma na página de inscrição do [Azure](https://aka.ms/createazuresubscription).
* Acesso a um dispositivo de gateway e a dois sensores ambientais (pode utilizar opcionalmente dispositivos simulados como descrito no tutorial)
* Modelos de dispositivo para os dispositivos que utiliza (os modelos são fornecidos para todos os dispositivos utilizados no tutorial)

## <a name="create-an-application"></a>Criar uma aplicação
Nesta secção, você cria uma nova aplicação Azure IoT Central a partir de um modelo. Você usará esta aplicação ao longo da série tutorial para construir uma solução completa.

Para criar uma nova aplicação do Azure IoT Central:

1. Navegue para o site do [gestor de aplicações Azure IoT Central.](https://aka.ms/iotcentral)

1. Se tiver uma subscrição Azure, inscreva-se com as credenciais que utiliza para aceder a ela, caso contrário, inscreva-se através de uma conta Microsoft:

    ![Introduza a sua conta da organização](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação).

1. Selecione **Varejo**.  A página de varejo apresenta vários modelos de aplicações de varejo.

Para criar uma nova aplicação de check-out de análise na loja:  

1. Selecione o **modelo de aplicação de check-out in-store.** Este modelo inclui modelos de dispositivo para todos os dispositivos utilizados no tutorial, exceto para os sensores RuuviTag. O modelo também fornece um painel de instrumentos do operador para monitorizar as condições de checkout e ambiente e o estado do dispositivo. 

1. Opcionalmente, escolha um nome de **aplicação**amigável. Esta aplicação baseia-se numa loja fictícia chamada Contoso. O tutorial usa o **nome de Aplicação** *Contoso checkout*. O modelo de aplicação baseia-se na empresa fictícia Northwind. Neste tutorial, você usa Contoso para aprender a personalizar a aplicação.

    > [!NOTE]
    > Se utilizar um nome de **aplicação**amigável, ainda terá de utilizar um valor único para o **URL**da aplicação.

1. Se tiver uma subscrição Azure, insira o seu *Diretório, subscrição Azure e Região*. Se não tiver uma subscrição, pode ativar um **teste gratuito de 7 dias** e completar as informações de contacto necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../core/quick-deploy-iot-central.md).

1. Selecione **Criar**.

    ![Página da Aplicação do Azure IoT Central](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Azure IoT Central Criar Informações de faturação de aplicações](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Personalizar configurações de aplicações

Como construtor, pode alterar várias definições para personalizar a experiência do utilizador na sua aplicação. Nesta secção, irá selecionar um tema de aplicação predefinido. Opcionalmente, você vai aprender a criar um tema personalizado, e atualizar a imagem da aplicação. Um tema personalizado permite-lhe definir as cores do navegador da aplicação, o ícone do navegador e o logotipo da aplicação que aparece no masthead.

Para selecionar um tema de aplicação predefinido:

1. Selecione **Definições** no cabeça de cartaz.

    ![Definições de aplicação Azure IoT Central](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Selecione um novo **tema**.

3. Selecione **Guardar**.

Em vez de usar um tema predefinido, pode criar um tema personalizado. Se pretender utilizar um conjunto de imagens de amostra para personalizar a aplicação e completar o tutorial, descarregue as imagens da [amostra de Contoso](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Para criar um tema personalizado:

1. Expanda o painel esquerdo, se não já expandido.

    ![Painel esquerdo Azure IoT Central](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Selecione **Administração > Personalize a sua aplicação.**

1. Utilize o botão **Alterar** para escolher uma imagem para carregar como **o logótipo da Aplicação**. Opcionalmente, especifique um valor para **o texto alt logo.** 

1. Utilize o botão **Alterar** para escolher uma imagem **de ícone do Navegador** que aparecerá nos separadores do navegador.

1. Opcionalmente, substitua as cores predefinidos do Navegador adicionando **códigos** de cores hexadémicas HTML. Para o **Cabeçalho**, adicione *#008575*.  Para o **Sotaque**, adicione *#A1F3EA*. 

1. Selecione **Guardar**. 

    ![Logotipo personalizado Azure IoT Central](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    Depois de guardar, a aplicação atualiza as cores do navegador, o logótipo no masthead e o ícone do navegador. 

    ![Definições de aplicações atualizadas Azure IoT Central](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

Para atualizar a imagem da aplicação:

1. Selecione **definições de aplicação > administração**.

1. Utilize o botão **de imagem Select** para escolher uma imagem para carregar como imagem de aplicação. Esta imagem aparece no azulejo da aplicação na página **My Apps** do gestor de aplicações IoT Central.

1. Selecione **Guardar**.

1. Opcionalmente, navegue para a vista **My Apps** no site do gestor [de aplicações Azure IoT Central.](https://aka.ms/iotcentral) O azulejo da aplicação exibe a imagem atualizada da aplicação.

    ![Azure IoT Central personaliza imagem de aplicação](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Criar modelos de dispositivo
Como construtor, pode criar modelos de dispositivos que permitem a si e aos operadores de aplicações configurar e gerir dispositivos. Você cria um modelo construindo um personalizado, importando um arquivo de modelo existente, ou importando um modelo do catálogo de dispositivos Azure IoT. Depois de criar e personalizar um modelo de dispositivo, use-o para ligar dispositivos reais à sua aplicação. Opcionalmente, utilize um modelo de dispositivo para gerar dispositivos simulados para testes.

O **modelo de aplicação de check-out na loja** tem modelos de dispositivo para vários dispositivos.  Existem modelos de dispositivo para dois dos três dispositivos que utiliza na aplicação. O modelo do dispositivo RuuviTag não está incluído na análise in-store - modelo de aplicação **de check-out.** Nesta secção, adicione um modelo de dispositivo para sensores RuuviTag à sua aplicação.

Para adicionar um modelo de dispositivo RuuviTag à sua aplicação:

1. Selecione **os modelos do dispositivo** no painel esquerdo.

1. Selecione **+ Novo** para criar um novo modelo de dispositivo.

1. Encontre e selecione o modelo do dispositivo do sensor **RuuviTag** no catálogo de dispositivos Azure IoT. 

1. Selecione **Seguinte: Customize** (Seguinte: Personalizar).

    ![Screenshot que realça o seguinte: Personalizar o botão.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Selecione **Criar**. A aplicação adiciona o modelo do dispositivo RuuviTag.

1. Selecione **os modelos** do dispositivo no painel esquerdo. A página apresenta todos os modelos de dispositivo incluídos no modelo de aplicação e o modelo de dispositivo RuuviTag que acaba de adicionar.

    ![Modelo de dispositivo de sensor RuuviTag central de Azure IoT](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Personalizar modelos de dispositivos
Pode personalizar os modelos do dispositivo na sua aplicação de três formas. Em primeiro lugar, personaliza as interfaces incorporadas nativas nos seus dispositivos alterando as capacidades do dispositivo. Por exemplo, com um sensor de temperatura, pode alterar detalhes como o nome de visualização da interface de temperatura, o tipo de dados, as unidades de medição e os intervalos de funcionamento mínimo e máximo. 

Em segundo lugar, personalize os seus modelos de dispositivo adicionando propriedades na nuvem. As propriedades da nuvem não fazem parte das capacidades do dispositivo incorporado. As propriedades em nuvem são dados personalizados que a sua aplicação Azure IoT Central cria, armazena e associa aos seus dispositivos. Um exemplo de uma propriedade na nuvem pode ser um valor calculado, ou metadados, como uma localização que pretende associar a um conjunto de dispositivos. 

Em terceiro lugar, personalize os modelos de dispositivos construindo vistas personalizadas. As vistas fornecem uma forma de os operadores visualizarem a telemetria e metadados para os seus dispositivos, tais como métricas de dispositivos e saúde.

Aqui, utiliza os dois primeiros métodos para personalizar o modelo do dispositivo para os seus sensores RuuviTag. Para obter informações sobre a criação de vistas para os seus sensores, consulte o [Dispositivo De adicionar um dispositivo simulado à sua aplicação IoT Central.](../core/quick-create-simulated-device.md)

Para personalizar as interfaces incorporadas do modelo do dispositivo RuuviTag:

1. Selecione **os modelos do dispositivo** no painel esquerdo. 

1. Selecione o modelo para sensores RuuviTag. 

1. Esconda o painel esquerdo. A visão sumária do modelo exibe as capacidades do dispositivo.

    ![Azure IoT Central RuuviTag visualização do modelo do modelo](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. **Selecione Personalizar** no menu do modelo do dispositivo RuuviTag. 

1. Percorra a lista de capacidades e encontre o `humidity` tipo de telemetria. É o item da linha com o valor editável **do nome do visor** da *humidade.*

Nos passos seguintes, personaliza o `humidity` tipo de telemetria para os sensores RuuviTag. Opcionalmente, personalize alguns dos outros tipos de telemetria.

Para o `humidity` tipo de telemetria, es faça as seguintes alterações:

1. Selecione o controlo **Expandir** para expandir os detalhes do esquema para a linha.

1. Atualize o valor do Nome do **Visualização** da *humidade* para um valor personalizado, como *humidade relativa.*

1. Altere a opção **Tipo Semântico** de *Nenhum* para *a Humidade*.  Opcionalmente, desaponte os valores de esquema para o tipo de telemetria de humidade na vista de esquema expandido. As definições de esquema permitem criar requisitos de validação detalhados para os dados que os seus sensores rastreiam. Por exemplo, pode definir valores de gama de funcionamento mínimos e máximos para uma determinada interface.

1. Selecione **Guardar** para guardar as alterações.

    ![Screenshot que mostra o ecrã Personalizar e realça o botão Guardar.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Para adicionar uma propriedade em nuvem a um modelo de dispositivo na sua aplicação:

1. Selecione **Propriedades cloud** no menu do modelo do dispositivo RuuviTag.

1. **Selecione Adicionar Propriedade Cloud**. 

Especifique os seguintes valores para criar uma propriedade personalizada para armazenar a localização de cada dispositivo:

1. Introduza o valor *Localização* para o **Nome do Visor**. Este valor é automaticamente copiado para o campo **Nome,** que é um nome amigável para a propriedade. Pode usar o valor copiado ou alterá-lo.

1. Selecione *String* in the **Schema** dropdown. Um tipo de corda permite-lhe associar uma cadeia de nome de localização a qualquer dispositivo baseado no modelo. Por exemplo, pode associar uma área numa loja a cada dispositivo. Opcionalmente, pode definir o **Tipo Semântico** da sua propriedade para *Localização,* e isto define automaticamente o **Schema** para *Geopoint*. Permite-lhe associar coordenadas de GPS a um dispositivo. 

1. Definir **comprimento mínimo** para *2*. 

1. Defina **o Espaço Branco** de Corte para **On**.

1. **Selecione Guardar** para guardar a sua propriedade personalizada na nuvem.

    ![Azure IoT Central RuuviTag design de design de dispositivo](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Selecione **Publicar**. 

    A publicação de um modelo de dispositivo torna-o visível para os operadores de aplicações. Depois de publicar um modelo, use-o para gerar dispositivos simulados para testes ou para ligar dispositivos reais à sua aplicação. Se já tiver dispositivos ligados à sua aplicação, a publicação de um modelo personalizado empurra as alterações aos dispositivos.

## <a name="add-devices"></a>Adicionar dispositivos
Depois de ter criado e personalizados modelos de dispositivos, é hora de adicionar dispositivos. 

Para este tutorial, utilize o seguinte conjunto de dispositivos reais e simulados para construir a aplicação:
- Um verdadeiro portal Rigado C500
- Dois verdadeiros sensores RuuviTag
- Um sensor **de ocupação** simulado. O sensor simulado está incluído no modelo de aplicação, pelo que não precisa de o criar. 

> [!NOTE]
> Se não tiver dispositivos reais, ainda pode completar este tutorial criando sensores RuuviTag simulados. As seguintes direções incluem passos para criar um RuuviTag simulado. Não precisas de criar um portal simulado.

Complete os passos nos dois artigos seguintes para ligar um verdadeiro gateway rigado e sensores RuuviTag. Depois de terminar, volte a este tutorial. Como já criou modelos de dispositivo neste tutorial, não precisa de os criar novamente no seguinte conjunto de direções.

- Para ligar uma porta de entrada rigado, consulte [uma Cascata Rigado 500 à sua aplicação Azure IoT Central](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- Para ligar os sensores RuuviTag, consulte [ligar um sensor RuuviTag à sua aplicação Azure IoT Central](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Também pode utilizar estas instruções para criar dois sensores simulados, se necessário.

## <a name="add-rules-and-actions"></a>Adicionar regras e ações
Como parte da utilização de sensores na sua aplicação Azure IoT Central para monitorizar as condições, pode criar regras para executar ações quando determinadas condições são satisfeitas. Uma regra está associada a um modelo de dispositivo e a um ou mais dispositivos, e contém condições que devem ser satisfeitas com base em telemetria ou eventos do dispositivo. Uma regra também tem uma ou mais ações associadas. As ações podem incluir o envio de notificações por e-mail ou desencadear uma ação webhook para enviar dados para outros serviços. O **modelo de aplicação de check-out in-store** inclui algumas regras predefinidas para os dispositivos da aplicação.

Nesta secção, cria-se uma nova regra que verifica o nível máximo de humidade relativa com base na telemetria do sensor RuuviTag. Adicione uma ação à regra para que, se a humidade exceder o máximo, a aplicação envie e-mail. 

Para criar uma regra: 

1. Expanda o painel esquerdo.

1. Selecione **Regras**.

1. Selecione **+ Novo**.

1. Insira *o nível de humidade* como o nome da regra. 

1. Escolha o modelo de dispositivo RuuviTag em **Âmbitos**. A regra que define aplicar-se-á a todos os sensores com base nesse modelo. Opcionalmente, pode criar um filtro que aplicaria a regra apenas a um subconjunto definido dos sensores. 

1. Escolha `Relative humidity` como **Telemetria.** É a capacidade do dispositivo que personalizou num passo anterior.

1. Escolha `Is greater than` como **Operador.** 

1. Introduza um nível típico de humidade interior de gama superior para o seu ambiente como o **Valor**. Por exemplo, insira *65*. Definiu uma condição para a sua regra que ocorre quando a humidade relativa em qualquer sensor ruuviTag real ou simulado excede este valor. Pode ser necessário ajustar o valor para cima ou para baixo, dependendo da gama normal de humidade no seu ambiente.  

   ![Azure IoT Central adicionar condições de regra](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Para adicionar uma ação à regra:

1. Selecione **+ E-mail**. 

1. Introduza *a notificação de humidade elevada* como o nome de **exibição** amigável para a ação. 

1. Introduza o endereço de e-mail associado à sua conta em **To**. Se utilizar um e-mail diferente, o endereço que utiliza deve ser para um utilizador que tenha sido adicionado à aplicação. O utilizador também precisa de iniciar sins ins e out pelo menos uma vez.

1. Opcionalmente, insira uma nota para incluir no texto do e-mail.

1. Selecione **Feito** para completar a ação.

   ![Azure IoT Central adiciona ações às regras](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. **Selecione Guardar** para guardar e ativar a nova regra. 

    Dentro de poucos minutos, a conta de e-mail especificada deve começar a receber e-mails. A aplicação envia e-mail sempre que um sensor indica que o nível de humidade excedeu o valor da sua condição.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

* Use o Azure IoT Central **In-store analytics - modelo de check-out** para criar uma aplicação de loja de retalho
* Personalize as definições de aplicação
* Criar e personalizar modelos de dispositivos IoT
* Conecte dispositivos à sua aplicação
* Adicionar regras e ações para monitorizar as condições

Agora que criou uma aplicação de monitorização da condição central Azure IoT, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Personalize o painel de instrumentos do operador](./tutorial-in-store-analytics-customize-dashboard.md)

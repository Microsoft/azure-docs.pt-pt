---
title: Tutorial - Criar uma aplicação de análise na loja na Azure IoT Central
description: Este tutorial mostra como criar uma aplicação de retalho de análise na loja na IoT Central. Irá criá-lo, personalizá-lo e adicionar dispositivos de sensores.
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
ms.openlocfilehash: 6fa4521cb50ecfc37004cbf9cff08644dc53f000
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990654"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Tutorial: Criar uma aplicação de análise na loja na Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

O tutorial mostra os construtores de soluções como criar uma aplicação de análise Azure IoT Central na loja. O pedido de amostra é para uma loja de retalho. É uma solução para a necessidade do negócio comum de monitorizar e adaptar-se às condições de ocupação e ambientais.

A aplicação de amostra que você constrói inclui três dispositivos reais: um gateway Rigado Cascade 500 e dois sensores RuuviTag. O tutorial também mostra como usar o sensor de ocupação simulado incluído no modelo de aplicação para fins de teste. O gateway Rigado C500 serve como centro de comunicação na sua aplicação. Comunica com sensores na sua loja e gere as suas ligações com a nuvem. O RuuviTag é um sensor ambiental que fornece telemetria, incluindo temperatura, humidade e pressão. O sensor de ocupação simulado fornece uma forma de rastrear o movimento e a presença nas áreas de check-out de uma loja. 

Este tutorial inclui instruções para ligar os dispositivos Rigado e RuuviTag à sua aplicação. Se tiver outra porta de entrada e sensores, ainda pode seguir os passos para construir a sua aplicação. O tutorial também mostra como criar sensores RuuviTag simulados. Os sensores simulados permitem-lhe construir a aplicação se não tiver dispositivos reais. 

Desenvolve a solução de monitorização de check-out e condição em três partes:

* Criar a aplicação e ligar dispositivos para monitorizar as condições
* Personalize o dashboard para permitir que os operadores monitorizem e gerem dispositivos
* Configure a exportação de dados para permitir aos gestores de lojas executar análises e visualizar insights

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Use a análise central da loja Azure IoT - modelo de **check-out** para criar uma aplicação de loja de retalho
> * Personalize as definições de aplicação
> * Criar e personalizar modelos de dispositivos IoT
> * Ligar dispositivos à sua aplicação
> * Adicionar regras e ações para monitorizar as condições

## <a name="prerequisites"></a>Pré-requisitos

Para completar esta série tutorial, precisa de:
* Recomenda-se uma subscrição Azure. Você pode opcionalmente usar um teste gratuito de 7 dias. Se não tiver uma subscrição Azure, pode criar uma na [página de inscrição](https://aka.ms/createazuresubscription)do Azure .
* Acesso a um dispositivo de gateway e dois sensores ambientais (pode utilizar opcionalmente dispositivos simulados conforme descrito no tutorial)
* Modelos de dispositivo para os dispositivos que utiliza (os modelos são fornecidos para todos os dispositivos utilizados no tutorial)

## <a name="create-an-application"></a>Criar uma aplicação
Nesta secção, você cria uma nova aplicação Azure IoT Central a partir de um modelo. Você usará esta aplicação ao longo da série tutorial para construir uma solução completa.

Para criar uma nova aplicação do Azure IoT Central:

1. Navegue para o site do gestor de [aplicações Azure IoT Central.](https://aka.ms/iotcentral)

1. Se tiver uma subscrição Azure, inscreva-se com as credenciais que utiliza para aceder ao mesmo, caso contrário, inscreva-se através de uma conta Microsoft:

    ![Introduza a sua conta da organização](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação).

1. Selecione **Retail**.  A página de varejo apresenta vários modelos de aplicação de retalho.

Para criar uma nova aplicação de check-out de análise na loja que utiliza funcionalidades de pré-visualização:  

1. Selecione a análise da loja - modelo de aplicação de **check-out.** Este modelo inclui modelos de dispositivopara todos os dispositivos utilizados no tutorial, exceto para os sensores RuuviTag. O modelo também fornece um painel de instrumentos do operador para monitorizar as condições de check-out e ambientais, e o estado do dispositivo. 

1. Opcionalmente, escolha um nome de **aplicação**amigável. Esta aplicação baseia-se numa loja fictícia chamada Contoso. O tutorial utiliza o **nome de inscrição** *Contoso checkout*. O modelo de aplicação baseia-se na empresa fictícia Northwind. Neste tutorial, você usa Contoso para aprender a personalizar a aplicação.

    > [!NOTE]
    > Se utilizar um nome de **aplicação**amigável, ainda deve utilizar um valor único para o **URL**da aplicação .

1. Se tiver uma assinatura Azure, insira o seu *Diretório, subscrição Azure e Região.* Se não tiver uma subscrição, pode ativar um **teste gratuito de 7** dias e completar as informações de contacto necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../preview/quick-deploy-iot-central.md).

1. Selecione **Criar**.

    ![Página da Aplicação do Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/preview-application-template.png)

    ![Azure IoT Central Criar informação sobre faturação de aplicações](./media/tutorial-in-store-analytics-create-app-pnp/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Personalizar as definições de aplicação
Como construtor, pode alterar várias definições para personalizar a experiência do utilizador na sua aplicação. Nesta secção, irá selecionar um tema de aplicação predefinido. Opcionalmente, você vai aprender a criar um tema personalizado, e atualizar a imagem da aplicação. Um tema personalizado permite definir as cores do navegador de aplicação, o ícone do navegador e o logótipo da aplicação que aparece no masthead.

Para selecionar um tema de aplicação predefinido:

1. Selecione **Definições** no masthead.

    ![Definições de aplicação Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/settings-icon.png)

2. Selecione um novo **tema.**

3. Selecione **Guardar**.

Em vez de usar um tema predefinido, pode criar um tema personalizado. Se quiser utilizar um conjunto de imagens de amostra para personalizar a aplicação e completar o tutorial, descarregue as [imagens da amostra Contoso.](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail)

Para criar um tema personalizado:

1. Expanda o painel esquerdo, se não estiver já expandido.

    ![Vidraça esquerda Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/dashboard-expand.png)

1. Selecione **Administration > Personalize a sua aplicação**.

1. Utilize o botão **Alterar** para escolher uma imagem para carregar como logótipo da **Aplicação**. Opcionalmente, especifique um valor para o **logo alt texto**. 

1. Utilize o botão **'Alterar'** para escolher uma imagem de ícone do **Navegador** que aparecerá nos separadores do navegador.

1. Opcionalmente, substitua as cores padrão do **Navegador** adicionando códigos de cores hexadecimais HTML. Para o **Cabeçalho,** adicione *#008575*.  Para o **Sotaque,** adicione *#A1F3EA.* 

1. Selecione **Guardar**. 

    ![Logotipo personalizado Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/select-application-logo.png)

    Depois de guardar, a aplicação atualiza as cores do navegador, o logótipo no masthead e o ícone do navegador. 

    ![Definições de aplicações atualizadas Do Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/saved-application-settings.png)

Para atualizar a imagem da aplicação:

1. Selecione Definições de **Administração e aplicação .**

1. Utilize o botão **de imagem Select** para escolher uma imagem para carregar como imagem de aplicação. Esta imagem aparece no azulejo da aplicação na página **My Apps** do gestor de aplicações IoT Central.

1. Selecione **Guardar**.

1. Opcionalmente, navegue para a vista **My Apps** no site do gestor de [aplicações Azure IoT Central.](https://aka.ms/iotcentral) O azulejo da aplicação exibe a imagem de aplicação atualizada.

    ![Azure IoT Central personaliza imagem de aplicação](./media/tutorial-in-store-analytics-create-app-pnp/customize-application-image.png)

## <a name="create-device-templates"></a>Criar modelos de dispositivo
Como construtor, pode criar modelos de dispositivoque lhe permitam configurar e gerir dispositivos. Você cria um modelo construindo um personalizado, importando um ficheiro de modelo existente, ou importando um modelo do catálogo de dispositivos Azure IoT. Depois de criar e personalizar um modelo de dispositivo, use-o para ligar dispositivos reais à sua aplicação. Opcionalmente, utilize um modelo de dispositivo para gerar dispositivos simulados para testes.

O **analítico in-store -** o modelo de aplicação de check-out tem modelos de dispositivo para vários dispositivos.  Existem modelos de dispositivo para dois dos três dispositivos que utiliza na aplicação. O modelo do dispositivo RuuviTag não está incluído na análise da loja - modelo de aplicação de **check-out.** Nesta secção, adicione um modelo de dispositivo para sensores RuuviTag à sua aplicação.

Para adicionar um modelo de dispositivo RuuviTag à sua aplicação:

1. Selecione **modelos** de dispositivo no painel esquerdo.

1. Selecione **+ Novo** para criar um novo modelo de dispositivo.

1. Encontre e selecione o modelo do dispositivo sensor **RuuviTag** no catálogo do dispositivo Azure IoT. 

1. Selecione **Seguinte: Personalizar**.

    ![Modelo de dispositivo de sensor Central RuuviTag Azure IoT](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template.png)

1. Selecione **Criar**. A aplicação adiciona o modelo do dispositivo RuuviTag.

1. Selecione **os modelos** do dispositivo no painel esquerdo. A página exibe todos os modelos de dispositivo incluídos no modelo de aplicação e o modelo do dispositivo RuuviTag que acabou de adicionar.

    ![Modelo de dispositivo de sensor Central RuuviTag Azure IoT](./media/tutorial-in-store-analytics-create-app-pnp/device-templates-list.png)

## <a name="customize-device-templates"></a>Personalizar modelos de dispositivo
Pode personalizar os modelos do dispositivo na sua aplicação de três maneiras. Em primeiro lugar, personaliza as interfaces nativas incorporadas nos seus dispositivos, alterando as capacidades do dispositivo. Por exemplo, com um sensor de temperatura, pode alterar detalhes como o nome de visualização da interface de temperatura, o tipo de dados, as unidades de medição e intervalos de funcionamento mínimo e máximo. 

Em segundo lugar, personalize os modelos do seu dispositivo adicionando propriedades em nuvem. As propriedades da nuvem não fazem parte das capacidades do dispositivo incorporado. As propriedades da cloud são dados personalizados que a sua aplicação Azure IoT Central cria, armazena e associa com os seus dispositivos. Um exemplo de uma propriedade em nuvem pode ser um valor calculado, ou metadados, como uma localização que você quer associar com um conjunto de dispositivos. 

Terceiro, personalize os modelos do dispositivo construindo vistas personalizadas. As vistas fornecem uma forma de os operadores visualizarem a telemetria e os metadados para os seus dispositivos, tais como métricas do dispositivo e saúde.

Aqui, você usa os dois primeiros métodos para personalizar o modelo do dispositivo para os seus sensores RuuviTag. Para obter informações sobre a criação de vistas para os seus sensores, consulte o [Adicionar um dispositivo simulado à sua aplicação IoT Central.](../preview/quick-create-pnp-device.md)

Para personalizar as interfaces incorporadas do modelo do dispositivo RuuviTag:

1. Selecione **modelos** de dispositivo no painel esquerdo. 

1. Selecione o modelo para sensores RuuviTag. 

1. Esconda o painel esquerdo. A visão sumária do modelo mostra as capacidades do dispositivo.

    ![Vista sumária do modelo do dispositivo Central RuuviTag azure IoT](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-summary-view.png)

1. Selecione **Personalizar** no menu de modelo de dispositivo RuuviTag. 

1. Percorra a lista de capacidades e encontre o `humidity` tipo de telemetria. É o item da linha com o valor de nome de **visualização** editável de *humidade*.

Nos passos seguintes, personaliza o tipo de telemetria `humidity` para os sensores RuuviTag. Opcionalmente, personalize alguns dos outros tipos de telemetria.

Para o `humidity` tipo de telemetria, efaça as seguintes alterações:

1. Selecione o controlo **Expandir** para expandir os detalhes do esquema para a linha.

1. Atualize o valor do Nome do **Visor** da *humidade* para um valor personalizado, como *humidade relativa*.

1. Altere a opção **Tipo Semântico** de *Nenhuma* a *Humidade*.  Opcionalmente, detete os valores do esquema para o tipo de telemetria de humidade na vista de esquema expandido. As definições de esquema permitem criar requisitos de validação detalhados para os dados que os seus sensores rastreiam. Por exemplo, pode definir valores mínimos e máximos de alcance de funcionamento para uma determinada interface.

1. Selecione **guardar** para guardar as alterações.

    ![Personalização do modelo de modelo do dispositivo Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-customize.png)

Para adicionar uma propriedade na nuvem a um modelo de dispositivo na sua aplicação:

1. Selecione **Cloud Properties** no menu de modelo de dispositivo RuuviTag.

1. **Selecione Adicionar Cloud Property**. 

Especifique os seguintes valores para criar uma propriedade personalizada para armazenar a localização de cada dispositivo:

1. Introduza o *local* de valor para o nome do **visor**. Este valor é automaticamente copiado para o campo **Nome,** que é um nome amigável para a propriedade. Pode usar o valor copiado ou mudá-lo.

1. Selecione *String* no **dropdown de Schema.** Um tipo de corda permite-lhe associar uma cadeia de nome de localização a qualquer dispositivo baseado no modelo. Por exemplo, pode associar uma área numa loja a cada dispositivo. Opcionalmente, pode definir o **tipo semantico** da sua propriedade para *Localização*, e isso automaticamente define o **Schema** para *Geopoint*. Isto permite-lhe associar coordenadas de GPS a um dispositivo. 

1. Definir **comprimento mínimo** para *2*. 

1. Definir espaço branco para **o set** **whitespace** para on .

1. Selecione **Guardar** para salvar a sua propriedade em nuvem personalizada.

    ![Personalização do modelo de modelo do dispositivo Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-cloud-property.png)

1. Selecione **Publicar**. 

    A publicação de um modelo de dispositivo torna-o visível para os operadores de aplicação. Depois de publicar um modelo, use-o para gerar dispositivos simulados para testes ou para ligar dispositivos reais à sua aplicação. Se já tem dispositivos ligados à sua aplicação, publicar um modelo personalizado empurra as alterações para os dispositivos.

## <a name="add-devices"></a>Adicionar dispositivos
Depois de ter criado e personalizado modelos de dispositivos, é hora de adicionar dispositivos. 

Para este tutorial, utiliza o seguinte conjunto de dispositivos reais e simulados para construir a aplicação:
- Um verdadeiro gateway Rigado C500
- Dois verdadeiros sensores RuuviTag
- Um sensor de **ocupação** simulado. O sensor simulado está incluído no modelo de aplicação, por isso não precisa criá-lo. 

> [!NOTE]
> Se não tiver dispositivos reais, ainda pode completar este tutorial criando sensores RuuviTag simulados. As seguintes direções incluem passos para criar um RuuviTag simulado. Não precisa criar um portal simulado.

Complete os passos nos dois artigos seguintes para ligar um verdadeiro gateway rigado e sensores RuuviTag. Depois de terminar, volte para este tutorial. Como já criou modelos de dispositivoneste tutorial, não precisa criá-los novamente no seguinte conjunto de direções.

- Para ligar um gateway Rigado, consulte [Ligue uma Cascata Rigado 500 à sua aplicação Central Azure IoT](../preview/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- Para ligar os sensores RuuviTag, consulte [Connect um sensor RuuviTag à sua aplicação Central Azure IoT](../preview/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Também pode usar estas direções para criar dois sensores simulados, se necessário.

## <a name="add-rules-and-actions"></a>Adicionar regras e ações
Como parte da utilização de sensores na sua aplicação Azure IoT Central para monitorizar as condições, pode criar regras para executar ações quando determinadas condições são satisfeitas. Uma regra está associada a um modelo de dispositivo e a um ou mais dispositivos, e contém condições que devem ser satisfeitas com base na telemetria do dispositivo ou eventos. Uma regra também tem uma ou mais ações associadas. As ações podem incluir o envio de notificações por e-mail ou o desencadeamento de uma ação de webhook para enviar dados para outros serviços. O modelo de aplicação **in-store - o** modelo de aplicação de check-out inclui algumas regras predefinidas para os dispositivos na aplicação.

Nesta secção, cria-se uma nova regra que verifica o nível máximo de humidade relativa com base na telemetria do sensor RuuviTag. Adicione uma ação à regra para que, se a humidade exceder o máximo, a aplicação envie e-mail. 

Para criar uma regra: 

1. Expanda o painel esquerdo.

1. Selecione **regras**.

1. Selecione **+ Novo**.

1. Introduza o *nível de humidade* como o nome da regra. 

1. Escolha o modelo do dispositivo RuuviTag em **Scopes**. A regra que define aplicar-se-á a todos os sensores com base nesse modelo. Opcionalmente, poderia criar um filtro que aplicasse a regra apenas a um subconjunto definido dos sensores. 

1. Escolha `Relative humidity` como **Telemetria.** Esta é a capacidade do dispositivo que personalizou num passo anterior.

1. Escolha `Is greater than` como **Operador**. 

1. Introduza um nível típico de humidade interior superior para o seu ambiente como o **Valor**. Por exemplo, insira *65*. Definiu uma condição para a sua regra que ocorre quando a humidade relativa em qualquer sensor real ou simulado ruuviTag excede este valor. Pode ser necessário ajustar o valor para cima ou para baixo, dependendo da gama normal de humidade no seu ambiente.  

   ![Azure IoT Central adiciona condições de regra](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-conditions.png)

Para adicionar uma ação à regra:

1. Selecione **+ E-mail**. 

1. Introduza a *notificação* de alta humidade como o nome de **exibição** amigável para a ação. 

1. Insira o endereço de e-mail associado à sua conta em **.** Se utilizar um e-mail diferente, o endereço que utiliza deve ser para um utilizador que tenha sido adicionado à aplicação. O utilizador também precisa de iniciar sessão e sair pelo menos uma vez.

1. Opcionalmente, insira uma nota para incluir no texto do e-mail.

1. Selecione **Feito** para completar a ação.

   ![Azure IoT Central adiciona ações às regras](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-action.png)

1. Selecione **Guardar** para guardar e ativar a nova regra. 

    Dentro de poucos minutos, a conta de e-mail especificada deve começar a receber e-mails. A aplicação envia e-mail cada vez que um sensor indica que o nível de humidade excedeu o valor no seu estado.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

* Use a análise central da loja Azure IoT - modelo de **check-out** para criar uma aplicação de loja de retalho
* Personalize as definições de aplicação
* Criar e personalizar modelos de dispositivos IoT
* Ligar dispositivos à sua aplicação
* Adicionar regras e ações para monitorizar as condições

Agora que criou uma aplicação de monitorização de condições Azure IoT Central, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Personalize o painel de instrumentos do operador](./tutorial-in-store-analytics-customize-dashboard-pnp.md)

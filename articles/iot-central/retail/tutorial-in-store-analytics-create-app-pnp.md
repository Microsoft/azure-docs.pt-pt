---
title: Criar um aplicativo de análise no repositório no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra como criar um aplicativo de varejo de análise no repositório no IoT Central. Você o criará, personalizará e adicionará dispositivos de sensor.
services: iot-central
ms.service: iot-central
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 10/03/2019
ms.openlocfilehash: 0d586aaab4fc87bb671e67152cf66e9fb1447504
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495183"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Tutorial: criar um aplicativo de análise no repositório no Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

O tutorial mostra os construtores de solução como criar um aplicativo de análise na loja do Azure IoT Central. O aplicativo de exemplo é para uma loja de varejo. Trata-se de uma solução para a necessidade comercial comum de monitorar e adaptar-se a ocupação e condições ambientais.

O aplicativo de exemplo que você cria inclui três dispositivos reais: um gateway 500 em cascata rigado e dois sensores RuuviTag. O tutorial também mostra como usar o sensor de ocupação simulado incluído no modelo de aplicativo para fins de teste. O gateway rigado C500 serve como o Hub de comunicação em seu aplicativo. Ele se comunica com sensores em sua loja e gerencia suas conexões com a nuvem. O RuuviTag é um sensor ambiental que fornece telemetria, incluindo temperatura, umidade e pressão. O sensor de ocupação simulado fornece uma maneira de acompanhar o movimento e a presença nas áreas de check-out de uma loja. 

Este tutorial inclui instruções para conectar os dispositivos rigado e RuuviTag ao seu aplicativo. Se você tiver outro gateway e sensores, ainda poderá seguir as etapas para compilar seu aplicativo. O tutorial também mostra como criar sensores RuuviTag simulados. Os sensores simulados permitem que você crie o aplicativo se não tiver dispositivos reais. 

Você desenvolve a solução de monitoramento de check-out e condição em três partes:

* Criar o aplicativo e conectar dispositivos para monitorar condições
* Personalizar o painel para permitir que os operadores monitorem e gerenciem dispositivos
* Configurar a exportação de dados para permitir que os gerentes de loja executem análises e visualizem insights

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Use o modelo Azure IoT Central **no repositório Analytics-checkout** para criar um aplicativo de loja de varejo
> * Personalizar as configurações do aplicativo
> * Criar e personalizar modelos de dispositivo IoT
> * Conectar dispositivos ao seu aplicativo
> * Adicionar regras e ações para monitorar condições

## <a name="prerequisites"></a>Pré-requisitos

Para concluir esta série de tutoriais, você precisa de:
* Uma assinatura do Azure é recomendada. Opcionalmente, você pode usar uma avaliação gratuita de 7 dias. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de inscrição do Azure](https://aka.ms/createazuresubscription).
* Acesso a um dispositivo de gateway e dois sensores ambientais (opcionalmente, você pode usar dispositivos simulados conforme descrito no tutorial)
* Modelos de dispositivo para os dispositivos que você usa (os modelos são fornecidos para todos os dispositivos usados no tutorial)

## <a name="create-an-application"></a>Criar uma aplicação
Nesta seção, você cria um novo aplicativo de IoT Central do Azure a partir de um modelo. Você usará este aplicativo em toda a série de tutoriais para criar uma solução completa.

Para criar uma nova aplicação do Azure IoT Central:

1. Navegue até o site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) .

1. Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-la, caso contrário, entre usando uma conta Microsoft:

    ![Introduza a sua conta da organização](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação).

1. Selecione **varejo**.  A página de varejo exibe vários modelos de aplicativos de varejo.

Para criar um novo aplicativo de check-out de análise no repositório que usa recursos de visualização:  

1. Selecione o modelo **no repositório Analytics-check-out** do aplicativo. Este modelo inclui modelos de dispositivo para todos os dispositivos usados no tutorial, exceto para sensores RuuviTag. O modelo também fornece um painel de operador para monitorar as condições de check-out e de ambiente e o status do dispositivo. 

1. Opcionalmente, escolha um **nome de aplicativo**amigável. Esse aplicativo se baseia em um loja de varejo fictício chamado contoso. O tutorial usa o **nome do aplicativo** *contoso checkout*. O modelo de aplicativo é baseado na empresa fictícia Northwind. Neste tutorial, você usa a Contoso para aprender a personalizar o aplicativo.

    > [!NOTE]
    > Se você usar um **nome de aplicativo**amigável, você ainda deverá usar um valor exclusivo para a **URL**do aplicativo.

1. Se você tiver uma assinatura do Azure, insira seu *diretório, a assinatura do Azure e a região*. Se você não tiver uma assinatura, poderá habilitar a **avaliação gratuita de 7 dias** e concluir as informações de contato necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Selecione **Criar**.

    ![Página da Aplicação do Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/preview-application-template.png)

## <a name="customize-application-settings"></a>Personalizar configurações do aplicativo
Como um construtor, você pode alterar várias configurações para personalizar a experiência do usuário em seu aplicativo. Nesta seção, você selecionará um tema de aplicativo predefinido. Opcionalmente, você aprenderá a criar um tema personalizado e a atualizar a imagem do aplicativo. Um tema personalizado permite que você defina as cores do navegador de aplicativos, o ícone do navegador e o logotipo do aplicativo que aparece na manchete.

Para selecionar um tema de aplicativo predefinido:

1. Selecione **configurações** na manchete.

    ![Configurações do aplicativo IoT Central do Azure](./media/tutorial-in-store-analytics-create-app-pnp/settings-icon.png)

2. Selecione um novo **tema**.

3. Selecione **Guardar**.

Em vez de usar um tema predefinido, você pode criar um tema personalizado. Se você quiser usar um conjunto de imagens de exemplo para personalizar o aplicativo e concluir o tutorial, baixe as [imagens de exemplo da Contoso](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Para criar um tema personalizado:

1. Expanda o painel esquerdo, se ainda não estiver expandido.

    ![Painel esquerdo do IoT Central do Azure](./media/tutorial-in-store-analytics-create-app-pnp/dashboard-expand.png)

1. Selecione **administração > personalizar seu aplicativo**.

1. Use o botão **alterar** para escolher uma imagem a ser carregada como o **logotipo do aplicativo**. Opcionalmente, especifique um valor para **texto alt do logotipo**. 

1. Use o botão **alterar** para escolher uma imagem de **ícone do navegador** que será exibida nas guias do navegador.

1. Opcionalmente, substitua as cores padrão do **navegador** adicionando códigos de cores hexadecimais HTML. Para o **cabeçalho**, adicione *#008575*.  Para o **acento**, adicione *#A1F3EA*. 

1. Selecione **Guardar**. 

    ![Logotipo personalizado do Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/select-application-logo.png)

    Depois de salvar, o aplicativo atualiza as cores do navegador, o logotipo no cabeçalho e o ícone do navegador. 

    ![Configurações do aplicativo atualizadas do Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/saved-application-settings.png)

Para atualizar a imagem do aplicativo:

1. Selecione **administração > configurações do aplicativo**.

1. Use o botão **selecionar imagem** para escolher uma imagem a ser carregada como a imagem do aplicativo. Essa imagem aparece no bloco do aplicativo na página **meus aplicativos** do IOT central Gerenciador de aplicativos.

1. Selecione **Guardar**.

1. Opcionalmente, navegue até a exibição **meus aplicativos** no site do [Azure IOT central Application Manager](https://aka.ms/iotcentral) . O bloco do aplicativo exibe a imagem de aplicativo atualizada.

    ![Personalizar o Azure IoT Central imagem do aplicativo](./media/tutorial-in-store-analytics-create-app-pnp/customize-application-image.png)

## <a name="create-device-templates"></a>Criar modelos de dispositivo
Como um construtor, você pode criar modelos de dispositivo que permitem que você e os operadores de aplicativo configurem e gerenciem dispositivos. Você cria um modelo criando um arquivo personalizado, importando um modelo existente ou importando um modelo do catálogo de dispositivos do Azure IoT. Depois de criar e personalizar um modelo de dispositivo, use-o para conectar dispositivos reais ao seu aplicativo. Opcionalmente, use um modelo de dispositivo para gerar dispositivos simulados para teste.

O modelo **de aplicativo análise de check-out no repositório** do é com modelos de dispositivo para vários dispositivos.  Há modelos de dispositivo para dois dos três dispositivos que você usa no aplicativo. O modelo de dispositivo RuuviTag não está incluído no modelo **de aplicativo Analytics-CHECKTABLE in-Store** . Nesta seção, você adiciona um modelo de dispositivo para sensores RuuviTag em seu aplicativo.

Para adicionar um modelo de dispositivo RuuviTag ao seu aplicativo:

1. Selecione **modelos de dispositivo** no painel esquerdo.

1. Selecione **+ novo** para criar um novo modelo de dispositivo.

1. Localize e selecione o modelo de dispositivo do sensor **RuuviTag** no catálogo do dispositivo IOT do Azure. 

1. Selecione **Avançar: Personalizar**.

    ![Modelo de dispositivo do sensor RuuviTag do Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template.png)

1. Selecione **Criar**. O aplicativo adiciona o modelo de dispositivo RuuviTag.

1. Selecione **modelos de dispositivo** no painel esquerdo. A página exibe todos os modelos de dispositivo incluídos no modelo de aplicativo e o modelo de dispositivo RuuviTag que você acabou de adicionar.

    ![Modelo de dispositivo do sensor RuuviTag do Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/device-templates-list.png)

## <a name="customize-device-templates"></a>Personalizar modelos de dispositivo
Você pode personalizar os modelos de dispositivo em seu aplicativo de três maneiras. Primeiro, você personaliza as interfaces internas nativas em seus dispositivos alterando os recursos do dispositivo. Por exemplo, com um sensor de temperatura, você pode alterar detalhes como o nome de exibição da interface de temperatura, o tipo de dados, as unidades de medida e os intervalos operacionais mínimo e máximo. 

Em segundo lugar, personalize os modelos de dispositivo adicionando Propriedades de nuvem. As propriedades de nuvem não fazem parte dos recursos internos do dispositivo. As propriedades de nuvem são dados personalizados que o aplicativo IoT Central do Azure cria, armazena e associa aos seus dispositivos. Um exemplo de uma propriedade de nuvem pode ser um valor calculado ou metadados como um local que você deseja associar a um conjunto de dispositivos. 

Em terceiro lugar, personalize os modelos de dispositivo criando exibições personalizadas. As exibições fornecem uma maneira de os operadores visualizarem a telemetria e os metadados para seus dispositivos, como métricas de dispositivo e integridade.

Aqui, você usa os dois primeiros métodos para personalizar o modelo de dispositivo para os sensores do RuuviTag. Para obter informações sobre como criar modos de exibição para seus sensores, consulte o guia de início rápido [Adicionar um dispositivo simulado ao seu aplicativo IOT central](../core/quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

Para personalizar as interfaces internas do modelo de dispositivo RuuviTag:

1. Selecione **modelos de dispositivo** no painel esquerdo. 

1. Selecione o modelo para sensores RuuviTag. 

1. Ocultar o painel esquerdo. A exibição de resumo do modelo exibe os recursos do dispositivo.

    ![Exibição do resumo do modelo de dispositivo do Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-summary-view.png)

1. Selecione **Personalizar** no menu modelo de dispositivo RuuviTag. 

1. Role na lista de recursos e localize o tipo de telemetria `humidity`. É o item de linha com o valor de **nome de exibição** editável de *umidade*.

Nas etapas a seguir, você personaliza o tipo de telemetria `humidity` para os sensores RuuviTag. Opcionalmente, personalize alguns dos outros tipos de telemetria.

Para o tipo de telemetria `humidity`, faça as seguintes alterações:

1. Selecione o controle de **expansão** para expandir os detalhes do esquema para a linha.

1. Atualize o valor do **nome de exibição** de *umidade* para um valor personalizado, como *umidade relativa*.

1. Altere a opção **tipo semântico** de *nenhum* para *umidade*.  Opcionalmente, defina valores de esquema para o tipo de telemetria umidade na exibição de esquema expandida. As configurações de esquema permitem que você crie requisitos de validação detalhados para os dados que seus sensores acompanham. Por exemplo, você pode definir valores mínimo e máximo de intervalo operacional para uma determinada interface.

1. Selecione **salvar** para salvar as alterações.

    ![Personalização do modelo de dispositivo RuuviTag do Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-customize.png)

Para adicionar uma propriedade de nuvem a um modelo de dispositivo em seu aplicativo:

1. Selecione **Propriedades de nuvem** no menu modelo de dispositivo RuuviTag.

1. Selecione **Adicionar Propriedade de nuvem**. 

Especifique os seguintes valores para criar uma propriedade personalizada para armazenar o local de cada dispositivo:

1. Insira o *local* do valor para o **nome de exibição**. Esse valor é copiado automaticamente para o campo **nome** , que é um nome amigável para a propriedade. Você pode usar o valor copiado ou alterá-lo.

1. Selecione *cadeia de caracteres* na lista suspensa **esquema** . Um tipo de cadeia de caracteres permite que você associe uma cadeia de caracteres de nome de local a qualquer dispositivo com base no modelo. Por exemplo, você pode associar uma área em um repositório a cada dispositivo. Opcionalmente, você pode definir o **tipo semântico** de sua propriedade como *local*, e isso define automaticamente o **esquema** como *geopoint*. Isso permite que você associe coordenadas de GPS a um dispositivo. 

1. Defina o **comprimento mínimo** como *2*. 

1. Defina o **espaço em branco de corte** como **ativado**.

1. Selecione **salvar** para salvar sua propriedade de nuvem personalizada.

    ![Personalização do modelo de dispositivo RuuviTag do Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-cloud-property.png)

1. Selecione **Publicar**. 

    A publicação de um modelo de dispositivo torna-o visível para os operadores de aplicativo. Depois de publicar um modelo, use-o para gerar dispositivos simulados para teste ou para conectar dispositivos reais ao seu aplicativo. Se você já tiver dispositivos conectados ao seu aplicativo, a publicação de um modelo personalizado enviará por push as alterações para os dispositivos.

## <a name="add-devices"></a>Adicionar dispositivos
Depois de criar e personalizar modelos de dispositivo, é hora de adicionar dispositivos. 

Para este tutorial, você usa o seguinte conjunto de dispositivos reais e simulados para compilar o aplicativo:
- Um gateway C500 rigado real
- Dois sensores reais de RuuviTag
- Um sensor de **ocupação** simulado. O sensor simulado está incluído no modelo de aplicativo, portanto, você não precisa criá-lo. 

> [!NOTE]
> Se você não tiver dispositivos reais, ainda poderá concluir este tutorial Criando sensores RuuviTag simulados. As instruções a seguir incluem etapas para criar um RuuviTag simulado. Você não precisa criar um gateway simulado.

Conclua as etapas nos dois artigos a seguir para conectar um gateway de rigado real e sensores de RuuviTag. Depois de terminar, retorne a este tutorial. Como você já criou modelos de dispositivo neste tutorial, não é necessário criá-los novamente no conjunto de instruções a seguir.

- Para conectar um gateway do rigado, consulte [conectar um rigado em cascata 500 ao aplicativo IOT central do Azure](../core/howto-connect-rigado-cascade-500-pnp.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- Para conectar sensores do RuuviTag, consulte [conectar um sensor RuuviTag ao aplicativo IOT central do Azure](../core/howto-connect-ruuvi-pnp.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Você também pode usar essas instruções para criar dois sensores simulados, se necessário.

## <a name="add-rules-and-actions"></a>Adicionar regras e ações
Como parte do uso de sensores em seu aplicativo de IoT Central do Azure para monitorar condições, você pode criar regras para executar ações quando determinadas condições forem atendidas. Uma regra é associada a um modelo de dispositivo e um ou mais dispositivos e contém condições que devem ser atendidas com base em eventos ou telemetria do dispositivo. Uma regra também tem uma ou mais ações associadas. As ações podem incluir o envio de notificações por email ou o disparo de uma ação de webhook para enviar dados a outros serviços. O modelo **de aplicativo análise na loja – check-out** inclui algumas regras predefinidas para os dispositivos no aplicativo.

Nesta seção, você cria uma nova regra que verifica o nível máximo de umidade relativa com base na telemetria do sensor RuuviTag. Você adiciona uma ação à regra para que, se a umidade exceder o máximo, o aplicativo envie email. 

Para criar uma regra: 

1. Expanda o painel esquerdo.

1. Selecione **regras**.

1. Selecione **+ novo**.

1. Insira o *nível de umidade* como o nome da regra. 

1. Escolha o modelo de dispositivo RuuviTag em **escopos**. A regra que você definir será aplicada a todos os sensores com base nesse modelo. Opcionalmente, você pode criar um filtro que aplicaria a regra somente a um subconjunto definido dos sensores. 

1. Escolha `Relative humidity` como **telemetria**. Essa é a funcionalidade de dispositivo que você personalizou em uma etapa anterior.

1. Escolha `Is greater than` como o **operador**. 

1. Insira um nível de umidade típico de intervalo superior para o seu ambiente como o **valor**. Por exemplo, digite *65*. Você definiu uma condição para a regra que ocorre quando a umidade relativa em qualquer sensor RuuviTag real ou simulado excede esse valor. Talvez seja necessário ajustar o valor para cima ou para baixo, dependendo do intervalo de umidade normal em seu ambiente.  

   ![Condições de adição de regra do IoT Central do Azure](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-conditions.png)

Para adicionar uma ação à regra:

1. Selecione **+ email**. 

1. Insira a *notificação de umidade alta* como o **nome de exibição** amigável para a ação. 

1. Insira o endereço de email associado à sua conta no **para**. Se você usar um email diferente, o endereço usado deverá ser para um usuário que foi adicionado ao aplicativo. O usuário também precisa entrar e sair pelo menos uma vez.

1. Opcionalmente, insira uma observação a ser incluída no texto do email.

1. Selecione **concluído** para concluir a ação.

   ![O Azure IoT Central adicionar ações às regras](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-action.png)

1. Selecione **salvar** para salvar e ativar a nova regra. 

    Em alguns minutos, a conta de email especificada deve começar a receber emails. O aplicativo envia email cada vez que um sensor indica que o nível de umidade excedeu o valor em sua condição.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

* Use o modelo Azure IoT Central **no repositório Analytics-checkout** para criar um aplicativo de loja de varejo
* Personalizar as configurações do aplicativo
* Criar e personalizar modelos de dispositivo IoT
* Conectar dispositivos ao seu aplicativo
* Adicionar regras e ações para monitorar condições

Agora que você criou um aplicativo de monitoramento da condição de IoT Central do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Personalizar o painel do operador](./tutorial-in-store-analytics-customize-dashboard-pnp.md)

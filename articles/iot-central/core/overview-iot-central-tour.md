---
title: Apresentação da IU do Azure IoT Central | Microsoft Docs
description: Familiarize-se com as áreas-chave da UI Central Azure IoT que utiliza para criar, gerir e utilizar a sua solução IoT.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: bf60f512416007137e71119fa7474b1393099ebf
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718886"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Apresentação da IU do Azure IoT Central

Este artigo apresenta-o à UI Central Azure IoT. Pode utilizar a UI para criar, gerir e utilizar uma aplicação IoT Central e os seus dispositivos conectados.

## <a name="iot-central-homepage"></a>Página inicial do IoT Central

A página inicial do [IoT Central](https://aka.ms/iotcentral-get-started) é o local para saber mais sobre as últimas novidades e funcionalidades disponíveis no IoT Central, criar novas aplicações e ver e lançar as suas aplicações existentes.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="Página inicial do IoT Central":::

### <a name="create-an-application"></a>Criar uma aplicação

Na secção **Build** pode navegar na lista de modelos IoT Central relevantes para a indústria, ou começar do zero usando um modelo de aplicação personalizado.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="Página de construção IoT Central":::

Para saber mais, consulte a [aplicação Create a azure IoT Central.](quick-deploy-iot-central.md)

### <a name="launch-your-application"></a>Lance a sua aplicação

Você lança a sua aplicação IoT Central navegando para o URL que escolheu durante a criação de aplicações. Também pode ver uma lista de todas as aplicações a que tem acesso no gestor de [aplicações IoT Central.](https://aka.ms/iotcentral-apps)

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="Gestor de aplicativos IoT Central":::

## <a name="navigate-your-application"></a>Navegue pela sua aplicação

Assim que estiver dentro da sua aplicação IoT, use o painel esquerdo para aceder a várias funcionalidades. Pode expandir ou colapsar o painel esquerdo selecionando o ícone de três linhas em cima do painel:

> [!NOTE]
> Os itens que vê no painel esquerdo dependem da sua função de utilizador. Saiba mais sobre [a gestão de utilizadores e funções.](howto-manage-users-roles.md) 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="painel esquerdo":::

  :::column-end:::
  :::column span="2":::
     **O painel** de instrumentos exibe todas as aplicações e dashboards pessoais. 
     
     **Os dispositivos** permitem-lhe gerir todos os seus dispositivos.

     **Os grupos** de dispositivos permitem-lhe visualizar e criar coleções de dispositivos especificados por uma consulta. Os grupos de dispositivos são utilizados através da aplicação para realizar operações a granel.

     **As regras** permitem criar e editar regras para monitorizar os seus dispositivos. As regras são avaliadas com base em dados do dispositivo e desencadeiam ações personalizáveis.

     **A Analytics** expõe capacidades ricas para analisar tendências históricas e correlacionar várias telemetrias dos seus dispositivos.

     **O Jobs** permite-lhe gerir os seus dispositivos em escala, executando operações a granel.

     **Os modelos do dispositivo** permitem criar e gerir as características dos dispositivos que se ligam à sua aplicação.

     **A exportação** de dados permite-lhe configurar uma exportação contínua para serviços externos, como armazenamento e filas.

     **A administração** permite-lhe gerir as configurações da sua aplicação, personalização, faturação, utilizadores e funções.

     **As minhas aplicações** permitem-te saltar de volta para o gestor de aplicações da IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Pesquisa, ajuda, tema e suporte

O menu superior é apresentado em todas as páginas:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="Barra de ferramentas central IoT":::

* Para procurar dispositivos, introduza um valor **de Pesquisa.**
* Para alterar o idioma ou tema da UI, escolha o ícone **Definições.** Saiba mais sobre [como gerir as suas preferências de candidatura](howto-manage-preferences.md)
* Para obter ajuda e suporte, escolha o menu pendente **Ajuda** para obter uma lista de recursos. Pode [obter informações sobre a sua aplicação](./howto-get-app-info.md) a partir do **link Sobre a sua aplicação.** Numa aplicação sobre o plano de preços gratuitos, os recursos de apoio incluem o acesso ao [chat ao vivo.](howto-show-hide-chat.md)
* Para assinar fora da aplicação, escolha o ícone **Conta.**

Pode escolher entre um tema claro ou um tema escuro para a IU:

> [!NOTE]
> A opção de escolher entre temas claros e escuros não está disponível se o seu administrador tiver configurado um tema personalizado para a aplicação.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Screenshot do IoT Central Escolha um Tema.":::

### <a name="dashboard"></a>Dashboard

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Screenshot do Painel Central IoT.":::

* **O dashboard** é a primeira página que vê quando se inscreve na sua aplicação IoT Central. Pode criar e personalizar vários dashboards de aplicação. Saiba mais sobre [a adição de azulejos ao seu painel de instrumentos](howto-add-tiles-to-your-dashboard.md)

* Os dashboards pessoais também podem ser criados para monitorizar o que te importa. Para saber mais, consulte os [painéis pessoais Create Azure IoT Central](howto-create-personal-dashboards.md) como fazer o artigo.

### <a name="devices"></a>Dispositivos

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Screenshot da página de dispositivos.":::

Esta página mostra os dispositivos da sua aplicação IoT Central agrupadas pelo _modelo do dispositivo_.

* Um modelo do dispositivo define um tipo de dispositivo que pode ligar à aplicação.
* Um dispositivo representa um dispositivo real ou simulado na aplicação.

Para saber mais, consulte o Monitor que [os seus dispositivos](./quick-monitor-devices.md) iniciam rapidamente. 

### <a name="device-groups"></a>Device groups

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Página do Grupo de Dispositivos":::

Esta página permite criar e visualizar grupos de dispositivos na sua aplicação IoT Central. Pode utilizar grupos de dispositivos para fazer operações a granel na sua aplicação ou para analisar dados. Para saber mais, consulte os [grupos de dispositivos Use no seu artigo de aplicação Azure IoT Central.](tutorial-use-device-groups.md)

### <a name="rules"></a>Regras
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Screenshot da Página das Regras.":::

Esta página permite-lhe visualizar e criar regras com base em dados do dispositivo. Quando uma regra dispara, pode desencadear uma ou mais ações, como o envio de um e-mail ou a invocação de um webhook. Para aprender, consulte o tutorial [de regras de configuração.](tutorial-create-telemetry-rules.md)

### <a name="analytics"></a>Análise

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Screenshot da página de Analytics.":::

A Analytics expõe capacidades ricas para analisar tendências históricas e correlacionar várias telemetrias dos seus dispositivos. Para saber mais, consulte a [analítica Create para o seu artigo de aplicação Azure IoT Central.](howto-create-analytics.md)

### <a name="jobs"></a>Tarefas

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Página jobs":::

Esta página permite-lhe visualizar e criar trabalhos que podem ser utilizados para operações de gestão de dispositivos a granel nos seus dispositivos. Pode atualizar as propriedades, configurações do dispositivo e executar comandos contra grupos de dispositivos. Para obter mais informações, veja o artigo [Executar uma tarefa](howto-run-a-job.md).

### <a name="device-templates"></a>Modelos de dispositivo

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Screenshot dos modelos do dispositivo.":::

A página de modelos do dispositivo é onde você pode ver e criar modelos de dispositivo na aplicação. Para obter mais informações, veja o tutorial [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](howto-set-up-template.md).

### <a name="data-export"></a>Exportação de dados

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Exportação de Dados":::

A exportação de dados permite-lhe configurar fluxos de dados para sistemas externos. Para saber mais, veja os [seus dados Exporte no artigo Azure IoT Central.](./howto-export-data.md)

### <a name="administration"></a>Administração

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Screenshot da Administração IoT.":::

A página da administração permite-lhe configurar e personalizar a sua aplicação IoT Central. Aqui pode alterar o nome da sua aplicação, URL, tema, gerir utilizadores e funções, criar fichas API e exportar a sua aplicação. Para obter mais informações, veja o artigo [Administrar a aplicação do Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Azure IoT Central e está familiarizado com o esquema da IU, o passo seguinte sugerido é concluir o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).

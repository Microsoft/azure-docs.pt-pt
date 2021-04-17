---
title: Apresentação da IU do Azure IoT Central | Microsoft Docs
description: Familiarize-se com as áreas-chave da UI Central Azure IoT que utiliza para criar, gerir e utilizar a sua solução IoT.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: ff795281b3471d5c6f57397edf5a52e0df9bbd32
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589130"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Apresentação da IU do Azure IoT Central

Este artigo apresenta-lhe a IU do Microsoft Azure IoT Central. Pode utilizar a IU para criar, gerir e utilizar uma solução do Azure IoT Central e os respetivos dispositivos ligados.

## <a name="iot-central-homepage"></a>Página inicial do IoT Central

A página inicial do [IoT Central](https://aka.ms/iotcentral-get-started) é o local para saber mais sobre as últimas novidades e funcionalidades disponíveis no IoT Central, criar novas aplicações e ver e lançar as suas aplicações existentes.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="Página inicial do IoT Central":::

### <a name="create-an-application"></a>Criar uma aplicação

Na secção Build pode navegar na lista de modelos IoT Central relevantes para a indústria, ou começar do zero usando um modelo de aplicação personalizado.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="Página de construção IoT Central":::

Para saber mais, consulte a [aplicação Create a azure IoT Central.](quick-deploy-iot-central.md)

### <a name="launch-your-application"></a>Lance a sua aplicação

Você lança a sua aplicação IoT Central navegando para o URL que escolheu durante a criação de aplicações. Também pode ver uma lista de todas as aplicações a que tem acesso no gestor de [aplicações IoT Central.](https://aka.ms/iotcentral-apps)

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="Gestor de aplicativos IoT Central":::

## <a name="navigate-your-application"></a>Navegue pela sua aplicação

Uma vez dentro da sua aplicação IoT, use o painel esquerdo para aceder às diferentes áreas. Pode expandir ou colapsar o painel esquerdo selecionando o ícone de três linhas em cima do painel:

> [!NOTE]
> Os itens que vê no painel esquerdo dependem da sua função de utilizador. Saiba mais sobre [a gestão de utilizadores e funções.](howto-manage-users-roles.md) 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="painel esquerdo":::

  :::column-end:::
  :::column span="2":::
     **Os dashboards** exibem todas as aplicações e dashboards pessoais. 
     
     **Os dispositivos** permitem-lhe gerir os seus dispositivos conectados - reais e simulados.

     **Os grupos** de dispositivos permitem-lhe visualizar e criar coleções de dispositivos especificados por uma consulta. Os grupos de dispositivos são utilizados através da aplicação para realizar operações a granel.

     **As regras** permitem criar e editar regras para monitorizar os seus dispositivos. As regras são avaliadas com base na telemetria do dispositivo e no gatilho de ações personalizáveis.

     **A Analytics** permite-lhe ver a telemetria dos seus dispositivos graficamente.

     **Os trabalhos** permitem-lhe gerir os seus dispositivos em escala, executando operações a granel.

     **Os modelos do dispositivo** são onde cria e gere as características dos dispositivos que se ligam à sua aplicação.

     **A exportação** de dados permite-lhe configurar uma exportação contínua para serviços externos - como armazenamento e filas.

     **A administração** é onde pode gerir as configurações da sua aplicação, personalização, faturação, utilizadores e funções.
     
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

* O painel de instrumentos é a primeira página que vê quando entra na sua aplicação Azure IoT Central. Pode criar e personalizar vários dashboards de aplicação. Saiba mais sobre [a adição de azulejos ao seu painel de instrumentos](howto-add-tiles-to-your-dashboard.md)

* Os dashboards pessoais também podem ser criados para monitorizar o que te importa. Para saber mais, consulte os [painéis pessoais Create Azure IoT Central](howto-create-personal-dashboards.md) como fazer o artigo.

### <a name="devices"></a>Dispositivos

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Screenshot da página de dispositivos.":::

A página do explorador mostra os _dispositivos_ na sua aplicação Azure IoT Central agrupadas pelo _modelo do dispositivo_. 

* Um modelo do dispositivo define um tipo de dispositivo que pode ligar à aplicação.
* Um dispositivo representa um dispositivo real ou simulado na aplicação.

Para saber mais, consulte o Monitor que [os seus dispositivos](./quick-monitor-devices.md) iniciam rapidamente. 

### <a name="device-groups"></a>Device groups

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Página do Grupo de Dispositivos":::

O grupo de dispositivos é uma coleção de dispositivos relacionados. Utiliza grupos de dispositivos para realizar operações a granel na sua aplicação. Para saber mais, consulte os [grupos de dispositivos Use no seu artigo de aplicação Azure IoT Central.](tutorial-use-device-groups.md)

### <a name="rules"></a>Regras
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Screenshot da Página das Regras.":::

A página de regras permite definir regras baseadas na telemetria, estado ou eventos dos dispositivos. Quando uma regra dispara, pode desencadear uma ou mais ações - como enviar um e-mail, notificar um sistema externo através de alertas webhook, etc. Para aprender, consulte o tutorial [de regras de configuração.](tutorial-create-telemetry-rules.md) 

### <a name="analytics"></a>Análise

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Screenshot da página de Analytics.":::

A página de análise permite-lhe ver a telemetria dos seus dispositivos graficamente, através de uma série de tempo. Para saber mais, consulte a [analítica Create para o seu artigo de aplicação Azure IoT Central.](howto-create-analytics.md)

### <a name="jobs"></a>Tarefas

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Página jobs":::

A página de empregos permite-lhe executar operações a granel nos seus dispositivos. Pode atualizar as propriedades, configurações do dispositivo e executar comandos contra grupos de dispositivos. Para obter mais informações, veja o artigo [Executar uma tarefa](howto-run-a-job.md).

### <a name="device-templates"></a>Modelos de dispositivo

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Screenshot dos modelos do dispositivo.":::

A página de modelos do dispositivo é onde você cria e gere os modelos do dispositivo na aplicação. Um modelo de dispositivo especifica características dos dispositivos tais como:

* Medições de telemetria, estado e evento
* Propriedades
* Comandos
* Vistas

Para obter mais informações, veja o tutorial [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Exportação de dados

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Exportação de Dados":::

A exportação de dados permite-lhe configurar fluxos de dados para sistemas externos. Para saber mais, veja os [seus dados Exporte no artigo Azure IoT Central.](./howto-export-data.md)

### <a name="administration"></a>Administração

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Screenshot da Administração IoT.":::

A página da administração permite-lhe configurar e personalizar a sua aplicação IoT Central. Aqui pode alterar o nome da sua aplicação, URL, tema, gerir utilizadores e funções, criar fichas API e exportar a sua aplicação. Para obter mais informações, veja o artigo [Administrar a aplicação do Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Azure IoT Central e está familiarizado com o esquema da IU, o passo seguinte sugerido é concluir o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).

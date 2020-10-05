---
title: Apresentação da IU do Azure IoT Central | Microsoft Docs
description: Familiarize-se com as áreas-chave da UI Central Azure IoT que utiliza para criar, gerir e utilizar a sua solução IoT.
author: lmasieri
ms.author: lmasieri
ms.date: 12/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: c07d247358d980152648fdf7f702c8efcb72bab9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90020078"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Apresentação da IU do Azure IoT Central

Este artigo apresenta-lhe a IU do Microsoft Azure IoT Central. Pode utilizar a IU para criar, gerir e utilizar uma solução do Azure IoT Central e os respetivos dispositivos ligados.

Como construtor de _soluções,_ utiliza a UI Central Azure IoT para definir a sua solução Azure IoT Central. Pode utilizar a IU para:

* Definir os tipos de dispositivos que ligam à solução.
* Configurar as regras e ações para os seus dispositivos. 
* Personalizar a IU para um _operador_ que utiliza a solução.

Como _operador_, utilize a IU do Azure IoT Central para gerir a sua solução do Azure IoT Central. Pode utilizar a IU para:

* Monitorizar os seus dispositivos.
* Configurar os seus dispositivos.
* Resolver e remediar problemas nos seus dispositivos.
* Aprovisionar novos dispositivos.

## <a name="iot-central-homepage"></a>Página inicial do IoT Central

A página inicial do [IoT Central](https://aka.ms/iotcentral-get-started) é o local onde pode saber mais sobre as últimas novidades e funcionalidades disponíveis no IoT Central, criar novas aplicações e ver e lançar a sua aplicação existente.

> [!div class="mx-imgBorder"]
> ![Página inicial do IoT Central](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Criar uma aplicação

Na secção Build pode navegar na lista de modelos IoT Central relevantes para a indústria para ajudá-lo a começar rapidamente, ou começar do zero usando um modelo de aplicação personalizado.  
> [!div class="mx-imgBorder"]
> ![Página de construção IoT Central](media/overview-iot-central-tour/iot-central-build-pnp.png)

Para saber mais, consulte a [aplicação Create a azure IoT Central.](quick-deploy-iot-central.md)

### <a name="launch-your-application"></a>Lance a sua aplicação

Pode lançar a sua aplicação IoT Central indo para o URL que você ou o seu construtor de soluções escolher durante a criação de aplicações. Também pode ver uma lista de todas as aplicações a que tem acesso no gestor de [aplicações IoT Central.](https://aka.ms/iotcentral-apps)

> [!div class="mx-imgBorder"]
> ![Gestor de aplicativos IoT Central](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navegue pela sua aplicação

Uma vez dentro da sua aplicação IoT, use o painel esquerdo para aceder às diferentes áreas. Pode expandir ou colapsar o painel esquerdo selecionando o ícone de três linhas em cima do painel:

> [!NOTE]
> Os itens que vê no painel esquerdo dependem da sua função de utilizador. Saiba mais sobre [a gestão de utilizadores e funções.](howto-manage-users-roles.md) 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![painel esquerdo](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **O painel** de instrumentos exibe o painel de aplicações. Como construtor de *soluções,* pode personalizar o dashboard global para os seus operadores. Dependendo da sua função de utilizador, os operadores também podem criar os seus próprios dashboards pessoais.
     
     **Os dispositivos** permitem-lhe gerir os seus dispositivos conectados - reais e simulados.

     **Os grupos** de dispositivos permitem-lhe visualizar e criar coleções lógicas de dispositivos especificados por uma consulta. Pode guardar esta consulta e utilizar grupos de dispositivos através da aplicação para realizar operações a granel.

     **As regras** permitem criar e editar regras para monitorizar os seus dispositivos. As regras são avaliadas com base na telemetria do dispositivo e no gatilho de ações personalizáveis.

     **O Analytics** permite criar vistas personalizadas sobre os dados do dispositivo para obter insights da sua aplicação.

     **Os trabalhos** permitem-lhe gerir os seus dispositivos em escala, executando operações a granel.

     **Os modelos do dispositivo** são onde cria e gere as características dos dispositivos que se ligam à sua aplicação.

     **A exportação** de dados permite-lhe configurar uma exportação contínua para serviços externos - como armazenamento e filas.

     **A administração** é onde pode gerir as configurações da sua aplicação, personalização, faturação, utilizadores e funções.

     **A IoT Central** permite que *os administradores* saltem de volta para o gestor de aplicações da IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Pesquisa, ajuda, tema e suporte

O menu superior é apresentado em todas as páginas:

> [!div class="mx-imgBorder"]
> ![Barra de ferramentas](media/overview-iot-central-tour/toolbar-pnp.png)

* Para procurar modelos e dispositivos do dispositivo, insira um valor **de Pesquisa.**
* Para alterar o idioma ou tema da UI, escolha o ícone **Definições.** Saiba mais sobre [como gerir as suas preferências de candidatura](howto-manage-preferences.md)
* Para assinar fora da aplicação, escolha o ícone **Conta.**
* Para obter ajuda e suporte, escolha o menu pendente **Ajuda** para obter uma lista de recursos. Pode [obter informações sobre a sua aplicação](./howto-get-app-info.md) a partir do **link Sobre a sua aplicação.** Numa aplicação sobre o plano de preços gratuitos, os recursos de apoio incluem o acesso ao [chat ao vivo.](howto-show-hide-chat.md)

Pode escolher entre um tema claro ou um tema escuro para a IU:

> [!NOTE]
> A opção de escolher entre temas claros e escuros não está disponível se o seu administrador tiver configurado um tema personalizado para a aplicação.

> [!div class="mx-imgBorder"]
> ![Escolher um tema para a IU](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Dashboard
> [!div class="mx-imgBorder"]
> ![Dashboard](media/overview-iot-central-tour/dashboard-pnp.png)

* O painel de instrumentos é a primeira página que vê quando entra na sua aplicação Azure IoT Central. Como construtor de *soluções,* pode criar e personalizar vários dashboards de aplicações globais para outros utilizadores. Saiba mais sobre [a adição de azulejos ao seu painel de instrumentos](howto-add-tiles-to-your-dashboard.md)

* Como *operador,* se a sua função de utilizador o permitir, pode criar dashboards pessoais para monitorizar o que lhe importa. Para saber mais, consulte os [painéis pessoais Create Azure IoT Central](howto-create-personal-dashboards.md) como fazer o artigo.

### <a name="devices"></a>Dispositivos

> [!div class="mx-imgBorder"]
> ![Página de dispositivos](media/overview-iot-central-tour/devices-pnp.png)

A página do explorador mostra os _dispositivos_ na sua aplicação Azure IoT Central agrupadas pelo _modelo do dispositivo_. 

* Um modelo do dispositivo define um tipo de dispositivo que pode ligar à aplicação.
* Um dispositivo representa um dispositivo real ou simulado na aplicação.

Para saber mais, consulte o Monitor que [os seus dispositivos](./quick-monitor-devices.md) iniciam rapidamente. 

### <a name="device-groups"></a>Device groups

> [!div class="mx-imgBorder"]
> ![Página de grupos de dispositivos](media/overview-iot-central-tour/device-groups-pnp.png)

O grupo de dispositivos é uma coleção de dispositivos relacionados. Um *construtor de soluções* define uma consulta para identificar os dispositivos que estão incluídos num grupo de dispositivos. Utiliza grupos de dispositivos para realizar operações a granel na sua aplicação. Para saber mais, consulte os [grupos de dispositivos Use no seu artigo de aplicação Azure IoT Central.](tutorial-use-device-groups.md)

### <a name="rules"></a>Regras
> [!div class="mx-imgBorder"]
> ![Página de regras](media/overview-iot-central-tour/rules-pnp.png)

A página de regras permite definir regras baseadas na telemetria, estado ou eventos dos dispositivos. Quando uma regra dispara, pode desencadear uma ou mais ações - como enviar um e-mail, notificar um sistema externo através de alertas webhook, etc. Para aprender, consulte o tutorial [de regras de configuração.](tutorial-create-telemetry-rules.md) 

### <a name="analytics"></a>Análise

> [!div class="mx-imgBorder"]
> ![Página de análise](media/overview-iot-central-tour/analytics-pnp.png)

A análise permite criar vistas personalizadas em cima dos dados do dispositivo para obter insights da sua aplicação. Para saber mais, consulte a [analítica Create para o seu artigo de aplicação Azure IoT Central.](howto-create-analytics.md)

### <a name="jobs"></a>Tarefas

> [!div class="mx-imgBorder"]
> ![Página de tarefas](media/overview-iot-central-tour/jobs-pnp.png)

A página de empregos permite-lhe executar operações de gestão de dispositivos a granel nos seus dispositivos. Pode atualizar as propriedades, configurações do dispositivo e executar comandos contra grupos de dispositivos. Para obter mais informações, veja o artigo [Executar uma tarefa](howto-run-a-job.md).

### <a name="device-templates"></a>Modelos de dispositivo

> [!div class="mx-imgBorder"]
> ![Página de modelos de dispositivo](media/overview-iot-central-tour/templates-pnp.png)

A página de modelos do dispositivo é onde um construtor cria e gere os modelos do dispositivo na aplicação. Um modelo de dispositivo especifica características dos dispositivos tais como:

* Medições de telemetria, estado e evento
* Propriedades
* Comandos
* Vistas

O *construtor de soluções* também pode criar formas e dashboards para os operadores usarem para gerir dispositivos.

Para obter mais informações, veja o tutorial [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Exportação de dados
> [!div class="mx-imgBorder"]
> ![Página de exportação de dados](media/overview-iot-central-tour/export-pnp.png)

A exportação de dados permite-lhe configurar fluxos de dados, como a telemetria, desde a aplicação a sistemas externos. Para saber mais, veja os [seus dados Exporte no artigo Azure IoT Central.](./howto-export-data.md)

### <a name="administration"></a>Administração
> [!div class="mx-imgBorder"]
> ![Página Administração](media/overview-iot-central-tour/administration-pnp.png)

A página da administração permite-lhe configurar e personalizar a sua aplicação IoT Central. Aqui pode alterar o nome da sua aplicação, URL, tema, gerir utilizadores e funções, criar fichas API e exportar a sua aplicação. Para obter mais informações, veja o artigo [Administrar a aplicação do Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Azure IoT Central e está familiarizado com o esquema da IU, o passo seguinte sugerido é concluir o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).

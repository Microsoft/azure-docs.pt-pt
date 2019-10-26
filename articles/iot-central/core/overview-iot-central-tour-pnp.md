---
title: Apresentação da IU do Azure IoT Central | Microsoft Docs
description: Familiarize-se com as principais áreas da interface do usuário IoT Central do Azure que você usa para criar, gerenciar e usar sua solução de IoT.
author: lmasieri
ms.author: lmasieri
ms.date: 10/21/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 0bd078e8c920ca1ebe01767915036ca84650aadd
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958197"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Faça um tour pela interface do usuário do IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este artigo apresenta-lhe a IU do Microsoft Azure IoT Central. Pode utilizar a IU para criar, gerir e utilizar uma solução do Azure IoT Central e os respetivos dispositivos ligados.

Como um _Construtor de soluções_, você usa a interface do usuário IOT central do Azure para definir sua solução de IOT central do Azure. Pode utilizar a IU para:

* Definir os tipos de dispositivos que ligam à solução.
* Configurar as regras e ações para os seus dispositivos. 
* Personalizar a IU para um _operador_ que utiliza a solução.

Como _operador_, utilize a IU do Azure IoT Central para gerir a sua solução do Azure IoT Central. Pode utilizar a IU para:

* Monitorizar os seus dispositivos.
* Configurar os seus dispositivos.
* Resolver e remediar problemas nos seus dispositivos.
* Aprovisionar novos dispositivos.

## <a name="iot-central-homepage"></a>Página inicial do IoT Central

A página da [Home Page do IOT central](https://aka.ms/iotcentral-get-started) é o local onde você pode saber mais sobre as notícias e os recursos mais recentes disponíveis no IOT central, criar novos aplicativos e ver e iniciar seu aplicativo existente.

> [!div class="mx-imgBorder"]
> Home Page do ![IoT Central](media/overview-iot-central-tour-pnp/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Criar uma aplicação

Na seção criar, você pode navegar pela lista de modelos de IoT Central relevantes do setor para ajudá-lo a começar rapidamente ou começar do zero usando um modelo de aplicativo personalizado.  
> [!div class="mx-imgBorder"]
> ![IoT Central página de compilação](media/overview-iot-central-tour-pnp/iot-central-build-pnp.png)

Para saber mais, consulte o guia de início rápido [criar um aplicativo do Azure IOT central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

### <a name="launch-your-application"></a>Inicie seu aplicativo

Você pode iniciar o aplicativo IoT Central acessando a URL que você ou seu construtor de soluções escolhe durante a criação do aplicativo. Você também pode ver uma lista de todos os aplicativos aos quais você tem acesso no [Gerenciador de aplicativos IOT central](https://aka.ms/iotcentral-apps).

> [!div class="mx-imgBorder"]
> ![IoT Central o Gerenciador de aplicativos](media/overview-iot-central-tour-pnp/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navegar pelo seu aplicativo

Quando você estiver dentro de seu aplicativo IoT, use o painel esquerdo para acessar as diferentes áreas. Você pode expandir ou recolher a barra de navegação selecionando o ícone de três linhas na parte superior da barra de navegação:

> [!NOTE]
> Os itens que você vê na barra de navegação dependerão de sua função de usuário. Saiba mais sobre como [gerenciar usuários e funções](howto-manage-users-roles-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![painel esquerdo](media/overview-iot-central-tour-pnp/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Painel** exibe o painel do aplicativo. Como um *Construtor de soluções*, você pode personalizar o painel global para seus operadores. Dependendo de sua função de usuário, os operadores também podem criar seus próprios painéis pessoais.
     
     Os **dispositivos** permitem que você gerencie seus dispositivos conectados – reais e simulados.

     **Grupos de dispositivos** permite exibir e criar coleções lógicas de dispositivos especificados por uma consulta. Você pode salvar essa consulta e usar grupos de dispositivos por meio do aplicativo para executar operações em massa.

     **As regras** permitem que você crie e edite regras para monitorar seus dispositivos. As regras são avaliadas com base na telemetria do dispositivo e disparam ações personalizáveis.

     O **Analytics** permite que você crie exibições personalizadas sobre os dados do dispositivo para obter informações do seu aplicativo.

     Os **trabalhos** permitem que você gerencie seus dispositivos em escala executando operações em massa.

     **Modelos de dispositivo** é onde você cria e gerencia as características dos dispositivos que se conectam ao seu aplicativo.

     A **exportação de dados** permite que você configure uma exportação contínua para serviços externos, como armazenamento e filas.

     **Administração** é onde você pode gerenciar as configurações, a personalização, a cobrança, os usuários e as funções do seu aplicativo.

     **IOT central** permite que *os administradores* retornem ao gerenciador de aplicativos de IOT central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Pesquisa, ajuda, tema e suporte

O menu superior é apresentado em todas as páginas:

> [!div class="mx-imgBorder"]
> ![barra de ferramentas](media/overview-iot-central-tour-pnp/toolbar-pnp.png)

* Para procurar dispositivos e modelos de dispositivo, insira um valor de **pesquisa** .
* Para alterar o idioma ou o tema da interface do usuário, escolha o ícone de **configurações** . Saiba mais sobre como [gerenciar suas preferências de aplicativo](howto-manage-preferences.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* Para sair do aplicativo, escolha o ícone de **conta** .
* Para obter ajuda e suporte, escolha o menu pendente **Ajuda** para obter uma lista de recursos. Em um aplicativo de avaliação, os recursos de suporte incluem acesso ao [Live Chat](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Pode escolher entre um tema claro ou um tema escuro para a IU:

> [!NOTE]
> A opção de escolher entre temas claros e escuros não estará disponível se o administrador tiver configurado um tema personalizado para o aplicativo.

> [!div class="mx-imgBorder"]
> ![escolher um tema para a interface do usuário](media/overview-iot-central-tour-pnp/themes-pnp.png)

### <a name="dashboard"></a>Dashboard
> [!div class="mx-imgBorder"]
> ![Dashboard](media/overview-iot-central-tour-pnp/dashboard-pnp.png)

* O painel é a primeira página que você vê quando entra em seu aplicativo de IoT Central do Azure. Como um *Construtor de soluções*, você pode criar e personalizar vários painéis de aplicativos globais para outros usuários. Saiba mais sobre como [Adicionar blocos ao seu painel](howto-add-tiles-to-your-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

* Como um *operador*, se sua função de usuário permitir, você poderá criar painéis pessoais para monitorar o que importa. Para saber mais, consulte o artigo "como [criar painéis pessoais do Azure IOT central](howto-create-personal-dashboards.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ".

### <a name="devices"></a>Dispositivos

> [!div class="mx-imgBorder"]
> página de dispositivos ![](media/overview-iot-central-tour-pnp/devices-pnp.png)

A página do Explorer mostra os _dispositivos_ em seu aplicativo de IOT central do Azure agrupado por _modelo de dispositivo_. 

* Um modelo do dispositivo define um tipo de dispositivo que pode ligar à aplicação.
* Um dispositivo representa um dispositivo real ou simulado na aplicação.

Para saber mais, confira o tutorial [monitorar seus dispositivos](tutorial-monitor-devices.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) . 

### <a name="device-groups"></a>Grupos de dispositivos

> [!div class="mx-imgBorder"]
> página ![grupos de dispositivos](media/overview-iot-central-tour-pnp/device-groups-pnp.png)

O grupo de dispositivos é uma coleção de dispositivos relacionados. Um *Construtor de soluções* define uma consulta para identificar os dispositivos que estão incluídos em um grupo de dispositivos. Você usa grupos de dispositivos para executar operações em massa em seu aplicativo. Para saber mais, confira o artigo [usar grupos de dispositivos no seu aplicativo IOT central do Azure](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

### <a name="rules"></a>Regras
> [!div class="mx-imgBorder"]
> página de regras de ![](media/overview-iot-central-tour-pnp/rules-pnp.png)

A página regras permite que você defina regras com base na telemetria, estado ou eventos dos dispositivos. Quando uma regra é acionada, ela pode disparar uma ou mais ações, como enviar um email, notificar um sistema externo por meio de alertas de webhook, etc. Para saber mais, consulte o tutorial [Configurando regras](tutorial-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) . 

### <a name="analytics"></a>Análise

> [!div class="mx-imgBorder"]
> ](media/overview-iot-central-tour-pnp/analytics-pnp.png) página de análise de ![

A análise permite que você crie exibições personalizadas sobre os dados do dispositivo para obter informações do seu aplicativo. Para obter mais informações, veja o artigo [Criar análise personalizada para a aplicação do Azure IoT Central](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

### <a name="jobs"></a>Tarefas

> [!div class="mx-imgBorder"]
> página de trabalhos do ![](media/overview-iot-central-tour-pnp/jobs-pnp.png)

A página trabalhos permite executar operações de gerenciamento de dispositivo em massa em seus dispositivos. Você pode atualizar propriedades do dispositivo, configurações e executar comandos em grupos de dispositivos. Para obter mais informações, veja o artigo [Executar uma tarefa](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

### <a name="device-templates"></a>Modelos de dispositivo

> [!div class="mx-imgBorder"]
> página de modelos de dispositivo ![](media/overview-iot-central-tour-pnp/templates-pnp.png)

A página modelos de dispositivo é onde um construtor cria e gerencia os modelos de dispositivo no aplicativo. Um modelo de dispositivo especifica características de dispositivos, como:

* Medições de telemetria, estado e evento
* Propriedades
* Comandos
* Vistas

O *Solution Builder* também pode criar formulários e painéis para que os operadores usem para gerenciar dispositivos.

Para obter mais informações, veja o tutorial [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). 

### <a name="data-export"></a>Exportação de dados
> [!div class="mx-imgBorder"]
> ](media/overview-iot-central-tour-pnp/export-pnp.png) ![página de exportação de dados

A exportação de dados permite que você configure fluxos de dados, como telemetria, do aplicativo para sistemas externos. Para saber mais, consulte o artigo [exportar dados no Azure IOT central](howto-export-data-blob-storage.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

### <a name="administration"></a>Administração
> [!div class="mx-imgBorder"]
> ](media/overview-iot-central-tour-pnp/administration-pnp.png) página de administração de ![

A página Administração permite que você configure e personalize seu aplicativo IoT Central. Aqui você pode alterar o nome do aplicativo, a URL, o tema, o gerenciamento de usuários e funções, criar tokens de API e exportar seu aplicativo. Para obter mais informações, veja o artigo [Administrar a aplicação do Azure IoT Central](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Azure IoT Central e está familiarizado com o esquema da IU, o passo seguinte sugerido é concluir o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

---
title: Apresentação da IU do Azure IoT Central | Microsoft Docs
description: Como construtor, familiarize-se com as principais áreas da IU do Azure IoT Central que utiliza para criar uma solução de IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 82996db232fde0424ccc8e3e478a70a5892231e6
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884822"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Faça um tour pela interface do usuário do IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Este artigo apresenta-lhe a IU do Microsoft Azure IoT Central. Pode utilizar a IU para criar, gerir e utilizar uma solução do Azure IoT Central e os respetivos dispositivos ligados.

Como _construtor_, utilize a IU do Azure IoT Central para definir a sua solução do Azure IoT Central. Pode utilizar a IU para:

* Definir os tipos de dispositivos que ligam à solução.
* Configurar as regras e ações para os seus dispositivos.
* Personalizar a IU para um _operador_ que utiliza a solução.

Como _operador_, utilize a IU do Azure IoT Central para gerir a sua solução do Azure IoT Central. Pode utilizar a IU para:

* Monitorizar os seus dispositivos.
* Configurar os seus dispositivos.
* Resolver e remediar problemas nos seus dispositivos.
* Aprovisionar novos dispositivos.

## <a name="use-the-left-navigation-menu"></a>Utilizar o menu de navegação à esquerda

Use o menu de navegação à esquerda para acessar as diferentes áreas do aplicativo. Você pode expandir ou recolher a barra de navegação **<** selecionando **>** ou:

:::row:::
  :::column span="":::
      ![Left navigation menu](media/overview-iot-central-tour-pnp/navigationbar.png)
  :::column-end:::
  :::column span="2":::
     **Painel** exibe o painel do aplicativo. Como um construtor, você pode personalizar o painel para seus operadores. Os usuários também podem criar seus próprios painéis.
     
     **Dispositivos** lista os dispositivos simulados e reais associados a cada modelo de dispositivo no aplicativo. Como operador, utilize o **Device Explorer** para gerir os dispositivos ligados.

     **Grupos de dispositivos** permite exibir e criar grupos de dispositivos. Como um operador, você pode criar grupos de dispositivos como uma coleção lógica de dispositivos especificados por uma consulta.

     **As regras** permitem que você edite regras que são acionadas com base na telemetria do dispositivo e disparam ações personalizáveis.

     A **análise** mostra a análise derivada da telemetria do dispositivo para dispositivos e grupos de dispositivos. Como operador, pode criar vistas personalizadas com base nos dados dos dispositivos para obter informações da aplicação.

     Os **trabalhos** permitem o gerenciamento de dispositivos em massa, fazendo com que você crie e execute trabalhos para atualizar seus dispositivos em escala.

     **Modelos de dispositivo** mostra as ferramentas que um construtor usa para criar e gerenciar modelos de dispositivo.

     A **exportação de dados** permite que um administrador configure uma exportação contínua para outros serviços do Azure, como armazenamento e filas.

     **Administração** mostra as páginas de administração do aplicativo em que um administrador pode gerenciar configurações, usuários e funções do aplicativo.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Pesquisa, ajuda e suporte

O menu superior é apresentado em todas as páginas:

![Barra de Ferramentas](media/overview-iot-central-tour-pnp/toolbar.png)

* Para procurar dispositivos e modelos de dispositivo, insira um valor de **pesquisa** .
* Para alterar o idioma ou o tema da interface do usuário, escolha o ícone de **configurações** .
* Para sair do aplicativo, escolha o ícone de **conta** .
* Para obter ajuda e suporte, escolha o menu pendente **Ajuda** para obter uma lista de recursos. Em um aplicativo de avaliação, os recursos de suporte incluem acesso ao [Live Chat](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Pode escolher entre um tema claro ou um tema escuro para a IU:

![Escolher um tema para a IU](media/overview-iot-central-tour-pnp/themes.png)

> [!NOTE]
> A opção de escolher entre temas claros e escuros não estará disponível se o administrador tiver configurado um tema personalizado para o aplicativo.

## <a name="dashboard"></a>Dashboard

![Dashboard](media/overview-iot-central-tour-pnp/homepage.png)

* O painel é a primeira página que você vê quando entra em seu aplicativo de IoT Central do Azure. Como um construtor, você pode personalizar o painel do aplicativo para outros usuários adicionando blocos. Para saber mais, consulte o tutorial [configurar um modelo de dispositivo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

* Como um operador, você pode criar painéis personalizados e alternar entre eles e o painel padrão. Para saber mais, confira o artigo como [criar e gerenciar painéis pessoais](howto-personalize-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="devices"></a>Dispositivos

![Página de dispositivos](media/overview-iot-central-tour-pnp/explorer.png)

A página do Explorer mostra os _dispositivos_ em seu aplicativo de IOT central do Azure agrupado por _modelo de dispositivo_.

* Um modelo do dispositivo define um tipo de dispositivo que pode ligar à aplicação. Para obter mais informações, veja [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
* Um dispositivo representa um dispositivo real ou simulado na aplicação. Para obter mais informações, veja [Adicionar um novo dispositivo à aplicação do Azure IoT Central](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-groups"></a>Grupos de dispositivos

![Página grupos de dispositivos](media/overview-iot-central-tour-pnp/devicesets.png)

A página _grupos de dispositivos_ mostra grupos de dispositivos criados pelo construtor. Um grupo de dispositivos é uma coleção de dispositivos relacionados. Um construtor define uma consulta para identificar os dispositivos que estão incluídos em um grupo de dispositivos. Você usa grupos de dispositivos ao personalizar a análise em seu aplicativo. Para saber mais, confira o artigo [usar grupos de dispositivos no seu aplicativo IOT central do Azure](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="rules"></a>Regras

![Página de regras](media/overview-iot-central-tour-pnp/rules.png)

A página regras permite definir regras com base em eventos de telemetria, estado do dispositivo ou dispositivo. Quando uma regra é acionada, ela pode disparar uma ação como enviar um email para um operador. O construtor usa essa página para criar e gerenciar regras. Para obter mais informações, consulte o tutorial [configurar regras e ações para seus dispositivos no Azure IOT central](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="analytics"></a>Análise

![Página de análise](media/overview-iot-central-tour-pnp/analytics.png)

A página de análise apresenta gráficos que o ajudam a compreender como se comportam os dispositivos ligados à sua aplicação. Um operador utiliza esta página para monitorizar e investigar problemas dos dispositivos ligados. O construtor pode definir os gráficos apresentados nesta página. Para obter mais informações, veja o artigo [Criar análise personalizada para a aplicação do Azure IoT Central](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="jobs"></a>Tarefas

![Página de tarefas](media/overview-iot-central-tour-pnp/jobs.png)

A página trabalhos permite executar operações de gerenciamento de dispositivo em massa em seus dispositivos. O construtor utiliza esta página para atualizar as propriedades, as definições e os comandos dos dispositivos. Para obter mais informações, veja o artigo [Executar uma tarefa](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-templates"></a>Modelos de dispositivo

![Página modelos de dispositivo](media/overview-iot-central-tour-pnp/templates.png)

A página modelos de dispositivo é onde um construtor cria e gerencia os modelos de dispositivo no aplicativo. Um modelo de dispositivo especifica características de dispositivo, como:

* Medições de telemetria, estado e evento.
* Properties.
* Comandos.

O Construtor também pode criar formulários e painéis para que os operadores usem para gerenciar dispositivos.

Para obter mais informações, veja o tutorial [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="data-export"></a>Exportação de dados

![Página exportação de dados](media/overview-iot-central-tour-pnp/export.png)

A página exportação de dados é onde um administrador define como transmitir dados, como telemetria, do aplicativo. Outros serviços podem armazenar os dados exportados ou usá-los para análise. Para saber mais, consulte o artigo [exportar dados no Azure IOT central](howto-export-data.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="administration"></a>Administração

![Página Administração](media/overview-iot-central-tour-pnp/administration.png)

A página Administração contém links para as ferramentas que um administrador usa, como a definição de usuários e funções no aplicativo e a personalização da interface do usuário. Para obter mais informações, veja o artigo [Administrar a aplicação do Azure IoT Central](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma descrição geral do Azure IoT Central e está familiarizado com o esquema da IU, o passo seguinte sugerido é concluir o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

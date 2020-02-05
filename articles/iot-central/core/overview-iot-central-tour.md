---
title: Apresentação da IU do Azure IoT Central | Microsoft Docs
description: Como construtor, familiarize-se com as principais áreas da IU do Azure IoT Central que utiliza para criar uma solução de IoT.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 1f71476e398d5fd4466fd861e35ecc3f5b3666b9
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987616"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Apresentação da IU do Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo apresenta-lhe a IU do Microsoft Azure IoT Central. Pode utilizar a IU para criar, gerir e utilizar uma solução do Azure IoT Central e os respetivos dispositivos ligados.

Como _construtor_, utilize a IU do Azure IoT Central para definir a sua solução do Azure IoT Central. Pode utilizar a IU para:

- Definir os tipos de dispositivos que ligam à solução.
- Configurar as regras e ações para os seus dispositivos.
- Personalizar a IU para um _operador_ que utiliza a solução.

Como _operador_, utilize a IU do Azure IoT Central para gerir a sua solução do Azure IoT Central. Pode utilizar a IU para:

- Monitorizar os seus dispositivos.
- Configurar os seus dispositivos.
- Resolver e remediar problemas nos seus dispositivos.
- Aprovisionar novos dispositivos.

## <a name="use-the-left-pane"></a>Use o painel esquerdo

Utilize o painel esquerdo para aceder às diferentes áreas da aplicação. Pode expandir ou colapsar a barra de navegação selecionando **<** ou **>:**

:::row:::
  :::column span="":::
      ![painel esquerdo](media/overview-iot-central-tour/navigationbar.png)
  :::column-end:::
  :::column span="2":::
     **Painel** exibe o painel do aplicativo. Como construtor, pode personalizar o painel de instrumentos para os seus operadores. Os utilizadores também podem criar os seus próprios dashboards.
    
     **O Device Explorer** lista os dispositivos simulados e reais associados a cada modelo de dispositivo na aplicação. Como operador, utilize o **Device Explorer** para gerir os dispositivos ligados.
    
     **O Conjunto de Dispositivos** permite-lhe visualizar e criar conjuntos de dispositivos. Como operador, pode criar conjuntos de dispositivos como uma coleção lógica de dispositivos especificados por uma consulta.
    
     **A análise** mostra a análise derivada da telemetria do dispositivo para dispositivos e conjuntos de dispositivos. Como operador, pode criar vistas personalizadas com base nos dados dos dispositivos para obter informações da aplicação.
    
     **Jobs** permite a gestão de dispositivos a granel, fazendo com que crie e execute trabalhos para realizar atualizações em escala.
    
     **Os modelos de dispositivo** mostram as ferramentas que um construtor usa para criar e gerir modelos de dispositivos.
    
     **A Exportação contínua de Dados** permite a um administrador configurar uma exportação contínua para outros serviços Azure, como armazenamento e filas.
    
     **A administração** mostra as páginas da administração da aplicação onde um administrador pode gerir as definições de aplicação, utilizadores e funções.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Pesquisa, ajuda e suporte

O menu superior é apresentado em todas as páginas:

![Barra de ferramentas](media/overview-iot-central-tour/toolbar.png)

- Para procurar dispositivos e modelos de dispositivo, insira um valor de **pesquisa** .
- Para alterar o idioma ou o tema da interface do usuário, escolha o ícone de **configurações** .
- Para sair do aplicativo, escolha o ícone de **conta** .
- Para obter ajuda e suporte, escolha o menu pendente **Ajuda** para obter uma lista de recursos. Numa aplicação sobre o plano de preços gratuitos, os recursos de apoio incluem o acesso ao [chat ao vivo.](howto-show-hide-chat.md)

Pode escolher entre um tema claro ou um tema escuro para a IU:

![Escolher um tema para a IU](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> A opção de escolher entre temas claros e escuros não estará disponível se o administrador tiver configurado um tema personalizado para o aplicativo.

## <a name="dashboard"></a>Dashboard

![Dashboard](media/overview-iot-central-tour/homepage.png)

* O painel é a primeira página que você vê quando entra em seu aplicativo de IoT Central do Azure. Como construtor, pode personalizar o painel de aplicações para outros utilizadores adicionando azulejos. Para obter mais informações, veja o tutorial [Personalizar a vista de operador do Azure IoT Central](tutorial-customize-operator.md).

* Como operador, pode criar dashboards personalizados e alternar entre eles e o dashboard predefinido. Para saber mais, consulte o artigo "como [criar painéis pessoais do Azure IOT central](howto-create-personal-dashboards.md) ".

## <a name="device-explorer"></a>Explorador de dispositivos

![Página do Explorador](media/overview-iot-central-tour/explorer.png)

A página do Explorer mostra os _dispositivos_ em seu aplicativo de IOT central do Azure agrupado por _modelo de dispositivo_.

* Um modelo do dispositivo define um tipo de dispositivo que pode ligar à aplicação. Para obter mais informações, veja [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](tutorial-define-device-type.md).
* Um dispositivo representa um dispositivo real ou simulado na aplicação. Para obter mais informações, veja [Adicionar um novo dispositivo à aplicação do Azure IoT Central](tutorial-add-device.md).

## <a name="device-sets"></a>Conjuntos de dispositivos

![Página Conjuntos de Dispositivos](media/overview-iot-central-tour/devicesets.png)

A página _conjuntos de dispositivo_ apresenta os conjuntos de dispositivos criados pelo construtor. Um conjunto de dispositivos é uma coleção de dispositivos relacionados. Um construtor define uma consulta para identificar os dispositivos que estão incluídos num conjunto de dispositivos. Utilize conjuntos de dispositivos quando personalizar a análise na aplicação. Para obter mais informações, veja o artigo [Utilizar conjuntos de dispositivos na aplicação do Azure IoT Central](howto-use-device-sets.md).

## <a name="analytics"></a>Análise

![Página de análise](media/overview-iot-central-tour/analytics.png)

A página de análise apresenta gráficos que o ajudam a compreender como se comportam os dispositivos ligados à sua aplicação. Um operador utiliza esta página para monitorizar e investigar problemas dos dispositivos ligados. O construtor pode definir os gráficos apresentados nesta página. Para obter mais informações, veja o artigo [Criar análise personalizada para a aplicação do Azure IoT Central](howto-use-device-sets.md).

## <a name="jobs"></a>Tarefas

![Página de tarefas](media/overview-iot-central-tour/jobs.png)

A página de empregos permite-lhe executar operações de gestão de dispositivos a granel nos seus dispositivos. O construtor utiliza esta página para atualizar as propriedades, as definições e os comandos dos dispositivos. Para obter mais informações, veja o artigo [Executar uma tarefa](howto-run-a-job.md).

## <a name="device-templates"></a>Modelos de dispositivo

![Página modelos de dispositivo](media/overview-iot-central-tour/templates.png)

A página modelos de dispositivo é onde um construtor cria e gerencia os modelos de dispositivo no aplicativo. Um modelo de dispositivo especifica as características do dispositivo tais como:

- Metetria, estado e medidas de eventos.
- Configurações e propriedades.
- Comandos.
- Regras baseadas em eventos ou valores de telemetria.

Para obter mais informações, veja o tutorial [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Exportação de dados contínua

![Página de Exportação contínua de dados](media/overview-iot-central-tour/export.png)

A página contínua de exportação de dados é onde um administrador define como transmitir dados, como a telemetria, a partir da aplicação. Outros serviços podem armazenar os dados exportados ou usá-lo para análise. Para saber mais, consulte o artigo [exportar dados no Azure IOT central](howto-export-data-blob-storage.md) .

## <a name="administration"></a>Administração

![Página Administração](media/overview-iot-central-tour/administration.png)

A página de administração contém links para as ferramentas que um administrador utiliza, como definir utilizadores e funções na aplicação, e personalizar a UI. Para obter mais informações, veja o artigo [Administrar a aplicação do Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Azure IoT Central e está familiarizado com o esquema da IU, o passo seguinte sugerido é concluir o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).

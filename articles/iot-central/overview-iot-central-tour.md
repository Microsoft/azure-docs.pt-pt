---
title: Apresentação da IU do Azure IoT Central | Microsoft Docs
description: Como construtor, familiarize-se com as principais áreas da IU do Azure IoT Central que utiliza para criar uma solução de IoT.
author: dominicbetts
ms.author: dobett
ms.date: 01/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: edc75711d8f727d8b1c9359835c6d5ebd575ff71
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678654"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Apresentação da IU do Azure IoT Central

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


## <a name="use-the-left-navigation-menu"></a>Utilizar o menu de navegação à esquerda

Utilize o menu de navegação à esquerda para aceder as áreas diferentes do aplicativo. Pode expandir ou fechar barra de navegação, selecionando **<** ou **>**:

| Menu | Descrição |
| ---- | ----------- |
| ![Menu de navegação à esquerda](media/overview-iot-central-tour/navigationbar.png) | <ul><li>O **Dashboard** botão Exibe seu dashboard de aplicações. Como um construtor, pode personalizar o dashboard para os operadores. Os utilizadores também podem criar seus próprios dashboards.</li><li>O **Device Explorer** botão lista os dispositivos simulados e real associados a cada modelo de dispositivo na aplicação. Como operador, utilize o **Device Explorer** para gerir os dispositivos ligados.</li><li>O botão **Device Sets** permite-lhe ver e criar conjuntos de dispositivos. Como operador, pode criar conjuntos de dispositivos como uma coleção lógica de dispositivos especificados por uma consulta.</li><li>O botão **Analytics** mostra a análise derivada da telemetria dos dispositivos, para dispositivos e conjuntos de dispositivo. Como operador, pode criar vistas personalizadas com base nos dados dos dispositivos para obter informações da aplicação.</li><li>O botão **Tarefas** permite a gestão de dispositivos em massa ao permitir criar e executar tarefas para realizar atualizações em escala.</li><li>O **modelos de dispositivos** botão mostra as ferramentas de um construtor usa para criar e gerir modelos de dispositivos.</li><li>O **a exportação contínua de dados** botão um administrador para configurar a exportação contínua para outros serviços do Azure como o armazenamento e filas.</li><li>O botão **Administration** mostra as páginas de administração da aplicação, onde um administrador pode gerir as definições, os utilizadores e as funções da aplicação.</li></ul> |

## <a name="search-help-and-support"></a>Pesquisa, ajuda e suporte

O menu superior é apresentado em todas as páginas:

![Barra de ferramentas](media/overview-iot-central-tour/toolbar.png)

- Para procurar dispositivos e modelos de dispositivos, introduza um **pesquisa** valor.
- Para alterar o idioma de interface do Usuário ou o tema, escolha o **definições** ícone.
- Para terminar a aplicação, escolha o **conta** ícone.
- Para obter ajuda e suporte, escolha o menu pendente **Ajuda** para obter uma lista de recursos.

Pode escolher entre um tema claro ou um tema escuro para a IU:

![Escolher um tema para a IU](media/overview-iot-central-tour/themes.png)

## <a name="dashboard"></a>Dashboard

![Dashboard](media/overview-iot-central-tour/homepage.png)

O dashboard é a primeira página que vê quando iniciar sessão na sua aplicação do Azure IoT Central. Como um construtor, pode personalizar o dashboard de aplicações para outros utilizadores através da adição de mosaicos. Para obter mais informações, veja o tutorial [Personalizar a vista de operador do Azure IoT Central](tutorial-customize-operator.md). Os utilizadores também podem [criar seus próprios dashboards pessoais](howto-personalize-dashboard.md).

## <a name="device-explorer"></a>Explorador de dispositivos

![Página do Explorador](media/overview-iot-central-tour/explorer.png)

Mostra a página de Explorador a _dispositivos_ na sua aplicação do Azure IoT Central, agrupada por _modelo de dispositivo_.

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

A página de tarefas permite-lhe realizar operações de gestão de dispositivos em massa nos seus dispositivos. O construtor utiliza esta página para atualizar as propriedades, as definições e os comandos dos dispositivos. Para obter mais informações, veja o artigo [Executar uma tarefa](howto-run-a-job.md).

## <a name="device-templates"></a>Modelos de Dispositivos

![Página de modelos de dispositivo](media/overview-iot-central-tour/templates.png)

A página de modelos de dispositivos é onde um construtor cria e gere os modelos de dispositivos na aplicação. Para obter mais informações, veja o tutorial [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Exportação de Dados Contínuos

![Página de exportação de dados contínua](media/overview-iot-central-tour/export.png)

A página de exportação contínua de dados é em que um administrador define como exportar dados, como telemetria, a partir da aplicação. Outros serviços podem armazenar os dados exportados ou utilizá-la para análise. Para obter mais informações, consulte a [exportar seus dados no Azure IoT Central](howto-export-data.md) artigo.

## <a name="administration"></a>Administração

![Página Administração](media/overview-iot-central-tour/administration.png)

A página de administração contém ligações para as ferramentas utilizadas pelo administrador, por exemplo, para definir utilizadores e funções nas aplicações. Para obter mais informações, veja o artigo [Administrar a aplicação do Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma descrição geral do Azure IoT Central e está familiarizado com o esquema da IU, o passo seguinte sugerido é concluir o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
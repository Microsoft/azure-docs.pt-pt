---
title: Descrição geral do portal do Azure | Microsoft Docs
description: Saiba como navegar na portal do Azure e usá-la para gerenciar serviços
services: azure-portal
keywords: portal
author: kfollis
ms.author: kfollis
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 90bbea1068bb237501daf5e0dfac865ca6b9a12a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579479"
---
# <a name="azure-portal-overview"></a>Descrição geral do portal do Azure

Este artigo apresenta o portal do Azure, identifica os elementos da página do portal e ajuda você a se familiarizar com a experiência de gerenciamento de portal do Azure.

## <a name="what-is-the-azure-portal"></a>O que é o portal do Azure?

O portal do Azure é uma consola unificada baseada na Web que constitui uma alternativa às ferramentas de linha de comandos. Com o portal do Azure, você pode gerenciar sua assinatura do Azure usando uma interface gráfica do usuário. Você pode criar, gerenciar e monitorar tudo, desde aplicativos Web simples até implantações de nuvem complexas. Crie painéis personalizados para uma exibição organizada de recursos. Configure opções de acessibilidade para uma experiência ideal.

A portal do Azure é projetada para resiliência e disponibilidade contínua. Ele tem uma presença em todos os datacenters do Azure. Essa configuração torna o portal do Azure resiliente a falhas de datacenter individuais e evita que a rede diminua os usuários. O portal do Azure atualiza continuamente e não requer nenhum tempo de inatividade para atividades de manutenção.

## <a name="azure-portal-menu"></a>Menu de portal do Azure

Você pode escolher o modo padrão para o menu do Portal. Ele pode ser encaixado ou pode atuar como um painel flutuante.

Quando o menu do portal estiver no modo de submenu, ele ficará oculto até que você precise dele. Selecione o ícone de menu para abrir ou fechar o menu.

![Menu de portal do Azure no modo de submenu](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Se você escolher o modo encaixado para o menu do portal, ele estará sempre visível. Você pode recolher o menu para fornecer mais espaço de trabalho.

![Menu de portal do Azure no modo encaixado](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Página inicial do Azure

Como um novo assinante dos serviços do Azure, a primeira coisa que você vê depois de [entrar no portal](https://portal.azure.com) é a **página inicial do Azure**. Esta página compila os recursos que o ajudarão a obter o máximo de sua assinatura do Azure. Incluímos links para cursos online gratuitos, documentação, serviços principais e sites úteis para manter o atual e gerenciar alterações para sua organização. Para acesso rápido e fácil ao trabalho em andamento, também mostramos uma lista dos recursos visitados mais recentemente. Você não pode personalizar esta página, mas pode escolher se deseja ver o **Azure Home** ou o **painel do Azure** como sua exibição padrão. Na primeira vez que você entrar, há um prompt na parte superior da página em que você pode salvar sua preferência.

![Captura de tela mostrando o seletor de exibição padrão](./media/azure-portal-overview/azure-portal-default-view.png)

O menu portal do Azure e o modo de exibição padrão do Azure podem ser alterados nas **configurações do portal**. Se você alterar sua seleção, a alteração será aplicada imediatamente.

![Captura de tela mostrando o seletor de exibição padrão](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Painel do Azure

Os painéis fornecem uma visão focada dos recursos em sua assinatura que são mais importantes para você. Nós lhe fornecemos um painel padrão para você começar. Você pode personalizar esse painel para trazer os recursos usados com frequência em uma única exibição. As alterações feitas no modo de exibição padrão afetam apenas a sua experiência. No entanto, você pode criar painéis adicionais para seu próprio uso ou publicar seus painéis personalizados e compartilhá-los com outros usuários em sua organização. Para obter mais informações, consulte [criar e compartilhar painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Contornar o portal

É útil entender o layout básico do portal e como interagir com ele. Aqui, apresentaremos os componentes da interface do usuário e parte da terminologia que usamos para fornecer instruções. Para obter um tour mais detalhado do portal, consulte a lição do curso [navegar no portal](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

O menu portal do Azure e o cabeçalho da página são elementos globais que estão sempre presentes. Esses recursos persistentes são o "Shell" para a interface do usuário associada a cada serviço ou recurso individual e o cabeçalho fornece acesso a controles globais. A página de configuração (às vezes chamada de "lâmina") para um recurso também pode ter um menu de recursos para ajudá-lo a se mover entre os recursos.

A figura abaixo rotula os elementos básicos da portal do Azure, cada um dos quais são descritos na tabela a seguir.

![Captura de tela mostrando o modo de exibição do portal inteiro e a chave para elementos da interface do usuário](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Captura de tela mostrando o menu expandido do portal](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Chave|Descrição
|:---:|---|
|1|Cabeçalho da página. Aparece na parte superior de cada página do portal e contém elementos globais.|
|2| Pesquisa global. Use a barra de pesquisa para localizar rapidamente um recurso específico, um serviço ou uma documentação.|
|3|Controles globais. Como todos os elementos globais, esses recursos persistem pelo portal e incluem: Cloud Shell, filtro de assinatura, notificações, configurações do portal, ajuda e suporte e nos enviar comentários.|
|4|Sua conta. Exiba informações sobre sua conta, alterne para os diretórios, saia ou entre com uma conta diferente.|
|5|Menu do Portal. O menu do portal é um elemento global que ajuda você a navegar entre serviços. Às vezes chamado de barra lateral, o modo de menu do portal pode ser alterado nas **configurações do portal**.|
|6|Menu de recursos. Muitos serviços incluem um menu de recursos para ajudá-lo a gerenciar o serviço. Você pode ver esse elemento referido como o painel esquerdo.|
|7|Barra de comandos. Os controles na barra de comandos são contextuais para seu foco atual.|
|8|Painel de trabalho.  Exibe os detalhes sobre o recurso que está atualmente em foco.|
|9|Breadcrumb. Você pode usar os links de navegação estrutural para voltar um nível em seu fluxo de trabalho.|
|10|Controle mestre para criar um novo recurso na assinatura atual. Expanda ou abra o menu do portal para localizar **+ criar um recurso**. Pesquise ou procure o tipo de recurso que você deseja criar no Azure Marketplace.|
|11|Sua lista de favoritos. Consulte [Adicionar, remover e classificar favoritos](../azure-portal/azure-portal-add-remove-sort-favorites.md) para saber como personalizar a lista.|

## <a name="get-started-with-services"></a>Introdução aos serviços

Se você for um novo assinante, precisará criar um recurso antes que haja qualquer coisa a ser gerenciada. Selecione **+ criar um recurso** para exibir os serviços disponíveis no Azure Marketplace. Você encontrará aplicativos e serviços de centenas de provedores aqui, todos certificados para execução no Azure.

Preenchemos previamente seus favoritos na barra lateral com links para os serviços mais usados.  Para exibir todos os serviços disponíveis, selecione **todos os serviços** na barra lateral.

> [!TIP]
> A maneira mais rápida de localizar um recurso, um serviço ou uma documentação é usar a *pesquisa* no cabeçalho global. Use os links de navegação estrutural para voltar às páginas anteriores.
>
Assista a este vídeo para ver uma demonstração sobre como usar a pesquisa global no portal do Azure.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Como usar a pesquisa global no portal do Azure](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre onde executar portal do Azure em [dispositivos e navegadores com suporte](../azure-portal/azure-portal-supported-browsers-devices.md)
* Mantenha-se conectado em qualquer lugar com [aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* Integre e configure seu ambiente de nuvem com o [centro de início rápido do Azure](../azure-portal/azure-portal-quickstart-center.md)

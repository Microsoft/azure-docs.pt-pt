---
title: Descrição geral do portal do Azure | Microsoft Docs
description: O portal Azure é uma interface de utilizador gráfica que pode utilizar para gerir os seus serviços Azure. Aprenda a navegar e a encontrar recursos no portal Azure.
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: overview
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 4a00c1dcf973098895e80d5e3d656efd4a35d0d0
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147050"
---
# <a name="azure-portal-overview"></a>Descrição geral do portal do Azure

Este artigo introduz o portal Azure, identifica elementos de página do portal e ajuda-o a familiarizar-se com a experiência de gestão do portal Azure.

## <a name="what-is-the-azure-portal"></a>O que é o portal do Azure?

O portal do Azure é uma consola unificada baseada na Web que constitui uma alternativa às ferramentas de linha de comandos. Com o portal do Azure, pode gerir a sua subscrição do Azure através de uma interface gráfica de utilizador. Pode criar, gerir e monitorizar tudo, desde aplicações Web simples a implementações na cloud complexas. Crie dashboards personalizados para uma vista organizada dos recursos. Configure opções de acessibilidade para uma experiência ideal.

O portal do Azure foi concebido para proporcionar resiliência e disponibilidade contínua. Tem presença em todos os datacenteres da Azure. Esta configuração torna o portal Azure resiliente a falhas individuais do datacenter e evita abrandamentos de rede por estar perto dos utilizadores. O portal do Azure é atualizado continuamente e não requer nenhum tempo de inatividade para atividades de manutenção.

## <a name="azure-portal-menu"></a>Menu do portal Azure

Pode escolher o modo predefinido para o menu do portal. Pode ser ancorado ou pode funcionar como um painel de voo.

Quando o menu do portal está em modo flyout, está escondido até precisar. Selecione o ícone do menu para abrir ou fechar o menu.

![Menu do portal Azure no modo flyout](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Se escolher o modo docked para o menu do portal, estará sempre visível. Pode colapsar o menu para fornecer mais espaço de trabalho.

![Menu do portal Azure em modo ancorado](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Casa Azure

Como novo subscritor dos serviços Azure, a primeira coisa que vê depois de [iniciar seduca no portal](https://portal.azure.com) é a **Azure Home.** Esta página compila recursos que o ajudam a tirar o máximo proveito da sua subscrição do Azure. Incluímos links para cursos online gratuitos, documentação, serviços centrais e sites úteis para se manter atual e gerir a mudança para a sua organização. Para um acesso rápido e fácil ao trabalho em curso, também mostramos uma lista dos seus recursos mais recentemente visitados. Não é possível personalizar esta página, mas pode escolher se vê **o Azure Home** ou **o Azure Dashboard** como a sua vista padrão. A primeira vez que iniciar sôs o seu registo, há uma solicitação no topo da página onde pode guardar a sua preferência.

![Screenshot mostrando onde guardar a sua preferência.](./media/azure-portal-overview/azure-portal-default-view.png)

Tanto o menu do portal Azure como o padrão Azure podem ser alterados nas **definições do Portal** . Se alterar a sua seleção, a alteração é imediatamente aplicada.

![Screenshot mostrando seletor de visualização padrão](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Painel Azure

Os dashboards fornecem uma visão focada dos recursos na sua subscrição que mais lhe interessam. Demos-lhe um painel padrão para começar. Pode personalizar este dashboard para trazer os recursos que utiliza frequentemente para uma única vista. Quaisquer alterações que faça à vista predefinida afetam apenas a sua experiência. No entanto, pode criar dashboards adicionais para uso próprio ou publicar os seus dashboards personalizados e partilhá-los com outros utilizadores da sua organização. Para obter mais informações, consulte [Criar e partilhar dashboards no portal Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Contornar o portal

É útil entender o layout do portal básico e como interagir com ele. Aqui, vamos introduzir os componentes da interface do utilizador e algumas das terminologias que usamos para dar instruções. Para uma visita mais detalhada ao portal, consulte a lição de curso [Navegar no portal.](/learn/modules/tour-azure-portal/3-navigate-the-portal)

O menu do portal Azure e o cabeçalho da página são elementos globais que estão sempre presentes. Estas características persistentes são a "concha" para a interface de utilizador associada a cada serviço ou funcionalidade individual e o cabeçalho fornece acesso a controlos globais. A página de configuração (por vezes referida como "lâmina") para um recurso também pode ter um menu de recursos para ajudá-lo a mover-se entre as funcionalidades.

A figura abaixo rotula os elementos básicos do portal Azure, cada um dos quais são descritos na tabela seguinte.

![Screenshot mostrando a vista do portal de ecrã completo e a chave para elementos de UI](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Screenshot mostrando menu de portal expandido](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Chave|Descrição
|:---:|---|
|1|Cabeçalho de página. Aparece no topo de cada página do portal e detém elementos globais.|
|2| Procura global. Utilize a barra de pesquisa para encontrar rapidamente um recurso específico, um serviço ou documentação.|
|3|Controlos globais. Como todos os elementos globais, estas funcionalidades persistem em todo o portal e incluem: Cloud Shell, filtro de subscrição, notificações, configurações do portal, ajuda e suporte, e enviam-nos feedback.|
|4|A sua conta. Ver informações sobre a sua conta, trocar diretórios, assinar ou iniciar sposição com uma conta diferente.|
|5|Menu portal. O menu do portal é um elemento global que o ajuda a navegar entre serviços. Por vezes referido como barra lateral, o modo de menu do portal pode ser alterado nas **definições do Portal** .|
|6|Menu de recursos. Muitos serviços incluem um menu de recursos para ajudá-lo a gerir o serviço. Pode ver este elemento referido como o painel esquerdo.|
|7|Barra de comando. Os controlos na barra de comando são contextuais ao seu foco atual.|
|8|Painel de trabalho.  Apresenta os detalhes sobre o recurso que está atualmente em foco.|
|9|Migalha de pão. Pode utilizar os links de migalhas de pão para retrocê-lo para retroc retrocamar um nível no seu fluxo de trabalho.|
|10|Controlo principal para criar um novo recurso na subscrição atual. Expandir ou abrir o menu do portal para encontrar **+ Criar um recurso.** Procure ou navegue no Azure Marketplace pelo tipo de recurso que pretende criar.|
|11|A tua lista de favoritos. Ver [Adicionar, remover e classificar os favoritos](../azure-portal/azure-portal-add-remove-sort-favorites.md) para aprender a personalizar a lista.|

## <a name="get-started-with-services"></a>Começar com serviços

Se for um novo subscritor, terá de criar um recurso antes de haver algo para gerir. Selecione **+ Crie um recurso** para visualizar os serviços disponíveis no Mercado Azure. Você encontrará aplicações e serviços de centenas de fornecedores aqui, todos certificados para executar em Azure.

Preobitámos os seus Favoritos na barra lateral com links para serviços comumente utilizados.  Para ver todos os serviços disponíveis, selecione **Todos os serviços** da barra lateral.

> [!TIP]
> A forma mais rápida de encontrar um recurso, serviço ou documentação é usar a *Pesquisa* no cabeçalho global. Utilize os links de migalhas de pão para voltar às páginas anteriores.
>
Veja este vídeo para uma demonstração sobre como usar a pesquisa global no portal Azure.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Como utilizar a pesquisa global no portal Azure](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre onde executar o portal Azure em [navegadores e dispositivos suportados](../azure-portal/azure-portal-supported-browsers-devices.md)
* Mantenha-se conectado em movimento com [a aplicação móvel Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* A bordo e configurar o seu ambiente em nuvem com o [Azure Quickstart Center](../azure-portal/azure-portal-quickstart-center.md)
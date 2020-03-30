---
title: Descrição geral do portal do Azure | Microsoft Docs
description: O portal Azure é uma interface gráfica de utilizador que pode utilizar para gerir os seus serviços Azure. Aprenda a navegar e encontre recursos no portal Azure.
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5e02e791185db3713c67b8ff97b8f7eebe9da77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310735"
---
# <a name="azure-portal-overview"></a>Descrição geral do portal do Azure

Este artigo introduz o portal Azure, identifica elementos de página do portal e ajuda-o a familiarizar-se com a experiência de gestão do portal Azure.

## <a name="what-is-the-azure-portal"></a>O que é o portal do Azure?

O portal do Azure é uma consola unificada baseada na Web que constitui uma alternativa às ferramentas de linha de comandos. Com o portal Azure, pode gerir a sua subscrição Azure utilizando uma interface gráfica de utilizador. Você pode construir, gerir e monitorizar tudo, desde aplicações web simples até implementações complexas em nuvem. Crie dashboards personalizados para uma visão organizada dos recursos. Configure as opções de acessibilidade para uma experiência ideal.

O portal Azure foi concebido para resiliência e disponibilidade contínua. Tem presença em todos os centros de dados do Azure. Esta configuração torna o portal Azure resiliente a falhas individuais do datacenter e evita abrandamentos da rede por estar perto dos utilizadores. O portal Azure atualiza continuamente e não necessita de tempo de paragem para as atividades de manutenção.

## <a name="azure-portal-menu"></a>Menu do portal Azure

Pode escolher o modo predefinido para o menu do portal. Pode ser ancorado ou pode funcionar como um painel de flyout.

Quando o menu do portal está em modo flyout, está escondido até precisar. Selecione o ícone do menu para abrir ou fechar o menu.

![Menu do portal Azure em modo flyout](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Se escolher o modo atracado para o menu do portal, será sempre visível. Você pode colapsar o menu para fornecer mais espaço de trabalho.

![Menu do portal Azure em modo ancorado](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Casa Azure

Como novo subscritor dos serviços Azure, a primeira coisa que vê depois de [iniciar sessão no portal](https://portal.azure.com) é o **Azure Home.** Esta página compila recursos que o ajudam a tirar o máximo partido da sua subscrição Azure. Incluímos links para cursos online gratuitos, documentação, serviços centrais e sites úteis para manter a atual e gerir a mudança para a sua organização. Para um acesso rápido e fácil ao trabalho em curso, também mostramos uma lista dos seus recursos mais recentemente visitados. Não pode personalizar esta página, mas pode escolher se vê o **Azure Home** ou **o Azure Dashboard** como a sua visão padrão. A primeira vez que faz sessão, há um pedido no topo da página onde pode salvar a sua preferência.

![Screenshot mostrando seletor de visualização padrão](./media/azure-portal-overview/azure-portal-default-view.png)

Tanto o menu do portal Azure como a vista padrão Azure podem ser alterados nas **definições**do Portal . Se alterar a sua seleção, a alteração é imediatamente aplicada.

![Screenshot mostrando seletor de visualização padrão](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Painel de Instrumentos Azure

Os dashboards proporcionam uma visão focada dos recursos da sua subscrição que mais lhe interessam. Demos-lhe um painel padrão para começar. Pode personalizar este dashboard para trazer os recursos que utiliza frequentemente para uma única vista. Quaisquer alterações que faça na visão padrão afetam apenas a sua experiência. No entanto, pode criar dashboards adicionais para seu próprio uso ou publicar os seus dashboards personalizados e partilhá-los com outros utilizadores na sua organização. Para mais informações, consulte [Criar e partilhar dashboards no portal Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Dar a volta ao portal

É útil entender o layout básico do portal e como interagir com ele. Aqui, vamos introduzir os componentes da interface do utilizador e algumas das terminologias que usamos para dar instruções. Para um passeio mais detalhado pelo portal, consulte a lição do curso [Navegar pelo portal.](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)

O menu do portal Azure e o cabeçalho de página são elementos globais que estão sempre presentes. Estas características persistentes são a "concha" para a interface do utilizador associada a cada serviço ou funcionalidade individual e o cabeçalho proporciona acesso aos controlos globais. A página de configuração (por vezes referida como "lâmina") para um recurso também pode ter um menu de recursos para ajudá-lo a mover-se entre funcionalidades.

A figura abaixo rotula os elementos básicos do portal Azure, cada um dos quais são descritos na tabela seguinte.

![Screenshot mostrando visão do portal de ecrã completo e chave para elementos UI](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Screenshot mostrando menu de portal expandido](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Chave|Descrição
|:---:|---|
|1|Cabeçalho de página. Aparece no topo de cada página do portal e detém elementos globais.|
|2| Procura global. Utilize a barra de pesquisa para encontrar rapidamente um recurso específico, um serviço ou documentação.|
|3|Controlos globais. Como todos os elementos globais, estas funcionalidades persistem em todo o portal e incluem: Cloud Shell, filtro de subscrição, notificações, configurações do portal, ajuda e suporte, e enviam-nos feedback.|
|4|A sua conta. Consulte informações sobre a sua conta, troque de diretórios, assine ou inscreva-se com uma conta diferente.|
|5|Menu do portal. O menu do portal é um elemento global que o ajuda a navegar entre serviços. Por vezes referido como barra lateral, o modo menu do portal pode ser alterado nas **definições**do Portal .|
|6|Menu de recursos. Muitos serviços incluem um menu de recursos para ajudá-lo a gerir o serviço. Pode ver este elemento referido como o painel esquerdo.|
|7|Barra de comando. Os controlos na barra de comando são contextuais ao seu foco atual.|
|8|Painel de trabalho.  Apresenta os detalhes sobre o recurso que está atualmente em foco.|
|9|Migalhas de pão. Pode utilizar os links de migalhas de pão para voltar a um nível no seu fluxo de trabalho.|
|10|Controlo principal para criar um novo recurso na subscrição atual. Expandir ou abrir o menu do portal para encontrar **+ Criar um recurso**. Procure ou navegue no Azure Marketplace pelo tipo de recurso que pretende criar.|
|11|A tua lista de favoritos. Ver [Adicionar, remover e classificar os favoritos](../azure-portal/azure-portal-add-remove-sort-favorites.md) para aprender a personalizar a lista.|

## <a name="get-started-with-services"></a>Começar com os serviços

Se for um novo assinante, terá de criar um recurso antes de haver algo para gerir. Selecione **+ Crie um recurso** para ver os serviços disponíveis no Mercado Azure. Você encontrará aplicações e serviços de centenas de fornecedores aqui, todos certificados para executar no Azure.

Prepovoámos os seus Favoritos na barra lateral com ligações a serviços comumente usados.  Para ver todos os serviços disponíveis, selecione **Todos os serviços** da barra lateral.

> [!TIP]
> A forma mais rápida de encontrar um recurso, serviço ou documentação é usar a *Search* no cabeçalho global. Utilize os links de migalhas de pão para voltar às páginas anteriores.
>
Veja este vídeo para uma demonstração sobre como usar a pesquisa global no portal Azure.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Como usar a pesquisa global no portal Azure](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre onde executar portal Azure em [navegadores e dispositivos Suportados](../azure-portal/azure-portal-supported-browsers-devices.md)
* Mantenha-se conectado em movimento com [a aplicação móvel Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* A bordo e instale o seu ambiente em nuvem com o [Azure Quickstart Center](../azure-portal/azure-portal-quickstart-center.md)

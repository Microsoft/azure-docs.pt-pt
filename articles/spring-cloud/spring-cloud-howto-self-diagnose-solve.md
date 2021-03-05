---
title: Como auto-diagnosticar e resolver problemas na Nuvem de primavera de Azure
description: Aprenda a auto-diagnosticar e resolver problemas em Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: c262ba87698c04da69728f7a370040c0679ec44e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122872"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Como auto-diagnosticar e resolver problemas na Nuvem de primavera de Azure

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

O diagnóstico Azure Spring Cloud é uma experiência interativa para resolver problemas na sua aplicação sem configuração. O diagnóstico Azure Spring Cloud identifica problemas e guia-o para informações que ajudam a resolver problemas e resolver problemas.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Uma instância de serviço Azure Spring Cloud implantada. Acompanhe o nosso [quickstart na implementação de uma aplicação através do CLI Azure](spring-cloud-quickstart.md) para começar.
* Pelo menos uma aplicação já criada na sua instância de serviço.

## <a name="navigate-to-the-diagnostics-page"></a>Navegue para a página de diagnósticos
1. Inicie sessão no Portal do Azure.
2. Vá à sua página de **visão geral da** nuvem de primavera Azure.
3. Selecione **diagnosticar e resolver problemas** no painel de navegação esquerdo.

 ![Diagnosticar, resolver diálogo](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Pesquisa de problemas registados
Para encontrar um problema, pode pesquisar digitando uma palavra-chave ou clicando em grupo de solução para explorar tudo nessa categoria.

 ![Problemas de pesquisa](media/spring-cloud-diagnose/search-detectors.png)

Seleção do **Config Server Health Check**, **Config Server Health Status** ou **Config Server Update History** apresentará vários resultados.

![Opções de edições](media/spring-cloud-diagnose/detectors-options.png)

Encontre o detetor de alvos e clique nele para executar. Um resumo dos diagnósticos será mostrado depois de executar o detetor. Pode selecionar **Ver Relatório Completo** para verificar detalhes de diagnóstico ou clicar no botão **'Mostrar menu'** para voltar à lista de detetores.

 ![Diagnóstico sumário](media/spring-cloud-diagnose/summary-diagnostics.png)

Na página de detalhes de diagnóstico, pode alterar o intervalo de tempo de diagnóstico com o controlador no canto superior direito. Para ver mais métricas ou registos, alterne cada diagnóstico. Pode haver um atraso de 15 minutos para métricas e registos.

 ![Detalhes do diagnóstico](media/spring-cloud-diagnose/diagnostics-details.png)

Alguns resultados contêm documentação relacionada.

 ![Detalhes relacionados](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Passos seguintes
* [Monitorar recursos do Spring Cloud com alertas e grupos de ações](spring-cloud-tutorial-alerts-action-groups.md)
* [Controlos de segurança do Serviço Azure Spring Cloud](spring-cloud-concept-security-controls.md)
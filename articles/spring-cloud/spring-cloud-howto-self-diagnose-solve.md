---
title: Como auto-diagnosticar e resolver problemas na Nuvem de primavera de Azure
description: Aprenda a auto-diagnosticar e resolver problemas em Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.openlocfilehash: f1d5161b0efe0b8d9b9c0b84ac149050c3823ab6
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84429471"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Como auto-diagnosticar e resolver problemas na Nuvem de primavera de Azure
O diagnóstico do Serviço de Aplicações é uma experiência interativa para ajudá-lo a resolver problemas na sua aplicação. Não é necessária qualquer configuração. Quando encontra problemas, o diagnóstico do Serviço de Aplicações aponta o que está errado e guia-o para informações que ajudam a resolver problemas e resolver o problema.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Uma instância de serviço Azure Spring Cloud implantada. Acompanhe o nosso [quickstart na implementação de uma aplicação através do CLI Azure](spring-cloud-quickstart-launch-app-cli.md) para começar.
* Pelo menos uma aplicação já criada na sua instância de serviço.

## <a name="navigate-to-the-diagnostics-page"></a>Navegue para a página de diagnósticos
1. Inicie sessão no Portal do Azure.
2. Vá à sua página de **visão geral da** nuvem de primavera Azure.
3. Abra **o diagnóstico e resolva problemas** no menu do lado esquerdo da página.

 ![Diagnosticar, resolver diálogo](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Pesquisa de problemas registados
Para encontrar um problema, pode pesquisar digitando uma palavra-chave ou clicando em grupo de solução para explorar tudo nessa categoria.

 ![Problemas de pesquisa](media/spring-cloud-diagnose/search-detectors.png)

Seleção do **Config Server Health Check**, **Config Server Health Status**ou **Config Server Update History** apresentará vários resultados.

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
---
title: Como auto-diagnosticar e resolver problemas na Nuvem de primavera de Azure
description: Aprenda a auto-diagnosticar e resolver problemas em Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.openlocfilehash: c4eada891f68a21732ab9a19548cbdfe1a3833a8
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325853"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Como auto-diagnosticar e resolver problemas na Nuvem de primavera de Azure
Este tutorial mostra-lhe como auto-diagnosticar e resolver problemas para a Azure Spring Cloud. Fornece uma maneira fácil de recolher métricas e registos.

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

## <a name="next-steps"></a>Próximos passos
* [Monitorar recursos do Spring Cloud com alertas e grupos de ações](spring-cloud-tutorial-alerts-action-groups.md)
* [Controlos de segurança do Serviço Azure Spring Cloud](spring-cloud-concept-security-controls.md)
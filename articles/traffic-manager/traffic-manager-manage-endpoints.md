---
title: Gerir pontos finais no Traffic Manager do Azure | Microsoft Docs
description: Este artigo ajuda-o a adicionar, remover, ativar e desativar pontos finais no Traffic Manager do Azure.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: kumud
ms.openlocfilehash: ea7b5b087a5cfb2a3eefaef7336de4a6acf7f116
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777537"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>Adicionar, desativar, ativar ou eliminar pontos finais

A funcionalidade Web Apps no Serviço de Aplicações do Azure já fornece ativação pós-falha e a funcionalidade de encaminhamento de tráfego round robin para sites num centro de dados, independentemente do modo do site. O Traffic Manager do Azure permite-lhe especificar a ativação pós-falha e o encaminhamento de tráfego round robin para sites e serviços em nuvem em diferentes centros de dados. O primeiro passo necessário para fornecer essa funcionalidade é adicionar o ponto final de serviço em nuvem ou do site ao Traffic Manager.

Também pode desativar pontos finais individuais que fazem parte de um perfil do Traffic Manager. A desativação de um ponto final deixa-o como parte do perfil, mas o perfil funciona como se o ponto final não estivesse incluído. Esta ação é útil para remover temporariamente um ponto final que está no modo de manutenção ou que vai ser implementado. Quando o ponto final está novamente em funcionamento, pode ativá-lo.

> [!NOTE]
> Desativar um ponto final não tem nada a ver com o estado de implementação no Azure. Um ponto final em bom estado continua ativo para receber tráfego, mesmo quando desativado no Gestor de Tráfego. Além disso, a desativação de um ponto final num perfil não afeta o estado noutro perfil.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>Para adicionar um serviço cloud ou um ponto final de serviço de Aplicações a um perfil do Gestor de Tráfego

1. Num browser, inicie sessão no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, procure o nome do **perfil do Gestor de Tráfego** que pretende modificar e, em seguida, clique no perfil do Gestor de Tráfego nos resultados apresentados.
3. No painel **Perfil do Gestor de Tráfego** , na secção **Definições**, clique em **Pontos Finais**.
4. No painel **Pontos Finais** apresentado, clique em **Adicionar**.
5. No painel **Adicionar ponto final**, faça o seguinte:
    1. Em **Tipo**, clique em **Ponto final do Azure**.
    2. Indique um **Nome** pelo qual pretende reconhecer este ponto final.
    3. Em **Tipo de recurso de destino**, na lista pendente, escolha o tipo de recurso adequado.
    4. Para o **Recurso de destino**, clique no seletor **Escolher...** para listar recursos na mesma subscrição no **painel de Recursos**. No painel **Recurso** apresentado, selecione o serviço que pretende adicionar como o primeiro ponto final.
    5. Em **Prioridade**, selecione **1**. Se estiver em bom estado de funcionamento, todo o tráfego vai para este ponto final.
    6. Mantenha a caixa **Adicionar como desativado** desmarcada.
    7. Clique em **OK**
6.  Repita os passos 4 e 5 para adicionar o ponto final do Azure seguinte. Adicione-o com o valor de **Prioridade** definido em **2**.
7.  Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados no painel **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

> [!NOTE]
> Depois de adicionar ou remover um ponto final de um perfil com o método de encaminhamento de tráfego *Ativação pós-falha*, a lista de prioridade de ativação pós-falha não pode ser ordenada da forma que pretende. Pode ajustar apenas a ordem da Lista de Prioridade de Ativação Pós-falha na Página de configuração. Para obter mais informações, consulte [Configurar o Encaminhamento de Tráfego de Ativação Pós-falha](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para desativar um ponto final

1. Num browser, inicie sessão no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, procure o nome do **perfil do Gestor de Tráfego** que pretende modificar e, em seguida, clique no perfil do Gestor de Tráfego nos resultados apresentados.
3. No painel **Perfil do Gestor de Tráfego** , na secção **Definições**, clique em **Pontos Finais**. 
4. Clique no ponto final que pretende desativar e, em seguida, no painel **Ponto final** que é apresentado, clique em **Editar**.
5. No painel **Ponto final**, altere o estado do ponto final para **Desativado** e, em seguida, clique em **Guardar**.
6. Os clientes continuam a enviar tráfego para o ponto final com a duração do Tempo de Vida (TTL). Pode alterar o TTL na página Configuração do perfil do Gestor de Tráfego.

## <a name="to-enable-an-endpoint"></a>Para ativar um ponto final

1. Num browser, inicie sessão no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, procure o nome do **perfil do Gestor de Tráfego** que pretende modificar e, em seguida, clique no perfil do Gestor de Tráfego nos resultados apresentados.
3. No painel **Perfil do Gestor de Tráfego** , na secção **Definições**, clique em **Pontos Finais**. 
4. Clique no ponto final que pretende desativar e, em seguida, no painel **Ponto final** que é apresentado, clique em **Editar**.
5. No painel **Ponto final**, altere o estado do ponto final para **Ativado** e, em seguida, clique em **Guardar**.
6. Os clientes continuam a enviar tráfego para o ponto final com a duração do Tempo de Vida (TTL). Pode alterar o TTL na página Configuração do perfil do Gestor de Tráfego.

## <a name="to-delete-an-endpoint"></a>Para eliminar um ponto final

1. Num browser, inicie sessão no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, procure o nome do **perfil do Gestor de Tráfego** que pretende modificar e, em seguida, clique no perfil do Gestor de Tráfego nos resultados apresentados.
3. No painel **Perfil do Gestor de Tráfego** , na secção **Definições**, clique em **Pontos Finais**. 
4. Clique no ponto final que pretende desativar e, em seguida, no painel **Ponto final** que é apresentado, clique em **Editar**.
5. No painel **Ponto final**, altere o estado do ponto final para **Ativado** e, em seguida, clique em **Guardar**.


## <a name="next-steps"></a>Passos Seguintes

* [Gerir perfis do Gestor de Tráfego](traffic-manager-manage-profiles.md)
* [Configurar métodos de encaminhamento](traffic-manager-configure-routing-method.md)
* [Resolução de problemas do estado degradado do Gestor de Tráfego](traffic-manager-troubleshooting-degraded.md)
* [Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
* [Operações do Gestor de Tráfego (Referência da API REST)](https://go.microsoft.com/fwlink/p/?LinkID=313584)


---
title: Configurar o roteamento de tráfego de prioridade-Gerenciador de tráfego do Azure
description: Este artigo explica como configurar o método de roteamento de tráfego de prioridade no Gerenciador de tráfego
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: f9954c7733c30efaea4a74e04949556a3a8617fd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040358"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Configurar o método de roteamento de tráfego prioritário no Gerenciador de tráfego

Independentemente do modo de site, os sites do Azure já fornecem a funcionalidade de failover para sites em um datacenter (também conhecido como região). O Gerenciador de tráfego fornece failover para sites em data centers diferentes.

Um padrão comum para failover de serviço é enviar tráfego para um serviço primário e fornecer um conjunto de serviços de backup idênticos para failover. As etapas a seguir explicam como configurar esse failover priorizado com os serviços de nuvem e sites do Azure:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Para configurar o método de roteamento de tráfego de prioridade

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure os **perfis do Gerenciador de tráfego** e clique no nome do perfil para o qual você deseja configurar o método de roteamento.
3. Na folha do **perfil do Gerenciador de tráfego** , verifique se os serviços de nuvem e os sites que você deseja incluir na sua configuração estão presentes.
4. Na seção **configurações** , clique em **configuração**e, na folha **configuração** , conclua da seguinte maneira:
    1. Para **configurações do método de roteamento de tráfego**, verifique se o método de roteamento de tráfego é **prioridade**. Se não estiver, clique em **prioridade** na lista suspensa.
    2. Defina as **configurações do monitor de ponto de extremidade** idênticas para todos os pontos de extremidade desse perfil, da seguinte maneira:
        1. Selecione o **protocolo**apropriado e especifique o número da **porta** . 
        2. Para **caminho** , digite uma barra */* . Para monitorar os pontos de extremidade, você deve especificar um caminho e um nome de arquivo. Uma barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão).
        3. Na parte superior da página, clique em **salvar**.
5. Na seção **configurações** , clique em **pontos de extremidade**.
6. Na folha **pontos de extremidade** , examine a ordem de prioridade para seus pontos de extremidade. Quando você seleciona o método de roteamento de tráfego de **prioridade** , a ordem dos pontos de extremidade selecionados é importante. Verifique a ordem de prioridade dos pontos de extremidade.  O ponto de extremidade primário está no topo. Faça uma verificação dupla na ordem em que ela é exibida. todas as solicitações serão roteadas para o primeiro ponto de extremidade e, se o Gerenciador de tráfego detectar que ele não está íntegro, o tráfego fará failover automaticamente para o próximo ponto de extremidade. 
7. Para alterar a ordem de prioridade do ponto de extremidade, clique no ponto de extremidade e, na folha do **ponto de extremidade** que é exibida, clique em **Editar** e altere o valor de **prioridade** conforme necessário. 
8. Clique em **salvar** para salvar a alteração das configurações do ponto de extremidade.
9. Depois de concluir as alterações de configuração, clique em **salvar** na parte inferior da página.
10. Teste as alterações em sua configuração da seguinte maneira:
    1.  Na barra de pesquisa do portal, procure o nome do perfil do Gerenciador de tráfego e clique no perfil do Gerenciador de tráfego nos resultados exibidos.
    2.  Na folha perfil do **Gerenciador de tráfego** , clique em **visão geral**.
    3.  A folha **perfil do Traffic Manager** exibe o nome DNS do seu perfil do Gerenciador de tráfego recém-criado. Isso pode ser usado por qualquer cliente (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto conforme determinado pelo tipo de roteamento. Nesse caso, todas as solicitações são roteadas para o primeiro ponto de extremidade e, se o Gerenciador de tráfego detectar que ele não está íntegro, o tráfego fará failover automaticamente para o próximo ponto de extremidade.
11. Depois que o perfil do Gerenciador de tráfego estiver funcionando, edite o registro DNS em seu servidor DNS autoritativo para apontar o nome de domínio da empresa para o nome de domínio do Traffic Manager.

![Configurando o método de roteamento de tráfego prioritário usando o Gerenciador de tráfego][1]

## <a name="next-steps"></a>Passos seguintes


- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md).
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as configurações do Gerenciador de tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png
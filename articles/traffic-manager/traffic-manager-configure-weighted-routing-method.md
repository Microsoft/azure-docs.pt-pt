---
title: Configurar roteamento de tráfego de Round Robin ponderado-Gerenciador de tráfego do Azure
description: Este artigo explica como balancear a carga do tráfego usando um método Round Robin no Gerenciador de tráfego
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: 0bfed558ec8db0ef715dad044c3965c1b1d8052b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040339"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Configurar o método de roteamento de tráfego ponderado no Gerenciador de tráfego

Um padrão de método de roteamento de tráfego comum é fornecer um conjunto de pontos de extremidade idênticos, que incluem serviços de nuvem e sites, e enviar tráfego para cada um igualmente. As etapas a seguir descrevem como configurar esse tipo de método de roteamento de tráfego.

> [!NOTE]
> O aplicativo Web do Azure já fornece a funcionalidade de balanceamento de carga Round Robin para sites dentro de uma região do Azure (que pode abranger vários data centers). O Gerenciador de tráfego permite distribuir o tráfego entre sites em diferentes data centers.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Para configurar o método de roteamento de tráfego ponderado

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure os **perfis do Gerenciador de tráfego** e clique no nome do perfil para o qual você deseja configurar o método de roteamento.
3. Na folha do **perfil do Gerenciador de tráfego** , verifique se os serviços de nuvem e os sites que você deseja incluir na sua configuração estão presentes.
4. Na seção **configurações** , clique em **configuração**e, na folha **configuração** , conclua da seguinte maneira:
    1. Para **configurações do método de roteamento de tráfego**, verifique se o método de roteamento de tráfego é **ponderado**. Se não estiver, clique em **ponderado** na lista suspensa.
    2. Defina as **configurações do monitor de ponto de extremidade** idênticas para todos os pontos de extremidade desse perfil, da seguinte maneira:
        1. Selecione o **protocolo**apropriado e especifique o número da **porta** . 
        2. Para **caminho** , digite uma barra */* . Para monitorar os pontos de extremidade, você deve especificar um caminho e um nome de arquivo. Uma barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão).
        3. Na parte superior da página, clique em **salvar**.
5. Teste as alterações em sua configuração da seguinte maneira:
    1.  Na barra de pesquisa do portal, procure o nome do perfil do Gerenciador de tráfego e clique no perfil do Gerenciador de tráfego nos resultados exibidos.
    2.  Na folha perfil do **Gerenciador de tráfego** , clique em **visão geral**.
    3.  A folha **perfil do Traffic Manager** exibe o nome DNS do seu perfil do Gerenciador de tráfego recém-criado. Isso pode ser usado por qualquer cliente (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto conforme determinado pelo tipo de roteamento. Nesse caso, todas as solicitações são roteadas para cada ponto de extremidade em um modo Round Robin.
6. Depois que o perfil do Gerenciador de tráfego estiver funcionando, edite o registro DNS em seu servidor DNS autoritativo para apontar o nome de domínio da empresa para o nome de domínio do Traffic Manager.

![Configurando o método de roteamento de tráfego ponderado usando o Gerenciador de tráfego][1]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [método de roteamento de tráfego de prioridade](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de roteamento de tráfego de desempenho](traffic-manager-configure-performance-routing-method.md).
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as configurações do Gerenciador de tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png

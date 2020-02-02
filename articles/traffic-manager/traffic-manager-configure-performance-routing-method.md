---
title: Configure o método de encaminhamento de tráfego de desempenho utilizando o Gestor de Tráfego Azure  Microsoft Docs
description: Este artigo explica como configurar o Traffic Manager para encaminhar o tráfego para o ponto final com a menor latência
services: traffic-manager
manager: twooley
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: f5e9b7690c28793a35c692a6125a6b11c7a140a4
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938764"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Configure o método de encaminhamento do tráfego de desempenho

O método de encaminhamento de tráfego de Desempenho permite-lhe direcionar o tráfego para o ponto final com a menor latência da rede do cliente. Tipicamente, o datacenter com a latência mais baixa é o mais próximo na distância geográfica. Este método de encaminhamento de tráfego não pode explicar alterações em tempo real na configuração ou carga da rede.

##  <a name="to-configure-performance-routing-method"></a>Para configurar o método de encaminhamento de desempenho

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure os perfis do Gestor de **Tráfego** e, em seguida, clique no nome de perfil para o qual pretende configurar o método de encaminhamento.
3. Na lâmina de perfil do Gestor de **Tráfego,** verifique se os serviços de cloud e os websites que pretende incluir na sua configuração estão presentes.
4. Na secção **Definições,** clique em **Configuração**, e na lâmina de **configuração,** complete da seguinte forma:
    1. Para configurações do método de **encaminhamento**de tráfego, para o **método de encaminhamento** selecione **Performance**.
    2. Detete as definições do **monitor Endpoint** idênticas a todos os pontos finais deste perfil da seguinte forma:
        1. Selecione o **Protocolo**apropriado e especifique o número **da Porta.** 
        2. Para **o path** tipo um corte dianteiro */* . Para monitorizar os pontos finais, deve especificar um caminho e um nome de ficheiro. Um corte para a frente "/" é uma entrada válida para o caminho relativo e implica que o ficheiro está no diretório raiz (padrão).
        3. No topo da página, clique em **Guardar**.
5.  Teste as alterações na sua configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego e clique no perfil do Gestor de Tráfego nos resultados apresentados.
    2.  Na lâmina de perfil do Gestor de **Tráfego,** clique em **Visão Geral**.
    3.  A lâmina de **perfil do Gestor** de Tráfego exibe o nome DNS do seu perfil recém-criado do Traffic Manager. Isto pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final certo, determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados para o ponto final com a menor latência da rede do cliente.
6. Assim que o seu perfil do Traffic Manager estiver a funcionar, edite o registo DNS no seu servidor DNS autoritário para indicar o nome de domínio da empresa para o nome de domínio do Gestor de Tráfego.

![Configurar o método de encaminhamento de tráfego de desempenho utilizando o Traffic Manager][1]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de encaminhamento prioritário](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as definições do Gestor](traffic-manager-testing-settings.md)de Tráfego .

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png
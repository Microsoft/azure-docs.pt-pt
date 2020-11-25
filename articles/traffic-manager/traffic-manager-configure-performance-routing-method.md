---
title: Configure método de encaminhamento de tráfego de desempenho usando Azure Traffic Manager / Microsoft Docs
description: Este artigo explica como configurar o Gestor de Tráfego para encaminhar o tráfego para o ponto final com a latência mais baixa
services: traffic-manager
manager: twooley
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 46d71a38b79f449084f7353527f2dfb05d5b92a4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994915"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Configure o método de encaminhamento de tráfego de desempenho

O método de encaminhamento de tráfego de desempenho permite-lhe direcionar o tráfego para o ponto final com a latência mais baixa da rede do cliente. Tipicamente, o datacenter com a latência mais baixa é o mais próximo na distância geográfica. Este método de encaminhamento de tráfego não pode ter em conta alterações em tempo real na configuração ou carga da rede.

##  <a name="to-configure-performance-routing-method"></a>Para configurar o método de encaminhamento de desempenho

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure os perfis do **Gestor de Tráfego** e, em seguida, clique no nome de perfil para o qual pretende configurar o método de encaminhamento.
3. Na lâmina de **perfil do Gestor de Tráfego,** verifique se tanto os serviços na nuvem como os websites que pretende incluir na sua configuração estão presentes.
4. Na secção **Definições,** clique em **Configuração** e na lâmina **de configuração,** completada da seguinte forma:
    1. Para **as definições do método de encaminhamento de tráfego**, para o método de encaminhamento selecione **Performance**. **Routing method**
    2. Defina as definições do **monitor Endpoint idênticas** para todos os pontos finais deste perfil da seguinte forma:
        1. Selecione o **Protocolo** apropriado e especifique o número **da porta.** 
        2. Para **o Caminho** escreva um corte para a frente */* . Para monitorizar os pontos finais, tem de especificar um caminho e um nome de ficheiro. Um corte dianteiro "/" é uma entrada válida para o caminho relativo e implica que o ficheiro está no diretório raiz (predefinição).
        3. No topo da página, clique em **Guardar**.
5.  Teste as alterações na sua configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego e clique no perfil de Gestor de Tráfego nos resultados que o apresentado.
    2.  Na lâmina de perfil **do Gestor de Tráfego,** clique em **Visão Geral.**
    3.  A lâmina **de perfil do Gestor de Tráfego** exibe o nome DNS do seu perfil de Gestor de Tráfego recém-criado. Isto pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final certo, conforme determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados para o ponto final com a latência mais baixa da rede do cliente.
6. Assim que o seu perfil de Gestor de Tráfego estiver a funcionar, edite o registo DNS no seu servidor DNS autoritário para apontar o nome de domínio da sua empresa para o nome de domínio do Gestor de Tráfego.

![Configurar o método de encaminhamento de tráfego de desempenho utilizando o Gestor de Tráfego][1]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de encaminhamento prioritário](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como testar as [definições do Gestor de Tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png
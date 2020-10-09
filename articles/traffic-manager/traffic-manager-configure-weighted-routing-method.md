---
title: Tutorial - Configurar roteamento de tráfego redondo-robin ponderado com Azure Traffic Manager
description: Este tutorial explica como carregar o tráfego de equilíbrio usando um método de rodada-robin em Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: dff7d4ec02c5a17b51d73b9d81f93984b95a7d22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401355"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Tutorial: Configurar o método de encaminhamento de tráfego ponderado no Gestor de Tráfego

Um padrão comum de método de encaminhamento de tráfego é fornecer um conjunto de pontos finais idênticos, que incluem serviços de nuvem e websites, e enviar tráfego para cada um igualmente. Os passos a seguir descrevem como configurar este tipo de método de encaminhamento de tráfego.

> [!NOTE]
> A Azure Web App já fornece funcionalidade de equilíbrio de carga de robin redondo para websites dentro de uma Região de Azure (que pode incluir vários centros de dados). O Gestor de Tráfego permite-lhe distribuir tráfego por websites em diferentes datacenters.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Para configurar o método ponderado de encaminhamento de tráfego

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure os perfis do **Gestor de Tráfego** e, em seguida, clique no nome de perfil para o qual pretende configurar o método de encaminhamento.
3. Na lâmina de **perfil do Gestor de Tráfego,** verifique se tanto os serviços na nuvem como os websites que pretende incluir na sua configuração estão presentes.
4. Na secção **Definições,** clique em **Configuração**e na lâmina **de configuração,** completada da seguinte forma:
    1. Para **as definições do método de encaminhamento de tráfego,** verifique se o método de encaminhamento de tráfego está **ponderado**. Se não for, clique em **Peso** da lista de suspensos.
    2. Defina as definições do **monitor Endpoint idênticas** para todos os pontos finais deste perfil da seguinte forma:
        1. Selecione o **Protocolo**apropriado e especifique o número **da porta.** 
        2. Para **o Caminho** escreva um corte para a frente */* . Para monitorizar os pontos finais, tem de especificar um caminho e um nome de ficheiro. Um corte dianteiro "/" é uma entrada válida para o caminho relativo e implica que o ficheiro está no diretório raiz (predefinição).
        3. No topo da página, clique em **Guardar**.
5. Teste as alterações na sua configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego e clique no perfil de Gestor de Tráfego nos resultados que o apresentado.
    2.  Na lâmina de perfil **do Gestor de Tráfego,** clique em **Visão Geral.**
    3.  A lâmina **de perfil do Gestor de Tráfego** exibe o nome DNS do seu perfil de Gestor de Tráfego recém-criado. Isto pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final certo, conforme determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados cada ponto final de uma forma redonda.
6. Assim que o seu perfil de Gestor de Tráfego estiver a funcionar, edite o registo DNS no seu servidor DNS autoritário para apontar o nome de domínio da sua empresa para o nome de domínio do Gestor de Tráfego.

![Configurar método de encaminhamento de tráfego ponderado utilizando o Gestor de Tráfego][1]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o método de encaminhamento de tráfego prioritário](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre [o método de encaminhamento de tráfego de desempenho](traffic-manager-configure-performance-routing-method.md).
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como testar as [definições do Gestor de Tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png

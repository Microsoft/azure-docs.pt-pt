---
title: Tutorial - Configurar encaminhamento de tráfego prioritário com Azure Traffic Manager
description: Este tutorial explica como configurar o método de encaminhamento de tráfego prioritário no Gestor de Tráfego
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
ms.openlocfilehash: 404338c3e36216833d39c3551ae2dee0be304d24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401015"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Tutorial: Configurar método de encaminhamento prioritário de tráfego em Gerente de Tráfego

Independentemente do modo do site, os Websites Azure já fornecem funcionalidade de failover para websites dentro de um datacenter (também conhecido como região). O Gestor de Tráfego fornece falhas para websites em diferentes datacenters.

Um padrão comum para o failover do serviço é enviar o tráfego para um serviço primário e fornecer um conjunto de serviços de backup idênticos para falha. Os seguintes passos explicam como configurar este failover priorizado com serviços e websites em nuvem Azure:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Para configurar o método prioritário de encaminhamento de tráfego

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure os perfis do **Gestor de Tráfego** e, em seguida, clique no nome de perfil para o qual pretende configurar o método de encaminhamento.
3. Na lâmina de **perfil do Gestor de Tráfego,** verifique se tanto os serviços na nuvem como os websites que pretende incluir na sua configuração estão presentes.
4. Na secção **Definições,** clique em **Configuração**e na lâmina **de configuração,** completada da seguinte forma:
    1. Para **as definições do método de encaminhamento de tráfego,** verifique se o método de encaminhamento de tráfego é **prioridade**. Se não for, clique em **Prioridade** a partir da lista de dropdown.
    2. Defina as definições do **monitor Endpoint idênticas** para todos os pontos finais deste perfil da seguinte forma:
        1. Selecione o **Protocolo**apropriado e especifique o número **da porta.** 
        2. Para **o Caminho** escreva um corte para a frente */* . Para monitorizar os pontos finais, tem de especificar um caminho e um nome de ficheiro. Um corte dianteiro "/" é uma entrada válida para o caminho relativo e implica que o ficheiro está no diretório raiz (predefinição).
        3. No topo da página, clique em **Guardar**.
5. Na secção **Definições,** clique em **Pontos de final**.
6. Na lâmina **Endpoints,** reveja a ordem prioritária para os seus pontos finais. Quando selecionar o método de encaminhamento de tráfego **prioritário,** a ordem dos pontos finais selecionados importa. Verifique a ordem prioritária dos pontos finais.  O ponto final principal está no topo. Verifique novamente a encomenda que é apresentada. todos os pedidos serão encaminhados para o primeiro ponto final e se o Gestor de Tráfego detetar que não é saudável, o tráfego automaticamente falha para o próximo ponto final. 
7. Para alterar a ordem de prioridade do ponto final, clique no ponto final e na lâmina **endpoint** que é visualizada, clique em **Editar** e altere o valor **Prioritário** conforme necessário. 
8. Clique **em Guardar** para guardar alterar as definições do ponto final.
9. Depois de concluir as alterações de configuração, clique em **Guardar** na parte inferior da página.
10. Teste as alterações na sua configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego e clique no perfil de Gestor de Tráfego nos resultados que o apresentado.
    2.  Na lâmina de perfil **do Gestor de Tráfego,** clique em **Visão Geral.**
    3.  A lâmina **de perfil do Gestor de Tráfego** exibe o nome DNS do seu perfil de Gestor de Tráfego recém-criado. Isto pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final certo, conforme determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados para o primeiro ponto final e se o Gestor de Tráfego detetar que não é saudável, o tráfego automaticamente falha para o próximo ponto final.
11. Assim que o seu perfil de Gestor de Tráfego estiver a funcionar, edite o registo DNS no seu servidor DNS autoritário para apontar o nome de domínio da sua empresa para o nome de domínio do Gestor de Tráfego.

![Configurar método de encaminhamento de tráfego prioritário utilizando o Gestor de Tráfego][1]

## <a name="next-steps"></a>Passos seguintes


- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre [o método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md).
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como testar as [definições do Gestor de Tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png
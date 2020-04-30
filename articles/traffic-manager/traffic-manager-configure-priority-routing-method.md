---
title: Tutorial - Configure o itinerário prioritário de tráfego com o Gestor de Tráfego Azure
description: Este tutorial explica como configurar o método prioritário de encaminhamento de tráfego no Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: ca223a19ff7ddeae95878f941f3cf295664e62b4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76938731"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Tutorial: Configurar método de encaminhamento de tráfego prioritário no Traffic Manager

Independentemente do modo de website, os Websites Azure já fornecem funcionalidade sinuosa para websites dentro de um datacenter (também conhecido como região). O Traffic Manager fornece falhas para websites em diferentes datacenters.

Um padrão comum para a falha do serviço é enviar tráfego para um serviço primário e fornecer um conjunto de serviços de backup idênticos para falha. Os seguintes passos explicam como configurar esta falha prioritária com serviços e websites da nuvem Azure:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Para configurar o método prioritário de encaminhamento de tráfego

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure os perfis do Gestor de **Tráfego** e, em seguida, clique no nome de perfil para o qual pretende configurar o método de encaminhamento.
3. Na lâmina de perfil do Gestor de **Tráfego,** verifique se os serviços de cloud e os websites que pretende incluir na sua configuração estão presentes.
4. Na secção **Definições,** clique em **Configuração**, e na lâmina de **configuração,** complete da seguinte forma:
    1. Para configurações do método de **encaminhamento**de tráfego, verifique se o método de encaminhamento de tráfego é **Prioridade**. Caso contrário, clique em **Prioridade** a partir da lista de abandono.
    2. Detete as definições do **monitor Endpoint** idênticas a todos os pontos finais deste perfil da seguinte forma:
        1. Selecione o **Protocolo**apropriado e especifique o número **da Porta.** 
        2. Para **o caminho** */* tipo um corte para a frente . Para monitorizar os pontos finais, deve especificar um caminho e um nome de ficheiro. Um corte para a frente "/" é uma entrada válida para o caminho relativo e implica que o ficheiro está no diretório raiz (padrão).
        3. No topo da página, clique em **Guardar**.
5. Na secção **Definições,** clique em **Pontos Finais**.
6. Na lâmina **Endpoints,** reveja a ordem prioritária para os seus pontos finais. Quando selecionar o método de encaminhamento de tráfego **Prioritário,** a ordem dos pontos finais selecionados importa. Verifique a ordem prioritária dos pontos finais.  O ponto final primário está no topo. Verifique duas vezes a encomenda que é apresentada. todos os pedidos serão encaminhados para o primeiro ponto final e se o Gestor de Tráfego detetar que não é saudável, o tráfego falha automaticamente até ao ponto final seguinte. 
7. Para alterar a ordem de prioridade do ponto final, clique no ponto final e na lâmina **Endpoint** que é exibida, clique em **Editar** e alterar o valor **Prioritário** conforme necessário. 
8. Clique em **Guardar** para guardar as definições de ponto final.
9. Depois de completar as alterações de configuração, clique em **Guardar** na parte inferior da página.
10. Teste as alterações na sua configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego e clique no perfil do Gestor de Tráfego nos resultados apresentados.
    2.  Na lâmina de perfil do Gestor de **Tráfego,** clique em **Visão Geral**.
    3.  A lâmina de **perfil do Gestor** de Tráfego exibe o nome DNS do seu perfil recém-criado do Traffic Manager. Isto pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final certo, determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados para o primeiro ponto final e se o Gestor de Tráfego detetar que não é saudável, o tráfego falha automaticamente até ao ponto final seguinte.
11. Assim que o seu perfil do Traffic Manager estiver a funcionar, edite o registo DNS no seu servidor DNS autoritário para indicar o nome de domínio da empresa para o nome de domínio do Gestor de Tráfego.

![Configurar método de encaminhamento de tráfego prioritário utilizando o Traffic Manager][1]

## <a name="next-steps"></a>Passos seguintes


- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Aprenda sobre o método de [encaminhamento](traffic-manager-configure-performance-routing-method.md)de desempenho.
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as definições do Gestor](traffic-manager-testing-settings.md)de Tráfego .

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png
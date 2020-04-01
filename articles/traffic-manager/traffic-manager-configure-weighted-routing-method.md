---
title: Tutorial - Configure encaminhamento de tráfego de rodada ponderada com o Gestor de Tráfego Azure
description: Este tutorial explica como carregar o tráfego de equilíbrio usando um método de rodapé em Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: 8bdc710b36cae70d29d32333f431b8a9dda154cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938716"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Tutorial: Configure o método de encaminhamento de tráfego ponderado no Traffic Manager

Um padrão comum de método de encaminhamento de tráfego é fornecer um conjunto de pontos finais idênticos, que incluem serviços na nuvem e websites, e enviar tráfego para cada um igualmente. Os seguintes passos descrevem como configurar este tipo de método de encaminhamento de tráfego.

> [!NOTE]
> A Azure Web App já fornece funcionalidade de equilíbrio de carga sonora para websites dentro de uma região do Azure (que pode incluir vários datacenters). O Traffic Manager permite-lhe distribuir tráfego em sites em diferentes datacenters.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Para configurar o método de encaminhamento de tráfego ponderado

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure os perfis do Gestor de **Tráfego** e, em seguida, clique no nome de perfil para o qual pretende configurar o método de encaminhamento.
3. Na lâmina de perfil do Gestor de **Tráfego,** verifique se os serviços de cloud e os websites que pretende incluir na sua configuração estão presentes.
4. Na secção **Definições,** clique em **Configuração**, e na lâmina de **configuração,** complete da seguinte forma:
    1. Para as definições do método de **encaminhamento**de tráfego, verifique se o método de encaminhamento de tráfego é **ponderado**. Caso contrário, clique em **Ponderação** a partir da lista de abandono.
    2. Detete as definições do **monitor Endpoint** idênticas a todos os pontos finais deste perfil da seguinte forma:
        1. Selecione o **Protocolo**apropriado e especifique o número **da Porta.** 
        2. Para **o caminho** */* tipo um corte para a frente . Para monitorizar os pontos finais, deve especificar um caminho e um nome de ficheiro. Um corte para a frente "/" é uma entrada válida para o caminho relativo e implica que o ficheiro está no diretório raiz (padrão).
        3. No topo da página, clique em **Guardar**.
5. Teste as alterações na sua configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego e clique no perfil do Gestor de Tráfego nos resultados apresentados.
    2.  Na lâmina de perfil do Gestor de **Tráfego,** clique em **Visão Geral**.
    3.  A lâmina de **perfil do Gestor** de Tráfego exibe o nome DNS do seu perfil recém-criado do Traffic Manager. Isto pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final certo, determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados para cada ponto final de forma redonda.
6. Assim que o seu perfil do Traffic Manager estiver a funcionar, edite o registo DNS no seu servidor DNS autoritário para indicar o nome de domínio da empresa para o nome de domínio do Gestor de Tráfego.

![Configurar método de encaminhamento de tráfego ponderado utilizando o Gestor de Tráfego][1]

## <a name="next-steps"></a>Passos seguintes

- Conheça o método prioritário de [encaminhamento](traffic-manager-configure-priority-routing-method.md)de tráfego.
- Saiba mais sobre o método de [encaminhamento](traffic-manager-configure-performance-routing-method.md)do tráfego de desempenho .
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as definições do Gestor](traffic-manager-testing-settings.md)de Tráfego .

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png

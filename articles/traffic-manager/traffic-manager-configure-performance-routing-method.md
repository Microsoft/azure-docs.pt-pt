---
title: Configurar o método de encaminhamento de tráfego de desempenho com o Gestor de tráfego do Azure | Documentos da Microsoft
description: Este artigo explica como configurar o Gestor de tráfego para encaminhar o tráfego para o ponto final com latência mais baixa
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 4c948668e355b87026240588c6fac11d86e355b2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006578"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Configurar o método de encaminhamento de tráfego de desempenho

O método de encaminhamento de tráfego de desempenho permite-lhe direcionar o tráfego para o ponto final com a menor latência de rede do cliente. Normalmente, o Centro de dados com a menor latência é o mais próximo na distância geográfica. Este método de encaminhamento de tráfego não é possível em conta as alterações em tempo real na configuração de rede ou de carga.

##  <a name="to-configure-performance-routing-method"></a>Para configurar o método de encaminhamento de desempenho

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure o **perfis do Gestor de tráfego** e, em seguida, clique no nome de perfil que pretende configurar o método de encaminhamento para.
3. Na **perfil do Traffic Manager** painel, certifique-se de que os serviços cloud e de Web sites que pretende incluir na sua configuração estão presentes.
4. Na **configurações** secção, clique em **configuração**e, no **configuração** painel, faça o seguinte:
    1. Para **definições do método de encaminhamento de tráfego**, para **método de encaminhamento** selecionar **desempenho**.
    2. Definir o **definições do monitor de ponto final** idêntica para todas as cada ponto final dentro deste perfil da seguinte forma:
        1. Selecione o adequado **protocolo**e especifique a **porta** número. 
        2. Para **caminho** escreva uma barra */*. Para monitorizar os pontos finais, tem de especificar um caminho e nome de ficheiro. A reencaminhar barra "/" é uma entrada válida para o caminho relativo e indica que o ficheiro está no diretório de raiz (predefinição).
        3. Na parte superior da página, clique em **guardar**.
5.  Teste as alterações na configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome de perfil do Gestor de tráfego e clique no perfil do Gestor de tráfego nos resultados que apresentados.
    2.  Na **Gestor de tráfego** painel de perfil, clique em **descrição geral**.
    3.  O **perfil do Traffic Manager** painel apresenta o nome DNS do perfil do Traffic Manager criado recentemente. Isto pode ser utilizado por todos os clientes (por exemplo, ao navegar para ele usando um navegador da web) obter encaminhado para o ponto de extremidade direita, como determinado pelo tipo de encaminhamento. Neste caso todos os pedidos são encaminhados para o ponto final com a menor latência de rede do cliente.
6. Assim que o perfil do Traffic Manager está a funcionar, edite o registo DNS no seu servidor DNS autoritativo para apontar o nome de domínio da empresa para o nome de domínio do Gestor de tráfego.

![Configurar o método de encaminhamento de tráfego de desempenho com o Gestor de tráfego][1]

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de encaminhamento prioritário](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as definições do Gestor de tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png
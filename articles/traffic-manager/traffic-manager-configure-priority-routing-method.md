---
title: Configurar o método de encaminhamento de tráfego de prioridade com o Gestor de tráfego do Azure | Documentos da Microsoft
description: Este artigo explica como configurar o método de encaminhamento de tráfego de prioridade no Gestor de tráfego
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
ms.openlocfilehash: 66c5bd9390d6fe0f26af66e18aed22c07a7da3e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003105"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Configurar método de encaminhamento de tráfego de prioridade no Gestor de tráfego

Independentemente do modo do site, Web sites do Azure já fornece funcionalidade de ativação pós-falha para os Web sites num centro de dados (também conhecido como região). O Gestor de tráfego fornece ativação pós-falha para os Web sites em datacenters diferentes.

É um padrão comum para ativação pós-falha do serviço enviar tráfego para um serviço principal e fornecer um conjunto de serviços de cópia de segurança idênticos para ativação pós-falha. Os passos seguintes explicam como configurar esta ativação pós-falha com prioridade com Web sites e serviços cloud do Azure:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Para configurar o método de encaminhamento de tráfego de prioridade

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure o **perfis do Gestor de tráfego** e, em seguida, clique no nome de perfil que pretende configurar o método de encaminhamento para.
3. Na **perfil do Traffic Manager** painel, certifique-se de que os serviços cloud e de Web sites que pretende incluir na sua configuração estão presentes.
4. Na **configurações** secção, clique em **configuração**e, no **configuração** painel, faça o seguinte:
    1. Para **definições do método de encaminhamento de tráfego**, certifique-se de que o método de encaminhamento de tráfego é **prioridade**. Se não estiver, clique em **prioridade** na lista pendente.
    2. Definir o **definições do monitor de ponto final** idêntica para todas as cada ponto final dentro deste perfil da seguinte forma:
        1. Selecione o adequado **protocolo**e especifique a **porta** número. 
        2. Para **caminho** escreva uma barra */*. Para monitorizar os pontos finais, tem de especificar um caminho e nome de ficheiro. A reencaminhar barra "/" é uma entrada válida para o caminho relativo e indica que o ficheiro está no diretório de raiz (predefinição).
        3. Na parte superior da página, clique em **guardar**.
5. Na **configurações** secção, clique em **pontos de extremidade**.
6. Na **pontos de extremidade** painel, reveja a ordem de prioridade para os pontos finais. Quando seleciona a **prioridade** método de encaminhamento de tráfego, a ordem das questões de pontos finais selecionados. Certifique-se a ordem de prioridade de pontos de extremidade.  O ponto final primário é na parte superior. Verifique novamente em ordem que é apresentado. todos os pedidos serão encaminhados para o primeiro ponto final e se o Gestor de tráfego detetar ser mau estado de funcionamento, o tráfego automaticamente efetua a ativação pós-falha para o próximo ponto de extremidade. 
7. Para alterar a ordem de prioridade de ponto final, clique no ponto final e, no **ponto final** painel apresentado, clique em **editar** e altere o **prioridade** valor conforme necessário. 
8. Clique em **guardar** para guardar alterar as definições de ponto final.
9. Depois de concluir as alterações de configuração, clique em **guardar** na parte inferior da página.
10. Teste as alterações na configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome de perfil do Gestor de tráfego e clique no perfil do Gestor de tráfego nos resultados que apresentados.
    2.  Na **Gestor de tráfego** painel de perfil, clique em **descrição geral**.
    3.  O **perfil do Traffic Manager** painel apresenta o nome DNS do perfil do Traffic Manager criado recentemente. Isto pode ser utilizado por todos os clientes (por exemplo, ao navegar para ele usando um navegador da web) obter encaminhado para o ponto de extremidade direita, como determinado pelo tipo de encaminhamento. Nesse caso, todos os pedidos são encaminhados para o primeiro ponto final e se o Gestor de tráfego detetar ser mau estado de funcionamento, o tráfego automaticamente efetua a ativação pós-falha para o próximo ponto de extremidade.
11. Assim que o perfil do Traffic Manager está a funcionar, edite o registo DNS no seu servidor DNS autoritativo para apontar o nome de domínio da empresa para o nome de domínio do Gestor de tráfego.

![Configurar o método de encaminhamento de tráfego de prioridade com o Gestor de tráfego][1]

## <a name="next-steps"></a>Passos Seguintes


- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre [método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md).
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as definições do Gestor de tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png
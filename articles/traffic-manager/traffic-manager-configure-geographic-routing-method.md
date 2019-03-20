---
title: Configurar o método de encaminhamento de tráfego geográfico utilizando o Gestor de tráfego do Azure
description: Este artigo explica como configurar o método de encaminhamento de tráfego geográfico utilizando o Gestor de tráfego do Azure
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 64e3cbd082f2d18668dff87fbec098add73ffda6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010091"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Configurar o método de encaminhamento de tráfego geográfico utilizando o Gestor de tráfego

O método de encaminhamento de tráfego geográfico permite-lhe direcionar o tráfego para pontos de extremidade específicos com base na localização geográfica onde os pedidos têm origem. Este tutorial mostra-lhe como criar um perfil do Gestor de tráfego com este método de encaminhamento e configurar os pontos de extremidade para receber o tráfego a partir de localizações geográficas específicas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de tráfego

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/).
2. Clique em **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego** > **Criar**.
4. Na **perfil do Gestor de tráfego criar**:
    1. Forneça um nome para o seu perfil. Este nome tem de ser exclusivo na zona trafficmanager.net. Para acessar o perfil do Traffic Manager, utilize o nome DNS <profilename>. trafficmanager.net.
    2. Selecione o **Geographic** método de encaminhamento.
    3. Selecione a subscrição que pretende criar este perfil.
    4. Utilizar um grupo de recursos existente ou criar um novo grupo de recursos para colocar este perfil. Se optar por criar um novo grupo de recursos, utilize o **localização do grupo de recursos** lista pendente para especificar a localização do grupo de recursos. Esta definição refere-se para a localização do grupo de recursos e não tem impacto sobre o perfil do Gestor de tráfego que é implementado globalmente.
    5. Depois de clicar em **criar**, perfil do Traffic Manager é criado e implementado globalmente.

![Criar um perfil do Gestor de Tráfego](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Adicionar pontos finais

1. Procure o nome de perfil do Gestor de tráfego que criou na barra de pesquisa do portal e clique no resultado quando é apresentado.
2. Navegue para **configurações** -> **pontos finais** no Gestor de tráfego.
3. Clique em **Add** para mostrar o **adicionar ponto final**.
3. Clique em **Add** e, no **adicionar ponto final** que é exibido, conclua os seguintes:
4. Selecione **tipo** consoante o tipo de ponto final que está a adicionar. Para os perfis de encaminhamento geográficos utilizados na produção, recomendamos vivamente utilizar tipos de ponto final aninhado que contém um perfil de subordinado com mais de um ponto final. Para obter mais detalhes, consulte [FAQs sobre métodos de encaminhamento de tráfego geográfico](traffic-manager-FAQs.md).
5. Indique um **Nome** pelo qual pretende reconhecer este ponto final.
6. Determinados campos nesta página dependem do tipo de ponto final que está a adicionar:
    1. Se estiver a adicionar um ponto final do Azure, selecione o **tipo de recurso de destino** e o **destino** com base no recurso que pretende direcionar o tráfego para
    2. Se estiver a adicionar um **externo** ponto final, forneça o **nome de domínio completamente qualificado (FQDN)** para o ponto final.
    3. Se estiver a adicionar um **aninhados endpoint**, selecione a **recurso de destino** que corresponde ao perfil de subordinados que pretende utilizar e especifique o **contagem de pontos finais de subordinados mínimo**.
7. Na secção de mapeamento geográfico, utilize o pendente para adicionar as regiões a partir de onde pretende que o tráfego seja enviado para este ponto final. Tem de adicionar pelo menos uma região, e pode ter várias regiões mapeados.
8. Repetir isso para todos os pontos finais de que pretende adicionar sob este perfil

![Adicionar um ponto final do Gestor de Tráfego](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Utilizar o perfil do Gestor de tráfego
1.  Na barra de pesquisa do portal, procure o **perfil do Traffic Manager** nome que criou na secção anterior e clique no perfil do Gestor de tráfego nos resultados que apresentados.
2. Clique em **Descrição geral**.
3. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar. Isto pode ser utilizado por todos os clientes (por exemplo, ao navegar para ele usando um navegador da web) obter encaminhado para o ponto de extremidade direita, como determinado pelo tipo de encaminhamento.  No caso de encaminhamento geográfico, o Gestor de tráfego analisa o IP de origem do pedido a receber e determina a região a partir do qual é a com origem. Se essa região é mapeada para um ponto de extremidade, o tráfego é encaminhado para lá. Se esta região não está mapeada para um ponto de extremidade, o Gestor de tráfego devolve uma resposta de consulta NODATA.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [método de encaminhamento de tráfego geográfico](traffic-manager-routing-methods.md#geographic).
- Saiba como [testar as definições do Gestor de tráfego](traffic-manager-testing-settings.md).

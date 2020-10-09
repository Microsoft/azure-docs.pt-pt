---
title: Tutorial - Configurar o encaminhamento de tráfego geográfico com o Gestor de Tráfego da Azure
description: Este tutorial explica como configurar o método de encaminhamento de tráfego geográfico usando O Gestor de Tráfego Azure
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: duau
ms.openlocfilehash: 53773d7c616edec067e1ed1778b7ce6b500ee936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462620"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Tutorial: Configurar o método de encaminhamento de tráfego geográfico usando o Gestor de Tráfego

O método de encaminhamento de tráfego geográfico permite-lhe direcionar o tráfego para pontos finais específicos com base na localização geográfica de onde os pedidos são originários. Este tutorial mostra-lhe como criar um perfil de Gestor de Tráfego com este método de encaminhamento e configurar os pontos finais para receber tráfego de geografias específicas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil de gestor de tráfego

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/).
2. Clique em Criar um perfil de Gestor de Tráfego de Rede de **Recursos**  >  **Networking**  >  **Traffic Manager profile**  >  **Criar**.
4. No **perfil Criar Gestor de Tráfego:**
    1. Forneça um nome para o seu perfil. Este nome tem de ser único dentro da zona trafficmanager.net. Para aceder ao seu perfil de Gestor de Tráfego, utilize o nome DNS `<profilename>.trafficmanager.net` .
    2. Selecione o método de encaminhamento **Geográfico.**
    3. Selecione a subscrição em que pretende criar este perfil.
    4. Utilize um grupo de recursos existente ou crie um novo grupo de recursos para colocar este perfil sob. Se optar por criar um novo grupo de recursos, utilize a **localização do Grupo de Recursos** para especificar a localização do grupo de recursos. Esta definição refere-se à localização do grupo de recursos, e não tem qualquer impacto no perfil do Gestor de Tráfego que é implantado globalmente.
    5. Depois de clicar em **Criar,** o seu perfil de Gestor de Tráfego é criado e implementado globalmente.

![Criar um perfil do Gestor de Tráfego](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Adicionar pontos finais

1. Procure o nome de perfil do Gestor de Tráfego que criou na barra de pesquisa do portal e clique no resultado quando este for mostrado.
2. Navegue para **Definições**  ->  **Pontos finais** no Gestor de Tráfego.
3. Clique em **Adicionar** e no painel **de ponto final Add** que é apresentado, completo da seguinte forma:
4. Selecione **Tipo** dependendo do tipo de ponto final que está a adicionar. Para perfis de encaminhamento geográficos utilizados na produção, recomendamos vivamente a utilização de tipos de pontos finais aninhados que contenham um perfil infantil com mais de um ponto final. Para obter mais detalhes, consulte [as PERGUNTAS Frequentes sobre os métodos de encaminhamento de tráfego geográfico.](traffic-manager-FAQs.md)
5. Indique um **Nome** pelo qual pretende reconhecer este ponto final.
6. Certos campos nesta página dependem do tipo de ponto final que está a adicionar:
    1. Se estiver a adicionar um ponto final Azure, selecione o **tipo de recurso Target** e o **Target** com base no recurso a que pretende dirigir o tráfego
    2. Se estiver a adicionar um ponto final **externo,** forneça o **nome de domínio totalmente qualificado (FQDN)** para o seu ponto final.
    3. Se estiver a adicionar um **ponto final aninhado,** selecione o **recurso Target** que corresponde ao perfil da criança que pretende utilizar e especifique a contagem mínima de **pontos finais**para crianças .
7. Na secção de Geo mapping, utilize a gota para baixo para adicionar as regiões de onde deseja que o tráfego seja enviado para este ponto final. Você deve adicionar pelo menos uma região, e você pode ter várias regiões mapeadas.
8. Repita isto para todos os pontos finais que pretende adicionar neste perfil

![Adicionar um ponto final do Gestor de Tráfego](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Utilize o perfil do Gestor de Tráfego
1.  Na barra de pesquisa do portal, procure o nome de **perfil do Gestor de Tráfego** que criou na secção anterior e clique no perfil do gestor de tráfego nos resultados que o apresentado.
2. Clique em **Descrição geral**.
3. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar. Isto pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final certo, conforme determinado pelo tipo de encaminhamento.  No caso do encaminhamento geográfico, o Gestor de Tráfego olha para a origem do pedido de entrada e determina a região de onde é originária. Se essa região for mapeada para um ponto final, o tráfego é encaminhado para lá. Se esta região não estiver mapeada para um ponto final, então o Gestor de Tráfego devolve uma resposta de consulta NODATA.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o método de encaminhamento de tráfego geográfico.](traffic-manager-routing-methods.md#geographic)
- Saiba como testar as [definições do Gestor de Tráfego](traffic-manager-testing-settings.md).

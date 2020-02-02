---
title: Tutorial - Configure o encaminhamento de tráfego geográfico com o Gestor de Tráfego Azure
description: Este tutorial explica como configurar o método de encaminhamento de tráfego geográfico usando o Gestor de Tráfego Azure
services: traffic-manager
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: rohink
ms.openlocfilehash: 3eb3f354d51833e55f405ed35679f1a5882c057a
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938798"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Tutorial: Configure o método de encaminhamento de tráfego geográfico utilizando o Traffic Manager

O método de encaminhamento de tráfego geográfico permite-lhe direcionar o tráfego para pontos finais específicos com base na localização geográfica de onde os pedidos são originados. Este tutorial mostra-lhe como criar um perfil de Gestor de Tráfego com este método de encaminhamento e configurar os pontos finais para receber tráfego de geografias específicas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil de gestor de tráfego

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/).
2. Clique em **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego** > **Criar**.
4. No **perfil Criar Gestor de Tráfego:**
    1. Forneça um nome para o seu perfil. Este nome tem de ser único dentro da zona trafficmanager.net. Para aceder ao seu perfil de Gestor de Tráfego, utiliza o nome DNS `<profilename>.trafficmanager.net`.
    2. Selecione o método de encaminhamento **Geográfico.**
    3. Selecione a subscrição que pretende criar este perfil.
    4. Utilize um grupo de recursos existente ou crie um novo grupo de recursos para colocar este perfil abaixo. Se optar por criar um novo grupo de recursos, utilize o dropdown de localização do **Grupo de Recursos** para especificar a localização do grupo de recursos. Esta definição refere-se à localização do grupo de recursos, e não tem qualquer impacto no perfil do Gestor de Tráfego que é implantado globalmente.
    5. Depois de clicar em **Criar**, o seu perfil de Gestor de Tráfego é criado e implementado globalmente.

![Criar um perfil do Gestor de Tráfego](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Adicionar pontos finais

1. Procure o nome de perfil do Gestor de Tráfego que criou na barra de pesquisa do portal e clique no resultado quando este for mostrado.
2. Navegue para **Definições** -> **pontos finais** no Gestor de Tráfego.
3. Clique em **Adicionar** para mostrar o **ponto final add**.
3. Clique em **Adicionar** e no **ponto final add** que é apresentado, completo da seguinte forma:
4. Selecione **Tipo** dependendo do tipo de ponto final que está a adicionar. Para perfis de encaminhamento geográfico utilizados na produção, recomendamos vivamente a utilização de tipos de pontos finais aninhados contendo um perfil infantil com mais de um ponto final. Para mais detalhes, consulte [as PERGUNTAS Frequentes sobre os métodos de encaminhamento de tráfego geográfico](traffic-manager-FAQs.md).
5. Indique um **Nome** pelo qual pretende reconhecer este ponto final.
6. Certos campos desta página dependem do tipo de ponto final que está a adicionar:
    1. Se estiver a adicionar um ponto final do Azure, selecione o tipo de **recurso Target** e o **Target** com base no recurso que pretende direcionar para o tráfego para
    2. Se estiver a adicionar um ponto final **externo,** forneça o nome de **domínio totalmente qualificado (FQDN)** para o seu ponto final.
    3. Se estiver a adicionar um **ponto final Anested**, selecione o **recurso Target** que corresponde ao perfil da criança que pretende utilizar e especifique a contagem mínima de **pontos finais para crianças**.
7. Na secção de Geomapping, utilize a queda para baixo para adicionar as regiões de onde pretende que o tráfego seja enviado para este ponto final. Você deve adicionar pelo menos uma região, e você pode ter várias regiões mapeadas.
8. Repita isto para todos os pontos finais que pretende adicionar sob este perfil

![Adicionar um ponto final do Gestor de Tráfego](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Utilize o perfil do Gestor de Tráfego
1.  Na barra de pesquisa do portal, procure o nome de perfil do Gestor de **Tráfego** que criou na secção anterior e clique no perfil do gestor de tráfego nos resultados que o apresentado.
2. Clique em **Descrição geral**.
3. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar. Isto pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final certo, determinado pelo tipo de encaminhamento.  No caso de encaminhamento geográfico, o Gestor de Tráfego analisa a fonte do pedido de entrada e determina a região de onde está a ser originário. Se essa região estiver mapeada para um ponto final, o tráfego é encaminhado para lá. Se esta região não estiver mapeada para um ponto final, então o Gestor de Tráfego devolve uma resposta de consulta NODATA.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o método de [encaminhamento de tráfego geográfico.](traffic-manager-routing-methods.md#geographic)
- Saiba como [testar as definições do Gestor](traffic-manager-testing-settings.md)de Tráfego .

---
title: Configurar o roteamento de tráfego geográfico-Gerenciador de tráfego do Azure
description: Este artigo explica como configurar o método de roteamento de tráfego geográfico usando o Gerenciador de tráfego do Azure
services: traffic-manager
author: asudbring
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: allensu
ms.openlocfilehash: f15871705b9839f1c7a7c7f04f6f4a88641673fd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031956"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de tráfego

O método de roteamento de tráfego geográfico permite direcionar o tráfego para pontos de extremidade específicos com base na localização geográfica onde as solicitações se originam. Este tutorial mostra como criar um perfil do Gerenciador de tráfego com esse método de roteamento e configurar os pontos de extremidade para receber tráfego de geografias específicos.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de tráfego

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/).
2. Clique em **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego** > **Criar**.
4. No **Criar perfil do Gerenciador de tráfego**:
    1. Forneça um nome para seu perfil. Esse nome precisa ser exclusivo na zona trafficmanager.net. Para acessar o perfil do Gerenciador de tráfego, use o nome DNS `<profilename>.trafficmanager.net`.
    2. Selecione o método de roteamento **geográfico** .
    3. Selecione a assinatura na qual você deseja criar esse perfil.
    4. Use um grupo de recursos existente ou crie um novo grupo de recursos no qual este perfil será colocado. Se você optar por criar um novo grupo de recursos, use a lista suspensa **local do grupo de recursos** para especificar o local do grupo de recursos. Essa configuração refere-se ao local do grupo de recursos e não tem impacto sobre o perfil do Gerenciador de tráfego implantado globalmente.
    5. Depois de clicar em **criar**, seu perfil do Gerenciador de tráfego é criado e implantado globalmente.

![Criar um perfil do Gestor de Tráfego](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Adicionar pontos de extremidade

1. Procure o nome do perfil do Gerenciador de tráfego que você criou na barra de pesquisa do portal e clique no resultado quando ele for mostrado.
2. Navegue até **configurações** -> **pontos de extremidade** no Gerenciador de tráfego.
3. Clique em **Adicionar** para mostrar o **ponto de extremidade de adição**.
3. Clique em **Adicionar** e, em **Adicionar ponto de extremidade** que é exibido, preencha da seguinte maneira:
4. Selecione **tipo** , dependendo do tipo de ponto de extremidade que você está adicionando. Para perfis de roteamento geográfico usados na produção, é altamente recomendável usar tipos de ponto de extremidade aninhados que contenham um perfil filho com mais de um ponto de extremidade. Para obter mais detalhes, consulte [perguntas frequentes sobre métodos de roteamento de tráfego geográfico](traffic-manager-FAQs.md).
5. Indique um **Nome** pelo qual pretende reconhecer este ponto final.
6. Determinados campos nesta página dependem do tipo de ponto de extremidade que você está adicionando:
    1. Se você estiver adicionando um ponto de extremidade do Azure, selecione o **tipo de recurso de destino** e o **destino** com base no recurso para o qual você deseja direcionar o tráfego
    2. Se você estiver adicionando um ponto de extremidade **externo** , forneça o **FQDN (nome de domínio totalmente qualificado)** para seu ponto de extremidade.
    3. Se você estiver adicionando um **ponto de extremidade aninhado**, selecione o **recurso de destino** que corresponde ao perfil filho que você deseja usar e especifique a **contagem de pontos de extremidade filhos mínimos**.
7. Na seção mapeamento geográfico, use a lista suspensa para adicionar as regiões de onde você deseja que o tráfego seja enviado para esse ponto de extremidade. Você deve adicionar pelo menos uma região e pode ter várias regiões mapeadas.
8. Repita isso para todos os pontos de extremidade que você deseja adicionar a este perfil

![Adicionar um ponto final do Gestor de Tráfego](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Usar o perfil do Gerenciador de tráfego
1.  Na barra de pesquisa do portal, procure o nome do **perfil do Gerenciador de tráfego** que você criou na seção anterior e clique no perfil do Gerenciador de tráfego nos resultados exibidos.
2. Clique em **Descrição geral**.
3. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar. Isso pode ser usado por qualquer cliente (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto conforme determinado pelo tipo de roteamento.  No caso de roteamento geográfico, o Traffic Manager examina o IP de origem da solicitação de entrada e determina a região da qual ele está se originando. Se essa região for mapeada para um ponto de extremidade, o tráfego será roteado para lá. Se essa região não estiver mapeada para um ponto de extremidade, o Gerenciador de tráfego retornará uma resposta de consulta NODATA.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [método de roteamento de tráfego geográfico](traffic-manager-routing-methods.md#geographic).
- Saiba como [testar as configurações do Gerenciador de tráfego](traffic-manager-testing-settings.md).

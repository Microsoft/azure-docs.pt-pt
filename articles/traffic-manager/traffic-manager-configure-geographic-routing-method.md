---
title: 'Tutorial: Configurar o encaminhamento de tráfego geográfico com o Gestor de Tráfego da Azure'
description: Este tutorial explica como configurar o método de encaminhamento de tráfego geográfico usando O Gestor de Tráfego Azure
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188671"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Tutorial: Configurar o método de encaminhamento de tráfego geográfico usando o Gestor de Tráfego

O método de encaminhamento de tráfego geográfico permite-lhe direcionar o tráfego para pontos finais específicos com base na localização geográfica de onde os pedidos são originários. Este tutorial mostra-lhe como criar um perfil de Gestor de Tráfego com este método de encaminhamento e configurar os pontos finais para receber tráfego de geografias específicas.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> - Crie um perfil de Gestor de Tráfego com encaminhamento geográfico.
> - Configurar um ponto final aninhado.
> - Utilize o perfil de Gestor de Tráfego.
> - Apague o perfil do Gestor de Tráfego.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil de gestor de tráfego

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com).

1. **Selecione + Crie um recurso** no lado esquerdo. Procure o **perfil de Gestor de Tráfego** e selecione **Criar**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Criar um perfil do Gestor de Tráfego":::

1. Na página de *perfil do Gestor de Tráfego,* defina as seguintes definições:

    | Definição         | Valor                                              |
    | ---             | ---                                                |
    | Nome            | Forneça um nome para o seu perfil. Este nome tem de ser único dentro da zona trafficmanager.net. Para aceder ao seu perfil de Gestor de Tráfego, utilize o nome DNS `<profilename>.trafficmanager.net` . |    
    | Método de encaminhamento  | Selecione **Geographic**. |
    | Subscrição    | Selecione a sua subscrição. |
    | Grupo de recursos   | Utilize um grupo de recursos existente ou crie um novo grupo de recursos para colocar este perfil sob. Se optar por criar um novo grupo de recursos, utilize a *localização do Grupo de Recursos* para especificar a localização do grupo de recursos. Esta definição refere-se à localização do grupo de recursos, e não tem qualquer impacto no perfil do Gestor de Tráfego que é implantado globalmente. |

1. Selecione **Criar** para implementar o seu perfil de Gestor de Tráfego.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Criar uma página de perfil do Gestor de Tráfego":::

## <a name="add-endpoints"></a>Adicionar pontos finais

1. Selecione o perfil de Gestor de Tráfego da lista.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Lista geográfica do Gestor de Tráfego":::

1. Selecione **pontos de final** em *Definições* e selecione **+ Adicione** para adicionar um novo ponto final.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Adicionar pontos finais":::

1. Selecione ou introduza as seguintes definições: 

    | Definição                | Valor                                              |
    | ---                    | ---                                                |
    | Tipo                   | Selecione o tipo de ponto final. Para perfis de encaminhamento geográficos utilizados na produção, recomendamos vivamente a utilização de tipos de pontos finais aninhados que contenham um perfil infantil com mais de um ponto final. Para obter mais informações, consulte [as PERGUNTAS Frequentes sobre os métodos de encaminhamento de tráfego geográfico.](traffic-manager-FAQs.md) |    
    | Name                   | Dê um nome para identificar este ponto final. |
    | Tipo de recurso de destino   | Selecione o tipo de recurso para o alvo. |
    | Recurso de destino        | Selecione o recurso da lista. |

    > [!Note]
    > Certos campos nesta página dependem do tipo de ponto final que está a adicionar:
    > 1. Se estiver a adicionar um ponto final Azure, selecione o **tipo de recurso Target** e o **Target** com base no recurso a que pretende dirigir o tráfego
    > 1. Se estiver a adicionar um ponto final **externo,** forneça o **nome de domínio totalmente qualificado (FQDN)** para o seu ponto final.
    > 1. Se estiver a adicionar um **ponto final aninhado,** selecione o **recurso Target** que corresponde ao perfil da criança que pretende utilizar e especifique a contagem mínima de **pontos finais** para crianças .

1. Na secção *de Geo mapping,* utilize o drop-down para adicionar as regiões de onde deseja que o tráfego seja enviado para este ponto final. Deve ser acrescentada pelo menos uma região. Pode mapetar várias regiões.

1. Repita o último passo para todos os pontos finais que pretende adicionar neste perfil e, em seguida, **selecione Guardar**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Adicionar um ponto final do Gestor de Tráfego":::

## <a name="use-the-traffic-manager-profile"></a>Utilize o perfil do Gestor de Tráfego

1.  Na barra de pesquisa do portal, procure o nome de **perfil do Gestor de Tráfego** que criou na secção anterior e selecione o perfil do gestor de tráfego nos resultados que o apresentado.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Perfil do gestor de tráfego de pesquisa":::

1. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar. O nome pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final direito, conforme determinado pelo tipo de encaminhamento. Com o encaminhamento geográfico, o Gestor de Tráfego olha para a origem do pedido de entrada e determina a região de onde está a ser originária. Se essa região for mapeada para um ponto final, o tráfego é encaminhado para lá. Se esta região não estiver mapeada para um ponto final, então o Gestor de Tráfego devolve uma resposta de consulta NODATA.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Visão geral do Gestor de Tráfego Geográfico":::

## <a name="clean-up-resources"></a>Limpar recursos

Se já não necessitar do perfil do Gestor de Tráfego, localize o perfil e selecione **Excluir o perfil**.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Eliminar perfil do Gestor de Tráfego":::

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o método de encaminhamento geográfico, consulte:

> [!div class="nextstepaction"]
> [Método de encaminhamento de tráfego geográfico](traffic-manager-routing-methods.md#geographic)

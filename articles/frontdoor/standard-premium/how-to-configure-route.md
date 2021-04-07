---
title: Configurar a rota da porta da frente azul
description: Este artigo mostra como configurar uma Rota entre os seus domínios e grupos de origem.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099538"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Configure uma rota padrão/premium da porta da frente azul

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Este artigo explica cada uma das definições utilizadas na criação de uma Rota da Porta Frontal Azure (AFD) para um ponto final existente. Depois de ter adicionado um domínio e origem personalizados ao seu ponto final da Porta Frontal Azure existente, precisa de configurar a rota para definir a associação entre domínios e origens para encaminhar o tráfego entre eles.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma Rota da Porta Frontal Azure, deve ter criado pelo menos um grupo de origem e um domínio personalizado dentro do ponto final atual. 

Para criar um grupo de origem, consulte criar um novo grupo de [origem Azure Front Door Standard/Premium](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Criar uma nova Rota Padrão/Premium da Porta frontal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu perfil Azure Front Door Standard/Premium.

1. Selecione **Gestor de Pontos de Final em** **Definições**.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Screenshot das definições do Gestor de Pontos de Extremidade da Porta Frontal." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Em seguida, **selecione Editar Endpoint** para o ponto final que pretende configurar a Rota.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Screenshot de selecionar o ponto final de edição.":::

1. Aparecerá a página **Editar Endpoint.** Selecione **+ Adicionar** para Rotas.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Screenshot de adicionar uma rota na página de ponto final de edição.":::    
    
1. Na página **'Adicionar Rota',** insira ou selecione as seguintes informações.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Screenshot de adicionar uma página de rota." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Definição | Valor |
    | --- | --- |
    | Nome | Insira um nome único para a nova Rota. |   
    | Domínio| Selecione um ou mais domínios que foram validados e não estão associados a outra Rota |
    | Padrões para combinar  | Configure todos os padrões de caminhos URL que esta rota aceitará. Por exemplo, pode definir isto `/images/*` para aceitar todos os pedidos no URL `www.contoso.com/images/*` . A AFD tentará determinar o tráfego baseado no Exact Match primeiro, se não corresponder em Paths, em seguida, procure um caminho wildcard que corresponda. Se não forem encontradas regras de encaminhamento com um Caminho correspondente, rejeitar o pedido e devolver uma resposta HTTP de erro de pedido de erro 400: Erro de pedido de mau pedido. |
    | Protocolos aceites | Especifique os protocolos que deseja que a Porta Frontal Azure aceite quando o cliente estiver a fazer o pedido. |
    | Redirecionar | Especificar se HTTPS é aplicado para o pedido de entrada com pedido HTTP |
    | Grupo de origem | Selecione para que grupo de origem deve ser reencaminhado quando o pedido de volta à origem ocorrer. |
    | Caminho da Origem | Insira o caminho para os recursos que deseja cache. Para permitir a caching de qualquer recurso no domínio, deixe esta definição em branco. |
    | Protocolo de encaminhamento | Selecione o protocolo utilizado para reencaminhar o pedido. |
    | Colocação em cache | Selecione esta opção para permitir o cache de conteúdo estático com a Porta Frontal Azure. |
    | Regra | Selecione Conjuntos de regras que serão aplicados a esta Rota. Para obter mais informações sobre como configurar regras, consulte [Configurar uma regra definida para porta frontal azul](how-to-configure-rule-set.md) | 

1. **Selecione Adicionar** para criar a nova Rota. A Rota aparecerá na lista de Rotas para o ponto final.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Screenshot da lista de rotas.":::  
    
## <a name="clean-up-resources"></a>Limpar os recursos

Para eliminar uma rota quando já não precisar, selecione a Rota e, em seguida, selecione **Eliminar**. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Screenshot de como apagar uma rota.":::  

## <a name="next-steps"></a>Passos seguintes
Para aprender sobre domínios personalizados, continue ao tutorial para adicionar um domínio personalizado ao seu ponto final da Porta Frontal Azure.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado]()

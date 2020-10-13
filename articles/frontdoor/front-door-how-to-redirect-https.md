---
title: Criar uma porta frontal com https para reorientação HTTPS usando o portal Azure
description: Saiba como criar uma Porta frontal com tráfego redirecionado de HTTP para HTTPS utilizando o portal Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91626647"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Criar uma porta frontal com https para reorientação HTTPS usando o portal Azure

Pode utilizar o portal Azure para [criar uma Porta frontal](quickstart-create-front-door.md) com um certificado para a rescisão de TLS. Uma regra de encaminhamento é utilizada para redirecionar o tráfego HTTP PARA HTTPS.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Criar uma Porta frontal com um recurso de Aplicação Web existente

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

1. **Selecione Criar um recurso** encontrado no canto superior esquerdo do portal Azure.

1. Procure a **Porta Frontal** utilizando a barra de pesquisa e assim que encontrar o tipo de recurso, selecione **Criar**.

1. Escolha uma *subscrição* e, em seguida, utilize um grupo de recursos existente ou crie um novo. Selecione **Seguinte** para introduzir o separador de configuração.

    > [!NOTE]
    > A localização pedida na UI é apenas para o grupo de recursos. A configuração da porta da frente será implantada em todas as [localizações POP da Porta Frontal do Azure](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="Configurar o básico para a nova Porta da Frente":::

1. A configuração para Porta Frontal acontece em três passos - adicionando um anfitrião frontal padrão, adicionando backends em uma piscina de backend e, em seguida, criando regras de encaminhamento para mapear o comportamento de encaminhamento para o anfitrião frontal. Selecione o **+** ícone ' ' nos _anfitriões Frontend_ para criar um anfitrião frontal.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Configurar o básico para a nova Porta da Frente":::

1. Insira um nome globalmente único para o seu anfitrião frontal padrão para a sua Porta da Frente. **Selecione Adicionar** para continuar ao próximo passo.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="Configurar o básico para a nova Porta da Frente":::

### <a name="create-backend-pool"></a>Criar piscina backend

1. Selecione o **+** ícone ' ' nas _piscinas backend_ para criar uma piscina de backend. Forneça um nome para a piscina de backend e, em seguida, **selecione Adicione um backend**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Configurar o básico para a nova Porta da Frente":::

1. Selecione o Tipo anfitrião backend como _serviço de aplicação_. Selecione a subscrição onde a sua aplicação web está hospedada e, em seguida, selecione a aplicação web específica a partir do dropdown para **o nome de anfitrião backend**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="Configurar o básico para a nova Porta da Frente":::

1. **Selecione Adicionar** para guardar o backend e selecione **Adicionar** novamente para guardar o pool config. 

## <a name="create-http-to-https-redirect-rule"></a>Criar http para verror regra

1. Selecione o **+** ícone ' ' nas *regras de encaminhamento* para criar uma rota. Fornecer um nome para a rota, por exemplo 'HttpToHttpsRedirect', e, em seguida, definir o campo *de protocolo aceite* **apenas para 'HTTP'.** Certifique-se de que os *domínios/frontend/domínios adequados são selecionados.*  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Configurar o básico para a nova Porta da Frente":::

1. Na secção Detalhes da *Rota,* desacione o *Tipo de Rota* para **Redirecionamento**. Certifique-se de que o *tipo de redirecionamento* é definido para **Found (302)** e *o protocolo de redirecionamento* é definido **apenas**para HTTPS . 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="Configurar o básico para a nova Porta da Frente":::

1. **Selecione Adicionar** para guardar a regra de encaminhamento para HTTPS redirecionamento.

## <a name="create-forwarding-rule"></a>Criar regra de encaminhamento

1. Adicione outra regra de encaminhamento para manusear o tráfego HTTPS. Selecione o **+** sinal ' ' nas *regras de encaminhamento* e forneça um nome para a rota, por exemplo 'DefaultForwardingRoute'. Em seguida, desa um campo *de protocolos aceites apenas* para **HTTPS**. Certifique-se de que os *domínios/frontend/domínios adequados são selecionados.*

1. Na secção 'Detalhes da Rota', desagrafe o *Tipo de Rota* para **Encaminhar**. Certifique-se de que o pool de backend direito é selecionado e que o *Protocolo de Encaminhamento* é definido apenas para **HTTPS**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="Configurar o básico para a nova Porta da Frente" border="false":::

1. **Selecione Adicionar** para guardar a regra de encaminhamento para reencaminhamento de pedido.

1. Selecione **Rever + criar** e, em seguida, **criar,** para criar o seu perfil porta frontal. Vá para o recurso uma vez criado.

## <a name="next-steps"></a>Passos seguintes

- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
- Saiba mais sobre [o redirecionamento de URL na Porta frontal.](front-door-url-redirect.md)

---
title: 'Tutorial: Adicione cabeçalhos de segurança com o Motor de Regras - Porta frontal Azure'
description: Este tutorial ensina-lhe a configurar um cabeçalho de segurança através do Motor de Regras na Porta frontal Azure
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 1727193faa028a1d681f2a74df950afeb9570ec9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91270054"
---
# <a name="tutorial-add-security-headers-with-rules-engine"></a>Tutorial: Adicione cabeçalhos de segurança com o motor de regras

Este tutorial mostra como implementar cabeçalhos de segurança para prevenir vulnerabilidades baseadas no navegador como HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy ou X-Frame-Options. Os atributos baseados em segurança também podem ser definidos com cookies.

O exemplo a seguir mostra-lhe como adicionar um cabeçalho de Política de Segurança de Conteúdo a todos os pedidos de entrada que correspondam ao caminho definido na rota com que a configuração do Motor de Regras está associada. Aqui, apenas permitimos que scripts do nosso site de confiança, **https://apiphany.portal.azure-api.net** para executar na nossa aplicação.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Configure uma política de segurança de conteúdo dentro do motor de regras.

## <a name="prerequisites"></a>Pré-requisitos

* Para concluir os passos neste tutorial, tem primeiro de criar um Front Door. Para obter mais informações, veja [Quickstart: Create a Front Door](quickstart-create-front-door.md) (Início Rápido: Criar um Front Door).
* Se esta for a sua primeira utilização da função "Rules Engine", consulte como [configurar um motor de regras.](front-door-tutorial-rules-engine.md)

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Adicione um cabeçalho de Política de Segurança de Conteúdo no portal Azure

1. Clique em **Adicionar** para adicionar uma nova regra. Forneça à regra um nome e, em seguida, clique **em Adicionar um**  >  **cabeçalho de resposta de ação**.

1. Desaprote o operador para ser **o apêndice** para adicionar este cabeçalho como resposta a todos os pedidos de entrada nesta rota.

1. Adicione o nome do cabeçalho: **Política de Segurança de Conteúdo** e defina os valores que este cabeçalho deve aceitar. Neste cenário, escolhemos *"script-src https://apiphany.portal.azure-api.net 'self'.".*

1. Depois de ter adicionado todas as regras que pretende à sua configuração, não se esqueça de ir à sua rota preferida e associar a configuração do Seu Motor de Regras à sua Regra de Rota. Este passo é necessário para permitir que a regra funcione. 

![amostra portal](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> Neste cenário, não acrescentámos [condições de jogo](front-door-rules-engine-match-conditions.md) à regra. Todos os pedidos de entrada que correspondam ao caminho definido na Regra da Rota terão esta regra aplicada. Se quiser que se aplique apenas a um subconjunto desses pedidos, certifique-se de adicionar as suas **condições** específicas de correspondência a esta regra.

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, configuraste cabeçalhos de segurança com o Motor das Regras. Se já não quiser a regra, pode removê-la clicando na regra Eliminar.

:::image type="content" source="./media/front-door-rules-engine/rules-engine-delete-rule.png" alt-text="Eliminar Regra":::

## <a name="next-steps"></a>Passos seguintes

Para aprender a configurar uma Firewall de Aplicação Web para a sua Porta frontal, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Firewall de aplicações Web e Front Door](front-door-waf.md)

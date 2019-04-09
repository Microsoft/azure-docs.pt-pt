---
title: Criar uma política de firewall de aplicações web para a porta da frente do Azure com o portal do Azure
titlesuffix: Azure web application firewall
description: Saiba como criar uma política de firewall (WAF) de aplicações web com o portal do Azure.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/8/2019
ms.author: kumud;tyao
ms.openlocfilehash: b2fbe57a94ad4c5faad3318c03f797446bea4535
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288520"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Criar uma política de WAF para a porta da frente do Azure com o portal do Azure

Este artigo descreve como criar uma política de firewall (WAF) de aplicações web do Azure básica e aplicá-la para um anfitrião de front-end na porta da frente do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Criar um perfil de porta de entrada ao seguir as instruções descritas em [início rápido: Criar um perfil de porta de entrada](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>Criar uma política de WAF

Em primeiro lugar, crie uma política de WAF básica com gerenciado predefinido regra definido (DSR) com o portal. 

1. No canto superior esquerdo do ecrã, selecione **criar um recurso**> procure **WAF**> selecione **firewall de aplicações Web (pré-visualização)** > selecione  **Criar**.
2. Na **Noções básicas** separador da **criar uma política de WAF** página, introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição            |Selecione o nome de subscrição de porta de entrada.|
    | Grupo de recursos          |Selecione o seu nome de grupo de recursos de porta de entrada.|
    | Nome da política             |Introduza um nome exclusivo para a sua política de WAF.|

   ![Criar uma política de WAF](./media/waf-front-door-create-portal/basic.png)

3. Na **associação** separador da **criar uma política de WAF** página, selecione **Adicionar anfitrião de front-end**, introduza as seguintes definições e, em seguida, selecione **adicionar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Porta de entrada              | Selecione o nome do perfil de porta de entrada.|
    | Anfitrião de front-end           | Selecione o nome do seu anfitrião de porta de entrada, em seguida, selecione **adicionar**.|
    
    > [!NOTE]
    > Se o anfitrião de front-end está associado a uma política de WAF, é mostrado como a cinzento. Tem primeiro de remover o anfitrião de front-end da política associada e, em seguida, volta a associar o anfitrião de front-end para uma nova política de WAF.
1. Selecione **rever + criar**, em seguida, selecione **criar**.

## <a name="configure-waf-rules-optional"></a>Configurar regras de WAF (opcionais)

### <a name="change-mode"></a>Alterar modo

Quando cria uma política de WAF, com a WAF a predefinição policy está em **deteção** modo. Na **deteção** modo, WAF não bloqueia todos os pedidos, em vez disso, os pedidos de regras WAF de correspondência são registados registos WAF.
Para ver o WAF em ação, pode alterar as definições do modo de **detecção** ao **prevenção**. Na **prevenção** modo, pedidos que as regras de correspondência definidos na predefinição regra definido (DRS) são bloqueadas e registadas registos WAF.

 ![Modo de política de alteração WAF](./media/waf-front-door-create-portal/policy.png)

### <a name="default-rule-set-drs"></a>Conjunto de regras padrão (DRS)

Função de conjunto de regras de padrão geridos pelo Azure está ativada por predefinição. Para desativar uma regra de individual dentro de um grupo de regras, expandir as regras dentro desse grupo de regra, selecione o **caixa de verificação** à frente de número de regra e selecione **desativar** no separador acima. Para alterar os tipos de ações para as regras individuais dentro da regra definida, selecione a caixa de verificação na frente do número de regra e, em seguida, selecione o **altere ação** separador acima.

 ![Alterar o conjunto de regras WAF](./media/waf-front-door-create-portal/managed.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [ firewall de aplicações web do Azure](waf-overview.md).
- Saiba mais sobre [porta da frente do Azure](front-door-overview.md).





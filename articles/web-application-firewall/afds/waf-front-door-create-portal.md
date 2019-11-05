---
title: 'Tutorial: criar uma política de firewall do aplicativo Web para a porta frontal do Azure usando o portal do Azure'
description: Neste tutorial, você aprenderá a criar uma política de WAF (firewall do aplicativo Web) usando o portal do Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/07/2019
ms.author: victorh
ms.openlocfilehash: 602a8620ef35ea247c5865d97495ea43a7d7d93c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501114"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Tutorial: criar uma política de firewall do aplicativo Web na porta frontal do Azure usando o portal do Azure

Este tutorial mostra como criar uma política básica do WAF (firewall do aplicativo Web) do Azure e aplicá-la a um host front-end na porta frontal do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de WAF
> * Associá-lo a um host de front-end
> * Configurar regras do WAF

## <a name="prerequisites"></a>Pré-requisitos

Crie um perfil de porta frontal seguindo as instruções descritas em [início rápido: criar um perfil de porta frontal](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Criar uma política de firewall do aplicativo Web

Primeiro, crie uma política de WAF básica com o DRS (conjunto de regras padrão) gerenciado usando o Portal. 

1. No canto superior esquerdo da tela, selecione **criar um recurso**> Pesquisar por **WAF**> selecione **Firewall do aplicativo Web (versão prévia)** > selecione **criar**.
2. Na guia **noções básicas** da página **criar uma política de WAF** , insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **revisar + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição            |Selecione o nome da assinatura do seu front door.|
    | Grupo de recursos          |Selecione o nome do grupo de recursos da porta frontal.|
    | Nome da política             |Insira um nome exclusivo para sua política de WAF.|

   ![Criar uma política de WAF](../media/waf-front-door-create-portal/basic.png)

3. Na guia **Associação** da página **criar uma política de WAF** , selecione **Adicionar host de front-end**, insira as seguintes configurações e, em seguida, selecione **Adicionar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Porta frontal              | Selecione o nome do perfil de porta frontal.|
    | Host de front-end           | Selecione o nome do host de porta frontal e, em seguida, selecione **Adicionar**.|
    
    > [!NOTE]
    > Se o host de front-end estiver associado a uma política de WAF, ele será mostrado como esmaecido. Você deve primeiro remover o host de front-end da política associada e, em seguida, associar novamente o host de front-end a uma nova política de WAF.
1. Selecione **revisar + criar**e, em seguida, selecionar **criar**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configurar regras de firewall do aplicativo Web (opcional)

### <a name="change-mode"></a>Alterar modo

Quando você cria uma política de WAF, a política de WAF padrão está no modo de **detecção** . No modo de **detecção** , o WAF não bloqueia nenhuma solicitação, em vez disso, as solicitações correspondentes às regras do WAF são registradas em logs do WAF.
Para ver WAF em ação, você pode alterar as configurações de modo de **detecção** para **prevenção**. No modo de **prevenção** , as solicitações que correspondem às regras definidas no conjunto de regras padrão (DRS) são bloqueadas e registradas em logs do WAF.

 ![Alterar o modo de política WAF](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Regras personalizadas

Você pode criar uma regra personalizada selecionando **Adicionar regra personalizada** na seção **regras personalizadas** . Isso inicia a página de configuração de regra personalizada. Abaixo está um exemplo de configuração de uma regra personalizada para bloquear uma solicitação se a cadeia de caracteres de consulta contiver **blockme**.

![Alterar o modo de política WAF](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>DRS (conjunto de regras padrão)

O conjunto de regras padrão gerenciadas pelo Azure é habilitado por padrão. Para desabilitar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, marque a **caixa de seleção** na frente do número da regra e selecione **desabilitar** na guia acima. Para alterar os tipos de ações para regras individuais dentro do conjunto de regras, marque a caixa de seleção na frente do número da regra e, em seguida, selecione a guia **ação de alteração** acima.

 ![Alterar conjunto de regras WAF](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o Firewall do aplicativo Web do azure](../overview.md)
> [saiba mais sobre a porta frontal do Azure](../../frontdoor/front-door-overview.md)
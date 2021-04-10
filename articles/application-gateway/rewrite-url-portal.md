---
title: Reescrever URL e cadeia de consulta com Azure Application Gateway - Portal Azure
description: Saiba como usar o portal Azure para configurar um Gateway de aplicação Azure para reescrever URL e cadeia de consultas
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: how-to
ms.date: 4/05/2021
ms.author: azhussai
ms.openlocfilehash: b8ddc5e57b9ce56d6bce7e220bc840ba0fa43ae2
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384762"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal"></a>Reescrever URL com Gateway de Aplicação Azure - Portal Azure

Este artigo descreve como usar o portal Azure para configurar uma [instância SKU do Gateway v2 para](application-gateway-autoscaling-zone-redundant.md) reescrever URL.

>[!NOTE]
> A funcionalidade de reescrita de URL está disponível apenas para Standard_v2 e WAF_v2 SKU de Gateway de aplicação. Quando a reescrita de URL é configurada num gateway ativado pela WAF, a avaliação da WAF será efetuada nos cabeçalhos de pedido reescrito e URL. [Saiba mais](rewrite-http-headers-url.md#using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Precisa de um exemplo de SKU do Gateway v2 para completar os passos deste artigo. A URL de reescrita não é suportada no V1 SKU. Se não tiver o V2 SKU, crie uma [instância SKU SKU do Gateway v2](tutorial-autoscale-ps.md) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

## <a name="configure-url-rewrite"></a>Configurar a reescrita de URLs

No exemplo abaixo sempre que o URL de pedido contém */artigo,* o caminho URL e a cadeia de consulta URL são reescritos

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. Selecione **Todos os recursos** e, em seguida, selecione o seu gateway de aplicações.

2. **Selecione Reescreve** no painel esquerdo.

3. Selecione **Conjunto de reescrita**:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="Adicione conjunto de reescrita":::

4. Fornecer um nome para o conjunto de reescrita e associá-lo a uma regra de encaminhamento:

    a. Introduza o nome para o conjunto de reescrita na caixa **Nome.**
    
    b. Selecione uma ou mais das regras listadas na lista **de regras de encaminhamento associados.** Isto é utilizado para associar a configuração de reescrita ao ouvinte de origem através da regra de encaminhamento. Pode selecionar apenas as regras de encaminhamento que não tenham sido associadas a outros conjuntos de reescrita. As regras que já foram associadas a outros conjuntos de reescritas estão acinzentados.
    
    c. Selecione **Seguinte**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="Associar-se a uma regra":::

5. Criar uma regra de reescrita:

    a. Selecione **Adicionar regra de reescrita**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="Screenshot que realça Adicionar regra de reescrita.":::
    
    b. Introduza um nome para a regra de reescrita na caixa **de nome de regra de reescrita.** Introduza um número na caixa **de sequência de regras.**

6. Neste exemplo, vamos reescrever o caminho do URL e a cadeia de consulta de URL apenas quando o caminho *contiver /artigo*. Para isso, adicione uma condição para avaliar se o caminho URL contém */artigo*

    a. **Selecione Adicione a condição** e, em seguida, selecione a caixa que contém as instruções para expandi-la. 
    
    b. Uma vez que neste exemplo queremos verificar o padrão */artigo* no caminho URL, no **Tipo de variável para verificar** lista, selecione a **variável servidor**.
    
    c. Na lista **variável do Servidor,** selecione uri_path
    
    d. No **âmbito do caso sensível ao caso,** selecione **Nº**.
    
    e. Na lista **do Operador,** selecione **igual (=)**.
    
    f. Introduza um padrão de expressão regular. Neste exemplo, vamos usar o padrão `.*article/(.*)/(.*)`
    
      ( ) é utilizado para capturar o sublges para posterior utilização na composição da expressão para reescrever o caminho URL. Para mais informações, consulte [aqui.](rewrite-http-headers-url.md#capturing)

    exemplo, Selecione **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Condition":::

 

7. Adicione uma ação para reescrever o caminho URL e URL

   a. Na lista de **tipos de reescrita,** selecione **URL**.

   b. Na lista **de tipos de Ação,** selecione **set**.

   c. Em **Componentes**, selecione **ambos o caminho URL e cadeia de consulta URL**

   d. No valor do **caminho URL,** insira o novo valor do caminho. Neste exemplo, vamos usar **/artigo.aspx** 

   e. No valor da **cadeia de consulta de URL,** insira o novo valor da cadeia de consulta URL. Neste exemplo, usaremos **id={var_uri_path_1}&título={var_uri_path_2}**
    
    `{var_uri_path_1}` e `{var_uri_path_1}` são usados para buscar os sublpementos capturados enquanto avaliam a condição nesta expressão `.*article/(.*)/(.*)`
    
   f. Selecione **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="Ação":::

8. Clique em **Criar** para criar o conjunto de reescrita.

9. Verifique se o novo conjunto de reescrita aparece na lista de conjuntos de reescrita

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="Adicionar regra de reescrita":::

## <a name="verify-url-rewrite-through-access-logs"></a>Verifique a reescrita de URL através de registos de acesso

Observe os campos abaixo nos registos de acesso para verificar se a reescrita de URL aconteceu de acordo com a sua expectativa.

* **originalRequestUriWithArgs**: Este campo contém o URL de pedido original
* **requestUri**: Este campo contém o URL após a operação de reescrita no Gateway de Aplicação

Para mais informações sobre todos os campos nos registos de acesso, consulte [aqui.](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku)

##  <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como configurar reescritas para alguns casos de uso comum, consulte [cenários comuns de reescrita](rewrite-http-headers.md).

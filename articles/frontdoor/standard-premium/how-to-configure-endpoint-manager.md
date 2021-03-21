---
title: Configurar Azure Front Door Standard/Premium endpoint com Endpoint Manager
description: Este artigo mostra como configurar um ponto final com o Endpoint Manager.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099670"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Configure um ponto final standard/premium da porta frontal Azure com o Endpoint Manager

> [!NOTE]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver **[Azure Front Door Docs](../front-door-overview.md)**.

Este artigo mostra-lhe como criar um ponto final para um perfil Azure Front Door Standard/Premium com o Endpoint Manager.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar um ponto final Azure Front Door Standard/Premium com Endpoint Manager, deve ter criado pelo menos um perfil Azure Front Door criado. O perfil tem de ter pelo menos um ou mais pontos finais Azure Front Door Standard/Premium. Para organizar os seus pontos finais Azure Front Door Standard/Premium por domínio de internet, aplicação web ou outros critérios, pode utilizar vários perfis. 

Para criar um perfil da Porta Frontal Azure, consulte [Criar um novo perfil Standard/Premium da Porta Frontal azul.](create-front-door-portal.md)

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Criar um novo Azure Front Door Standard/Premium Endpoint

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu perfil Azure Front Door Standard/Premium.

1. Selecione **Gestor de Pontos de Final**. Em seguida, **selecione Adicionar um Ponto Final** para criar um novo Ponto Final.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Screenshot de adicionar um ponto final através do Endpoint Manager.":::

1. Na página **'Adicionar um ponto final',** insira e selecione as seguintes definições.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="Screenshot de adicionar uma página de ponto final.":::

    | Definições | Valor |
    | -------- | ----- |
    | Nome | Insira um nome único para o novo Azure Front Door Standard/Premium endpoint. Este nome é usado para aceder aos seus recursos em cache no domínio `<endpointname>.az01.azurefd.net` |
    | Intervalo de tempo de resposta à origem (segs) | Introduza um valor de tempo limite em segundos que a Porta Frontal Azure aguarda antes de considerar que a ligação com a origem tem tempo limite. |
    | Estado | Selecione a caixa de verificação para ativar este ponto final. |

## <a name="add-domains-origin-group-routes-and-security"></a>Adicionar Domínios, Grupo de Origem, Rotas e Segurança

1. **Selecione Editar Ponto final** no ponto final para configurar a rota.

1. Na página **'Editar Ponto final',** selecione **+ Adicionar** em Domínios.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Screenshot do domínio selecionado na página Editar Endpoint.":::

### <a name="add-domain"></a>Adicionar domínio

1. Na página **Add Domain,** opte por associar um domínio *do seu perfil da Porta Frontal Azure* ou adicione um novo *domínio*. Para obter informações sobre como criar um novo domínio, consulte [Criar um novo domínio personalizado Azure Front Door Standard/Premium](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Screenshot de Adicionar uma página de domínio.":::

1. **Selecione Adicionar** para adicionar o domínio ao ponto final atual. O domínio selecionado deve aparecer dentro do painel de domínio.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Screenshot de domínios na vista de domínio.":::

### <a name="add-origin-group"></a>Grupo Add Origin

1. **Selecione Adicionar** na vista dos grupos Origin. Aparece a página **do grupo Add uma origem** 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="Screenshot de adicionar uma página de grupo de origem":::

1. Para **nome,** insira um nome único para o novo grupo de origem

1. **Selecione Adicionar uma Origem** para adicionar uma nova origem ao grupo atual.
 
#### <a name="health-probes"></a>Sondas de Saúde
A Porta frontal envia pedidos periódicos de sonda HTTP/HTTPS para cada uma das suas origens. Os pedidos de sonda determinam a proximidade e a saúde de cada origem para carregar o equilíbrio dos seus pedidos de utilizador final. As configurações da sonda de saúde para um grupo de origem definem como sondamos o estado de saúde da origem da aplicação. Estão disponíveis as seguintes definições para a configuração de equilíbrio de carga:

> [!WARNING]
> Uma vez que a Porta Frontal tem muitos ambientes de borda a nível global, o volume da sonda de saúde para a sua origem pode ser bastante elevado - variando de 25 pedidos a cada minuto até 1200 pedidos por minuto, dependendo da frequência da sonda de saúde configurada. Com a frequência padrão da sonda de 30 segundos, o volume da sonda na sua origem deve ser de cerca de 200 pedidos por minuto.

* **Estado**: Especificar se deve ligar a sondagem sanitária. Se tiver uma única origem no seu grupo de origem, pode optar por desativar as sondas de saúde reduzindo a carga no backend da sua aplicação. Mesmo que tenha múltiplas origens no grupo, mas apenas uma delas está em estado ativado, pode desativar as sondas de saúde.
   
* **Caminho**: O URL utilizado para pedidos de sonda para toda a origem deste grupo de origem. Por exemplo, se uma das suas origens for contoso-westus.azurewebsites.net e o caminho estiver definido para /sonda/teste.aspx, então os ambientes da Porta Frontal, assumindo que o protocolo está definido para HTTP, enviarão pedidos de sonda de saúde para `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **Protocolo**: Define se deve enviar os pedidos de sonda de saúde da Porta frontal para a sua origem com o protocolo HTTP ou HTTPS.
   
* **Método da sonda**: O método HTTP a utilizar para o envio de sondas de saúde. As opções incluem GET ou HEAD (predefinição).

    > [!NOTE]
    > Para uma carga mais baixa e um custo na sua origem, a Porta Frontal recomenda a utilização de pedidos DE CABEÇA para sondas sanitárias.

* **Intervalo (em segundos)**: Define a frequência das sondas sanitárias até à sua origem ou os intervalos em que cada um dos ambientes da porta frontal envia uma sonda.
   
    >[!NOTE]
    >Para falhas mais rápidas, desa um intervalo para um valor mais baixo. Quanto menor for o valor, maior o volume da sonda de saúde que a sua origem recebe. Por exemplo, se o intervalo for definido para 30 segundos com, digamos, 100 POPs front door globalmente, cada backend receberá cerca de 200 pedidos de sonda por minuto.

#### <a name="load-balancing"></a>Balanceamento de carga
As definições de equilíbrio de carga para o grupo de origem definem como avaliamos as sondas de saúde. Estas definições determinam se o backend é saudável ou pouco saudável. Também verificam como equilibrar o tráfego entre diferentes origens no grupo de origem. Estão disponíveis as seguintes definições para a configuração de equilíbrio de carga:

- **Tamanho da amostra.** Identifica quantas amostras de sondas de saúde precisamos considerar para avaliação de saúde de origem.

- **Tamanho da amostra bem-sucedido.** Define o tamanho da amostra como mencionado anteriormente, o número de amostras bem sucedidas necessárias para chamar a origem de saudável. Por exemplo, assuma que um intervalo de sonda de saúde da porta frontal é de 30 segundos, o tamanho da amostra é 5, e o tamanho da amostra bem-sucedido é 3. Cada vez que avaliamos as sondas de saúde para a sua origem, olhamos para as últimas cinco amostras em 150 segundos (5 x 30). Pelo menos três sondas bem sucedidas são necessárias para declarar o backend como saudável.

- **Sensibilidade à latência (latência extra)**. Define se deseja que a Porta Frontal envie o pedido à origem dentro do intervalo de sensibilidade à medição de latência ou encaminhe o pedido para o backend mais próximo.

**Selecione Adicionar** para adicionar o grupo de origem ao ponto final atual. O grupo de origem deve aparecer dentro do painel do grupo Origin

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Screenshot de origens no grupo de origem.":::

### <a name="add-route"></a>Adicionar Rota

**Selecione Adicionar** na vista Rotas, aparece a página **'Adicionar uma rota'.** Para obter informações sobre como associar o grupo de domínio e origem, consulte [Criar uma nova rota da Porta Frontal Azure](how-to-configure-route.md)

### <a name="add-security"></a>Adicionar Segurança

1. **Selecione Adicionar** na vista de Segurança, aparece a página **de política Add a WAF**
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="Screenshot de adicionar uma página de política da WAF.":::

1. **Política WAF**: selecione uma política DE WAF que gosta de se candidatar ao domínio selecionado dentro deste ponto final. 
  
   Selecione **Create New** para criar uma nova política de WAF.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Screenshot de criar uma nova política da WAF.":::
   
    **Nome**: insira um nome único para a nova política da WAF. Pode editar esta política com mais configuração a partir da página Web Application Firewall.

    **Domínios**: selecione o domínio para aplicar a política WAF.

1. **Selecione Adicionar** o botão. A política da WAF deve aparecer no painel de segurança

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Screenshot da política da WAF na vista de segurança.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Para eliminar um ponto final quando já não for necessário, **selecione Delete Endpoint** no final da linha de ponto final 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="Screenshot de como apagar um ponto final.":::

## <a name="next-steps"></a>Passos seguintes

Para conhecer domínios personalizados, continue a [adicionar um domínio personalizado.](how-to-add-custom-domain.md)

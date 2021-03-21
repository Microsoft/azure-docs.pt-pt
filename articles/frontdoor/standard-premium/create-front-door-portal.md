---
title: 'Quickstart: Criar um perfil Standard/Premium da Porta Frontal Azure - Portal Azure'
description: Este quickstart mostra como usar o Azure Front Door Standard/Premium Service para a sua aplicação web global altamente disponível e de alto desempenho, utilizando o portal Azure.
services: frontdoor
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 18956948a32a79c1435bf4fc1554b09480c9010c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100868"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Quickstart: Criar um perfil Standard/Premium da Porta Frontal Azure - Portal Azure

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [Azure Front Door Docs](../front-door-overview.md).

Neste arranque rápido, aprende-se a criar um perfil Azure Front Door Standard/Premium utilizando o portal Azure. Pode criar o perfil Azure Front Door Standard/Premium através de *Quick Create* com configurações básicas ou através da *criação personalizada* com configurações mais avançadas. Com *a criação personalizada,* implementa duas Web Apps. Em seguida, cria o perfil Azure Front Door Standard/Premium usando as duas Web Apps como origem. Em seguida, verificará a conectividade com as suas Aplicações Web utilizando o nome de anfitrião frontend Standard/Premium da porta frontal Azure.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-front-door-profile---quick-create"></a>Criar perfil da porta da frente - Criar Rápido

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir da página inicial ou do menu Azure, selecione **+ Crie um recurso.** Pesquisa por *Porta Frontal Standard/Premium (Pré-visualização)*. Em seguida, selecione **Criar**.

1. Na página **'Comparar ofertas',** selecione **Quick create**. Em seguida, **selecione Continue a criar uma porta frontal**.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Screenshot de ofertas de comparação.":::

1. Na página de perfil da **porta da frente,** insira ou selecione as seguintes definições.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Screenshot da página de criação rápida da Porta da Frente.":::    

    | Definições | Valor |
    | --- | --- |
    | **Subscrição**  | Selecione a sua subscrição. |
    | **Grupo de recursos**  | **Selecione Criar novo** e *insira o serviço contoso-app* na caixa de texto.|
    | **Nome** | Dê um nome ao seu perfil. Este exemplo utiliza **contoso-afd-quickcreate**. |
    | **Escalão de serviço** | Selecione Standard ou Premium SKU. O SKU padrão é otimizado na entrega de conteúdos. Premium SKU baseia-se na Standard SKU e está focado na segurança. Ver [Comparação de nível](tier-comparison.md).  |
    | **Nome do ponto final** | Insira um nome globalmente único para o seu ponto final. |
    | **Tipo de origem** | Selecione o tipo de recurso para a sua origem. Neste exemplo, selecionamos um serviço de App como a origem que tem o Link Privado ativado. |
    | **Nome hospedeiro de origem** | Insira o nome de anfitrião para a sua origem. |
    | **Ativar Ligação Privada** | Se quiser ter uma ligação privada entre a sua Porta frontal Azure e a sua origem. Para mais detalhes, consulte a [orientação do link privado](concept-private-link.md) e [ative o link privado](how-to-enable-private-link.md).
    | **Colocação em cache** | Selecione a caixa de verificação se pretender cache de conteúdo mais próximo dos utilizadores em todo o mundo usando os POPs de borda da Azure Front Door e a rede Microsoft. |
    | **Política da WAF** | Selecione **Criar uma** nova ou selecione uma política WAF existente a partir do dropdown se quiser ativar esta função. |

    > [!NOTE]
    > Ao criar um perfil Azure Front Door Standard/Premium, tem de selecionar uma origem a partir da mesma subscrição em que a Porta frontal é criada.

1. Selecione **Review + Criar** para obter o perfil da porta da frente em funcionamento.

   > [!NOTE]
   > Pode levar alguns minutos para que as configurações sejam propagadas a todos os POPs de borda.

1. Em seguida, clique em **Criar** para obter o seu perfil da porta frontal implantado e funcionando.

1. Se ativou o Private Link, vá à sua origem (serviço de aplicações neste exemplo). **Selecione Ligação**  >  **Privada Configuração** de Rede . Em seguida, selecione o pedido pendente a partir da Porta frontal Azure e clique em Aprovar. Após alguns segundos, a sua aplicação estará acessível através da Porta Frontal Azure de forma segura.

## <a name="create-front-door-profile---custom-create"></a>Criar perfil da porta frontal - Criar Personalizado

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Criar uma aplicação web com duas instâncias como a origem

Se já tiver uma origem ou um grupo de origem configurado, salte para Criar um Padrão/Premium da porta da frente (pré-visualização) para a sua aplicação.

Neste exemplo, criamos uma aplicação web com dois casos que funcionam em diferentes regiões do Azure. Ambas as instâncias de aplicação web são executadas no modo *Ative/Ative,* para que qualquer um possa tomar o tráfego. Esta configuração difere de uma configuração *Ative/Stand-By,* onde se atua como um failover.

Se ainda não tiver uma aplicação web, use os seguintes passos para configurar uma aplicação web de exemplo.

1. Inicie sessão no portal do Azure em https://portal.azure.com.

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  **WebApp** de recursos .

1. No **separador Básicos** da página **'Criar Aplicação Web',** insira ou selecione as seguintes informações.

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | **Subscrição**               | Selecione a sua subscrição. |
    | **Grupo de recursos**       | **Selecione Criar novo** e introduza *FrontDoorQS_rg1* na caixa de texto.|
    | **Nome**                   | Introduza um **Nome** único para a sua aplicação web. Este exemplo utiliza *o WebAppContoso-001*. |
    | **Publicar** | Selecione **Código**. |
    | **Pilha de runtime**         | Selecione **.NET Core 2.1 (LTS)**. |
    | **Sistema operativo**          | Selecione **Windows**. |
    | **Região**           | Selecione **Central US**. |
    | **Plano do Windows** | Selecione **Criar novo** e insira *o myAppServicePlanCentralUS* na caixa de texto. |
    | **SKU e tamanho** | Selecione **Standard S1 100 total ACU, memória de 1,75 GB**. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Crie rapidamente porta da frente premium SKU no portal Azure":::

1. Selecione **Rever + criar,** rever o resumo e, em seguida, selecione **Criar**. Pode levar vários minutos para implantar para um

Depois da sua implementação estar concluída, crie uma segunda aplicação web. Utilize as mesmas definições acima, com exceção das seguintes definições:

| Definição          | Valor     |
| ---              | ---  |
| **Grupo de recursos**   | **Selecione Criar novo** e introduza *FrontDoorQS_rg2*. |
| **Nome**             | Insira um nome único para a sua Web App, neste exemplo, *WebAppContoso-002*.  |
| **Região**           | Uma região diferente, neste exemplo, *South Central US* |
| **Plano de Serviço de**  >  Aplicações **Plano Windows**         | Selecione **Novo** e insira *o myAppServicePlanSouthCentralUS* e, em seguida, selecione **OK**. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Crie um Padrão/Premium da Porta Da Frente (Pré-visualização) para a sua aplicação

Configure Azure Front Door Standard/Premium (Preview) para direcionar o tráfego do utilizador com base na latência mais baixa entre os dois servidores de aplicações web. Também proteja a porta da frente com firewall de aplicação web. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
 
1. A partir da página inicial ou do menu Azure, selecione **+ Crie um recurso.** Pesquisa por *Porta Frontal Standard/Premium (Pré-visualização)*. Em seguida, selecione **Criar**.

1. Na página **'Comparar ofertas',** selecione **Custom create**. Em seguida, **selecione Continue a criar uma porta frontal**.

1. No **separador Básicos,**   introduza ou selecione as seguintes informações e, em seguida, selecione **Seguinte: Segredo**. 

    | Definição | Valor |
    | --- | --- |
    | **Subscrição** | Selecione a sua subscrição. |
    | **Grupo de recursos** | **Selecione Criar novo** e introduza *FrontDoorQS_rg0* na caixa de texto. |
    | **Localização do grupo de recursos** | Selecione **E.U.A. Leste**. |
    | **Nome do perfil** | Insira um nome único nesta subscrição **Webapp-Contoso-AFD** |
    | **Escalão de serviço** | Selecione **Premium**. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Criar perfil da porta da frente":::

1. *Opcional*: **Segredos**. Se pretender utilizar certificados geridos, este passo é opcional. Se tiver um Cofre-Chave existente em Azure que pretende utilizar para trazer o seu certificado próprio para domínio personalizado, em seguida, **selecione Adicionar um certificado**. Também pode adicionar certificado na experiência de gestão após a criação.

    > [!NOTE]
    > Tem de ter a permissão certa para adicionar o certificado do Azure Key Vault como utilizador. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Screenshot de adicionar um segredo em criação personalizada.":::

1. No **separador Endpoint,** **selecione Adicionar um Ponto Final** e dê ao seu ponto final um nome globalmente único. Pode criar vários pontos finais no seu perfil Azure Front Door Standard/Premium depois de terminar a experiência de criação. Este exemplo utiliza *contoso-frontend*. Deixe o tempo de tempo de resposta da Origem (em segundos) e o estado como padrão. **Selecione Adicionar** para adicionar o ponto final.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Screenshot de adicionar um ponto final.":::

1. Em seguida, adicione um Grupo Origin que contenha as suas duas aplicações web.  **Selecione + Adicionar** para abrir Adicione uma página de grupo de    **origem.** Para nomear, insira *o myOrignGroup* e, em seguida, selecione **+ Adicione uma origem**.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Screenshot de adicionar um grupo de origem.":::

1. Na página **Adicionar uma página de origem,**   insira ou selecione as informações abaixo. Em seguida, selecione **Adicionar**.

    | Definição | Valor |
    | --- | --- |
    | **Nome** | Insira **webapp1** |
    | **Tipo de origem** | Selecione **serviços de aplicações** |
    | **Nome do anfitrião** | Selecione `WebAppContoso-001.azurewebsites.net` |
    | **Cabeçalho de anfitrião de origem** | Selecione `WebAppContoso-001.azurewebsites.net` |
    | **Outros campos** | Deixe todos os outros campos como padrão. |

    > [!NOTE]
    > Ao criar um perfil Azure Front Door Standard/Premium, tem de selecionar uma origem a partir da mesma subscrição em que o Azure Front Door Standard/Premium é criado.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Screenshot de adicionar mais origens.":::

1. Repita o passo 8 para adicionar o segundo webapp de origem002. Selecione `webappcontoso-002.azurewebsite.net` como **nome anfitrião origin** e **cabeçalho anfitrião Origin**.

1. Na página do **grupo Add uma origem,** verá duas origens adicionadas, deixando todos os outros campos padrão.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Screenshot de adicionar uma página de grupo de origem.":::

1. Em seguida, adicione uma Rota para mapear o seu ponto final frontal para o grupo Origin. Esta rota encaminha os pedidos do ponto final para o myOriginGroup. **Selecione + Adicionar** na Rota para configurar uma Rota.  

1. Na página **De rota adicionar,** introduzir ou selecionar as informações abaixo. Em seguida, selecione **Adicionar**.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Adicionar rota sem caching":::

    | Definição | Valor |
    | --- | --- |
    | **Nome** | Insira **MyRoute** |    
    | **Domínio** | Selecione `contoso-frontend.z01.azurefd.net` | 
    | **Nome do anfitrião** | Selecione `WebAppContoso-001.azurewebsites.net` |
    | **Padrões a combinar** | Não altere a predefinição. |
    | **Protocolos aceites** | Não altere a predefinição. | 
    | **Redirecionar** | Deixe-o predefinido para **redirecionar todo o tráfego para utilizar HTTPS**. | 
    | **Grupo de origem** | Selecione **MyOriginGroup**. | 
    | **Caminho de origem** | Não altere a predefinição. | 
    | **Protocolo de encaminhamento** | Selecione **Pedido de entrada de match**. | 
    | **Colocação em cache** | Deixe desmarcado neste arranque rápido. Se pretender ter o seu conteúdo em cache nas bordas, selecione a caixa de verificação para **ativar o cache**. |
    | **Regras** | Não altere a predefinição. Depois de criar o seu perfil de porta da frente, pode criar regras personalizadas e aplicá-las em rotas. |  
       
    >[!WARNING]
    > **Certifique-se de** que existe uma rota para cada ponto final. A ausência de uma rota pode fazer com que um ponto final falhe.

1. Em seguida, **selecione + Adicione** a Segurança para adicionar uma política WAF. **Selecione Add** New e dê à sua política um nome único. Selecione a caixa de verificação para **adicionar proteção bot**. Selecione o ponto final em **Domínios** e, em seguida, **selecione Adicionar**.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="adicionar política WAF":::

1. Selecione **Rever + Criar** e, em seguida, **Criar**. Leva alguns minutos para que as configurações sejam propagadas a todos os POPs de borda. Agora tem o seu primeiro perfil da Porta da Frente e ponto final.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Rever a criação personalizada":::

## <a name="verify-azure-front-door"></a>Verifique a porta da frente Azure

Quando cria o perfil Azure Front Door Standard/Premium, leva alguns minutos para a configuração ser implantada globalmente. Uma vez concluído, pode aceder ao anfitrião frontal que criou. Em um navegador, vá a `contoso-frontend.z01.azurefd.net` . O seu pedido será automaticamente encaminhado para o servidor mais próximo a partir dos servidores especificados no grupo de origem.

Se criou estas aplicações neste arranque rápido, verá uma página de informação.

Para testar o fracasso global instantâneo, usaremos os seguintes passos:

1. Abra um browser, como descrito acima, e vá para o endereço frontal: `contoso-frontend.azurefd.net` .

1. No portal Azure, procure e selecione *os serviços de Aplicação.* Percorra para baixo para encontrar uma das suas aplicações web, **WebAppContoso-001** neste exemplo.

1. Selecione a sua aplicação web e, em seguida, **selecione Stop** e **Yes** para verificar.

1. Atualize o seu browser. Devia ver a mesma página de informação.

   >[!TIP]
   >Há um pouco de atraso para estas ações. Talvez precises de voltar a refrescar-te.

1. Encontre a outra aplicação web, e pare-a também.

1. Atualize o seu browser. Desta vez, deve ver uma mensagem de erro.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Ambas as instâncias da aplicação web pararam":::

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar, pode remover todos os itens que criou. A eliminação de um grupo de recursos também elimina o seu conteúdo. Se não pretender usar esta Porta frontal, deve remover recursos para evitar cargas desnecessárias.

1. No portal Azure, procure e selecione **grupos de Recursos,** ou selecione **grupos** de Recursos a partir do menu do portal Azure.

1. Filtre ou desloque-se para baixo para encontrar um grupo de recursos, como **FrontDoorQS_rg0**.

1. Selecione o grupo de recursos e, em seguida, **selecione Eliminar grupo de recursos**.

   >[!WARNING]
   >Esta ação é irreversível.

1. Digite o nome do grupo de recursos para verificar e, em seguida, **selecione Delete**.

Repita o procedimento para os outros dois grupos.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a adicionar um domínio personalizado à sua Porta da Frente.
> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](how-to-add-custom-domain.md)

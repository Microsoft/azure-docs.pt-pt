---
title: Gerir o tráfego para aplicações multi-inquilino, como aplicações de web do serviço de aplicações com o Gateway de aplicação do Azure - Portal
description: Este artigo fornece orientações sobre como configurar aplicações de web do serviço de aplicações do Azure como membros no agrupamento de back-end num gateway de aplicação nova ou existente.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176213"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurar o serviço de aplicações com o Gateway de aplicação

Gateway de aplicação permite que tenha uma webapp do serviço de aplicações do Azure ou outros serviços de multi-inquilinos como um membro do conjunto de back-end. 

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> - Criar um conjunto de back-end e adicionar-lhe um serviço de aplicações
> - Criar definições de HTTP e a sonda personalizada com comutadores de "Escolha o nome de anfitrião" ativadas

## <a name="prerequisites"></a>Pré-requisitos

- Gateway de aplicação: Se não tiver um gateway de aplicação existente, consulte como [criar um gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Serviço de aplicações: Se não tiver um serviço de aplicações existente, consulte [documentação do serviço de aplicações](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Adicionar serviço de aplicações como o conjunto back-end

1. No portal do Azure, abra a vista de configuração de o gateway de aplicação.

2. Sob **conjuntos de back-end**, clique em **Add** para criar um novo conjunto de back-end.

3. Forneça um nome adequado para o conjunto de back-end. 

4. Sob **destinos**, clique no menu pendente e escolha **dos serviços de aplicações** como a opção.

5. Uma lista suspensa imediatamente abaixo da **destinos** suspensa aparecerá que irá conter uma lista dos seus serviços de aplicação. Esta lista pendente, escolha o serviço de aplicações que pretende adicionar como membro do conjunto de back-end e clique em Adicionar.

   ![Back-end de serviço de aplicações](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Criar definições de HTTP para o serviço de aplicações

1. Sob **definições de HTTP**, clique em **Add** para criar uma nova definição de HTTP.

2. Introduza um nome para a definição de HTTP e pode ativar ou desativar a afinidade de com base em cookies de acordo com seus requisitos.

3. Selecione o protocolo, como HTTP ou HTTPS, de acordo com o seu caso de utilização. 

4. Marque a caixa **utilização para o serviço de aplicações** e ele irá ativar o **criar uma sonda com escolha de nome de anfitrião do endereço de back-end** e **escolher o nome de anfitrião do endereço de back-end** opções. Esta opção também irá criar uma sonda automaticamente com a opção ativada e associá-la para esta definição de HTTP.

5. Clique em **OK** para criar a definição de HTTP.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Criar regra de vincular o serviço de escuta, o conjunto de back-end e a definição de HTTP

1. Sob **regras**, clique em **básica** para criar uma nova regra básica.

2. Forneça um nome adequado e selecione o serviço de escuta que irá ser abertos ao recebimento de pedidos recebidos para o serviço de aplicações.

3. Na **conjunto back-end** lista pendente, escolha o conjunto de back-end que criou acima.

4. Na **definição de HTTP** lista pendente, escolha o definição que criou acima de HTTP.

5. Clique em **OK** para guardar esta regra.

   ![Regra](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Restringir o acesso

Para as aplicações web implementadas nesses exemplos utilizam endereços IP públicos, que podem ser acedidos diretamente a partir da Internet. Isto ajuda a resolver problemas quando estiver a aprender uma nova funcionalidade e tentar novas coisas. Mas se pretende implantar um recurso em produção, convém adicionar mais restrições.

Pode restringir o acesso às suas aplicações web de uma maneira é usar [restrições de IP estáticas do serviço de aplicações do Azure](../app-service/app-service-ip-restrictions.md). Por exemplo, pode restringir a aplicação web para que ele só recebe tráfego do gateway de aplicação. Utilize a funcionalidade de restrição de IP de serviço de aplicações para listar o VIP do gateway de aplicação como o único endereço com acesso.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o serviço de aplicações e outro suporte multi-inquilino com o gateway de aplicação, veja [suporte do serviço de multi-inquilino com o gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

No caso da resposta do seu serviço de aplicações está a redirecionar para o URL do serviço de aplicações, consulte como [resolver problemas de redirecionamento para o problema de URL do serviço de aplicações](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

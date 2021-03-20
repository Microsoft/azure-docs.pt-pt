---
title: Gerir o tráfego para apps multi-inquilinos usando o portal
titleSuffix: Azure Application Gateway
description: Este artigo fornece orientações sobre como configurar aplicações web de serviço de aplicações Azure App como membros em backend pool em um gateway de aplicações existente ou novo.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97854915"
---
# <a name="configure-app-service-with-application-gateway"></a>Configure o Serviço de Aplicações com Gateway de Aplicações

Como o serviço de aplicações é um serviço multi-inquilino em vez de uma implementação dedicada, utiliza o cabeçalho de anfitrião no pedido recebido para resolver o pedido para o ponto final de serviço da aplicação correto. Normalmente, o nome DNS da aplicação, que por sua vez é o nome DNS associado ao gateway de aplicação que faz frente ao serviço da aplicação, é diferente do nome de domínio do serviço de aplicações backend. Portanto, o cabeçalho do anfitrião no pedido original recebido pelo gateway de aplicação não é o mesmo que o nome de anfitrião do serviço de backend. Por isso, a menos que o cabeçalho do anfitrião no pedido da porta de entrada de aplicação para o backend seja alterado para o nome de anfitrião do serviço de backend, os backends multi-inquilinos não são capazes de resolver o pedido para o ponto final correto.

O Application Gateway fornece um interruptor chamado `Pick host name from backend target` que substitui o cabeçalho do anfitrião no pedido com o nome de anfitrião do back-end quando o pedido é encaminhado do Gateway de Aplicação para o backend. Esta capacidade permite suporte para extremidades traseiras multi-arrendatários, como o serviço de aplicações Azure e a gestão da API. 

Neste artigo, vai aprender a:

- Edite uma piscina de backend e adicione-lhe um Serviço de Aplicações
- Editar DEFINIções HTTP com o interruptor 'Escolha o nome anfitrião' ativado

## <a name="prerequisites"></a>Pré-requisitos

- Gateway de aplicação: Criar um gateway de aplicação sem um alvo de piscina de backend. Para mais informações, consulte [Quickstart: Tráfego web direto com Gateway de Aplicação Azure - Portal Azure](quick-create-portal.md)

- Serviço de aplicações: Se não tiver um serviço de Aplicações existente, consulte [a documentação do serviço app.](../app-service/index.yml)

## <a name="add-app-service-as-backend-pool"></a>Adicione o serviço app como backend pool

1. No portal Azure, selecione o seu gateway de aplicação.

2. Em **piscinas backend,** selecione a piscina de backend.

4. Sob **o tipo Target**, selecione **Serviços de Aplicação.**

5. No **Target** selecione o seu Serviço de Aplicações.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="Backend do serviço de aplicações":::
   
   > [!NOTE]
   > O dropdown apenas povoa os serviços de aplicações que estão na mesma subscrição que o seu Gateway de aplicações. Se pretender utilizar um serviço de aplicações que esteja numa subscrição diferente daquela em que está o Gateway de Aplicação, em vez de escolher **os Serviços** de Aplicações no **dropdown dos Targets,** escolha o endereço IP ou opção **de nome de anfitrião** e insira o nome de anfitrião (exemplo. azurewebsites.net) do serviço de aplicações.
1. Selecione **Guardar**.

## <a name="edit-http-settings-for-app-service"></a>Editar definições HTTP para Serviço de Aplicações

1. Em **DEFINIÇÕES HTTP**, selecione a definição HTTP existente.

2. Under **Override com novo nome de anfitrião,** selecione **Sim**.
3. No **nome do anfitrião,** selecione **O nome do anfitrião do alvo backend**.
4. Selecione **Guardar**.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="Escolha o nome do anfitrião a partir das definições de backend http":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Configuração adicional em caso de reorientação para o caminho relativo do serviço de aplicações

Quando o serviço de aplicações envia uma resposta de redirecionamento para o cliente para redirecionar para o seu caminho relativo (por exemplo, um redirecionamento de `contoso.azurewebsites.net/path1` `contoso.azurewebsites.net/path2` para), utiliza o mesmo nome de anfitrião no cabeçalho de localização da sua resposta como o pedido que recebeu do gateway de aplicação. Assim, o cliente fará o pedido diretamente para `contoso.azurewebsites.net/path2` em vez de passar pelo gateway de aplicação ( `contoso.com/path2` ). Contornar o portal de aplicações não é desejável.

Se no seu caso de utilização, existem cenários em que o serviço app terá de enviar uma resposta de redirecionamento ao cliente, efetue os [passos adicionais para reescrever o cabeçalho de localização](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration).

## <a name="restrict-access"></a>Restringir o acesso

As aplicações web implementadas nestes exemplos utilizam endereços IP públicos que podem ser acedidos diretamente a partir da Internet. Isto ajuda na resolução de problemas quando está a aprender sobre uma nova funcionalidade e a experimentar coisas novas. Mas se pretende colocar uma funcionalidade na produção, vai querer adicionar mais restrições.

Uma forma de restringir o acesso às suas aplicações web é utilizar [as restrições estáticas ip do Azure App Service.](../app-service/app-service-ip-restrictions.md) Por exemplo, pode restringir a aplicação web para que apenas receba tráfego a partir do gateway da aplicação. Utilize a funcionalidade de restrição IP do serviço de aplicações para listar o gateway de aplicações VIP como o único endereço com acesso.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o serviço App e outros suportes multi-inquilinos com gateway de aplicações, consulte [o suporte de serviço de vários inquilinos com porta de aplicação.](./application-gateway-web-app-overview.md)

---
title: Gerir o tráfego para aplicações multi-inquilinos usando o portal
titleSuffix: Azure Application Gateway
description: Este artigo fornece orientações sobre como configurar as aplicações web do serviço Azure App como membros em backend pool em um gateway de aplicação existente ou novo.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075167"
---
# <a name="configure-app-service-with-application-gateway"></a>Configure serviço de aplicações com gateway de aplicação

Uma vez que o serviço de aplicações é um serviço multi-inquilino em vez de uma implementação dedicada, utiliza o cabeçalho do anfitrião no pedido de entrada para resolver o pedido para o ponto final do serviço de aplicações correto. Normalmente, o nome DNS da aplicação, que por sua vez é o nome DNS associado ao gateway da aplicação que está em frente ao serviço de aplicações, é diferente do nome de domínio do serviço de aplicações backend. Por isso, o cabeçalho do anfitrião no pedido original recebido pelo gateway de aplicação não é o mesmo que o nome de anfitrião do serviço backend. Por isso, a menos que o cabeçalho do anfitrião no pedido da porta de entrada de candidatura para o backend seja alterado para o nome de anfitrião do serviço backend, os backends multi-inquilinos não são capazes de resolver o pedido para o ponto final correto.

O Application Gateway fornece `Pick host name from backend address` um interruptor chamado que substitui o cabeçalho do anfitrião no pedido com o nome de anfitrião do back-end quando o pedido é encaminhado do Gateway de Aplicação para o backend. Esta capacidade permite o suporte para extremidades multi-arrendatárias, como o serviço de aplicações Azure e a gestão da API. 

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> - Crie uma piscina de backend e adicione-lhe um Serviço de Aplicações
> - Criar definições http e sonda personalizada com interruptores "Pick Hostname" ativados

## <a name="prerequisites"></a>Pré-requisitos

- Gateway de aplicação: Se não tiver um gateway de aplicação existente, consulte como criar um gateway de [aplicação](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Serviço de aplicações: Se não tiver um serviço de Aplicações existente, consulte a [documentação do serviço app](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Adicione o serviço de aplicativos como piscina de backend

1. No portal Azure, abra a vista de configuração do seu gateway de aplicação.

2. Em **piscinas Backend,** clique em **Adicionar** para criar uma nova piscina de backend.

3. Forneça um nome adequado para a piscina de backend. 

4. Em **'Destinos',** clique no dropdown e escolha os **Serviços de Aplicação** como opção.

5. Aparecerá uma queda imediatamente abaixo do dropdown dos **Targets,** que conterá uma lista dos seus Serviços de Aplicação. A partir desta queda, escolha o Serviço de Aplicações que pretende adicionar como membro da piscina de backend e clique em Adicionar.

   ![Backend do serviço de aplicações](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > O dropdown só irá preencher os serviços de aplicações que estão na mesma subscrição que o gateway da aplicação. Se pretender utilizar um serviço de aplicações que se encontra numa subscrição diferente daquela em que se encontra o Gateway da Aplicação, em vez de escolher serviços de **aplicação** no dropdown dos **Targets,** escolha o endereço IP ou a opção de nome de **anfitrião** e introduza o nome de anfitrião (exemplo. azurewebsites.net) do serviço de aplicações.

## <a name="create-http-settings-for-app-service"></a>Criar definições http para o serviço de aplicações

1. Em **definições http,** clique em **Adicionar** para criar uma nova Definição HTTP.

2. Insera um nome para a Definição HTTP e pode ativar ou desativar a Affinity baseada em Cookies de acordo com o seu requisito.

3. Escolha o protocolo como HTTP ou HTTPS de acordo com o seu caso de utilização. 

   > [!NOTE]
   > Se selecionar HTTPS, não precisa de carregar qualquer certificado de autenticação ou certificado de raiz fidedigno para whitelist o backend do serviço de aplicações, uma vez que o serviço de aplicações é um serviço Azure de confiança.

4. Verifique a caixa para **utilização do serviço** de aplicações . Note que os `Create a probe with pick host name from backend address` `Pick host name from backend address` interruptores e serão ativados automaticamente.`Pick host name from backend address` substituirá o cabeçalho do anfitrião no pedido com o nome anfitrião do back-end quando o pedido for encaminhado do Gateway de Aplicação para o backend.  

   `Create a probe with pick host name from backend address`criará automaticamente uma sonda de saúde e associá-la-á a esta Definição HTTP. Não precisa de criar outra sonda de saúde para esta definição HTTP. Pode verificar se foi adicionada <HTTP Setting name> <Unique GUID> uma nova sonda com o nome na lista `Pick host name from backend http settings enabled`de sondas de saúde e já tem o interruptor .

   Se já tiver uma ou mais Definições HTTP que estão a ser utilizadas para o serviço de Aplicações e `Create a probe with pick host name from backend address` se essas definições http usarem o mesmo protocolo que a que está a usar no que está a criar, em vez do interruptor, terá uma queda para selecionar uma das sondas personalizadas . Isto porque, uma vez que já existe um HTTP Setting com serviço de aplicações, por isso, existiria também uma sonda de saúde que tem o interruptor `Pick host name from backend http settings enabled` . Escolha a sonda personalizada do dropdown.

5. Clique **em OK** para criar a definição HTTP.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Crie regra para ligar o Ouvinte, Backend Pool e HTTP Setting

1. De acordo com **regras,** clique em **Básico** para criar uma nova regra básica.

2. Forneça um nome adequado e selecione o ouvinte que aceitará os pedidos de entrada para o serviço app.

3. Na **piscina Backend** dropdown, escolha a piscina de backend que criou acima.

4. Na **definição de DROPDOWN HTTP,** escolha a definição HTTP que criou acima.

5. Clique **em OK** para salvar esta regra.

   ![Regra](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Configuração adicional em caso de reorientação para o caminho relativo do serviço de aplicações

Quando o serviço de aplicações envia uma resposta de reorientação ao cliente para redirecionar para o seu caminho relativo (por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2), utiliza o mesmo nome de anfitrião no cabeçalho de localização da sua resposta como o pedido que recebeu do gateway da aplicação. Assim, o cliente fará o pedido diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway da aplicação (contoso.com/path2). Contornar a porta de aplicação não é desejável.

Se no seu caso de utilização, existem cenários em que o serviço app terá de enviar uma resposta de reorientação ao cliente, executar os [passos adicionais para reescrever o cabeçalho de localização](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Restringir o acesso

As aplicações web implementadas nestes exemplos utilizam endereços IP públicos que podem ser acedidos diretamente a partir da Internet. Isto ajuda na resolução de problemas quando se está a aprender sobre uma nova funcionalidade e a experimentar coisas novas. Mas se pretende implementar uma funcionalidade em produção, vai querer adicionar mais restrições.

Uma das formas de restringir o acesso às suas aplicações web é utilizar [restrições ip estáticas do Azure App Service.](../app-service/app-service-ip-restrictions.md) Por exemplo, pode restringir a aplicação web para que receba apenas tráfego a partir do gateway da aplicação. Utilize a funcionalidade de restrição IP do serviço de aplicação para listar o gateway DE aplicação VIP como o único endereço com acesso.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o serviço app e outro suporte multi-inquilino com gateway de aplicação, consulte o suporte de [serviço multi-inquilino com gateway de aplicações](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

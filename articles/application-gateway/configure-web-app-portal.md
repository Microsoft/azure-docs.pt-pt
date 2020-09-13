---
title: Gerir o tráfego para apps multi-inquilinos usando o portal
titleSuffix: Azure Application Gateway
description: Este artigo fornece orientações sobre como configurar aplicações web de serviço de aplicações Azure App como membros em backend pool em um gateway de aplicações existente ou novo.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: absha
ms.openlocfilehash: daf4c4ab56e23a212256c2629b7d0ae52a09e7c4
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595715"
---
# <a name="configure-app-service-with-application-gateway"></a>Configure o Serviço de Aplicações com Gateway de Aplicações

Uma vez que o serviço de aplicações é um serviço multi-inquilino em vez de uma implementação dedicada, utiliza o cabeçalho do anfitrião no pedido de entrada para resolver o pedido para o ponto final correto do serviço de aplicações. Normalmente, o nome DNS da aplicação, que por sua vez é o nome DNS associado ao gateway de aplicação que faz frente ao serviço da aplicação, é diferente do nome de domínio do serviço de aplicações backend. Portanto, o cabeçalho do anfitrião no pedido original recebido pelo gateway de aplicação não é o mesmo que o nome de anfitrião do serviço de backend. Por isso, a menos que o cabeçalho do anfitrião no pedido da porta de entrada de aplicação para o backend seja alterado para o nome de anfitrião do serviço de backend, os backends multi-inquilinos não são capazes de resolver o pedido para o ponto final correto.

O Application Gateway fornece um interruptor chamado `Pick host name from backend address` que substitui o cabeçalho do anfitrião no pedido com o nome de anfitrião do back-end quando o pedido é encaminhado do Gateway de Aplicação para o backend. Esta capacidade permite suporte para extremidades traseiras multi-arrendatários, como o serviço de aplicações Azure e a gestão da API. 

Neste artigo, vai aprender a:

- Crie uma piscina de backend e adicione-lhe um Serviço de Aplicações
- Criar definições HTTP e sonda personalizada com comutadores "Escolha o nome anfitrião" ativados

## <a name="prerequisites"></a>Pré-requisitos

- Gateway de aplicações: Se não tiver um gateway de aplicações existente, veja como criar um gateway de [aplicações](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Serviço de aplicações: Se não tiver um serviço de Aplicações existente, consulte [a documentação do serviço app.](https://docs.microsoft.com/azure/app-service/)

## <a name="add-app-service-as-backend-pool"></a>Adicione o serviço app como backend pool

1. No portal Azure, abra a vista de configuração do seu gateway de aplicações.

2. Em **piscinas backend**, clique em **Adicionar** para criar uma nova piscina de backend.

3. Forneça um nome adequado à piscina de backend. 

4. Em **'Alvos'**, clique no dropdown e escolha **os Serviços de Aplicação** como opção.

5. Aparecerá uma retirada imediatamente abaixo do dropdown targets que conterá uma lista dos seus **Serviços**  de Aplicações. A partir deste dropdown, escolha o Serviço de Aplicações que pretende adicionar como membro de pool backend e clique em Adicionar.

   ![Backend do serviço de aplicações](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > O dropdown só irá povoar os serviços de aplicações que estão na mesma subscrição que o seu Gateway de aplicações. Se pretender utilizar um serviço de aplicações que esteja numa subscrição diferente daquela em que está o Gateway de Aplicação, em vez de escolher **os Serviços** de Aplicações no **dropdown dos Targets,** escolha o endereço IP ou opção **de nome de anfitrião** e insira o nome de anfitrião (exemplo. azurewebsites.net) do serviço de aplicações.

## <a name="create-http-settings-for-app-service"></a>Criar definições HTTP para o serviço de aplicações

1. Em **Definições HTTP**, clique em **Adicionar** para criar uma nova definição HTTP.

2. Introduza um nome para a Definição HTTP e pode ativar ou desativar a Affinity Baseada em Cookies de acordo com o seu requisito.

3. Escolha o protocolo como HTTP ou HTTPS de acordo com o seu caso de utilização. 

   > [!NOTE]
   > Se selecionar HTTPS, não precisa de carregar nenhum certificado de autenticação ou certificado de raiz fidedigno para permitir o backend do serviço de aplicações, uma vez que o serviço de aplicações é um serviço Azure de confiança.

4. Verifique a caixa de **utilização para o Serviço de Aplicações.** Note que os interruptores  `Create a probe with pick host name from backend address` e `Pick host name from backend address` serão automaticamente ativados.`Pick host name from backend address` irá anular o cabeçalho do anfitrião no pedido com o nome de anfitrião do back-end quando o pedido for encaminhado do Gateway de Aplicação para o backend.  

   `Create a probe with pick host name from backend address` irá automaticamente criar uma sonda de saúde e associá-la a esta Definição HTTP. Não é necessário criar nenhuma outra sonda de saúde para esta definição HTTP. Pode verificar se uma nova sonda com o nome <HTTP Setting name> <Unique GUID> foi adicionada na lista de sondas health e já tem o interruptor `Pick host name from backend http settings enabled` .

   Se já tiver uma ou mais definições HTTP que estão a ser utilizadas para o serviço de Aplicações e se essas definições HTTP utilizarem o mesmo protocolo que está a utilizar no que está a criar, então em vez do `Create a probe with pick host name from backend address` comutador, receberá um dropdown para selecionar uma das sondas personalizadas. Isto porque já existe uma definição HTTP com o serviço de aplicações, portanto, também existiria uma sonda de saúde que tem o `Pick host name from backend http settings enabled` interruptor. Escolha a sonda personalizada do dropdown.

5. Clique **em OK** para criar a definição HTTP.

   ![Definição HTTP1](./media/configure-web-app-portal/http-setting1.png)

   ![DEFINIÇÃO HTTP2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Criar Regra para ligar o ouvinte, o backend pool e a definição HTTP

1. De **acordo com as Regras,** clique em **Básico** para criar uma nova regra Básica.

2. Forneça um nome adequado e selecione o ouvinte que irá aceitar os pedidos de entrada para o serviço app.

3. No **dropdown da piscina backend,** escolha a piscina de backend que criou acima.

4. Na **definição HTTP,** escolha a definição HTTP que criou acima.

5. Clique **em OK** para guardar esta regra.

   ![Regra](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Configuração adicional em caso de reorientação para o caminho relativo do serviço de aplicações

Quando o serviço de aplicações envia uma resposta de redirecionamento para o cliente para redirecionar para o seu caminho relativo (por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2), utiliza o mesmo nome de anfitrião no cabeçalho de localização da sua resposta que o pedido que recebeu do gateway de aplicação. Assim, o cliente fará o pedido diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicação (contoso.com/path2). Contornar o portal de aplicações não é desejável.

Se no seu caso de utilização, existem cenários em que o serviço app terá de enviar uma resposta de redirecionamento ao cliente, efetue os [passos adicionais para reescrever o cabeçalho de localização](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Restringir o acesso

As aplicações web implementadas nestes exemplos utilizam endereços IP públicos que podem ser acedidos diretamente a partir da Internet. Isto ajuda na resolução de problemas quando está a aprender sobre uma nova funcionalidade e a experimentar coisas novas. Mas se pretende colocar uma funcionalidade na produção, vai querer adicionar mais restrições.

Uma forma de restringir o acesso às suas aplicações web é utilizar [as restrições estáticas ip do Azure App Service.](../app-service/app-service-ip-restrictions.md) Por exemplo, pode restringir a aplicação web para que apenas receba tráfego a partir do gateway da aplicação. Utilize a funcionalidade de restrição IP do serviço de aplicações para listar o gateway de aplicações VIP como o único endereço com acesso.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o serviço App e outros suportes multi-inquilinos com gateway de aplicações, consulte [o suporte de serviço de vários inquilinos com porta de aplicação.](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)

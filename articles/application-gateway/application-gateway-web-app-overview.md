---
title: Várias extremidades de volta inquilino
titleSuffix: Azure Application Gateway
description: Esta página fornece uma descrição geral do suporte de Gateway de Aplicação para back-ends multi-inquilino.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: fe6ea6f348d796962141bd39ff858d891a29a2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397693"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Suporte de Gateway de aplicações para extremidades traseiras multi-arrendatários, como o serviço app

Em projetos arquitetônicos multi-inquilinos em servidores web, vários sites estão executando na mesma instância do servidor web. Os nomes de anfitrião são usados para diferenciar entre as diferentes aplicações que são hospedadas. Por predefinição, o gateway de aplicação não altera o cabeçalho de anfitrião de HTTP recebido do cliente e envia o cabeçalho inalterado para o back-end. Isto funciona bem para membros do pool backend, tais como NICs, conjuntos de escala de máquinas virtuais, endereços IP públicos, endereços IP internos e FQDN, uma vez que estes não dependem de um cabeçalho de hospedeiro específico ou extensão SNI para resolver o ponto final correto. No entanto, existem muitos serviços como aplicações web de serviço azure app e gestão AZURE API que são multi-inquilinos na natureza e dependem de um cabeçalho de anfitrião específico ou extensão SNI para resolver o ponto final correto. Normalmente, o nome DNS da aplicação, que por sua vez é o nome DNS associado ao gateway de aplicação, é diferente do nome de domínio do serviço de backend. Portanto, o cabeçalho do anfitrião no pedido original recebido pelo gateway de aplicação não é o mesmo que o nome de anfitrião do serviço de backend. Por isso, a menos que o cabeçalho do anfitrião no pedido da porta de entrada de aplicação para o backend seja alterado para o nome de anfitrião do serviço de backend, os backends multi-inquilinos não são capazes de resolver o pedido para o ponto final correto. 

O Gateway de Aplicação fornece uma capacidade que permite aos utilizadores substituir o cabeçalho de anfitrião HTTP no pedido com base no nome do anfitrião do back-end. Esta capacidade permite o suporte para back-ends multi-inquilinos como a gestão de APIs e aplicações Web do Serviço aplicações do Azure. Esta capacidade está disponível tanto para os SKU WAF como para o SKU standard v1 e v2. 

![substituição hospedeiro](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Isto não é aplicável ao ambiente de serviço da App Azure (ASE), uma vez que a ASE é um recurso dedicado ao contrário do serviço Azure App, que é um recurso multi-inquilino.

## <a name="override-host-header-in-the-request"></a>Anular o cabeçalho do anfitrião no pedido

A capacidade de especificar uma sobreposição de hospedeiro é definida nas [definições HTTP](./configuration-overview.md#http-settings) e pode ser aplicada a qualquer piscina de back-end durante a criação de regras. São suportadas as duas formas de sub-cabeçalho do hospedeiro e da extensão SNI para as extremidades traseiras de vários inquilinos:

- A capacidade de definir o nome de anfitrião para um valor fixo explicitamente introduzido nas definições HTTP. Esta capacidade garante que o cabeçalho do anfitrião é ultrapassado a este valor para todo o tráfego para a piscina traseira onde são aplicadas as definições HTTP específicas. Ao utilizar o fim para o fim do TLS, este nome de anfitrião ultrapassado é utilizado na extensão SNI. Esta capacidade permite cenários onde uma quinta de piscinas de back-end espera um cabeçalho de anfitrião diferente do cabeçalho do anfitrião do cliente.

- A capacidade de obter o nome do anfitrião a partir do IP ou FQDN dos membros da piscina back-end. As definições HTTP também fornecem uma opção para escolher dinamicamente o nome de anfitrião do FQDN de um membro de piscina de back-end se configurado com a opção de obter o nome de anfitrião de um membro de piscina de back-end individual. Ao utilizar o TLS de ponta para o fim, este nome de hospedeiro é derivado do FQDN e é utilizado na extensão SNI. Esta capacidade permite cenários onde um pool back-end pode ter dois ou mais serviços PaaS multi-inquilinos como aplicações web Azure e o cabeçalho do anfitrião do pedido para cada membro contém o nome de anfitrião derivado do seu FQDN. Para implementar este cenário, utilizamos um interruptor nas Definições HTTP chamada [Nome de anfitrião Pick do endereço backend](./configuration-http-settings.md#pick-host-name-from-back-end-address) que irá sobrepor-se dinamicamente ao cabeçalho do anfitrião no pedido original ao mencionado no pool de backend.  Por exemplo, se o seu pool de backend FQDN contiver "contoso11.azurewebsites.net" e "contoso22.azurewebsites.net", o cabeçalho de anfitrião do pedido original, que é contoso.com será ultrapassado para contoso11.azurewebsites.net ou contoso22.azurewebsites.net quando o pedido for enviado para o servidor de backend apropriado. 

  ![cenário de aplicação Web](./media/application-gateway-web-app-overview/scenario.png)

Com esta capacidade, os clientes especificam as opções nas definições de HTTP e as pesquisas personalizadas com a configuração adequada. Esta definição é então ligada a um ouvinte e a uma piscina traseira utilizando uma regra.

## <a name="special-considerations"></a>Considerações especiais

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>Rescisão de TLS e fim do fim do TLS com serviços multi-inquilinos

Tanto a rescisão de TLS como a encriptação final do TLS são suportadas com serviços multi-inquilinos. Para a rescisão de TLS no gateway de aplicação, o certificado TLS continua a ser obrigado a ser adicionado ao ouvinte do gateway de aplicação. No entanto, em caso de fim de fim de TLS, serviços fidedignos da Azure App, como aplicações web de serviço da App Azure, não requerem permitir o backends no gateway da aplicação. Portanto, não há necessidade de adicionar quaisquer certificados de autenticação. 

![fim para terminar TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Note que na imagem acima, não há necessidade de adicionar certificados de autenticação quando o serviço de Aplicação é selecionado como backend.

### <a name="health-probe"></a>Sonda de estado de funcionamento

A sobreposição do cabeçalho do anfitrião nas **definições HTTP** apenas afeta o pedido e o seu encaminhamento. não afeta o comportamento da sonda de saúde. Para a funcionalidade ponto a ponto funcionar, tanto a pesquisa como as definições de HTTP têm de ser modificadas de modo a refletirem a configuração correta. Além de fornecer a capacidade de especificar um cabeçalho de anfitrião na configuração da sonda, as sondas personalizadas também suportam a capacidade de derivar o cabeçalho do anfitrião a partir das definições HTTP atualmente configuradas. Esta configuração pode ser especificada com o parâmetro `PickHostNameFromBackendHttpSettings` na configuração da pesquisa.

### <a name="redirection-to-app-services-url-scenario"></a>Redirecionamento para o cenário de URL do Serviço de Aplicações

Pode haver cenários em que o nome anfitrião na resposta do serviço app pode direcionar o navegador de utilizador final para o nome de anfitrião *.azurewebsites.net em vez do domínio associado ao Gateway de Aplicação. Esta questão pode acontecer quando:

- Tem a reorientação configurada no seu Serviço de Aplicações. A reorientação pode ser tão simples como adicionar um corte de fuga ao pedido.
- Tem a autenticação AD AZure que causa a reorientação.

Para resolver estes casos, consulte [a reorientação da Resolução de Problemas para a emissão de URL do serviço app](./troubleshoot-app-service-redirection-app-service-url.md).

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar um gateway de aplicações com uma app multi-inquilino, como a app de serviço da App Azure App, como membro de back-end pool, visitando [aplicações web do Configure App Service com App Gateway](./configure-web-app-portal.md)

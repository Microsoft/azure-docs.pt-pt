---
title: Termina o multi-inquilino
titleSuffix: Azure Application Gateway
description: Esta página fornece uma descrição geral do suporte de Gateway de Aplicação para back-ends multi-inquilino.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: a171dc795e685655b5a3c73d088d3963c2aaa4ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312309"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Suporte de Gateway de aplicação para extremidades de vários inquilinos, como serviço de App

Em projetos arquitetônicos multi-inquilinos em servidores web, vários sites estão funcionando na mesma instância de servidor web. Os nomes de anfitriões são usados para diferenciar entre as diferentes aplicações que são hospedadas. Por predefinição, o gateway de aplicação não altera o cabeçalho de anfitrião de HTTP recebido do cliente e envia o cabeçalho inalterado para o back-end. Isto funciona bem para membros do pool backend, tais como NICs, conjuntos de escala de máquinas virtuais, endereços IP públicos, endereços IP internos e FQDN, uma vez que estes não dependem de um cabeçalho específico do anfitrião ou extensão SNI para resolver o ponto final correto. No entanto, existem muitos serviços, como aplicações web de serviço Azure App e gestão de API Azure que são multi-inquilinos de natureza e dependem de um cabeçalho de anfitrião específico ou extensão SNI para resolver o ponto final correto. Normalmente, o nome DNS da aplicação, que por sua vez é o nome DNS associado ao gateway da aplicação, é diferente do nome de domínio do serviço backend. Por isso, o cabeçalho do anfitrião no pedido original recebido pelo gateway de aplicação não é o mesmo que o nome de anfitrião do serviço backend. Por isso, a menos que o cabeçalho do anfitrião no pedido da porta de entrada de candidatura para o backend seja alterado para o nome de anfitrião do serviço backend, os backends multi-inquilinos não são capazes de resolver o pedido para o ponto final correto. 

O Gateway de Aplicação fornece uma capacidade que permite aos utilizadores substituir o cabeçalho de anfitrião HTTP no pedido com base no nome do anfitrião do back-end. Esta capacidade permite o suporte para back-ends multi-inquilinos como a gestão de APIs e aplicações Web do Serviço aplicações do Azure. Esta capacidade está disponível tanto para os SKU WAF como para o SKU standard v1 e v2. 

![sobreposição anfitrião](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Isto não é aplicável ao ambiente de serviço da App Azure (ASE), uma vez que a ASE é um recurso dedicado ao contrário do serviço Azure App, que é um recurso multi-inquilino.

## <a name="override-host-header-in-the-request"></a>Anular cabeçalho anfitrião no pedido

A capacidade de especificar uma sobreposição do hospedeiro é definida nas [definições http](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) e pode ser aplicada a qualquer piscina de back-end durante a criação de regras. As duas seguintes formas de dominar o cabeçalho do anfitrião e a extensão SNI para extremidades traseiras multi-arrendatárias são suportadas:

- A capacidade de definir o nome do anfitrião para um valor fixo explicitamente introduzido nas definições http. Esta capacidade garante que o cabeçalho do hospedeiro é ultrapassado a este valor para todo o tráfego para a piscina traseira onde são aplicadas as definições http-finais específicas. Ao utilizar tLS de ponta para ponta, este nome de anfitrião ultrapassado é utilizado na extensão SNI. Esta capacidade permite cenários onde uma quinta de piscinas de back-end espera um cabeçalho de anfitrião diferente do cabeçalho do anfitrião do cliente.

- A capacidade de obter o nome de anfitrião do IP ou FQDN dos membros do pool de back-end. As definições http também fornecem uma opção para escolher dinamicamente o nome do anfitrião a partir do FQDN de um membro do pool de back-end se configurado com a opção de derivar o nome do anfitrião de um membro do pool de back-end individual. Ao utilizar tLS de ponta para ponta, este nome de hospedeiro é derivado do FQDN e é utilizado na extensão SNI. Esta capacidade permite cenários onde um pool back-end pode ter dois ou mais serviços paaS multi-inquilinos como aplicações web Azure e o cabeçalho do pedido para cada membro contém o nome de anfitrião derivado do seu FQDN. Para implementar este cenário, utilizamos um interruptor nas Definições HTTP chamado [Pick hostname from backend address](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) que irá sobrepor-se dinamicamente ao cabeçalho do anfitrião no pedido original ao mencionado na piscina de backend.  Por exemplo, se o seu pool de backend FQDN contiver "contoso11.azurewebsites.net" e "contoso22.azurewebsites.net", o cabeçalho do anfitrião do pedido original, que é contoso.com será ultrapassado para contoso11.azurewebsites.net ou contoso22.azurewebsites.net quando o pedido for enviado para o servidor de backend apropriado. 

  ![cenário de aplicação Web](./media/application-gateway-web-app-overview/scenario.png)

Com esta capacidade, os clientes especificam as opções nas definições de HTTP e as pesquisas personalizadas com a configuração adequada. Esta definição é então ligada a um ouvinte e a uma piscina traseira usando uma regra.

## <a name="special-considerations"></a>Considerações especiais

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>TLS rescisão e fim do TLS com serviços multi-inquilinos

Tanto a rescisão do TLS como a encriptação tLS final é suportada com serviços multi-inquilinos. Para a rescisão de TLS no gateway da aplicação, o certificado TLS continua a ser necessário para ser adicionado ao ouvinte do gateway de aplicação. No entanto, em caso de fim para acabar com o TLS, serviços de confiança do Azure, como as aplicações web do serviço Azure App, não requerem a lista de backbacks no gateway da aplicação. Por isso, não há necessidade de adicionar quaisquer certificados de autenticação. 

![fim para fim TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Note que na imagem acima, não existe qualquer obrigação de adicionar certificados de autenticação quando o serviço app é selecionado como backend.

### <a name="health-probe"></a>Sonda de estado de funcionamento

A sobrecondução do cabeçalho do hospedeiro nas **definições http** só afeta o pedido e o seu encaminhamento. não afeta o comportamento da sonda de saúde. Para a funcionalidade ponto a ponto funcionar, tanto a pesquisa como as definições de HTTP têm de ser modificadas de modo a refletirem a configuração correta. Além de fornecer a capacidade de especificar um cabeçalho de anfitrião na configuração da sonda, as sondas personalizadas também suportam a capacidade de obter o cabeçalho do anfitrião a partir das definições http configuradas atualmente. Esta configuração pode ser especificada com o parâmetro `PickHostNameFromBackendHttpSettings` na configuração da pesquisa.

### <a name="redirection-to-app-services-url-scenario"></a>Reorientação para o cenário url do Serviço de Aplicações

Pode haver cenários em que o nome de anfitrião na resposta do serviço App pode direcionar o navegador de utilizador final para o nome de anfitrião *.azurewebsites.net em vez do domínio associado ao Gateway da Aplicação. Esta questão pode acontecer quando:

- Tem uma reorientação configurada no seu Serviço de Aplicações. A reorientação pode ser tão simples como adicionar um corte de rasto ao pedido.
- Tem autenticação Azure AD que causa a reorientação.

Para resolver estes casos, consulte a [redirecionamento](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)de Troubleshoot para o problema de URL do serviço app .

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar um portal de aplicações com uma aplicação multi-inquilino, como a aplicação web de serviço azure App como membro de piscina de back-end, visitando aplicações web do [Configure App Service com Application Gateway](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)

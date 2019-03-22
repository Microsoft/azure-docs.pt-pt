---
title: Descrição geral de back-ends multi-inquilino, como o serviço de aplicações do Azure, com o Gateway de aplicação do Azure
description: Esta página fornece uma descrição geral do suporte de Gateway de Aplicação para back-ends multi-inquilino.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 8434340bb7ed95cc36115c05048b2b67682b5796
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224503"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Suporte de Gateway de aplicação para a cópia de multi-inquilino termina, como o serviço de aplicações

Na multi-inquilinos designs de arquiteturais em servidores web, vários Web sites estão em execução na mesma instância de servidor web. Os nomes de anfitrião são utilizados para diferenciar entre os diferentes aplicativos que estão alojados. Por predefinição, o gateway de aplicação não altera o cabeçalho de anfitrião de HTTP recebido do cliente e envia o cabeçalho inalterado para o back-end. Isso funciona bem para membros do agrupamento de back-end, como NICs, máquina virtual conjuntos de dimensionamento, endereços IP públicos, endereços IP internos e FQDN como elas não dependem de um cabeçalho de anfitrião específico ou a extensão SNI para resolver para o ponto final correto. No entanto, existem vários serviços, tais como aplicações de web do serviço de aplicações do Azure e a gestão de API do Azure que são multi-inquilino por natureza e dependem de um cabeçalho de anfitrião específico ou a extensão SNI para resolver para o ponto final correto. Normalmente, o nome DNS do aplicativo, que por sua vez, é o nome DNS associado com o gateway de aplicação, é diferente do nome de domínio do serviço de back-end. Portanto, o cabeçalho de anfitrião na solicitação original recebido pelo gateway de aplicação não é igual ao nome de anfitrião do serviço de back-end. Por este motivo, a menos que o cabeçalho de anfitrião no pedido do gateway de aplicação para o back-end é alterado para o nome de anfitrião do serviço de back-end, os back-ends multi-inquilino não são possível resolver o pedido para o ponto final correto. 

Gateway de aplicação fornece um recurso que permite aos utilizadores substituir o cabeçalho de anfitrião HTTP no pedido com base no nome do host de back-end. Esta capacidade permite suporte para o back-ends multi-inquilino, como aplicações de web do serviço de aplicações do Azure e a gestão de API. Esta capacidade está disponível para a v1 e v2 padrão e SKUs de WAF. 

![Substituição de anfitrião](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Não se aplica ao ambiente de serviço de aplicações do Azure (ASE), uma vez que o ASE é um recurso dedicado ao contrário do serviço de aplicações do Azure que é um recurso de multi-inquilino.

## <a name="override-host-header-in-the-request"></a>Substituir o cabeçalho de anfitrião no pedido

A capacidade de especificar uma substituição de anfitrião está definida no [definições de HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) e podem ser aplicadas a qualquer conjunto de back-end durante a criação de regra. São suportadas as seguintes duas formas de substituir o cabeçalho de anfitrião e a extensão SNI para back-ends multi-inquilino:

- A capacidade de definir o nome de anfitrião para um valor fixo introduzido explicitamente nas definições de HTTP. Esta capacidade garante que o cabeçalho de anfitrião é substituído para este valor para todo o tráfego para o conjunto de back-end em que são aplicadas as definições de HTTP específicas. Ao utilizar o SSL ponto a ponto, este nome de anfitrião substituído é utilizado na extensão SNI. Esta capacidade possibilita cenários onde um farm de conjunto de back-end espera um cabeçalho de anfitrião diferente do cabeçalho de anfitrião de cliente recebido.

- A capacidade de derivar o nome de anfitrião, o IP ou FQDN dos membros do conjunto de back-end. Definições de HTTP também disponibilizam uma opção para escolher dinamicamente o nome de anfitrião de FQDN de um membro conjunto de back-end, se configurado com a opção de derivar o nome de anfitrião de um membro do conjunto de back-end individual. Ao utilizar o SSL ponto a ponto, este nome de anfitrião é derivado a partir do FQDN e é utilizado na extensão SNI. Esta capacidade possibilita cenários em que um conjunto de back-end pode ter dois ou mais serviços de PaaS de multi-inquilino, como de aplicações web do Azure e o cabeçalho de anfitrião do pedido para cada membro contém o nome de anfitrião derivado a partir do respetivo FQDN. Implementação deste cenário, vamos utilizar um comutador nas definições de HTTP chamado [escolha o nome de anfitrião do endereço de back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-backend-address) dinamicamente que irá substituir o cabeçalho de anfitrião no pedido original para mencionado no conjunto de back-end.  Por exemplo, se o seu conjunto de back-end FQDN contém "contoso11.azurewebsites.net" e "contoso22.azurewebsites.net", cabeçalho de anfitrião do pedido original, que é o contoso.com irá ser substituído para contoso11.azurewebsites.net ou contoso22.azurewebsites.net Quando a solicitação é enviada para o servidor de back-end adequado. 

  ![cenário de aplicação Web](./media/application-gateway-web-app-overview/scenario.png)

Com esta capacidade, os clientes especificam as opções nas definições de HTTP e as pesquisas personalizadas com a configuração adequada. Esta definição, em seguida, está associada a um serviço de escuta e um conjunto de back-end utilizando uma regra.

## <a name="special-considerations"></a>Considerações especiais

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Terminação de SSL e o SSL ponto a ponto com serviços de multi-inquilinos

Terminação de SSL e a encriptação SSL de ponta a ponta é suportada com os serviços de multi-inquilinos. Para a terminação de SSL no gateway de aplicação, o certificado SSL continua a ser necessário adicionar para o serviço de escuta do gateway de aplicação. No entanto, em caso de SSL de ponta a ponta, Azure fidedigna serviços, tais como aplicações de web do serviço de aplicações do Azure não requerem os back-ends no gateway de aplicação da lista de permissões. Por conseguinte, não há necessidade de adicionar quaisquer certificados de autenticação. 

![SSL ponto a ponto](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Tenha em atenção que na imagem acima, não é necessário adicionar certificados de autenticação quando o serviço de aplicações está selecionado como back-end.

### <a name="health-probe"></a>Sonda de estado de funcionamento

Substituir o cabeçalho de anfitrião na **definições de HTTP** afeta apenas a solicitação e seu roteamento. ela não afeta o comportamento de sonda de estado de funcionamento. Para a funcionalidade ponto a ponto funcionar, tanto a pesquisa como as definições de HTTP têm de ser modificadas de modo a refletirem a configuração correta. Além de fornecer a capacidade de especificar um cabeçalho de anfitrião na configuração da pesquisa, as pesquisas personalizadas também suportam a capacidade de derivar o cabeçalho de anfitrião de definições de HTTP configuradas atualmente. Esta configuração pode ser especificada com o parâmetro `PickHostNameFromBackendHttpSettings` na configuração da pesquisa.

### <a name="redirection-to-app-services-url-scenario"></a>Redirecionamento para o cenário de URL do serviço de aplicações

É possível que cenários em que o nome de anfitrião na resposta do serviço de aplicações pode direcionar o browser do utilizador final para o *. azurewebsites.net nome de anfitrião em vez do domínio associado ao Gateway de aplicação. Este problema pode ocorrer quando:

- Tem o redirecionamento configurado no seu serviço de aplicações. Redirecionamento pode ser tão simples quanto adicionar uma barra à solicitação.
- Tem a autenticação do Azure AD, que faz com que o redirecionamento.

Para resolver tais casos, consulte [resolver problemas de redirecionamento para o problema de URL do serviço de aplicações](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Passos Seguintes

Saiba como configurar um gateway de aplicação com uma aplicação multi-inquilino, como a aplicação de web do serviço de aplicações do Azure como um membro do conjunto de back-end, visite a página [aplicações de web de configurar o serviço de aplicações com o Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/create-web-app)

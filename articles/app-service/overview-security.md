---
title: Segurança
description: Saiba como o App Service ajuda a proteger a sua aplicação e como pode bloquear ainda mais a sua aplicação a partir de ameaças.
keywords: Serviço de aplicações azure, web app, aplicação móvel, app api, app de função, segurança, seguro, seguro, conformidade, conformidade, certificado, certificados, https, ftps, tls, trust, encriptação, encriptação, encriptação, encriptação, encriptação, encriptação, encriptação, autação, autorização, authn, autho, msi, identidade de serviço gerida, identidade gerida, segredos, segredos, segredos, patching, patch, versão, isolamento,
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 8a098b1924bf7c2866f6afd7452b8dd3b93f3109
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535660"
---
# <a name="security-in-azure-app-service"></a>Segurança no Serviço de Aplicações Azure

Este artigo mostra-lhe como [o Azure App Service](overview.md) ajuda a proteger a sua aplicação web, aplicação móvel no final, app API e app de [funções](/azure/azure-functions/). Também mostra como pode proteger ainda mais a sua aplicação com as funcionalidades do Serviço de Aplicações incorporadas.

Os componentes da plataforma do App Service, incluindo VMs Azure, armazenamento, conexões de rede, quadros web, funcionalidades de gestão e integração, são ativamente protegidos e endurecidos. O Serviço de Aplicações passa por controlos de conformidade vigorosos numa base contínua para garantir que:

- Os recursos da sua aplicação estão [protegidos](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) dos recursos Azure dos outros clientes.
- [As instâncias vM e o software de tempo de execução são regularmente atualizados](overview-patch-os-runtime.md) para resolver vulnerabilidades recentemente descobertas. 
- A comunicação de segredos (como cordas de ligação) entre a sua app e outros recursos Azure (como a Base de [Dados SQL)](https://azure.microsoft.com/services/sql-database/)permanece dentro do Azure e não ultrapassa quaisquer limites de rede. Os segredos são sempre encriptados quando armazenados.
- Toda a comunicação sobre as funcionalidades de conectividade do Serviço app, como [a ligação híbrida,](app-service-hybrid-connections.md)está encriptada. 
- As ligações com ferramentas de gestão remota como Azure PowerShell, Azure CLI, Azure SDKs, REST APIs, estão todas encriptadas.
- A gestão de ameaças 24 horas protege a infraestrutura e plataforma contra malware, distribuição de negação de serviço (DDoS), man-in-the-middle (MITM) e outras ameaças.

Para obter mais informações sobre infraestruturas e segurança na plataforma em Azure, consulte [o Azure Trust Center.](https://azure.microsoft.com/overview/trusted-cloud/)

As seguintes secções mostram-lhe como proteger ainda mais a sua aplicação de Serviço de Aplicações contra ameaças.

## <a name="https-and-certificates"></a>HTTPS e Certificados

O Serviço de Aplicações permite-lhe proteger as suas aplicações com [HTTPS](https://wikipedia.org/wiki/HTTPS). Quando a sua aplicação é\<criada, o seu nome de domínio padrão (app_name>.azurewebsites.net) já está acessível através do HTTPS. Se [configurar um domínio personalizado para a sua aplicação,](app-service-web-tutorial-custom-domain.md)também deve [alterá-lo com um certificado TLS/SSL](configure-ssl-bindings.md) para que os navegadores de clientes possam fazer ligações HTTPS seguras ao seu domínio personalizado. Existem vários tipos de certificados suportados pelo App Service:

- Certificado gerido pelo serviço de aplicações gratuito
- Certificado de serviço de aplicações
- Certificado de terceiros
- Certificado importado do Cofre-Chave Azure

Para mais informações, consulte [Adicionar um certificado TLS/SSL no Serviço de Aplicações Azure](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protocolos inseguros (HTTP, TLS 1.0, FTP)

Para proteger a sua aplicação contra todas as ligações não encriptadas (HTTP), o Serviço de Aplicações fornece uma configuração de um clique para impor HTTPS. Os pedidos não seguros são rejeitados antes mesmo de chegarem ao seu código de candidatura. Para mais informações, consulte [Enforce HTTPS](configure-ssl-bindings.md#enforce-https).

[O TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 já não é considerado seguro pelas normas da indústria, como [o PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). O Serviço de Aplicações permite-lhe desativar protocolos desatualizados através da aplicação do [TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions).

O Serviço de Aplicações suporta ftp e FTPS para a implementação dos seus ficheiros. No entanto, o FTPS deve ser utilizado em vez de FTP, se possível. Quando um ou ambos os protocolos não estiverem a ser utilizados, deve [desativá-los](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Restrições ip estáticas

Por padrão, a sua aplicação App Service aceita pedidos de todos os endereços IP da internet, mas pode limitar esse acesso a um pequeno subconjunto de endereços IP. O Serviço de Aplicações no Windows permite definir uma lista de endereços IP que podem aceder à sua aplicação. A lista permitida pode incluir endereços IP individuais ou uma série de endereços IP definidos por uma máscara de sub-rede. Para mais informações, consulte [as restrições de IP estáticas do Serviço de Aplicação Azure.](app-service-ip-restrictions.md)

Para o Serviço de Aplicações no Windows, também pode restringir os endereços IP de forma dinâmica configurando o _web.config_. Para mais informações, consulte [Dynamic IP Security \<dynamicIpSecurity>](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Autenticação e autorização do cliente

O Azure App Service fornece autenticação e autorização de utilizadores ou aplicações de clientes. Quando ativado, pode iniciar sessão em utilizadores e aplicações de clientes com pouco ou nenhum código de aplicação. Pode implementar a sua própria solução de autenticação e autorização, ou permitir que o Serviço de Aplicações o trate por si. O módulo de autenticação e autorização trata dos pedidos da Web antes de os entregar ao código de aplicação, e nega pedidos não autorizados antes de atingirem o seu código.

A autenticação e autorização do Serviço app suportam vários fornecedores de autenticação, incluindo o Azure Ative Directory, contas Microsoft, Facebook, Google e Twitter. Para obter mais informações, veja [Autenticação e autorização no Serviço de Aplicações do Azure](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Ao autenticar contra um serviço back-end, o Serviço de Aplicações fornece dois mecanismos diferentes dependendo da sua necessidade:

- **Identidade de serviço** - Inscreva-se no recurso remoto utilizando a identidade da própria app. O Serviço de Aplicações permite-lhe criar facilmente uma [identidade gerida](overview-managed-identity.md), que pode utilizar para autenticar com outros serviços, como o [Azure SQL Database](/azure/sql-database/) ou o [Azure Key Vault](/azure/key-vault/). Para um tutorial de ponta a ponta desta abordagem, consulte a [ligação Secure Azure SQL Database do Serviço de Aplicações utilizando uma identidade gerida](app-service-web-tutorial-connect-msi.md).
- **Em nome da OBO (OBO)** - Faça o acesso delegado a recursos remotos em nome do utilizador. Com o Azure Ative Directory como fornecedor de autenticação, a sua aplicação App Service pode realizar sessão delegada num serviço remoto, como a [Microsoft Graph API](../active-directory/develop/microsoft-graph-intro.md) ou uma aplicação aPI remota no App Service. Para um tutorial de ponta a ponta desta abordagem, consulte [Authenticate e autorize os utilizadores de ponta a ponta no Serviço de Aplicações Azure](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Conectividade com recursos remotos

Existem três tipos de recursos remotos que a sua app pode necessitar de aceder: 

- [Recursos do Azure](#azure-resources)
- [Recursos dentro de uma Rede Virtual Azure](#resources-inside-an-azure-virtual-network)
- [Recursos no local](#on-premises-resources)

Em cada um destes casos, o App Service fornece uma forma de fazer ligações seguras, mas ainda deve observar as melhores práticas de segurança. Por exemplo, utilize sempre ligações encriptadas mesmo que o recurso de back-end permita ligações não encriptadas. Além disso, certifique-se de que o seu serviço Back-end Azure permite o conjunto mínimo de endereços IP. Pode encontrar os endereços IP de saída para a sua aplicação [em endereços IP de entrada e saída no Serviço de Aplicações Azure](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Recursos do Azure

Quando a sua aplicação se conecta aos recursos do Azure, como a Base de [Dados SQL](https://azure.microsoft.com/services/sql-database/) e o [Armazenamento Azure,](/azure/storage/)a ligação permanece dentro do Azure e não ultrapassa quaisquer limites de rede. No entanto, a ligação passa pela rede partilhada em Azure, por isso certifique-se sempre de que a sua ligação está encriptada. 

Se a sua aplicação estiver hospedada num ambiente de Serviço de [Aplicações,](environment/intro.md)deve [ligar-se aos serviços Azure suportados utilizando pontos finais do serviço rede virtual.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="resources-inside-an-azure-virtual-network"></a>Recursos dentro de uma Rede Virtual Azure

A sua aplicação pode aceder a recursos numa [Rede Virtual Azure](/azure/virtual-network/) através da [integração da Rede Virtual.](web-sites-integrate-with-vnet.md) A integração é estabelecida com uma Rede Virtual utilizando uma VPN ponto-a-site. A aplicação pode então aceder aos recursos da Rede Virtual utilizando os seus endereços IP privados. A ligação ponto-a-local, no entanto, ainda atravessa as redes partilhadas em Azure. 

Para isolar completamente a sua conectividade de recursos das redes partilhadas no Azure, crie a sua aplicação num ambiente de Serviço de [Aplicações.](environment/intro.md) Uma vez que um ambiente de Serviço de Aplicações é sempre implantado para uma Rede Virtual dedicada, a conectividade entre a sua app e os recursos dentro da Rede Virtual está totalmente isolada. Para outros aspetos da segurança da rede num ambiente de Serviço de Aplicações, consulte o isolamento da [rede.](#network-isolation)

### <a name="on-premises-resources"></a>Recursos no local

Pode aceder de forma segura aos recursos no local, tais como bases de dados, de três formas: 

- [Ligações híbridas](app-service-hybrid-connections.md) - Estabelece uma ligação ponto-a-ponto ao seu recurso remoto através de um túnel TCP. O túnel TCP é estabelecido utilizando as teclas TLS 1.2 com teclas de assinatura de acesso partilhado (SAS).
- [Integração da Rede Virtual](web-sites-integrate-with-vnet.md) com VPN site-to-site - Conforme descrito em Recursos dentro de [uma Rede Virtual Azure,](#resources-inside-an-azure-virtual-network)mas a Rede Virtual pode ser ligada à sua rede no local através de uma [VPN site-to-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). Nesta topologia da rede, a sua aplicação pode ligar-se a recursos no local, como outros recursos na Rede Virtual.
- Ambiente de Serviço de [Aplicações](environment/intro.md) com VPN site-to-site - Conforme descrito em Recursos dentro de [uma Rede Virtual Azure](#resources-inside-an-azure-virtual-network), mas a Rede Virtual pode ser ligada à sua rede no local através de uma [VPN site-to-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). Nesta topologia da rede, a sua aplicação pode ligar-se a recursos no local, como outros recursos na Rede Virtual.

## <a name="application-secrets"></a>Segredos de aplicação

Não guarde segredos de aplicação, tais como credenciais de base de dados, fichas API e chaves privadas no seu código ou ficheiros de configuração. A abordagem geralmente aceite é acessá-las como [variáveis ambientais](https://wikipedia.org/wiki/Environment_variable) usando o padrão padrão na sua linguagem de eleição. No Serviço de Aplicações, a forma de definir variáveis ambientais é através de definições de [aplicações](configure-common.md#configure-app-settings) (e, especialmente, para aplicações .NET, [cordas de ligação).](configure-common.md#configure-connection-strings) As definições de aplicativos e as cordas de ligação são armazenadas encriptadas no Azure, e só são desencriptadas antes de serem injetadas na memória de processo da sua aplicação quando a aplicação começar. As chaves de encriptação são rodadas regularmente.

Em alternativa, pode integrar a sua app App Service com [o Azure Key Vault](/azure/key-vault/) para uma gestão avançada de segredos. Ao [aceder ao Key Vault com uma identidade gerida,](../key-vault/tutorial-web-application-keyvault.md)a sua aplicação App Service pode aceder de forma segura aos segredos de que necessita.

## <a name="network-isolation"></a>Isolamento da rede

Com exceção do nível de preços **isolados,** todos os níveis executam as suas aplicações na infraestrutura de rede partilhada no Serviço de Aplicações. Por exemplo, os endereços IP públicos e os equilibradores de carga front-end são partilhados com outros inquilinos. O nível **Isolado** dá-lhe o isolamento total da rede executando as suas aplicações dentro de um ambiente dedicado ao Serviço de [Aplicações.](environment/intro.md) Um ambiente de serviço de aplicações funciona no seu próprio exemplo de [Rede Virtual Azure](/azure/virtual-network/). Permite-lhe: 

- Sirva as suas apps através de um ponto final público dedicado, com extremidades dianteiras dedicadas.
- Sirva a aplicação interna utilizando um equilibrador de carga interno (ILB), que permite o acesso apenas a partir de dentro da sua Rede Virtual Azure. O ILB tem um endereço IP da sua subnet privada, que proporciona o isolamento total das suas aplicações a partir da internet.
- Utilize um ILB por trás de uma firewall de [aplicação web (WAF)](environment/integrate-with-application-gateway.md). O WAF oferece proteção ao nível da empresa às suas aplicações viradas para o público, tais como proteção DDoS, filtragem URI e prevenção de injeção SQL.

Para mais informações, consulte Introdução aos Ambientes de Serviço de [Aplicações Azure](environment/intro.md). 

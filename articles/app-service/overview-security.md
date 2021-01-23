---
title: Segurança
description: Saiba como o Serviço de Aplicações ajuda a proteger a sua aplicação e como pode bloquear ainda mais a sua aplicação a partir de ameaças.
keywords: serviço de aplicações azure, aplicativo web, aplicativo móvel, app API, app de função, segurança, seguro, seguro, conformidade, conformidade, certificado, certificado, certificados, https, ftps, tls, trust, encryption, encrypted, encrypted, imut, restrição ip, autenticação, autorização, authn, msi, identidade de serviço gerido, identidade gerida, segredos, patch, patch, versão,
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 61bffcfa8b98ed666e450c344023258b752e4880
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736110"
---
# <a name="security-in-azure-app-service"></a>Segurança no Serviço de Aplicações do Azure

Este artigo mostra-lhe como o [Azure App Service](overview.md) ajuda a proteger a sua aplicação web, a aplicação móvel back end, a app API e [a aplicação de função.](../azure-functions/index.yml) Também mostra como pode proteger ainda mais a sua aplicação com as funcionalidades incorporadas do Serviço de Aplicações.

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

As secções seguintes mostram-lhe como proteger ainda mais a sua aplicação de Serviço de Aplicações contra ameaças.

## <a name="https-and-certificates"></a>HTTPS e Certificados

O Serviço de Aplicações permite-lhe proteger as suas aplicações com [HTTPS](https://wikipedia.org/wiki/HTTPS). Quando a sua aplicação é criada, o seu nome de domínio predefinido \<app_name> (.azurewebsites.net) já está acessível através do HTTPS. Se [configurar um domínio personalizado para a sua aplicação,](app-service-web-tutorial-custom-domain.md)também deverá [fixá-lo com um certificado TLS/SSL](configure-ssl-bindings.md) para que os navegadores do cliente possam fazer ligações HTTPS seguras ao seu domínio personalizado. Existem vários tipos de certificados suportados pelo Serviço de Aplicações:

- Certificado gerido pelo Serviço de Aplicações Gratuitas
- Certificado de Serviço de Aplicações
- Certificado de terceiros
- Certificado importado do Cofre da Chave Azure

Para mais informações, consulte [Adicionar um certificado TLS/SSL no Azure App Service](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protocolos inseguros (HTTP, TLS 1.0, FTP)

Para proteger a sua aplicação contra todas as ligações não encriptadas (HTTP), o Serviço de Aplicações fornece uma configuração de um clique para impor HTTPS. Os pedidos não protegidos são rejeitados antes mesmo de chegarem ao código de aplicação. Para mais informações, consulte [Enforce HTTPS](configure-ssl-bindings.md#enforce-https).

[O TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 já não é considerado seguro pelas normas da indústria, tais como [pci DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). O Serviço de Aplicações permite-lhe desativar protocolos desatualizados, [aplicando o TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions).

O Serviço de Aplicações suporta tanto o FTP como o FTPS para a implementação dos seus ficheiros. No entanto, os FTPS devem ser utilizados em vez de FTP, se possível. Quando um ou ambos os protocolos não estiverem a ser utilizados, deve [desativá-los](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Restrições de IP estático

Por predefinição, a sua aplicação De Serviço de Aplicações aceita pedidos de todos os endereços IP a partir da internet, mas pode limitar esse acesso a um pequeno subconjunto de endereços IP. O Serviço de Aplicações no Windows permite definir uma lista de endereços IP que podem aceder à sua aplicação. A lista permitida pode incluir endereços IP individuais ou uma gama de endereços IP definidos por uma máscara de sub-rede. Para obter mais informações, consulte [as restrições estáticas de IP do Serviço de Aplicações Azure.](app-service-ip-restrictions.md)

Para o Serviço de Aplicações no Windows, também pode restringir os endereços IP de forma dinâmica configurando o _web.config_. Para mais informações, consulte [a Dynamic IP Security \<dynamicIpSecurity>](/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Autenticação e autorização do cliente

O Azure App Service fornece autenticação chave-na-turno e autorização de utilizadores ou aplicações de clientes. Quando ativado, pode iniciar sação em utilizadores e aplicações de clientes com pouco ou nenhum código de aplicação. Pode implementar a sua própria solução de autenticação e autorização, ou permitir que o Serviço de Aplicações o manuseie por si. O módulo de autenticação e autorização trata dos pedidos da Web antes de os entregar ao código de aplicação, e nega pedidos não autorizados antes de chegarem ao seu código.

A autenticação e autorização do Serviço de Aplicações suportam vários fornecedores de autenticação, incluindo o Azure Ative Directory, contas microsoft, Facebook, Google e Twitter. Para obter mais informações, veja [Autenticação e autorização no Serviço de Aplicações do Azure](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Ao autenticar contra um serviço back-end, o App Service fornece dois mecanismos diferentes dependendo da sua necessidade:

- **Identidade de** serviço - Inscreva-se no recurso remoto utilizando a identidade da própria app. O Serviço de Aplicações permite-lhe criar facilmente uma [identidade gerida,](overview-managed-identity.md)que pode utilizar para autenticar com outros serviços, como [a Base de Dados Azure SQL](/azure/sql-database/) ou [o Cofre da Chave Azure.](../key-vault/index.yml) Para obter um tutorial de ponta a ponta desta abordagem, consulte a [ligação Secure Azure SQL Database do Serviço de Aplicações utilizando uma identidade gerida.](app-service-web-tutorial-connect-msi.md)
- **Em nome da OBO -** Faça o acesso delegado a recursos remotos em nome do utilizador. Com o Azure Ative Directory como fornecedor de autenticação, a sua aplicação de Serviço de Aplicações pode realizar o sign-in delegado a um serviço remoto, como [a Microsoft Graph API](../active-directory/develop/microsoft-graph-intro.md) ou uma aplicação remota da API no Serviço de Aplicações. Para obter um tutorial de ponta a ponta desta abordagem, consulte [Authenticate e autorize os utilizadores de ponta a ponta no Azure App Service](tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Conectividade com recursos remotos

Existem três tipos de recursos remotos que a sua aplicação poderá ter de aceder: 

- [Recursos do Azure](#azure-resources)
- [Recursos dentro de uma Rede Virtual Azure](#resources-inside-an-azure-virtual-network)
- [Recursos no local](#on-premises-resources)

Em cada um destes casos, o App Service fornece uma forma de fazer ligações seguras, mas ainda deve observar as melhores práticas de segurança. Por exemplo, utilize sempre ligações encriptadas mesmo que o recurso back-end permita ligações não encriptadas. Além disso, certifique-se de que o seu serviço de back-end Azure permite o conjunto mínimo de endereços IP. Pode encontrar os endereços IP de saída para a sua aplicação nos [endereços IP de entrada e saída no Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Recursos do Azure

Quando a sua aplicação se conecta aos recursos Azure, como [a SQL Database](https://azure.microsoft.com/services/sql-database/) e [o Azure Storage,](../storage/index.yml)a ligação permanece dentro do Azure e não ultrapassa os limites da rede. No entanto, a ligação passa pela rede partilhada em Azure, por isso certifique-se sempre de que a sua ligação está encriptada. 

Se a sua aplicação estiver hospedada num [ambiente de Serviço de Aplicações,](environment/intro.md)deverá [ligar-se aos serviços Azure suportados utilizando pontos finais de serviço de Rede Virtual.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="resources-inside-an-azure-virtual-network"></a>Recursos dentro de uma Rede Virtual Azure

A sua aplicação pode aceder a recursos numa [Rede Virtual Azure](../virtual-network/index.yml) através da [integração da Rede Virtual.](web-sites-integrate-with-vnet.md) A integração é estabelecida com uma Rede Virtual utilizando uma VPN ponto-a-local. A aplicação pode então aceder aos recursos na Rede Virtual utilizando os seus endereços IP privados. A ligação ponto-a-local, no entanto, ainda atravessa as redes partilhadas em Azure. 

Para isolar completamente a conectividade dos recursos das redes partilhadas no Azure, crie a sua aplicação num [ambiente de Serviço de Aplicações.](environment/intro.md) Uma vez que um ambiente de Serviço de Aplicações é sempre implantado numa Rede Virtual dedicada, a conectividade entre a sua app e os recursos dentro da Rede Virtual está totalmente isolada. Para outros aspetos da segurança da rede num ambiente de Serviço de Aplicações, consulte [o isolamento da Rede](#network-isolation).

### <a name="on-premises-resources"></a>Recursos no local

Pode aceder de forma segura aos recursos no local, como bases de dados, de três formas: 

- [Ligações híbridas](app-service-hybrid-connections.md) - Estabelece uma ligação ponto-a-ponto ao seu recurso remoto através de um túnel TCP. O túnel TCP é estabelecido utilizando as teclas TLS 1.2 com assinatura de acesso partilhado (SAS).
- [Integração de Rede Virtual](web-sites-integrate-with-vnet.md) com VPN site-to-site - Como descrito em [Recursos dentro de uma Rede Virtual Azure](#resources-inside-an-azure-virtual-network), mas a Rede Virtual pode ser ligada à sua rede de instalações através de uma [VPN site-to-site](../vpn-gateway/tutorial-site-to-site-portal.md). Nesta topologia de rede, a sua aplicação pode ligar-se a recursos no local, como outros recursos na Rede Virtual.
- [Ambiente de Serviço](environment/intro.md) de Aplicações com VPN site-to-site - Como descrito em [Recursos dentro de uma Rede Virtual Azure](#resources-inside-an-azure-virtual-network), mas a Rede Virtual pode ser conectado à sua rede de instalações através de uma [VPN site-to-site](../vpn-gateway/tutorial-site-to-site-portal.md). Nesta topologia de rede, a sua aplicação pode ligar-se a recursos no local, como outros recursos na Rede Virtual.

## <a name="application-secrets"></a>Segredos de aplicação

Não guarde segredos de aplicações, tais como credenciais de base de dados, fichas API e chaves privadas nos seus ficheiros de código ou configuração. A abordagem geralmente aceite é aceder-lhes como [variáveis ambientais](https://wikipedia.org/wiki/Environment_variable) usando o padrão padrão na sua linguagem de eleição. No Serviço de Aplicações, a forma de definir variáveis ambientais é através de [configurações de aplicações](configure-common.md#configure-app-settings) (e, especialmente para aplicações .NET, [cadeias de ligação).](configure-common.md#configure-connection-strings) As definições de aplicações e as cadeias de ligação são armazenadas encriptadas no Azure, e são desencriptadas apenas antes de serem injetadas na memória de processo da sua aplicação quando a aplicação começa. As chaves de encriptação são rodadas regularmente.

Em alternativa, pode integrar a sua aplicação de Serviço de Aplicações com [o Azure Key Vault](../key-vault/index.yml) para gestão de segredos avançados. Ao [aceder ao Cofre-Chave com uma identidade gerida,](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)a sua aplicação App Service pode aceder de forma segura aos segredos de que necessita.

## <a name="network-isolation"></a>Isolamento da rede

Com exceção do nível de preços isolado, todos os níveis **executam** as suas aplicações na infraestrutura de rede partilhada no Serviço de Aplicações. Por exemplo, os endereços IP públicos e os equilibradores de carga frontal são partilhados com outros inquilinos. O nível **isolado** dá-lhe um completo isolamento de rede executando as suas aplicações dentro de um ambiente dedicado [do Serviço de Aplicações.](environment/intro.md) Um ambiente de Serviço de Aplicações funciona no seu próprio caso de [Rede Virtual Azure.](../virtual-network/index.yml) Permite-lhe: 

- Sirva as suas aplicações através de um ponto final público dedicado, com extremidades frontais dedicadas.
- Sirva a aplicação interna utilizando um equilibrador de carga interno (ILB), que permite o acesso apenas a partir de dentro da sua Rede Virtual Azure. O ILB tem um endereço IP da sua sub-rede privada, que proporciona o isolamento total das suas aplicações a partir da internet.
- [Utilize um ILB por trás de uma firewall de aplicação web (WAF)](environment/integrate-with-application-gateway.md). O WAF oferece proteção ao nível da empresa para as suas aplicações viradas para o público, tais como proteção DDoS, filtragem URI e prevenção de injeção SQL.

Para mais informações, consulte [Introdução aos Ambientes de Serviço de Aplicações Azure](environment/intro.md).
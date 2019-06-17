---
title: Descrição geral de segurança - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba mais sobre como o serviço de aplicações ajuda a proteger seu aplicativo e como pode mais bloquear a aplicação de ameaças.
keywords: serviço de aplicações do Azure, aplicação web, aplicação móvel, a aplicação de api, aplicação de funções, segurança, segura e protegida, conformidade, em conformidade, certificado, certificados, https, ftps, tls, confiança, encriptação, encriptar, encriptados, restrições de ip, autenticação, autorização, authn, autho, msi, identidade de serviço gerida, identidade gerida, segredos, segredo, aplicação de patches, patch, patches, versão, isolamento, isolamento de rede, ddos, mitm
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0e592c03da222e5265ed53aab8ef73f3b477f33a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475858"
---
# <a name="security-in-azure-app-service"></a>Segurança no serviço de aplicações do Azure

Este artigo mostra-lhe como [App Service do Azure](overview.md) ajuda a proteger a sua aplicação web, o back-end de aplicação móvel, a aplicação API, e [aplicação de funções](/azure/azure-functions/). Ela também mostra como pode proteger ainda mais sua aplicação com as funcionalidades do serviço de aplicações incorporadas.

Os componentes da plataforma do serviço de aplicações, incluindo as VMs do Azure, armazenamento, ligações de rede, estruturas da web, recursos de gerenciamento e integração, ativamente são seguros e protegidos. Serviço de aplicações é através de verificações de conformidade vigorous de forma contínua para se certificar de que:

- Os recursos de aplicação estão [segura](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) dos recursos do Azure de outros clientes.
- [Instâncias de VM e o tempo de execução software são atualizadas regularmente](overview-patch-os-runtime.md) a vulnerabilidades de endereço detetado recentemente. 
- Comunicação de segredos (por exemplo, cadeias de ligação) entre a aplicação e outros recursos do Azure (por exemplo, [base de dados SQL](https://azure.microsoft.com/services/sql-database/)) permanece no Azure e não em várias quaisquer limites de rede. Segredos são sempre encriptados quando armazenada.
- Funcionalidades de todas as comunicações sobre a conectividade do serviço de aplicações, como [ligação híbrida](app-service-hybrid-connections.md), são encriptados. 
- Ligações com ferramentas de gestão remota, como o Azure PowerShell, CLI do Azure, SDKs do Azure, REST APIs, que são todos os encriptados.
- ameaças de 24 horas management protege a infraestrutura e plataforma contra malware, distributed denial of service (DDoS), man-in-the-middle (MITM) e outras ameaças.

Para obter mais informações sobre infraestrutura e plataforma de segurança no Azure, consulte [Centro de fidedignidade do Azure](https://azure.microsoft.com/overview/trusted-cloud/).

As secções seguintes mostram como proteger ainda mais o seu serviço de aplicações de ameaças.

## <a name="https-and-certificates"></a>HTTPS e certificados

Serviço de aplicações permite-lhe proteger as suas aplicações com [HTTPS](https://wikipedia.org/wiki/HTTPS). Quando a aplicação é criada, seu nome de domínio predefinido (\<app_name >. azurewebsites.net) já se encontra acessível através de HTTPS. Se [configurar um domínio personalizado para a sua aplicação](app-service-web-tutorial-custom-domain.md), deve também [protegê-la com um certificado personalizado](app-service-web-tutorial-custom-ssl.md) , para que browsers cliente possam ligações de HTTPS seguras ao seu domínio personalizado. Existem duas formas de fazê-lo:

- **Certificado de serviço de aplicações** -criar um certificado diretamente no Azure. O certificado está protegido na [do Azure Key Vault](/azure/key-vault/)e podem ser importados para o seu serviço de aplicações. Para obter mais informações, consulte [comprar e configurar um certificado SSL para o serviço de aplicações do Azure](web-sites-purchase-ssl-web-site.md).
- **Certificado de terceiros** - carregar um certificado SSL personalizado que comprado numa autoridade de certificado fidedigno e vinculá-lo à sua aplicação de serviço de aplicações. Serviço de aplicações suporta certificados de domínio único e certificados de caráter universal. Também suporta certificados autoassinados para fins de teste. Para obter mais informações, consulte [vincular um certificado SSL personalizado já existente para o serviço de aplicações do Azure](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protocolos não seguro (HTTP, TLS 1.0, FTP)

Para proteger a sua aplicação contra todas as ligações de (protocolo HTTP) não encriptadas, o serviço de aplicações fornece configuração de um clique para impor HTTPS. Pedidos não seguros são ativados imediatamente antes de atingirem até mesmo o código da aplicação. Para obter mais informações, consulte [impor HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 é já não é considerado seguro pelas normas do setor, tais como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Serviço de aplicações permite-lhe desativar protocolos desatualizados ao [impor TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

Serviço de aplicações suporta FTP e FTPS para implementar os seus ficheiros. No entanto, FTPS deve ser usado em vez de FTP, se possível. Quando um ou ambos os protocolos não estão em utilização, deverá [desativá-las](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Restrições de IP estáticas

Por predefinição, o seu serviço de aplicações aceita os pedidos de todos os endereços IP da internet, mas pode limitar o que o acesso a um pequeno subconjunto de endereços IP. Serviço de aplicações no Windows permite-lhe definir uma lista de endereços IP que têm permissão para aceder à sua aplicação. Lista de permissões pode incluir os endereços IP individuais ou um intervalo de endereços IP definidos por uma máscara de sub-rede. Para obter mais informações, consulte [restrições de IP do Azure App Service estático](app-service-ip-restrictions.md).

Para o serviço de aplicações no Windows, pode também restringir os endereços IP dinamicamente ao configurar o _Web. config_. Para obter mais informações, consulte [segurança de IP dinâmica \<dynamicIpSecurity >](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Autorização e autenticação de cliente

Serviço de aplicações do Azure fornece autenticação de chave na mão e autorização de utilizadores ou cliente aplicações. Quando ativada, pode iniciar sessão em utilizadores e aplicações de cliente com pouco ou nenhum código de aplicação. Pode implementar a sua própria solução de autenticação e autorização, ou permitir que o serviço de aplicações para manipulá-lo para. O módulo de autenticação e autorização processa solicitações da web antes de manuseio-los para o código da aplicação e nega pedidos não autorizados, antes de atingirem o seu código.

Autorização e autenticação de serviço de aplicações suportam vários fornecedores de autenticação, incluindo o Azure Active Directory, contas de Microsoft, Facebook, Google e Twitter. Para obter mais informações, consulte [autenticação e autorização no serviço de aplicações do Azure](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Durante a autenticação em relação a um serviço de back-end, o serviço de aplicações fornece dois mecanismos diferentes dependendo da sua necessidade:

- **Identidade de serviço** -iniciar sessão para o recurso remoto usando a identidade da aplicação em si. Serviço de aplicações permite-lhe criar facilmente um [identidade gerida](overview-managed-identity.md), que pode ser usado para autenticar com outros serviços, como [base de dados do Azure SQL](/azure/sql-database/) ou [Azure Key Vault](/azure/key-vault/). Para obter um tutorial ponto-a-ponto dessa abordagem, veja [ligação de proteger a base de dados de SQL do Azure a partir do serviço de aplicações com uma identidade gerida](app-service-web-tutorial-connect-msi.md).
- **Em-nome-de (OBO)** -fazer o acesso delegado a recursos remotos em nome do utilizador. Com o Azure Active Directory como o fornecedor de autenticação, a aplicação de serviço de aplicações pode executar delegado início de sessão para um serviço remoto, como [do Azure Active Directory Graph API](../active-directory/develop/active-directory-graph-api.md) ou uma aplicação de API remota no serviço de aplicações. Para obter um tutorial ponto-a-ponto dessa abordagem, veja [autenticar e autorizar utilizadores ponto-a-ponto no serviço de aplicações do Azure](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Conectividade a recursos remotos

Existem três tipos de recursos remotos, que a aplicação poderá ter de aceder a: 

- [Recursos do Azure](#azure-resources)
- [Recursos dentro de uma rede Virtual do Azure](#resources-inside-an-azure-virtual-network)
- [Recursos no local](#on-premises-resources)

Em cada um desses casos, o serviço de aplicações fornece uma forma que precisa tomar ligações seguras, mas observe ainda melhores práticas de segurança. Por exemplo, utilize sempre ligações encriptadas, mesmo que o recurso de back-end permite conexões não criptografadas. Além disso, certifique-se de que o serviço do Azure de back-end permite que o conjunto mínimo de endereços IP. Pode encontrar os endereços IP de saída para a sua aplicação em [de entrada e endereços IP de saída no serviço de aplicações do Azure](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Recursos do Azure

Quando a aplicação se liga a recursos do Azure, tal como [base de dados SQL](https://azure.microsoft.com/services/sql-database/) e [armazenamento do Azure](/azure/storage/), a ligação permanece no Azure e não em várias quaisquer limites de rede. No entanto, a ligação atravessa a rede partilhada no Azure, por conseguinte, certifique-se de que a ligação está encriptada. 

Se a sua aplicação estiver alojada numa [ambiente do serviço de aplicações](environment/intro.md), deve [ligar a serviços do Azure através de pontos finais de serviço de rede Virtual suportados](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Recursos dentro de uma rede Virtual do Azure

A aplicação pode aceder a recursos num [rede Virtual do Azure](/azure/virtual-network/) através de [integração da rede Virtual](web-sites-integrate-with-vnet.md). A integração é estabelecida com uma rede Virtual com uma VPN ponto a site. A aplicação, em seguida, pode aceder aos recursos na rede Virtual através dos endereços IP privados. No entanto, a ligação ponto a site, ainda atravessa as redes compartilhadas no Azure. 

Para isolar a conectividade de recurso completamente das redes partilhadas no Azure, criar a sua aplicação uma [ambiente do serviço de aplicações](environment/intro.md). Uma vez que um ambiente de serviço de aplicações sempre for implementado para uma rede Virtual dedicada, a conectividade entre o aplicativo e os recursos dentro da rede Virtual é totalmente isolada. Para outros aspectos de segurança de rede num ambiente de serviço de aplicações, consulte [isolamento de rede](#network-isolation).

### <a name="on-premises-resources"></a>Recursos no local

Pode aceder em segurança a recursos no local, como bases de dados, de três formas: 

- [Ligações híbridas](app-service-hybrid-connections.md) -estabelece uma ligação ponto a ponto para o recurso remoto através de um túnel TCP. Estabelecido o túnel TCP usando o TLS 1.2 com chaves de assinatura (SAS) de acesso partilhado.
- [Integração da rede virtual](web-sites-integrate-with-vnet.md) com a VPN de site a site – conforme descrito em [recursos dentro de uma rede Virtual do Azure](#resources-inside-an-azure-virtual-network), mas a rede Virtual pode ser ligada à sua rede no local através de um [ VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). Nesta topologia de rede, a sua aplicação pode ligar a recursos no local, como outros recursos na rede Virtual.
- [Ambiente de serviço de aplicações](environment/intro.md) com a VPN de site a site – conforme descrito em [recursos dentro de uma rede Virtual do Azure](#resources-inside-an-azure-virtual-network), mas a rede Virtual pode ser ligada à sua rede no local através de um [site a site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). Nesta topologia de rede, a sua aplicação pode ligar a recursos no local, como outros recursos na rede Virtual.

## <a name="application-secrets"></a>Segredos da aplicação

Não armazene segredos da aplicação, como credenciais de base de dados, tokens de API e as chaves privadas em seus arquivos de código ou configuração. A abordagem mais aceita é acessá-los como [variáveis de ambiente](https://wikipedia.org/wiki/Environment_variable) usando o padrão na linguagem de sua escolha. No serviço de aplicações, a forma de definir as variáveis de ambiente é através de [as definições da aplicação](configure-common.md#configure-app-settings) (e, especialmente para aplicações de .NET [cadeias de ligação](configure-common.md#configure-connection-strings)). Definições de aplicações e as cadeias de ligação são armazenadas de forma encriptada no Azure, e eles são desencriptados apenas antes de a ser injetado na memória de processo da sua aplicação quando a aplicação for iniciada. As chaves de encriptação são revezadas regularmente.

Em alternativa, pode integrar o seu serviço de aplicações com [do Azure Key Vault](/azure/key-vault/) para a gestão de segredos avançadas. Por [aceder ao Key Vault com uma identidade gerida](../key-vault/tutorial-web-application-keyvault.md), seu serviço de aplicações pode aceder em segurança os segredos que precisa.

## <a name="network-isolation"></a>Isolamento de rede

Exceto para o **Isolated** escalão de preço, todos os escalões de executam as suas aplicações na infraestrutura de rede partilhado no serviço de aplicações. Por exemplo, os endereços IP públicos e Balanceadores de carga de front-end são partilhadas com outros inquilinos. O **Isolated** escalão dá-lhe isolamento de rede completa ao executar as suas aplicações dentro de um dedicado [ambiente de serviço de aplicações](environment/intro.md). Um ambiente de serviço de aplicações é executada na sua própria instância do [rede Virtual do Azure](/azure/virtual-network/). Permite-lhe: 

- Servir as suas aplicações através de um ponto final público dedicado, com dedicado front-ends.
- Servir aplicação interna com um balanceador de carga interno (ILB), que permite o acesso apenas a partir de dentro da sua rede Virtual do Azure. O ILB tem um endereço IP do seu sub-rede privada, o que proporciona isolamento total das suas aplicações a partir da internet.
- [Utilizar um ILB por trás de uma firewall de aplicações web (WAF)](environment/integrate-with-application-gateway.md). O WAF proporciona proteção de nível empresarial às suas aplicações de destinado ao público, como proteção contra DDoS, URI filtragem e prevenção de injeção de SQL.

Para obter mais informações, consulte [introdução aos ambientes de serviço de aplicações do Azure](environment/intro.md). 

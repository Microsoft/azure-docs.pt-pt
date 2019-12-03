---
title: Segurança
description: Saiba mais sobre como o serviço de aplicativo ajuda a proteger seu aplicativo e como você pode bloquear ainda mais seu aplicativo contra ameaças.
keywords: serviço de aplicativo do Azure, aplicativo Web, aplicativo móvel, aplicativo de API, aplicativo de funções, segurança, seguro, seguro, conformidade, compatível, certificado, certificados, HTTPS, FTPS, TLS, confiança, criptografia, criptografar, criptografar, restrição de IP, autenticação, autorização, Authn, autho, MSI, identidade de serviço gerenciado, identidade gerenciada, segredos, segredo, aplicação de patch, patch, patches, versão, isolamento, isolamento de rede, DDoS, MITM
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 28394689048e730aa0c84e3bf807ef3afb898b1e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688564"
---
# <a name="security-in-azure-app-service"></a>Segurança no serviço de Azure App

Este artigo mostra como [Azure app serviço](overview.md) ajuda a proteger seu aplicativo Web, back-end de aplicativo móvel, aplicativo de API e [aplicativo de funções](/azure/azure-functions/). Ele também mostra como você pode proteger ainda mais seu aplicativo com os recursos internos do serviço de aplicativo.

Os componentes da plataforma do serviço de aplicativo, incluindo as VMs do Azure, armazenamento, conexões de rede, estruturas da Web, recursos de gerenciamento e integração, são ativamente protegidos e protegidos. O serviço de aplicativo passa por verificações de conformidade rigorosas continuamente para garantir que:

- Os recursos do aplicativo são [protegidos](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) dos recursos do Azure dos outros clientes.
- As [instâncias de VM e o software de tempo de execução são atualizados regularmente](overview-patch-os-runtime.md) para resolver vulnerabilidades recém-descobertas. 
- A comunicação de segredos (como cadeias de conexão) entre seu aplicativo e outros recursos do Azure (como o [banco de dados SQL](https://azure.microsoft.com/services/sql-database/)) permanece no Azure e não cruza nenhum limite de rede. Os segredos são sempre criptografados quando armazenados.
- Toda a comunicação sobre os recursos de conectividade do serviço de aplicativo, como [conexão híbrida](app-service-hybrid-connections.md), é criptografada. 
- As conexões com ferramentas de gerenciamento remoto, como Azure PowerShell, CLI do Azure, SDKs do Azure, APIs REST, são todas criptografadas.
- o gerenciamento de ameaças de 24 horas protege a infraestrutura e a plataforma contra malware, DDoS (negação de serviço distribuído), MITM (Man-in-the-Middle) e outras ameaças.

Para obter mais informações sobre infraestrutura e segurança de plataforma no Azure, consulte [central de confiabilidade do Azure](https://azure.microsoft.com/overview/trusted-cloud/).

As seções a seguir mostram como proteger ainda mais seu aplicativo de serviço de aplicativo contra ameaças.

## <a name="https-and-certificates"></a>HTTPS e certificados

O serviço de aplicativo permite que você proteja seus aplicativos com [https](https://wikipedia.org/wiki/HTTPS). Quando seu aplicativo for criado, seu nome de domínio padrão (\<app_name >. azurewebsites. net) já estará acessível usando HTTPS. Se você [configurar um domínio personalizado para seu aplicativo](app-service-web-tutorial-custom-domain.md), também deverá [protegê-lo com um certificado SSL](configure-ssl-bindings.md) para que os navegadores cliente possam fazer conexões HTTPS seguras com seu domínio personalizado. Há vários tipos de certificados com suporte pelo serviço de aplicativo:

- Serviço de Aplicativo Gratuito certificado gerenciado
- Certificado do serviço de aplicativo
- Certificado de terceiros
- Certificado importado de Azure Key Vault

Para obter mais informações, consulte [Adicionar um certificado SSL no serviço de Azure app](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protocolos inseguros (HTTP, TLS 1,0, FTP)

Para proteger seu aplicativo contra todas as conexões não criptografadas (HTTP), o serviço de aplicativo fornece uma configuração de clique para impor HTTPS. As solicitações não seguras são desativadas antes mesmo de alcançar o código do aplicativo. Para obter mais informações, consulte [impor https](configure-ssl-bindings.md#enforce-https).

O [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1,0 não é mais considerado seguro por padrões do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). O serviço de aplicativo permite que você desabilite protocolos desatualizados [impondo o TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions).

O serviço de aplicativo dá suporte a FTP e FTPS para implantar seus arquivos. No entanto, FTPS deve ser usado em vez de FTP, se possível. Quando um ou ambos os protocolos não estão em uso, você deve [desabilitá-los](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Restrições de IP estático

Por padrão, o aplicativo do serviço de aplicativo aceita solicitações de todos os endereços IP da Internet, mas você pode limitar o acesso a um pequeno subconjunto de endereços IP. O serviço de aplicativo no Windows permite que você defina uma lista de endereços IP que têm permissão para acessar seu aplicativo. A lista de permissões pode incluir endereços IP individuais ou um intervalo de endereços IP definidos por uma máscara de sub-rede. Para obter mais informações, consulte [Azure app restrições de IP estático do serviço](app-service-ip-restrictions.md).

Para o serviço de aplicativo no Windows, você também pode restringir os endereços IP dinamicamente Configurando o _Web. config_. Para obter mais informações, consulte [segurança de IP dinâmico \<dynamicIpSecurity >](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Autenticação e autorização de cliente

Azure App serviço fornece autenticação de chave e autorização de usuários ou aplicativos cliente. Quando habilitado, ele pode entrar em usuários e aplicativos cliente com pouco ou nenhum código de aplicativo. Você pode implementar sua própria solução de autenticação e autorização ou permitir que o serviço de aplicativo a manipule para você em vez disso. O módulo de autenticação e autorização manipula solicitações da Web antes de entregá-las ao código do aplicativo e nega solicitações não autorizadas antes que elas cheguem ao seu código.

A autenticação e a autorização do serviço de aplicativo dão suporte a vários provedores de autenticação, incluindo Azure Active Directory, contas da Microsoft, Facebook, Google e Twitter. Para obter mais informações, veja [Autenticação e autorização no Serviço de Aplicações do Azure](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Ao autenticar em um serviço de back-end, o serviço de aplicativo fornece dois mecanismos diferentes dependendo da sua necessidade:

- **Identidade do serviço** -entre no recurso remoto usando a identidade do aplicativo em si. O serviço de aplicativo permite que você crie facilmente uma [identidade gerenciada](overview-managed-identity.md), que pode ser usada para autenticar com outros serviços, como o [banco de dados SQL do Azure](/azure/sql-database/) ou o [Azure Key Vault](/azure/key-vault/). Para um tutorial completo dessa abordagem, consulte [proteger a conexão do banco de dados SQL do Azure do serviço de aplicativo usando uma identidade gerenciada](app-service-web-tutorial-connect-msi.md).
- **Em nome de (obo)** – torne o acesso delegado a recursos remotos em nome do usuário. Com Azure Active Directory como o provedor de autenticação, seu aplicativo do serviço de aplicativo pode executar a entrada delegada em um serviço remoto, como [Azure Active Directory API do Graph](../active-directory/develop/active-directory-graph-api.md) ou um aplicativo de API remoto no serviço de aplicativo. Para obter um tutorial completo dessa abordagem, consulte [autenticar e autorizar usuários de ponta a ponta no serviço de Azure app](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Conectividade com recursos remotos

Há três tipos de recursos remotos que seu aplicativo pode precisar acessar: 

- [Recursos do Azure](#azure-resources)
- [Recursos dentro de uma rede virtual do Azure](#resources-inside-an-azure-virtual-network)
- [Recursos locais](#on-premises-resources)

Em cada um desses casos, o serviço de aplicativo fornece uma maneira de fazer conexões seguras, mas você ainda deve observar as práticas recomendadas de segurança. Por exemplo, sempre use conexões criptografadas, mesmo se o recurso de back-end permitir conexões não criptografadas. Além disso, verifique se o serviço back-end do Azure permite o conjunto mínimo de endereços IP. Você pode encontrar os endereços IP de saída para seu aplicativo em [endereços IP de entrada e saída no serviço Azure app](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Recursos do Azure

Quando seu aplicativo se conecta aos recursos do Azure, como o [banco de dados SQL](https://azure.microsoft.com/services/sql-database/) e o [armazenamento do Azure](/azure/storage/), a conexão permanece no Azure e não cruza nenhum limite de rede. No entanto, a conexão passa pela rede compartilhada no Azure, portanto, sempre verifique se a conexão está criptografada. 

Se seu aplicativo estiver hospedado em um [ambiente do serviço de aplicativo](environment/intro.md), você deverá [se conectar aos serviços do Azure com suporte usando pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Recursos dentro de uma rede virtual do Azure

Seu aplicativo pode acessar recursos em uma [rede virtual do Azure](/azure/virtual-network/) por meio da [integração de rede virtual](web-sites-integrate-with-vnet.md). A integração é estabelecida com uma rede virtual usando uma VPN ponto a site. O aplicativo pode então acessar os recursos na rede virtual usando seus endereços IP privados. A conexão ponto a site, no entanto, ainda atravessa as redes compartilhadas no Azure. 

Para isolar completamente a conectividade de recursos das redes compartilhadas no Azure, crie seu aplicativo em um [ambiente do serviço de aplicativo](environment/intro.md). Como um ambiente do serviço de aplicativo sempre é implantado em uma rede virtual dedicada, a conectividade entre o aplicativo e os recursos na rede virtual é totalmente isolada. Para outros aspectos da segurança de rede em um ambiente do serviço de aplicativo, consulte [isolamento de rede](#network-isolation).

### <a name="on-premises-resources"></a>Recursos locais

Você pode acessar com segurança recursos locais, como bancos de dados, de três maneiras: 

- [Conexões híbridas](app-service-hybrid-connections.md) – estabelece uma conexão ponto a ponto com o recurso remoto por meio de um túnel TCP. O túnel TCP é estabelecido usando TLS 1,2 com chaves SAS (assinatura de acesso compartilhado).
- [Integração de rede virtual](web-sites-integrate-with-vnet.md) com VPN site a site – conforme descrito em [recursos dentro de uma rede virtual do Azure](#resources-inside-an-azure-virtual-network), mas a rede virtual pode ser conectada à sua rede local por meio de uma [VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). Nessa topologia de rede, seu aplicativo pode se conectar a recursos locais, como outros recursos na rede virtual.
- [Ambiente do serviço de aplicativo](environment/intro.md) com VPN site a site-conforme descrito em [recursos dentro de uma rede virtual do Azure](#resources-inside-an-azure-virtual-network), mas a rede virtual pode ser conectada à sua rede local por meio de uma [VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). Nessa topologia de rede, seu aplicativo pode se conectar a recursos locais, como outros recursos na rede virtual.

## <a name="application-secrets"></a>Segredos do aplicativo

Não armazene segredos do aplicativo, como credenciais de banco de dados, tokens de API e chaves privadas em seu código ou arquivos de configuração. A abordagem comumente aceita é acessá-las como [variáveis de ambiente](https://wikipedia.org/wiki/Environment_variable) usando o padrão padrão no idioma de sua escolha. No serviço de aplicativo, a maneira de definir variáveis de ambiente é por meio de [configurações de aplicativo](configure-common.md#configure-app-settings) (e, especialmente para aplicativos .net, [cadeias de conexão](configure-common.md#configure-connection-strings)). As configurações do aplicativo e as cadeias de conexão são armazenadas criptografadas no Azure e são descriptografadas somente antes de serem injetadas na memória do processo do aplicativo quando o aplicativo é iniciado. As chaves de criptografia são giradas regularmente.

Como alternativa, você pode integrar seu aplicativo do serviço de aplicativo com [Azure Key Vault](/azure/key-vault/) para o gerenciamento avançado de segredos. Ao [acessar o Key Vault com uma identidade gerenciada](../key-vault/tutorial-web-application-keyvault.md), seu aplicativo do serviço de aplicativo pode acessar com segurança os segredos de que você precisa.

## <a name="network-isolation"></a>Isolamento de rede

Exceto para o tipo de preço **isolado** , todas as camadas executam seus aplicativos na infraestrutura de rede compartilhada no serviço de aplicativo. Por exemplo, os endereços IP públicos e os balanceadores de carga de front-end são compartilhados com outros locatários. A camada **isolada** dá a você total isolamento de rede executando seus aplicativos dentro de um [ambiente de serviço de aplicativo](environment/intro.md)dedicado. Um ambiente do serviço de aplicativo é executado em sua própria instância da [rede virtual do Azure](/azure/virtual-network/). Ele permite que você: 

- Forneça seus aplicativos por meio de um ponto de extremidade público dedicado, com front-ends dedicados.
- Sirva o aplicativo interno usando um ILB (balanceador de carga interno), que permite o acesso somente de dentro de sua rede virtual do Azure. O ILB tem um endereço IP de sua sub-rede privada, que fornece isolamento total de seus aplicativos da Internet.
- [Use um ILB atrás de um WAF (firewall do aplicativo Web)](environment/integrate-with-application-gateway.md). O WAF oferece proteção de nível empresarial para seus aplicativos voltados para o público, como proteção contra DDoS, filtragem de URI e prevenção de injeção de SQL.

Para obter mais informações, consulte [introdução aos ambientes de serviço Azure app](environment/intro.md). 

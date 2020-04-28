---
title: Desenvolver uma aplicação Web Azure AD segura [ Microsoft Docs
description: Esta simples aplicação de amostra implementa as melhores práticas de segurança que melhoram a sua aplicação e a postura de segurança da sua organização quando se desenvolve no Azure.
keywords: na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 5b04bbbbe5425e65f3ed4ff82d9700dec6dd2c39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188332"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Desenvolver aplicativo seguro para uma aplicação Azure AD
## <a name="overview"></a>Descrição geral

Esta amostra é um simples Azure Ative Diretório com aplicação web que se liga a recursos de segurança para o desenvolvimento de aplicações no Azure. A aplicação implementa as melhores práticas de segurança que podem ajudar a melhorar a sua aplicação e a postura de segurança da sua organização quando desenvolve aplicações no Azure.

Os scripts de implantação configuram a infraestrutura. Depois de executar os scripts de implementação, terá de fazer alguma configuração manual no portal Azure para ligar os componentes e serviços em conjunto. Esta amostra é direcionada a desenvolvedores experientes no Azure que trabalham no sector retalhista e querem construir um Diretório Ativo Azure seguro com infraestruturas Azure seguras. 


Ao desenvolver e implementar esta aplicação, você vai aprender a 
- Crie uma instância azure key vault, guarde e recupere segredos dele.
- Implemente a Web App Azure, dedicada isolada com acesso frontal à firewall. 
- Crie e configure uma instância de Gateway de Aplicação Azure com uma firewall que utilize o Top 10 Ruleset oWASP. 
- Ativar a encriptação de dados em trânsito e em repouso utilizando os serviços Azure. 
- Criar o Azure Policy and Security Center para avaliar a conformidade. 

Depois de desenvolver e implementar esta aplicação, terá configurado a seguinte aplicação web de amostra, juntamente com as medidas de configuração e segurança descritas.

## <a name="architecture"></a>Arquitetura
A aplicação é uma aplicação típica de n-tier com três níveis. A parte frontal, a parte traseira e a camada de base de dados com componentes de monitorização e gestão secreta integrados são mostradas aqui:

![Arquitetura de aplicações](./media/secure-aad-app/architecture.png)

Esta solução utiliza os seguintes serviços Azure. Os detalhes da arquitetura de implantação estão na secção de Arquitetura de Implantação. 

A arquitetura é composta por estes componentes

- [Gateway de aplicação Azure](../../application-gateway/index.yml). Fornece o portal e firewall para a nossa arquitetura de aplicação.
- [Insights de aplicação](../../azure-monitor/app/app-insights-overview.md). Fornece um serviço extensível de Gestão de Desempenho de Aplicações (APM) em várias plataformas.
- [Cofre de Chaves Azure.](../../key-vault/index.yml) Armazena e encripta os segredos da nossa aplicação e gere a criação de políticas de acesso à sua volta.
- [Diretório Azure Ative](../../active-directory/fundamentals/active-directory-whatis.md). Fornece serviço de gestão de identidade e acesso baseado na nuvem, registo e acesso de recursos.
- [Sistema de nomede domínio azure](../../dns/dns-overview.md). Preste o serviço para hospedar o domínio.
- [Equilíbrio de carga azure](../../load-balancer/load-balancer-overview.md). Fornece para escalar as suas aplicações e criar uma elevada disponibilidade para os seus serviços.
- [Aplicação Web Azure.](../../app-service/overview.md)  Fornece um serviço baseado em HTTP para hospedar aplicações web.
- [Centro de Segurança Azure.](../../security-center/index.yml) fornece proteção avançada contra ameaças através das suas cargas de trabalho híbridas na nuvem.
- [Política Azure.](../../governance/policy/overview.md) Fornece avaliação dos seus recursos para o incumprimento das políticas atribuídas.

## <a name="threat-model"></a>Modelo de ameaça
A modelação de ameaças é o processo de identificação de potenciais ameaças à segurança para o seu negócio e aplicação e, em seguida, garantir que um plano de mitigação adequado está em vigor.

Esta amostra utilizou a Ferramenta de [Modelação de Ameaças](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) da Microsoft para implementar o modelamento de ameaças para a aplicação de amostra segura. Ao diagramar os componentes e os fluxos de dados, pode identificar problemas e ameaças no início do processo de desenvolvimento. O tempo e o dinheiro serão poupados mais tarde usando isto.

Aqui está o modelo de ameaça para a aplicação de amostra

![Modelo de ameaça](./media/secure-aad-app/threat-model.png)

Algumas ameaças de amostra e potenciais vulnerabilidades que a ferramenta de modelação de ameaças gera são mostradas na seguinte imagem. O modelo de ameaça dá uma visão geral da superfície de ataque exposta e leva os desenvolvedores a pensar em como mitigar os problemas.

![Saída do modelo de ameaça](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Pré-requisitos
Para que a aplicação esteja a funcionar, é necessário instalar estas ferramentas:

- Um editor de código para modificar e ver o código de aplicação. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código aberto.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) no seu computador de desenvolvimento.
- [Git](https://git-scm.com/) no seu sistema. Git é usado para clonar o código fonte localmente.
- [jq](https://stedolan.github.io/jq/), uma ferramenta UNIX para consulta jSON de uma forma amigável para o utilizador.

Precisa de uma subscrição Azure para implementar os recursos da aplicação de amostra. Se não tiver uma subscrição azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/) para testar a aplicação de amostras.

Depois de instalar estas ferramentas, está pronto para implementar a aplicação no Azure.

### <a name="implementation-guidance"></a>Orientação de implementação
O script de implantação é um script que pode ser dividido em quatro fases. Cada fase implanta e configura um recurso Azure que está no diagrama de [arquitetura.](#architecture)

As quatro fases são

- Implante o cofre da chave Azure.
- Implemente aplicações web azure.
- Implementar Gateway de aplicações com firewall de aplicação web.
- Configure um Anúncio Azure com app implantada.

Cada fase baseia-se na anterior utilizando a configuração dos recursos previamente implantados.

Para completar os passos de implementação, certifique-se de que instalou as ferramentas listadas em [pré-requisitos](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Implementar cofre de chave azure
Nesta secção, cria-se e implementa-se uma instância azure key vault que é usada para armazenar segredos e certificados.

Depois de completar a implantação, tem uma instância azure key vault implantada no Azure.

Para implantar o Cofre de Chaves Azure usando powershell
 
1. Declare as variáveis para o Cofre chave Azure.
2. Registe o fornecedor azure key vault.
3. Crie o grupo de recursos, por exemplo.
4. Crie a instância Azure Key Vault no grupo de recursos criado no passo 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>O utilizador da AD Azure abaixo terá permissões de administração para o Cofre chave
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Registe os Fornecedores Az
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Crie a instância do Cofre chave Azure
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Adicione as políticas do Administrador ao Cofre chave
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Para criar uma política de acesso para permitir que um utilizador obtenha e liste chaves, certificados e segredos criptográficos se souber o Nome Principal do Utilizador:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

É uma melhor prática usar identidades geridas para recursos Azure em apps que usam Key Vault para aceder a recursos. A sua postura de segurança aumenta quando as chaves de acesso ao Cofre chave não são armazenadas em código ou na configuração.

Um certificado de raiz está incluído no recipiente. As medidas tomadas para obter o certificado são

1. Faça o download do ficheiro de certificado da Autoridade de [Certificados.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)
2. Descodifique o seu ficheiro de certificado:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Este script cria uma identidade atribuída para a instância do Serviço de Aplicações que pode ser usada com MSI para interagir com o Cofre chave Azure sem segredos de codificação rígidos em código ou configuração.

Vá à instância do Cofre chave Azure no portal para autorizar a identidade atribuída no separador de política de acesso. **Selecione Adicionar nova política**de acesso . Em **'Selecionar principal',** procure o nome da aplicação semelhante ao nome da instância do Serviço de Aplicações criada.
Deve ser visível um diretor de serviço ligado à aplicação. Selecione-o e guarde a página da política de acesso, como mostra a seguinte imagem.

Como a aplicação só precisa de recuperar chaves, selecione a permissão **Obter** nas opções de segredos, permitindo o acesso reduzindo os privilégios concedidos.

![Política de acesso ao cofre chave](./media/secure-aad-app/kv-access-policy.png)

*Criar uma política de acesso ao Cofre Chave*

Guarde a política de acesso e, em seguida, guarde a nova alteração no separador Políticas de **Acesso** para atualizar as políticas.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Implementar gateway de aplicação com firewall de aplicação web ativado
Nas aplicações web, não é aconselhável expor serviços diretamente ao mundo exterior na internet.
As regras de equilíbrio de carga e firewall proporcionam mais segurança e controlo sobre o tráfego de entrada e ajudam-no a geri-lo.

Para implementar uma instância de Gateway de Aplicação

1. Crie o grupo de recursos para alojar o portal de aplicação.
2. Fornecer uma rede virtual para anexar ao portal.
3. Crie uma sub-rede para o portal na rede virtual.
4. Fornecer um endereço IP público.
5. Providenciar a porta de entrada de candidatura.
6. Ativar a firewall de aplicação web no portal.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Implementar aplicações web azure
O Azure App Service permite-lhe construir e hospedar aplicações web usando os idiomas python, Ruby, C#e Java. O Azure também suporta recipientes personalizados, que podem permitir que praticamente todos os idiomas de programação possam funcionar na plataforma Azure App Service.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Criar um plano de Serviço de Aplicações em nível gratuito
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Criar uma aplicação Web
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Antes de continuar, vá ao UI de configuração do seu Sistema https://aka.ms/appservicecustomdns de Nome de Domínio Azure para o seu domínio personalizado e siga as instruções para configurar um registo CNAME para o nome de anfitrião "www" e aponte-o para o nome de domínio padrão da sua aplicação web

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Plano de upgrade do Serviço de Aplicações para o nível Partilhado (mínimo exigido por domínios personalizados)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Adicione um nome de domínio personalizado à aplicação web
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="network"></a>Rede
Depois de concluir a implementação, tem um portal de aplicação com firewall de aplicação web ativado.

A instância de gateway expõe a porta 443 para HTTPS. Esta configuração garante que a nossa aplicação só está acessível na porta 443 através HTTPS.

Bloquear portas não utilizadas e limitar a exposição à superfície de ataque é uma boa prática de segurança.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Adicione grupos de segurança de rede à instância do Serviço de Aplicações

As instâncias do Serviço de Aplicações podem ser integradas com redes virtuais. Esta integração permite-lhes ser configurados com políticas de grupo de segurança de rede que gerem o tráfego de entrada e saída da app.

1. Para ativar esta funcionalidade, na lâmina de instância de instância do serviço Da Aplicação Azure, em **Definições,** seleciona **a rede**. No painel certo, configure sob **integração VNet**.

   ![Nova integração de rede virtual](./media/secure-web-app/app-vnet-menu.png)

    *Nova integração de rede virtual para o Serviço de Aplicações*
1. Na página seguinte, selecione **Adicionar VNET (pré-visualização)**.

1. No menu seguinte, selecione a rede virtual `aad-vnet`criada na implementação que começa com . Pode criar uma nova sub-rede ou selecionar uma existente.
   Neste caso, crie uma nova sub-rede. Detete a **gama 'Endereço'** para **10.0.3.0/24** e nomeie a **sub-sub-rede**de aplicações da sub-rede .

   ![Configuração de rede virtual do Serviço de Aplicações](./media/secure-web-app/app-vnet-config.png)

    *Configuração de rede virtual para serviço de aplicações*

Agora que permitiu a integração virtual da rede, pode adicionar grupos de segurança de rede à nossa aplicação.

1. Utilize a caixa de pesquisa, procure **grupos**de segurança de rede . Selecione **grupos de segurança da Rede** nos resultados.

    ![Pesquisa de grupos de segurança da rede](./media/secure-web-app/nsg-search-menu.png)

    *Pesquisa de grupos de segurança da rede*

2. No menu seguinte, selecione **Adicionar**. Insira o **nome** do NSG e do **grupo Derecursos** no qual deve ser localizado. Este NSG será aplicado à sub-rede do gateway da aplicação.

    ![Criar um NSG](./media/secure-web-app/nsg-create-new.png)

    *Criar um NSG*

3. Depois de criado o NSG, selecione-o. Na sua lâmina, em **Definições,** selecione regras de **segurança de entrada**. Configure estas definições para permitir que as ligações entrem no gateway da aplicação sobre a porta 443.

   ![Configure o NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Configure o NSG*

4. Nas regras de saída para o gateway NSG, adicione uma regra que permite ligações de saída à instância do Serviço de Aplicações, criando uma regra que visa a etiqueta de serviço`AppService`

   ![Adicione regras de saída para o NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Adicione regras de saída para o NSG*

    Adicione outra regra de saída para permitir que o portal envie regras de saída para uma rede virtual.

   ![Adicione outra regra de saída](./media/secure-web-app/nsg-outbound-vnet.png)

    *Adicione outra regra de saída*

5. Na lâmina das sub-redes do NSG, selecione **Associate,** selecione a rede virtual criada na implementação e selecione a subnet de gateway denominada **gw-subnet**. O NSG é aplicado à sub-rede.

6. Crie outro NSG como no passo anterior, desta vez para a instância do Serviço de Aplicações. Dá-lhe um nome. Adicione a regra de entrada para a porta 443 como fez para o gateway de aplicação NSG.

   Se tiver uma instância do Serviço de Aplicações implementada numa instância do App Service Environment, o que não é o caso desta aplicação, pode adicionar regras de entrada para permitir as sondas Azure Service Health abrindo portas 454-455 nos grupos de segurança de entrada do seu Serviço de Aplicações NSG. Aqui está a configuração:

   ![Adicione regras para sondas Azure Service Health](./media/secure-web-app/nsg-create-healthprobes.png)

    *Adicionar regras para sondas de saúde de serviço Azure (apenas App Service Environment)*

Para limitar a superfície de ataque, modifique as definições da rede do Serviço de Aplicações para permitir apenas o portal de aplicação para aceder à aplicação.
Para aplicar as definições, vá ao separador de rede do Serviço de Aplicações, selecionando o separador **de restrições IP** e criando uma regra de licenciamento que permite apenas o IP do gateway da aplicação aceder diretamente ao serviço. Pode recuperar o endereço IP do portal a partir da sua página geral. No **separador IP Address CIDR,** introduza `<GATEWAY_IP_ADDRESS>/32`o endereço IP neste formato: .

![Permitir apenas o portal](./media/secure-web-app/app-allow-gw-only.png)

*Permitir apenas o gateway IP para aceder ao Serviço de Aplicações*

### <a name="azure-domain-name-system"></a>Sistema de nomede domínio azure 
O Sistema de Nomede Domínio Azure, ou Sistema de Nome de Domínio Azure, é responsável pela tradução (ou resolução) de um website ou nome de serviço para o seu endereço IP. O Sistema dehttps://docs.microsoft.com/azure/dns/dns-overview) Nome de Domínio Azure é um serviço de hospedagem para domínios do Sistema de Nome de Domínio que fornece resolução de nome utilizando a infraestrutura Azure. Ao hospedar domínios no Azure, os utilizadores podem gerir os registos do Sistema de Nomede Domínio utilizando as mesmas credenciais, APIs, ferramentas e faturação como outros serviços Azure. O Sistema de Nomede Domínio Azure também suporta domínios privados do Sistema de Nomede Domínio.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
A encriptação do disco Azure aproveita a funcionalidade BitLocker do Windows para fornecer encriptação de volume para discos de dados. A solução integra-se com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de discos.

### <a name="identity-management"></a>Gestão de identidades
As seguintes tecnologias fornecem capacidades para gerir o acesso aos dados do titular do cartão no ambiente Azure
- O Azure Ative Directory é o serviço de diretório e gestão de identidade multi-inquilinos da Microsoft. Todos os utilizadores desta solução são criados no Diretório Ativo do Azure, incluindo utilizadores que acedam ao Azure WebApp.
- O controlo de acesso baseado em funções azure permite aos administradores definir permissões de acesso de grãos finos para conceder apenas a quantidade de acesso que os utilizadores precisam para desempenhar em seus trabalhos. Em vez de dar a cada utilizador permissão sem restrições para os recursos do Azure, os administradores só podem permitir certas ações de acesso aos dados dos titulares do cartão. O acesso por subscrição está limitado ao administrador de subscrição.
- A Azure Ative Directory Privileged Identity Management permite aos clientes minimizar o número de utilizadores que têm acesso a determinadas informações, como dados do titular do cartão. Os administradores podem usar a Azure Ative Directory Privileged Identity Management para descobrir, restringir e monitorizar identidades privilegiadas e o seu acesso aos recursos. Esta funcionalidade também pode ser utilizada para impor o acesso administrativo a pedido e just-in-time quando necessário.
- A Azure Ative Directory Identity Protection deteta potenciais vulnerabilidades que afetam as identidades de uma organização, configura respostas automatizadas a ações suspeitas detetadas relacionadas com as identidades de uma organização, e investiga incidentes suspeitos para tomar as medidas adequadas para resolvê-las.
### <a name="secrets-management"></a>Gestão de segredos
A solução utiliza o Cofre chave Azure para a gestão de chaves e segredos. O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. As seguintes capacidades do Cofre chave Azure ajudam os clientes a proteger e aceder a esses dados
   - As políticas avançadas de acesso são configuradas numa base de necessidade.
   - As políticas de acesso ao Cofre Chave são definidas com permissões mínimas necessárias a chaves e segredos.
   - Todas as chaves e segredos no Cofre Chave têm datas de validade.
   - Todas as chaves no Cofre chave estão protegidas por módulos de segurança de hardware especializados. O tipo chave é um módulo de segurança de hardware (HSM) Chave RSA protegida de 2048 bits.
   - Com o Key Vault, pode encriptar chaves e segredos (tais como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, . Ficheiros PFX e palavras-passe) utilizando chaves protegidas por módulos de segurança de hardware (HSMs). 
   - Utilize o Controlo de Acesso Baseado em Funções (RBAC) para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito.     
   - Utilize o Key Vault para gerir os seus certificados TLS com renovação automática. 
   - Os registos de diagnóstico do Cofre chave estão ativados com um período de retenção de pelo menos 365 dias.
   - As operações criptográficas permitidas para as teclas são restritas às necessárias.
### <a name="azure-security-center"></a>Centro de Segurança do Azure
Com o Azure Security Center, os clientes podem aplicar centralmente e gerir políticas de segurança através de cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Além disso, 
   - O Azure Security Center acede às configurações existentes dos serviços Azure para fornecer recomendações de configuração e serviço para ajudar a melhorar a postura de segurança e proteger os dados.
   - O Azure Security Center utiliza uma variedade de capacidades de deteção para alertar os clientes de potenciais ataques direcionados para os seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. O Azure Security Center tem um conjunto de alertas de segurança predefinidos, que são desencadeados quando ocorre uma ameaça, ou atividade suspeita. As regras de alerta personalizados no Azure Security Center permitem aos clientes definir novos alertas de segurança com base em dados que já são recolhidos do seu ambiente.
   - O Azure Security Center fornece alertas e incidentes de segurança prioritários, tornando mais simples para os clientes descobrir e resolver potenciais problemas de segurança. Um relatório de inteligência de ameaça é gerado por cada ameaça detetada para ajudar as equipas de resposta a incidentes na investigação e remediação de ameaças.
### <a name="azure-application-gateway"></a>Gateway de Aplicação do Azure 
   A arquitetura reduz o risco de vulnerabilidades de segurança usando um Portal de Aplicação Azure com uma firewall de aplicação web configurada, e o conjunto de regras OWASP ativado. Capacidades adicionais incluem
   - TLS de ponta a ponta.
   - Desative tLS v1.0 e v1.1.
   - Ativar TLSv1.2.
   - Firewall de aplicação web (modo de prevenção).
   - Modo de prevenção com definição de regra OWASP 3.0.
   - Ativar o registo de diagnósticos.
   - Sondas de saúde personalizadas.
   - O Azure Security Center e um Consultor Azure fornecem proteção e notificações adicionais. O Azure Security Center também fornece um sistema de reputação.
### <a name="logging-and-auditing"></a>Registo e auditoria
Os serviços Azure registam extensivamente o sistema de log e a atividade do utilizador, bem como a saúde do sistema:
   - Registos de atividade: [Os registos de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações realizadas sobre recursos numa subscrição. Os registos de atividade podem ajudar a determinar o iniciador de uma operação, a hora da ocorrência e o estado.
   - Registos de diagnóstico: [Os registos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) de diagnóstico incluem todos os registos emitidos por cada recurso. Estes registos incluem registos do sistema de eventos windows, registos de armazenamento azure, registos de auditoria key vault e acesso de Gateway de aplicação e registos de Firewall. Todos os registos de diagnóstico escrevem para uma conta de armazenamento Azure centralizada e encriptada para arquivo. A retenção é configurável pelo utilizador, até 730 dias, para satisfazer os requisitos de retenção específicos da organização.
### <a name="azure-monitor-logs"></a>Registos do Azure Monitor
   Estes registos são consolidados em [registos do Monitor Azure](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e reporte de dashboards. Uma vez recolhidos, os dados são organizados em tabelas separadas para cada tipo de dados dentro dos espaços de trabalho do Log Analytics, o que permite que todos os dados sejam analisados em conjunto, independentemente da sua origem original. Além disso, o Azure Security Center integra-se com os registos do Azure Monitor, permitindo aos clientes utilizarem consultas kusto para aceder em dados dos seus eventos de segurança e combiná-lo com dados de outros serviços.

   As [seguintes soluções](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) de monitorização azure estão incluídas como parte desta arquitetura

   - [Avaliação](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)do Diretório Ativo : A solução Ative Directory Health Check avalia o risco e a saúde dos ambientes dos servidores num intervalo regular e fornece uma lista prioritária de recomendações específicas à infraestrutura de servidores implementadas.
   - [Agente Saúde](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução Agent Health informa quantos agentes são destacados e a sua distribuição geográfica, bem como quantos agentes, que não respondem e o número de agentes, que estão a enviar dados operacionais.
   - [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Activity Log Analytics ajuda na análise dos registos de atividade do Azure em todas as subscrições do Azure para um cliente.
### <a name="azure-monitor"></a>Azure Monitor
   [O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)ajuda os utilizadores a rastrear o desempenho, a manter a segurança e a identificar tendências, permitindo às organizações auditar, criar alertas e arquivar dados, incluindo rastrear chamadas de API nos seus recursos Azure.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço extensível de Gestão de Desempenho de Aplicações para desenvolvedores web em várias plataformas. Aplicação Insights deteta anomalias de desempenho e os clientes podem usá-lo para monitorizar a aplicação web ao vivo. Inclui ferramentas de análise poderosas para ajudar os clientes a diagnosticar problemas e a entender o que os utilizadores realmente fazem com a sua aplicação. É projetado para ajudar os clientes a melhorar continuamente o desempenho e a usabilidade.

### <a name="azure-key-vault"></a>Azure Key Vault
   Crie um cofre para a organização em que armazenar chaves, e manter a responsabilidade por tarefas operacionais como abaixo

   - Dados armazenados em Cofre chave inclui   
   - Chave de informação de aplicação
   - Chave de acesso ao armazenamento de dados
   - Cadeia de ligação
   - Nome da tabela de dados
   - Credenciais de utilizador
   - As políticas avançadas de acesso são configuradas numa base de necessidade
   - As políticas de acesso ao Cofre Chave são definidas com permissões mínimas necessárias a chaves e segredos
   - Todas as chaves e segredos no Cofre Chave têm datas de validade
   - Todas as teclas no Cofre chave estão protegidas por módulo de segurança de hardware (HSM) [Tipo chave = módulo de segurança de hardware (HSM) Protegido       
     Chave RSA de 2048]
   - Todos os utilizadores/identidades recebem permissões mínimas necessárias utilizando o Controlo de Acesso Baseado em Funções (RBAC)
   - As aplicações não partilham um Cofre chave a menos que confiem uns nos outros e precisam de acesso aos mesmos segredos em tempo de execução
   - Os registos de diagnóstico do Cofre chave estão ativados com um período de retenção de pelo menos 365 dias.
   - As operações criptográficas permitidas para as chaves são restritas às necessárias

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
   Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisava de ser configurado estabelecendo de forma segura uma ligação aos recursos implantados como parte desta arquitetura de referência de aplicação web PaaS. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

   Através da implementação de um túnel VPN seguro com o Azure, pode ser criada uma ligação privada virtual entre uma rede no local e uma Rede Virtual Azure. Esta ligação ocorre através da Internet e permite que os clientes "túneis" de forma segura dentro de uma ligação encriptada entre a rede do cliente e o Azure. O Site-to-Site VPN é uma tecnologia segura e madura que tem sido implementada por empresas de todos os tamanhos durante décadas. O modo de túnel IPsec é usado nesta opção como um mecanismo de encriptação.

   Uma vez que o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site-to-site, a Microsoft oferece outra opção de ligação ainda mais segura. Azure ExpressRoute é uma ligação WAN dedicada entre o Azure e um local no local ou um fornecedor de hospedagem exchange. Como as ligações ExpressRoute não passam pela Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações típicas através da Internet. Além disso, por se trata de uma ligação direta do fornecedor de telecomunicações do cliente, os dados não viajam pela Internet e, por conseguinte, não estão expostos a ele.

   Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)as melhores práticas para a implementação de uma rede híbrida segura que alarga uma rede no local ao Azure.

#### <a name="implement-azure-active-directory-oidc"></a>Implementar diretório ativo Azure OIDC

1. Para clonar o repositório de código fonte, use este comando Git

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Atualizar os URLs redireccionadores
1.  Navegue de volta ao portal Azure. No painel de navegação à esquerda, selecione o serviço de Diretório Ativo Azure e, em seguida, selecione os registos da App.
2.  No ecrã resultante, selecione a aplicação WebApp-OpenIDConnect-DotNet-código-v2.
3.  No separador autenticação o Na secção REdirecionamento de URIs, selecione Web na caixa de combinação e adicione os seguintes URIs redirecionais.
    `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net`
    `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc`
    o Na secção de definições avançadas definir URL de logout para`https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc`
4.  No separador Branding o Atualize o URL da página `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net`inicial para o endereço do seu serviço de aplicações, por exemplo .
        o Guardar a configuração.
5.  Se a sua aplicação chamar um api web, certifique-se de aplicar as alterações necessárias nas aplicações do projeto.json, pelo que chama o URL aPI publicado em vez de local de acolhimento.
Publicação da amostra
    1.  A partir do separador Overview do Serviço de Aplicações, descarregue o perfil de publicação clicando no link de perfil Get publish e guarde-o. Outros mecanismos de implantação, como o controlo de origem, também podem ser utilizados.
    2.  Mude para Visual Studio e vá ao projeto WebApp-OpenIDConnect-DotNet-code-v2. Clique no projeto no Solution Explorer e selecione Publicar. Clique no Perfil de Importação na barra inferior e importe o perfil de publicação que descarregou anteriormente.
    3.  Clique em Configure e no separador Ligação, atualize o URL de Destino `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net`de modo a que seja um https no url da página inicial, por exemplo . Clique em Seguinte.
    4.  No separador Definições, certifique-se de que a autenticação organizacional ativa não está selecionada. Clique em Guardar. Clique em Publicar no ecrã principal.
    5.  O Visual Studio publicará o projeto e abrirá automaticamente um navegador para o URL do projeto. Se vir a página web padrão do projeto, a publicação foi bem sucedida.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementar autenticação multi-factor para diretório ativo azure
   Os administradores têm de garantir que as contas de subscrição no portal estão protegidas. A subscrição é vulnerável a ataques porque gere os recursos que criou. Para proteger a subscrição, ative a autenticação multi-factor no separador De **Diretório Ativo Azure** da subscrição.

   Um Azure AD opera com base em políticas que são aplicadas a um utilizador ou grupos de utilizadores que se enquadram em determinados critérios.
O Azure cria uma política predefinida especificando que os administradores precisam de autenticação de dois fatores para iniciar sessão no portal.
Depois de ativar esta política, pode ser solicitado a assinar e a assinar de volta para o portal Azure.

Para permitir o MFA para inscrições de administração

   1. Vá ao separador **Azure Ative Diretório** no portal Azure
   2. Na categoria de segurança, selecione acesso condicional. Você vê este ecrã

       ![Acesso Condicional - Políticas](./media/secure-aad-app/ad-mfa-conditional-add.png)

Se não pode criar uma nova política

   1. Vá ao separador **MFA.**
   2. Selecione um link de **teste Azure** AD Premium Free para subscrever o teste gratuito.

   ![Um teste gratuito Azure AD Premium](./media/secure-aad-app/ad-trial-premium.png)

Volte ao ecrã de acesso condicional.

   1. Selecione o novo separador de política.
   2. Introduza o nome da política.
   3. Selecione os utilizadores ou grupos para os quais pretende ativar o MFA.
   4. Sob **os controlos de Acesso,** selecione o separador **Grant** e, em seguida, selecione **Exigir autenticação de vários fatores** (e outras definições se desejar).

   ![Requerer MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Pode ativar a apólice selecionando a caixa de verificação na parte superior do ecrã ou fazê-lo no separador **Acesso Condicional.** Quando a política está ativada, os utilizadores precisam de MFA para iniciar sessão no portal.

   Há uma política de base que requer MFA para todos os administradores do Azure. Pode permitir imediatamente no portal. Permitir esta política pode invalidar a sessão atual e forçá-lo a assinar novamente.

   Se a política de base não estiver ativada
   1.   Selecione **Exigir MFA para administradores**.
   2.   Selecione **a política de utilização imediatamente**.

   ![Selecione a política de utilização imediatamente](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Use o Azure Sentinel para monitorizar apps e recursos

   À medida que uma aplicação cresce, torna-se difícil agregar todos os sinais e métricas de segurança recebidos dos recursos e torná-los úteis de forma orientada para a ação.

   O Azure Sentinel foi concebido para recolher dados, detetar os tipos de ameaças possíveis e fornecer visibilidade em incidentes de segurança.
Enquanto aguarda a intervenção manual, o Azure Sentinel pode contar com playbooks pré-escritos para dar início a alertas e processos de gestão de incidentes.

   A aplicação de amostras é composta por vários recursos que o Azure Sentinel pode monitorizar.
Para configurar o Azure Sentinel, é necessário criar primeiro um espaço de trabalho log Analytics que armazene todos os dados recolhidos a partir dos vários recursos.

Para criar este espaço de trabalho

   1. Na caixa de pesquisa no portal Azure, procure o **Log Analytics.** Selecione espaços de **trabalho Log Analytics**.

   ![Pesquisa de espaços de trabalho de Log Analytics](./media/secure-aad-app/sentinel-log-analytics.png)

   *Pesquisa de espaços de trabalho de Log Analytics*

   2. Na página seguinte, selecione **Adicionar** e, em seguida, fornecer um nome, grupo de recursos e localização para o espaço de trabalho.
   ![Criar uma área de trabalho do Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Criar uma área de trabalho do Log Analytics*

   3. Utilize a caixa de pesquisa para procurar **o Azure Sentinel**.

   ![Procurar o Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Procurar o Azure Sentinel*

   4. Selecione **Adicionar** e, em seguida, selecione o espaço de trabalho log Analytics que criou anteriormente.

   ![Adicione um espaço de trabalho log Analytics](./media/secure-aad-app/sentinel-workspace-add.png)

   *Adicione um espaço de trabalho log Analytics*

   5. Na página **Azure Sentinel - Conectores** de dados, sob **Configuração,** selecione **conectores**de dados . Você vê uma variedade de serviços Azure que você pode ligar à instância de armazenamento Log Analytics para análise em Azure Sentinel.

   ![Conectores de dados de Log Analytics](./media/secure-aad-app/sentinel-connectors.png)

      *Adicione um conector de dados ao Azure Sentinel*

   Por exemplo, para ligar o gateway da aplicação, tome estas medidas:

   1. Abra a lâmina de instância de gateway de aplicação Azure.
   2. Em **Monitorização**, selecione **Definições de diagnóstico**.
   3. **Selecione Adicionar definição de diagnóstico**.

   ![Adicionar diagnósticos de Gateway de aplicação](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Adicionar diagnósticos de Gateway de aplicação*

   4. Na página de definições de **Diagnóstico,** selecione o espaço de trabalho do Log Analytics que criou e, em seguida, selecione todas as métricas que pretende recolher e enviar para o Azure Sentinel. Selecione **Guardar**.

   ![Definições do conector Azure Sentinel](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Considerações de custos
   Se ainda não tem uma conta Azure, pode criar uma gratuita. Vá à página de [conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que pode fazer com uma conta Azure gratuita e saiba quais os produtos gratuitos durante 12 meses.

   Para implementar os recursos na aplicação de amostracom as funcionalidades de segurança, precisa de pagar algumas funcionalidades premium. À medida que as escalas de aplicações e os níveis e testes gratuitos oferecidos pelo Azure precisam de ser atualizados para satisfazer os requisitos de aplicação, os seus custos podem aumentar. Utilize a [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços Azure para estimar os seus custos.

## <a name="next-steps"></a>Passos seguintes
   Os seguintes artigos podem ajudá-lo a projetar, desenvolver e implementar aplicações seguras.

- [Design](secure-design.md)
- [Programar](secure-develop.md)
- [Implementar](secure-deploy.md)

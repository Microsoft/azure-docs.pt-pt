---
title: Desenvolver um aplicativo Web seguro do Azure AD | Microsoft Docs
description: Este aplicativo de exemplo simples implementa práticas recomendadas de segurança que melhoram seu aplicativo e a postura de segurança de sua organização quando você desenvolve no Azure.
keywords: nd
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
ms.openlocfilehash: a936fb4a0a6eadc2840fc6d642428091a6b0fe9e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771279"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Desenvolver um aplicativo seguro para um aplicativo do Azure AD
## <a name="overview"></a>Visão geral

Este exemplo é um Azure Active Directory simples com o aplicativo Web que é vinculado a recursos de segurança para o desenvolvimento de aplicativos no Azure. O aplicativo implementa práticas recomendadas de segurança que podem ajudar a melhorar seu aplicativo e a postura de segurança da sua organização ao desenvolver aplicativos no Azure.

Os scripts de implantação configuram a infraestrutura. Depois de executar os scripts de implantação, você precisará fazer alguma configuração manual no portal do Azure para vincular os componentes e serviços juntos. Este exemplo destina-se a desenvolvedores experientes no Azure que trabalham no setor de varejo e desejam criar um Azure Active Directory seguro com a infraestrutura segura do Azure. 


Ao desenvolver e implantar este aplicativo, você aprenderá a 
- Crie um Azure Key Vault instância, armazene e recupere segredos dele.
- Implante o aplicativo Web do Azure, que é dedicado isolado com acesso de firewall de front-end. 
- Crie e configure uma instância de gateway Aplicativo Azure com um firewall que usa o OWASP 10 principais RuleSet. 
- Habilite a criptografia de dados em trânsito e em repouso usando os serviços do Azure. 
- Configure a central de segurança e a política do Azure para avaliar o compliancies. 

Depois de desenvolver e implantar esse aplicativo, você terá configurado o exemplo de aplicativo Web a seguir junto com as medidas de configuração e segurança descritas.

## <a name="architecture"></a>Arquitetura
O aplicativo é um aplicativo típico de n camadas com três camadas. O front-end, back-end e a camada de banco de dados com componentes de monitoramento e gerenciamento de segredo integrados são mostrados aqui:

![Arquitetura de aplicações](./media/secure-aad-app/architecture.png)

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão na seção arquitetura de implantação. 

A arquitetura consiste nesses componentes

- [Aplicativo Azure gateway](../../application-gateway/index.yml). Fornece o gateway e o firewall para nossa arquitetura de aplicativo.
- [Application insights](../../azure-monitor/app/app-insights-overview.md). Fornece um serviço de gerenciamento de desempenho de aplicativos (APM) extensível em várias plataformas.
- [Azure Key Vault](../../key-vault/index.yml). Armazena e criptografa os segredos de nosso aplicativo e gerencia a criação de políticas de acesso em relação a eles.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Fornece identidade baseada em nuvem e serviço de gerenciamento de acesso, entrada e acesso a recursos.
- [Sistema de nomes de domínio do Azure](../../dns/dns-overview.md). Forneça o serviço para hospedar o domínio.
- [Balanceador de Carga do Azure](../../load-balancer/load-balancer-overview.md). Fornece para dimensionar seus aplicativos e criar alta disponibilidade para seus serviços.
- [Aplicação Web do Azure](../../app-service/overview.md).  Fornece um serviço baseado em HTTP para hospedar aplicativos Web.
- [Centro de Segurança do Azure](../../security-center/index.yml). fornece proteção avançada contra ameaças em suas cargas de trabalho híbridas na nuvem.
- [Azure Policy](../../governance/policy/overview.md). Fornece avaliação de seus recursos para não conformidade com políticas atribuídas.

## <a name="threat-model"></a>Modelo de ameaça
A modelagem de ameaças é o processo de identificar possíveis ameaças à segurança para seus negócios e aplicativos e, em seguida, garantir que um plano de mitigação adequado esteja em vigor.

Este exemplo usou o [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) para implementar a modelagem de ameaças para o aplicativo de exemplo seguro. Ao diagramar os componentes e os fluxos de dados, você pode identificar problemas e ameaças no início do processo de desenvolvimento. O tempo e o dinheiro serão salvos mais tarde usando isso.

Este é o modelo de risco para o aplicativo de exemplo

![Modelo de ameaça](./media/secure-aad-app/threat-model.png)

Algumas ameaças de exemplo e possíveis vulnerabilidades que a ferramenta de modelagem de ameaças gera são mostradas na captura de tela a seguir. O modelo de ameaça fornece uma visão geral da superfície de ataque exposta e solicita que os desenvolvedores pensem sobre como mitigar os problemas.

![Saída do modelo de ameaça](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Pré-requisitos
Para colocar o aplicativo em funcionamento, você precisa instalar essas ferramentas:

- Um editor de código para modificar e exibir o código do aplicativo. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código-fonte aberto.
- [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) em seu computador de desenvolvimento.
- [Git](https://git-scm.com/) em seu sistema. O Git é usado para clonar o código-fonte localmente.
- [JQ](https://stedolan.github.io/jq/), uma ferramenta UNIX para consultar o JSON de maneira amigável.

Você precisa de uma assinatura do Azure para implantar os recursos do aplicativo de exemplo. Se você não tiver uma assinatura do Azure, poderá [criar uma conta gratuita](https://azure.microsoft.com/free/) para testar o aplicativo de exemplo.

Depois de instalar essas ferramentas, você estará pronto para implantar o aplicativo no Azure.

### <a name="implementation-guidance"></a>Orientações de implementação
O script de implantação é um script que pode ser dividido em quatro fases. Cada fase implanta e configura um recurso do Azure que está no [diagrama da arquitetura](#architecture).

As quatro fases são

- Implantar Azure Key Vault.
- Implantar aplicativos Web do Azure.
- Implante o gateway de aplicativo com o Firewall do aplicativo Web.
- Configure um Azure AD com o aplicativo implantado.

Cada fase se baseia na anterior, usando a configuração dos recursos implantados anteriormente.

Para concluir as etapas de implementação, verifique se você instalou as ferramentas listadas em [pré-requisitos](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Implantar Azure Key Vault
Nesta seção, você cria e implanta uma instância de Azure Key Vault que é usada para armazenar segredos e certificados.

Depois de concluir a implantação, você terá uma instância Azure Key Vault implantada no Azure.

Para implantar Azure Key Vault usando o PowerShell
 
1. Declare as variáveis para Azure Key Vault.
2. Registre o provedor de Azure Key Vault.
3. Crie o grupo de recursos para a instância.
4. Crie a instância de Azure Key Vault no grupo de recursos criado na etapa 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>O usuário do Azure AD abaixo terá permissões de administrador para o Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Registrar os provedores AZ
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Criar a instância de Azure Key Vault
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Adicionar as políticas de administrador ao Key Vault
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Para criar uma política de acesso para permitir que um usuário obtenha e liste chaves criptográficas, certificados e segredos se você souber o nome principal do usuário:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

É uma prática recomendada usar identidades gerenciadas para recursos do Azure em aplicativos que usam Key Vault para acessar recursos. Sua postura de segurança aumenta quando as chaves de acesso para Key Vault não são armazenadas no código ou na configuração.

Um certificado raiz está incluído no contêiner. As etapas usadas para obter o certificado são

1. Baixe o arquivo de certificado da [autoridade de certificação](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Decodifique seu arquivo de certificado:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Esse script cria uma identidade atribuída para a instância do serviço de aplicativo que pode ser usada com o MSI para interagir com Azure Key Vault sem segredos de codificação rígida no código ou na configuração.

Vá para a instância de Azure Key Vault no portal para autorizar a identidade atribuída na guia política de acesso. Selecione **Adicionar nova política de acesso**. Em **selecionar entidade de segurança**, procure o nome do aplicativo que é semelhante ao nome da instância do serviço de aplicativo criada.
Uma entidade de serviço anexada ao aplicativo deve estar visível. Selecione-a e salve a página política de acesso, conforme mostrado na captura de tela a seguir.

Como o aplicativo precisa apenas recuperar as chaves, selecione a permissão **obter** nas opções de segredos, permitindo o acesso enquanto reduz os privilégios concedidos.

![Política de acesso de Key Vault](./media/secure-aad-app/kv-access-policy.png)

*Criar uma política de acesso de Key Vault*

Salve a política de acesso e salve a nova alteração na guia **políticas de acesso** para atualizar as políticas.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Implantar o gateway de aplicativo com o Firewall do aplicativo Web habilitado
Em aplicativos Web, não é recomendável que você exponha serviços diretamente para o mundo exterior na Internet.
O balanceamento de carga e as regras de firewall fornecem mais segurança e controle sobre o tráfego de entrada e o ajudam a gerenciá-lo.

Para implantar uma instância do gateway de aplicativo

1. Crie o grupo de recursos para alojar o gateway de aplicativo.
2. Provisione uma rede virtual para anexar ao gateway.
3. Crie uma sub-rede para o gateway na rede virtual.
4. Provisionar um endereço IP público.
5. Provisione o gateway de aplicativo.
6. Habilite o Firewall do aplicativo Web no gateway.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.0.0/24

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

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.0.0

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 

#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Implantar aplicativos Web do Azure
Azure App serviço permite que você crie e hospede aplicativos Web usando as linguagens como Python, Ruby C#, e Java. O Azure também dá suporte a contêineres personalizados, que podem permitir que praticamente todas as linguagens de programação sejam executadas na plataforma de serviço de Azure App.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Criar um plano do serviço de aplicativo na camada gratuita
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Criar uma aplicação Web
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Antes de continuar, vá para a interface do usuário de configuração do sistema de nome de domínio do Azure para seu domínio personalizado e siga as instruções em https://aka.ms/appservicecustomdns para configurar um registro CNAME para o nome de host "www" e apontar para o seu padrão de domínio do aplicativo Web

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Atualizar o plano do serviço de aplicativo para a camada compartilhada (mínimo exigido pelos domínios personalizados)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Adicionar um nome de domínio personalizado ao aplicativo Web
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="network"></a>Rede
Depois de concluir a implantação, você terá um gateway de aplicativo com o Firewall do aplicativo Web habilitado.

A instância de gateway expõe a porta 443 para HTTPS. Essa configuração garante que nosso aplicativo só esteja acessível na porta 443 por HTTPS.

Bloquear portas não utilizadas e limitar a exposição da superfície de ataque é uma prática recomendada de segurança.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Adicionar grupos de segurança de rede à instância do serviço de aplicativo

As instâncias do serviço de aplicativo podem ser integradas com redes virtuais. Essa integração permite que eles sejam configurados com políticas de grupo de segurança de rede que gerenciam o tráfego de entrada e saída do aplicativo.

1. Para habilitar esse recurso, na folha instância do serviço Azure App, em **configurações**, seleciona **rede**. No painel direito, configure em **integração VNet**.

   ![Nova integração de rede virtual](./media/secure-web-app/app-vnet-menu.png)

    *Nova integração de rede virtual para o serviço de aplicativo*
1. Na página seguinte, selecione **Adicionar VNET (versão prévia)** .

1. No menu avançar, selecione a rede virtual criada na implantação que começa com `aad-vnet`. Você pode criar uma nova sub-rede ou selecionar uma existente.
   Nesse caso, crie uma nova sub-rede. Defina o **intervalo de endereços** como **10.0.3.0/24** e nomeie a sub-rede **app-subnet**.

   ![Configuração de rede virtual do serviço de aplicativo](./media/secure-web-app/app-vnet-config.png)

    *Configuração de rede virtual para o serviço de aplicativo*

Agora que você habilitou a integração de rede virtual, você pode adicionar grupos de segurança de rede ao nosso aplicativo.

1. Use a caixa de pesquisa, procure **grupos de segurança de rede**. Selecione **grupos de segurança de rede** nos resultados.

    ![Pesquisar grupos de segurança de rede](./media/secure-web-app/nsg-search-menu.png)

    *Pesquisar grupos de segurança de rede*

2. No menu avançar, selecione **Adicionar**. Insira o **nome** do NSG e o **grupo de recursos** no qual ele deve ser localizado. Este NSG será aplicado à sub-rede do gateway de aplicativo.

    ![Criar um NSG](./media/secure-web-app/nsg-create-new.png)

    *Criar um NSG*

3. Depois que o NSG for criado, selecione-o. Em sua folha, em **configurações**, selecione **regras de segurança de entrada**. Defina essas configurações para permitir conexões que entram no gateway de aplicativo pela porta 443.

   ![Configurar o NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Configurar o NSG*

4. Nas regras de saída para o NSG do gateway, adicione uma regra que permita conexões de saída à instância do serviço de aplicativo Criando uma regra que tenha como alvo a marca de serviço `AppService`

   ![Adicionar regras de saída para o NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Adicionar regras de saída para o NSG*

    Adicione outra regra de saída para permitir que o gateway envie regras de saída para uma rede virtual.

   ![Adicionar outra regra de saída](./media/secure-web-app/nsg-outbound-vnet.png)

    *Adicionar outra regra de saída*

5. Na folha sub-redes do NSG, selecione **associar**, selecione a rede virtual criada na implantação e selecione a sub-rede de gateway denominada **GW-subnet**. O NSG é aplicado à sub-rede.

6. Crie outro NSG como na etapa anterior, desta vez para a instância do serviço de aplicativo. Dê um nome a ela. Adicione a regra de entrada para a porta 443, como fez para o NSG do gateway de aplicativo.

   Se você tiver uma instância do serviço de aplicativo implantada em uma instância do Ambiente do Serviço de Aplicativo, que não é o caso desse aplicativo, você poderá adicionar regras de entrada para permitir investigações de integridade do serviço do Azure abrindo as portas 454-455 nos grupos de segurança de entrada de seu serviço de aplicativo NSG. Aqui está a configuração:

   ![Adicionar regras para investigações de integridade do serviço do Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Adicionar regras para investigações de integridade do serviço do Azure (somente Ambiente do Serviço de Aplicativo)*

Para limitar a superfície de ataque, modifique as configurações de rede do serviço de aplicativo para permitir que apenas o gateway de aplicativo acesse o aplicativo.
Para aplicar as configurações, vá para a guia rede do serviço de aplicativo, selecione a guia **restrições de IP** e crie uma regra de permissão que permita que apenas o IP do gateway de aplicativo acesse o serviço diretamente. Você pode recuperar o endereço IP do gateway de sua página de visão geral. Na guia **CIDR endereço IP** , insira o endereço IP neste formato: `<GATEWAY_IP_ADDRESS>/32`.

![Permitir somente o gateway](./media/secure-web-app/app-allow-gw-only.png)

*Permitir que somente o IP do gateway acesse o serviço de aplicativo*

### <a name="azure-domain-name-system"></a>Sistema de nomes de domínio do Azure 
O sistema de nomes de domínio do Azure ou o sistema de nome de domínio do Azure é responsável por converter (ou resolver) um site ou nome de serviço para seu endereço IP. O sistema de nomes de domínio do Azure (https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios do sistema de nomes de domínio que fornece resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar registros do sistema de nomes de domínio usando as mesmas credenciais, APIs, ferramentas e cobrança que outros serviços do Azure. O sistema de nomes de domínio do Azure também dá suporte a domínios de sistema de nome de domínio privado

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

### <a name="identity-management"></a>Gestão de identidades
As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados do titular do cartão no ambiente do Azure
- Azure Active Directory é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários desta solução são criados no Azure Active Directory, incluindo usuários que acessam o Azure WebApp.
- O controle de acesso baseado em função do Azure permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissão irrestrita para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados de titulares de cartão. O acesso à assinatura é limitado ao administrador da assinatura.
- Azure Active Directory Privileged Identity Management permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações, como dados de titulares de cartão. Os administradores podem usar Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- Azure Active Directory Identity Protection detecta possíveis vulnerabilidades que afetam as identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização e investiga suspeitas incidentes para executar a ação apropriada para resolvê-los.
### <a name="secrets-management"></a>Gerenciamento de segredos
A solução usa Azure Key Vault para o gerenciamento de chaves e segredos. O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Os recursos de Azure Key Vault a seguir ajudam os clientes a proteger e acessar esses dados
   - As políticas de acesso avançadas são configuradas de acordo com a necessidade.
   - Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
   - Todas as chaves e segredos em Key Vault têm datas de expiração.
   - Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida por HSM (módulo de segurança de hardware).
   - Com o Key Vault, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados,. Arquivos PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware). 
   - Use o RBAC (controle de acesso baseado em função) para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo.     
   - Use Key Vault para gerenciar seus certificados TLS com a renovação automática. 
   - Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
   - As operações de criptografia permitidas para chaves são restritas às necessárias.
### <a name="azure-security-center"></a>Centro de Segurança do Azure
Com a central de segurança do Azure, os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. Além disso 
   - A central de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a postura de segurança e proteger os dados.
   - A central de segurança do Azure usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques visando seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança do Azure tem um conjunto de alertas de segurança predefinidos, que são disparados quando ocorre uma ameaça ou atividade suspeita. As regras de alerta personalizadas na central de segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.
   - A central de segurança do Azure fornece alertas e incidentes de segurança priorizados, tornando mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um relatório de inteligência contra ameaças é gerado para cada ameaça detectada para ajudar as equipes de resposta a incidentes na investigação e na correção de ameaças.
### <a name="azure-application-gateway"></a>Gateway de Aplicação do Azure 
   A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de Aplicativo Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Recursos adicionais incluem
   - SSL de ponta a ponta.
   - Desabilite o TLS v 1.0 e o v 1.1.
   - Habilite o TLSv 1.2.
   - Firewall do aplicativo Web (modo de prevenção).
   - Modo de prevenção com conjunto de regras OWASP 3,0.
   - Habilite o log de diagnóstico.
   - Investigações de integridade personalizadas.
   - A central de segurança do Azure e um consultor do Azure fornecem proteção e notificações adicionais. A central de segurança do Azure também fornece um sistema de reputação.
### <a name="logging-and-auditing"></a>Registo e auditoria
Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
   - Logs de atividade: [os logs de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
   - Logs de diagnóstico: os [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, até 730 dias, para atender aos requisitos de retenção específicos da organização.
### <a name="azure-monitor-logs"></a>Registos do Azure Monitor
   Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados dentro de Log Analytics espaços de trabalho, o que permite que todos os dados sejam analisados juntos, independentemente de sua fonte original. Além disso, a central de segurança do Azure integra-se aos logs de Azure Monitor, permitindo que os clientes usem consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

   As soluções de [monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) do Azure a seguir estão incluídas como parte dessa arquitetura

   - [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): a solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
   - [Integridade do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): a solução integridade do agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes, que não respondem e o número de agentes, que estão enviando dados operacionais.
   - [Análise do log de atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): a solução de análise do log de atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.
### <a name="azure-monitor"></a>Monitor do Azure
   [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o acompanhamento de chamadas de API em seus recursos do Azure.
### <a name="application-insights"></a>Estatísticas das Aplicações 
   O [Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gerenciamento de desempenho de aplicativos extensível para desenvolvedores da Web em várias plataformas. Application Insights detecta anomalias de desempenho e os clientes podem usá-lo para monitorar o aplicativo Web em tempo real. Ele inclui ferramentas de análise poderosas para ajudar os clientes a diagnosticar problemas e entender o que os usuários realmente fazem com seus aplicativos. Ele foi projetado para ajudar os clientes a melhorar continuamente o desempenho e a usabilidade.

### <a name="azure-key-vault"></a>Azure Key Vault
   Criar um cofre para a organização na qual armazenar chaves e manter a responsabilidade de tarefas operacionais, como a seguir

   - Os dados armazenados no Key Vault incluem   
   - Chave do Application insights
   - Chave de acesso de armazenamento de dados
   - Cadeia de ligação
   - Nome da tabela de dados
   - Credenciais de Utilizador
   - As políticas de acesso avançadas são configuradas de acordo com a necessidade
   - Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos
   - Todas as chaves e segredos no Key Vault têm datas de expiração
   - Todas as chaves em Key Vault são protegidas pelo módulo de segurança de hardware (HSM) [tipo de chave = módulo de segurança de hardware (HSM) protegido       
     Chave RSA de 2048 bits]
   - Todos os usuários/identidades recebem as permissões mínimas necessárias usando o RBAC (controle de acesso baseado em função)
   - Os aplicativos não compartilham um Key Vault, a menos que eles confiem um do outro e precisem acessar os mesmos segredos no tempo de execução
   - Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
   - As operações de criptografia permitidas para chaves são restritas às necessárias

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
   Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisavam ser configurados, estabelecendo com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência do aplicativo Web PaaS. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

   Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão ocorre pela Internet e permite aos clientes "encapsular" informações com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O modo de encapsulamento IPsec é usado nessa opção como um mecanismo de criptografia.

   Como o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e mais segurança do que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicação do cliente, os dados não viajam pela Internet e, portanto, não são expostos a ele.

   As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

#### <a name="implement-azure-active-directory-oidc"></a>Implementar Azure Active Directory OIDC

1. Para clonar o repositório de código-fonte, use este comando git

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Atualizar as URLs de redirecionamento
1.  Navegue de volta para a portal do Azure. No painel de navegação à esquerda, selecione o serviço Azure Active Directory e, em seguida, selecione Registros de aplicativo.
2.  Na tela resultante, selecione o aplicativo WebApp-OpenIDConnect-DotNet-Code-v2.
3.  Na guia Autenticação o na seção URIs de redirecionamento, selecione Web na caixa de combinação e adicione os seguintes URIs de redirecionamento.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o na seção Configurações avançadas definir URL de logout para https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  Na guia identidade visual o, atualize a URL da Home Page para o endereço do seu serviço de aplicativo, por exemplo https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net.
        o salve a configuração.
5.  Se seu aplicativo chamar uma API da Web, certifique-se de aplicar as alterações necessárias no projeto appSettings. JSON, para que chame a URL da API publicada em vez do localhost.
Publicando o exemplo
    1.  Na guia Visão geral do serviço de aplicativo, baixe o perfil de publicação clicando no link obter perfil de publicação e salve-o. Outros mecanismos de implantação, como do controle do código-fonte, também podem ser usados.
    2.  Alterne para o Visual Studio e vá para o projeto WebApp-OpenIDConnect-DotNet-Code-v2. Clique com o botão direito do mouse no projeto na Gerenciador de Soluções e selecione publicar. Clique em importar perfil na barra inferior e importe o perfil de publicação que você baixou anteriormente.
    3.  Clique em configurar e, na guia conexão, atualize a URL de destino para que ela seja um https na URL de home page, por exemplo https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net. Clique em Seguinte.
    4.  Na guia Configurações, certifique-se de que habilitar autenticação organizacional não esteja selecionado. Clique em Guardar. Clique em publicar na tela principal.
    5.  O Visual Studio publicará o projeto e abrirá automaticamente um navegador para a URL do projeto. Se você vir a página da Web padrão do projeto, a publicação foi bem-sucedida.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementar a autenticação multifator para Azure Active Directory
   Os administradores precisam garantir que as contas de assinatura no portal sejam protegidas. A assinatura é vulnerável a ataques porque gerencia os recursos que você criou. Para proteger a assinatura, habilite a autenticação multifator na guia **Azure Active Directory** da assinatura.

   Um Azure AD opera com base em políticas que são aplicadas a um usuário ou grupos de usuários que se ajustam a determinados critérios.
O Azure cria uma política padrão especificando que os administradores precisam de autenticação de dois fatores para entrar no Portal.
Depois de habilitar essa política, você poderá ser solicitado a sair e entrar novamente no portal do Azure.

Para habilitar o MFA para entradas de administrador

   1. Vá para a guia **Azure Active Directory** na portal do Azure
   2. Na categoria segurança, selecione acesso condicional. Você vê esta tela

       ![Políticas de Acesso Condicional](./media/secure-aad-app/ad-mfa-conditional-add.png)

Se você não puder criar uma nova política

   1. Vá para a guia **MFA** .
   2. Selecione um Azure AD Premium link de **avaliação gratuita** para assinar a avaliação gratuita.

   ![Uma Azure AD Premium avaliação gratuita](./media/secure-aad-app/ad-trial-premium.png)

Retorne à tela de acesso condicional.

   1. Selecione a guia nova política.
   2. Introduza o nome da política.
   3. Selecione os usuários ou grupos para os quais você deseja habilitar a MFA.
   4. Em **controles de acesso**, selecione a guia **conceder** e, em seguida, selecione **exigir autenticação multifator** (e outras configurações, se desejar).

   ![Requerer MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Você pode habilitar a política marcando a caixa de seleção na parte superior da tela ou fazer isso na guia **acesso condicional** . Quando a política está habilitada, os usuários precisam de MFA para entrar no Portal.

   Há uma política de linha de base que requer MFA para todos os administradores do Azure. Você pode habilitá-lo imediatamente no Portal. A habilitação dessa política pode invalidar a sessão atual e forçá-lo a entrar novamente.

   Se a política de linha de base não estiver habilitada
   1.   Selecione **exigir MFA para administradores**.
   2.   Selecione **usar política imediatamente**.

   ![Selecione usar política imediatamente](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Usar o Azure Sentinel para monitorar aplicativos e recursos

   À medida que um aplicativo cresce, torna-se difícil agregar todos os sinais de segurança e métricas recebidos dos recursos e torná-los úteis de forma orientada a ações.

   O Azure Sentinel foi projetado para coletar dados, detectar os tipos de ameaças possíveis e fornecer visibilidade de incidentes de segurança.
Enquanto ele aguarda a intervenção manual, o Azure Sentinel pode contar com guias estratégicos pré-gravados para iniciar os alertas e os processos de gerenciamento de incidentes.

   O aplicativo de exemplo é composto por vários recursos que o Azure Sentinel pode monitorar.
Para configurar o Azure Sentinel, primeiro você precisa criar um espaço de trabalho Log Analytics que armazena todos os dados coletados de vários recursos.

Para criar este espaço de trabalho

   1. Na caixa de pesquisa na portal do Azure, procure **log Analytics**. Selecione **log Analytics espaços de trabalho**.

   ![Pesquisar espaços de trabalho do Log Analytics](./media/secure-aad-app/sentinel-log-analytics.png)

   *Pesquisar espaços de trabalho do Log Analytics*

   2. Na página seguinte, selecione **Adicionar** e forneça um nome, um grupo de recursos e um local para o espaço de trabalho.
   ![Criar uma área de trabalho do Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Criar uma área de trabalho do Log Analytics*

   3. Use a caixa de pesquisa para pesquisar o **Azure Sentinel**.

   ![Procurar o Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Pesquisar pelo Azure Sentinel*

   4. Selecione **Adicionar** e, em seguida, selecione o log Analytics espaço de trabalho que você criou anteriormente.

   ![Adicionar um espaço de trabalho Log Analytics](./media/secure-aad-app/sentinel-workspace-add.png)

   *Adicionar um espaço de trabalho Log Analytics*

   5. Na página **Azure Sentinel-conectores de dados** , em **configuração**, selecione **conectores de dados**. Você verá uma matriz de serviços do Azure que você pode vincular à instância de armazenamento Log Analytics para análise no Azure Sentinel.

   ![Conectores de dados Log Analytics](./media/secure-aad-app/sentinel-connectors.png)

      *Adicionar um conector de dados ao Azure Sentinel*

   Por exemplo, para conectar o gateway de aplicativo, execute estas etapas:

   1. Abra a folha da instância do gateway Aplicativo Azure.
   2. Sob **monitorização**, selecione **das definições de diagnóstico**.
   3. Selecione **Adicionar configuração de diagnóstico**.

   ![Adicionar diagnóstico do gateway de aplicativo](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Adicionar diagnóstico do gateway de aplicativo*

   4. Na página **configurações de diagnóstico** , selecione o espaço de trabalho log Analytics que você criou e, em seguida, selecione todas as métricas que deseja coletar e enviar para o Azure Sentinel. Selecione **Guardar**.

   ![Configurações do conector do Azure Sentinel](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Considerações de custos
   Se você ainda não tiver uma conta do Azure, poderá criar uma gratuita. Acesse a [página conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que você pode fazer com uma conta gratuita do Azure e saiba quais produtos são gratuitos por 12 meses.

   Para implantar os recursos no aplicativo de exemplo com os recursos de segurança, você precisa pagar por alguns recursos premium. Conforme o aplicativo é dimensionado e as camadas e avaliações gratuitas oferecidas pelo Azure precisam ser atualizadas para atender aos requisitos do aplicativo, seus custos podem aumentar. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) do Azure para estimar seus custos.

## <a name="next-steps"></a>Passos seguintes
   Os artigos a seguir podem ajudá-lo a projetar, desenvolver e implantar aplicativos seguros.

- [Estruturar](secure-design.md)
- [Programar](secure-develop.md)
- [Implementar](secure-deploy.md)

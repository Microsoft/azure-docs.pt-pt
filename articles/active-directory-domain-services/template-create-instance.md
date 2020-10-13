---
title: Ativar os Serviços de Domínio Azure DS usando um modelo Microsoft Docs
description: Saiba como configurar e ativar os Serviços de Domínio do Diretório Ativo do Azure utilizando um modelo de Gestor de Recursos Azure
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: f257a186f05dc94923d1d39829b5ed68b518f20c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967635"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Crie um domínio gerido por Serviços de Domínio de Diretório Ativo Azure usando um modelo de Gestor de Recursos Azure

Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a junção de domínio, política de grupo, autenticação de LDAP, Kerberos/NTLM que é totalmente compatível com o Windows Server Ative Directory. Você consome estes serviços de domínio sem implantar, gerir e remendar controladores de domínio si mesmo. A Azure AD DS integra-se com o seu inquilino AZure AD existente. Esta integração permite que os utilizadores assinem a utilização das suas credenciais corporativas, podendo utilizar grupos e contas de utilizador existentes para garantir o acesso aos recursos.

Este artigo mostra-lhe como criar um domínio gerido usando um modelo de Gestor de Recursos Azure. Os recursos de apoio são criados usando a Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure PowerShell e ligue-se à sua assinatura Azure](/powershell/azure/install-az-ps).
    * Certifique-se de que faz sedução na sua assinatura Azure utilizando o [cmdlet Connect-AzAccount.][Connect-AzAccount]
* Instale e configuure Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure AD PowerShell e ligar-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de que faz sedundo ao seu inquilino AD Azure usando o cmdlet [Connect-AzureAD.][Connect-AzureAD]
* Você precisa de privilégios *de administrador global* no seu inquilino AZure AD para ativar Azure AD DS.
* Precisa de privilégios *colaboradores* na sua subscrição Azure para criar os recursos Azure AD DS necessários.

## <a name="dns-naming-requirements"></a>Requisitos de nomeação de DNS

Quando cria um domínio gerido AZure AD DS, especifica um nome DNS. Existem algumas considerações quando escolhe este nome DNS:

* **Nome de domínio incorporado:** Por predefinição, é utilizado o nome de domínio incorporado do diretório (um sufixo *.onmicrosoft.com).* Se desejar permitir o acesso seguro do LDAP ao domínio gerido através da internet, não é possível criar um certificado digital para garantir a ligação com este domínio predefinido. A Microsoft detém o domínio *.onmicrosoft.com,* pelo que uma Autoridade de Certificados (CA) não emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, tipicamente um que já possui e é roteável. Quando utiliza um domínio roteível e personalizado, o tráfego pode fluir corretamente conforme necessário para suportar as suas aplicações.
* **Sufixos de domínio não-encaminháveis:** Recomendamos geralmente que evite um sufixo de nome de domínio não-encaminhável, como *contoso.local*. O *sufixo .local* não é roteável e pode causar problemas com a resolução dns.

> [!TIP]
> Se criar um nome de domínio personalizado, tenha cuidado com os espaços de nome DNS existentes. Recomenda-se a utilização de um nome de domínio separado de qualquer espaço de nome DNS existente ou no local.
>
> Por exemplo, se tiver um espaço de nome DNS existente *de contoso.com,* crie um domínio gerido com o nome de domínio personalizado de *aaddscontoso.com*. Se precisar de utilizar lDAP seguro, deve registar-se e possuir este nome de domínio personalizado para gerar os certificados necessários.
>
> Poderá ser necessário criar alguns registos DNS adicionais para outros serviços no seu ambiente, ou reencaminhadores de DNS condicionados entre os espaços de nome DNS existentes no seu ambiente. Por exemplo, se executar um webserver que hospeda um site usando o nome DE DNS raiz, pode haver nomeação de conflitos que requerem entradas adicionais de DNS.
>
> Nesta amostra e artigos de como fazer, o domínio personalizado de *aaddscontoso.com* é usado como um exemplo curto. Em todos os comandos, especifique o seu próprio nome de domínio.

Aplicam-se também as seguintes restrições de nome DNS:

* **Restrições de prefixo de domínio:** Não é possível criar um domínio gerido com um prefixo superior a 15 caracteres. O prefixo do seu nome de domínio especificado (como *aaddscontoso* no *aaddscontoso.com* nome de domínio) deve conter 15 ou menos caracteres.
* **Conflitos de nomes de rede:** O nome de domínio DNS para o seu domínio gerido já não deveria existir na rede virtual. Especificamente, verifique os seguintes cenários que levariam a um conflito de nomes:
    * Se já tem um domínio ative Directory com o mesmo nome de domínio DNS na rede virtual Azure.
    * Se a rede virtual onde planeia ativar o domínio gerido tiver uma ligação VPN com a sua rede no local. Neste cenário, certifique-se de que não tem um domínio com o mesmo nome de domínio DNS na sua rede no local.
    * Se tiver um serviço em nuvem Azure existente com esse nome na rede virtual Azure.

## <a name="create-required-azure-ad-resources"></a>Criar recursos AD AD necessários

A Azure AD DS requer um diretor de serviço e um grupo AZure AD. Estes recursos permitem que o domínio gerido sincronize os dados e defina quais os utilizadores que têm permissões administrativas no domínio gerido.

Em primeiro lugar, registe o fornecedor de recursos de serviços de domínio Azure AD utilizando o cmdlet [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Crie um diretor de serviço AZure AD utilizando o cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] para Azure AD DS comunicar e autenticar-se. Um ID de aplicação específico é usado chamado *Serviços de Controlador* de Domínio com um ID de *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Não mude esta identificação da aplicação.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Agora crie um grupo AD AZure chamado *Administradores AAD DC* usando o cmdlet [New-AzureADGroup.][New-AzureADGroup] Os utilizadores adicionados a este grupo são então autorizados a executar tarefas de administração no domínio gerido.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Com o grupo *de administradores AAD DC* criado, adicione um utilizador ao grupo utilizando o cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] Primeiro obtém o ID do grupo *de administradores AAD DC* utilizando o cmdlet [Get-AzureADGroup,][Get-AzureADGroup] em seguida, o ID do objeto do utilizador desejado usando o cmdlet [Get-AzureADUser.][Get-AzureADUser]

No exemplo seguinte, o iD do objeto de utilizador para a conta com uma UPN de `admin@contoso.onmicrosoft.com` . Substitua esta conta de utilizador pela UPN do utilizador que pretende adicionar ao grupo *de administradores AAD DC:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Finalmente, crie um grupo de recursos utilizando o cmdlet [New-AzResourceGroup.][New-AzResourceGroup] No exemplo seguinte, o grupo de recursos é nomeado *myResourceGroup* e é criado na região *oeste.* Use o seu próprio nome e região desejada:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Se escolher uma região que suporte Zonas de Disponibilidade, os recursos Azure AD DS são distribuídos por zonas para redundância adicional. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas.

Não há nada que possa configurar para que o Azure AD DS seja distribuído por zonas. A plataforma Azure lida automaticamente com a distribuição de recursos na zona. Para mais informações e para ver a disponibilidade da região, consulte [quais são as Zonas de Disponibilidade em Azure?][availability-zones]

## <a name="resource-definition-for-azure-ad-ds"></a>Definição de recursos para Azure AD DS

Como parte da definição de recursos do Gestor de Recursos, são necessários os seguintes parâmetros de configuração:

| Parâmetro               | Valor |
|-------------------------|---------|
| domínioName              | O nome de domínio DNS para o seu domínio gerido, tendo em conta os pontos anteriores sobre o nome de prefixos e conflitos. |
| filtradoSync            | O Azure AD DS permite sincronizar *todos os* utilizadores e grupos disponíveis no Azure AD, ou uma sincronização *de* grupos específicos.<br /><br /> Para obter mais informações sobre a sincronização de âmbito, consulte [a sincronização dos Serviços de Domínio AZure AD][scoped-sync].|
| notificaçõesS    | Se houver alertas gerados no domínio gerido, podem ser enviadas notificações por e-mail. <br /><br />*Os administradores globais* do inquilino Azure e membros do grupo de *administradores da AAD DC* podem ser *habilitados* para estas notificações.<br /><br /> Se desejar, pode adicionar destinatários adicionais para notificações quando há alertas que requerem atenção.|
| ConfigurationType de domínio | Por padrão, um domínio gerido é criado como uma floresta *de utilizadores.* Este tipo de floresta sincroniza todos os objetos a partir de Azure AD, incluindo quaisquer contas de utilizador criadas em um ambiente AD DS no local. Não é necessário especificar um *valor de configuração de domínio* para criar uma floresta de utilizadores.<br /><br /> Uma floresta *de recursos* apenas sincroniza utilizadores e grupos criados diretamente em Azure AD. Desfie o valor para *o ResourceTrusting* para criar uma floresta de recursos.<br /><br />Para obter mais informações sobre as florestas *de recursos,* incluindo por que você pode usar uma e como criar fidedignidades florestais com domínios AD DS no local, consulte a visão geral das [florestas de recursos AD DS AD.][resource-forests]|

A seguinte definição de parâmetros condensados mostra como estes valores são declarados. Uma floresta de utilizadores chamada *aaddscontoso.com* é criada com todos os utilizadores do Azure AD sincronizados com o domínio gerido:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

O seguinte tipo de modelo condensado do gestor de recursos é então usado para definir e criar o domínio gerido. Uma rede virtual Azure e uma sub-rede já devem existir, ou ser criadas como parte do modelo do Gestor de Recursos. O domínio gerido está ligado a esta sub-rede.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Estes parâmetros e tipo de recurso podem ser usados como parte de um modelo mais amplo do Gestor de Recursos para implementar um domínio gerido, como mostrado na secção seguinte.

## <a name="create-a-managed-domain-using-sample-template"></a>Crie um domínio gerido usando o modelo de amostra

O seguinte modelo completo de amostra de Gestor de Recursos cria um domínio gerido e as regras de rede virtual de suporte, sub-rede e grupo de segurança de rede. As regras do grupo de segurança da rede são necessárias para proteger o domínio gerido e certificar-se de que o tráfego pode fluir corretamente. É criada uma floresta de utilizadores com o nome DNS de *aaddscontoso.com,* com todos os utilizadores sincronizados a partir de Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Este modelo pode ser implementado utilizando o seu método de implantação preferido, como o [portal Azure][portal-deploy], [Azure PowerShell][powershell-deploy]ou um pipeline CI/CD. O exemplo a seguir utiliza o [cmdlet New-AzResourceGroupDeployment.][New-AzResourceGroupDeployment] Especifique o nome do seu próprio grupo de recursos e nome de ficheiro de modelo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Demora alguns minutos a criar o recurso e a devolver o controlo à solicitação PowerShell. O domínio gerido continua a ser a provisionado em segundo plano, podendo demorar até uma hora para completar a implementação. No portal Azure, a página **'Visão Geral'** para o seu domínio gerido mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a junção de domínio ou autenticação.
    * Para configurar o DNS, selecione o seu domínio gerido no portal. Na janela **'Vista Geral',** é solicitado que configufique automaticamente estas definições de DNS.
* [Ativar a sincronização de palavras-passe para Azure AD DS para](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar sação no domínio gerido utilizando as suas credenciais corporativas.

## <a name="next-steps"></a>Passos seguintes

Para ver o domínio gerido em ação, pode [juntar-se a um VM do Windows,][windows-join] [configurar LDAP seguro][tutorial-ldaps]e [configurar a sincronização de haxixe de palavra-passe][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
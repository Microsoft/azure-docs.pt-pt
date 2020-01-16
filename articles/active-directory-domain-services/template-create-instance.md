---
title: Habilitar os serviços de domínio do Azure DS usando um modelo | Microsoft Docs
description: Saiba como configurar e habilitar Azure Active Directory Domain Services usando um modelo de Azure Resource Manager
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: e63f330d463be21905467869474527fdf9d6abff
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030201"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Criar um Azure Active Directory Domain Services domínio gerenciado usando um modelo de Azure Resource Manager

O Azure Active Directory Domain Services (Azure AD DS) fornece serviços de domínio gerenciados, como ingresso no domínio, diretiva de grupo, LDAP, autenticação Kerberos/NTLM que é totalmente compatível com o Windows Server Active Directory. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches a controladores de domínio por conta própria. O Azure AD DS integra-se ao seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar grupos existentes e contas de usuário para proteger o acesso aos recursos.

Este artigo mostra como habilitar o Azure AD DS usando um modelo de Azure Resource Manager. Os recursos de suporte são criados usando Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure PowerShell e conectar-se à sua assinatura do Azure](/powershell/azure/install-az-ps).
    * Certifique-se de entrar em sua assinatura do Azure usando o cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Instalar e configurar o PowerShell do Azure AD.
    * Se necessário, siga as instruções para [instalar o módulo do PowerShell do Azure AD e conectar-se ao Azure ad](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de entrar no seu locatário do Azure AD usando o cmdlet [Connect-AzureAD][Connect-AzureAD] .
* Você precisa de privilégios de *administrador global* em seu locatário do Azure ad para habilitar o Azure AD DS.
* Você precisa de privilégios de *colaborador* em sua assinatura do Azure para criar os recursos de AD DS do Azure necessários.

## <a name="dns-naming-requirements"></a>Requisitos de nomenclatura de DNS

Ao criar uma instância de AD DS do Azure, você especifica um nome DNS. Há algumas considerações quando você escolhe esse nome DNS:

* **Nome de domínio interno:** Por padrão, o nome de domínio interno do diretório é usado (sufixo *. onmicrosoft.com* ). Se desejar habilitar o acesso LDAP seguro ao domínio gerenciado pela Internet, você não poderá criar um certificado digital para proteger a conexão com esse domínio padrão. A Microsoft possui o domínio *. onmicrosoft.com* , portanto, uma AC (autoridade de certificação) não emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, normalmente um que você já possui e é roteável. Quando você usa um domínio reroteável, personalizado, o tráfego pode fluir corretamente conforme necessário para dar suporte aos seus aplicativos.
* **Sufixos de domínio não roteáveis:** Geralmente, recomendamos que você evite um sufixo de nome de domínio não roteável, como *contoso. local*. O sufixo *. local* não é roteável e pode causar problemas com a resolução DNS.

> [!TIP]
> Se você criar um nome de domínio personalizado, tome cuidado com os namespaces DNS existentes. É recomendável incluir um prefixo exclusivo para o nome de domínio. Por exemplo, se o nome raiz do DNS for *contoso.com*, crie um domínio gerenciado AD DS do Azure com o nome de domínio personalizado *Corp.contoso.com* ou *DS.contoso.com*. Em um ambiente híbrido com um ambiente de AD DS local, esses prefixos já podem estar em uso. Use um prefixo exclusivo para AD DS do Azure.
>
> Você pode usar o nome DNS raiz para seu domínio gerenciado AD DS do Azure, mas talvez precise criar alguns registros DNS adicionais para outros serviços em seu ambiente. Por exemplo, se você executar um servidor Webque hospeda um site usando o nome DNS raiz, poderá haver conflitos de nomenclatura que exigem entradas DNS adicionais.
>
> Nesses tutoriais e artigos de instruções, o domínio personalizado do *aadds.contoso.com* é usado como um breve exemplo. Em todos os comandos, especifique seu próprio nome de domínio, que pode incluir um prefixo exclusivo.
>
> Para obter mais informações, consulte [selecionar um prefixo de nomenclatura para o domínio][naming-prefix].

As seguintes restrições de nome DNS também se aplicam:

* **Restrições de prefixo de domínio:** Você não pode criar um domínio gerenciado com um prefixo com mais de 15 caracteres. O prefixo do nome de domínio especificado (como *contoso* no nome de domínio *contoso.com* ) deve conter 15 caracteres ou menos.
* **Conflitos de nome de rede:** O nome de domínio DNS para seu domínio gerenciado já não deve existir na rede virtual. Especificamente, verifique os seguintes cenários que levam a um conflito de nome:
    * Se você já tiver um domínio Active Directory com o mesmo nome de domínio DNS na rede virtual do Azure.
    * Se a rede virtual em que você planeja habilitar o domínio gerenciado tiver uma conexão VPN com sua rede local. Nesse cenário, verifique se você não tem um domínio com o mesmo nome de domínio DNS em sua rede local.
    * Se você tiver um serviço de nuvem do Azure existente com esse nome na rede virtual do Azure.

## <a name="create-required-azure-ad-resources"></a>Criar recursos necessários do Azure AD

O Azure AD DS requer uma entidade de serviço e um grupo do Azure AD. Esses recursos permitem que o Azure AD DS domínio gerenciado sincronize dados e defina quais usuários têm permissões administrativas no domínio gerenciado.

Primeiro, registre o provedor de recursos Azure AD Domain Services usando o cmdlet [Register-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Crie uma entidade de serviço do Azure AD usando o cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] para o Azure AD DS se comunicar e se autenticar. Uma ID de aplicativo específica é usada chamada *serviços de controlador de domínio* com uma ID de *2565bd9d-DA50-47d4-8b85-4c97f669dc36*. Não altere essa ID de aplicativo.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Agora, crie um grupo do Azure AD chamado *Administradores de DC do AAD* usando o cmdlet [New-AzureADGroup][New-AzureADGroup] . Os usuários adicionados a esse grupo recebem permissões para executar tarefas de administração no domínio gerenciado AD DS do Azure.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Com o grupo de *Administradores do AAD DC* criado, adicione um usuário ao grupo usando o cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember] . Primeiro, você obtém a ID de objeto do grupo de *Administradores do AAD DC* usando o cmdlet [Get-AZUREADGROUP][Get-AzureADGroup] e a ID de objeto do usuário desejado usando o cmdlet [Get-AzureADUser][Get-AzureADUser] .

No exemplo a seguir, a ID de objeto de usuário para a conta com um UPN de `admin@contoso.onmicrosoft.com`. Substitua essa conta de usuário pelo UPN do usuário que você deseja adicionar ao grupo de *Administradores do AAD DC* :

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

Por fim, crie um grupo de recursos usando o cmdlet [New-AzResourceGroup][New-AzResourceGroup] . No exemplo a seguir, o grupo de recursos é denominado *MyResource* Group e é criado na região *westus* . Use seu próprio nome e a região desejada:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Se você escolher uma região com suporte a Zonas de Disponibilidade, os recursos de AD DS do Azure serão distribuídos entre zonas para redundância adicional. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.

Não há nada a ser configurado para o Azure AD DS ser distribuído entre zonas. A plataforma Azure manipula automaticamente a distribuição de zona de recursos. Para obter mais informações e ver a disponibilidade da região, consulte [o que são zonas de disponibilidade no Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Definição de recurso para AD DS do Azure

Como parte da definição de recurso do Resource Manager, os seguintes parâmetros de configuração são necessários:

| Parâmetro               | Valor |
|-------------------------|---------|
| domainName              | O nome de domínio DNS para seu domínio gerenciado, levando em consideração os pontos anteriores em prefixos de nomenclatura e conflitos. |
| filteredSync            | O AD DS do Azure permite sincronizar *todos* os usuários e grupos disponíveis no Azure ad ou uma sincronização com *escopo* apenas de grupos específicos. Se você optar por sincronizar todos os usuários e grupos, não poderá optar por executar apenas uma sincronização com escopo.<br /> Para obter mais informações sobre sincronização com escopo, consulte [Azure AD Domain Services sincronização com escopo][scoped-sync].|
| notificationSettings    | Se houver alertas gerados no domínio gerenciado AD DS do Azure, as notificações por email poderão ser enviadas. <br />*Os administradores globais* do locatário do Azure e os membros do grupo de *Administradores do AAD DC* podem ser *habilitados* para essas notificações.<br /> Se desejar, você pode adicionar destinatários adicionais para notificações quando houver alertas que exigem atenção.|
| domainConfigurationType | Por padrão, um domínio gerenciado do Azure AD DS é criado como uma floresta de *usuário* . Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente de AD DS local. Você não precisa especificar um valor de *domainConfiguration* para criar uma floresta de usuário.<br /> Uma floresta de *recursos* só sincroniza usuários e grupos criados diretamente no Azure AD. Atualmente, as florestas de recursos estão em versão prévia. Defina o valor como *ResourceTrusting* para criar uma floresta de recursos.<br />Para obter mais informações sobre florestas de *recursos* , incluindo por que você pode usar um e como criar relações de confiança de floresta com domínios AD DS locais, consulte [visão geral das florestas de recursos do Azure AD DS][resource-forests].|

A definição de parâmetros condensada a seguir mostra como esses valores são declarados. Uma floresta de usuário chamada *aadds.contoso.com* é criada com todos os usuários do Azure ad sincronizados com o domínio gerenciado AD DS do Azure:

```json
"parameters": {
    "domainName": {
        "value": "aadds.contoso.com"
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

O tipo de recurso de modelo do Resource Manager condensado a seguir é usado para definir e criar o domínio gerenciado AD DS do Azure. Uma rede virtual e uma sub-rede do Azure já devem existir ou ser criadas como parte do modelo do Resource Manager. O domínio gerenciado do AD DS do Azure está conectado a esta sub-rede.

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

Esses parâmetros e o tipo de recurso podem ser usados como parte de um modelo mais amplo do Resource Manager para implantar um domínio gerenciado, conforme mostrado na seção a seguir.

## <a name="create-a-managed-domain-using-sample-template"></a>Criar um domínio gerenciado usando o modelo de exemplo

O modelo de exemplo completo do Resource Manager cria um domínio gerenciado do Azure AD DS e as regras de rede virtual de suporte, sub-rede e grupo de segurança de rede. As regras do grupo de segurança de rede são necessárias para proteger o domínio gerenciado e garantir que o tráfego possa fluir corretamente. Uma floresta de usuário com o nome DNS de *aadds.contoso.com* é criada, com todos os usuários sincronizados do Azure AD:

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
            "value": "aadds.contoso.com"
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

Esse modelo pode ser implantado usando seu método de implantação preferencial, como o [portal do Azure][portal-deploy], [Azure PowerShell][powershell-deploy]ou um pipeline de CI/CD. O exemplo a seguir usa o cmdlet [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] . Especifique o nome do grupo de recursos e o nome do arquivo do modelo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Leva alguns minutos para criar o recurso e retornar o controle para o prompt do PowerShell. O Azure AD DS domínio gerenciado continua a ser provisionado em segundo plano e pode levar até uma hora para concluir a implantação. No portal do Azure, a página **visão geral** do domínio gerenciado do Azure AD DS mostra o status atual durante esse estágio de implantação.

Quando o portal do Azure mostra que o domínio gerenciado do Azure AD DS concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você defina automaticamente essas configurações de DNS.
* [Habilite a sincronização de senha para Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="next-steps"></a>Passos seguintes

Para ver o domínio gerenciado AD DS do Azure em ação, você pode [ingressar no domínio em uma VM do Windows][windows-join], [Configurar o LDAP seguro][tutorial-ldaps]e configurar a sincronização de hash de [senha][tutorial-phs].

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
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment

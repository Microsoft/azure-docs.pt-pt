---
title: Ativar os Serviços de Domínio Azure DS utilizando um modelo / Microsoft Docs
description: Saiba como configurar e ativar os Serviços de Domínio de Diretório Ativo azure usando um modelo de Gestor de Recursos Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: 2daadb539bc08df37f15c187866b735e45309288
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612791"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Criar um domínio gerido por Serviços de Domínio de Diretório Ativo Azure utilizando um modelo de Gestor de Recursos Azure

Os Serviços de Domínio de Diretório Ativo Azure (Azure AD DS) fornecem serviços de domínio geridos, tais como a adesão ao domínio, política de grupo, autenticação LDAP, Kerberos/NTLM que é totalmente compatível com o Diretório Ativo do Windows Server. Você consome estes serviços de domínio sem implementar, gerir e remendar controladores de domínio por si mesmo. A Azure AD DS integra-se com o seu inquilino Azure AD existente. Esta integração permite que os utilizadores assinem usando as suas credenciais corporativas, e você pode usar grupos e contas de utilizador existentes para garantir o acesso aos recursos.

Este artigo mostra-lhe como ativar o Azure AD DS usando um modelo de Gestor de Recursos Azure. Os recursos de apoio são criados usando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo PowerShell Azure e ligue-se à sua subscrição Azure](/powershell/azure/install-az-ps).
    * Certifique-se de que insere a sua subscrição Azure utilizando o cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Instale e configure o Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo PowerShell Azure AD e ligue-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de que inscreveu o seu inquilino Azure AD utilizando o cmdlet [Connect-AzureAD.][Connect-AzureAD]
* Você precisa de privilégios *de administrador global* no seu inquilino Azure AD para permitir o Azure AD DS.
* Você precisa de privilégios *Contributivos* na sua subscrição Azure para criar os recursos DS Azure necessários.

## <a name="dns-naming-requirements"></a>Requisitos de nomeação dNS

Quando cria uma instância Azure AD DS, especifica um nome DNS. Existem algumas considerações quando escolhe este nome DNS:

* **Nome de domínio incorporado:** Por predefinição, é utilizado o nome de domínio incorporado do diretório (um *sufixo .onmicrosoft.com).* Se desejar ativar o acesso lDAP seguro ao domínio gerido através da internet, não pode criar um certificado digital para garantir a ligação com este domínio predefinido. A Microsoft é proprietária do domínio *.onmicrosoft.com,* pelo que uma Autoridade de Certificados (CA) não emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, tipicamente um que você já possui e é routable. Quando utiliza um domínio personalizado e roteável, o tráfego pode fluir corretamente conforme necessário para suportar as suas aplicações.
* **Sufixos de domínio não-enrpreensíveis:** Recomendamos geralmente que evite um sufixo de nome de domínio não rotativa, como *contoso.local*. O sufixo *.local* não é repreensível e pode causar problemas com a resolução do DNS.

> [!TIP]
> Se criar um nome de domínio personalizado, tenha cuidado com os espaços de nome DNS existentes. É aconselhável usar um nome de domínio separado de qualquer espaço de nome DNS existente ou no local.
>
> Por exemplo, se tiver um espaço de nome DNS existente de *contoso.com,* crie um domínio gerido por Azure AD DS com o nome de domínio personalizado de *aaddscontoso.com*. Se precisar de utilizar LDAP seguro, tem de registar e possuir este nome de domínio personalizado para gerar os certificados necessários.
>
> Poderá ser necessário criar alguns registos DNS adicionais para outros serviços no seu ambiente, ou avançados de DNS condicionados entre os espaços de nome DNS existentes no seu ambiente. Por exemplo, se executar um webserver que acolhe um site usando o nome DNS raiz, pode haver conflitos de nomeação que requerem entradas dNS adicionais.
>
> Nestes tutoriais e artigos como fazer, o domínio personalizado da *aaddscontoso.com* é usado como um exemplo curto. Em todos os comandos, especifique o seu próprio nome de domínio.

Aplicam-se igualmente as seguintes restrições de nome dNS:

* **Restrições de prefixo de domínio:** Não se pode criar um domínio gerido com um prefixo superior a 15 caracteres. O prefixo do seu nome de domínio especificado (como *aaddscontoso* no nome de domínio *aaddscontoso.com)* deve conter 15 ou menos caracteres.
* **Conflitos de nome de rede:** O nome de domínio DNS para o seu domínio gerido não deve já existir na rede virtual. Especificamente, verifique os seguintes cenários que levariam a um conflito de nomes:
    * Se já tem um domínio de Diretório Ativo com o mesmo nome de domínio DNS na rede virtual Azure.
    * Se a rede virtual onde planeia ativar o domínio gerido tiver uma ligação VPN com a sua rede no local. Neste cenário, certifique-se de que não tem um domínio com o mesmo nome de domínio DNS na sua rede no local.
    * Se tiver um serviço de nuvem Azure existente com esse nome na rede virtual Azure.

## <a name="create-required-azure-ad-resources"></a>Criar recursos adatos necessários do Azure

A Azure AD DS requer um diretor de serviço e um grupo Azure AD. Estes recursos permitem que o Azure AD DS gerido sincronize os dados e defina quais os utilizadores que têm permissões administrativas no domínio gerido.

Em primeiro lugar, registe o fornecedor de recursos da Azure AD Domain Services utilizando o [cmdlet Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Crie um diretor de serviço azure AD utilizando o [cmdlet New-AzureADServicePrincipal][New-AzureADServicePrincipal] para o Azure AD DS para comunicar e autenticar-se. Um ID de aplicação específico é usado chamado *Serviços* de Controlador de Domínio com uma identificação de *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Não altere esta identificação da aplicação.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Agora crie um grupo Azure AD chamado *AAD DC Administrators* usando o cmdlet [New-AzureADGroup.][New-AzureADGroup] Os utilizadores adicionados a este grupo são então autorizados a executar tarefas de administração no domínio gerido pelo Azure AD DS.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Com o grupo de *administradores aAD DC* criado, adicione um utilizador ao grupo utilizando o Cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] Primeiro obtém o ID de objeto de grupo *aAD DC* administradores utilizando o cmdlet [Get-AzureADGroup,][Get-AzureADGroup] em seguida, o ID de objeto do utilizador desejado utilizando o cmdlet [Get-AzureADUser.][Get-AzureADUser]

No exemplo seguinte, o id do objeto do utilizador para a conta com uma UPN de `admin@aaddscontoso.onmicrosoft.com`. Substitua esta conta de utilizador pela UPN do utilizador que pretende adicionar ao grupo de *Administradores aAD DC:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Por fim, crie um grupo de recursos utilizando o cmdlet [New-AzResourceGroup.][New-AzResourceGroup] No exemplo seguinte, o grupo de recursos é nomeado *myResourceGroup* e é criado na região *de Westus.* Use o seu próprio nome e região desejada:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Se escolher uma região que apoie zonas de disponibilidade, os recursos Da DS Azure são distribuídos por zonas para despedimentoadicional. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.

Não há nada para configurar para que o Azure AD DS seja distribuído por zonas. A plataforma Azure lida automaticamente com a distribuição de recursos da zona. Para mais informações e para ver disponibilidade da região, consulte [As Zonas de Disponibilidade em Azure?][availability-zones]

## <a name="resource-definition-for-azure-ad-ds"></a>Definição de recursos para DS AD Azure

Como parte da definição de recursos do Gestor de Recursos, são necessários os seguintes parâmetros de configuração:

| Parâmetro               | Valor |
|-------------------------|---------|
| domainName              | O nome de domínio DNS para o seu domínio gerido, tendo em conta os pontos anteriores sobre a nomeação de prefixos e conflitos. |
| sincronizado            | O Azure AD DS permite sincronizar *todos os* utilizadores e grupos disponíveis em Azure AD, ou uma sincronização *de* apenas grupos específicos. Se optar por sincronizar todos os utilizadores e grupos, não poderá optar mais tarde por realizar apenas uma sincronização com âmbito de aplicação.<br /> Para obter mais informações sobre a sincronização por via geminada, consulte a [sincronização de sincronização dos Serviços de Domínio da AD Azure.][scoped-sync]|
| notificationSettings    | Se houver alertas gerados no domínio gerido pelo Azure AD DS, as notificações de e-mail podem ser enviadas. <br />*Os administradores globais* do inquilino Azure e membros do grupo de administradores da *AAD DC* podem ser *ativados* para estas notificações.<br /> Se desejar, pode adicionar destinatários adicionais para notificações quando houver alertas que requerem atenção.|
| domainConfigurationType | Por padrão, um domínio gerido por Azure AD DS é criado como uma floresta *de utilizadores.* Este tipo de floresta sincroniza todos os objetos da AD Azure, incluindo quaisquer contas de utilizador criadas num ambiente AD DS no local. Não é necessário especificar um valor de *configuração* de domínio para criar uma floresta de utilizadores.<br /> Uma floresta de *recursos* apenas sincroniza utilizadores e grupos criados diretamente em Azure AD. As florestas de recursos estão atualmente em pré-visualização. Detete o valor para *a ResourceTrusting* para criar uma floresta de recursos.<br />Para obter mais informações sobre as florestas de *Recursos,* incluindo por que você pode usar uma e como criar fundos florestais com domínios AD DS no local, consulte a visão geral das florestas de [recursos da AD DS azure][resource-forests].|

A definição de parâmetros condensados seguinte mostra como estes valores são declarados. Uma floresta de utilizadores chamada *aaddscontoso.com* é criada com todos os utilizadores desde o Azure AD sincronizado para o domínio gerido pela AD DS do Azure:

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

O seguinte modelo de recurso condensado do Gestor de Recursos é então usado para definir e criar o domínio gerido pelo Azure AD DS. Uma rede virtual Azure e uma subnet já devem existir, ou ser criadas como parte do modelo de Gestor de Recursos. O domínio gerido pelo Azure AD DS está ligado a esta subrede.

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

Estes parâmetros e o tipo de recursos podem ser usados como parte de um modelo mais amplo do Gestor de Recursos para implantar um domínio gerido, como mostra a secção seguinte.

## <a name="create-a-managed-domain-using-sample-template"></a>Criar um domínio gerido usando o modelo de amostra

O seguinte modelo completo de amostra de Gestor de Recursos cria um domínio gerido pelo Azure AD DS e as regras de rede virtual de suporte, subnet e grupo de segurança de rede. As regras do grupo de segurança da rede são necessárias para proteger o domínio gerido e certificar-se de que o tráfego pode fluir corretamente. É criada uma floresta de utilizadores com o nome DNS de *aaddscontoso.com,* com todos os utilizadores sincronizados a partir de Azure AD:

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

Este modelo pode ser implantado utilizando o seu método de implantação preferido, como o [portal Azure,][portal-deploy] [Azure PowerShell,][powershell-deploy]ou um pipeline CI/CD. O exemplo seguinte utiliza o cmdlet [New-AzResourceGroupDeployment.][New-AzResourceGroupDeployment] Especifique o nome do seu próprio grupo de recursos e o nome de ficheiro do modelo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Leva alguns minutos para criar o recurso e devolver o controlo ao pedido powerShell. O domínio gerido pela AD DS Azure continua a ser aprovisionado em segundo plano, podendo demorar até uma hora a concluir a implantação. No portal Azure, a página **de visão geral** do seu domínio gerido pelo Azure AD DS mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido pela AD DS azure terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a adesão ou autenticação de domínio.
    * Para configurar o DNS, selecione o seu domínio gerido pelo Azure AD DS no portal. Na janela **Overview,** é-lhe solicitado que configure automaticamente estas definições de DNS.
* Ative a sincronização de [passwords para os Serviços de Domínio AD Do Azure para](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar sessão no domínio gerido usando as suas credenciais corporativas.

## <a name="next-steps"></a>Passos seguintes

Para ver o domínio gerido pelo Azure AD DS em ação, pode [juntar-se a um Windows VM,][windows-join] [configurar lDAP seguro][tutorial-ldaps]e configurar a sincronização de hash de [palavra-passe][tutorial-phs].

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

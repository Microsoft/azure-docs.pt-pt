---
title: O Azure Service Fabric melhores práticas de segurança | Documentos da Microsoft
description: Melhores práticas para segurança do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 350aef037f019733e02331623758c14a3c64ab50
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805006"
---
# <a name="azure-service-fabric-security"></a>Segurança do Azure Service Fabric 

Para obter mais informações sobre [práticas recomendadas de segurança do Azure](https://docs.microsoft.com/azure/security/), reveja [práticas recomendadas de segurança do Azure Service Fabric](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices)

## <a name="key-vault"></a>Cofre de Chaves

[O Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) é os recomendada segredos serviço de gestão para aplicações do Azure Service Fabric e clusters.
> [!NOTE]
> Se forem implementados certificados/segredos de um cofre de chaves de um conjunto de dimensionamento de Máquina Virtual como um segredo de conjunto de dimensionamento da Máquina Virtual, em seguida, o Cofre de chaves e, em seguida, o conjunto de dimensionamento de Máquina Virtual tem de ser colocalizado.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Criar a autoridade de certificação que emitiu o certificado do Service Fabric

Um certificado do Azure Key Vault pode ser criado ou importado para um cofre de chaves. Quando é criado um certificado do Key Vault, a chave privada é criada dentro do Key Vault e nunca é exposta para o proprietário do certificado. Aqui estão as formas de criar um certificado no Cofre de chaves:

- Crie um certificado autoassinado para criar um par de chaves públicas-privadas e associá-la com um certificado. O certificado será assinado pela sua própria chave. 
- Crie um novo certificado manualmente para criar um par de chaves públicas-privadas e gerar um pedido de assinatura de certificado X.509. O pedido de assinatura pode ser assinado pela sua autoridade de registo ou de uma autoridade de certificação. Emparelhe x509 assinado certificado pode ser intercalado com a chave de pendente para concluir o certificado de KV no Cofre de chaves. Embora este método requer mais passos, ele fornecer com maior segurança como a chave privada é criada no e restrito ao Cofre de chaves. Isso é explicado no diagrama abaixo. 

Revisão [métodos de criação de certificado do Azure Keyvault](https://docs.microsoft.com/azure/key-vault/create-certificate) para obter mais detalhes.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Implementar certificados do Key Vault para conjuntos de dimensionamento da máquina virtual de cluster de Service Fabric

Para implementar certificados a partir de um cofre de chaves de localização conjunta para um conjunto de dimensionamento de Máquina Virtual, utilize o conjunto de dimensionamento de máquinas virtuais [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). Seguem-se as propriedades de modelo do Resource Manager:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> O Cofre tem de estar ativado para implementação de modelo do Resource Manager.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Aplicam-se uma lista de controlo de acesso (ACL) para o certificado para o seu cluster do Service Fabric

[As extensões de conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) Publicador Microsoft.Azure.ServiceFabric é utilizado para configurar a segurança de nós.
Para aplicar uma ACL a seus certificados para os seus processos de Cluster do Service Fabric, utilize as seguintes propriedades de modelo do Resource Manager:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Proteger um certificado de cluster do Service Fabric ao nome comum

Para proteger o seu cluster do Service Fabric por certificado `Common Name`, utilize a propriedade de modelo do Resource Manager [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), da seguinte forma:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Clusters do Service Fabric irão utilizar o primeiro certificado válido que se encontra no arquivo de certificados do seu host. No Windows, este será o certificado com a data mais recente prestes a expirar, que corresponde ao thumbprint seu nome comum e o emissor.

Domínios do Azure, tal como *\<SUBDOMÍNIO YOUR\>. cloudapp.azure.com ou \<seu SUBDOMÍNIO\>. trafficmanager.net, pertencerem à Microsoft. Autoridades de certificação não irão emitir certificados para domínios para utilizadores não autorizados. A maioria dos usuários será necessário comprar um domínio de uma entidade de registo ou ser um administrador do domínio autorizado, para uma autoridade de certificação emitir um certificado com esse nome comum.

Para obter mais informações sobre como configurar o serviço de DNS para resolver o seu domínio para um endereço IP da Microsoft, consulte como configurar [DNS do Azure para alojar o seu domínio](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Depois de delegar os seus servidores de nomes de domínios para os servidores de nome de zona DNS do Azure, adicione os seguintes dois registos à sua zona DNS:
> - Uma ' um "registo para o domínio APEX que não seja um `Alias record set` para todos os endereços IP irá resolver o seu domínio personalizado.
> - Um registo de "C" para sub domínios da Microsoft que aprovisionou que não são um `Alias record set`. Por exemplo, pode utilizar o Gestor de tráfego ou o nome DNS do Balanceador de carga.

Para atualizar o seu portal para apresentar um nome DNS personalizado para o seu Cluster do Service Fabric `"managementEndpoint"`, Atualize as seguintes propriedades do modelo de Gestor de recursos de Cluster do Service Fabric:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Encriptar os valores secretos do pacote de recursos de infraestrutura do serviço

Valores comuns que são encriptados em pacotes do Service Fabric incluem as credenciais do Azure Container Registry (ACR), variáveis de ambiente, as definições e chaves de conta de armazenamento de plug-in de Volume do Azure.

Para [configurar um certificado de encriptação e encriptar os segredos em clusters do Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Gere um certificado autoassinado para encriptar o seu segredo:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Utilize as instruções em [conjuntos de dimensionamento de certificados do Key Vault implementar a máquina de virtual de cluster do Service Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) para implementar os certificados do Key Vault para conjuntos de dimensionamento de Máquina Virtual do Cluster do Service Fabric.

Encriptar o segredo com o seguinte comando do PowerShell e, em seguida, atualize o manifesto da aplicação de Service Fabric com o valor encriptado:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Para [configurar um certificado de encriptação e encriptar os segredos em clusters do Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Gere um certificado autoassinado para encriptar os segredos:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Utilize as instruções em [conjuntos de dimensionamento de certificados do Key Vault implementar a máquina de virtual de cluster do Service Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) para conjuntos de dimensionamento de Máquina Virtual do Cluster do Service Fabric.

Encriptar o segredo com os comandos seguintes e, em seguida, atualize o manifesto de aplicativo de serviço de recursos de infraestrutura com o valor encriptado:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Depois de seus valores protegidos, a encriptar [especificar os segredos encriptados na aplicação do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application), e [desencriptar os segredos encriptados a partir do código de serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Autenticar aplicações do Service Fabric para recursos do Azure com a identidade de serviço gerida (MSI)

Para saber mais sobre identidades geridas para recursos do Azure, veja [o que há de identidades geridas para recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-it-work).
Clusters do Service Fabric do Azure estão alojados em conjuntos de dimensionamento de Máquina Virtual, que suportam [identidade do serviço gerido](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Para obter uma lista de serviços que MSI pode ser utilizado para autenticação, consulte [dos serviços do Azure que suportam o Azure Active Directory Authentication](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Para ativar o sistema de identidade gerida de atribuída durante a criação de um conjunto de dimensionamento de máquinas virtuais ou um conjunto de dimensionamento de máquinas virtuais existente, declarar o seguinte `"Microsoft.Compute/virtualMachinesScaleSets"` propriedade:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Ver [o que há de identidades geridas para recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity) para obter mais informações.

Se tiver criado uma [atribuído ao utilizador a identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), declarar o seguinte recurso no seu modelo atribuí-la ao conjunto de dimensionamento de máquina virtual. Substitua `\<USERASSIGNEDIDENTITYNAME\>` com o nome do atribuído ao utilizador que criou a identidade de gerido:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Antes de usar seus recursos de infraestrutura do serviço de aplicativo pode fazer de uma identidade gerida, devem ser concedidas permissões para os recursos do Azure tem de autenticar com o.
Os seguintes comandos de conceder acesso a um recurso do Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

No código da aplicação de Service Fabric, obter um token de acesso para o Azure Resource Manager, fazendo uma pausa tudo semelhante ao seguinte:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

A aplicação do Service Fabric, em seguida, pode utilizar o token de acesso para se autenticarem nos recursos do Azure que suportam o Active Directory.
O exemplo seguinte mostra como fazê-lo para o recurso do Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```

## <a name="windows-defender"></a>Windows Defender 

Por predefinição, o Windows Defender antivírus está instalado no Windows Server 2016. Para obter detalhes, consulte [antivírus do Windows Defender no Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). A interface do usuário é instalada por predefinição em alguns SKUs, mas não é necessária. Para reduzir qualquer consumo de recursos e o impacto de desempenho sobrecarga cobrado pelo Windows Defender e se as políticas de segurança permitem-lhe excluir processos e caminhos para o software de código-fonte aberto, declare o seguinte dimensionamento Definir extensão do recurso de Máquina Virtual Propriedades do modelo de gestor para excluir o cluster do Service Fabric de análises de:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Se não estiver a utilizar o Windows Defender, consulte a documentação do Antimalware para as regras de configuração. O Windows Defender não é suportado no Linux.

## <a name="next-steps"></a>Passos Seguintes

* Crie um cluster em VMs ou computadores, com o Windows Server: [Criação do cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Crie um cluster em VMs ou computadores, em execução no Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre [opções de suporte do Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
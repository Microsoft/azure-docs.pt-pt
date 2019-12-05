---
title: Práticas recomendadas de segurança do Azure Service Fabric | Microsoft Docs
description: Práticas recomendadas para a segurança de Service Fabric do Azure.
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
ms.openlocfilehash: fec81e843753656d651c6d5d0b73077a964be9d4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807447"
---
# <a name="azure-service-fabric-security"></a>Segurança do Azure Service Fabric 

Para obter mais informações sobre [as práticas recomendadas de segurança do Azure](https://docs.microsoft.com/azure/security/), examine [as práticas recomendadas de segurança do Azure Service Fabric](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) é o serviço de gerenciamento de segredos recomendado para aplicativos e clusters do Azure Service Fabric.
> [!NOTE]
> Se os certificados/segredos de um Key Vault forem implantados em um conjunto de dimensionamento de máquinas virtuais como um segredo do conjunto de dimensionamento de máquinas virtuais, o Key Vault e o conjunto de dimensionamento de máquinas virtuais deverão ser colocalizados.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Criar autoridade de certificação emitida Service Fabric certificado

Um certificado Azure Key Vault pode ser criado ou importado para um Key Vault. Quando um certificado de Key Vault é criado, a chave privada é criada dentro da Key Vault e nunca exposta ao proprietário do certificado. Aqui estão as maneiras de criar um certificado no Key Vault:

- Crie um certificado autoassinado para criar um par de chaves pública-privada e associá-lo a um certificado. O certificado será assinado por sua própria chave. 
- Crie um novo certificado manualmente para criar um par de chaves pública-privada e gerar uma solicitação de assinatura de certificado X. 509. A solicitação de assinatura pode ser assinada pela autoridade de registro ou autoridade de certificação. O certificado X509 assinado pode ser mesclado com o par de chaves pendente para concluir o certificado KV no Key Vault. Embora esse método exija mais etapas, ele fornece maior segurança, pois a chave privada é criada e restrita a Key Vault. Isso é explicado no diagrama a seguir. 

Examine os [métodos de criação de certificado do Azure keyvault](https://docs.microsoft.com/azure/key-vault/create-certificate) para obter detalhes adicionais.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Implantar Key Vault certificados em conjuntos de dimensionamento de máquinas virtuais de Cluster Service Fabric

Para implantar certificados de um keyvault colocalizado em um conjunto de dimensionamento de máquinas virtuais, use o conjunto de dimensionamento de máquinas virtuais [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). A seguir estão as propriedades do modelo do Resource Manager:

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
> O cofre deve ser habilitado para a implantação do modelo do Resource Manager.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Aplicar uma lista de controle de acesso (ACL) ao seu certificado para o Cluster Service Fabric

[Extensões do conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) Publicador Microsoft. Azure. o infabric é usado para configurar a segurança dos nós.
Para aplicar uma ACL aos seus certificados para seus Service Fabric processos de cluster, use as seguintes propriedades de modelo do Resource Manager:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Proteger um certificado de Cluster Service Fabric por nome comum

Para proteger o cluster de Service Fabric por `Common Name`de certificado, use a propriedade de modelo [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)do Resource Manager, da seguinte maneira:

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
> Os clusters de Service Fabric usarão o primeiro certificado válido encontrado no repositório de certificados do host. No Windows, esse será o certificado com a data de expiração mais recente que corresponde ao nome comum e à impressão digital do emissor.

Domínios do Azure, como *\<seu subdomínio\>. cloudapp.azure.com ou \<seu subdomínio\>. trafficmanager.net, pertencem à Microsoft. As autoridades de certificação não emitirão certificados para domínios para usuários não autorizados. A maioria dos usuários precisará comprar um domínio de um registrador ou ser um administrador de domínio autorizado, para que uma autoridade de certificação emita um certificado com esse nome comum.

Para obter detalhes adicionais sobre como configurar o serviço DNS para resolver seu domínio para um endereço IP da Microsoft, examine como configurar o [DNS do Azure para hospedar seu domínio](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Depois de delegar os servidores de nome de seus domínios aos servidores de nome de zona DNS do Azure, adicione os dois registros a seguir à sua zona DNS:
> - Um registro ' A ' para o APEX do domínio que não é um `Alias record set` a todos os endereços IP que seu domínio personalizado resolverá.
> - Um registro ' C' para subdomínios da Microsoft que você provisionou que não são `Alias record set`. Por exemplo, você pode usar o seu Gerenciador de tráfego ou o nome DNS do Load Balancer.

Para atualizar seu portal para exibir um nome DNS personalizado para o `"managementEndpoint"`de Cluster Service Fabric, atualize as propriedades de modelo do Resource Manager de cluster a seguir Service Fabric:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Criptografando Service Fabric valores secretos do pacote

Os valores comuns criptografados em pacotes de Service Fabric incluem credenciais de ACR (registro de contêiner do Azure), variáveis de ambiente, configurações e chaves de conta de armazenamento de plug-in de volume do Azure.

Para [configurar um certificado de criptografia e criptografar segredos em clusters do Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Gere um certificado autoassinado para criptografar seu segredo:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Use as instruções em [implantar certificados Key Vault para Service Fabric conjuntos de dimensionamento de máquinas virtuais de cluster](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) para implantar certificados Key Vault nos conjuntos de dimensionamento de máquinas virtuais do seu cluster Service Fabric.

Criptografe seu segredo usando o seguinte comando do PowerShell e, em seguida, atualize o manifesto do aplicativo Service Fabric com o valor criptografado:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Para [configurar um certificado de criptografia e criptografar segredos em clusters do Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Gere um certificado autoassinado para criptografar seus segredos:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Use as instruções em [implantar certificados Key Vault para Service Fabric conjuntos de dimensionamento de máquinas virtuais de cluster](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) para os conjuntos de dimensionamento de máquinas virtuais do seu cluster Service Fabric.

Criptografe seu segredo usando os comandos a seguir e, em seguida, atualize o manifesto do aplicativo Service Fabric com o valor criptografado:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Depois de criptografar os valores protegidos, [especifique segredos criptografados no aplicativo Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)e [descriptografe os segredos criptografados do código de serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Incluir certificado em aplicativos Service Fabric

Para dar ao seu aplicativo acesso aos segredos, inclua o certificado adicionando um elemento **SecretsCertificate** ao manifesto do aplicativo.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Autenticar Service Fabric aplicativos para recursos do Azure usando Identidade de Serviço Gerenciada (MSI)

Para saber mais sobre identidades gerenciadas para recursos do Azure, consulte [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).
Os clusters do Azure Service Fabric são hospedados em conjuntos de dimensionamento de máquinas virtuais, que dão suporte a [identidade de serviço gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Para obter uma lista de serviços que o MSI pode usar para autenticar no, consulte [Serviços do Azure que dão suporte à autenticação Azure Active Directory](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Para habilitar a identidade gerenciada atribuída pelo sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais ou de um conjunto de dimensionamento de máquinas virtuais existente, declare a seguinte propriedade de `"Microsoft.Compute/virtualMachinesScaleSets"`:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Consulte [o que são identidades gerenciadas para recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity) para obter mais informações.

Se você criou uma [identidade gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), declare o recurso a seguir em seu modelo para atribuí-lo ao conjunto de dimensionamento de máquinas virtuais. Substitua `\<USERASSIGNEDIDENTITYNAME\>` pelo nome da identidade gerenciada atribuída pelo usuário que você criou:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Antes que seu aplicativo Service Fabric possa usar uma identidade gerenciada, é necessário conceder permissões para os recursos do Azure com os quais ele precisa se autenticar.
Os comandos a seguir concedem acesso a um recurso do Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

No código do aplicativo Service Fabric, [obtenha um token de acesso](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) para Azure Resource Manager tornando um restante semelhante ao seguinte:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Seu aplicativo Service Fabric pode usar o token de acesso para autenticar para recursos do Azure que dão suporte a Active Directory.
O exemplo a seguir mostra como fazer isso para Cosmos DB recurso:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Linhas de base de segurança do Windows
[Recomendamos que você implemente uma configuração padrão do setor que seja amplamente conhecida e bem testada, como linhas de base de segurança da Microsoft, em oposição à criação de uma linha de base por conta própria](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines); uma opção para provisioná-los em seus conjuntos de dimensionamento de máquinas virtuais é usar o manipulador de extensão de DSC (configuração de estado desejado) do Azure para configurar as VMs à medida que elas ficam online, para que estejam executando o software de produção.

## <a name="azure-firewall"></a>Azure Firewall
[O Firewall do Azure é um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos de rede virtual do Azure. Trata-se de um firewall totalmente com estado como um serviço com alta disponibilidade interna e escalabilidade de nuvem irrestrita.](https://docs.microsoft.com/azure/firewall/overview) Isso habilita a capacidade de limitar o tráfego HTTP/S de saída a uma lista especificada de FQDN (nomes de domínio totalmente qualificados), incluindo curingas. Esta funcionalidade não requer terminação de SSL. É recomendável que você aproveite as [marcas de FQDN do firewall do Azure](https://docs.microsoft.com/azure/firewall/fqdn-tags) para atualizações do Windows e para habilitar o tráfego de rede para os pontos de extremidade do Microsoft Windows Update podem fluir pelo firewall. [Implantar o Firewall do Azure usando um modelo](https://docs.microsoft.com/azure/firewall/deploy-template) fornece um exemplo para a definição do modelo de recurso Microsoft. Network/azureFirewalls. Regras de firewall comuns a Service Fabric aplicativos é permitir o seguinte para sua rede virtual de clusters:

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

Essas regras de firewall complementam seus grupos de segurança de rede de saída permitidos, que incluem o infabric e o armazenamento, como destinos permitidos de sua rede virtual.

## <a name="tls-12"></a>TLS 1.2
[TSG](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Por padrão, o Windows Defender antivírus é instalado no Windows Server 2016. Para obter detalhes, consulte [Windows Defender antivírus no Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). A interface do usuário é instalada por padrão em alguns SKUs, mas não é necessária. Para reduzir qualquer impacto no desempenho e sobrecarga de consumo de recursos incorridas pelo Windows Defender, e se suas políticas de segurança permitirem que você exclua os processos e caminhos para software livre, declare o recurso de extensão do conjunto de dimensionamento de máquinas virtuais a seguir Propriedades do modelo de Gerenciador para excluir seu Service Fabric cluster das verificações:


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
> Consulte a documentação de antimalware para obter regras de configuração se você não estiver usando o Windows Defender. Não há suporte para o Windows Defender no Linux.

## <a name="platform-isolation"></a>Isolamento de plataforma
Por padrão, os aplicativos Service Fabric recebem acesso ao próprio Service Fabric do tempo de execução, que se manifesta em diferentes formas: [variáveis de ambiente](service-fabric-environment-variables-reference.md) apontando para caminhos de arquivo no host correspondente a arquivos de malha e aplicativo, um ponto de extremidade de comunicação entre processos que aceita solicitações específicas de aplicativo e o certificado de cliente que a malha espera que o aplicativo use para se autenticar. Na eventualidade de que o serviço hospede o código não confiável, é aconselhável desabilitar esse acesso ao tempo de execução da it-a menos que seja explicitamente necessário. O acesso ao tempo de execução é removido usando a seguinte declaração na seção de políticas do manifesto do aplicativo: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Passos seguintes

* Crie um cluster em VMs ou computadores executando o Windows Server: [Service Fabric a criação de cluster para o Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Criar um cluster em VMs ou computadores executando o Linux: [criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

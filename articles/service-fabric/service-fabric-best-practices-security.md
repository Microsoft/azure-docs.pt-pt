---
title: Melhores práticas de segurança do Service Fabric
description: Boas práticas e considerações de design para manter os clusters e aplicações do Azure Service Fabric seguros.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 90ffd1c01411982f56aed3332c499aa0c10b8a94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257606"
---
# <a name="azure-service-fabric-security"></a>Segurança do Azure Service Fabric 

Para mais informações sobre [as melhores práticas da Azure Security,](../security/index.yml)reveja as [melhores práticas de segurança da Azure Service Fabric](../security/fundamentals/service-fabric-best-practices.md)

## <a name="key-vault"></a>Cofre de Chaves

[Azure Key Vault](../key-vault/index.yml) é o serviço de gestão de segredos recomendado para aplicações e clusters de tecido de serviço Azure.
> [!NOTE]
> Se os certificados/segredos de um cofre de chaves forem implantados num Conjunto de Escala de Máquina Virtual como um conjunto de conjunto de escala de máquina virtual, então o conjunto de porta-chaves e de balança de máquinas virtuais deve ser co-localizado.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Criar certificado certificado da autoridade de certificação emitido Certificado de Tecido de Serviço

Um certificado Azure Key Vault pode ser criado ou importado para um Cofre-Chave. Quando um certificado Key Vault é criado, a chave privada é criada dentro do Cofre de Chaves e nunca exposta ao proprietário do certificado. Aqui estão as formas de criar um certificado no Cofre-Chave:

- Crie um certificado auto-assinado para criar um par de chaves público-privado e associá-lo a um certificado. O certificado será assinado pela sua própria chave. 
- Crie manualmente um novo certificado para criar um par de chaves público-privado e gerar um pedido de assinatura de certificado X.509. O pedido de assinatura pode ser assinado pela sua autoridade de registo ou autoridade de certificação. O certificado x509 assinado pode ser fundido com o par de chaves pendente para completar o certificado KV no Key Vault. Embora este método exija mais passos, proporciona-lhe uma maior segurança porque a chave privada é criada e restrita ao Key Vault. Isto é explicado no diagrama abaixo. 

[Reveja os métodos de criação de certificados Azure Keyvault](../key-vault/certificates/create-certificate.md) para obter mais detalhes.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Implementar certificados de cofre de chaves para conjuntos de escala de máquina virtual de cluster de tecido de serviço

Para implementar certificados de um keyvault co-localizado para um conjunto de balança de máquina virtual, utilize o conjunto de escala de máquina virtual [osProfile](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). Seguem-se as propriedades do modelo do Gestor de Recursos:

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
> O cofre deve ser ativado para a implementação do modelo do Gestor de Recursos.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Aplicar uma Lista de Controlo de Acesso (ACL) no seu certificado para o seu cluster de tecido de serviço

O editor [de extensões virtual de escala de máquinas](/cli/azure/vmss/extension?view=azure-cli-latest) Microsoft.Azure.ServiceFabric é utilizado para configurar a segurança dos nós.
Para aplicar um ACL nos seus certificados para os seus processos de Cluster de Tecido de Serviço, utilize as seguintes propriedades do modelo do Gestor de Recursos:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Garantir um certificado de cluster de tecido de serviço com nome comum

Para garantir o seu cluster de Tecido de Serviço por `Common Name` certificado, utilize o certificado de propriedade do modelo de gestor de [recursosCommonNames,](/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)da seguinte forma:

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
> Os clusters de tecido de serviço utilizarão o primeiro certificado válido que encontrar na loja de certificados do seu anfitrião. No Windows, este será o certificado com a data de validade mais recente que corresponde à sua impressão digital de Nome Comum e Emitente.

Os domínios Azure, tais como * \<YOUR SUBDOMAIN\> .cloudapp.azure.com ou \<YOUR SUBDOMAIN\> .trafficmanager.net, são propriedade da Microsoft. As autoridades de certificados não emitirão certificados para domínios a utilizadores não autorizados. A maioria dos utilizadores terá de adquirir um domínio a partir de um registrador, ou ser um administrador de domínio autorizado, para que uma autoridade de certificado lhe emita um certificado com esse nome comum.

Para obter mais detalhes sobre como configurar o Serviço DNS para resolver o seu domínio num endereço IP da Microsoft, reveja como configurar [o Azure DNS para hospedar o seu domínio](../dns/dns-delegate-domain-azure-dns.md).

> [!NOTE]
> Depois de delegar os servidores de nomes dos seus domínios nos servidores de nome da zona Azure DNS, adicione os seguintes dois registos à sua Zona DNS:
> - Um registo 'A' para o domínio APEX que não é um `Alias record set` endereço IP para todos os endereços IP que o seu domínio personalizado resolverá.
> - Um registo 'C' para sub-domínios da Microsoft que a provisionaste que não são um `Alias record set` . Por exemplo, pode utilizar o nome DNS do seu Gestor de Tráfego ou Do Balancer de Carga.

Para atualizar o seu portal para apresentar um nome DNS personalizado para o seu Cluster de Tecido de `"managementEndpoint"` Serviço, atualize as propriedades do modelo de Gestor de Recursos do Cluster de Tecido de Serviço:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Encriptação de serviços de produtos secretos

Valores comuns encriptados em Pacotes de Tecido de Serviço incluem credenciais de registo de contentores Azure (ACR), variáveis ambientais, configurações e chaves de armazenamento de plugin de volume Azure Volume.

Para [configurar um certificado de encriptação e encriptar segredos em clusters Windows](./service-fabric-application-secret-management-windows.md):

Gere um certificado auto-assinado para encriptar o seu segredo:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Utilize as instruções nos [certificados de cofre de chave de implantação para conjuntos de máquinas virtuais de cluster de tecido de serviço](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) para implantar certificados de cofre de chaves nos conjuntos de balanças de máquinas virtuais do seu cluster de tecido de serviço.

Criptografe o seu segredo utilizando o seguinte comando PowerShell e, em seguida, atualize o manifesto da sua aplicação Service Fabric com o valor encriptado:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Para [configurar um certificado de encriptação e encriptar segredos em clusters Linux:](./service-fabric-application-secret-management-linux.md)

Gere um certificado auto-assinado para encriptar os seus segredos:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Utilize as instruções nos [certificados de Abasco de Chave de Implantação para conjuntos de escala de máquina virtual de cluster](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) de tecido de serviço para os conjuntos de balanças de máquinas virtuais do seu cluster de tecido de serviço.

Criptografe o seu segredo utilizando os seguintes comandos e, em seguida, atualize o manifesto de aplicação de tecido de serviço com o valor encriptado:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Depois de encriptar os seus valores protegidos, [especifique os segredos encriptados na Aplicação do Tecido](./service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application)de Serviço e [desencripta os segredos encriptados do código de serviço](./service-fabric-application-secret-management.md#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Incluir certificado em aplicações de Tecido de Serviço

Para dar acesso à sua aplicação aos segredos, inclua o certificado adicionando um elemento **SecretsCertificate** ao manifesto de aplicação.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Autenticar aplicações de Tecido de Serviço para Recursos Azure utilizando identidade de serviço gerido (MSI)

Para conhecer as identidades geridas para os recursos da Azure, veja [o que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
Os clusters de tecido de serviço Azure estão hospedados em conjuntos de escala de máquina virtual, que [suportam identidade de serviço gerido.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)
Para obter uma lista de serviços a que a MSI pode ser usada para autenticar, consulte [os Serviços Azure que suportam a Autenticação do Diretório Ativo Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).


Para permitir a identidade gerida atribuída pelo sistema durante a criação de um conjunto de escala de máquinas virtuais ou de um conjunto de escala de máquinas virtuais existentes, declare a seguinte `"Microsoft.Compute/virtualMachinesScaleSets"` propriedade:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Veja [o que são identidades geridas para recursos Azure?](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss.md#system-assigned-managed-identity)

Se criou uma  [identidade gerida atribuída ao utilizador,](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)declare o seguinte recurso no seu modelo para atribuí-lo ao seu conjunto de escala de máquina virtual. `\<USERASSIGNEDIDENTITYNAME\>`Substitua-a pelo nome da identidade gerida atribuída pelo utilizador que criou:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Antes que a sua aplicação Service Fabric possa utilizar uma identidade gerida, devem ser concedidas permissões aos Recursos Azure com os mesmos necessários.
Os seguintes comandos concedem acesso a um Recurso Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

No seu código de aplicação De Tecido de Serviço, [obtenha um token de acesso para](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) O Gestor de Recursos Azure, fazendo um REST todos semelhantes aos seguintes:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

A sua aplicação Service Fabric pode então utilizar o token de acesso para autenticar os Recursos Azure que suportam o Ative Directory.
O exemplo a seguir mostra como fazê-lo para o recurso Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Linhas de base de segurança do Windows
[Recomendamos que implemente uma configuração padrão da indústria que seja amplamente conhecida e bem testada, como as linhas de base de segurança da Microsoft, em oposição à criação de uma linha de base por si mesmo;](/windows/security/threat-protection/windows-security-baselines) uma opção para o provisionar nos seus conjuntos de escala de máquina virtual é usar o controlador de extensão de configuração estatal (DSC) do Azure Desired, para configurar os VMs à medida que estes entram em linha, para que estejam a executar o software de produção.

## <a name="azure-firewall"></a>Azure Firewall
[O Azure Firewall é um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos da Rede Virtual Azure. É uma firewall totalmente imponente como um serviço com alta disponibilidade incorporada e escalabilidade de nuvem sem restrições.](../firewall/overview.md) isto permite limitar o tráfego HTTP/S de saída a uma lista especificada de nomes de domínio totalmente qualificados (FQDN) incluindo cartões selvagens. Esta função não requer a rescisão de TLS/SSL. Recomenda-se que aproveite as [tags FQDN do Azure Firewall](../firewall/fqdn-tags.md) para atualizações do Windows e que permita que o tráfego de rede para os pontos finais do Microsoft Windows Update possa fluir através da sua firewall. [Implementar a Azure Firewall utilizando um modelo](../firewall/deploy-template.md) fornece uma amostra para a definição do modelo de recurso Microsoft.Network/azureFirewalls. As regras de firewall comuns às Aplicações de Tecido de Serviço são para permitir o seguinte para a sua rede virtual de clusters:

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

Estas regras de firewall complementam os seus grupos de segurança de rede de saída permitidos, que incluiriam o ServiceFabric e o Storage, como destinos permitidos da sua rede virtual.

## <a name="tls-12"></a>TLS 1.2

O Microsoft [Azure recomenda](https://azure.microsoft.com/updates/azuretls12/) a todos os clientes a migração completa para soluções que suportem a segurança da camada de transporte (TLS) 1.2 e certifique-se de que o TLS 1.2 é utilizado por padrão.

Os serviços da Azure, incluindo o [Service Fabric,](https://techcommunity.microsoft.com/t5/azure-service-fabric/microsoft-azure-service-fabric-6-3-refresh-release-cu1-notes/ba-p/791493)concluíram os trabalhos de engenharia para remover a dependência dos protocolos TLS 1.0/1.1 e prestar apoio total aos clientes que pretendam ter as suas cargas de trabalho configuradas para aceitar e iniciar apenas ligações TLS 1.2.

Os clientes devem configurar as suas cargas de trabalho e aplicações no local, interagindo com os serviços da Azure para utilizar o TLS 1.2 por padrão. Aqui está como [configurar os nós e aplicações](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md) de cluster de tecido de serviço para usar uma versão TLS específica.

## <a name="windows-defender"></a>Windows Defender 

Por predefinição, o antivírus do Windows Defender encontra-se instalado no Windows Server 2016. Para mais informações, consulte [o Antivírus do Windows Defender no Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). A interface do utilizador é instalada por padrão em alguns SKUs, mas não é necessária. Para reduzir qualquer impacto de desempenho e consumo de recursos incorrido pelo Windows Defender, e se as suas políticas de segurança permitirem excluir processos e caminhos para software de código aberto, declare as seguintes propriedades do modelo do Gestor de Recursos de Extensão de Conjunto de Escala de Máquina Virtual para excluir o seu cluster de tecido de serviço de digitalizações:


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
> Consulte a documentação antimalware para obter regras de configuração se não estiver a utilizar o Windows Defender. O Windows Defender não é suportado no Linux.

## <a name="platform-isolation"></a>Isolamento da Plataforma
Por predefinição, as aplicações do Service Fabric têm acesso ao próprio tempo de funcionamento do Tecido de Serviço, o que se manifesta de diferentes formas: [variáveis ambientais](service-fabric-environment-variables-reference.md) que apontam para percursos de arquivo no anfitrião correspondentes a ficheiros de aplicação e tecido, um ponto final de comunicação inter-processante que aceita pedidos específicos da aplicação, e o certificado de cliente que a Fabric espera que o pedido utilize para se autenticar. Na eventualidade de o serviço se hospedar em código não fidedificado, é aconselhável desativar este acesso ao tempo de funcionação do SF - a menos que seja explicitamente necessário. O acesso ao tempo de funcionamento é removido utilizando a seguinte declaração na secção Políticas do manifesto de aplicação: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Passos seguintes

* Crie um cluster em VMs, ou computadores, executando o Windows Server: [Criação de cluster de tecido de serviço para o Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Crie um cluster em VMs, ou computadores, executando Linux: [Crie um cluster Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre [as opções de suporte do Tecido de Serviço.](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

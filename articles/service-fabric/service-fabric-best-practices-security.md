---
title: Melhores práticas de segurança do Service Fabric
description: Boas práticas e considerações de design para manter os clusters e aplicações Azure Service Fabric seguros.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: dcdc338bdcdb2c04f6b8894ccb358bc773b95c07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258931"
---
# <a name="azure-service-fabric-security"></a>Segurança do Azure Service Fabric 

Para mais informações sobre as [Melhores Práticas de Segurança azure,](https://docs.microsoft.com/azure/security/)reveja as [melhores práticas](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) de segurança do Tecido de Serviço Azure

## <a name="key-vault"></a>Cofre de Chaves

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) é o serviço de gestão de segredos recomendado para aplicações e clusters Azure Service Fabric.
> [!NOTE]
> Se os certificados/segredos de um cofre de chaves forem implantados para um conjunto de escala de máquina virtual como um segredo de escala de máquina virtual, então o cofre chave e o conjunto de escala de máquina virtual devem ser co-localizados.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Criar certificado de autoridade emitida certificado de tecido de serviço

Um certificado azure key vault pode ser criado ou importado para um cofre chave. Quando um certificado key vault é criado, a chave privada é criada dentro do Cofre chave e nunca exposta ao proprietário do certificado. Aqui estão as formas de criar um certificado no Cofre Chave:

- Crie um certificado auto-assinado para criar um par de chaves público-privado e associe-o a um certificado. O certificado será assinado pela sua própria chave. 
- Crie um novo certificado manualmente para criar um par de chaves público-privado e gerar um pedido de assinatura de certificado X.509. O pedido de assinatura pode ser assinado pela sua autoridade de registo ou autoridade de certificação. O certificado x509 assinado pode ser fundido com o par de chaves pendente para completar o certificado KV no Key Vault. Embora este método exija mais passos, proporciona-lhe maior segurança porque a chave privada é criada e restrita ao Cofre chave. Isto é explicado no diagrama abaixo. 

Reveja os métodos de criação de [certificados de keyvault Azure](https://docs.microsoft.com/azure/key-vault/create-certificate) para mais detalhes.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Implementar certificados chave vault para conjuntos de máquinas de cluster de tecido de serviço

Para implantar certificados de um cofre de chaves co-localizado para um conjunto de escala de máquina virtual, utilize [o osPerfil](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)de Conjunto de Escala de Máquina Virtual . Seguem-se as propriedades do modelo do Gestor de Recursos:

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
> O cofre deve ser ativado para a implantação do modelo do Gestor de Recursos.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Aplique uma Lista de Controlo de Acesso (ACL) no seu certificado para o seu cluster de Tecido de Serviço

[Extensões de conjunto de escala](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) de máquina virtual Microsoft.Azure.ServiceFabric é usado para configurar a segurança dos seus nódosos.
Para aplicar um ACL nos seus certificados para os seus processos de Cluster de Tecidos de Serviço, utilize as seguintes propriedades do modelo de Gestor de Recursos:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Garanta um certificado de cluster de tecido de serviço por nome comum

Para garantir o seu `Common Name`cluster de tecido de serviço por certificado, utilize o certificado de propriedade do modelo De gestor de [recursosCommonNames,](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)da seguinte forma:

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
> Os clusters service Fabric utilizarão o primeiro certificado válido que encontrar na loja de certificados do seu anfitrião. No Windows, este será o certificado com a data de validade mais recente que corresponde à sua impressão digital de Nome Comum e Emitente.

Os domínios Azure,\<tais\>como * \<YOUR\>SUBDOMAIN .cloudapp.azure.com ou YOUR SUBDOMAIN .trafficmanager.net, são propriedade da Microsoft. As autoridades de certificados não emitirão certificados para domínios a utilizadores não autorizados. A maioria dos utilizadores terá de adquirir um domínio a um registrador, ou ser um administrador de domínio autorizado, para que uma autoridade de certificados lhe emita um certificado com esse nome comum.

Para mais detalhes sobre como configurar o Serviço DNS para resolver o seu domínio num endereço IP da Microsoft, reveja como configurar o [DNS Azure para hospedar o seu domínio](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Depois de delegar os servidores de nome sinuosos dos seus domínios para os servidores de nome sinos do Azure, adicione os seguintes dois registos à sua Zona DNS:
> - Um registo 'A' para o domínio `Alias record set` APEX que não seja um para todos os Endereços IP que o seu domínio personalizado irá resolver.
> - Um registo 'C' para sub domínios da `Alias record set`Microsoft que forprovisionou que não são um . Por exemplo, pode utilizar o nome DNS do seu Gestor de Tráfego ou do Balancer de Carga.

Para atualizar o seu portal para exibir um `"managementEndpoint"`nome DNS personalizado para o seu Cluster de Tecidos de Serviço, atualize as propriedades do gestor de recursos do cluster de tecido de serviço:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Encriptar os valores secretos do pacote de tecido de serviço

Valores comuns que são encriptados em pacotes de tecido de serviço incluem credenciais de registo de contentores Azure (ACR), variáveis ambientais, configurações e chaves de conta de armazenamento plugin de volume Azure.

Para configurar um certificado de [encriptação e encriptar segredos em clusters do Windows:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows)

Gere um certificado auto-assinado para encriptar o seu segredo:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Utilize as instruções em ['Implementar certificados de cofre de chaves' para conjuntos](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) de máquinas virtuais de cluster de tecido de serviço para implantar certificados de cofre chave para os conjuntos de escala virtual de máquina do seu cluster de tecido de serviço.

Criptografe o seu segredo utilizando o seguinte comando PowerShell e, em seguida, atualize o manifesto de aplicação do Tecido de Serviço com o valor encriptado:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Para configurar um certificado de [encriptação e encriptar segredos em clusters Linux:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux)

Gere um certificado auto-assinado para encriptar os seus segredos:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Utilize as instruções em ['Implementar certificados chave vault' para conjuntos](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) de máquinas virtuais de cluster de tecido de serviço para os conjuntos de escala de máquina virtual do seu cluster de tecido de serviço.

Criptografe o seu segredo utilizando os seguintes comandos e, em seguida, atualize o manifesto de aplicação de tecido de serviço com o valor encriptado:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Depois de encriptar os seus valores protegidos, [especifique segredos encriptados na Aplicação](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)de Tecido de Serviço , e [descografie segredos encriptados do código de serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Incluir certificado nas aplicações de Tecido de Serviço

Para dar acesso à sua aplicação aos segredos, inclua o certificado adicionando um elemento **SecretsCertificate** ao manifesto de aplicação.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Aplicações authenticate Service Fabric para Recursos Azure usando identidade de serviço gerido (MSI)

Para aprender sobre identidades geridas para os recursos Do Azure, veja [o que é gerida identidades para os recursos Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)
Os clusters de tecido de serviço Azure estão hospedados em conjuntos de escala de máquina virtual, que suportam [a Identidade de Serviço Gerido.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources)
Para obter uma lista de serviços a que a MSI pode ser usada para autenticar, consulte [os Serviços Azure que suportam a Autenticação de DirectórioActivo Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication)


Para permitir o sistema atribuído à identidade gerida durante a criação de um conjunto de `"Microsoft.Compute/virtualMachinesScaleSets"` escala de máquinas virtuais ou de um conjunto de escala de máquinas virtuais existente, declare a seguinte propriedade:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Veja [o que são as identidades geridas para os recursos do Azure para](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity) mais informações.

Se criou uma identidade gerida atribuída ao [utilizador,](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)declare o seguinte recurso no seu modelo para atribuí-lo ao seu conjunto de escala de máquina virtual. Substitua `\<USERASSIGNEDIDENTITYNAME\>` pelo nome da identidade gerida atribuída pelo utilizador que criou:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Antes que a sua aplicação Service Fabric possa utilizar uma identidade gerida, devem ser concedidas permissões aos Recursos Azure com as que necessita de autenticar.
Os seguintes comandos concedem acesso a um Recurso Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

No seu código de aplicação Service Fabric, [obtenha um token](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) de acesso para o Gestor de Recursos Azure, tornando um REST tudo semelhante ao seguinte:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

A sua aplicação Service Fabric pode então utilizar o sinal de acesso para autenticar a Azure Resources que suporta o Ative Directory.
O exemplo que se segue mostra como fazê-lo para o recurso Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Linhas de base de segurança do Windows
[Recomendamos que implemente uma configuração padrão da indústria que seja amplamente conhecida e bem testada, como as linhas](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines)de segurança da Microsoft, em oposição à criação de uma linha de base por si mesma; Uma opção para o fornecimento destes nos seus Conjuntos de Escala de Máquina Virtual é utilizar o manipulador de extensão de configuração do Estado Do Estado (DSC) do Azure Desired State, para configurar os VMs à medida que entram online, pelo que estão a executar o software de produção.

## <a name="azure-firewall"></a>Azure Firewall
O Azure Firewall é um serviço de segurança de rede gerido e [baseado na nuvem que protege os seus recursos da Rede Virtual Azure. É uma firewall totalmente audabilizada como um serviço com alta disponibilidade incorporada e escalabilidade de nuvens sem restrições.](https://docs.microsoft.com/azure/firewall/overview) isto permite limitar o tráfego HTTP/S de saída a uma lista especificada de nomes de domínio totalmente qualificados (FQDN), incluindo cartões selvagens. Esta funcionalidade não requer terminação de SSL. Recomenda-se que aproveite [as etiquetas FQDN do Firewall Do Firewall para](https://docs.microsoft.com/azure/firewall/fqdn-tags) as Atualizações do Windows e que o tráfego de rede para os pontos finais do Microsoft Windows Update pode fluir através da sua firewall. [Implementar firewall azure usando um modelo](https://docs.microsoft.com/azure/firewall/deploy-template) fornece uma amostra para a definição de modelo de recurso Microsoft.Network/azureFirewalls. As regras de firewall comuns às Aplicações de Tecido de Serviço são para permitir o seguinte para a rede virtual dos seus clusters:

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

Estas regras de firewall complementam os seus grupos de segurança de rede de saída autorizados, que incluiriam serviceFabric e Armazenamento, como destinos permitidos da sua rede virtual.

## <a name="tls-12"></a>TLS 1.2
[TSG](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Por predefinição, o antivírus Do Windows Defender está instalado no Windows Server 2016. Para mais detalhes, consulte [o Antivírus Do Windows Defender no Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). A interface do utilizador é instalada por padrão em algumas SKUs, mas não é necessária. Para reduzir qualquer impacto de desempenho e sobrecarga de consumo de recursos incorrido pelo Windows Defender, e se as suas políticas de segurança lhe permitirem excluir processos e caminhos para software de código aberto, declare o seguinte Recurso de Extensão de Conjunto de Escala virtual de Máquinas Propriedades do modelo de gestor para excluir o seu cluster de Tecido de Serviço de digitalizações:


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
> Consulte a documentação Antimalware para obter regras de configuração se não estiver a utilizar o Windows Defender. O Windows Defender não é suportado no Linux.

## <a name="platform-isolation"></a>Isolamento da plataforma
Por predefinição, as aplicações de Tecido de Serviço têm acesso ao próprio tempo de funcionamento do Tecido de Serviço, que se manifesta de diferentes formas: [variáveis ambientais](service-fabric-environment-variables-reference.md) que apontam para caminhos de ficheiros no anfitrião correspondentes a ficheiros de aplicação e tecido, um ponto final de comunicação inter-processo que aceita pedidos específicos de aplicação, e o certificado de cliente que a Fabric espera que a aplicação utilize para se autenticar. Na eventualidade de o serviço se hospedar código não confiável, é aconselhável desativar este acesso ao tempo de funcionação do SF - a menos que seja explicitamente necessário. O acesso ao prazo de execução é removido utilizando a seguinte declaração na secção Políticas do manifesto de candidatura: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Passos seguintes

* Crie um cluster em VMs, ou computadores, executando o Windows Server: [Criação](service-fabric-cluster-creation-for-windows-server.md)de cluster de tecido de serviço para O Servidor do Windows .
* Crie um cluster em VMs, ou computadores, executando Linux: [Crie um cluster Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre as opções de suporte do [Tecido de Serviço.](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

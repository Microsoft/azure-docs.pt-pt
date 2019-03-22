---
title: Proteger um cluster do Azure Service Fabric no Windows através de certificados | Documentos da Microsoft
description: Proteger a comunicação dentro de um cluster autónomo ou no local do Azure Service Fabric, bem como entre clientes e o cluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 17dd2a8cf58066fda7f82ba53b048df8e9b89da8
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285252"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Proteger um cluster autónomo no Windows com certificados X.509
Este artigo descreve como proteger a comunicação entre os vários nós do seu cluster autónomo do Windows. Também descreve como autenticar clientes que se ligam a este cluster, utilizando certificados X.509. Autenticação garante que apenas os utilizadores autorizados podem aceder ao cluster e as aplicações implementadas e efetuar tarefas de gestão. Segurança do certificado deve ser ativada no cluster, quando o cluster é criado.  

Para obter mais informações sobre segurança do cluster, como o nó para nó segurança, segurança de cliente para nó e controlo de acesso baseado em funções, consulte [cenários de segurança do Cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Certificados de que precisa?
Para começar, [transferir o pacote de recursos de infraestrutura de serviço para o Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) para um de nós do cluster. No pacote transferido, encontrar um ficheiro de ClusterConfig.X509.MultiMachine.json. Abra o ficheiro e reveja a secção de segurança sob a secção de propriedades:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

Esta secção descreve os certificados de que precisa de proteger o seu cluster do Windows autônoma. Se especificar um certificado de cluster, defina o valor da ClusterCredentialType para _X509_. Se especificar um certificado de servidor para conexões externas, defina o ServerCredentialType como _X509_. Embora não seja obrigatório, recomendamos que tem ambos estes certificados para um cluster bem protegido. Se definir esses valores para *X509*, também tem de especificar os certificados correspondentes ou o Service Fabric lança uma exceção. Em alguns cenários, talvez queira especificar apenas o _ClientCertificateThumbprints_ ou o _ReverseProxyCertificate_. Nesses cenários, não precisa definir _ClusterCredentialType_ ou _ServerCredentialType_ para _X509_.


> [!NOTE]
> R [thumbprint](https://en.wikipedia.org/wiki/Public_key_fingerprint) é a identidade principal de um certificado. Para obter o thumbprint dos certificados que criar, ver [obter um thumbprint de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

A tabela seguinte lista os certificados de que precisa no seu programa de configuração do cluster:

| **Definição de CertificateInformation** | **Descrição** |
| --- | --- |
| ClusterCertificate |Recomendado para um ambiente de teste. Este certificado é necessário para proteger a comunicação entre os nós num cluster. Pode usar dois certificados diferentes, um site primário e um secundário, para atualização. Defina o thumbprint do certificado principal na secção de Thumbprint e que o elemento secundário nas variáveis de ThumbprintSecondary. |
| ClusterCertificateCommonNames |Recomendado para um ambiente de produção. Este certificado é necessário para proteger a comunicação entre os nós num cluster. Pode utilizar nomes do certificado de cluster comuns de um ou dois. O CertificateIssuerThumbprint corresponde ao thumbprint do emissor deste certificado. Se for utilizado mais de um certificado com o mesmo nome comum, é possível especificar vários thumbprints do emissor.|
| ClusterCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emissor do certificado de cluster. Nome comum e o nome de arquivo correspondente sob esta secção em vez de especificar o thumbprint do emissor em ClusterCertificateCommonNames, pode fornecer o emissor.  Isto torna mais fácil para certificados de emissor de cluster de rollover. Vários emissores podem ser especificados se o cluster de mais do que um certificado é utilizado. Uma listas de permissões de IssuerCommonName vazia todos os certificados nos arquivos de correspondentes especificadas em X509StoreNames.|
| ServerCertificate |Recomendado para um ambiente de teste. Este certificado é apresentado para o cliente quando tentar ligar a este cluster. Para sua comodidade, pode optar por utilizar o mesmo certificado para ClusterCertificate e ServerCertificate. Pode usar dois certificados de servidor diferente, um site primário e um secundário, para atualização. Defina o thumbprint do certificado principal na secção de Thumbprint e que o elemento secundário nas variáveis de ThumbprintSecondary. |
| ServerCertificateCommonNames |Recomendado para um ambiente de produção. Este certificado é apresentado para o cliente quando tentar ligar a este cluster. O CertificateIssuerThumbprint corresponde ao thumbprint do emissor deste certificado. Se for utilizado mais de um certificado com o mesmo nome comum, é possível especificar vários thumbprints do emissor. Para sua comodidade, pode optar por utilizar o mesmo certificado para ClusterCertificateCommonNames e ServerCertificateCommonNames. Pode utilizar nomes do certificado de servidor comuns de um ou dois. |
| ServerCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emissor do certificado de servidor. Nome comum e o nome de arquivo correspondente sob esta secção em vez de especificar o thumbprint do emissor em ServerCertificateCommonNames, pode fornecer o emissor.  Isto torna mais fácil para certificados de emissor de servidor de rollover. Vários emissores podem ser especificado se mais de um certificado de servidor é utilizado. Uma listas de permissões de IssuerCommonName vazia todos os certificados nos arquivos de correspondentes especificadas em X509StoreNames.|
| ClientCertificateThumbprints |Instale este conjunto de certificados nos clientes autenticados. Pode ter um número de certificados de cliente diferentes instalados nas máquinas que pretende permitir o acesso ao cluster. Defina o thumbprint de cada certificado na variável CertificateThumbprint. Se definir IsAdmin como *true*, o cliente com este certificado instalado no mesmo pode fazer administrador atividades de gestão no cluster. Se for IsAdmin *false*, o cliente com este certificado pode realizar as ações só são permitidas para direitos de acesso de usuário, normalmente, só de leitura. Para obter mais informações sobre as funções, consulte [controlo de acesso baseado em funções (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Defina o nome comum do certificado de cliente primeiro para o CertificateCommonName. O CertificateIssuerThumbprint é o thumbprint para o emissor deste certificado. Para saber mais sobre os nomes comuns e o emissor, veja [trabalhar com certificados](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emissor do certificado de cliente (funções de administrador e não-administrador). Nome comum e o nome de arquivo correspondente sob esta secção em vez de especificar o thumbprint do emissor em ClientCertificateCommonNames, pode fornecer o emissor.  Isto torna mais fácil para certificados de emissor de cliente de rollover. Vários emissores podem ser especificado se mais de um certificado de cliente é utilizado. Uma listas de permissões de IssuerCommonName vazia todos os certificados nos arquivos de correspondentes especificadas em X509StoreNames.|
| ReverseProxyCertificate |Recomendado para um ambiente de teste. Este certificado opcional pode ser especificado se pretender proteger o seu [proxy inverso](service-fabric-reverseproxy.md). Certifique-se de que reverseProxyEndpointPort está definido no nodeTypes se utilizar este certificado. |
| ReverseProxyCertificateCommonNames |Recomendado para um ambiente de produção. Este certificado opcional pode ser especificado se pretender proteger o seu [proxy inverso](service-fabric-reverseproxy.md). Certifique-se de que reverseProxyEndpointPort está definido no nodeTypes se utilizar este certificado. |

Eis um exemplo de configuração de cluster em que foram fornecidos o cluster, servidor e certificados de cliente. Para certificados de cluster/servidor/reverseProxy, o thumbprint e o nome comum não podem ser configurados em conjunto para o mesmo tipo de certificado.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Certificado de rollover
Quando utiliza um nome comum do certificado em vez de um thumbprint, certificado de rollover não exige uma atualização de configuração de cluster. Para atualizações de thumbprint do emissor, certifique-se de que a nova lista de thumbprint intersetar-com a lista antiga. Primeiro tem de fazer uma atualização de configuração com os novo thumbprints do emissor e, em seguida, instale os novos certificados (certificado de cluster/servidor e certificados de emissor) na loja. Mantenha o antigo certificado de emissor no arquivo de certificados para, pelo menos, duas horas depois de instalar o novo certificado de emissor.
Se estiver a utilizar arquivos de emissor, nenhuma atualização de configuração tem de ser efetuada emissor rollover de certificados. Instale o novo certificado de emissor com uma data de expiração último no arquivo de certificados correspondentes e remover o antigo certificado de emissor após algumas horas.

## <a name="acquire-the-x509-certificates"></a>Adquirir os certificados X.509
Para proteger a comunicação dentro do cluster, tem primeiro de obter certificados X.509 para os nós do cluster. Além disso, para limitar a ligação a este cluster para máquinas/utilizadores autorizados, terá de obter e instalar certificados para as máquinas de cliente.

Para clusters que executam cargas de trabalho de produção, utilize um [autoridade de certificação (AC)](https://en.wikipedia.org/wiki/Certificate_authority)-assinado certificado X.509 para proteger o cluster. Para obter mais informações sobre como obter estes certificados, consulte [como obter um certificado](https://msdn.microsoft.com/library/aa702761.aspx).

Para os clusters que utiliza para fins de teste, pode optar por utilizar um certificado autoassinado.

## <a name="optional-create-a-self-signed-certificate"></a>Opcional: Criar um certificado autoassinado
Uma forma de criar um certificado autoassinado que pode ser protegido corretamente é usar o script de CertSetup.ps1 na pasta SDK do Service Fabric no diretório C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Edite esse arquivo para alterar o nome predefinido do certificado. (Procure o valor CN = ServiceFabricDevClusterCert.) Execute este script como `.\CertSetup.ps1 -Install`.

Agora, exporte o certificado para um ficheiro. pfx com uma palavra-passe protegido. Em primeiro lugar, obtenha o thumbprint do certificado. 
1. Do **começar** menu, execute **gerir os certificados de computador**. 

2. Vá para o **Local\Pessoal.** pasta e localizar o certificado que criou. 

3. Clique duas vezes o certificado para abri-lo, selecione o **detalhes** separador e desloque para baixo até o **Thumbprint** campo. 

4. Remova os espaços e copie o valor do thumbprint para o seguinte comando do PowerShell. 

5. Alterar o `String` valor a uma palavra-passe segura adequada para protegê-lo e execute o seguinte no PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Para ver os detalhes de um certificado instalado no computador, execute o seguinte comando do PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Em alternativa, se tiver uma subscrição do Azure, siga os passos em [criar um cluster do Service Fabric com o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Instalar os certificados
Depois de ter certificados, pode instalá-los em nós do cluster. Os nós têm de ter o PowerShell mais recente do Windows 3.x instalado nos mesmos. Repita estes passos em cada nó de cluster e certificados de servidor e todos os certificados secundários.

1. Copie o ficheiro. pfx ou ficheiros para o nó.

2. Abra uma janela do PowerShell como administrador e introduza os seguintes comandos. Substitua *$pswd* com a palavra-passe que utilizou para criar este certificado. Substitua *$PfxFilePath* com o caminho completo do. pfx copiados para este nó.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Agora defina o controlo de acesso deste certificado para que o processo de Service Fabric, que é executado na conta de serviço de rede, pode utilizá-lo ao executar o script a seguir. Forneça o thumbprint do certificado e **serviço de rede** para a conta de serviço. Pode verificar que as ACLs no certificado estão corretas, abrindo o certificado no **começar** > **gerir certificados de computador** e observar **todas as tarefas**  >  **Gerir chaves privadas**.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Repita os passos anteriores para cada certificado de servidor. Também pode utilizar estes passos para instalar os certificados de cliente nas máquinas que pretende permitir o acesso ao cluster.

## <a name="create-the-secure-cluster"></a>Criar o cluster seguro
Depois de configurar a secção de segurança do ficheiro ClusterConfig.X509.MultiMachine.json, pode avançar para o [criar o cluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) secção para configurar os nós e criar o cluster autónomo. Lembre-se usar o arquivo de ClusterConfig.X509.MultiMachine.json enquanto cria o cluster. Por exemplo, o comando pode ser semelhante ao seguinte:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Depois de ter o Windows autónomo segura de cluster em execução com êxito e de ter configurado os clientes autenticados para ligar ao mesmo, siga os passos na secção [ligar a um cluster com o PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) para ligar ao mesmo. Por exemplo:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Pode, em seguida, executar outros comandos do PowerShell para trabalhar com este cluster. Por exemplo, pode executar [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para mostrar uma lista de nós neste cluster seguro.


Para remover o cluster, ligar para o nó no cluster onde transferiu o pacote de recursos de infraestrutura do serviço, abra uma linha de comando e vá para a pasta do pacote. Agora, execute o seguinte comando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Configuração de certificado incorreto poderá impedir que o cluster em breve durante a implementação. Para Self-diagnosticar problemas de segurança, dê uma olhada no caso o grupo de Visualizador **Applications and Services Logs** > **Microsoft Service Fabric**.
> 
> 


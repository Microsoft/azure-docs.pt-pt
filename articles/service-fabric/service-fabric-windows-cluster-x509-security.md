---
title: Proteger um cluster no Windows usando certificados
description: Proteger a comunicação em um cluster do Azure Service Fabric autônomo ou local, bem como entre clientes e o cluster.
author: dkkapur
ms.topic: conceptual
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 5a18f957dfb7143f403d5ac30ea184023021f12c
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613929"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Proteger um cluster autônomo no Windows usando certificados X. 509
Este artigo descreve como proteger a comunicação entre os vários nós do seu cluster do Windows autônomo. Ele também descreve como autenticar clientes que se conectam a esse cluster usando certificados X. 509. A autenticação garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados e executar tarefas de gerenciamento. A segurança do certificado deve ser habilitada no cluster quando o cluster é criado.  

Para obter mais informações sobre segurança de cluster, como segurança de nó para nó, segurança de cliente para nó e controle de acesso baseado em função, consulte [cenários de segurança de cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>De quais certificados você precisa?
Para começar, [Baixe o pacote Service Fabric para Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) para um dos nós no cluster. No pacote baixado, você encontra um arquivo ClusterConfig. X509. Multimachine. JSON. Abra o arquivo e examine a seção de segurança na seção Propriedades:

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

Esta seção descreve os certificados de que você precisa para proteger seu cluster autônomo do Windows. Se você especificar um certificado de cluster, defina o valor de ClusterCredentialType como _X509_. Se você especificar um certificado de servidor para conexões externas, defina o ServerCredentialType como _X509_. Embora não seja obrigatório, recomendamos que você tenha esses dois certificados para um cluster adequadamente protegido. Se você definir esses valores como *X509*, também deverá especificar os certificados correspondentes ou Service Fabric gera uma exceção. Em alguns cenários, talvez você queira especificar apenas _ClientCertificateThumbprints_ ou _ReverseProxyCertificate_. Nesses cenários, você não precisa definir _ClusterCredentialType_ ou _ServerCredentialType_ como _X509_.


> [!NOTE]
> Uma [impressão digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) é a identidade primária de um certificado. Para descobrir a impressão digital dos certificados que você cria, consulte [recuperar uma impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

A tabela a seguir lista os certificados necessários na configuração do cluster:

| **Configuração de CertificateInformation** | **Descrição** |
| --- | --- |
| ClusterCertificate |Recomendado para um ambiente de teste. Esse certificado é necessário para proteger a comunicação entre os nós em um cluster. Você pode usar dois certificados diferentes, um primário e um secundário, para atualização. Defina a impressão digital do certificado primário na seção impressão digital e a do secundário nas variáveis ThumbprintSecondary. |
| ClusterCertificateCommonNames |Recomendado para um ambiente de produção. Esse certificado é necessário para proteger a comunicação entre os nós em um cluster. Você pode usar um ou dois nomes comuns de certificado de cluster. O CertificateIssuerThumbprint corresponde à impressão digital do emissor deste certificado. Se mais de um certificado com o mesmo nome comum for usado, você poderá especificar várias impressões digitais do emissor.|
| ClusterCertificateIssuerStores |Recomendado para um ambiente de produção. Esse certificado corresponde ao emissor do certificado do cluster. Você pode fornecer o nome comum do emissor e o nome do repositório correspondente nesta seção em vez de especificar a impressão digital do emissor em ClusterCertificateCommonNames.  Isso facilita a substituição de certificados de emissor de cluster. Vários emissores poderão ser especificados se mais de um certificado de cluster for usado. Uma lista branca vazia IssuerCommonName todos os certificados nos repositórios correspondentes especificados em X509StoreNames.|
| ServerCertificate |Recomendado para um ambiente de teste. Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. Para sua conveniência, você pode optar por usar o mesmo certificado para ClusterCertificate e ServerCertificate. Você pode usar dois certificados de servidor diferentes, um primário e um secundário, para atualização. Defina a impressão digital do certificado primário na seção impressão digital e a do secundário nas variáveis ThumbprintSecondary. |
| ServerCertificateCommonNames |Recomendado para um ambiente de produção. Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. O CertificateIssuerThumbprint corresponde à impressão digital do emissor deste certificado. Se mais de um certificado com o mesmo nome comum for usado, você poderá especificar várias impressões digitais do emissor. Para sua conveniência, você pode optar por usar o mesmo certificado para ClusterCertificateCommonNames e ServerCertificateCommonNames. Você pode usar um ou dois nomes comuns de certificado de servidor. |
| ServerCertificateIssuerStores |Recomendado para um ambiente de produção. Esse certificado corresponde ao emissor do certificado do servidor. Você pode fornecer o nome comum do emissor e o nome do repositório correspondente nesta seção em vez de especificar a impressão digital do emissor em ServerCertificateCommonNames.  Isso facilita a substituição dos certificados do emissor do servidor. Vários emissores poderão ser especificados se mais de um certificado de servidor for usado. Uma lista branca vazia IssuerCommonName todos os certificados nos repositórios correspondentes especificados em X509StoreNames.|
| ClientCertificateThumbprints |Instale esse conjunto de certificados nos clientes autenticados. Você pode ter vários certificados de cliente diferentes instalados nos computadores que você deseja permitir acesso ao cluster. Defina a impressão digital de cada certificado na variável CertificateThumbprint. Se você definir ISAdmin como *true*, o cliente com esse certificado instalado poderá realizar atividades de gerenciamento do administrador no cluster. Se ISAdmin for *false*, o cliente com esse certificado poderá executar as ações permitidas somente para direitos de acesso do usuário, normalmente somente leitura. Para obter mais informações sobre funções, consulte [RBAC (controle de acesso baseado em função)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Defina o nome comum do primeiro certificado de cliente para o CertificateCommonName. O CertificateIssuerThumbprint é a impressão digital para o emissor deste certificado. Para saber mais sobre nomes comuns e o emissor, confira [trabalhar com certificados](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Recomendado para um ambiente de produção. Esse certificado corresponde ao emissor do certificado do cliente (funções de administrador e não administrador). Você pode fornecer o nome comum do emissor e o nome do repositório correspondente nesta seção em vez de especificar a impressão digital do emissor em ClientCertificateCommonNames.  Isso facilita a substituição de certificados do emissor do cliente. Vários emissores poderão ser especificados se mais de um certificado de cliente for usado. Uma lista branca vazia IssuerCommonName todos os certificados nos repositórios correspondentes especificados em X509StoreNames.|
| ReverseProxyCertificate |Recomendado para um ambiente de teste. Esse certificado opcional pode ser especificado se você quiser proteger seu [proxy reverso](service-fabric-reverseproxy.md). Verifique se reverseProxyEndpointPort está definido em nodeTypes se você usar esse certificado. |
| ReverseProxyCertificateCommonNames |Recomendado para um ambiente de produção. Esse certificado opcional pode ser especificado se você quiser proteger seu [proxy reverso](service-fabric-reverseproxy.md). Verifique se reverseProxyEndpointPort está definido em nodeTypes se você usar esse certificado. |

Aqui está um exemplo de configuração de cluster em que os certificados de cluster, servidor e cliente foram fornecidos. Para certificados cluster/servidor/reverseProxy, a impressão digital e o nome comum não podem ser configurados juntos para o mesmo tipo de certificado.

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

## <a name="certificate-rollover"></a>Substituição de certificado
Quando você usa um nome comum de certificado em vez de uma impressão digital, a substituição de certificado não requer uma atualização de configuração de cluster. Para atualizações de impressão digital do emissor, verifique se a nova lista de impressão digital está interseccionada com a lista antiga. Primeiro, você precisa fazer uma atualização de configuração com as novas impressões digitais do emissor e, em seguida, instalar os novos certificados (certificado do cluster/servidor e certificados do emissor) no repositório. Mantenha o antigo certificado do emissor no repositório de certificados por pelo menos duas horas depois de instalar o novo certificado do emissor.
Se você estiver usando repositórios do emissor, nenhuma atualização de configuração precisará ser executada para substituição do certificado do emissor. Instale o novo certificado do emissor com uma última data de expiração no repositório de certificados correspondente e remova o antigo certificado do emissor após algumas horas.

## <a name="acquire-the-x509-certificates"></a>Adquirir os certificados X. 509
Para proteger a comunicação no cluster, primeiro você precisa obter certificados X. 509 para seus nós de cluster. Além disso, para limitar a conexão a esse cluster a computadores/usuários autorizados, você precisa obter e instalar certificados para os computadores cliente.

Para clusters que estão executando cargas de trabalho de produção, use um certificado X. 509 assinado por [AC (autoridade](https://en.wikipedia.org/wiki/Certificate_authority)de certificação) para proteger o cluster. Para obter mais informações sobre como obter esses certificados, consulte [como obter um certificado](https://msdn.microsoft.com/library/aa702761.aspx).

Para clusters que você usa para fins de teste, você pode optar por usar um certificado autoassinado.

## <a name="optional-create-a-self-signed-certificate"></a>Opcional: criar um certificado autoassinado
Uma maneira de criar um certificado autoassinado que pode ser protegido corretamente é usar o script script certsetup. ps1 na pasta Service Fabric SDK no diretório C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Edite esse arquivo para alterar o nome padrão do certificado. (Procure o valor CN = ServiceFabricDevClusterCert.) Execute este script como `.\CertSetup.ps1 -Install`.

Agora, exporte o certificado para um arquivo. pfx com uma senha protegida. Primeiro, obtenha a impressão digital do certificado. 
1. No menu **Iniciar** , execute **gerenciar certificados de computador**. 

2. Vá para a pasta **local/pessoal** e localize o certificado que você criou. 

3. Clique duas vezes no certificado para abri-lo, selecione a guia **detalhes** e role para baixo até o campo **impressão digital** . 

4. Remova os espaços e copie o valor da impressão digital para o comando do PowerShell a seguir. 

5. Altere o valor de `String` para uma senha segura adequada para protegê-lo e execute o seguinte no PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Para ver os detalhes de um certificado instalado no computador, execute o seguinte comando do PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Como alternativa, se você tiver uma assinatura do Azure, siga as etapas em [criar um Service Fabric cluster usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Instalar os certificados
Depois de ter certificados, você pode instalá-los nos nós do cluster. Seus nós precisam ter o Windows PowerShell 3. x mais recente instalado neles. Repita essas etapas em cada nó para certificados de cluster e de servidor e quaisquer certificados secundários.

1. Copie o arquivo. pfx ou os arquivos para o nó.

2. Abra uma janela do PowerShell como administrador e insira os comandos a seguir. Substitua *$PSWD* pela senha que você usou para criar esse certificado. Substitua *$PfxFilePath* pelo caminho completo do. pfx copiado para este nó.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Agora, defina o controle de acesso nesse certificado para que o processo de Service Fabric, que é executado na conta de serviço de rede, possa usá-lo executando o script a seguir. Forneça a impressão digital do certificado e do **serviço de rede** para a conta de serviço. Você pode verificar se as ACLs no certificado estão corretas abrindo o certificado em **iniciar** > **gerenciar certificados de computador** e examinando **todas as tarefas** > **gerenciar chaves privadas**.
   
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
4. Repita as etapas anteriores para cada certificado de servidor. Você também pode usar estas etapas para instalar os certificados de cliente nos computadores que você deseja permitir acesso ao cluster.

## <a name="create-the-secure-cluster"></a>Criar o cluster seguro
Depois de configurar a seção de segurança do arquivo ClusterConfig. X509. Multimachine. JSON, você pode prosseguir para a seção [criar o cluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) para configurar os nós e criar o cluster autônomo. Lembre-se de usar o arquivo ClusterConfig. X509. Multimachine. JSON enquanto cria o cluster. Por exemplo, o comando pode ser semelhante ao seguinte:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Depois que o cluster autônomo do Windows seguro for executado com êxito e tiver configurado os clientes autenticados para se conectarem a ele, siga as etapas na seção [conectar-se a um cluster usando o PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) para se conectar a ele. Por exemplo:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Em seguida, você pode executar outros comandos do PowerShell para trabalhar com esse cluster. Por exemplo, você pode executar [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para mostrar uma lista de nós neste cluster seguro.


Para remover o cluster, conecte-se ao nó no cluster em que você baixou o pacote de Service Fabric, abra uma linha de comando e vá para a pasta do pacote. Execute o seguinte comando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> A configuração de certificado incorreta pode impedir que o cluster seja lançado durante a implantação. Para diagnosticar problemas de segurança automaticamente, consulte os logs de **aplicativos e serviços** do Visualizador de Eventos Group > **Microsoft-Service Fabric**.
> 
> 


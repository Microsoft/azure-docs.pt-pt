---
title: Proteja um cluster no Windows usando certificados
description: Comunicação segura dentro de um cluster de tecido de serviço Azure ou no local, bem como entre clientes e o cluster.
author: dkkapur
ms.topic: conceptual
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 5a18f957dfb7143f403d5ac30ea184023021f12c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75613929"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Proteja um cluster autónomo no Windows utilizando certificados X.509
Este artigo descreve como garantir a comunicação entre os vários nós do seu cluster Windows autónomo. Também descreve como autenticar clientes que se ligam a este cluster utilizando certificados X.509. A autenticação garante que apenas os utilizadores autorizados podem aceder ao cluster e às aplicações implementadas e executar tarefas de gestão. A segurança do certificado deve ser ativada no cluster quando o cluster for criado.  

Para obter mais informações sobre a segurança do cluster, tais como segurança nó-a-nó, segurança cliente-a-nó e controlo de acesso baseado em papéis, consulte [cenários](service-fabric-cluster-security.md)de segurança do Cluster .

## <a name="which-certificates-do-you-need"></a>Que certificados precisa?
Para começar, [descarregue o pacote Service Fabric para Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) para um dos nós do seu cluster. No pacote descarregado, encontra um ficheiro ClusterConfig.X509.MultiMachine.json. Abra o ficheiro e reveja a secção de segurança sob a secção de propriedades:

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

Esta secção descreve os certificados que necessita para proteger o seu cluster Windows autónomo. Se especificar um certificado de cluster, dejuste o valor do ClusterCredentialType para _X509_. Se especificar um certificado de servidor para ligações externas, detete o ServerCredentialType para _X509_. Embora não seja obrigatório, recomendamos que tenha ambos os certificados para um cluster devidamente seguro. Se definir estes valores para *X509,* também deve especificar os certificados correspondentes ou o Tecido de Serviço abre uma exceção. Em alguns cenários, é possível especificar apenas as _impressões de Polegares do ClienteCertificateou_ o _ReverseProxyCertificate_. Nesses cenários, não é necessário definir _clusterCredentialType_ ou _ServerCredentialType_ para _X509_.


> [!NOTE]
> Uma [impressão digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) é a identidade primária de um certificado. Para descobrir a impressão digital dos certificados que cria, consulte [Recuperar uma impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

A tabela que se segue lista os certificados de que necessita na configuração do seu cluster:

| **Definição de Informação de Certificados** | **Descrição** |
| --- | --- |
| Certificado de Cluster |Recomendado para um ambiente de teste. Este certificado é necessário para assegurar a comunicação entre os nós de um cluster. Pode usar dois certificados diferentes, um primário e um secundário, para upgrade. Detete a impressão digital do certificado primário na secção Thumbprint e a do secundário nas variáveis ThumbprintSecondary. |
| Nomes Comuns clusterCertificate |Recomendado para um ambiente de produção. Este certificado é necessário para assegurar a comunicação entre os nós de um cluster. Pode usar um ou dois nomes comuns de certificado de cluster. A impressão de Impressão Digital do Certificado Emite corresponde à impressão digital do emitente deste certificado. Se for utilizado mais de um certificado com o mesmo nome comum, pode especificar várias impressões digitais emitentes.|
| ClusterCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emitente do certificado de cluster. Pode fornecer o nome comum do emitente e o nome correspondente da loja nesta secção, em vez de especificar a impressão digital do emitente em ClusterCertificateCommonNames.  Isto facilita a reversão dos certificados de emitentes de cluster. Vários emitentes podem ser especificados se for utilizado mais de um certificado de cluster. Um Emitente comum nome emcausa vazio lista todos os certificados nas lojas correspondentes especificados em X509StoreNames.|
| Certificado de servidor |Recomendado para um ambiente de teste. Este certificado é apresentado ao cliente quando tenta ligar-se a este cluster. Por conveniência, pode optar por utilizar o mesmo certificado para ClusterCertificate e ServerCertificate. Pode utilizar dois certificados de servidor diferentes, um primário e um secundário, para upgrade. Detete a impressão digital do certificado primário na secção Thumbprint e a do secundário nas variáveis ThumbprintSecondary. |
| Nomes comuns de certificados de servidor |Recomendado para um ambiente de produção. Este certificado é apresentado ao cliente quando tenta ligar-se a este cluster. A impressão de Impressão Digital do Certificado Emite corresponde à impressão digital do emitente deste certificado. Se for utilizado mais de um certificado com o mesmo nome comum, pode especificar várias impressões digitais emitentes. Por conveniência, pode optar por utilizar o mesmo certificado para ClusterCertificateCommonNames e ServerCertificateCommonNames. Pode utilizar um ou dois nomes comuns de um ou dois certificados de servidor. |
| ServerCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emitente do certificado do servidor. Pode fornecer o nome comum do emitente e o nome correspondente da loja nesta secção, em vez de especificar a impressão digital do emitente em ServerCertificateCommonNames.  Isto facilita a reversão dos certificados de emitentes de servidores. Vários emitentes podem ser especificados se for utilizado mais de um certificado de servidor. Um Emitente comum nome emcausa vazio lista todos os certificados nas lojas correspondentes especificados em X509StoreNames.|
| Impressões de Polegarde ClienteCertificate |Instale este conjunto de certificados nos clientes autenticados. Pode ter vários certificados de cliente diferentes instalados nas máquinas que pretende permitir o acesso ao cluster. Detete a impressão digital de cada certificado na variável CertificateThumbprint. Se definir a IsAdmin *como verdadeira,* o cliente com este certificado instalado no mesmo pode fazer atividades de gestão de administrador no cluster. Se a IsAdmin for *falsa,* o cliente com este certificado pode executar as ações apenas permitidas para direitos de acesso ao utilizador, tipicamente apenas para leitura. Para obter mais informações sobre as funções, consulte [O Controlo de Acesso baseado em Funções (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| Nomes Comuns de Certificados de Cliente |Detete o nome comum do primeiro certificado de cliente para o CertificateCommonName. O CertificateIssuerThumbprint é a impressão digital para o emitente deste certificado. Para saber mais sobre nomes comuns e o emitente, consulte [Trabalhar com certificados.](https://msdn.microsoft.com/library/ms731899.aspx) |
| ClientCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emitente do certificado de cliente (tanto as funções de administrador como de administrador). Pode fornecer o nome comum do emitente e o nome correspondente da loja nesta secção, em vez de especificar a impressão digital do emitente em Nomes Comuns do Cliente.  Isto facilita a reversão dos certificados de emitentes de clientes. Vários emitentes podem ser especificados se for utilizado mais de um certificado de cliente. Um Emitente comum nome emcausa vazio lista todos os certificados nas lojas correspondentes especificados em X509StoreNames.|
| Certificado de Procuração Inversa |Recomendado para um ambiente de teste. Este certificado opcional pode ser especificado se pretender assegurar o seu [proxy inverso](service-fabric-reverseproxy.md). Certifique-se de que o ProxyEndpointPort invertido está definido em nósTypes se utilizar este certificado. |
| Nomes Comuns de Certificados De Procuração Invertido |Recomendado para um ambiente de produção. Este certificado opcional pode ser especificado se pretender assegurar o seu [proxy inverso](service-fabric-reverseproxy.md). Certifique-se de que o ProxyEndpointPort invertido está definido em nósTypes se utilizar este certificado. |

Aqui está uma configuração de cluster de exemplo onde foram fornecidos os certificados de cluster, servidor e cliente. Para os certificados de cluster/servidor/inversão proxy, a impressão digital e o nome comum não podem ser configurados em conjunto para o mesmo tipo de certificado.

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

## <a name="certificate-rollover"></a>Capotamento de certificado
Quando utiliza um nome comum de certificado em vez de uma impressão digital, o capotamento do certificado não requer uma atualização de configuração do cluster. Para atualizações de impressão digital emitentes, certifique-se de que a nova lista de impressões digitais se cruza com a lista antiga. Primeiro tem de fazer uma atualização de config com as novas impressões digitais do emitente e, em seguida, instalar os novos certificados (certificado de cluster/servidor e certificados de emitente) na loja. Guarde o antigo certificado de emitente na loja de certificados durante pelo menos duas horas após a instalação do novo certificado de emitente.
Se estiver a utilizar lojas emitentes, então não é necessário realizar nenhuma atualização de config para a capotação do certificado emitente. Instale o novo certificado de emitente com uma última data de validade no armazém de certificados correspondente e retire o antigo certificado de emitente após algumas horas.

## <a name="acquire-the-x509-certificates"></a>Adquirir os certificados X.509
Para garantir a comunicação dentro do cluster, primeiro é necessário obter certificados X.509 para os seus nós de cluster. Além disso, para limitar a ligação a este cluster a máquinas/utilizadores autorizados, é necessário obter e instalar certificados para as máquinas clientes.

Para os clusters que estão a executar cargas de trabalho de produção, utilize um certificado x.509 assinado pela autoridade de [certificados](https://en.wikipedia.org/wiki/Certificate_authority)para assegurar o cluster. Para obter mais informações sobre como obter estes certificados, consulte [Como obter um certificado](https://msdn.microsoft.com/library/aa702761.aspx).

Para os clusters que utiliza para fins de teste, pode optar por utilizar um certificado auto-assinado.

## <a name="optional-create-a-self-signed-certificate"></a>Opcional: Criar um certificado auto-assinado
Uma forma de criar um certificado auto-assinado que pode ser protegido corretamente é usar o script CertSetup.ps1 na pasta SDK de tecido de serviço no diretório C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Editar este ficheiro para alterar o nome predefinido do certificado. (Procure o valor CN=ServiceFabricDevClusterCert.) Execute este `.\CertSetup.ps1 -Install`guião como.

Agora exporte o certificado para um ficheiro .pfx com uma senha protegida. Primeiro, pegue a impressão digital do certificado. 
1. A partir do menu **Iniciar,** executar **Gerir certificados de computador**. 

2. Vá à pasta Pessoal do **Computador Local** e encontre o certificado que criou. 

3. Clique duas vezes no certificado para o abrir, selecione o separador **Details** e desloque-se até ao campo **de impressão digital.** 

4. Retire os espaços e copie o valor da impressão digital no seguinte comando PowerShell. 

5. Mude `String` o valor para uma senha segura adequada para protegê-lo e executar o seguinte na PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Para ver os detalhes de um certificado instalado na máquina, execute o seguinte comando PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Em alternativa, se tiver uma subscrição Azure, siga os passos no Create a Service Fabric cluster utilizando o Gestor de [Recursos Azure](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Instalar os certificados
Depois de ter certificados, pode instalá-los nos nós do cluster. Os seus nós precisam de ter os mais recentes Windows PowerShell 3.x instalados nos mesmos. Repita estes passos em cada nó para certificados de cluster e servidor e quaisquer certificados secundários.

1. Copie o ficheiro ou ficheiros .pfx para o nó.

2. Abra uma janela PowerShell como administrador e introduza os seguintes comandos. Substitua *$pswd* com a palavra-passe que usou para criar este certificado. Substitua *$PfxFilePath* pelo percurso completo do .pfx copiado para este nó.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Agora, detete te o controlo de acesso deste certificado para que o processo de Tecido de Serviço, que funciona sob a conta do Serviço de Rede, possa usá-lo executando o seguinte script. Forneça a impressão digital do certificado e do **SERVIÇO DE REDE** para a conta de serviço. Pode verificar se os ACLs do certificado estão corretos abrindo o certificado em **Certificados**de > **computador** Start Manage e olhando para **todas as tarefas** > gerir**chaves privadas**.
   
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
Depois de configurar a secção de segurança do ficheiro ClusterConfig.X509.MultiMachine.json, pode proceder à secção [Criar a secção](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) de cluster para configurar os nós e criar o cluster autónomo. Lembre-se de utilizar o ficheiro ClusterConfig.X509.MultiMachine.json enquanto cria o cluster. Por exemplo, o seu comando pode parecer o seguinte:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Depois de ter o cluster de Windows autónomo seguro a funcionar com sucesso e ter configurado os clientes autenticados para se ligarem ao mesmo, siga os passos na secção Connect a um cluster que utilize o [PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) para se ligar ao mesmo. Por exemplo:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Em seguida, pode executar outros comandos PowerShell para trabalhar com este cluster. Por exemplo, pode executar [O Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para mostrar uma lista de nós neste cluster seguro.


Para remover o cluster, ligue-se ao nó do cluster onde descarregou o pacote Service Fabric, abra uma linha de comando e vá para a pasta do pacote. Agora executa o seguinte comando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> A configuração incorreta do certificado pode impedir que o cluster apareça durante a implantação. Para auto-diagnosticar problemas de segurança, procure no grupo Evento **Aplicações e Serviços Logs** > **Microsoft-Service Fabric**.
> 
> 


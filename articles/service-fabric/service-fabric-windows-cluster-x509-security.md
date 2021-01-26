---
title: Garantir um cluster no Windows utilizando certificados
description: Comunicação segura dentro de um cluster autónomo ou no local do Azure Service Fabric, bem como entre clientes e o cluster.
ms.topic: conceptual
ms.date: 10/15/2017
ms.openlocfilehash: d75c644be47ea44f6a8a6ccac91b785af0132833
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791042"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Garantir um cluster autónomo no Windows utilizando certificados X.509
Este artigo descreve como garantir a comunicação entre os vários nós do seu cluster Windows autónomo. Também descreve como autenticar clientes que se conectam a este cluster utilizando certificados X.509. A autenticação garante que apenas os utilizadores autorizados podem aceder ao cluster e às aplicações implementadas e executar tarefas de gestão. A segurança do certificado deve ser ativada no cluster quando o cluster for criado.  

Para obter mais informações sobre a segurança do cluster, tais como a segurança do nó-a-nó, a segurança do cliente-a-nó e o controlo de acesso baseado em funções, consulte [cenários](service-fabric-cluster-security.md)de segurança do Cluster .

## <a name="which-certificates-do-you-need"></a>Que certificados precisa?
Para começar, [descarregue o pacote 'Service Fabric for Windows Server'](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) para um dos nós do seu cluster. No pacote descarregado, encontra-se um ClusterConfig.X509.MultiMachine.jsno ficheiro. Abra o ficheiro e reveja a secção de segurança na secção propriedades:

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

Esta secção descreve os certificados de que necessita para proteger o seu cluster Windows autónomo. Se especificar um certificado de cluster, desloque o valor do ClusterCredentialType para _X509_. Se especificar um certificado de servidor para ligações externas, desloque o ServerCredentialType para _X509_. Embora não seja obrigatório, recomendamos que tenha ambos estes certificados para um cluster devidamente seguro. Se definir estes valores para *X509,* também deve especificar os certificados correspondentes ou o Tecido de Serviço lança uma exceção. Em alguns cenários, é possível que queira especificar apenas as _impressões de ClientCertificateThumb ou_ o _ReverseProxyCertificate_. Nesses cenários, não é necessário definir _ClusterCredentialType_ ou _ServerCredentialType_ para _X509_.


> [!NOTE]
> Uma [impressão digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) é a identidade primária de um certificado. Para obter a impressão digital dos certificados que criar, consulte [Recuperar uma impressão digital de um certificado.](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)
> 
> 

A tabela a seguir lista os certificados de que necessita na configuração do seu cluster:

| **Definição de Informação de Certificados** | **Descrição** |
| --- | --- |
| ClusterCertificate |Recomendado para um ambiente de teste. Este certificado é necessário para assegurar a comunicação entre os nós num cluster. Pode utilizar dois certificados diferentes, um primário e um secundário, para upgrade. Desaver a impressão digital do certificado primário na secção de impressão digital e a do secundário nas variáveis MiniaturaS. |
| ClusterCertificateCommonNames |Recomendado para um ambiente de produção. Este certificado é necessário para assegurar a comunicação entre os nós num cluster. Pode utilizar um ou dois nomes comuns de certificado de cluster. O CertificadoIssuerThumbprint corresponde à impressão digital do emitente deste certificado. Se for utilizado mais de um certificado com o mesmo nome comum, pode especificar várias impressões digitais emitentes.|
| ClusterCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emitente do certificado de agrupamento. Pode fornecer o nome comum do emitente e o nome correspondente da loja nesta secção em vez de especificar a impressão digital do emitente sob os nomes do ClusterCertificateCommonNames.  Isto facilita a capotamento dos certificados de emitente de cluster. Vários emitentes podem ser especificados se for utilizado mais de um certificado de cluster. Um Emitente VazioCommonName permite todos os certificados nas lojas correspondentes especificadas em X509StoreNames.|
| ServerCertificate |Recomendado para um ambiente de teste. Este certificado é apresentado ao cliente quando tenta ligar-se a este cluster. Por conveniência, pode optar por utilizar o mesmo certificado para ClusterCertificate e ServerCertificate. Pode utilizar dois certificados de servidor diferentes, um primário e um secundário, para atualização. Desaver a impressão digital do certificado primário na secção de impressão digital e a do secundário nas variáveis MiniaturaS. |
| ServidorCertiadoCommonnames |Recomendado para um ambiente de produção. Este certificado é apresentado ao cliente quando tenta ligar-se a este cluster. O CertificadoIssuerThumbprint corresponde à impressão digital do emitente deste certificado. Se for utilizado mais de um certificado com o mesmo nome comum, pode especificar várias impressões digitais emitentes. Por conveniência, pode optar por utilizar o mesmo certificado para ClusterCertificateCommonNames e ServerCertificateCommonNames. Pode utilizar um ou dois nomes comuns de certificado de servidor. |
| ServerCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emitente do certificado do servidor. Pode fornecer o nome comum do emitente e o nome correspondente da loja nesta secção em vez de especificar a impressão digital do emitente em serverCertificateCommonNames.  Isto facilita a capotamento dos certificados de emitente do servidor. Vários emitentes podem ser especificados se for utilizado mais de um certificado de servidor. Um Emitente VazioCommonName permite todos os certificados nas lojas correspondentes especificadas em X509StoreNames.|
| Certificado de ClienteSThumbprints |Instale este conjunto de certificados nos clientes autenticados. Pode ter vários certificados de clientes instalados nas máquinas que pretende permitir o acesso ao cluster. Desa estampa de cada certificado na variável CertificateThumbprint. Se definir o IsAdmin como *verdadeiro,* o cliente com este certificado instalado pode fazer atividades de gestão de administradores no cluster. Se o IsAdmin for *falso,* o cliente com este certificado pode executar as ações apenas permitidas para os direitos de acesso ao utilizador, normalmente apenas de leitura. Para obter mais informações sobre as funções, consulte [o controlo de acesso baseado em funções do Service Fabric.](service-fabric-cluster-security.md#service-fabric-role-based-access-control) |
| ClienteCertificadoCommonnames |Desabine o nome comum do primeiro certificado de cliente para o CertificateCommonName. O CertificadoIssuerThumbprint é a impressão digital do emitente deste certificado. Para saber mais sobre nomes comuns e o emitente, consulte [Work with certificates](/dotnet/framework/wcf/feature-details/working-with-certificates). |
| ClienteCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emitente do certificado de cliente (tanto as funções de administrador como não administração). Pode fornecer o nome comum do emitente e o nome correspondente da loja nesta secção em vez de especificar a impressão digital do emitente sob os nomes do ClientCertificateCommonNames.  Isto facilita a revogação dos certificados de emitente de clientes. Vários emitentes podem ser especificados se for utilizado mais de um certificado de cliente. Um Emitente VazioCommonName permite todos os certificados nas lojas correspondentes especificadas em X509StoreNames.|
| ReverseProxyCertificate |Recomendado para um ambiente de teste. Este certificado opcional pode ser especificado se pretender proteger o seu [representante inverso](service-fabric-reverseproxy.md). Certifique-se de que o ReverseProxyEndpointPort está definido em nodeTypes se utilizar este certificado. |
| ReverseProxyCertificateCommonNames |Recomendado para um ambiente de produção. Este certificado opcional pode ser especificado se pretender proteger o seu [representante inverso](service-fabric-reverseproxy.md). Certifique-se de que o ReverseProxyEndpointPort está definido em nodeTypes se utilizar este certificado. |

Aqui está uma configuração de cluster exemplo onde os certificados de cluster, servidor e cliente foram fornecidos. Para os certificados cluster/servidor/reverseProxy, a impressão digital e o nome comum não podem ser configurados em conjunto para o mesmo tipo de certificado.

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
Quando se utiliza um nome comum de certificado em vez de uma impressão digital, o capotamento do certificado não requer uma atualização de configuração de cluster. Para atualizações de impressão digital emitente, certifique-se de que a nova lista de impressões digitais se cruza com a lista antiga. Primeiro tem de fazer uma atualização config com as novas impressões digitais do emitente e depois instalar os novos certificados (certificados de cluster/servidor e certificados emitentes) na loja. Guarde o antigo certificado emitente na loja de certificados durante pelo menos duas horas após a instalação do novo certificado emitente.
Se estiver a utilizar lojas de emitentes, não é necessário realizar nenhuma atualização config para a capotamento do certificado emitente. Instale o novo certificado emitente com uma última data de validade na loja de certificados correspondente e retire o antigo certificado emitente após algumas horas.

## <a name="acquire-the-x509-certificates"></a>Adquirir os certificados X.509
Para garantir a comunicação dentro do cluster, primeiro precisa de obter certificados X.509 para os seus nós de cluster. Além disso, para limitar a ligação a este cluster a máquinas/utilizadores autorizados, é necessário obter e instalar certificados para as máquinas cliente.

Para os clusters que estão a executar cargas de trabalho de produção, utilize um certificado X.509 assinado pela autoridade de [certificados (CA)](https://en.wikipedia.org/wiki/Certificate_authority)para assegurar o cluster. Para obter mais informações sobre como obter estes certificados, consulte [Como obter um certificado](/dotnet/framework/wcf/feature-details/how-to-obtain-a-certificate-wcf). 

Existem uma série de propriedades que o certificado deve ter para funcionar corretamente:

* O fornecedor do certificado deve ser **o Microsoft Enhanced RSA e o AES Cryptographic Provider**

* Ao criar uma chave RSA, certifique-se de que a chave é **2048 bits**.

* A extensão de Utilização chave tem um valor de **Assinatura Digital, Enciframento chave (a0)**

* A extensão de utilização da chave melhorada tem valores de **Autenticação** do Servidor (OID: 1.3.6.1.5.5.7.3.1) e **Autenticação do Cliente** (OID: 1.3.6.1.5.5.7.3.2)

Para os agrupamentos que utiliza para efeitos de teste, pode optar por utilizar um certificado auto-assinado.

Para perguntas adicionais, consulte [perguntas frequentes sobre certificados.](./cluster-security-certificate-management.md#troubleshooting-and-frequently-asked-questions)

## <a name="optional-create-a-self-signed-certificate"></a>Opcional: Criar um certificado auto-assinado
Uma forma de criar um certificado auto-assinado que possa ser protegido corretamente é utilizar o script CertSetup.ps1 na pasta SDK do tecido de serviço no diretório C:\Program Files\Microsoft SDKs\Service\ClusterSetup\Secure. Edite este ficheiro para alterar o nome predefinido do certificado. (Procure o valor CN=ServiceFabricDevCluster.) Executar este script como `.\CertSetup.ps1 -Install` .

Agora exporte o certificado para um ficheiro .pfx com uma senha protegida. Primeiro, pegue a impressão digital do certificado. 
1. A partir do menu **Iniciar,** executar **Gerir os certificados de computador.** 

2. Vá ao **Computador Local\Pasta pessoal** e encontre o certificado que criou. 

3. Clique duas vezes no certificado para o abrir, selecione o separador **Detalhes** e desloque-se para o campo **de impressão digital.** 

4. Retire os espaços e copie o valor da impressão digital no seguinte comando PowerShell. 

5. Altere o `String` valor para uma palavra-passe segura adequada para protegê-lo e execute o seguinte em PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Para ver os detalhes de um certificado instalado na máquina, execute o seguinte comando PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Em alternativa, se tiver uma subscrição Azure, siga os passos no [conjunto Criar um conjunto de tecidos de serviço utilizando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Instalar os certificados
Depois de ter certificados, pode instalá-los nos nós do cluster. Os seus nós precisam de ter os mais recentes Windows PowerShell 3.x instalados neles. Repita estes passos em cada nó para certificados de cluster e servidor e quaisquer certificados secundários.

1. Copie o ficheiro .pfx ou ficheiros para o nó.

2. Abra uma janela PowerShell como administrador e introduza os seguintes comandos. Substitua *$pswd* pela palavra-passe que usou para criar este certificado. Substitua *$PfxFilePath* por todo o caminho do .pfx copiado para este nó.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Agora, desacorde o controlo de acesso neste certificado para que o processo de Tecido de Serviço, que funciona sob a conta de Serviço de Rede, possa usá-lo executando o seguinte script. Fornecer a impressão digital do certificado e **serviço de rede** para a conta de serviço. Pode verificar se os ACLs no certificado estão corretos abrindo o certificado em  >  **Iniciar a Gestão de Certificados de Computador** e olhando para todas as **tarefas**  >  **gerir chaves privadas**.
   
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
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
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
4. Repita os passos anteriores para cada certificado do servidor. Também pode utilizar estes passos para instalar os certificados de cliente nas máquinas que pretende permitir o acesso ao cluster.

## <a name="create-the-secure-cluster"></a>Criar o cluster seguro
Depois de configurar a secção de segurança do ClusterConfig.X509.MultiMachine.jsem arquivo, pode proceder à secção [de cluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) para configurar os nós e criar o cluster autónomo. Lembre-se de utilizar o ClusterConfig.X509.MultiMachine.jsno ficheiro enquanto cria o cluster. Por exemplo, o seu comando pode parecer o seguinte:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Depois de ter o cluster Windows autónomo seguro a funcionar com sucesso e de ter configurado os clientes autenticados para se ligarem ao mesmo, siga os passos na secção [Ligar a um cluster utilizando o PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) para se ligar ao mesmo. Por exemplo:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Em seguida, pode executar outros comandos PowerShell para trabalhar com este cluster. Por exemplo, pode executar [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) para mostrar uma lista de nós neste cluster seguro.


Para remover o cluster, ligue-se ao nó no cluster onde descarregou o pacote 'Service Fabric', abra uma linha de comando e vá para a pasta do pacote. Agora executar o seguinte comando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Uma configuração incorreta do certificado pode impedir que o cluster se levante durante a sua implantação. Para diagnosticar problemas de segurança auto-diagnosticem, procure no grupo de **espectadores de eventos aplicações e serviços**  >  **Microsoft-Service Fabric**.
> 
> 

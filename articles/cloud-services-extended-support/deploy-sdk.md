---
title: Implementar Serviços de Nuvem (suporte alargado) - SDK
description: Implementar serviços cloud (suporte alargado) utilizando o Azure SDK
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d36bae57a9e1609e053326cf7288b5b1bc470cef
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166892"
---
# <a name="deploy-cloud-services-extended-support-by-using-the-azure-sdk"></a>Implementar serviços cloud (suporte alargado) utilizando o Azure SDK

Este artigo mostra como usar o [Azure SDK](https://azure.microsoft.com/downloads/) para implementar uma instância cloud Services (suporte alargado) que tem múltiplas funções (papel web e papel de trabalhador) e a extensão remota do ambiente de trabalho. Cloud Services (suporte alargado) é um modelo de implementação dos Azure Cloud Services que é baseado no Azure Resource Manager.

> [!IMPORTANT]
> Os Serviços cloud (suporte alargado) estão atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Antes de começar

Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado) e crie recursos associados.

## <a name="deploy-cloud-services-extended-support"></a>Implementar serviços de nuvem (suporte alargado)
1. Instale o [pacote Azure Compute SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute/43.0.0-preview) e inicialize o cliente utilizando um mecanismo de autenticação padrão.

    ```csharp
        public class CustomLoginCredentials : ServiceClientCredentials
    {
        private string AuthenticationToken { get; set; }
        public override void InitializeServiceClient<T>(ServiceClient<T> client)
           {
               var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantID}");
               var credential = new ClientCredential(clientId: "{clientID}", clientSecret: "{clientSecret}");
               var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
               if (result == null) throw new InvalidOperationException("Failed to obtain the JWT token");
               AuthenticationToken = result.Result.AccessToken;
           }
        public override async Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
           {
                if (request == null) throw new ArgumentNullException("request");
                if (AuthenticationToken == null) throw new InvalidOperationException("Token Provider Cannot Be Null");
                request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", AuthenticationToken);
                request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                //request.Version = new Version(apiVersion);
                await base.ProcessHttpRequestAsync(request, cancellationToken);
            }
    
        var creds = new CustomLoginCredentials();
        m_subId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
        ResourceManagementClient m_ResourcesClient = new ResourceManagementClient(creds);
        NetworkManagementClient m_NrpClient = new NetworkManagementClient(creds);
        ComputeManagementClient m_CrpClient = new ComputeManagementClient(creds);
        StorageManagementClient m_SrpClient = new StorageManagementClient(creds);
        m_ResourcesClient.SubscriptionId = m_subId;
        m_NrpClient.SubscriptionId = m_subId;
        m_CrpClient.SubscriptionId = m_subId;
        m_SrpClient.SubscriptionId = m_subId;
    ```

2. Crie um novo grupo de recursos instalando o pacote NuGet do Gestor de Recursos Azure.

    ```csharp 
    var resourceGroups = m_ResourcesClient.ResourceGroups;
    var m_location = “East US”;
    var resourceGroupName = "ContosoRG";//provide existing resource group name, if created already
    var resourceGroup = new ResourceGroup(m_location); 
    resourceGroup = await resourceGroups.CreateOrUpdateAsync(resourceGroupName, resourceGroup);
    ```

3. Crie uma conta de armazenamento e um recipiente onde armazenará os ficheiros de serviço (.cspkg) e de configuração de serviço (.cscfg). Instale o [pacote Azure Storage NuGet](https://www.nuget.org/packages/Azure.Storage.Common/). Este passo é opcional se estiver a utilizar uma conta de armazenamento existente. O nome da conta de armazenamento deve ser único.

    ```csharp
    string storageAccountName = “ContosoSAS”
    var stoInput = new StorageAccountCreateParameters
       {
            Location = m_location,
            Kind = Microsoft.Azure.Management.Storage.Models.Kind.StorageV2,
            Sku = new Microsoft.Azure.Management.Storage.Models.Sku(SkuName.StandardRAGRS),
        };
            StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.Create(rgName,
            storageAccountName, stoInput);
            bool created = false;
            while (!created)
               {
                    Thread.Sleep(600);
                    var stos = m_SrpClient.StorageAccounts.ListByResourceGroup(rgName);
                    created =
                    stos.Any(
                        t =>
                        StringComparer.OrdinalIgnoreCase.Equals(t.Name, storageAccountName));
                }
        
    StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.GetProperties(rgName, storageAccountName);.
    var accountKeyResult = m_SrpClient.StorageAccounts.ListKeysWithHttpMessagesAsync(rgName, storageAccountName).Result;
    CloudStorageAccount storageAccount = new CloudStorageAccount(new StorageCredentials(storageAccountName, accountKeyResult.Body.Keys.FirstOrDefault(). Value), useHttps: true);
        
    var blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExistsAsync().Wait();
    sharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddDays(-1);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddDays(2);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
    ```

4. Faça o upload do ficheiro do pacote de serviço (.cspkg) para a conta de armazenamento. O URL do pacote pode ser uma assinatura de acesso partilhado (SAS) URI a partir de qualquer conta de armazenamento.

    ```csharp
    CloudBlockBlob cspkgblockBlob = container.GetBlockBlobReference(“ContosoApp.cspkg”);
    cspkgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cspkg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string cspkgsasContainerToken = cspkgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cspkgSASUrl = cspkgblockBlob.Uri + cspkgsasContainerToken;
    ```

5. Faça o upload do ficheiro de configuração do seu serviço (.cscfg) para a conta de armazenamento. Especifique a configuração do serviço como um formato XML ou URL de cadeia.

    ```csharp
    CloudBlockBlob cscfgblockBlob = container.GetBlockBlobReference(“ContosoApp.cscfg”);
    cscfgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cscfg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasCscfgContainerToken = cscfgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cscfgSASUrl = cscfgblockBlob.Uri + sasCscfgContainerToken;
    ```

6. Crie uma rede virtual e uma sub-rede. Instale o [pacote Azure Network NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Network/). Este passo é opcional se estiver a utilizar uma rede e uma sub-rede existentes.

    ```csharp
    VirtualNetwork vnet = new VirtualNetwork(name: vnetName) 
       { 
            AddressSpace = new AddressSpace 
               { 
                    AddressPrefixes = new List<string> { "10.0.0.0/16" } 
               }, 
                    Subnets = new List<Subnet> 
                    { 
                        new Subnet(name: subnetName) 
                        { 
                            AddressPrefix = "10.0.0.0/24" 
                        } 
                    }, 
                    Location = m_location 
               }; 
    m_NrpClient.VirtualNetworks.CreateOrUpdate(resourceGroupName, “ContosoVNet”, vnet);
    ```

7. Crie um endereço IP público e desaponija a propriedade da etiqueta DNS do endereço IP público. Os Serviços cloud (suporte alargado) só suportam [Endereços https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) IP públicos] (endereços IP públicos SKU. Os IPs públicos padrão da SKU não funcionam com os Serviços cloud.
Se estiver a utilizar um IP estático, precisa de o referir como um ficheiro IP reservado na configuração de serviço (.cscfg)

    ```csharp
    PublicIPAddress publicIPAddressParams = new PublicIPAddress(name: “ContosIp”) 
       { 
            Location = m_location, 
            PublicIPAllocationMethod = IPAllocationMethod.Dynamic, 
            DnsSettings = new PublicIPAddressDnsSettings() 
               { 
                    DomainNameLabel = “contosoappdns” 
               } 
       }; 
    PublicIPAddress publicIpAddress = m_NrpClient.PublicIPAddresses.CreateOrUpdate(resourceGroupName, publicIPAddressName, publicIPAddressParams);
    ```

8. Crie um Objeto de Perfil de Rede e associe o endereço IP público à parte frontal do equilibrador de carga. A plataforma Azure cria automaticamente um recurso de balançador de carga SKU 'Classic' na mesma subscrição que o recurso de serviço de cloud. O recurso do balanceador de carga é um recurso apenas de leitura na ARM. Quaisquer atualizações ao recurso são suportadas apenas através dos ficheiros de implementação do serviço na nuvem (.cscfg & .csdef)

    ```csharp
    LoadBalancerFrontendIPConfiguration feipConfiguration = new LoadBalancerFrontendIPConfiguration() 
        { 
            Name = “ContosoFe”, 
            Properties = new LoadBalancerFrontendIPConfigurationProperties() 
                { 
                PublicIPAddress = new CM.SubResource() 
                    { 
                        Id = $"/subscriptions/{m_subId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{publicIPAddressName}", 
                    } 
                } 
        }; 
    
    CloudServiceNetworkProfile cloudServiceNetworkProfile = new CloudServiceNetworkProfile() 
        { 
            LoadBalancerConfigurations = new List<LoadBalancerConfiguration>() 
                { 
                    new LoadBalancerConfiguration() 
                       { 
                        Name  = 'ContosoLB', 
                        Properties = new LoadBalancerConfigurationProperties() 
                            { 
                            FrontendIPConfigurations = new List<LoadBalancerFrontendIPConfiguration>() 
                                { 
                                feipConfig 
                                } 
                            } 
                        } 
                } 
        }; 
    
    ```

9. Criar um cofre de chaves. Este cofre-chave será utilizado para armazenar certificados associados às funções cloud Services (suporte alargado). O cofre-chave deve estar localizado na mesma região e subscrição que a instância cloud Services (suporte alargado) e ter um nome único. Para obter mais informações, consulte [os certificados de utilização com a Azure Cloud Services (suporte alargado)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US”
    ```

10. Atualize a política de acesso do cofre-chave e conceda permissões de certificado à sua conta de utilizador.

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg'      -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete
    ```

    Em alternativa, desajei a política de acesso através do ID do objeto (que pode obter `Get-AzADUser` funcionando).

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -     ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates          create,get,list,delete
    ```

11. Neste exemplo, vamos adicionar um certificado auto-assinado a um cofre chave. A impressão digital do certificado precisa de ser adicionada no ficheiro de configuração de serviço (.cscfg) para implantação em funções de Cloud Services (suporte alargado).

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -       SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -      CertificatePolicy $Policy
    ```

12. Crie um objeto de perfil de SO. O perfil de SO especifica os certificados associados às funções de Cloud Services (suporte alargado). Aqui, é o mesmo certificado que criamos no passo anterior.

    ```csharp
    CloudServiceOsProfile cloudServiceOsProfile = 
        new CloudServiceOsProfile 
           { 
                Secrets = new List<CloudServiceVaultSecretGroup> 
                   { 
                        New CloudServiceVaultSecretGroup { 
                        SourceVault = <sourceVault>, 
                        VaultCertificates = <vaultCertificates> 
                        } 
                   } 
           };
    ```

13. Crie um objeto de perfil de função. Um perfil de função define propriedades específicas de função para um SKU, como nome, capacidade e nível. 

    Neste exemplo, definimos dois papéis: ContosoFrontend e ContosoBackend. As informações de perfil de função devem coincidir com a configuração de função definida no ficheiro de configuração de serviço (.cscfg) e no ficheiro de definição de serviço (.csdef).

    ```csharp
    CloudServiceRoleProfile cloudServiceRoleProfile = new CloudServiceRoleProfile()
       {
            Roles = new List<CloudServiceRoleProfileProperties>();
            
                // foreach role in cloudService
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoFrontend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoBackend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                    }
                    }
    ```

14. (Opcional) Crie um objeto de perfil de extensão que pretende adicionar à sua instância cloud Services (suporte alargado). Neste exemplo, adicionamos uma extensão de PDR.

    ```csharp
    string rdpExtensionPublicConfig = "<PublicConfig>" +
        "<UserName>adminRdpTest</UserName>" +
        "<Expiration>2021-10-27T23:59:59</Expiration>" +
        "</PublicConfig>";
        string rdpExtensionPrivateConfig = "<PrivateConfig>" +
        "<Password>VsmrdpTest!</Password>" +
        "</PrivateConfig>";
    
        Extension rdpExtension = new Extension
                {
                    Name = name,
                    Properties = new CloudServiceExtensionProperties
                    {
                        Publisher = "Microsoft.Windows.Azure.Extensions",
                        Type = "RDP",
                        TypeHandlerVersion = "1.2.1",,
                        AutoUpgradeMinorVersion = true,
                        Settings = rdpExtensionPublicConfig,
                        ProtectedSettings = rdpExtensionPrivateConfig,
                        RolesAppliedTo = [“*”],
                    }
                };
                        
    CloudServiceExtensionProfile cloudServiceExtensionProfile = new CloudServiceExtensionProfile
        {
            Extensions = rdpExtension
        };
    ```

15. Crie a implementação da instância Cloud Services (suporte alargado).

    ```csharp
    CloudService cloudService = new CloudService
        {
            Properties = new CloudServiceProperties
                {
                    RoleProfile = cloudServiceRoleProfile
                    Configuration = < Add Cscfg xml content here>,
                    // ConfigurationUrl = <Add your configuration URL here>,
                    PackageUrl = <Add cspkg SAS url here>,
                    ExtensionProfile = cloudServiceExtensionProfile,
                    OsProfile= cloudServiceOsProfile,
                    NetworkProfile = cloudServiceNetworkProfile,
                    UpgradeMode = 'Auto'
                },
                    Location = m_location
                };
    
    CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
    ```

## <a name="next-steps"></a>Passos seguintes
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar serviços cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md)um [modelo](deploy-template.md)ou [Visual Studio](deploy-visual-studio.md).
- Visite o [repositório de amostras para serviços em nuvem (suporte alargado)](https://github.com/Azure-Samples/cloud-services-extended-support)

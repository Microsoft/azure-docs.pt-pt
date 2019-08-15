---
title: Gerenciar certificados em um cluster de Service Fabric do Azure | Microsoft Docs
description: Descreve como adicionar novos certificados, sobrepor o certificado e remover o certificado de ou para um Cluster Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: d84525e869d47fc609ee8aac7feb7feda36a5f23
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68599949"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Adicionar ou remover certificados para um Cluster Service Fabric no Azure
É recomendável que você se familiarize com o modo como o Service Fabric usa certificados X. 509 e esteja familiarizado com os [cenários de segurança do cluster](service-fabric-cluster-security.md). Você deve entender o que é um certificado de cluster e o que é usado para, antes de prosseguir.

O comportamento de carregamento de certificado padrão do SDK do Azure Service fabrics, é implantar e usar um certificado definido com uma data de expiração mais distante no futuro; independentemente de sua definição de configuração primária ou secundária. Voltar ao comportamento clássico é uma ação avançada não recomendada e requer a definição do valor do parâmetro de configuração "UseSecondaryIfNewer" como falso em sua malha. configuração de código.

O Service Fabric permite especificar dois certificados de cluster, um primário e um secundário, quando você configura a segurança do certificado durante a criação do cluster, além dos certificados do cliente. Consulte [criando um cluster do Azure por meio do portal](service-fabric-cluster-creation-via-portal.md) ou [criando um cluster do Azure por meio de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter detalhes sobre como configurá-los no momento da criação. Se você especificar apenas um certificado de cluster em tempo de criação, ele será usado como o certificado primário. Após a criação do cluster, você pode adicionar um novo certificado como um secundário.

> [!NOTE]
> Para um cluster seguro, sempre será necessário pelo menos um certificado de cluster válido (não revogado e não expirado) (primário ou secundário) implantado (caso contrário, o cluster para de funcionar). 90 dias antes de todos os certificados válidos atingirem a expiração, o sistema gera um rastreamento de aviso e também um evento de integridade de aviso no nó. No momento, não há nenhum email ou outra notificação que Service Fabric envia neste artigo. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Adicionar um certificado de cluster secundário usando o portal
O certificado de cluster secundário não pode ser adicionado por meio do portal do Azure, use o Azure PowerShell. O processo é descrito posteriormente neste documento.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Remover um certificado de cluster usando o portal
Para um cluster seguro, sempre será necessário pelo menos um certificado válido (não revogado e não expirado). O certificado implantado com o mais distante na data de expiração futura estará em uso e removê-lo fará com que o cluster pare de funcionar; Certifique-se de remover apenas o certificado que expirou ou um certificado não usado que expire mais cedo.

Para remover um certificado de segurança de cluster não utilizado, navegue até a seção segurança e selecione a opção ' excluir ' no menu de contexto do certificado não utilizado.

Se sua intenção for remover o certificado marcado como primário, você precisará implantar um certificado secundário com uma data de expiração posterior ao certificado primário, habilitando o comportamento de substituição automática; Exclua o certificado primário após a conclusão da substituição automática.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Adicionar um certificado secundário usando o PowerShell do Gerenciador de recursos
> [!TIP]
> Agora há uma maneira melhor e mais fácil de adicionar um certificado secundário usando o cmdlet [Add-AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) . Você não precisa seguir o restante das etapas nesta seção.  Além disso, você não precisa do modelo usado originalmente para criar e implantar o cluster ao usar o cmdlet [Add-AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) .

Essas etapas pressupõem que você esteja familiarizado com o funcionamento do Gerenciador de recursos e implantou pelo menos um Service Fabric cluster usando um modelo do Resource Manager e ter o modelo usado para configurar o cluster de forma útil. Também pressupõe-se que você esteja familiarizado com o uso de JSON.

> [!NOTE]
> Se você estiver procurando um modelo de exemplo e parâmetros que você pode usar para acompanhar ou como um ponto de partida, baixe-o deste [repositório de git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Editar seu modelo do Resource Manager

Para facilitar o acompanhamento, o exemplo 5-VM-1-NodeTypes-Secure_Step2. JSON contém todas as edições que iremos fazer. o exemplo está disponível em [git-Repositório](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Certifique-se de seguir todas as etapas**

1. Abra o modelo do Resource Manager usado para implantar o cluster. (Se você baixou o exemplo do repositório anterior, use 5-VM-1-NodeTypes-Secure_Step1. JSON para implantar um cluster seguro e, em seguida, abra esse modelo).

2. Adicione **dois novos parâmetros** "secCertificateThumbprint" e "secCertificateUrlValue" do tipo "String" à seção de parâmetro do seu modelo. Você pode copiar o trecho de código a seguir e adicioná-lo ao modelo. Dependendo da origem do modelo, talvez você já tenha essas definidas, se passar para a próxima etapa. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Faça alterações no recurso **Microsoft. perfabric/clusters** -localize a definição de recurso "Microsoft. autofabric/clusters" em seu modelo. Em Propriedades dessa definição, você encontrará a marca JSON "certificado", que deve ser semelhante ao trecho JSON a seguir:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Adicione uma nova marca "thumbprintSecondary" e dê a ela um valor "[Parameters (' secCertificateThumbprint ')]".  

    Agora, a definição de recurso deve ser parecida com a seguinte (dependendo da origem do modelo, talvez não seja exatamente como o trecho de código abaixo). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Se você quiser **sobrepor o certificado**, especifique o novo certificado como primário e mova o primário atual como secundário. Isso resulta na substituição do certificado primário atual para o novo certificado em uma etapa de implantação.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Faça alterações em **todas** as definições de recurso **Microsoft. Compute/virtualMachineScaleSets** -localize a definição de recurso Microsoft. Compute/virtualMachineScaleSets. Role até o "Publicador": "Microsoft. Azure. perfabric", em "virtualMachineProfile".

    No Service Fabric configurações do Publicador, você deverá ver algo assim.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Adicionar as novas entradas de certificado a ela
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    As propriedades agora devem ser semelhantes a esta
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Se você quiser **sobrepor o certificado**, especifique o novo certificado como primário e mova o primário atual como secundário. Isso resulta na substituição do certificado atual para o novo certificado em uma etapa de implantação.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    As propriedades agora devem ser semelhantes a esta    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Faça alterações em **todas** as definições de recurso **Microsoft. Compute/virtualMachineScaleSets** -localize a definição de recurso Microsoft. Compute/virtualMachineScaleSets. Role até "vaultCertificates":, em "OSProfile". Ele deve ser semelhante a este.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Adicione o secCertificateUrlValue a ele. Use o trecho a seguir:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Agora, o JSON resultante deve ser semelhante a este.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Certifique-se de que você tenha repetido as etapas 4 e 5 para todas as definições de recursos de NodeTypes/Microsoft. Compute/virtualMachineScaleSets em seu modelo. Se você perder um deles, o certificado não será instalado nesse conjunto de dimensionamento de máquinas virtuais e você terá resultados imprevisíveis em seu cluster, incluindo o cluster ficar inativo (se você terminar sem certificados válidos que o cluster possa usar para segurança. Portanto, dê uma verificação dupla antes de continuar.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Edite o arquivo de modelo para refletir os novos parâmetros que você adicionou acima
Se você estiver usando o exemplo do [repositório de git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) para acompanhar, poderá começar a fazer alterações no exemplo 5-VM-1-NodeTypes-Secure. PARAMETERS_STEP2. JSON 

Edite seu arquivo de parâmetro de modelo do Resource Manager, adicione os dois novos parâmetros para secCertificateThumbprint e secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Implantar o modelo no Azure

- Agora você está pronto para implantar seu modelo no Azure. Abra um prompt de comando do Azure PS versão 1 +.
- Entre em sua conta do Azure e selecione a assinatura específica do Azure. Essa é uma etapa importante para pessoas que têm acesso a mais de uma assinatura do Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Teste o modelo antes de implantá-lo. Use o mesmo grupo de recursos no qual o cluster está implantado no momento.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Implante o modelo em seu grupo de recursos. Use o mesmo grupo de recursos no qual o cluster está implantado no momento. Execute o comando New-AzResourceGroupDeployment. Você não precisa especificar o modo, pois o valor padrão é **incremental**.

> [!NOTE]
> Se você definir o modo como concluído, poderá excluir inadvertidamente os recursos que não estão em seu modelo. Portanto, não o use neste cenário.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Aqui está um exemplo de preenchimento do mesmo PowerShell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Depois que a implantação for concluída, conecte-se ao cluster usando o novo certificado e execute algumas consultas. Se você puder fazer isso. Em seguida, você pode excluir o certificado antigo. 

Se você estiver usando um certificado autoassinado, não se esqueça de importá-los para o repositório de certificados do TrustedPeople local.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Para referência rápida aqui está o comando para se conectar a um cluster seguro 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Para referência rápida aqui está o comando para obter a integridade do cluster

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Implantando certificados de cliente no cluster.

Você pode usar as mesmas etapas descritas nas etapas 5 anteriores para que os certificados sejam implantados de um keyvault para os nós. Você só precisa definir e usar parâmetros diferentes.


## <a name="adding-or-removing-client-certificates"></a>Adicionando ou removendo certificados de cliente

Além dos certificados de cluster, você pode adicionar certificados de cliente para executar operações de gerenciamento em um Cluster Service Fabric.

Você pode adicionar dois tipos de certificados de cliente-admin ou somente leitura. Eles podem ser usados para controlar o acesso às operações de administração e às operações de consulta no cluster. Por padrão, os certificados de cluster são adicionados à lista de certificados de administrador permitidos.

Você pode especificar qualquer número de certificados de cliente. Cada adição/exclusão resulta em uma atualização de configuração para o Cluster Service Fabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Adicionando certificados de cliente-administrador ou somente leitura por meio do portal

1. Navegue até a seção segurança e selecione o botão ' + autenticação ' na parte superior da seção segurança.
2. Na seção ' Adicionar autenticação ', escolha o ' tipo de autenticação '-' cliente somente leitura ' ou ' cliente administrador '
3. Agora, escolha o método de autorização. Isso indica para Service Fabric se deve procurar esse certificado usando o nome da entidade ou a impressão digital. Em geral, não é uma boa prática de segurança usar o método de autorização do nome da entidade. 

![Adicionar certificado de cliente][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Exclusão de certificados de cliente-administrador ou somente leitura usando o portal

Para remover um certificado secundário de ser usado para segurança de cluster, navegue até a seção segurança e selecione a opção ' excluir ' no menu de contexto do certificado específico.

## <a name="next-steps"></a>Passos Seguintes
Leia estes artigos para obter mais informações sobre o gerenciamento de cluster:

* [Service Fabric processo de atualização de cluster e as expectativas de você](service-fabric-cluster-upgrade.md)
* [Configurar o acesso baseado em função para clientes](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG



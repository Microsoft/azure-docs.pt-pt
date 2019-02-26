---
title: Gerir certificados num cluster do Azure Service Fabric | Documentos da Microsoft
description: Descreve como adicionar novos certificados, o certificado de rollover e remover o certificado de ou para um cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2018
ms.author: aljo
ms.openlocfilehash: 37140b665f30d8bb99dd49dbdcca265969eaaf2f
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806696"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Adicionar ou remover certificados para um cluster do Service Fabric no Azure
Recomenda-se familiarizar com como o Service Fabric utiliza certificados X.509 e estar familiarizado com o [cenários de segurança do Cluster](service-fabric-cluster-security.md). Tem de compreender é que um certificado de cluster e o que é utilizado para, antes de prosseguir.

Comportamento de carga de certificado predefinido do SDK do Azure serviço recursos de infraestrutura, é implementar e utilizar um certificado definido com uma data de expiração mais afastada no futuro; independentemente da respetiva definição de configuração primária ou secundária. Reverter para o comportamento clássico é um não avançada ação recomendada e requer definindo o valor do parâmetro de definição de "UseSecondaryIfNever" como false na sua configuração de Fabric.Code.

O Service fabric permite-lhe especificar os dois certificados de cluster, um site primário e um secundário, quando configurar a segurança de certificado durante a criação do cluster, para além dos certificados de cliente. Consulte a [criar um cluster do azure através do portal](service-fabric-cluster-creation-via-portal.md) ou [criar um cluster do azure através do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter detalhes sobre a configuração dos mesmos em tempo de criação. Se especificar apenas um certificado de cluster em tempo de criação, em seguida, que é utilizado como o certificado primário. Após a criação do cluster, pode adicionar um novo certificado como um secundário.

> [!NOTE]
> Para um cluster seguro, sempre precisará de, pelo menos, um cluster (não revogado e não expirado) válido certificado (primário ou secundário) implementado (caso contrário, o pára de cluster a funcionar). 90 dias antes de todos os certificados válidos atingirem a expiração, o sistema gera um rastreio de aviso e também um evento de estado de funcionamento de aviso no nó. Existe atualmente nenhum e-mail ou qualquer outra notificação a indicar que o Service Fabric envia neste artigo. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Adicionar um certificado de cluster secundário com o portal
Não é possível adicionar o certificado de cluster secundário através do portal do Azure, utilize o Azure powershell. O processo é descrito mais adiante neste documento.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Remover um certificado de cluster com o portal
Para um cluster seguro, terá sempre pelo menos um certificado (não revogado e não expirado) válido. O certificado implementado com o furthest para a data de expiração futura irá estar em uso e removê-la fará com que o cluster parar a funcionar; Certifique-se para remover apenas o certificado que expirou ou um certificado não utilizado que expira a cedo.

Para remover um certificado de segurança do cluster não utilizados, navegue para a secção de segurança e selecione a opção "Eliminar" no menu de contexto no certificado não utilizado.

Se sua intenção é remover o certificado que está marcado como primário, em seguida, terá de implementar um certificado secundário com uma data de expiração ainda mais para o futuro do que o certificado principal, ativar o comportamento de rollover automático; Elimine o certificado primário depois do rollover automático foi concluída.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Adicionar um certificado secundário através do Powershell do Resource Manager
> [!TIP]
> Agora, é melhor e mais fácil adicionar um certificado secundário com o [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) cmdlet. Não precisa de seguir o resto dos passos nesta secção.  Além disso, não é necessário o modelo utilizado originalmente para criar e implementar o cluster ao utilizar o [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) cmdlet.

Estes passos partem do princípio de que está familiarizado com o funcionamento do Gestor de recursos e tiver implementado pelo menos um cluster do Service Fabric com um modelo do Resource Manager e que tem o modelo que utilizou para configurar o cluster útil. Também é assumido que está familiarizado com o JSON.

> [!NOTE]
> Se estiver à procura de um modelo de exemplo e parâmetros que pode utilizar a seguir ao longo ou como um ponto de partida, em seguida, transfira-o partir [repositório do git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Editar modelo do Resource Manager

Para facilitar a seguir ao longo, o exemplo 5-VM-1-NodeTypes-Secure_Step2.JSON contém todas as edições que iremos fazer. o exemplo está disponível em [repositório do git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Lembre-se de que siga todos os passos**

1. Abra o modelo do Resource Manager que utilizou para implementar o Cluster. (Se tiver transferido o exemplo a partir do repositório de anterior, em seguida, utilize 5-VM-1-NodeTypes-Secure_Step1.JSON para implementar um cluster seguro e, em seguida, abrir esse modelo).

2. Adicione **dois novos parâmetros** "secCertificateThumbprint" e "secCertificateUrlValue" de tipo de "string" para a secção de parâmetro do seu modelo. Pode copiar o seguinte trecho de código e adicioná-lo para o modelo. Dependendo da origem do seu modelo, pode já ter estes definido, se assim, mover para o passo seguinte. 
 
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

3. Efetue alterações para o **Microsoft.ServiceFabric/clusters** recursos - localize a definição do recurso "Microsoft.ServiceFabric/clusters" no seu modelo. Nas propriedades dessa definição, encontrará "Certificate" JSON marca, que deve ter um aspeto semelhante ao seguinte fragmento JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Adicionar uma nova etiqueta "thumbprintSecondary" e dê a ele um valor "[parameters('secCertificateThumbprint')]".  

    Portanto, agora, a definição do recurso deve ser semelhante ao seguinte (consoante a origem do modelo, pode não ser exatamente como o fragmento abaixo). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Se quiser **sobre o certificado**, em seguida, especifique o novo certificado como principal e mover o primário atual como secundário. Isso resulta no rollover do seu certificado primário atual para o novo certificado num único passo de implementação.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Realizar alterações ao **todos os** a **Compute/virtualmachinescalesets** definições de recursos - localize a definição do recurso Compute/virtualmachinescalesets. Desloque-se para o "Editor": "Microsoft.Azure.ServiceFabric", em "virtualMachineProfile".

    Nas definições de publicador do Service Fabric, deverá ver algo assim.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Adicionar-lhe as novas entradas de cert
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    As propriedades devem agora ter um aspeto semelhante a esta
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Se quiser **sobre o certificado**, em seguida, especifique o novo certificado como principal e mover o primário atual como secundário. Isso resulta no rollover do certificado atual para o novo certificado num único passo de implementação.     

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

    As propriedades devem agora ter um aspeto semelhante a esta    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Realizar alterações ao **todos os** a **Compute/virtualmachinescalesets** definições de recursos - localize a definição do recurso Compute/virtualmachinescalesets. Desloque-se para o "vaultCertificates":, em "OSProfile". ele deve ter um aspeto semelhante ao seguinte.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Adicione o secCertificateUrlValue a ele. Utilize o seguinte fragmento:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Agora o Json resultante deve ter um aspeto semelhante ao seguinte.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Certifique-se de que tem repetido passos 4 e 5 para todas as definições do recurso de Nodetypes/Microsoft.Compute/virtualMachineScaleSets no seu modelo. Se perder um deles, o certificado não irá obter instalado em que o conjunto de dimensionamento de máquina virtual e poderá ter resultados imprevisíveis no seu cluster, incluindo o cluster ficarem indisponíveis (se terminar com nenhum certificado válido que o cluster pode utilizar para a segurança. Por isso, verifique novamente, antes de prosseguir.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Edite o ficheiro de modelo para refletir os novos parâmetros que adicionou acima
Se estiver a utilizar o exemplo a partir da [repositório do git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) para acompanhar, pode começar a fazer alterações no exemplo 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON 

Editar o ficheiro de parâmetro de modelo do Resource Manager, adicione dois novos parâmetros para secCertificateThumbprint e secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Implementar o modelo para o Azure

- Agora está pronto para implementar o modelo para o Azure. Abra uma linha de comandos de versão 1 + Azure PS.
- Inicie sessão na sua conta do Azure e selecione a subscrição do azure específica. Este é um passo importante para quem tem acesso a mais do que uma subscrição do azure.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subscription ID> 

```

Teste o modelo antes de implantá-lo. Utilize o mesmo grupo de recursos que o cluster está a ser implementado na.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Implemente o modelo para o grupo de recursos. Utilize o mesmo grupo de recursos que o cluster está a ser implementado na. Execute o comando New-AzureRmResourceGroupDeployment. Não é necessário especificar o modo, uma vez que o valor predefinido é **incremental**.

> [!NOTE]
> Se definir o modo como concluída, pode eliminar inadvertidamente recursos que não estão no seu modelo. Portanto, não usá-lo neste cenário.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Eis um exemplo de manchas fora do mesmo powershell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Uma vez concluída a implementação, ligar ao cluster com o novo certificado e executar algumas consultas. Se for capaz de fazer. Em seguida, pode eliminar o certificado antigo. 

Se estiver a utilizar um certificado autoassinado, não se esqueça de importá-los para o arquivo de certificados TrustedPeople local.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Para referência rápida, eis o comando para ligar a um cluster seguro 

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
Para referência rápida, eis o comando para obter o estado de funcionamento do cluster

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>Implementação de certificados de aplicação para o cluster.

Pode utilizar os mesmos passos conforme descrito em 5 de passos anteriores para que os certificados implementados a partir de um cofre de chaves para os nós. Só tem de definir e utilizar parâmetros diferentes.


## <a name="adding-or-removing-client-certificates"></a>Adicionar ou remover certificados de cliente

Para além dos certificados de cluster, é possível adicionar certificados de cliente para executar operações de gestão no cluster do Service Fabric.

Pode adicionar dois tipos de certificados de cliente - administrador ou como somente leitura. Em seguida, estes podem ser utilizados para controlar o acesso para as operações de administração e operações de consulta no cluster. Por predefinição, os certificados de cluster são adicionados à lista de certificados permitidos do administrador.

Pode especificar qualquer número de certificados de cliente. Cada adições/eliminações resulta numa atualização de configuração para o cluster do Service Fabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Adicionar certificados de cliente - Admin ou só de leitura através do portal

1. Navegue para a secção de segurança e selecione o '+ de autenticação' botão na parte superior da seção de segurança.
2. Na secção "Adicionar autenticação", escolha o "tipo de autenticação' -"Cliente só de leitura"ou"Cliente administrativo"
3. Agora, escolha o método de autorização. Isto indica ao Service Fabric se ele deve procurar este certificado com o nome do requerente ou o thumbprint. Em geral, não é uma boa prática de segurança para utilizar o método de autorização do nome do requerente. 

![Adicionar certificado de cliente][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Eliminação de certificados de cliente - administrador ou só de leitura com o portal

Para remover um certificado secundário de que está a ser utilizado para segurança do cluster, navegue para a secção de segurança e selecione a opção "Eliminar" no menu de contexto no certificado específico.

## <a name="next-steps"></a>Passos Seguintes
Leia os artigos seguintes para obter mais informações sobre o gerenciamento de cluster:

* [Processo de atualização do Cluster do Service Fabric e as expectativas da sua](service-fabric-cluster-upgrade.md)
* [Configurar o acesso baseado em funções para clientes](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG



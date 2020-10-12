---
title: Gerir certificados num cluster de tecido de serviço Azure
description: Descreve como adicionar novos certificados, certificado de capotamento e remover certificado de ou de um cluster de Tecido de Serviço.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b1ccf83e666f9106a31809ff41d55062826be78c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88869767"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Adicionar ou remover certificados para um cluster do Service Fabric no Azure
Recomenda-se que se familiarize com a forma como o Service Fabric utiliza certificados X.509 e esteja familiarizado com os [cenários de segurança](service-fabric-cluster-security.md)do Cluster. Deve entender o que é um certificado de cluster e para o que é usado, antes de prosseguir.

O comportamento de carga de certificado padrão da Azure Service Fabrics SDK é implantar e utilizar o certificado definido com data de validade mais distante no futuro; independentemente da definição de configuração primária ou secundária. Voltar ao comportamento clássico é uma ação avançada não recomendada e requer a definição do valor do parâmetro de definição "UseSecondaryIfNewer" para falso dentro da sua `Fabric.Code` configuração.

O tecido de serviço permite especificar dois certificados de cluster, um primário e um secundário, quando configura a segurança do certificado durante a criação do cluster, além dos certificados de cliente. Consulte a [criação de um cluster azul via portal](service-fabric-cluster-creation-via-portal.md) ou a [criação de um cluster azul via Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter detalhes sobre a sua configuração no momento de criar. Se especificar apenas um certificado de cluster no momento de criação, então este é usado como certificado primário. Após a criação do cluster, pode adicionar um novo certificado como secundário.

> [!NOTE]
> Para um cluster seguro, será sempre necessário pelo menos um certificado de cluster válido (não revogado e não caducado) (primário ou secundário) implantado (primário ou secundário) (se não, o cluster deixa de funcionar). 90 dias antes de todos os certificados válidos chegarem à expiração, o sistema gera um rasto de aviso e também um evento de saúde de aviso no nó. Não existe atualmente nenhum e-mail ou qualquer outra notificação que a Service Fabric envie neste artigo. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Adicione um certificado de cluster secundário usando o portal
O certificado de cluster secundário não pode ser adicionado através do portal Azure, use a powershell Azure. O processo é delineado mais tarde neste documento.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Remover um certificado de cluster usando o portal
Para um cluster seguro, você sempre precisará de pelo menos um certificado válido (não revogado e não caducado). O certificado implantado com o mais longe na data de validade futura será utilizado, e removê-lo fará com que o seu cluster deixe de funcionar; certifique-se de remover apenas o certificado que está caducado, ou um certificado nãoureado que expire o mais cedo.

Para remover um certificado de segurança do cluster não reutilizado, navegue na secção de Segurança e selecione a opção 'Eliminar' do menu de contexto no certificado não reutilizado.

Se a sua intenção é remover o certificado que está marcado como primário, então terá de implantar um certificado secundário com uma data de validade mais para o futuro do que o certificado primário, permitindo o comportamento de capotamento automático; eliminar o certificado primário após a capotamento automático concluída.

## <a name="add-a-secondary-certificate-using-azure-resource-manager"></a>Adicione um certificado secundário usando O Gestor de Recursos Azure

Estes passos assumem que está familiarizado com o funcionamento do Gestor de Recursos e implementou pelo menos um cluster de Tecido de Serviço usando um modelo de Gestor de Recursos, e tem o modelo que usou para configurar o cluster à mão. Também se supõe que se sente confortável usando json.

> [!NOTE]
> Se você estiver procurando um modelo de amostra e parâmetros que você pode usar para seguir ou como um ponto de partida, em seguida, descarregue-o a partir deste [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Edite o seu modelo de Gestor de Recursos

Para facilitar o acompanhamento, a amostra 5-VM-1-NodeTypes-Secure_Step2.JSON contém todas as edições que vamos fazer. a amostra está disponível em [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample).

**Certifique-se de seguir todos os passos**

1. Abra o modelo de Gestor de Recursos que usou para o implementar Cluster. (Se tiver descarregado a amostra do repo anterior, use 5-VM-1-NodeTypes-Secure_Step1.JSON para implantar um cluster seguro e, em seguida, abra esse modelo).

2. Adicione **dois novos parâmetros** "secCertificateThumbprint" e "secCertificateUrlValue" do tipo "string" à secção de parâmetros do seu modelo. Pode copiar o seguinte corte de código e adicioná-lo ao modelo. Dependendo da origem do seu modelo, pode já ter estes definidos, se assim for, passar para o passo seguinte. 
 
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

3. Escote alterações no recurso **Microsoft.ServiceFabric/clusters** - Localize a definição de recurso "Microsoft.ServiceFabric/clusters" no seu modelo. Sob propriedades dessa definição, você encontrará a etiqueta JSON "Certificado", que deve parecer algo como o seguinte corte JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Adicione uma nova etiqueta "thumbprintSecondary" e dê-lhe um valor "[parâmetros ('secCertificateThumbprint')".  

    Assim, agora a definição de recurso deve parecer o seguinte (dependendo da sua origem do modelo, pode não ser exatamente igual ao corte abaixo). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Se quiser **rolar sobre o cert,** especifique o novo certificado como primário e mova a atual primária como secundária. Isto resulta na capotamento do seu certificado primário atual para o novo certificado numa única etapa de implantação.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Escode alterações em **todas as** definições de recursos **microsoft.Compute/virtualMachineScaleSets** - Localizar a definição de recurso Microsoft.Compute/virtualMachineScaleSets. Percorra a "editora": "Microsoft.Azure.ServiceFabric", em "virtualMachineProfile".

    Nas definições de editores de Service Fabric, você deve ver algo assim.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Adicione as novas entradas de cert
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    As propriedades devem agora se parecer com esta
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Se quiser **rolar sobre o cert,** especifique o novo certificado como primário e mova a atual primária como secundária. Isto resulta na capotamento do seu certificado atual para o novo certificado numa única etapa de implantação.     

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

    As propriedades devem agora se parecer com esta    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Fazer alterações em **todas as** definições de recursos **microsoft.Compute/virtualMachineScaleSets** - Localizar a definição de recurso Microsoft.Compute/virtualMachineScaleSets. Percorra para o "vaultCertificates": , em "OSProfile". Deve ser algo parecido com isto.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Adicione-lhe o secCertificateUrlValue. Utilizar o seguinte corte:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Agora o Json resultante deve ser algo parecido com isto.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Certifique-se de que tem repetidas definições de recursos 4 e 5 para todos os nodetipos/Microsoft.Compute/virtualMachineScaleSets definições de recursos no seu modelo. Se perder um deles, o certificado não será instalado nesse conjunto de escala de máquina virtual e terá resultados imprevisíveis no seu cluster, incluindo o cluster a descer (se acabar por não ter certificados válidos que o cluster possa usar para segurança. Então, verifique duas vezes, antes de prosseguir.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Edite o seu ficheiro de modelo para refletir os novos parâmetros que adicionou acima
Se estiver a utilizar a amostra do [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) para acompanhar, pode começar a fazer alterações na amostra 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON 

Edite o seu ficheiro de parâmetro de modelo de gestor de recursos, adicione os dois novos parâmetros para secCertificateThumbprint e secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Implementar o modelo no Azure

- Está agora pronto para implementar o seu modelo para Azure. Abra um pedido de comando Azure PS versão 1+.
- Inscreva-se na sua Conta Azure e selecione a subscrição específica do azul. Este é um passo importante para as pessoas que têm acesso a mais de uma subscrição azul.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Teste o modelo antes de o implementar. Utilize o mesmo Grupo de Recursos para o qual o seu cluster está atualmente implantado.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Implemente o modelo para o seu grupo de recursos. Utilize o mesmo Grupo de Recursos para o qual o seu cluster está atualmente implantado. Executar o comando New-AzResourceGroupDeployment. Não é necessário especificar o modo, uma vez que o valor predefinido é **incremental**.

> [!NOTE]
> Se definir o Modo para Completar, pode eliminar inadvertidamente recursos que não estão no seu modelo. Portanto, não a utilize neste cenário.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Aqui está um exemplo preenchido da mesma concha.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Assim que a implementação estiver concluída, ligue-se ao seu cluster utilizando o novo Certificado e efetue algumas consultas. Se puder fazer. Depois pode apagar o certificado antigo. 

Se estiver a utilizar um certificado auto-assinado, não se esqueça de os importar para a sua loja local de certPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Para referência rápida aqui é o comando para ligar a um cluster seguro 

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
Para referência rápida aqui é o comando para obter saúde de cluster

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Implantação de certificados de cliente para o cluster.

Pode utilizar os mesmos passos descritos nos passos 5 anteriores para que os certificados são distribuídos de um teclado para os Nós. Só precisas de definir e usar diferentes parâmetros.


## <a name="adding-or-removing-client-certificates"></a>Adicionar ou remover certificados de cliente

Além dos certificados de cluster, pode adicionar certificados de cliente para realizar operações de gestão num cluster de Tecidos de Serviço.

Pode adicionar dois tipos de certificados de cliente - Administrador ou Read-only. Estes podem então ser usados para controlar o acesso às operações de administração e operações de consulta no cluster. Por predefinição, os certificados de cluster são adicionados à lista de certificados de administração permitidos.

Pode especificar qualquer número de certificados de cliente. Cada adição/eliminação resulta numa atualização de configuração do cluster de Tecido de Serviço.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Adicionar certificados de cliente - Administrador ou Read-Only via portal

1. Navegue na secção Segurança e selecione o botão '+ Autenticação' em cima da secção de segurança.
2. Na secção 'Adicionar autenticação', escolha o 'Tipo de Autenticação' - 'Cliente apenas de leitura' ou 'Cliente administrador'
3. Agora escolha o método de Autorização. Isto indica ao Service Fabric se deve procurar este certificado utilizando o nome do sujeito ou a impressão digital. Em geral, não é uma boa prática de segurança utilizar o método de autorização do nome do sujeito. 

![Adicionar certificado de cliente][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Supressão de Certificados de Cliente - Administração ou Read-Only usando o portal

Para remover um certificado secundário de ser utilizado para a segurança do cluster, navegue para a secção de Segurança e selecione a opção 'Eliminar' a partir do menu de contexto no certificado específico.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Adicionar certificados de aplicação a um conjunto de escala de máquina virtual

Para implementar um certificado que utilize para as suas aplicações no seu cluster, consulte [esta amostra do script Powershell](scripts/service-fabric-powershell-add-application-certificate.md).

## <a name="next-steps"></a>Passos seguintes
Leia estes artigos para obter mais informações sobre gestão de clusters:

* [Processo de upgrade do Cluster de Tecido de Serviço e expectativas de si](service-fabric-cluster-upgrade.md)
* [Acesso baseado em funções de configuração para clientes](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG



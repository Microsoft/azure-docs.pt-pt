---
title: Gerir certificados num cluster Azure Service Fabric
description: Descreve como adicionar novos certificados, certificado de capotamento e remover certificado de ou para um cluster de Tecido de Serviço.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: a3c92e1b39261af32085e4d9b6cb2462d5c0eb64
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75458362"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Adicionar ou remover certificados para um cluster do Service Fabric no Azure
Recomenda-se que se familiarize com a forma como o Service Fabric utiliza certificados X.509 e esteja familiarizado com os [cenários](service-fabric-cluster-security.md)de segurança do Cluster . Deve entender o que é um certificado de cluster e para que é usado, antes de avançar mais.

O comportamento de carga de certificado padrão da Azure Service Fabrics SDK é implantar e utilizar o certificado definido com data de validade mais longe no futuro; independentemente da sua definição de configuração primária ou secundária. Voltar ao comportamento clássico é uma ação avançada não recomendada e requer a definição do valor do `Fabric.Code` parâmetro de definição "UseSecondaryIfNewer" para falso dentro da sua configuração.

O tecido de serviço permite especificar dois certificados de cluster, um primário e um secundário, quando configura a segurança do certificado durante a criação do cluster, além dos certificados de cliente. Consulte a [criação](service-fabric-cluster-creation-via-portal.md) de um cluster azul via portal ou a criação de um cluster [azure via Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter detalhes sobre a sua configuração no momento de criar. Se especificar apenas um certificado de cluster no momento de criar, então este é usado como o certificado primário. Após a criação do cluster, pode adicionar um novo certificado como secundário.

> [!NOTE]
> Para um cluster seguro, necessitará sempre de pelo menos um certificado de cluster válido (não revogado e não expirado) (primário ou secundário) implantado (se não, o cluster deixa de funcionar). 90 dias antes de todos os certificados válidos chegarem à expiração, o sistema gera um rastreio de advertência e também um evento de saúde de alerta no nó. Atualmente não existe nenhum e-mail ou qualquer outra notificação que o Service Fabric envie neste artigo. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Adicione um certificado de cluster secundário usando o portal
O certificado de cluster secundário não pode ser adicionado através do portal Azure, utilize a powershell Azure. O processo é delineado mais tarde neste documento.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Remova um certificado de cluster utilizando o portal
Para um cluster seguro, necessitará sempre de pelo menos um certificado válido (não revogado e não expirado). O certificado implantado com o mais longe na data de validade futura estará em uso, e removê-lo-á fazendo com que o seu cluster pare de funcionar; certifique-se de apenas remover o certificado que está expirado, ou um certificado não utilizado que expire o mais cedo.

Para remover um certificado de segurança de cluster não utilizado, navegue para a secção de Segurança e selecione a opção 'Eliminar' do menu de contexto no certificado não utilizado.

Se a sua intenção é remover o certificado que está marcado como primário, então terá de implementar um certificado secundário com uma data de validade ainda mais para o futuro do que o certificado primário, permitindo o comportamento de capotamento automático; eliminar o certificado primário após a conclusão da capotação automática.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Adicione um certificado secundário usando powershell do Gestor de Recursos
> [!TIP]
> Existe agora uma forma melhor e mais fácil de adicionar um certificado secundário utilizando o cmdlet [Add-AzServiceFabricClusterCertificate.](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) Não precisa seguir o resto dos passos nesta secção.  Além disso, não precisa do modelo originalmente utilizado para criar e implantar o cluster quando utilizar o cmdlet [Add-AzServiceClusterCluster.](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate)

Estes passos assumem que você está familiarizado com o funcionamento do Gestor de Recursos e implementou pelo menos um cluster de Tecido de Serviço usando um modelo de Gestor de Recursos, e tem o modelo que você usou para configurar o cluster à mão. Também se assume que se sente confortável usando jSON.

> [!NOTE]
> Se procura um modelo de amostra e parâmetros que pode usar para seguir ou como ponto de partida, então descarregue-o a partir deste [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Editar o seu modelo de Gestor de Recursos

Para facilitar o acompanhamento, a amostra 5-VM-1-NodeTypes-Secure_Step2.JSON contém todas as edições que vamos fazer. a amostra está disponível em [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample).

**Certifique-se de seguir todos os passos**

1. Abra o modelo de Gestor de Recursos que usou para implementá-lo Cluster. (Se descarregou a amostra do repo anterior, utilize 5-VM-1-NodeTypes-Secure_Step1.JSON para implementar um cluster seguro e, em seguida, abra o modelo).

2. Adicione **dois novos parâmetros** "secCertificateThumbprint" e "secCertificateUrlValue" do tipo "string" à secção de parâmetros do seu modelo. Pode copiar o seguinte código e adicioná-lo ao modelo. Dependendo da origem do seu modelo, pode já ter estes definidos, se assim for, passar para o próximo passo. 
 
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

3. Faça alterações no recurso **Microsoft.ServiceFabric/clusters** - Localize a definição de recursos "Microsoft.ServiceFabric/clusters" no seu modelo. Em propriedades dessa definição, você encontrará etiqueta JSON "Certificado", que deve se parecer com o seguinte corte JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Adicione uma nova etiqueta "thumbprintSecondary" e dê-lhe um valor "[parâmetros('secCertificateThumbprint')]".  

    Portanto, agora a definição de recursos deve parecer a seguinte (dependendo da sua fonte do modelo, pode não ser exatamente como o corte abaixo). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Se quiser **passar o certificado,** especifique o novo certificado como primário e movimente o primário atual como secundário. Isto resulta no capotamento do seu certificado primário atual para o novo certificado numa etapa de implantação.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Faça alterações em **todas as** definições de recursos **microsoft.Compute/virtualMachineScaleSets** - Localize a definição de recursos microsoft.Compute/virtualMachineScaleSets. Percorra a "editora": "Microsoft.Azure.ServiceFabric", em "virtualMachineProfile".

    Nas definições da editora Service Fabric, deve ver algo assim.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Adicione-lhe as novas entradas cert
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    As propriedades devem agora ser assim
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Se quiser **passar o certificado,** especifique o novo certificado como primário e movimente o primário atual como secundário. Isto resulta no capotamento do seu certificado atual para o novo certificado numa única etapa de implantação.     

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

    As propriedades devem agora ser assim    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Faça alterações em **todas as** definições de recursos **microsoft.Compute/virtualMachineScaleSets** - Localize a definição de recursos microsoft.Compute/virtualMachineScaleSets. Percorra os "certificados de abóbada": , em "OSProfile". Deve ser algo assim.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Adicione-lhe o secCertificateUrlValue. Utilize o seguinte corte:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Agora o Json resultante deve ser algo assim.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Certifique-se de que tem passos repetidos 4 e 5 para todos os nós/Microsoft.Compute/virtualMachineScaleSets definições de recursos no seu modelo. Se perder um deles, o certificado não será instalado nesse conjunto de escala de máquina virtual e terá resultados imprevisíveis no seu cluster, incluindo a queda do cluster (se acabar sem certificados válidos que o cluster possa usar para a segurança. Então, verifique duas vezes, antes de prosseguir.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Editar o seu ficheiro de modelo para refletir os novos parâmetros que adicionou acima
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

### <a name="deploy-the-template-to-azure"></a>Desdobrar o modelo para Azure

- Está agora pronto para lançar o seu modelo para O Azure. Abra um pedido de comando azure PS versão 1+.
- Inscreva-se na sua Conta Azure e selecione a subscrição específica do Azure. Este é um passo importante para as pessoas que têm acesso a mais de uma subscrição azul.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Teste o modelo antes de o implementar. Utilize o mesmo Grupo de Recursos para o que o seu cluster está atualmente implantado.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Desloque o modelo para o seu grupo de recursos. Utilize o mesmo Grupo de Recursos para o que o seu cluster está atualmente implantado. Executar o comando New-AzResourceGroupDeployment. Não é necessário especificar o modo, uma vez que o valor predefinido é **incremental**.

> [!NOTE]
> Se definir o Modo para Completar, pode eliminar inadvertidamente recursos que não estão no seu modelo. Portanto, não o use neste cenário.
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

Uma vez concluída a implementação, ligue-se ao seu cluster utilizando o novo Certificado e execute algumas consultas. Se for capaz de fazer. Depois pode apagar o certificado antigo. 

Se estiver a usar um certificado auto-assinado, não se esqueça de os importar para a sua loja local TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Para uma referência rápida aqui é o comando para ligar a um cluster seguro 

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
Para uma referência rápida aqui é o comando para obter saúde de cluster

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>A implantar certificados de cliente para o cluster.

Pode utilizar os mesmos passos que os passos 5 anteriores para ter os certificados implantados a partir de um cofre para os nódosos. Só precisas de definir e usar parâmetros diferentes.


## <a name="adding-or-removing-client-certificates"></a>Adicionar ou remover certificados de cliente

Além dos certificados de cluster, pode adicionar certificados de cliente para realizar operações de gestão num cluster de Tecido de Serviço.

Pode adicionar dois tipos de certificados de cliente - Administrador ou apenas leitura. Estes podem então ser utilizados para controlar o acesso às operações de administração e operações de consulta no cluster. Por predefinição, os certificados de cluster são adicionados à lista de certificados de Administração permitida.

pode especificar qualquer número de certificados de cliente. Cada adição/eliminação resulta numa atualização de configuração para o cluster Service Fabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Adicionar certificados de cliente - Administrador ou Read-Only via portal

1. Navegue na secção de Segurança e selecione o botão '+ Autenticação' em cima da secção de segurança.
2. Na secção 'Adicionar autenticação', escolha o 'Tipo de Autenticação' - 'Cliente apenas de leitura' ou 'Cliente Administrador'
3. Agora escolha o método de Autorização. Isto indica à Fabric de Serviço se deve procurar este certificado utilizando o nome do assunto ou a impressão digital. Em geral, não é uma boa prática de segurança utilizar o método de autorização do nome do sujeito. 

![Adicionar certificado de cliente][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Eliminação de Certificados de Cliente - Administrador ou Read-Only utilizando o portal

Para remover um certificado secundário de ser utilizado para a segurança do cluster, navegue para a secção de Segurança e selecione a opção 'Eliminar' do menu de contexto no certificado específico.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Adicionar certificados de aplicação a um conjunto de escala de máquina virtual

Para implementar um certificado que utiliza para as suas aplicações no seu cluster, consulte esta amostra de [script Powershell](scripts/service-fabric-powershell-add-application-certificate.md).

## <a name="next-steps"></a>Passos seguintes
Leia estes artigos para obter mais informações sobre gestão de clusters:

* [Processo de upgrade do Cluster de Tecidode Serviço e expectativas de si](service-fabric-cluster-upgrade.md)
* [Configurar o acesso baseado em papéis para clientes](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG



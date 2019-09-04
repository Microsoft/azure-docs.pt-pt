---
title: Modelo de recurso do aplicativo Service Fabric do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do gerenciamento de um aplicativo de Service Fabric do Azure com Azure Resource Manager
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: atsenthi
ms.openlocfilehash: 36c0f02202c738ac96d26b748b741cd8eee27380
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241830"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>O que é o modelo de recurso de aplicativo Service Fabric?
É recomendável que Service Fabric aplicativos sejam implantados no Cluster Service Fabric por meio do Azure Resource Manager. Esse método possibilita descrever aplicativos e serviços em JSON e implantá-los no mesmo modelo do Resource Manager que o cluster. Em oposição à implantação e ao gerenciamento de aplicativos por meio do PowerShell ou CLI do Azure, não há necessidade de esperar que o cluster esteja pronto. Pode realizar o processo de registo, aprovisionamento e implementação de aplicações com um único passo. Esta é a melhor prática para gerir o ciclo de vida das aplicações no seu cluster. Para obter mais informações, consulte [as práticas recomendadas](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources).

Quando aplicável, gerencie seus aplicativos como recursos do Resource Manager para melhorar:
* Trilha de auditoria: O Resource Manager audita cada operação e mantém um *log de atividades* detalhado que pode ajudá-lo a rastrear todas as alterações feitas nesses aplicativos e no cluster.
* Controle de acesso baseado em função: O gerenciamento de acesso a clusters, bem como aplicativos implantados no cluster, pode ser feito por meio do mesmo modelo do Resource Manager.
* Azure Resource Manager (por meio do portal do Azure) se torna uma loja única para gerenciar o cluster e implantações de aplicativos críticos.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Service Fabric ciclo de vida do aplicativo com Azure Resource Manager 
Neste documento, você aprenderá a:

> [!div class="checklist"]
> * Implantar recursos do aplicativo usando o Azure Resource Manager 
> * Atualizar recursos do aplicativo usando o Azure Resource Manager
> * Excluir recursos do aplicativo

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Implantar recursos do aplicativo usando o Azure Resource Manager  
Para implantar um aplicativo e seus serviços usando o modelo de recurso de aplicativo Azure Resource Manager, você precisa empacotar o código do aplicativo, carregar o pacote e, em seguida, fazer referência ao local do pacote em um modelo de Azure Resource Manager como um aplicativo Kit. Para obter mais informações, veja [empacotar um aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg).
          
Em seguida, crie um modelo de Azure Resource Manager, atualize o arquivo de parâmetros com os detalhes do aplicativo e implante-o no Cluster Service Fabric. Consulte os exemplos [aqui](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Criar uma conta de Armazenamento 
A implantação de um aplicativo de um modelo do Resource Manager requer uma conta de armazenamento para preparar a imagem do aplicativo. Você pode usar novamente uma conta de armazenamento existente ou criar uma nova conta de armazenamento para preparar seus aplicativos. Se você quiser usar uma conta de armazenamento existente, poderá ignorar esta etapa. 

![Criar uma conta de armazenamento][CreateStorageAccount]

### <a name="configure-storage-account"></a>Configurar conta de armazenamento 
Depois que a conta de armazenamento tiver sido criada, você precisará criar um contêiner de BLOBs onde os aplicativos podem ser preparados. Na portal do Azure, navegue até a conta de armazenamento para a qual você deseja armazenar seus aplicativos. Selecione a folha BLOBs e clique no botão **Adicionar contêiner** . Adicione um novo contêiner com o nível de acesso público do blob.
   
![Criar blob][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>Preparar aplicativo em uma conta de armazenamento
Antes que o aplicativo possa ser implantado, ele deve ser preparado no armazenamento de BLOBs. Neste tutorial, criaremos o pacote de aplicativos manualmente, no entanto, essa etapa pode ser automatizada.  Para obter mais informações, veja [empacotar um aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). Nas etapas a seguir, o [aplicativo de exemplo de votação](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) será usado.

1. No Visual Studio, clique com o botão direito do mouse no projeto de votação e selecione pacote.   
![Aplicativo de pacote][PackageApplication]  
2. Abra o diretório **.\Service-Fabric-dotnet-quickstart\Voting\pkg\Debug** que acabou de criar e compacte o conteúdo em um arquivo chamado **votação. zip** , de modo que o ApplicationManifest. xml esteja na raiz do arquivo zip.  
![Aplicativo zip][ZipApplication]  
3. Renomeie a extensão do arquivo de. zip para **. sfpkg**.
4. No portal do Azure, no contêiner **aplicativos** da sua conta de armazenamento, clique em **carregar** e carregar **votação. sfpkg**.  
![Carregar pacote do aplicativo][UploadAppPkg]

O aplicativo agora é preparado. Agora estamos prontos para criar o modelo de Azure Resource Manager para implantar o aplicativo.      
   
### <a name="create-the-azure-resource-manager-template"></a>Criar o modelo de Azure Resource Manager
O aplicativo de exemplo contém [modelos de Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) que podem ser usados para implantar o aplicativo. Os arquivos de modelo são nomeados **UserApp. JSON** e **UserApp. Parameters. JSON**. 

> [!NOTE] 
> O arquivo **UserApp. Parameters. JSON** deve ser atualizado com o nome do cluster.
>
>

| Parâmetro              | Descrição                                 | Exemplo                                                      | Comentários                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | O nome do cluster no qual você está implantando | It-cluster123                                                |                                                              |
| aplicação            | O nome do aplicativo                 | Dica                                                       |
| applicationTypeName    | O nome do tipo do aplicativo           | VotingType                                                   | Deve corresponder ao que está em ApplicationManifest. xml                 |
| applicationTypeVersion | A versão do tipo de aplicativo         | 1.0.0                                                        | Deve corresponder ao que está em ApplicationManifest. xml                 |
| serviceName            | O nome do serviço que o serviço         | Votação ~ VotingWeb                                             | Deve estar no formato ApplicationName ~ ServiceType            |
| serviceTypeName        | O nome do tipo do serviço                | VotingWeb                                                    | Deve corresponder ao que está no manifesto. xml                 |
| appPackageUrl          | A URL de armazenamento de BLOBs do aplicativo     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | A URL do pacote de aplicativos no armazenamento de BLOBs (o procedimento para definir isso é descrito abaixo) |
       
```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Implementar a aplicação 
Para implantar o aplicativo, execute o New-AzResourceGroupDeployment para implantar no grupo de recursos que contém o cluster.
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Atualizar Service Fabric aplicativo usando Azure Resource Manager
Os aplicativos já implantados em um Cluster Service Fabric serão atualizados pelos seguintes motivos:

1. Um novo serviço é adicionado ao aplicativo. Uma definição de serviço deve ser adicionada ao arquivo Service-manifest. xml e Application-manifest. xml. Em seguida, para refletir a nova versão do aplicativo, você precisa atualizar a versão do tipo de aplicativo de 1.0.0 para 1.0.1 [UserApp. Parameters. JSON](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json).

    ```
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```
2. Uma nova versão de um serviço existente é adicionada ao aplicativo. Isso envolve alterações de código do aplicativo e atualizações para a versão e o nome do tipo de aplicativo.

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Excluir recursos do aplicativo
Os aplicativos implantados usando o modelo de recurso de aplicativo no Azure Resource Manager podem ser excluídos do cluster usando as etapas abaixo

1) Obter ID de recurso para o aplicativo usando o comando [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0)  

    #### <a name="get-resource-id-for-application"></a>Obter ID de recurso para o aplicativo
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) Exclua os recursos do aplicativo usando [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)  

    #### <a name="delete-application-resource-using-its-resource-id"></a>Excluir recurso de aplicativo usando sua ID de recurso
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Passos Seguintes
Obter informações sobre o modelo de recurso de aplicativo:

* [Modelar um aplicativo no Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Service Fabric manifestos de aplicativo e serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>Consultar Também
* [Melhores práticas](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [Gerenciar aplicativos e serviços como recursos do Azure](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png

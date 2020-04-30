---
title: Modelo de recurso de aplicação Azure Service Fabric
description: Este artigo fornece uma visão geral da gestão de uma aplicação Azure Service Fabric utilizando o Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682632"
---
# <a name="service-fabric-application-resource-model"></a>Modelo de recursos de aplicação de tecido de serviço

Tem várias opções para implementar aplicações azure service fabric no seu cluster Service Fabric. Recomendamos a utilização do Gestor de Recursos Azure. Se utilizar o Gestor de Recursos, pode descrever aplicações e serviços na JSON e, em seguida, implantá-los no mesmo modelo de Gestor de Recursos que o seu cluster. Ao contrário de utilizar o PowerShell ou o Azure CLI para implementar e gerir aplicações, se utilizar o Gestor de Recursos, não precisa de esperar que o cluster esteja pronto; o registo, o provisionamento e a implantação da candidatura podem acontecer num só passo. Usar o Gestor de Recursos é a melhor forma de gerir o ciclo de vida da aplicação no seu cluster. Para mais informações, consulte [As melhores práticas: Infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Gerir as suas aplicações como recursos em Gestor de Recursos pode ajudá-lo a obter melhorias nestas áreas:

* Pista de auditoria: O Gestor de Recursos audita todas as operações e mantém um registo de atividade detalhado. Um registo de atividade pode ajudá-lo a rastrear quaisquer alterações feitas nas aplicações e no seu cluster.
* Controlo de acesso baseado em funções: Pode gerir o acesso a clusters e a aplicações implementadas no cluster utilizando o mesmo modelo de Gestor de Recursos.
* Eficiência de gestão: A utilização do Gestor de Recursos dá-lhe uma única localização (o portal Azure) para gerir o seu cluster e implementações críticas de aplicações.

Neste documento, aprenderá a:

> [!div class="checklist"]
>
> * Implementar recursos de aplicação utilizando o Gestor de Recursos.
> * Atualize os recursos de aplicação utilizando o Gestor de Recursos.
> * Eliminar os recursos de aplicação.

## <a name="deploy-application-resources"></a>Implementar recursos de aplicação

As medidas de alto nível que toma para implementar uma aplicação e os seus serviços utilizando o modelo de recursos de aplicação do Gestor de Recursos são:
1. Embala o código de inscrição.
1. Faça o upload da encomenda.
1. Consulte a localização do pacote num modelo de Gestor de Recursos como recurso de aplicação. 

Para mais informações, consulte [pacote uma aplicação](service-fabric-package-apps.md#create-an-sfpkg).

Em seguida, cria um modelo de Gestor de Recursos, atualiza o ficheiro de parâmetros com detalhes da aplicação e implementa o modelo no cluster Service Fabric. [Explore as amostras.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para implementar uma aplicação a partir de um modelo de Gestor de Recursos, você deve ter uma conta de armazenamento. A conta de armazenamento é utilizada para encenar a imagem da aplicação. 

Pode reutilizar uma conta de armazenamento existente ou pode criar uma nova conta de armazenamento para a realização das suas aplicações. Se utilizar uma conta de armazenamento existente, pode saltar este passo. 

![Criar uma conta de armazenamento][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Configure a sua conta de armazenamento

Após a criação da conta de armazenamento, cria-se um recipiente de bolha onde as aplicações podem ser encenadas. No portal Azure, vá à conta de Armazenamento Azure onde pretende armazenar as suas aplicações. Selecione **Blobs** > **Adicionar Recipiente**. 

Os recursos do seu cluster podem ser assegurados estabelecendo o nível de acesso público ao **privado.** Pode conceder acesso de várias formas:

* Autorize o acesso a bolhas e filas utilizando o [Diretório Ativo Azure](../storage/common/storage-auth-aad-app.md).
* Conceder acesso à bolha Azure e aos dados da fila utilizando [o RBAC no portal Azure.](../storage/common/storage-auth-aad-rbac-portal.md)
* Delege o acesso utilizando uma assinatura de [acesso partilhado.](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)

O exemplo na seguinte imagem utiliza acesso de leitura anónimo para bolhas.

![Criar bolha][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Fase da aplicação na sua conta de armazenamento

Antes de poder implementar uma aplicação, deve encenar a aplicação no armazenamento de blob. Neste tutorial, criamos manualmente o pacote de aplicação. Tenha em mente que este passo pode ser automatizado. Para mais informações, consulte [Pacote uma aplicação](service-fabric-package-apps.md#create-an-sfpkg). 

Neste tutorial, utilizamos a aplicação da [amostra de voto.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)

1. No Estúdio Visual, clique no projeto **De Votação** e, em seguida, selecione **Pacote**.

   ![Aplicação de pacotes][PackageApplication]  
1. Vá ao diretório *.\service-fabric-dotnet-quickstart\Vote\pkg\Debug.* Feche o conteúdo num ficheiro chamado *Vote.zip*. O ficheiro *ApplicationManifest.xml* deve estar na raiz do ficheiro zip.

   ![Aplicação Zip][ZipApplication]  
1. Mude o nome do ficheiro para alterar a extensão de .zip para *.sfpkg*.

1. No portal Azure, no recipiente de **aplicações** para a sua conta de armazenamento, selecione **Upload**, e, em seguida, carregue **Vote.sfpkg**. 

   ![Pacote de aplicativos de upload][UploadAppPkg]

Agora, a aplicação está agora encenou e pode criar o modelo de Gestor de Recursos para implementar a aplicação.

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager

A aplicação da amostra contém modelos do Gestor de [Recursos Azure](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) que pode utilizar para implementar a aplicação. Os nomes dos ficheiros do modelo são *UserApp.json* e *UserApp.Parameters.json*.

> [!NOTE]
> O ficheiro *UserApp.Parameters.json* deve ser atualizado com o nome do seu cluster.
>
>

| Parâmetro              | Descrição                                 | Exemplo                                                      | Comentários                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | O nome do aglomerado para o qual está a implantar | sf-cluster123                                                |                                                              |
| aplicação            | O nome da aplicação                 | Votar                                                       |
| aplicaçãoTypeName    | O nome tipo do pedido           | Tipo de voto                                                   | Deve corresponder applicationManifest.xml                 |
| aplicaçãoTypeVersion | A versão do tipo de aplicação         | 1.0.0                                                        | Deve corresponder applicationManifest.xml                 |
| serviceName            | O nome do serviço         | Voting~VotingWeb                                             | Deve estar no formato ApplicationName~ServiceType            |
| serviçoTypeName        | O nome tipo do serviço                | VotingWeb                                                    | Deve corresponder serviceManifest.xml                 |
| appPackageUrl          | O URL de armazenamento blob da aplicação     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | O URL do pacote de aplicação no armazenamento de bolhas (o procedimento para definir o URL é descrito mais tarde no artigo) |

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

Executar o cmdlet **New-AzResourceGroupDeployment** cmdlet para implementar a aplicação para o grupo de recursos que contém o seu cluster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Atualize a aplicação De Tecido de Serviço utilizando o Gestor de Recursos

Você pode atualizar uma aplicação que já está implantada para um cluster de Tecido de Serviço por uma destas razões:

* Um novo serviço é adicionado à aplicação. Uma definição de serviço deve ser adicionada aos ficheiros *manifesto.xml* e *de pedido-manifesto.xml* quando um serviço é adicionado à aplicação. Para refletir uma nova versão de uma aplicação, também deve alterar a versão do tipo de aplicação de 1.0.0 para 1.0.1 no [UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
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

* Uma nova versão de um serviço existente é adicionada à aplicação. Exemplos incluem alterações de código de aplicação e atualizações para versão e nome tipo app. Para esta atualização, atualize userApp.Parameters.json como este:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Eliminar recursos de aplicação

Para eliminar uma aplicação que foi implementada utilizando o modelo de recursos de aplicação no Gestor de Recursos:

1. Utilize o cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) para obter o ID de recurso para a aplicação:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Utilize o cmdlet [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) para eliminar os recursos de aplicação:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Passos seguintes

Obtenha informações sobre o modelo de recursos de aplicação:

* [Modelo de uma aplicação em Tecido de Serviço](service-fabric-application-model.md)
* [Formulários de aplicação e serviço de tecido de serviço](service-fabric-application-and-service-manifests.md)
* [Boas práticas: Infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Gerir aplicações e serviços como recursos Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png

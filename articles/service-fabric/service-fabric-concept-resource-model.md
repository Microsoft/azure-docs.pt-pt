---
title: Modelo de recurso de aplicação Azure Service Fabric
description: Este artigo fornece uma visão geral da gestão de uma aplicação Azure Service Fabric utilizando o Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0019f154f301d2b688d4c16c9adb36ec386adef2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790728"
---
# <a name="service-fabric-application-resource-model"></a>Modelo de recurso de aplicação de tecido de serviço

Tem várias opções para implementar aplicações Azure Service Fabric no seu cluster de Tecido de Serviço. Recomendamos a utilização do Gestor de Recursos Azure. Se utilizar o Gestor de Recursos, pode descrever aplicações e serviços em JSON e, em seguida, implantá-los no mesmo modelo de Gestor de Recursos que o seu cluster. Ao contrário de utilizar o PowerShell ou o Azure CLI para implementar e gerir aplicações, se utilizar o Resource Manager, não tem de esperar que o cluster esteja pronto; registo de pedidos, provisionamento e implantação podem acontecer numa só etapa. Utilizar o Resource Manager é a melhor forma de gerir o ciclo de vida da aplicação no seu cluster. Para mais informações, consulte [as melhores práticas: Infraestruturas como código.](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)

Gerir as suas aplicações como recursos no Resource Manager pode ajudá-lo a obter melhorias nestas áreas:

* Pista de auditoria: Gestor de Recursos audita cada operação e mantém um registo de atividade detalhado. Um registo de atividade pode ajudá-lo a rastrear quaisquer alterações feitas nas aplicações e no seu cluster.
* Controlo de acesso baseado em funções: Pode gerir o acesso a clusters e aplicações implementadas no cluster utilizando o mesmo modelo de Gestor de Recursos.
* Eficiência de gestão: A utilização do Gestor de Recursos dá-lhe uma única localização (o portal Azure) para gerir o seu cluster e implementações de aplicações críticas.

Neste documento, aprenderá a:

> [!div class="checklist"]
>
> * Implemente recursos de aplicação utilizando o Gestor de Recursos.
> * Atualize os recursos da aplicação utilizando o Gestor de Recursos.
> * Eliminar recursos de aplicação.

## <a name="deploy-application-resources"></a>Implementar recursos de aplicação

As medidas de alto nível que toma para implementar uma aplicação e os seus serviços utilizando o modelo de recurso de aplicação do Gestor de Recursos são:
1. Embale o código de inscrição.
1. Faça o upload do pacote.
1. Refira a localização do pacote num modelo de Gestor de Recursos como um recurso de aplicação. 

Para mais informações, consulte [uma aplicação para o Pacote](service-fabric-package-apps.md#create-an-sfpkg).

Em seguida, cria um modelo de Gestor de Recursos, atualiza o ficheiro de parâmetros com detalhes da aplicação e implementa o modelo no cluster de Tecido de Serviço. [Explore amostras.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para implementar uma aplicação a partir de um modelo de Gestor de Recursos, tem de ter uma conta de armazenamento. A conta de armazenamento é usada para encenar a imagem da aplicação. 

Pode reutilizar uma conta de armazenamento existente ou criar uma nova conta de armazenamento para a realização das suas aplicações. Se utilizar uma conta de armazenamento existente, pode saltar este passo. 

![Criar uma conta de armazenamento][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Configure a sua conta de armazenamento

Após a criação da conta de armazenamento, cria-se um recipiente blob onde as aplicações podem ser encenadas. No portal Azure, aceda à conta Azure Storage onde pretende armazenar as suas aplicações. Selecione **Blobs**  >  **Adicionar Recipiente**. 

Os recursos do seu cluster podem ser assegurados definindo o nível de acesso público a **privados.** Pode conceder acesso de várias formas:

* Autorizar o acesso a bolhas e filas utilizando [o Azure Ative Directory](../storage/common/storage-auth-aad-app.md).
* Conceder acesso aos dados de blob e fila Azure utilizando [o Azure RBAC no portal Azure](../storage/common/storage-auth-aad-rbac-portal.md).
* Delege o acesso utilizando uma [assinatura de acesso partilhado.](/rest/api/storageservices/delegate-access-with-shared-access-signature)

O exemplo na imagem a seguir utiliza acesso de leitura anónimo para bolhas.

![Criar bolha][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Encenar a aplicação na sua conta de armazenamento

Antes de poder implementar uma aplicação, tem de encenar a aplicação no armazenamento de bolhas. Neste tutorial, criamos o pacote de aplicações manualmente. Tenha em mente que este passo pode ser automatizado. Para mais informações, consulte [pacote uma aplicação.](service-fabric-package-apps.md#create-an-sfpkg) 

Neste tutorial, utilizamos o [pedido de amostra de voto.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)

1. No Estúdio Visual, clique à direita no projeto **De Votação** e, em seguida, selecione **Pacote**.

   ![Aplicação de pacotes][PackageApplication]  
1. Vá ao *diretório .\service-fabric-dotnet-quickstart\Vot\pkg\Debug.* Feche o conteúdo num ficheiro chamado *Voting.zip*. O *ficheiroApplicationManifest.xml* deve estar na raiz do ficheiro zip.

   ![Aplicação Zip][ZipApplication]  
1. Mude o nome do ficheiro para alterar a extensão de .zip para *.sfpkg*.

1. No portal Azure, no recipiente de **aplicações** para a sua conta de armazenamento, selecione **Upload** e, em seguida, carreja **Voting.sfpkg**. 

   ![Pacote de aplicativos de upload][UploadAppPkg]

Agora, a aplicação está agora encenada e pode criar o modelo de Gestor de Recursos para implementar a aplicação.

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager

A aplicação da amostra contém [modelos do Gestor de Recursos Azure](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) que pode utilizar para implementar a aplicação. Os nomes dos ficheiros do modelo são *UserApp.js* e *UserApp.Parameters.jsem*.

> [!NOTE]
> O *UserApp.Parameters.jsem* ficheiro deve ser atualizado com o nome do seu cluster.
>
>


| Parâmetro              | Descrição                                 | Exemplo                                                      | Comentários                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | O nome do cluster para o qual está a implantar | sf-cluster123                                                |                                                              |
| aplicação            | O nome da aplicação                 | Votar                                                       |
| aplicativoTypeName    | O nome-tipo da aplicação           | Tipo de votação                                                   | Deve coincidir ApplicationManifest.xml                 |
| aplicaçãoTypeVersion | A versão do tipo de aplicação         | 1.0.0                                                        | Deve coincidir ApplicationManifest.xml                 |
| nome de serviço            | O nome do serviço         | Votação~VoteWeb                                             | Deve estar no formato ApplicationName~ServiceType            |
| serviçoTypeName        | O nome-tipo do serviço                | VotingWeb                                                    | Deve coincidir ServiceManifest.xml                 |
| appPackageUrl          | O URL de armazenamento de bolhas da aplicação     | https: \/ /servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | O URL do pacote de aplicações no armazenamento de bolhas (o procedimento para definir o URL é descrito mais tarde no artigo) |

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

Executar o **cmdlet New-AzResourceGroupDeployment** para implementar a aplicação no grupo de recursos que contém o seu cluster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Atualize a aplicação de Tecido de Serviço utilizando o Gestor de Recursos


> [!IMPORTANT]
> Qualquer serviço que seja implantado através da definição ARM JSON deve ser removido da secção DefaultServices do ficheiro ApplicationManifest.xml correspondente.


Você pode atualizar uma aplicação que já foi implantada para um cluster de Tecido de Serviço por uma destas razões:

* Um novo serviço é adicionado à aplicação. Uma definição de serviço deve ser adicionada aos ficheiros *service-manifest.xml* e *application-manifest.xml* quando um serviço é adicionado à aplicação. Para refletir uma nova versão de uma aplicação, também deve alterar a versão do tipo de aplicação de 1.0.0 para 1.0.1 em [UserApp.Parameters.jsem](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

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

* Uma nova versão de um serviço existente é adicionada à aplicação. Exemplos incluem alterações de código de aplicação e atualizações para a versão e nome do tipo de aplicação. Para esta atualização, a atualização UserApp.Parameters.jsassim:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Eliminar recursos de aplicação

Para eliminar uma aplicação que foi implementada utilizando o modelo de recursos de aplicação no Gestor de Recursos:

1. Utilize o [cmdlet Get-AzResource](/powershell/module/az.resources/get-azresource) para obter o ID de recursos para a aplicação:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Utilize o [cmdlet Remove-AzResource](/powershell/module/az.resources/remove-azresource) para eliminar os recursos da aplicação:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Próximos passos

Obtenha informações sobre o modelo de recursos de aplicação:

* [Modelar uma aplicação em Tecido de Serviço](service-fabric-application-model.md)
* [Aplicação de tecido de serviço e manifestos de serviço](service-fabric-application-and-service-manifests.md)
* [Melhores práticas: Infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Gerir aplicações e serviços como recursos da Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png

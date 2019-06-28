---
title: Criar e implementar projetos de grupo de recursos do Visual Studio do Azure
description: Utilize o Visual Studio para criar um projeto do grupo de recursos do Azure e implementar os recursos no Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: tomfitz
ms.openlocfilehash: 8677d906375853bdde5c192c86dacc7479f2e31e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311328"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Criar e implementar grupos de recursos do Azure através do Visual Studio

Com o Visual Studio, pode criar um projeto que implementa a sua infraestrutura e o código para o Azure. Por exemplo, pode implementar o host da web, o web site e o código para o web site. O Visual Studio fornece muitos modelos de introdução diferentes para cenários comuns de implementação. Neste artigo, vai implementar uma aplicação web.  

Este artigo mostra como usar [Visual Studio 2019 ou posterior com o desenvolvimento do Azure e cargas de trabalho do ASP.NET instaladas](/visualstudio/install/install-visual-studio?view=vs-2019). Se usar o Visual Studio 2017, sua experiência é basicamente o mesmo.

## <a name="create-azure-resource-group-project"></a>Criar projeto do Grupo de Recursos do Azure

Nesta secção, vai criar um projeto do grupo de recursos do Azure com um **aplicação Web** modelo.

1. No Visual Studio, escolha **arquivo**, **New**, e **projeto**. Selecione o **grupo de recursos do Azure** modelo de projeto e **próxima**.

    ![Criar o projeto](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Dê um nome ao seu projeto. A predefinição definições estão provavelmente bem, mas analisá-los para que eles trabalham para o seu ambiente. Quando terminar, selecione **Criar**.

    ![Criar o projeto](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-project.png)

1. Escolha o modelo que pretende implementar no Azure Resource Manager. Tenha em atenção que existem muitas opções diferentes com base no tipo de projeto que pretende implementar. Neste artigo, escolha o **aplicação Web** modelo e **OK**.

    ![Escolher um modelo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    O modelo que escolher é apenas um ponto de partida; pode adicionar e remover recursos para satisfazer o seu cenário.

1. Visual Studio cria um projeto de implementação do grupo de recursos para a aplicação web. Para ver os ficheiros para o seu projeto, observe o nó no projeto de implantação.

    ![Mostrar nós](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Uma vez que escolhemos o modelo de aplicativo Web, verá os seguintes ficheiros:

   | Nome de ficheiro | Descrição |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Um script do PowerShell que executa comandos do PowerShell para implementar no Azure Resource Manager. Visual Studio utiliza este script do PowerShell para implementar o modelo. |
   | WebSite.json |O modelo do Resource Manager que define a infraestrutura que pretende implementar no Azure e os parâmetros que pode fornecer durante a implementação. Define as dependências entre os recursos para que o Resource Manager os implemente na ordem correta. |
   | WebSite.parameters.json |Um ficheiro de parâmetros que tem os valores que são precisos para o modelo. Passa nos valores dos parâmetros para personalizar cada implementação. |

    Todos os projetos de implementação do grupo de recursos têm estes ficheiros básicos. Outros projetos poderão ter ficheiros adicionais para suportar outras funcionalidades.

## <a name="customize-resource-manager-template"></a>Personalizar o modelo do Resource Manager

Pode personalizar um projeto de implantação, modificando o modelo do Resource Manager que descreve os recursos que pretende implementar. Para saber mais sobre os elementos do modelo do Resource Manager, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).

1. Para trabalhar no seu modelo, abra **WebSite.json**.

1. O editor do Visual Studio fornece ferramentas para o ajudar a editar o modelo do Resource Manager. A janela **Contorno de JSON** faz com que seja mais fácil ver os elementos definidos no seu modelo.

   ![Mostrar contorno de JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

1. Selecione um elemento no contorno para ir para essa parte do modelo.

   ![navegar no JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

1. Pode adicionar um recurso, selecionando o botão **Adicionar Recurso** na parte superior da janela Contorno de JSON ou clicando com o botão direito do rato em **recursos** e selecionando **Adicionar Novo Recurso**.

   ![Adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

1. Selecione **conta de armazenamento** e atribua um nome. Forneça um nome com um máximo de 11 carateres e apenas números e letras minúsculas.

   ![Adicionar armazenamento](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

1. Tenha em atenção que o recurso foi adicionado, mas também um parâmetro para a conta do Storage do tipo e uma variável para o nome da conta do Storage.

   ![Mostrar contorno](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

1. O parâmetro para o tipo de conta de armazenamento é predefinido com tipos permitidos e um tipo predefinido. Pode deixar estes valores ou editá-los para o seu cenário. Se não pretender que ninguém implemente uma conta de armazenamento **Premium_LRS** através deste modelo, remova-a dos tipos permitidos.

   ```json
   "demoaccountType": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_ZRS",
       "Standard_GRS",
       "Standard_RAGRS"
     ]
   }
   ```

1. O Visual Studio também fornece intellisense para ajudar a compreender as propriedades que estão disponíveis quando editar o modelo. Por exemplo, para editar as propriedades para o seu plano do Serviço de Aplicações, navegue até ao recurso **HostingPlan** e adicione um valor para as **propriedades**. Repare que o intellisense mostra os valores disponíveis e fornece uma descrição desse valor.

   ![Mostrar o intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

   Pode definir **numberOfWorkers** como 1 e guarde o ficheiro.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Abra o **WebSite.parameters.json** ficheiro. Utilize o ficheiro de parâmetros para passar valores durante a implementação que personalizam o recurso a ser implementado. Dê um nome ao plano de alojamento e guarde o ficheiro.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Implementar o projeto no Azure

Agora, está pronto para implementar o projeto para um grupo de recursos.

Por predefinição, o script do PowerShell (Deploy-AzureResourceGroup.ps1) no projeto utiliza o módulo AzureRM. Se ainda tiver o módulo de AzureRM instalado e pretende continuar a utilizá-lo, pode utilizar este script de predefinição. Com este script, pode utilizar a interface do Visual Studio para implementar a sua solução.

No entanto, se tiver migrado para o novo [módulo Az](/powershell/azure/new-azureps-module-az), precisa adicionar um novo script ao seu projeto. Para adicionar um script que utiliza o módulo de Az, copie os [Deploy AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) do script e adicioná-lo ao seu projeto. Para utilizar este script para a implementação, tem de executá-lo de uma consola do PowerShell, em vez de utilizar a interface de implementação do Visual Studio.

Ambas as abordagens são apresentadas neste artigo. Este artigo refere-se para o script de predefinição como o script de módulo AzureRM e o novo script como o script de módulo Az.

### <a name="az-module-script"></a>Script de módulo de AZ

Para o script de módulo de Az, abra uma consola do PowerShell e execute:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Script de módulo de AzureRM

Para o script de módulo AzureRM, utilize o Visual Studio:

1. No menu de atalho do nó do projeto de implementação, selecione **Implementar** > **Novo**.

    ![Novo item de menu de implementação](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

1. É apresentada a caixa de diálogo **Implementar no Grupo de Recursos**. Na caixa de diálogo pendente **Grupo de recursos**, escolha um grupo de recursos existente ou crie um novo. Selecione **Implementar**.

    ![Implementar a caixa de diálogo de grupo de recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. O estado da implementação é apresentado nas janelas **Saída**. Após concluir a implementação, a última mensagem indicará uma implementação bem sucedida com algo semelhante a:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Ver recursos implementados

Vamos ver os resultados.

1. Num browser, abra o [Portal do Azure](https://portal.azure.com/) e inicie sessão na sua conta. Para ver o grupo de recursos, selecione **Grupos de recursos** e o grupo de recursos no qual implementou.

1. Veja todos os recursos implementados. Repare que o nome da conta de armazenamento não é exatamente aquele que especificou quando adicionou esse recurso. A conta de armazenamento deve ser exclusiva. O modelo adiciona automaticamente uma cadeia de carateres para o nome que indicou para criar um nome exclusivo.

    ![Mostrar recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Adicionar código ao projeto

Neste momento, implementou a infraestrutura para a sua aplicação, mas não existe qualquer código real implementado com o projeto.

1. Adicione um projeto à sua solução do Visual Studio. Clique com o botão direito do rato na solução e selecione **Adicionar** > **Novo Projeto**.

    ![Adicionar projeto](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Adicionar uma **aplicação Web ASP.NET Core**.

    ![adicionar a aplicação web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)

1. Dê um nome ao seu aplicativo da web e selecione **criar**.

    ![Aplicação web de nome](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-web-app.png)

1. Selecione **aplicação Web** e **criar**.

    ![Selecione a aplicação Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project-type.png)

1. Depois de o Visual Studio ter criado a sua aplicação Web, verá ambos os projetos na solução.

    ![Mostrar projetos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Agora, tem de verificar se o projeto do grupo de recursos deteta o novo projeto. Regresse ao seu projeto de grupo de recursos (ExampleAppDeploy). Clique com o botão direito do rato em **Referências** e selecione **Adicionar Referência**.

    ![Adicionar referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Selecione o projeto da aplicação Web criado.

   ![Adicionar referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)

   Ao adicionar uma referência, liga o projeto de aplicação web para o projeto do grupo de recursos e define automaticamente algumas propriedades. Pode ver estas propriedades na janela **Propriedades** da referência. O **Incluir Caminho do Ficheiro** tem o caminho onde o pacote é criado. Anote a pasta (ExampleApp) e o ficheiro (package.zip). É necessário saber estes valores, uma vez que os fornece como parâmetros ao implementar a aplicação.

   ![ver referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)

1. Volte ao seu modelo (WebSite.json) e adicione um recurso ao modelo.

    ![Adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Desta vez, selecione **Implementação Web de Aplicações Web**. 

    ![Adicionar web implementar](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)

   Guarde o seu modelo.

1. Existem alguns parâmetros novos no seu modelo. Foram adicionadas no passo anterior. Não precisa de fornecer valores para **artifactslocation** ou **artifactslocationsastoken** porque esses valores são gerados automaticamente. No entanto, é necessário definir a pasta e o nome de ficheiro para o caminho que contém o pacote de implementação. Os nomes destes parâmetros terminam com **PackageFolder** e **PackageFileName**. A primeira parte do nome é o nome do recurso Web Deploy que adicionou. Neste artigo, estamos denominados **ExampleAppPackageFolder** e **ExampleAppPackageFileName**. 

   Open **Website.parameters.json** e defina os parâmetros para os valores que viu nas propriedades de referência. Definir **ExampleAppPackageFolder** para o nome da pasta. Definir **ExampleAppPackageFileName** para o nome do ficheiro zip.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Implementar código com a infraestrutura

Como adicionou o código ao projeto, a implementação é um pouco diferente neste momento. Durante a implementação, testar artefactos para o seu projeto para um local que podem aceder a Resource Manager. Os artefactos foram transitados para uma conta de armazenamento.

### <a name="az-module-script"></a>Script de módulo de AZ

Há uma pequena alteração, que tem de certificar-se ao seu modelo se estiver a utilizar o script de módulo Az. Este script adiciona uma barra para a localização de artefatos, mas o modelo não espere essa barra. Abra WebSite.json e localizar as propriedades para a extensão de MSDeploy. Ele possui uma propriedade chamada **packageUri**. Remova a barra entre a localização de artefactos e a pasta do pacote.

Deve ser semelhante:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Observe que no exemplo anterior, não há nenhuma `'/',` entre **parameters('_artifactsLocation')** e **parameters('ExampleAppPackageFolder')** .

Reconstrua o projeto. Compilar o projeto torna-se de que os ficheiros de que necessita para implementar são adicionados para a pasta de transição.

Agora, abra uma consola do PowerShell e execute:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Script de módulo de AzureRM

Para o script de módulo AzureRM, utilize o Visual Studio:

1. Para voltar a implementar, escolha **Deploy**e o grupo de recursos que implementou anteriormente.

    ![Implementar novamente o projeto](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

1. Selecione a conta de armazenamento que implementou com este grupo de recursos para o **conta de armazenamento de artefactos**.

   ![Voltar a implementar web implementar](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy-web-app.png)

## <a name="view-web-app"></a>Aplicação web de modo de exibição

1. Após concluir a implementação, selecione a aplicação Web no portal. Selecione o URL para navegar para o site.

   ![Procurar site](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Repare que implementou com sucesso a aplicação ASP.NET predefinida.

   ![Mostrar aplicação implementada](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Adicionar dashboard de operações

Não está limitado apenas aos recursos disponíveis através da interface do Visual Studio. Pode personalizar a sua implementação, ao adicionar um recurso personalizado ao seu modelo. Para mostrar a adição de um recurso, adicione um dashboard operacional para gerir os recursos que implementou.

1. Abra o ficheiro de WebSite.json e adicione o seguinte JSON após o recurso de conta de armazenamento, mas antes do `]` da secção de recursos.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "type": "Microsoft.Portal/dashboards",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Reimplemente o projeto.

1. Após a implementação estiver concluída, ver o dashboard no portal. Selecione **Dashboard** e escolhe aquela que implementou.

   ![Dashboard Personalizado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

1. Ver o dashboard personalizado.

   ![Dashboard Personalizado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

Pode gerir o acesso ao dashboard através de grupos do RBAC. Também pode personalizar a aparência do dashboard após a sua implementação. No entanto, se voltar a implementar o grupo de recursos, o dashboard ficará no estado predefinido no seu modelo. Para obter mais informações sobre a criação de dashboards, veja [Criar Dashboards do Azure programaticamente](../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal do Azure, selecione **grupos de recursos** no menu à esquerda.

1. Selecione o nome do grupo de recursos.

1. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, aprendeu a criar e implementar modelos com o Visual Studio. O próximo tutorial mostra-lhe como localizar as informações de referência de modelo para que possa criar uma conta de Armazenamento do Azure encriptada.

> [!div class="nextstepaction"]
> [Criar conta de armazenamento encriptada](./resource-manager-tutorial-create-encrypted-storage-accounts.md)

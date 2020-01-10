---
title: Criar & implantar projetos do grupo de recursos do Visual Studio
description: Use o Visual Studio para criar um projeto de grupo de recursos do Azure e implantar os recursos no Azure.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: ee76c2ae94373b63be6a1efd9ae13d4629dd98c8
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680635"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Criar e implementar grupos de recursos do Azure através do Visual Studio

Com o Visual Studio, pode criar um projeto que implementa a sua infraestrutura e o código para o Azure. Por exemplo, você pode implantar o host da Web, o site e o código para o site. O Visual Studio fornece muitos modelos de introdução diferentes para cenários comuns de implementação. Neste artigo, você implantará um aplicativo Web.

Este artigo mostra como usar [o Visual Studio 2019 ou posterior com as cargas de trabalho de desenvolvimento e ASP.net do Azure instaladas](/visualstudio/install/install-visual-studio?view=vs-2019). Se você usar o Visual Studio 2017, sua experiência será basicamente a mesma.

## <a name="create-azure-resource-group-project"></a>Criar projeto do Grupo de Recursos do Azure

Nesta seção, você cria um projeto de grupo de recursos do Azure com um modelo de **aplicativo Web** .

1. No Visual Studio, escolha **arquivo**>**novo** **projeto**de>.
1. Selecione o modelo de projeto do **grupo de recursos do Azure** e **Avançar**.

    ![Criar o projeto](./media/create-visual-studio-deployment-project/create-project.png)

1. Dê um nome ao seu projeto. As outras configurações padrão provavelmente estão corretas, mas examine-as para que elas funcionem para o seu ambiente. Quando terminar, selecione **Criar**.

    ![Criar o projeto](./media/create-visual-studio-deployment-project/name-project.png)

1. Escolha o modelo que pretende implementar no Azure Resource Manager. Tenha em atenção que existem muitas opções diferentes com base no tipo de projeto que pretende implementar. Para este artigo, escolha o modelo de **aplicativo Web** e **OK**.

    ![Escolher um modelo](./media/create-visual-studio-deployment-project/select-project.png)

    O modelo que escolher é apenas um ponto de partida; pode adicionar e remover recursos para satisfazer o seu cenário.

1. O Visual Studio cria um projeto de implantação de grupo de recursos para o aplicativo Web. Para ver os arquivos do seu projeto, examine o nó no projeto de implantação.

    ![Mostrar nós](./media/create-visual-studio-deployment-project/show-items.png)

    Como você escolheu o modelo de aplicativo Web, verá os seguintes arquivos:

   | Nome de ficheiro | Descrição |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Um script do PowerShell que executa comandos do PowerShell para implementar no Azure Resource Manager. O Visual Studio usa esse script do PowerShell para implantar seu modelo. |
   | WebSite.json |O modelo do Resource Manager que define a infraestrutura que pretende implementar no Azure e os parâmetros que pode fornecer durante a implementação. Define as dependências entre os recursos para que o Resource Manager os implemente na ordem correta. |
   | WebSite.parameters.json |Um ficheiro de parâmetros que tem os valores que são precisos para o modelo. Passa nos valores dos parâmetros para personalizar cada implementação. |

    Todos os projetos de implementação do grupo de recursos têm estes ficheiros básicos. Outros projetos poderão ter ficheiros adicionais para suportar outras funcionalidades.

## <a name="customize-resource-manager-template"></a>Personalizar modelo do Resource Manager

Você pode personalizar um projeto de implantação modificando o modelo do Resource Manager que descreve os recursos que você deseja implantar. Para saber mais sobre os elementos do modelo do Resource Manager, consulte [Criação de modelos do Azure Resource Manager](template-syntax.md).

1. Para trabalhar em seu modelo, abra **site. JSON**.

1. O editor do Visual Studio fornece ferramentas para o ajudar a editar o modelo do Resource Manager. A janela **Contorno de JSON** faz com que seja mais fácil ver os elementos definidos no seu modelo.

   ![Mostrar estrutura de tópicos JSON](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Selecione um elemento na estrutura de tópicos para ir para essa parte do modelo.

   ![Navegar pelo JSON](./media/create-visual-studio-deployment-project/navigate-json.png)

1. Pode adicionar um recurso, selecionando o botão **Adicionar Recurso** na parte superior da janela Contorno de JSON ou clicando com o botão direito do rato em **recursos** e selecionando **Adicionar Novo Recurso**.

   ![Adicionar recurso](./media/create-visual-studio-deployment-project/add-resource.png)

1. Selecione **conta de armazenamento** e dê a ela um nome. Forneça um nome com um máximo de 11 carateres e apenas números e letras minúsculas.

   ![Adicionar armazenamento](./media/create-visual-studio-deployment-project/add-storage.png)

1. Tenha em atenção que o recurso foi adicionado, mas também um parâmetro para a conta do Storage do tipo e uma variável para o nome da conta do Storage.

   ![Mostrar estrutura de tópicos](./media/create-visual-studio-deployment-project/show-new-items.png)

1. O parâmetro para o tipo de conta de armazenamento é predefinido com tipos permitidos e um tipo padrão. Pode deixar estes valores ou editá-los para o seu cenário. Se não pretender que ninguém implemente uma conta de armazenamento **Premium_LRS** através deste modelo, remova-a dos tipos permitidos.

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

1. O Visual Studio também fornece IntelliSense para ajudá-lo a entender as propriedades disponíveis ao editar o modelo. Por exemplo, para editar as propriedades para o seu plano do Serviço de Aplicações, navegue até ao recurso **HostingPlan** e adicione um valor para as **propriedades**. Repare que o intellisense mostra os valores disponíveis e fornece uma descrição desse valor.

   ![Mostrar IntelliSense](./media/create-visual-studio-deployment-project/show-intellisense.png)

   Você pode definir **numberOfWorkers** como 1 e salvar o arquivo.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Abra o arquivo **site. Parameters. JSON** . Você usa o arquivo de parâmetros para passar valores durante a implantação que personalizam o recurso que está sendo implantado. Dê um nome ao plano de hospedagem e salve o arquivo.

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

## <a name="deploy-project-to-azure"></a>Implantar projeto no Azure

Agora você está pronto para implantar seu projeto em um grupo de recursos.

Por padrão, o script do PowerShell (Deploy-azureresourcegroup. ps1) no projeto usa o módulo AzureRM. Se você ainda tiver o módulo AzureRM instalado e quiser continuar a usá-lo, poderá usar esse script padrão. Com esse script, você pode usar a interface do Visual Studio para implantar sua solução.

No entanto, se você migrou para o novo [módulo AZ](/powershell/azure/new-azureps-module-az), precisará adicionar um novo script ao seu projeto. Para adicionar um script que usa o módulo AZ, copie o script [Deploy-AzTemplate. ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) e adicione-o ao seu projeto. Para usar esse script para implantação, você deve executá-lo em um console do PowerShell, em vez de usar a interface de implantação do Visual Studio.

Ambas as abordagens são mostradas neste artigo. Este artigo refere-se ao script padrão como o script de módulo AzureRM e o novo script como o script de módulo AZ.

### <a name="az-module-script"></a>Script de módulo AZ

Para o script de módulo AZ, abra um console do PowerShell e execute:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Script de módulo AzureRM

Para o script do módulo AzureRM, use o Visual Studio:

1. No menu de atalho do nó do projeto de implementação, selecione **Implementar** > **Novo**.

    ![Novo item de menu de implantação](./media/create-visual-studio-deployment-project/deploy.png)

1. É apresentada a caixa de diálogo **Implementar no Grupo de Recursos**. Na caixa de diálogo pendente **Grupo de recursos**, escolha um grupo de recursos existente ou crie um novo. Selecione **Implementar**.

    ![Caixa de diálogo implantar no grupo de recursos](./media/create-visual-studio-deployment-project/show-deployment.png)

1. O estado da implementação é apresentado nas janelas **Saída**. Após concluir a implementação, a última mensagem indicará uma implementação bem sucedida com algo semelhante a:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Exibir recursos implantados

Vamos verificar os resultados.

1. Num browser, abra o [Portal do Azure](https://portal.azure.com/) e inicie sessão na sua conta. Para ver o grupo de recursos, selecione **Grupos de recursos** e o grupo de recursos no qual implementou.

1. Veja todos os recursos implementados. Repare que o nome da conta de armazenamento não é exatamente aquele que especificou quando adicionou esse recurso. A conta de armazenamento deve ser exclusiva. O modelo adiciona automaticamente uma cadeia de caracteres ao nome que você forneceu para criar um nome exclusivo.

    ![Mostrar recursos](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Adicionar código ao projeto

Neste momento, implementou a infraestrutura para a sua aplicação, mas não existe qualquer código real implementado com o projeto.

1. Adicione um projeto à sua solução do Visual Studio. Clique com o botão direito do rato na solução e selecione **Adicionar** > **Novo Projeto**.

    ![Adicionar projeto](./media/create-visual-studio-deployment-project/add-project.png)

1. Adicione um **aplicativo Web ASP.NET Core**.

    ![Adicionar aplicativo Web](./media/create-visual-studio-deployment-project/add-app.png)

1. Dê um nome ao seu aplicativo Web e selecione **criar**.

    ![Nomear aplicativo Web](./media/create-visual-studio-deployment-project/name-web-app.png)

1. Selecione **aplicativo Web** e **criar**.

    ![Selecionar aplicativo Web](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Depois de o Visual Studio ter criado a sua aplicação Web, verá ambos os projetos na solução.

    ![Mostrar projetos](./media/create-visual-studio-deployment-project/show-projects.png)

1. Agora, tem de verificar se o projeto do grupo de recursos deteta o novo projeto. Volte para o projeto do grupo de recursos (ExampleAppDeploy). Clique com o botão direito do rato em **Referências** e selecione **Adicionar Referência**.

    ![Adicionar referência](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Selecione o projeto da aplicação Web criado.

   ![Adicionar referência](./media/create-visual-studio-deployment-project/add-reference.png)

   Ao adicionar uma referência, você vincula o projeto do aplicativo Web ao projeto do grupo de recursos e define automaticamente algumas propriedades. Pode ver estas propriedades na janela **Propriedades** da referência. O **Incluir Caminho do Ficheiro** tem o caminho onde o pacote é criado. Anote a pasta (ExampleApp) e o ficheiro (package.zip). É necessário saber estes valores, uma vez que os fornece como parâmetros ao implementar a aplicação.

   ![Consulte a referência](./media/create-visual-studio-deployment-project/see-reference.png)

1. Volte para o modelo (site. JSON) e adicione um recurso ao modelo.

    ![Adicionar recurso](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Desta vez, selecione **Implementação Web de Aplicações Web**.

    ![Adicionar implantação da Web](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Salve seu modelo.

1. Há alguns novos parâmetros em seu modelo. Elas foram adicionadas na etapa anterior. Você não precisa fornecer valores para **_artifactsLocation** ou **_artifactsLocationSasToken** porque esses valores são gerados automaticamente. No entanto, você precisa definir a pasta e o nome do arquivo para o caminho que contém o pacote de implantação. Os nomes desses parâmetros terminam com **PackageFolder** e **PackageFileName**. A primeira parte do nome é o nome do Implantação da Web recurso que você adicionou. Neste artigo, eles são nomeados **ExampleAppPackageFolder** e **ExampleAppPackageFileName**.

   Abra **website. Parameters. JSON** e defina esses parâmetros para os valores que você viu nas propriedades de referência. Defina **ExampleAppPackageFolder** como o nome da pasta. Defina **ExampleAppPackageFileName** como o nome do arquivo zip.

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

## <a name="deploy-code-with-infrastructure"></a>Implantar código com infraestrutura

Como você adicionou código ao projeto, sua implantação é um pouco diferente desta vez. Durante a implantação, você pode preparar artefatos para o seu projeto para um local que o Resource Manager possa acessar. Os artefatos são preparados para uma conta de armazenamento.

### <a name="az-module-script"></a>Script de módulo AZ

Há uma pequena alteração que você precisa fazer em seu modelo se estiver usando o script de módulo AZ. Esse script adiciona uma barra ao local dos artefatos, mas o modelo não espera essa barra. Abra WebSite. JSON e localize as propriedades da extensão MSDeploy. Ele tem uma propriedade chamada **packageUri**. Remova a barra entre o local dos artefatos e a pasta do pacote.

Deverá ter o seguinte aspeto:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Observe que no exemplo anterior não há nenhum `'/',` entre os **parâmetros (' _artifactsLocation ')** e os **parâmetros (' ExampleAppPackageFolder ')** .

Recompile o projeto. A criação do projeto garante que os arquivos que você precisa implantar sejam adicionados à pasta de preparo.

Agora, abra um console do PowerShell e execute:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Script de módulo AzureRM

Para o script do módulo AzureRM, use o Visual Studio:

1. Para reimplantar, escolha **implantar**e o grupo de recursos que você implantou anteriormente.

    ![Reimplantar projeto](./media/create-visual-studio-deployment-project/redeploy.png)

1. Selecione a conta de armazenamento que você implantou com esse grupo de recursos para a **conta de armazenamento de artefatos**.

   ![Reimplantar a implantação da Web](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Exibir aplicativo Web

1. Após concluir a implementação, selecione a aplicação Web no portal. Selecione o URL para navegar para o site.

   ![Procurar site](./media/create-visual-studio-deployment-project/browse-site.png)

1. Repare que implementou com sucesso a aplicação ASP.NET predefinida.

   ![Mostrar aplicativo implantado](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Adicionar painel de operações

Não está limitado apenas aos recursos disponíveis através da interface do Visual Studio. Pode personalizar a sua implementação, ao adicionar um recurso personalizado ao seu modelo. Para mostrar a adição de um recurso, adicione um dashboard operacional para gerir os recursos que implementou.

1. Abra o arquivo site. JSON e adicione o JSON a seguir após o recurso de conta de armazenamento, mas antes do `]` de fechamento da seção de recursos.

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

1. Reimplante o projeto.

1. Após a conclusão da implantação, exiba seu painel no Portal. Selecione **painel** e escolha aquele que você implantou.

   ![Dashboard Personalizado](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. Você verá o painel personalizado.

   ![Dashboard Personalizado](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

Pode gerir o acesso ao dashboard através de grupos do RBAC. Também pode personalizar a aparência do dashboard após a sua implementação. No entanto, se voltar a implementar o grupo de recursos, o dashboard ficará no estado predefinido no seu modelo. Para obter mais informações sobre a criação de dashboards, veja [Criar Dashboards do Azure programaticamente](../../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **grupos de recursos** no menu à esquerda.

1. Selecione o nome do grupo de recursos.

1. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a criar e implantar modelos usando o Visual Studio. Para saber mais sobre o desenvolvimento de modelos, consulte nossa nova série de tutoriais iniciantes:

> [!div class="nextstepaction"]
> [Tutoriais para iniciantes](./template-tutorial-create-first-template.md)

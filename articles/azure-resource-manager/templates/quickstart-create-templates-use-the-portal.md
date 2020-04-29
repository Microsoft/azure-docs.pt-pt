---
title: Modelo de implantação - Portal Azure
description: Saiba como criar o seu primeiro modelo do Azure Resource Manager com o portal do Azure e como implementá-lo.
author: mumian
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: dd3d9caa8184b8637b509fc3318851751b211405
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80131878"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Quickstart: Criar e implementar modelos ARM utilizando o portal Azure

Aprenda a gerar um modelo de Gestor de Recursos Azure (ARM) utilizando o portal Azure, e o processo de edição e implementação do modelo a partir do portal. Os modelos ARM são ficheiros JSON que definem os recursos necessários para implementar para a sua solução. Para compreender os conceitos associados à implementação e gestão das suas soluções Azure, consulte a visão geral da [implementação](overview.md)do modelo .

![Modelo de gestor de recursos diagrama de portal quickstart](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Depois de concluir o tutorial, implementa uma conta de Armazenamento Azure. O mesmo processo pode ser utilizado para implantar outros recursos Azure.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="generate-a-template-using-the-portal"></a>Gerar um modelo com o portal

Criar um modelo ARM do zero não é uma tarefa fácil, especialmente se for novo na implementação do Azure e não estiver familiarizado com o formato JSON. Utilizando o portal Azure, pode configurar um recurso, por exemplo, uma conta de Armazenamento Azure. Antes de implementar o recurso, pode exportar a sua configuração para um modelo. Pode guardar o modelo e reutilizá-lo no futuro.

Muitos desenvolvedores de modelos experientes usam este método para gerar modelos quando tentam implementar recursos Azure que não estão familiarizados. Para obter mais informações sobre modelos de exportação utilizando o portal, consulte grupos de [recursos de exportação para modelos](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). A outra forma de encontrar um modelo de trabalho é a partir de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/).

1. Num navegador web, vá ao [portal Azure](https://portal.azure.com) e inscreva-se.
1. A partir do menu do portal Azure, selecione **Criar um recurso**.

    ![Selecione Criar um recurso do menu do portal Azure](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Selecione**conta de** **armazenamento** > .

    ![Criar uma conta de armazenamento do Azure](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
1. Introduza as seguintes informações:

    |Nome|Valor|
    |----|----|
    |**Grupo de recursos**|Selecione **Criar novo**e especificar um nome de grupo de recursos à sua escolha. Na captura de ecrã, o nome do grupo de recursos é *mystorage1016rg*. O grupo de recursos é um contentor para os recursos azure. O grupo de recursos facilita a gestão dos recursos do Azure. |
    |**Nome**|Dê à sua conta de armazenamento um nome único. O nome da conta de armazenamento deve ser único em todo o Azure, e contém apenas letras minúsculas e números. O nome deve estar entre 3 e 24 caracteres. Se receber uma mensagem de erro a dizer "O nome da conta de armazenamento 'mystorage1016' já foi tomado", tente usar ** &lt;o seu nome>armazenamento&lt;Data de hoje em>MMDD **, por **exemplo, johndolestorage1016**. Para mais informações, consulte [as regras e restrições de nomeação.](/azure/architecture/best-practices/resource-naming)|

    Pode utilizar os valores predefinidos para as restantes propriedades.

    ![Criar uma configuração de conta de armazenamento do Azure com o portal do Azure](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Alguns dos modelos exportados requerem algumas edições antes de poder implementá-los.

1. Selecione **Rever + criar**, na parte inferior do ecrã. Não selecione **Criar** no próximo passo.
1. Selecione **Transferir um modelo para automatização**, na parte inferior do ecrã. O portal mostra o modelo gerado:

    ![Gerar um modelo a partir do portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    O painel principal mostra o modelo. É um ficheiro JSON com seis elementos de topo - `schema` `contentVersion`, `parameters`, `variables`, , `resources`e `output`. Para mais informações, consulte [Compreender a estrutura e a sintaxe dos modelos ARM](./template-syntax.md)

    Existem seis parâmetros definidos. Um desses parâmetros é denominado **storageAccountName**. A segunda parte realçada na imagem anterior mostra como referenciar este parâmetro no modelo. Na próxima secção, vai editar o modelo de modo a utilizar um nome gerado para a conta de armazenamento.

    No modelo, é definido um recurso do Azure. O tipo `Microsoft.Storage/storageAccounts`é. Veja como o recurso é definido e a estrutura de definição.
1. Selecione **Baixar** a partir da parte superior do ecrã.
1. Abra o ficheiro zip descarregado e, em seguida, guarde o **modelo.json** para o seu computador. Na próxima secção, vai utilizar uma ferramenta de implementação de modelos para editar o modelo.
1. Selecione o separador **Parameter** (Parâmetro) para ver os valores que indicou para os parâmetros. Aponte-os, pois vai precisar dos mesmos na próxima secção, quando implementar o modelo.

    ![Gerar um modelo a partir do portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Utilizando tanto o ficheiro do modelo como o ficheiro de parâmetros, pode criar um recurso, neste tutorial, uma conta de armazenamento Azure.

## <a name="edit-and-deploy-the-template"></a>Editar e implementar o modelo

O portal do Azure pode ser utilizado para fazer algumas edições básicas aos modelos. Neste início rápido, vai utilizar uma ferramenta do portal, denominada *Template Deployment*. *A implantação* do modelo é usada neste tutorial para que possa completar todo o tutorial usando uma interface - o portal Azure. Para editar um modelo mais complexo, considere usar o [Código do Estúdio Visual,](quickstart-create-templates-use-visual-studio-code.md)que fornece funcionalidades de edição mais ricas.

> [!IMPORTANT]
> A implementação do modelo fornece uma interface para testar modelos simples. Não é aconselhável utilizar esta funcionalidade na produção. Em vez disso, guarde os seus modelos numa conta de armazenamento Azure, ou num repositório de código fonte como o GitHub.

O Azure exige que cada serviço do Azure tenha um nome exclusivo. A implementação pode falhar se inseriu um nome de conta de armazenamento que já existe. Para evitar este problema, modifica o modelo `uniquestring()` para utilizar uma chamada de função de modelo para gerar um nome de conta de armazenamento único.

1. A partir do menu do portal Azure ou da página **Inicial,** selecione **Criar um recurso**.
1. Em **Pesquisar no Marketplace**, escreva **implementação de modelo** e prima **ENTER**.
1. Selecione **Implementação de modelo**.

    ![Biblioteca de modelos do Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
1. Selecione **Criar**.
1. Selecione **Crie o seu próprio modelo no editor**.
1. Selecione **Carregar ficheiro** e siga as instruções para carregar template.json, que transferiu na última secção.
1. Faça as seguintes três alterações no modelo:

    ![Modelos do Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Remova o parâmetro de nome da conta de **armazenamento,** como mostrado na imagem anterior.
   - Adicione uma variável chamada **storageAccountName** como mostrado na imagem anterior:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       São utilizadas duas funções de modelo aqui: `concat()` e `uniqueString()`.
   - Atualize o elemento name do recurso **Microsoft.Storage/storageAccounts** para utilizar a variável recém-definida em vez do parâmetro:

       ```json
       "name": "[variables('storageAccountName')]",
       ```

     O modelo final deve ter o seguinte aspeto:

     ```json
     {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "location": {
               "type": "string"
           },
           "accountType": {
               "type": "string"
           },
           "kind": {
               "type": "string"
           },
           "accessTier": {
               "type": "string"
           },
           "supportsHttpsTrafficOnly": {
               "type": "bool"
           }
       },
       "variables": {
           "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       },
       "resources": [
           {
               "name": "[variables('storageAccountName')]",
               "type": "Microsoft.Storage/storageAccounts",
               "apiVersion": "2018-07-01",
               "location": "[parameters('location')]",
               "properties": {
                   "accessTier": "[parameters('accessTier')]",
                   "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
               },
               "dependsOn": [],
               "sku": {
                   "name": "[parameters('accountType')]"
               },
               "kind": "[parameters('kind')]"
           }
       ],
       "outputs": {}
     }
     ```
1. Selecione **Guardar**.
1. Introduza os seguintes valores:

    |Nome|Valor|
    |----|----|
    |**Grupo de recursos**|Selecione o nome do grupo de recursos que criou na última secção. |
    |**Localização**|Selecione um local para a conta de armazenamento. Por exemplo, **Centro dos EUA.** |
    |**Tipo de conta**|Insira **Standard_LRS** para este arranque rápido. |
    |**Tipo**|Introduza **o StorageV2** para este arranque rápido. |
    |**Nível de Acesso**|Introduza **Hot** para este arranque rápido. |
    |**Tráfego https apenas ativado**| Selecione **verdadeiro** para este início rápido. |
    |**Concordo com os termos e condições acima referidos**|(selecionar)|

    Segue-se uma captura de ecrã de uma implementação de exemplo:

    ![Implementação de modelos do Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

1. Selecione **Comprar**.
1. Selecione o ícone de campainha (notificações) na parte superior do ecrã para ver o estado de implementação. Verá a **implantação em curso.** Aguarde até que a implementação esteja concluída.

    ![Notificação de implementação de modelos do Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. Selecione **Ir para o grupo de recursos** no painel de notificação. Deverá ver um ecrã semelhante a:

    ![Grupo de recursos de implementação de modelos do Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Pode ver que o estado de implementação foi bem-sucedido e que não existe apenas uma conta de armazenamento no grupo de recursos. O nome da conta de armazenamento é uma cadeia de carateres exclusiva gerada pelo modelo. Para saber mais sobre como utilizar contas de armazenamento do Azure, veja [Início Rápido: Carregar, transferir e listar blobs através do portal do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
1. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
1. Selecione o nome do grupo de recursos.  Deverá ver a conta de armazenamento no grupo de recursos.
1. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a gerar um modelo a partir do portal do Azure e a implementar o modelo através do portal. O modelo utilizado neste Início Rápido é um modelo simples com um recurso do Azure. Quando o modelo é complexo, é mais fácil utilizar o Visual Studio Code ou o Visual Studio para desenvolver o modelo. Para saber mais sobre o desenvolvimento do modelo, consulte a nossa nova série tutorial de principiante:

> [!div class="nextstepaction"]
> [Tutoriais para principiantes](./template-tutorial-create-first-template.md)
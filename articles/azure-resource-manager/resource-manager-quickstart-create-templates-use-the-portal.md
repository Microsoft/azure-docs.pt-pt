---
title: Criar e implementar um modelo do Azure Resource Manager com o portal do Azure | Microsoft Docs
description: Saiba como criar o seu primeiro modelo do Azure Resource Manager com o portal do Azure e como implementá-lo.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 84025953e74cb2ace358aa041f55dc1498d22f2f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079065"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Início rápido: Criar e implementar modelos Azure Resource Manager com o portal do Azure

Saiba como gerar um modelo do Resource Manager com o portal do Azure e o processo de edição e a implementar o modelo a partir do portal. Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução. Para compreender os conceitos associados à implementação e gestão das suas soluções do Azure, veja [Descrição geral do Azure Resource Manager](resource-group-overview.md).

![Diagrama de início rápido de modelo manager do recurso portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Depois de concluir o tutorial, vai implementar uma conta de armazenamento do Azure. O mesmo processo pode ser utilizado para implementar a outros recursos do Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="generate-a-template-using-the-portal"></a>Gerar um modelo com o portal

Criar um modelo do Resource Manager a partir do zero não é uma tarefa fácil, especialmente se estiver familiarizado com a implementação do Azure e está a não estão familiarizada com o formato JSON. Utilizar o portal do Azure, pode configurar um recurso, por exemplo uma conta de armazenamento do Azure. Antes de implementar o recurso, pode exportar a configuração num modelo do Resource Manager. Pode guardar o modelo e reutilizá-lo no futuro.

Muitos desenvolvedores experientes modelo utilizam este método para gerar modelos ao tentar implementar recursos do Azure que não estão familiarizados. Para obter mais informações sobre a exportação de modelos através do portal, consulte [exportar grupos de recursos para modelos](./manage-resource-groups-portal.md#export-resource-groups-to-templates). A outra forma para localizar um modelo de trabalho é partir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento – blob, ficheiro, tabela, fila**.

    ![Criar uma conta de armazenamento do Azure com o portal do Azure](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Introduza as seguintes informações:

    |Name|Value|
    |----|----|
    |**Grupo de recursos**|Selecione **criar novo**e especifique um nome de grupo de recursos da sua preferência. Na captura de ecrã, o nome do grupo de recursos é *mystorage1016rg*. Grupo de recursos é um contentor para recursos do Azure. Grupo de recursos torna mais fácil de gerir recursos do Azure. |
    |**Nome**|Dê um nome exclusivo de sua conta de armazenamento. O nome da conta de armazenamento tem de ser exclusivo em todas as do Azure e conter apenas letras minúsculas e números. Nome deve ter entre 3 e 24 carateres. Se obtiver uma mensagem de erro dizendo "já estiver atribuído o nome da conta de armazenamento 'mystorage1016'", tente usar  **&lt;seu nome > armazenamento&lt;data de hoje no MMDD >**, por exemplo  **johndolestorage1016**. Para obter mais informações, consulte [regras e restrições de nomenclatura](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions).|

    Pode utilizar os valores predefinidos para as restantes propriedades.

    ![Criar uma configuração de conta de armazenamento do Azure com o portal do Azure](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Alguns dos modelos exportados requerem algumas edições antes de poder implementá-los.

4. Selecione **Rever + criar**, na parte inferior do ecrã. Não selecione **criar** no próximo passo.
5. Selecione **Transferir um modelo para automatização**, na parte inferior do ecrã. O portal mostra o modelo gerado:

    ![Gerar um modelo a partir do portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    O painel principal mostra o modelo. É um ficheiro JSON com elementos de nível superior seis - `schema`, `contentVersion`, `parameters`, `variables`, `resources`, e `output`. Para obter mais informações, veja [Compreender a estrutura e a sintaxe de modelos do Azure Resource Manager](./resource-group-authoring-templates.md)

    Existem seis parâmetros definidos. Um desses parâmetros é denominado **storageAccountName**. A segunda parte realçada na captura de ecrã anterior mostra como fazer referência a esse parâmetro no modelo. Na próxima secção, vai editar o modelo de modo a utilizar um nome gerado para a conta de armazenamento.

    No modelo, é definido um recurso do Azure. O tipo é `Microsoft.Storage/storageAccounts`. Efetuar uma análise de como o recurso está definido e a estrutura de definição.
6. Selecione **transferir** da parte superior do ecrã. 
7. Abra o ficheiro zip transferido e, em seguida, guarde **Template** para o seu computador. Na próxima secção, vai utilizar uma ferramenta de implementação de modelos para editar o modelo.
8. Selecione o separador **Parameter** (Parâmetro) para ver os valores que indicou para os parâmetros. Aponte-os, pois vai precisar dos mesmos na próxima secção, quando implementar o modelo.

    ![Gerar um modelo a partir do portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Utilizar o ficheiro de modelo e o ficheiro de parâmetros, pode criar um recurso, neste tutorial, uma conta de armazenamento do Azure.

## <a name="edit-and-deploy-the-template"></a>Editar e implementar o modelo

O portal do Azure pode ser utilizado para fazer algumas edições básicas aos modelos. Neste início rápido, vai utilizar uma ferramenta do portal, denominada *Template Deployment*. *Implementação do modelo* é utilizado neste tutorial, pelo que pode concluir o tutorial completo, utilizando uma interface - portal do Azure. Para editar um modelo mais complexo, considere usar [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), que fornece funcionalidades mais avançadas de edição.

O Azure exige que cada serviço do Azure tenha um nome exclusivo. A implementação poderá falhar se tiver introduzido um nome de conta de armazenamento que já existe. Para evitar este problema, é modificar o modelo a utilizar uma chamada de função de modelo `uniquestring()` para gerar um nome de conta de armazenamento exclusivo.

1. No portal do Azure, selecione **Criar um recurso**.
2. Em **Pesquisar no Marketplace**, escreva **implementação de modelo** e prima **ENTER**.
3. Selecione **Implementação de modelo**.

    ![Biblioteca de modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. Selecione **Criar**.
5. Selecione **Crie o seu próprio modelo no editor**.
6. Selecione **Carregar ficheiro** e siga as instruções para carregar template.json, que transferiu na última secção.
7. Faça as alterações de três seguintes ao modelo:

    ![Modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Remover os **storageAccountName** parâmetro conforme mostrado na captura de ecrã anterior.
   - Adicionar uma variável chamada **storageAccountName** conforme mostrado na captura de ecrã anterior:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       Duas funções de modelo são utilizadas aqui: `concat()` e `uniqueString()`.
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
8. Selecione **Guardar**.
9. Introduza os seguintes valores:

    |Name|Value|
    |----|----|
    |**Grupo de recursos**|Selecione o nome do grupo de recursos que criou na última secção. |
    |**Localização**|Selecione uma localização para a conta de armazenamento. Por exemplo, **E.U.A. Central**. |
    |**Tipo de conta**|Introduza **Standard_LRS** para este início rápido. |
    |**Kind**|Introduza **StorageV2** para este início rápido. |
    |**Camada de acesso**|Introduza **frequente** para este início rápido. |
    |**Tráfego HTTPS apenas ativada**| Selecione **verdadeiro** para este início rápido. |
    |**Eu concordo com os termos e condições acima apresentados**|(selecionar)|

    Segue-se uma captura de ecrã de uma implementação de exemplo:

    ![Implementação de modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Selecione **Comprar**.
11. Selecione o ícone de campainha (notificações) na parte superior do ecrã para ver o estado de implementação. Deverá ver **implementação em curso**. Aguarde até que a implementação esteja concluída.

    ![Notificação de implementação de modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. Selecione **Ir para o grupo de recursos** no painel de notificação. Deverá ver um ecrã semelhante a:

    ![Grupo de recursos de implementação de modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Pode ver que o estado de implementação foi bem-sucedido e que não existe apenas uma conta de armazenamento no grupo de recursos. O nome da conta de armazenamento é uma cadeia de carateres exclusiva gerada pelo modelo. Para saber mais sobre como utilizar contas de armazenamento do Azure, veja [início rápido: Carregar, transferir e listar blobs através do portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Deverá ver a conta de armazenamento no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a gerar um modelo a partir do portal do Azure e a implementar o modelo através do portal. O modelo utilizado neste Início Rápido é um modelo simples com um recurso do Azure. Quando o modelo é complexo, é mais fácil utilizar o Visual Studio Code ou o Visual Studio para desenvolver o modelo. O próximo início rápido também lhe mostra como implementar modelos com o Azure PowerShell e a CLI (Interface de Linha de Comandos) do Azure.

> [!div class="nextstepaction"]
> [Criar modelos com o Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)

---
title: Modelo de implementação - Portal Azure
description: Saiba como criar o seu primeiro modelo de Gestor de Recursos Azure (modelo ARM) utilizando o portal Azure e como implementá-lo.
author: mumian
ms.date: 03/09/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 20b1bf47ae2fd63e91a11c8cccd1f03cf3464899
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548176"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Quickstart: Criar e implementar modelos ARM utilizando o portal Azure

Aprenda a gerar um modelo de Gestor de Recursos Azure (modelo ARM) utilizando o portal Azure e o processo de edição e implementação do modelo a partir do portal. Os modelos ARM são ficheiros JSON que definem os recursos necessários para a sua solução. Para compreender os conceitos associados à implementação e gestão das suas soluções Azure, consulte a [visão geral da implementação do modelo.](overview.md)

![Diagrama do portal quickstart do gestor de recursos](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Depois de completar o tutorial, você implanta uma conta de Armazenamento Azure. O mesmo processo pode ser usado para implantar outros recursos Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="generate-a-template-using-the-portal"></a>Gerar um modelo com o portal

Criar um modelo ARM de raiz não é uma tarefa fácil, especialmente se for novo na versão Azure e não estiver familiarizado com o formato JSON. Utilizando o portal Azure, pode configurar um recurso, por exemplo, uma conta de Armazenamento Azure. Antes de implementar o recurso, pode exportar a sua configuração para um modelo. Pode guardar o modelo e reutilizá-lo no futuro.

Muitos desenvolvedores de modelos experientes usam este método para gerar modelos quando tentam implementar recursos Azure que eles não estão familiarizados. Para obter mais informações sobre modelos de exportação utilizando o portal, consulte [os grupos de recursos de exportação para os modelos](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). A outra maneira de encontrar um modelo de trabalho é a partir de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/).

1. Num navegador web, vá ao [portal Azure](https://portal.azure.com) e inscreva-se.
1. No menu do portal do Azure, selecione **Criar um recurso**.

    ![Selecione Criar um recurso a partir do menu do portal Azure](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Na caixa de pesquisa, escreva **a conta de armazenamento** e, em seguida, prima **[ENTER]**.
1. Selecione a seta para baixo ao lado **de Criar** e, em seguida, selecione a conta **de Armazenamento**.

    ![Criar uma conta de armazenamento do Azure](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)

1. Introduza as seguintes informações:

    |Name|Valor|
    |----|----|
    |**Grupo de recursos**|Selecione **Criar novo**, e especifique um nome de grupo de recursos à sua escolha. Na captura de ecrã, o nome do grupo de recursos é *mystorage1016rg*. O grupo de recursos é um contentor para os recursos da Azure. O grupo de recursos facilita a gestão dos recursos da Azure. |
    |**Nome**|Dê à sua conta de armazenamento um nome único. O nome da conta de armazenamento deve ser único em todo o Azure, e contém apenas letras minúsculas e números. O nome deve estar entre 3 e 24 caracteres. Se receber uma mensagem de erro a dizer "O nome da conta de armazenamento 'mystorage1016' já está tomado", tente usar **&lt; o seu nome>armazenamento A data de &lt; hoje em MMDD>**, por **exemplo, johndolestorage1016**. Para obter mais informações, consulte [as regras e restrições de nomeação.](/azure/architecture/best-practices/resource-naming)|

    Pode utilizar os valores predefinidos para as restantes propriedades.

    ![Criar uma configuração de conta de armazenamento do Azure com o portal do Azure](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Alguns dos modelos exportados requerem algumas edições antes de poder implementá-los.

1. Selecione **Rever + criar**, na parte inferior do ecrã. Não **selecione Criar** no passo seguinte.
1. Selecione **Transferir um modelo para automatização**, na parte inferior do ecrã. O portal mostra o modelo gerado:

    ![Gerar um modelo a partir do portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    O painel principal mostra o modelo. É um ficheiro JSON com seis elementos de alto nível - `schema` , , , , , e `contentVersion` `parameters` `variables` `resources` `output` . Para mais informações, consulte [Compreender a estrutura e sintaxe dos modelos ARM](./template-syntax.md)

    Há nove parâmetros definidos. Um desses parâmetros é denominado **storageAccountName**. A segunda parte realçada na imagem anterior mostra como fazer referência a este parâmetro no modelo. Na próxima secção, vai editar o modelo de modo a utilizar um nome gerado para a conta de armazenamento.

    No modelo, é definido um recurso do Azure. O tipo `Microsoft.Storage/storageAccounts` é. Veja como o recurso é definido e a estrutura de definição.
1. Selecione **Baixar** a partir da parte superior do ecrã.
1. Abra o ficheiro zip descarregado e guarde **template.jsno** seu computador. Na próxima secção, vai utilizar uma ferramenta de implementação de modelos para editar o modelo.
1. Selecione o separador **Parameter** (Parâmetro) para ver os valores que indicou para os parâmetros. Aponte-os, pois vai precisar dos mesmos na próxima secção, quando implementar o modelo.

    ![Screenshot que realça o separador Parâmetro que mostra os valores que forneceu.](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Utilizando tanto o ficheiro de modelo como o ficheiro de parâmetros, pode criar um recurso, neste tutorial, de uma conta de armazenamento Azure.

## <a name="edit-and-deploy-the-template"></a>Editar e implementar o modelo

O portal do Azure pode ser utilizado para fazer algumas edições básicas aos modelos. Neste início rápido, vai utilizar uma ferramenta do portal, denominada *Template Deployment*. *A Implementação do Modelo* é utilizada neste tutorial para que possa completar todo o tutorial usando uma interface - o portal Azure. Para editar um modelo mais complexo, considere usar [o Código do Estúdio Visual,](quickstart-create-templates-use-visual-studio-code.md)que fornece funcionalidades de edição mais ricas.

> [!IMPORTANT]
> A implementação do modelo fornece uma interface para testar modelos simples. Não é aconselhável utilizar esta função na produção. Em vez disso, guarde os seus modelos numa conta de armazenamento Azure, ou num repositório de código fonte como o GitHub.

O Azure exige que cada serviço do Azure tenha um nome exclusivo. A implementação pode falhar se introduzir um nome de conta de armazenamento que já existe. Para evitar este problema, modifica o modelo para usar uma chamada de função de modelo `uniquestring()` para gerar um nome de conta de armazenamento único.

1. A partir do menu do portal Azure, na caixa de pesquisa, **escreva** e, em seguida, **selecione Implementar um modelo personalizado**.

    ![Biblioteca de modelos do Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)

1. Selecione **Construa o seu próprio modelo no editor.**
1. Selecione **Carregar ficheiro** e siga as instruções para carregar template.json, que transferiu na última secção.
1. Faça as seguintes três alterações ao modelo:

    ![Modelos do Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Remover o **parâmetro de armazenamentoAconselho de nome** como mostrado na imagem anterior.
   - Adicione uma variável chamada **storageAccountName** como mostrado na imagem anterior:

      ```json
      "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
      ```

      Duas funções de modelo são usadas aqui: `concat()` e `uniqueString()` .
   - Atualize o elemento name do recurso **Microsoft.Storage/storageAccounts** para utilizar a variável recém-definida em vez do parâmetro:

      ```json
      "name": "[variables('storageAccountName')]",
      ```

      O modelo final deve ter o seguinte aspeto:

      ```json
      {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
          "minimumTlsVersion": {
            "type": "string"
          },
          "supportsHttpsTrafficOnly": {
            "type": "bool"
          },
          "allowBlobPublicAccess": {
            "type": "bool"
          },
          "allowSharedKeyAccess": {
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
            "apiVersion": "2019-06-01",
            "location": "[parameters('location')]",
            "properties": {
              "accessTier": "[parameters('accessTier')]",
              "minimumTlsVersion": "[parameters('minimumTlsVersion')]",
              "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
              "allowBlobPublicAccess": "[parameters('allowBlobPublicAccess')]",
              "allowSharedKeyAccess": "[parameters('allowSharedKeyAccess')]"
            },
            "dependsOn": [],
            "sku": {
              "name": "[parameters('accountType')]"
            },
            "kind": "[parameters('kind')]",
            "tags": {}
          }
        ],
        "outputs": {}
      }
      ```

1. Selecione **Guardar**.
1. Introduza os seguintes valores:

    |Name|Valor|
    |----|----|
    |**Grupo de recursos**|Selecione o nome do grupo de recursos que criou na última secção. |
    |**Região**|Selecione uma localização para o grupo de recursos. Por exemplo, **E.U.A. Central**. |
    |**Localização**|Selecione um local para a conta de armazenamento. Por exemplo, **E.U.A. Central**. |
    |**Tipo de Conta**|Introduza **Standard_LRS** para este arranque rápido. |
    |**Tipo**|Introduza **o StorageV2** para este arranque rápido. |
    |**Nível de acesso**|Entre **em brasa** para este arranque rápido. |
    |**Versão mínima TLS**|Introduza **TLS1_0.** |
    |**Suporta apenas o tráfego de https**| Selecione **verdadeiro** para este início rápido. |
    |**Permitir o acesso público blob**| Selecione **falso** para este início rápido. |
    |**Permitir acesso de chave partilhada**| Selecione **verdadeiro** para este início rápido. |

1. Selecione **Rever + criar**.
1. Selecione **Criar**.
1. Selecione o ícone de campainha (notificações) na parte superior do ecrã para ver o estado de implementação. Verá a **implantação em curso.** Aguarde até que a implementação esteja concluída.

    ![Notificação de implementação de modelos do Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. Selecione **Ir para o grupo de recursos** no painel de notificação. Deverá ver um ecrã semelhante a:

    ![Grupo de recursos de implementação de modelos do Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Pode ver que o estado de implementação foi bem-sucedido e que não existe apenas uma conta de armazenamento no grupo de recursos. O nome da conta de armazenamento é uma cadeia de carateres exclusiva gerada pelo modelo. Para saber mais sobre como utilizar contas de armazenamento do Azure, veja [Início Rápido: Carregar, transferir e listar blobs através do portal do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
1. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
1. Selecione o nome do grupo de recursos.  Deverá ver a conta de armazenamento no grupo de recursos.
1. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a gerar um modelo a partir do portal do Azure e a implementar o modelo através do portal. O modelo utilizado neste Início Rápido é um modelo simples com um recurso do Azure. Quando o modelo é complexo, é mais fácil utilizar o Visual Studio Code ou o Visual Studio para desenvolver o modelo. Para saber mais sobre o desenvolvimento do modelo, consulte a nossa nova série tutorial de principiantes:

> [!div class="nextstepaction"]
> [Tutoriais para principiantes](./template-tutorial-create-first-template.md)

---
title: Descubra propriedades de recursos
description: Descreve como procurar propriedades de recursos.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91327753"
---
# <a name="discover-resource-properties"></a>Descubra propriedades de recursos

Antes de criar modelos de Gestor de Recursos, precisa entender que tipos de recursos estão disponíveis e que valores usar no seu modelo. Este artigo mostra algumas formas de encontrar as propriedades para incluir no seu modelo.

## <a name="find-resource-provider-namespaces"></a>Encontre espaços de nome do fornecedor de recursos

Os recursos num modelo ARM são definidos com um espaço de nome e tipo de recurso do fornecedor de recursos. Por exemplo, Microsoft.Storage/storageAcounts é o nome completo do tipo de recurso da conta de armazenamento. Microsoft.Storage é o espaço de nome. Se ainda não conhece os espaços de nome para os tipos de recursos que pretende utilizar, consulte [os fornecedores de recursos para os serviços Azure.](../management/azure-services-resource-providers.md)

![Mapeamento do espaço de nome do fornecedor de recursos do gestor de recursos de recursos](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>Exportar modelos

A maneira mais fácil de obter as propriedades do modelo para os seus recursos existentes é exportar o modelo. Para obter mais informações, consulte [exportação única e multi-recursos para um modelo no portal Azure](./export-template-portal.md).

## <a name="use-resource-manager-tools-extension"></a>Use extensão de ferramentas do Gestor de Recursos

O Código do Estúdio Visual e a extensão de ferramentas do Azure Resource Manager ajudam-no a ver exatamente quais as propriedades necessárias para cada tipo de recurso. Eles fornecem intellisense e snippets que simplificam a forma como define um recurso no seu modelo. Para obter mais informações, consulte [Quickstart: Crie modelos de Gestor de Recursos Azure com Código de Estúdio Visual](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource).

A imagem que se segue mostra que um recurso de conta de armazenamento é adicionado a um modelo:

![Corte de extensão de ferramentas do Gestor de Recursos](./media/view-resources/resource-manager-tools-extension-snippets.png)

A extensão também fornece uma lista de opções para as propriedades de configuração.

![Valores configuráveis de extensão de ferramentas de gestor de recursos](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>Utilizar a referência do modelo

A referência do modelo Azure Resource Manager é o recurso mais abrangente para o esquema do modelo. Pode encontrar versões API, formato de modelo e informações sobre propriedades.

1. Navegue na referência do [modelo do Azure Resource Manager](/azure/templates/).
1. A partir da navegação à esquerda, selecione **Armazenamento** e, em seguida, selecione **Todos os recursos**. A página de Todos os recursos resume os tipos de recursos e as versões.

    ![versões de recursos de referência de modelo](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    Se souber o tipo de recurso, pode ir diretamente a esta página com o seguinte formato URL: `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` .

1. Selecione a versão mais recente. Recomenda-se a utilização da versão API mais recente.

    A secção **de formato modelo** lista todas as propriedades para a conta de armazenamento. **sku** está na lista:

    ![formato de conta de armazenamento de referência de modelo](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    Desloque-se para baixo para ver o **objeto Sku** na secção **valores de propriedade.** O artigo mostra os valores permitidos para o nome SKU:

    ![conta de armazenamento de referência de modelo SKU valores](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    No final da página, a secção de **modelos Quickstart lista alguns modelos** Azure Quickstart que contêm o tipo de recurso:

    ![modelos de quickstart de conta de referência de modelos](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

A referência do modelo está ligada a partir de cada um dos sites de documentação de serviço Azure.  Por exemplo, o site de [documentação do Cofre de Chaves:](../../key-vault/general/overview.md)

![Cofre de referência do modelo do gestor de recursos](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>Use Explorador de Recursos

O Resource Explorer está incorporado no portal Azure. Antes de utilizar este método, precisa de uma conta de armazenamento. Se não tiver um, selecione o seguinte botão para criar um:

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na caixa de pesquisa, insira o **explorador de recursos** e, em seguida, selecione Resource **Explorer**.

    ![A screenshot mostra a procura do Explorador de Recursos no portal Azure.](./media/view-resources/azure-portal-resource-explorer.png)

1. A partir da esquerda, expanda **as Subscrições** e, em seguida, expanda a sua subscrição Azure. Pode encontrar a conta de armazenamento em **fornecedores** ou **Grupos de Recursos.**

    ![Explorador de Recursos do portal Azure](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **Fornecedores**: expandir **os fornecedores**  ->  **Microsoft.StoragesCocounts**  ->  e, em seguida, selecionar a sua conta de armazenamento.
    - **Grupos de Recursos**: selecione o grupo de recursos, que contém a conta de armazenamento, selecione **Recursos** e, em seguida, selecione a conta de armazenamento.

    À direita, vê-se a configuração SKU para a conta de armazenamento existente semelhante a:

    ![Sku de armazenamento de conta de armazenamento do portal Azure](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Use Resources.azure.com

Resources.azure.com é um site público que pode ser acedido por qualquer pessoa com uma assinatura Azure. Está na pré-estreia.  Considere usar [o Resource Explorer](#use-resource-explorer) em vez disso. Esta ferramenta fornece estas funcionalidades:

- Descubra as APIs de Gestão de Recursos Azure.
- Obtenha documentação da API e informações sobre esquemas.
- Faça chamadas API diretamente nas suas próprias subscrições.

Para demonstrar como recuperar informações de esquema utilizando esta ferramenta, precisa de uma conta de armazenamento. Se não tiver um, selecione o seguinte botão para criar um:

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Navegue para [resources.azure.com](https://resources.azure.com/). Leva alguns momentos para a ferramenta popularizar o painel esquerdo.
1. Selecione **subscrições**.

    ![resource.azure.com mapeamento api](./media/view-resources/resources-azure-com-api-mapping.png)

    O nó à esquerda coincide com a chamada da API à direita. Pode esboar a chamada da API selecionando o botão **GET.**
1. A partir da esquerda, expanda **as Subscrições** e, em seguida, expanda a sua subscrição Azure. Pode encontrar a conta de armazenamento em **fornecedores** ou **Grupos de Recursos.**

    - **Fornecedores**: expandir **os fornecedores**  ->  **Microsoft.StoragesCocounts**  ->  e, em seguida, navegar na conta de armazenamento.
    - **Grupos de Recursos**: selecione o grupo de recursos, que contém a conta de armazenamento e, em seguida, selecione **Recursos**.

    À direita, vê-se a configuração do sku para a conta de armazenamento existente semelhante a:

    ![Sku de armazenamento de conta de armazenamento do portal Azure](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a encontrar informações de esquema de modelo. Para saber mais sobre a criação de modelos de Gestor de Recursos, consulte [compreender a estrutura e a sintaxe dos modelos ARM](./template-syntax.md).

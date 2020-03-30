---
title: Configure SKUs para uma oferta de candidatura Azure [ Mercado Azure
description: Como configurar as SKUs para uma aplicação gerida pelo Azure e um modelo de solução Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 043394a1303456ce5b209bb84b5afaf09f6beba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289078"
---
# <a name="azure-application-skus-tab"></a>Tab SKUs aplicação Azure

Este artigo descreve como usar o separador SKUs para criar SKUs para a sua aplicação Azure. 

> [!IMPORTANT]
> Os passos para configurar um SKU são diferentes para uma oferta de aplicação gerida e uma oferta de modelo de Solução. Estas diferenças estão documentadas neste artigo. 

## <a name="configure-azure-application-skus"></a>Configure aplicação Azure SKUs

### <a name="create-a-new-sku"></a>Criar um novo SKU

Use estes passos para criar um novo SKU:

1. Selecione o separador **SKUs.**
2. Em SKUs, selecione **+ New SKU**.

    ![Novo pedido sku](./media/azureapp-plus-sku.png)

3. Na janela popup New SKU, escreva um **ID SKU**. Este id é limitado a 50 caracteres e deve consistir apenas em caracteres minúsculos, alfanuméricos, traços ou sublinhados. O SKU ID não pode terminar num traço.
4. O ID SKU é visível para os clientes em URLs de produto, modelos de Gestor de Recursos (se aplicável) e relatórios de faturação. Não pode modificar esta identidade depois da oferta ser publicada.

### <a name="sku-details-for-a-solution-template"></a>Detalhes do SKU para um modelo de solução

A próxima captura do ecrã mostra o formulário De detalhes SKU para um modelo de solução.

![Formulário de detalhes sKU para modelo de solução](./media/azureapp-sku-details-solutiontemplate.png)

Forneça os seguintes valores SKU.  Os campos anexados com um asterisco são necessários.

|    Campo         |       Descrição                                                            |
|  ---------       |     ---------------                                                          |
|  **Título\***     | Um título para o SKU. Este título está exposto na galeria para este artigo.   |
| **Resumo\***    | Uma breve descrição sumária do SKU. (Comprimento máximo é de 100 caracteres.)  |
| **Descrição\*** | Uma descrição detalhada do SKU. O HTML básico é suportado.                 | 
| **Tipo SKU\***   | Tipo de solução de aplicação Azure, selecione * Modelo de**solução** para este cenário. |
| **Disponibilidade de Nuvem\*** | A localização do SKU. O padrão é **Public Azure.**  <b/>   **Public Azure** - App será implantável para clientes em todas as regiões públicas do Azure que tenham integração no mercado.  <b/>   **Azure Government Cloud** - App será implantado na Nuvem do Governo Azure. Antes de publicar ao [Governo do Azure,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)a Microsoft recomenda aos editores que testem e validem os seus trabalhos de solução como esperado neste ambiente. Para encenar e testar, solicite uma [conta de teste.](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)  |
| **Isto é um SKU privado?\*** | Selecione **Sim** se este SKU estiver disponível apenas para um grupo selecionado de clientes. |
|   |   |

  > [!NOTE] 
  > O Governo microsoft Azure é uma nuvem governamental-comunitária com acesso controlado para clientes dos EUA Federal, State, local ou tribal e parceiros elegíveis para servir estas entidades.


### <a name="sku-details-for-managed-application"></a>Detalhes sKU para aplicação gerida

A próxima captura do ecrã mostra o formulário SKU Details para uma Aplicação Gerida.

   ![Formulário de detalhes sKU para aplicação gerida](./media/azureapp-sku-details-managedapplication.png)

Configure as seguintes definições de SKU. Os campos anexados com um asterisco são necessários.

|    Campo         |       Descrição                                                            |
|  ---------       |     ---------------                                                          |
|  **Título\***     | Um título para o SKU. Este título está exposto na galeria para este artigo.   |
| **Resumo\***    | Uma breve descrição sumária do SKU. (Comprimento máximo é de 100 caracteres.)  |
| **Descrição\*** | Uma descrição detalhada do SKU. O HTML básico é suportado.                 | 
| **Tipo SKU\***   | Tipo de solução de aplicação Azure, selecione ***Aplicação gerida** para este cenário. 
| **Disponibilidade de Nuvem\*** | A localização do SKU. O padrão é **Public Azure.**  <b/>   **Public Azure** - App será implantável para clientes em todas as regiões públicas do Azure que tenham integração no mercado.  <b/>   **Azure Government Cloud** - App será implantado na Nuvem do Governo Azure. Antes de publicar ao [Governo do Azure,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)a Microsoft recomenda aos editores que testem e validem os seus trabalhos de solução como esperado neste ambiente. Para encenar e testar, solicite uma [conta de teste.](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)   O Governo microsoft Azure é uma nuvem governamental-comunitária com acesso controlado para clientes dos EUA Federal, State, local ou tribal e parceiros elegíveis para servir estas entidades. |
| **Isto é um SKU privado?\*** | Selecione **Sim** se este SKU estiver disponível apenas para um grupo selecionado de clientes. |
| **Disponibilidade país/região\*** | Utilize **regiões selecionadas** para ver a lista de países/regiões disponíveis. Verifique cada país/região e, em seguida, selecione **OK** para guardar as suas escolhas.  <b/>   ![Lista de disponibilidade de país e região](./media/azure-app-select-country-region.png)  |
| **Preços Antigos\*** | O preço do SKU, em USD por mês. Os preços são definidos em moeda local utilizando as taxas de câmbio atuais após a configuração. Valide-as uma vez que, em última análise, possui estas definições. Para definir ou visualizar individualmente o preço de cada país/região, por favor exporte a folha de cálculo de preços e importe com preços personalizados.  Deve guardar as alterações de preços para permitir a exportação/importação de dados de preços.  |
| **Preços simplificados da moeda\*** | O preço do SKU, em USD por mês. Isto deve ser o mesmo que o Preço Antigo. Para mais informações, consulte [preços simplificados da moeda](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Detalhes do pacote para o modelo de solução

![Detalhes do pacote para o modelo de solução](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Forneça os seguintes valores de Detalhes do **Pacote.**  Os campos anexados com um asterisco são necessários.

- **Versão\* ** - A versão do pacote que irá carregar. As etiquetas de versão devem ser do formulário X.Y.Z, onde X, Y e Z são inteiros.
- **Ficheiro de pacote\* (.zip)** - Este pacote contém os seguintes ficheiros, guardados num ficheiro .zip.
  - **mainTemplate.json\* ** - O ficheiro de modelo de implementação que é usado para implementar a solução/aplicação e criar os recursos definidos para a solução. Para mais informações, consulte [Como autor de ficheiros](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)de modelos de implementação .
  - **createUIDefinition.json\* ** - Este ficheiro é utilizado pelo portal Azure para gerar a interface do utilizador para o fornecimento desta solução/aplicação. Para mais informações, consulte a interface de utilizador do [portal Create Azure para a sua aplicação gerida.](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)
  - Scripts (se necessário) - Quaisquer scripts adicionais que possam ser `Microsoft.Compute/virtualMachines/extensions`necessários ao executar o modelo, por exemplo, .
  - Modelos aninhados (se necessário) - Quaisquer modelos aninhados adicionais.

  > [!IMPORTANT] 
  > Este pacote deve conter quaisquer modelos ou scripts aninhados que sejam necessários para fornecer esta aplicação. O ficheiro principalTemplate.json e o ficheiro createUIDefinition.json devem estar na pasta raiz. Para mais informações sobre artefactos de implantação, consulte modelos de [gestor de recursos do Azure - Guia](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts)de Boas Práticas .


### <a name="package-details-for-managed-application"></a>Detalhes do pacote para aplicação gerida

   ![Detalhes do pacote para aplicação gerida](./media/azureapp-sku-pkgdetails-managedapplication.png)

Forneça os seguintes Detalhes do Pacote.  Os campos anexados com um asterisco são necessários.

- **Versão\* ** - A versão do pacote que irá carregar. As etiquetas de versão devem ser do formulário X.Y.Z, onde X, Y e Z são inteiros.
- **Ficheiro de pacote\* (.zip)** - Este pacote contém os seguintes ficheiros, guardados num ficheiro .zip.
  - applianceMainTemplate.json - O ficheiro de modelo de implementação que é utilizado para implementar a solução/aplicação e criar os recursos definidos. Para mais informações, consulte [Quickstart: Crie e implemente modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)do Gestor de Recursos Azure utilizando o portal Azure . 
  - aparelhoCreateUIDefinition.json - Este ficheiro é utilizado pelo portal Azure para gerar a interface do utilizador para o fornecimento desta solução/aplicação. Para mais informações, consulte a interface de utilizador do [portal Create Azure para a sua aplicação gerida.](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)
  - mainTemplate.json - O ficheiro de modelo que contém apenas o recurso Microsoft.Solution/appliances. Para mais informações, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) <br>
Note as seguintes propriedades-chave deste recurso:
    - "kind" - O valor deve ser "Marketplace" no caso da aplicação gerida pelo Marketplace.
    - "ManagedResourceGroupId" - O grupo de recursos na subscrição do cliente onde serão implantados todos os recursos definidos no aparelhoMainTemplate.json.
    - "PublisherPackageId"- A corda que identifica exclusivamente o pacote. Este valor tem de ser construído da seguinte forma: é uma concatenação de [editorId]. [Offerid]-pré-visualização[SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Este pacote deve conter quaisquer modelos ou scripts aninhados que sejam necessários para fornecer esta aplicação. Estes ficheiros devem estar na pasta raiz: MainTemplate.json, applianceMainTemplate.json e applianceCreateUIDefinition.json.

- **Id\* do Inquilino** - A Identidade de Inquilino Ativo Azure da sua organização.
- Ativar o **Acesso JIT? \* ** - Selecione **Sim** para permitir o acesso à gestão just-in-time para implementações de clientes utilizando esta oferta.

  >[!NOTE] 
  >Se ativar o JIT, tem de atualizar o ficheiro CreateUiDefinition.json para suportar o acesso ao JIT.

Para uma aplicação gerida, deve configurar Definições de Autorização e Política.


#### <a name="authorization"></a>Autorização

Adicione o Identificador de Diretório Ativo Azure do utilizador, grupo ou aplicação ao qual pretende conceder a permissão ao grupo de recursos geridos. A permissão que é concedida é indicada pela definição de papel Id. Pode ser um Proprietário, Colaborador ou qualquer papel personalizado.


#### <a name="policy-settings"></a>Política Definições

Adicione as políticas com as que a App Gerida cumpre. Saiba mais sobre as políticas de Recursos Azure, veja [o que é a Política Azure?](../../../governance/policy/overview.md)

   ![Definições de autorização e política para uma aplicação gerida](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Para criar uma nova autorização:**

1. Sob **Autorização,** **selecione + Nova Autorização**.
2. Para id **principal**, digite o Identificador de Diretório Ativo Azure do utilizador, grupo ou aplicação ao qual pretende conceder a permissão ao grupo de recursos geridos. A permissão concedida é indicada pela Definição de Papel.
3. Para **Definição de Funções,** selecione uma destas opções da lista de abandono: Proprietário ou Colaborador. Para obter mais informações, veja as [Funções incorporadas dos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Várias autorizações podem ser adicionadas. No entanto, recomenda-se criar um grupo de utilizadores de Diretório Ativo e especificar o seu ID no "Principado". Isto permitirá a adição de mais utilizadores ao grupo de utilizadores sem ter de atualizar o SKU.

**Para criar uma nova política:**

1. Em **Definições de Política,** selecione **+ Nova Política**.
2. Para **Nome político,** insira um nome para a apólice. O comprimento máximo do nome é de 50 caracteres.
3. Para **Políticas**, selecione uma das opções da lista de abandono. Escolha a política que o fornecedor de dados quer ser ativado quando a aplicação utiliza os dados. Para mais informações, consulte as [Amostras políticas do Azure.](https://docs.microsoft.com/azure/governance/policy/samples/index)

    ![Definições de política para uma aplicação gerida](./media/azureapp-sku-policy-settings.png)

4. Para **Política SKU**, selecione Free ou Standard como o tipo sKU de política. O SKU Padrão é necessário para políticas de auditoria.


## <a name="next-steps"></a>Passos seguintes

Descreverá ainda mais a sua oferta e fornecerá ativos de marketing no [separador Marketplace](./cpp-marketplace-tab.md). 

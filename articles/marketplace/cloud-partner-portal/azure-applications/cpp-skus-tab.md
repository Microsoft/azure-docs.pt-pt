---
title: Configurar SKUs para uma oferta de aplicativo do Azure | Azure Marketplace
description: Como configurar as SKUs para um aplicativo gerenciado do Azure e um modelo de solução do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 2430d7e6fa74438c148d3cb849510be06243faa0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543159"
---
# <a name="azure-application-skus-tab"></a>Guia SKUs de aplicativo do Azure

Este artigo descreve como usar a guia SKUs para criar SKUs para seu aplicativo do Azure. 

> [!IMPORTANT]
> As etapas para configurar uma SKU são diferentes para uma oferta de aplicativo gerenciado e uma oferta de modelo de solução. Essas diferenças são documentadas neste artigo. 

## <a name="configure-azure-application-skus"></a>Configurar SKUs de aplicativo do Azure

### <a name="create-a-new-sku"></a>Criar uma nova SKU

Use estas etapas para criar uma nova SKU:

1. Selecione a guia **SKUs** .
2. Em SKUs, selecione **+ novo SKU**.

    ![Novo prompt do SKU](./media/azureapp-plus-sku.png)

3. Na janela pop-up nova SKU, digite uma **ID de SKU**. Essa ID é limitada a 50 caracteres e deve consistir apenas em letras minúsculas, caracteres alfanuméricos, traços ou sublinhados. A ID do SKU não pode terminar com um traço.
4. A ID do SKU é visível para clientes em URLs de produtos, modelos do Resource Manager (se aplicável) e relatórios de cobrança. Não é possível modificar essa ID após a publicação da oferta.

### <a name="sku-details-for-a-solution-template"></a>Detalhes de SKU para um modelo de solução

A próxima captura de tela mostra o formulário detalhes de SKU para um modelo de solução.

![Formulário de detalhes do SKU para o modelo de solução](./media/azureapp-sku-details-solutiontemplate.png)

Forneça os valores de SKU a seguir.  Os campos anexados a um asterisco são obrigatórios.

|    Campo         |       Descrição                                                            |
|  ---------       |     ---------------                                                          |
|  **Título\***     | Um título para a SKU. Este título é exibido na galeria deste item.   |
| **Resumo\***    | Uma breve descrição resumida da SKU. (O comprimento máximo é de 100 caracteres.)  |
| **Descrição\*** | Uma descrição detalhada da SKU. Há suporte para HTML básico.                 | 
| **Tipo de SKU\***   | Tipo de solução de aplicativo do Azure, selecione ***modelo de solução** para este cenário. |
| **\* de disponibilidade na nuvem** | O local do SKU. O padrão é **Azure público**.  <b/>**Azure público** -o aplicativo será implantável para clientes em todas as regiões públicas do Azure que têm integração com o Marketplace.  <b/>aplicativo de **nuvem do Azure governamental** será implantado na nuvem do Azure governamental. Antes de publicar no [Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), a Microsoft recomenda que os editores testem e validem sua solução funcione conforme o esperado nesse ambiente. Para preparar e testar, solicite uma [conta de avaliação](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Este é um SKU privado?\*** | Selecione **Sim** se esta SKU estiver disponível apenas para um grupo selecionado de clientes. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Governamental é uma nuvem de comunidade governamental com acesso controlado para clientes dos EUA Federal, estadual, local ou tribal e parceiros qualificados para atender a essas entidades.


### <a name="sku-details-for-managed-application"></a>Detalhes do SKU para aplicativo gerenciado

A próxima captura de tela mostra o formulário detalhes de SKU para um aplicativo gerenciado.

   ![Formulário de detalhes do SKU para aplicativo gerenciado](./media/azureapp-sku-details-managedapplication.png)

Defina as seguintes configurações de SKU. Os campos anexados a um asterisco são obrigatórios.

|    Campo         |       Descrição                                                            |
|  ---------       |     ---------------                                                          |
|  **Título\***     | Um título para a SKU. Este título é exibido na galeria deste item.   |
| **Resumo\***    | Uma breve descrição resumida da SKU. (O comprimento máximo é de 100 caracteres.)  |
| **Descrição\*** | Uma descrição detalhada da SKU. Há suporte para HTML básico.                 | 
| **Tipo de SKU\***   | Tipo de solução de aplicativo do Azure, selecione ***aplicativo gerenciado** para este cenário. 
| **\* de disponibilidade na nuvem** | O local do SKU. O padrão é **Azure público**.  <b/>**Azure público** -o aplicativo será implantável para clientes em todas as regiões públicas do Azure que têm integração com o Marketplace.  <b/>aplicativo de **nuvem do Azure governamental** será implantado na nuvem do Azure governamental. Antes de publicar no [Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), a Microsoft recomenda que os editores testem e validem sua solução funcione conforme o esperado nesse ambiente. Para preparar e testar, solicite uma [conta de avaliação](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Governamental é uma nuvem de comunidade governamental com acesso controlado para clientes dos EUA Federal, estadual, local ou tribal e parceiros qualificados para atender a essas entidades. |
| **Este é um SKU privado?\*** | Selecione **Sim** se esta SKU estiver disponível apenas para um grupo selecionado de clientes. |
| **Disponibilidade de país/região\*** | Use **selecionar regiões** para exibir a lista de países/regiões disponíveis. Verifique cada país/região e, em seguida, selecione **OK** para salvar suas escolhas.  <b/>![lista de disponibilidade de país e região](./media/azure-app-select-country-region.png)  |
| **\* de preços antigos** | O preço da SKU, em USD por mês. Os preços são definidos na moeda local usando as taxas de câmbio atuais na configuração. Valide-os, pois você basicamente tem essas configurações. Para definir ou exibir o preço de cada país/região individualmente, exporte a planilha de preços e importe com preços personalizados.  Você deve salvar as alterações de preços para habilitar a exportação/importação de dados de preços.  |
| **Preços de moedas simplificados\*** | O preço da SKU, em USD por mês. Isso deve ser o mesmo que o preço antigo. Para obter mais informações, consulte [preço de moeda simplificado](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Detalhes do pacote para o modelo de solução

![Detalhes do pacote para o modelo de solução](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Forneça os valores de **detalhes do pacote** a seguir.  Os campos anexados a um asterisco são obrigatórios.

- **Versão\*** -a versão do pacote que será carregada. As marcas de versão devem estar no formato X. Y. Z, em que X, Y e Z são inteiros.
- **Arquivo de pacote (. zip)\*** -este pacote contém os arquivos a seguir, salvos em um arquivo. zip.
  - **MainTemplate. json\*** -o arquivo de modelo de implantação usado para implantar a solução/aplicativo e criar os recursos definidos para a solução. Para obter mais informações, consulte [como criar arquivos de modelo de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - **createUIDefinition. json\*** -esse arquivo é usado pelo portal do Azure para gerar a interface do usuário para provisionar esta solução/aplicativo. Para obter mais informações, consulte [criar portal do Azure interface do usuário para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - Scripts (se necessário)-quaisquer scripts adicionais que possam ser necessários ao executar o modelo, por exemplo, `Microsoft.Compute/virtualMachines/extensions`.
  - Modelos aninhados (se necessário)-quaisquer modelos aninhados adicionais.

  > [!IMPORTANT] 
  > Esse pacote deve conter quaisquer modelos ou scripts aninhados necessários para provisionar esse aplicativo. O arquivo MainTemplate. JSON e o arquivo createUIDefinition. JSON devem estar na pasta raiz. Para obter mais informações sobre os artefatos de implantação, consulte [modelos de Azure Resource Manager – guia de práticas recomendadas](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts).


### <a name="package-details-for-managed-application"></a>Detalhes do pacote para aplicativo gerenciado

   ![Detalhes do pacote para aplicativo gerenciado](./media/azureapp-sku-pkgdetails-managedapplication.png)

Forneça os detalhes do pacote a seguir.  Os campos anexados a um asterisco são obrigatórios.

- **Versão\*** -a versão do pacote que será carregada. As marcas de versão devem estar no formato X. Y. Z, em que X, Y e Z são inteiros.
- **Arquivo de pacote (. zip)\*** -este pacote contém os arquivos a seguir, salvos em um arquivo. zip.
  - applianceMainTemplate. JSON – o arquivo de modelo de implantação que é usado para implantar a solução/aplicativo e criar os recursos que são definidos. Para obter mais informações, consulte [início rápido: criar e implantar modelos de Azure Resource Manager usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition. JSON-esse arquivo é usado pelo portal do Azure para gerar a interface do usuário para provisionar esta solução/aplicativo. Para obter mais informações, consulte [criar portal do Azure interface do usuário para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - MainTemplate. JSON-o arquivo de modelo que contém somente o recurso Microsoft. Solution/appliances. Para obter mais informações, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Observe as seguintes propriedades de chave deste recurso:
    - "Kind" – o valor deve ser "Marketplace" no caso do aplicativo gerenciado pelo Marketplace.
    - "ManagedResourceGroupId" – o grupo de recursos na assinatura do cliente em que todos os recursos definidos no applianceMainTemplate. JSON serão implantados.
    - "PublisherPackageId" – a cadeia de caracteres que identifica exclusivamente o pacote. Esse valor precisa ser construído da seguinte maneira: é uma concatenação de [PublisherID]. [OfferId]-visualização [SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Esse pacote deve conter quaisquer modelos ou scripts aninhados necessários para provisionar esse aplicativo. Esses arquivos devem estar na pasta raiz: MainTemplate. JSON, applianceMainTemplate. JSON e applianceCreateUIDefinition. JSON.

- **ID do locatário\*** -a ID de locatário Azure Active Directory da sua organização.
- **Habilitar acesso JIT?\*** -selecione **Sim** para habilitar o acesso de gerenciamento just-in-time para implantações de clientes usando esta oferta.

  >[!NOTE] 
  >Se você habilitar o JIT, deverá atualizar o arquivo CreateUiDefinition. JSON para dar suporte ao acesso JIT.

Para um aplicativo gerenciado, você deve configurar a autorização e as configurações de política.


#### <a name="authorization"></a>Autorização

Adicione o identificador de Azure Active Directory do usuário, grupo ou aplicativo ao qual você deseja conceder a permissão para o grupo de recursos gerenciado. A permissão concedida é indicada pela ID de definição de função. Pode ser um proprietário, colaborador ou qualquer função personalizada.


#### <a name="policy-settings"></a>Definições de política

Adicione as políticas com as quais o aplicativo gerenciado está em conformidade. Saiba mais sobre as políticas de recursos do Azure, confira [o que é Azure Policy?](../../../governance/policy/overview.md)

   ![Configurações de política e autorização para um aplicativo gerenciado](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Para criar uma nova autorização:**

1. Em **autorização**, selecione **+ nova autorização**.
2. Para **ID da entidade de segurança**, digite o identificador de Azure Active Directory do usuário, grupo ou aplicativo ao qual você deseja conceder a permissão para o grupo de recursos gerenciado. A permissão concedida é indicada pela definição de função.
3. Para **definição de função**, selecione uma destas opções na lista suspensa: proprietário ou colaborador. Para obter mais informações, veja as [Funções incorporadas dos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Várias autorizações podem ser adicionadas. No entanto, é recomendável criar um grupo de usuários Active Directory e especificar sua ID no "PrincipalId". Isso permitirá a adição de mais usuários ao grupo de usuários sem a necessidade de atualizar a SKU.

**Para criar uma nova política:**

1. Em **configurações de política**, selecione **+ nova política**.
2. Para **nome da política**, insira um nome para a política. O comprimento máximo do nome é de 50 caracteres.
3. Para **políticas**, selecione uma das opções na lista suspensa. Escolha a política que o provedor de dados deseja habilitar quando o aplicativo usar os dados. Para obter mais informações, consulte os [exemplos de Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Configurações de política para um aplicativo gerenciado](./media/azureapp-sku-policy-settings.png)

4. Para **SKU de política**, selecione gratuito ou standard como o tipo de SKU de política. O SKU Standard é necessário para as políticas de auditoria.


## <a name="next-steps"></a>Passos seguintes

Você descreverá ainda mais sua oferta e fornecerá ativos de marketing na [guia Marketplace](./cpp-marketplace-tab.md). 

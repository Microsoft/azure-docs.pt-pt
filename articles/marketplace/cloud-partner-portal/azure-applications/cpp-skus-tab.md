---
title: Configurar SKUs para uma oferta de aplicação do Azure | Documentos da Microsoft
description: Como configurar os SKUs de Azure managed application e um modelo de solução do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 7bf7e341a83727e21e9225c384829c05b7230579
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095998"
---
# <a name="azure-application-skus-tab"></a>Separador de SKUs de aplicação do Azure

Este artigo descreve como utilizar o separador de SKUs para criar as SKUs para a sua aplicação do Azure. 

> [!IMPORTANT]
> Os passos para configurar um SKU são diferentes para uma oferta de aplicação gerida e uma oferta de modelo de solução. Essas diferenças são documentadas neste artigo. 

## <a name="configure-azure-application-skus"></a>Configurar os SKUs de aplicação do Azure

### <a name="create-a-new-sku"></a>Criar um novo SKU

Utilize estes passos para criar um novo SKU:

1. Selecione o **SKUs** separador.
2. Em SKUs, selecione **+ novo SKU**.

    ![Nova linha de SKU](./media/azureapp-plus-sku.png)

3. Na janela de pop-up do novo SKU, escreva um **ID de SKU**. Este id está limitado a 50 carateres e deve ser composto apenas carateres em minúsculas, de alfanuméricos, traços ou carateres de sublinhado. O ID de SKU não pode terminar com um traço.
4. O ID do SKU está visível para os clientes em URLs de produto, modelos do Resource Manager (se aplicável) e relatórios de faturação. Não é possível modificar este id após a oferta de publicação do.

### <a name="sku-details-for-a-solution-template"></a>Detalhes SKU de um modelo de solução

Forneça as seguintes definições de SKU:

- **Título** -um título para o SKU. Este título é apresentado na Galeria para este item.
- **Resumo** - uma breve descrição de resumo do SKU. (O comprimento máximo é 100 carateres.)
- **Descrição** - uma descrição do SKU de detalhadas.
- **Tipo de SKU** -uma lista suspensa com estes valores: "Modelo de solução" e "Aplicação gerida". Para este cenário, selecione **modelo de solução**.
- **Disponibilidade da cloud** -a localização do SKU. A predefinição é **público do Azure**.
Azure público - esta máquina virtual será implementável aos clientes em todas as regiões públicas do Azure com integração do Marketplace.
- **O Azure Government Cloud** -esta máquina virtual será implementada na Cloud do Azure Government. Antes de publicação para [do Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), a Microsoft recomenda editores de testar e validar a sua solução funciona conforme esperado no ambiente. Para testar e testar, solicitar um [conta de avaliação](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >O Microsoft Azure Government é uma cloud de Comunidade governamental com acesso controlado para clientes do Federal, estado, locais ou Tribais parceiros AND elegíveis para atender a essas entidades.

- **Este é um SKU privado?** – Selecione Sim se este SKU só está disponível para um grupo de clientes.

    ![Formulário de detalhes SKU para o modelo de solução](./media/azureapp-sku-details-solutiontemplate.png)

### <a name="sku-details-for-managed-application"></a>Detalhes SKU para aplicação gerida

Captura de ecrã seguinte mostra o formulário de detalhes de SKU para uma aplicação gerida.

   ![Formulário de detalhes SKU para aplicação gerida](./media/azureapp-sku-details-managedapplication.png)

Configure as seguintes definições de SKU:

- **Título** -um título para o SKU. Este título é apresentado na Galeria para este item.
- **Resumo** - uma breve descrição de resumo do SKU. (O comprimento máximo é 100 carateres.)
- **Descrição** - uma descrição do SKU de detalhadas.
- **Tipo de SKU** -uma lista suspensa com estes valores: "Modelo de solução" e "Aplicação gerida". Para este cenário, selecione **Managed Application**.
- **Disponibilidade da cloud** -a localização do SKU. A predefinição é **público do Azure**.
- **Azure público** -esta máquina virtual será possível implementar a clientes em todas as regiões públicas do Azure com integração do Marketplace.
- **O Azure Government Cloud** -esta máquina virtual será implementada na Cloud do Azure Government. Antes de publicação para [do Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), a Microsoft recomenda editores de testar e validar a sua solução funciona conforme esperado no ambiente. Para testar e testar, solicitar um [conta de avaliação](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >O Microsoft Azure Government é uma cloud de Comunidade governamental com acesso controlado para clientes do Federal, estado, locais ou Tribais parceiros AND elegíveis para atender a essas entidades.

- **Este é um SKU privado?** – Selecione Sim se este SKU só está disponível para um grupo de clientes.
- **Disponibilidade de país/região** – utilize **selecionar regiões** para ver a lista de países/regiões disponíveis. Verifique cada país/região e, em seguida, selecione **OK** para guardar as suas escolhas. 

   ![Lista de disponibilidade do país e região](./media/azure-app-select-country-region.png)

- **Preço antigo** – introduza o preço para o SKU e, em USD por mês. Os preços são definidos na moeda local utilizando as taxas de câmbio atuais após a configuração. Valide uma vez que é proprietário, por fim, estas definições. Para definir ou ver o preço de cada país/região individualmente, exportar a folha de cálculo do preço e importe com preços personalizados.

  >[!NOTE]
  >Guarde as alterações de preços para ativar a exportação/importação de dados de preços.

- **Simplificado de preços de moeda** -introduza o preço para o SKU e, em USD por mês. Tem de ser igual a preços antigo. Para obter mais informações, consulte [preços de moeda simplificado](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer).

### <a name="package-details-for-solution-template"></a>Detalhes do pacote para o modelo de solução

Forneça os detalhes do pacote seguintes:

- **Versão** -a versão do pacote que irá carregar. Etiquetas de versão tem de ser do formulário x.y. z, em que X, Y e Z são números inteiros.
- **Ficheiro de pacote (. zip)** -este pacote contém os seguintes arquivos, guardados num ficheiro. zip.
  - Maintemplate. JSON - o ficheiro de modelo de implementação que é utilizado para implementar a solução/aplicação e criar os recursos definidos para a solução. Para obter mais informações, consulte [como criar arquivos de modelo de implementação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - Createuidefinition - este ficheiro é utilizado pelo portal do Azure para gerar a interface do usuário para o aprovisionamento desta solução/aplicação. Para obter mais informações, consulte [interface de utilizador do portal do Azure de criar para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Este pacote deve conter quaisquer modelos aninhados ou scripts que são necessários para aprovisionar esta aplicação. O ficheiro de maintemplate. JSON e o ficheiro Createuidefinition tem de ser na pasta raiz.

   ![Detalhes do pacote para o modelo de solução](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

### <a name="package-details-for-managed-application"></a>Detalhes do pacote para aplicação gerida

Forneça os detalhes do pacote seguintes:

- **Versão** -a versão do pacote que irá carregar. Etiquetas de versão tem de ser do formulário x.y. z, em que X, Y e Z são números inteiros.
- **Ficheiro de pacote (. zip)** -este pacote contém os seguintes arquivos, guardados num ficheiro. zip.
  - applianceMainTemplate.json - o ficheiro de modelo de implementação que é utilizado para implementar a solução/aplicação e criar os recursos que estão definidos. Para obter mais informações, consulte [início rápido: Criar e implementar modelos Azure Resource Manager com o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json - este ficheiro é utilizado pelo portal do Azure para gerar a interface do usuário para o aprovisionamento desta solução/aplicação. Para obter mais informações, consulte [interface de utilizador do portal do Azure de criar para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - maintemplate. JSON - o ficheiro de modelo que contém apenas o recurso de Microsoft.Solution/appliances. Para obter mais informações, consulte [compreender a estrutura e a sintaxe de modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Tenha em atenção as seguintes propriedades de chave deste recurso:
    - "kind" - o valor deve ser "Marketplace" no caso da aplicação gerida pelo Marketplace.
    - "ManagedResourceGroupId" - o grupo de recursos na subscrição do cliente, onde todos os recursos definidos no applianceMainTemplate.json serão implementados.
    - "PublisherPackageId" – a cadeia de caracteres que identifica exclusivamente o pacote. Este valor tem de ser construída da seguinte forma: é uma concatenação das [publisherId]. [OfferId]-[SKUID] de pré-visualização. [PackageVersion].

  >[!IMPORTANT] 
  >Este pacote deve conter quaisquer modelos aninhados ou scripts que são necessários para aprovisionar esta aplicação. Estes ficheiros têm de estar na pasta raiz:  Maintemplate. JSON, applianceMainTemplate.json e applianceCreateUIDefinition.json.

- **Id do inquilino** -id de inquilino do Azure Active Directory da sua organização.
- **Ativar o acesso JIT?** – Selecione **Sim** para ativar o acesso de gestão de Just-In-Time para implementações de cliente com esta oferta.

  >[!NOTE] 
  >Se ativar JIT, tem de atualizar o ficheiro Createuidefinition para suportar o acesso JIT.

   ![Detalhes do pacote para aplicação gerida](./media/azureapp-sku-pkgdetails-managedapplication.png)

Para uma aplicação gerida tem de configurar definições de política e de autorização.

#### <a name="authorization"></a>Autorização

Adicione o Azure Active Directory do identificador de utilizador, grupo ou aplicação para o qual pretende conceder a permissão para o grupo de recursos gerido. A permissão que é concedida é indicada pela definição de função ID. Pode ser um proprietário, contribuinte ou qualquer função personalizada.

#### <a name="policy-settings"></a>Definições de política

Adicione as políticas que a aplicação gerida está em conformidade com. Saiba mais sobre as políticas de recursos do Azure, consulte [o que é o Azure Policy?](../../../governance/policy/overview.md)


   ![Definições de autorização e a política para uma aplicação gerida](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Para criar uma nova autorização:**

1. Sob **autorização**, selecione **+ nova autorização**.
2. Para **Id de Principal de**, escreva o identificador de diretório Active Directory do Azure do utilizador, grupo ou aplicação para o qual pretende conceder a permissão para o grupo de recursos gerido. A permissão que é concedida é indicada pela definição de função.
3. Para **definição de função**, selecione uma destas opções na lista pendente:  Proprietário ou contribuinte. Para obter mais informações, consulte [funções incorporadas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Podem ser adicionadas vários autorizações. No entanto, é recomendado para criar um grupo de utilizadores do Active Directory e especificar o respetivo ID em "PrincipalId." Isto irá permitir a adição de mais usuários ao grupo de utilizadores sem ter de atualizar o SKU.

**Para criar uma nova política:**

1. Sob **definições de política**, selecione **+ nova política**.
2. Para **nome da política**, introduza um nome para a política. O comprimento máximo do nome é de 50 carateres.
3. Para **políticas**, selecione uma das opções na lista pendente. Escolha a política que quer que o fornecedor de dados seja ativado quando o aplicativo usa os dados. Para obter mais informações, consulte a [exemplos de política do Azure](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Definições de política para uma aplicação gerida](./media/azureapp-sku-policy-settings.png)

4. Para **SKU da política**, selecione gratuito ou Standard como a política de tipo de SKU. O SKU Standard é necessário para políticas de auditoria.

## <a name="next-steps"></a>Passos Seguintes

[Separador Marketplace](./cpp-marketplace-tab.md)

---
title: Planeie um pedido gerido pela Azure para uma oferta de candidatura da Azure
description: Saiba o que é necessário para criar um plano de aplicações gerido para uma nova oferta de aplicações Azure utilizando o portal de marketplace comercial no Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 2f32fc9ffb8be5b71bfe84a4f0e946e68e8fcd03
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577826"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Planeie um pedido gerido pela Azure para uma oferta de candidatura da Azure

Um plano _de aplicação gerido pela_ Azure é uma forma de publicar uma oferta de aplicações da Azure no Azure Marketplace. Se ainda não o fez, leia [Plano uma oferta de aplicação Azure para o mercado comercial.](plan-azure-application-offer.md)

As aplicações geridas são ofertas de transação que são implementadas e faturadas através do Azure Marketplace. A opção de listagem que um utilizador vê é Get It Now.

Utilize uma Aplicação Azure: Plano de candidatura gerido quando forem necessárias as seguintes condições:

- Irá implementar uma solução baseada em subscrição para o seu cliente utilizando uma máquina virtual (VM) ou uma infraestrutura inteira como solução baseada em serviço (IaaS).
- Você ou o seu cliente requer que a solução seja gerida por um parceiro. Por exemplo, um parceiro pode ser um integrador de sistemas ou um prestador de serviços gerido (MSP).

## <a name="managed-application-offer-requirements"></a>Requisitos de oferta de aplicação geridos

| Requisitos | Detalhes |
| ------------ | ------------- |
| Uma subscrição do Azure | As aplicações geridas devem ser implementadas na subscrição de um cliente, mas podem ser geridas por terceiros. |
| Faturação e medição | Os recursos são fornecidos na subscrição Azure de um cliente. Os VMs que utilizam o modelo de pagamento pay-as-you-go são transacionados com o cliente através da Microsoft e faturados através da subscrição Azure do cliente. <br><br> Para os VMs de sua própria licença, a Microsoft fatura quaisquer custos de infraestrutura que sejam incorridos na subscrição do cliente, mas você transaciona diretamente as taxas de licenciamento de software com o cliente. |
| Disco rígido virtual compatível com Azure (VHD) | Os VMs devem ser construídos em Windows ou Linux. Para obter mais informações, veja:<br> • [Criar um ativo técnico Azure VM](/azure/marketplace/partner-center-portal/vm-certification-issues-solutions#how-to-address-a-vulnerability-or-exploit-in-a-vm-offer.md) (para VHDs windows).<br> •  [Distribuição linux endossada no Azure](/azure/virtual-machines/linux/endorsed-distros) (para Os VHDs Linux). |
| Atribuição de utilização dos clientes | Todas as novas ofertas de aplicações da Azure também devem incluir uma [atribuição de utilização do cliente parceiro Azure](azure-partner-customer-usage-attribution.md) GUID. Para obter mais informações sobre a atribuição de utilização do cliente e como a capacitar, consulte a [atribuição de utilização do cliente do parceiro Azure](azure-partner-customer-usage-attribution.md). |
| Pacote de implantação | Vai precisar de um pacote de implementação que permitirá aos clientes implementarem o seu plano. Se criar vários planos que exijam a mesma configuração técnica, pode utilizar o mesmo pacote. Para mais detalhes, consulte a secção seguinte: Pacote de implementação. |
|||

> [!NOTE]
> As aplicações geridas devem ser implementadas através do Azure Marketplace. Se a comunicação do cliente for uma preocupação, contacte os clientes interessados depois de ter ativado a partilha de chumbo.

## <a name="deployment-package"></a>Pacote de implantação

O pacote de implementação contém todos os ficheiros de modelo necessários para este plano, bem como quaisquer recursos adicionais, embalados como um ficheiro .zip.

Todas as aplicações Azure devem incluir estes dois ficheiros na pasta raiz de um arquivo .zip:

- Um ficheiro de modelo do Gestor de Recursos nomeado [mainTemplate.jsem](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md). Este modelo define os recursos para implantar na subscrição Azure do cliente. Para exemplos de modelos de Gestor de Recursos, consulte [a galeria de modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates/) ou o [gitHub correspondente: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) repo.
- Uma definição de interface de utilizador para a experiência de criação de aplicações Azure chamada [createUiDefinition.jsem](/azure/azure-resource-manager/managed-application-createuidefinition-overview). Na interface de utilizador, especifique os elementos que permitem aos consumidores fornecer valores de parâmetros.

Os tamanhos máximos de ficheiro suportados são:

- Até 1 Gb no tamanho total comprimido .zip archive
- Até 1 Gb para qualquer ficheiro individual não comprimido dentro do arquivo .zip

Todas as novas ofertas de aplicações da Azure também devem incluir uma [atribuição de utilização do cliente parceiro Azure](azure-partner-customer-usage-attribution.md) GUID.

## <a name="azure-regions"></a>Regiões do Azure

Pode publicar o seu plano na região pública de Azure, região do Governo de Azure, ou em ambos. Antes de publicar para o [Governo Azure,](/azure/azure-government/documentation-government-manage-marketplace-partners)teste e valide o seu plano no ambiente, uma vez que certos pontos finais podem diferir. Para configurar e testar o seu plano, solicite uma conta de teste do julgamento do [Governo Microsoft Azure](https://azure.microsoft.com/global-infrastructure/government/request/).

Você, como editora, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas. O Governo Azure utiliza centros e redes de dados fisicamente isolados (localizados apenas nos EUA).

Para obter uma lista de países e regiões apoiados pelo mercado comercial, consulte a disponibilidade geográfica e o [apoio cambial.](marketplace-geo-availability-currencies.md)

Os serviços do Governo Azure tratam dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para consciencializar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para a sua listagem diretamente no programa ou links para descrições da sua conformidade com eles nos seus próprios websites. Estas ligações visíveis apenas aos clientes do Governo Azure.

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver o seu plano

Pode configurar cada plano para ser visível para todos (público) ou apenas para um público específico (privado). Pode criar até 100 planos e até 45 deles podem ser privados. Pode querer criar um plano privado para oferecer diferentes opções de preços ou configurações técnicas a clientes específicos.

Você concede acesso a um plano privado usando IDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição que você atribui. Pode adicionar um máximo de 10 IDs de subscrição manualmente ou até 10.000 IDs de subscrição usando um . Ficheiro CSV. Os IDs de assinatura Azure são representados como GUIDs e as letras devem ser minúsculas.

Os planos privados não são suportados com subscrições Azure estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP). Para mais informações, consulte [ofertas privadas no mercado comercial da Microsoft.](private-offers.md)

> [!NOTE]
> Se publicar um plano privado, pode mudar a sua visibilidade para público mais tarde. No entanto, uma vez publicado um plano público, não se pode mudar a sua visibilidade para privado.

## <a name="define-pricing"></a>Definir preços

Deve fornecer o preço mensal para cada plano. Este preço é além de qualquer infraestrutura Azure ou custos de software pay-as-you-go incorridos pelos recursos implementados por esta solução.

Além do preço mensal, também pode definir preços para consumo de unidades não standard usando [faturação medido.](partner-center-portal/azure-app-metered-billing.md) Pode definir o preço por mês para zero e cobrar exclusivamente usando faturação medido, se quiser.

Os preços são definidos em USD (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando guardados. Mas pode optar por definir os preços dos clientes para cada mercado.

## <a name="just-in-time-jit-access"></a>Mesmo a tempo (JIT) acesso

O acesso ao JIT permite-lhe solicitar um acesso elevado aos recursos de uma aplicação gerida para resolução de problemas ou manutenção. Sempre tem acesso apenas de leitura aos recursos, mas por um período específico pode ter um maior acesso. Para obter mais informações, consulte [Ativar e solicitar acesso just-in-time para aplicações geridas azure](/azure/managed-applications/request-just-in-time-access).

> [!NOTE]
> Informações que o utilizador deve notar mesmo que desacerte a sua atualização do seu `createUiDefinition.json` ficheiro de forma a suportar esta funcionalidade.

## <a name="deployment-mode"></a>Modo de implantação

Pode configurar um plano de aplicação gerido para utilizar o modo de implementação **Completo** ou **Incremental.** Em modo completo, uma redistribuição da aplicação pelo cliente resulta na remoção de recursos no grupo de recursos geridos se os recursos não forem definidos no [mainTemplate.jsem](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md). Em modo incremental, uma redistribuição da aplicação deixa os recursos existentes inalterados. Para saber mais, consulte [os modos de implementação do Gestor de Recursos Azure](/azure/azure-resource-manager/templates/deployment-modes).

## <a name="notification-endpoint-url"></a>URL de ponto final de notificação

Pode opcionalmente fornecer um ponto final HTTPS Webhook para receber notificações sobre todas as operações da CRUD em instâncias de aplicação geridas de um plano.

## <a name="customize-allowed-customer-actions-optional"></a>Personalizar ações permitidas para o cliente (opcional)

Pode especificar opcionalmente quais as ações que os clientes podem realizar sobre os recursos geridos, além das `*/read` ações que estão disponíveis por padrão.

Se escolher esta opção, tem de fornecer as ações de controlo ou as ações de dados permitidas, ou ambas. Para obter mais informações, consulte [Understanding negar atribuições para recursos Azure](/azure/role-based-access-control/deny-assignments). Para as ações disponíveis, consulte [as operações do fornecedor de recursos do Azure Resource Manager](/azure/role-based-access-control/resource-provider-operations). Por exemplo, permitir que os consumidores reiniciem as máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` às ações permitidas.

## <a name="choose-who-can-manage-the-application"></a>Escolha quem pode gerir a aplicação

Deve indicar quem pode gerir uma aplicação gerida em cada uma das nuvens selecionadas: _Public Azure_ e _Azure Government Cloud_. Recolha as seguintes informações:

- **Azure Ative Directory Tenanty ID** – The Azure AD Tenant ID (também conhecido como ID de diretório) contendo as identidades dos utilizadores, grupos ou aplicações a que pretende conceder permissões. Você pode encontrar o seu ID de Inquilino Azure no portal Azure, em [Propriedades para Diretório Ativo Azure.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)
- **Autorizações** – Adicione o ID do objeto Azure Ative De cada utilizador, grupo ou aplicação que pretende ser autorizado ao grupo de recursos geridos. Identifique o utilizador pelo seu ID principal, que pode ser encontrado na lâmina de utilizadores do [Azure Ative Directory no portal Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Para cada ID principal, irá associar uma das funções incorporadas AD AD (Proprietário ou Contribuinte). A função selecionada descreve as permissões que o principal terá sobre os recursos na subscrição do cliente. Para obter mais informações, veja [Funções incorporadas do Azure](/azure/role-based-access-control/built-in-roles). Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), consulte [Começar com o RBAC no portal Azure](/azure/role-based-access-control/overview).

> [!NOTE]
> Embora possa adicionar até 100 autorizações por região de Azure, é geralmente mais fácil criar um grupo de utilizadores do Ative Directory e especificar o seu ID no "ID principal". Isto permite adicionar mais utilizadores ao grupo de gestão após a implementação do plano e reduzir a necessidade de atualizar o plano apenas para adicionar mais autorizações.

## <a name="policy-settings"></a>Definições de política

Pode aplicar [as Políticas Azure](/azure/governance/policy) à sua aplicação gerida para especificar os requisitos de conformidade para a solução implementada. Para definições de política e o formato de valores de parâmetros, consulte [Exemplos de Política do Azure](/azure/governance/policy/samples).

Pode configurar um máximo de cinco políticas, e apenas um caso de cada tipo de Política. Alguns tipos de política requerem parâmetros adicionais.

| Tipo de política | Parâmetros de política necessários |
| ------------ | ------------- |
| Encriptação da base de dados Azure SQL | Não |
| Definições de auditoria do servidor Azure SQL | Yes |
| Encriptação da Loja Azure Data Lake | Não |
| Definição de diagnóstico de auditoria | Yes |
| Conformidade da Localização de Recursos de Auditoria | Não |
|||

Para cada tipo de apólice que adicionar, deve associar a Standard ou Free Policy SKU. O SKU Standard é necessário para as políticas de auditoria. Os nomes das apólices estão limitados a 50 caracteres.

## <a name="next-steps"></a>Passos seguintes

- [Como criar uma oferta de aplicação Azure no mercado comercial](create-new-azure-apps-offer.md)

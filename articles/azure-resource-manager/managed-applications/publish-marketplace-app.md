---
title: Aplicativos geridos no Mercado
description: Descreve as aplicações geridas do Azure disponíveis através do Marketplace.
author: tfitzmac
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: dcd036f09982cba8271ed6057a167eb7440303a9
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054496"
---
# <a name="tutorial-publish-azure-managed-applications-in-the-marketplace"></a>Tutorial: Publicar aplicações geridas pela Azure no Mercado

Os fornecedores podem utilizar as aplicações geridas do Azure para disponibilizar as suas soluções a todos os clientes do Azure Marketplace. Esses fornecedores podem incluir fornecedores de serviços geridos (MSPs), fabricantes independentes de software (ISVs) e integradores de sistemas (SIs). As aplicações geridas reduzem os custos gerais de manutenção para os clientes. Os fornecedores vendem infraestruturas e software através do marketplace. Podem anexar serviços e suporte operacional às aplicações geridas. Para obter mais informações, veja [Descrição geral das aplicações geridas](overview.md).

Este artigo explica como pode publicar uma aplicação no marketplace e torná-la amplamente disponível para os clientes.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Pré-requisitos para publicar uma aplicação gerida

Para concluir este artigo, pecisa de ter o ficheiro .zip para a definição da aplicação gerida. Para obter mais informações, veja [Criar uma aplicação do catálogo de serviços](publish-service-catalog-app.md).

Existem vários pré-requisitos de negócio. São:

* A sua empresa ou subsidiária deve estar localizada num país/região onde as vendas sejam apoiadas pelo mercado.
* O produto tem de estar licenciado de forma a ser compatível com os modelos de faturação suportados pelo marketplace.
* Disponibilizar suporte técnico para os clientes de forma comercialmente razoável. O suporte pode ser gratuito, pago ou através da comunidade.
* Licencie o software e quaisquer dependências de software de terceiros.
* Forneça conteúdo que cumpra os critérios da sua oferta apresentado no Marketplace e no portal do Azure.
* Aceite os termos das Políticas de Participação e do Contrato de Publicação do Azure Marketplace.
* Aceite cumprir Termos de Utilização, a Declaração de Privacidade da Microsoft e o Contrato do Programa Microsoft Azure Certified.

Também deve ter uma conta marketplace. Para criar uma conta, consulte [como criar uma conta de Mercado Comercial no Partner Center.](../../marketplace/partner-center-portal/create-account.md)

## <a name="create-a-new-azure-application-offer"></a>Criar uma nova oferta de aplicação do Azure

Depois de criar a sua conta do portal de parceiros, está pronto para criar a sua oferta de aplicação gerida.

### <a name="set-up-an-offer"></a>Configurar uma oferta

A oferta de uma aplicação gerida corresponde a uma classe de oferta de produto de um editor. Se tiver um novo tipo de aplicação que queira disponibilizar no marketplace, pode configurá-lo como uma nova oferta. Uma oferta é uma coleção de SKUs. Cada oferta aparece como a sua própria entidade no marketplace.

1. Inicie sessão no [Cloud Partner Portal](https://cloudpartner.azure.com/).

1. No painel de navegação à esquerda, selecione **+ Novas**  >  **aplicações Azure.**

1. Na vista **Editor**, verá os formulários necessários. Cada formulário é descrito mais à frente neste artigo.

## <a name="offer-settings-form"></a>Formulário de Definições da Oferta

Os campos do formulário **Definições da Oferta** são:

* **ID da Oferta**: este identificador exclusivo identifica a oferta num perfil de editor. Este ID é visível nos URLs do produto, nos modelos do Resource Manager e nos relatórios de faturação. Apenas pode ser constituído por carateres alfanuméricos em minúsculas ou hífenes (-). O ID não pode terminar com um hífen. Está limitado a um máximo de 50 carateres. Depois da publicação de uma oferta, este campo fica bloqueado.
* **ID do Editor**: utilize esta lista pendente para escolher o perfil de editor onde quer publicar esta oferta. Depois da publicação de uma oferta, este campo fica bloqueado.
* **Nome**: este nome a apresentar da sua oferta aparece no Marketplace e no portal. Pode ter um máximo de 50 carateres. Inclua um nome de marca reconhecível para o produto. Não inclua o nome da sua empresa aqui, a menos que seja como está comercializado. Se estiver a comercializar esta oferta no seu próprio site, certifique-se de que o nome é exatamente como aparece no seu site.

Quando terminar, selecione **Guardar** para guardar o seu progresso.

## <a name="skus-form"></a>Formulário de SKUs

O passo seguinte é adicionar SKUs à sua oferta.

Um SKU é a unidade de compra mais pequena de uma oferta. Pode utilizar um SKU na mesma classe de produto (oferta) para diferenciar entre:

* As diversas funcionalidades suportadas
* Se a oferta é gerida ou não gerida
* Os modelos de faturação suportados

Um SKU aparece na oferta principal no marketplace. É apresentado como a sua própria entidade de compra no portal do Azure.

1. Selecione **SKUs**  >  **New SKU**.

1. Introduza o **ID do SKU**. O ID do SKU é um identificador exclusivo do SKU numa oferta. Este ID é visível nos URLs do produto, nos modelos do Resource Manager e nos relatórios de faturação. Apenas pode ser constituído por carateres alfanuméricos em minúsculas ou hífenes (-). O ID não pode terminar com um hífen e está limitado a um máximo de 50 carateres. Depois da publicação de uma oferta, este campo fica bloqueado. Pode ter vários SKUs numa oferta. Precisa de um SKU para cada imagem que queira publicar.

1. Preencha a secção **Detalhes do SKU** no seguinte formulário:

   Preencha os seguintes campos:

   * **Título**: introduza um título para o SKU. Este título é apresentado na galeria deste item.
   * **Resumo**: introduza um breve resumo para o SKU. Este texto é apresentado abaixo do título.
   * **Descrição**: introduza uma descrição detalhada do SKU.
   * **Tipo de SKU**: os valores permitidos são *Aplicação Gerida* e *Modelos de Solução*. Neste caso, selecione *Aplicação Gerida*.
   * **Disponibilidade país/região**: Selecione os países/regiões onde a aplicação gerida está disponível.
   * **Preços**: forneça um preço para a gestão da aplicação. Selecione os países/regiões disponíveis antes de definir o preço.

1. Adicione um novo pacote. Preencha a secção **Detalhes do Pacote** no seguinte formulário:

   Preencha os seguintes campos:

   * **Versão**: Introduza uma versão para o pacote que carrega. Deverá estar no formato `{number}.{number}.{number}{number}`.
   * **Ficheiro de pacote(.zip)**: Este pacote contém dois ficheiros necessários comprimidos num pacote .zip. Um ficheiro é um modelo do Resource Manager que define os recursos a implementar para a aplicação gerida. O outro ficheiro define a [interface de utilizador](create-uidefinition-overview.md) para os consumidores implementarem a aplicação gerida através do portal. Na interface de utilizador, especifique os elementos que permitem aos consumidores fornecer valores de parâmetros.
   * **Identificação do inquilino**: A identificação do inquilino para a conta para ter acesso.
   * **Ativar o Acesso JIT**: Selecione **Sim** para ativar o [controlo de acesso just-in-time](request-just-in-time-access.md) para a conta. Quando ativado, solicita o acesso à conta do consumidor por um período de tempo especificado. Para exigir que os consumidores da sua aplicação gerida concedam acesso permanente à sua conta, selecione **Nº**.
   * **Personalizar as ações permitidas para o cliente?**: Selecione **Sim** para especificar que ações os consumidores podem realizar nos recursos geridos.
   * **Ações permitidas ao cliente**: Se selecionar **Sim** para a definição anterior, pode especificar quais as ações permitidas aos consumidores utilizando [atribuições de negação para recursos Azure](../../role-based-access-control/deny-assignments.md).

     Para as ações disponíveis, consulte [as operações do fornecedor de recursos do Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md). Por exemplo, permitir que os consumidores reiniciem as máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` às ações permitidas. A `*/read` ação é automaticamente permitida para que não seja necessário incluir essa definição.
   * **PrincipalId**: esta propriedade é o identificador do Azure Active Directory (Azure AD) de um utilizador, grupo de utilizadores ou aplicação com acesso concedido aos recursos na subscrição do cliente. A Definição de Função descreve as permissões.
   * **Definição de Função**: esta propriedade é uma lista de todas as funções de Controlo de Acesso Baseado em Funções (RBAC) incorporadas fornecidas pelo Azure AD. Pode selecionar a função mais adequada a utilizar para gerir os recursos em nome do cliente.
   * **Definições de Política**: Aplicar uma [Azure Policy](../../governance/policy/overview.md) à sua aplicação gerida para especificar os requisitos de conformidade para as soluções implementadas. De entre as opções disponíveis, selecione as políticas a aplicar. Para **Parâmetros de Política**, forneça uma cadeia de carateres JSON com os valores parâmetro. Para definições de política e o formato de valores de parâmetros, consulte [Exemplos de Política do Azure](../../governance/policy/samples/index.md).

Pode adicionar várias autorizações. Recomendamos que crie um grupo de utilizadores do AD e especifique o respetivo ID em **PrincipalId**. Desta forma, pode adicionar mais utilizadores ao grupo de utilizadores sem a necessidade de atualizar o SKU.

Para obter mais informações sobre o RBAC, veja [Começar a utilizar o RBAC no portal do Azure](../../role-based-access-control/overview.md).

## <a name="marketplace-form"></a>Formulário do Marketplace

O formulário do Marketplace pede campos que aparecem no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal do Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Pré-visualizar IDs de subscrição

Introduza uma lista de IDs de subscrição do Azure que possa aceder à oferta depois da sua publicação. Pode utilizar estas subscrições permitidas para testar a oferta pré-visualizada antes de publicá-la. Pode compilar uma lista de até 100 subscrições no portal do parceiro.

### <a name="suggested-categories"></a>Categorias sugeridas

Selecione até cinco categorias na lista a que oferta pode ser melhor associada. Estas categorias são utilizadas para mapear a oferta para as categorias de produtos disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal do Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

O resumo da aplicação gerida apresenta os seguintes campos:

![Resumo do Marketplace](./media/publish-marketplace-app/publishvm10.png)

O separador **Descrição Geral** da aplicação gerida apresenta os seguintes campos:

![Descrição geral do Marketplace](./media/publish-marketplace-app/publishvm11.png)

O separador **Planos + Preços** da aplicação gerida apresenta os seguintes campos:

![Planos do Marketplace](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Portal do Azure

O resumo da aplicação gerida apresenta os seguintes campos:

![Resumo do portal](./media/publish-marketplace-app/publishvm12.png)

A descrição geral da aplicação gerida apresenta os seguintes campos:

![Descrição geral do portal](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Diretrizes para logótipos

Siga estas diretrizes para qualquer logótipo que carregar no Cloud Partner Portal:

*   O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias no logótipo.
*   As cores do tema do portal são preto e branco. Não utilize estas cores como cor de fundo do logótipo. Utilize uma cor que realce o logótipo no portal. Recomendamos cores primárias simples. *Se utilizar um fundo transparente, certifique-se de que o logótipo e o texto não são brancos, pretos ou azuis.*
*   Não utilize um fundo de gradação no logótipo.
*   Não coloque texto no logótipo, nem mesmo o nome da empresa ou da marca. O aspeto e funcionalidade do logótipo deve ser simples e evitar gradações.
*   Certifique-se que o logótipo não está ampliado.

#### <a name="hero-logo"></a>Logótipo de destaque

O logótipo de destaque é opcional. O editor pode optar por não carregar um logótipo de destaque. Depois de o ícone de destaque ser carregado, não pode ser eliminado. Nessa altura, o parceiro tem de seguir as diretrizes do Marketplace para ícones de destaque.

Siga estas diretrizes para o ícone do logótipo de destaque:

*   O nome a apresentar do editor, o título do plano e o resumo longo da oferta são apresentados a branco. Por conseguinte, não utilize uma cor clara para o fundo do ícone de destaque. Não é permitido um fundo preto, branco ou transparente para ícones de destaque.
*   Depois de a oferta ser listada, os elementos são incorporados programaticamente dentro do logótipo de destaque. Os elementos incorporados incluem o nome a apresentar do editor, o título do plano, o resumo longo da oferta e o botão **Criar**. Consequentemente, não introduza texto quando estruturar o logótipo de destaque. Deixe espaço em branco à direita porque o texto é incluído programaticamente nesse espaço. O espaço em branco para o texto deve ter 415 x 100 pixéis à direita. O deslocamento é de 370 pixéis a contar da esquerda.

    ![Exemplo de logótipo de destaque](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Formulário de suporte

Preencha o formulário de **Suporte** com contactos de suporte da sua empresa. Estas informações podem ser contactos de engenharia e contactos de apoio ao cliente.

## <a name="publish-an-offer"></a>Publicar uma oferta

Depois de preencher todas as secções, selecione **Publicar** para iniciar o processo que disponibiliza a oferta aos clientes.

## <a name="next-steps"></a>Próximos passos

* Para obter informações sobre o que acontece depois de clicar em **Publicar,** consulte [a oferta de aplicação da Publish Azure](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md)
* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para obter informações sobre como publicar uma aplicação gerida do Catálogo de Serviços, veja [Criar e publicar uma aplicação gerida do Catálogo de Serviços](publish-service-catalog-app.md).

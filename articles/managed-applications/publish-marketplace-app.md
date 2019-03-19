---
title: Aplicações geridas do Azure no Marketplace | Microsoft Docs
description: Descreve as aplicações geridas do Azure disponíveis através do Marketplace.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 07/10/2018
ms.author: tomfitz
ms.openlocfilehash: 0bf1558f81f2936b4556ee658a235cd09dddf905
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892919"
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Aplicações geridas do Azure no Marketplace

Os fornecedores podem utilizar as aplicações geridas do Azure para disponibilizar as suas soluções a todos os clientes do Azure Marketplace. Esses fornecedores podem incluir fornecedores de serviços geridos (MSPs), fabricantes independentes de software (ISVs) e integradores de sistemas (SIs). As aplicações geridas reduzem os custos gerais de manutenção para os clientes. Os fornecedores vendem infraestruturas e software através do marketplace. Podem anexar serviços e suporte operacional às aplicações geridas. Para obter mais informações, veja [Descrição geral das aplicações geridas](overview.md).

Este artigo explica como pode publicar uma aplicação no marketplace e torná-la amplamente disponível para os clientes.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Pré-requisitos para publicar uma aplicação gerida

Para concluir este artigo, pecisa de ter o ficheiro .zip para a definição da aplicação gerida. Para obter mais informações, veja [Criar uma aplicação do catálogo de serviços](publish-service-catalog-app.md).

Além disso, existem vários pré-requisitos empresariais. São:

* A sua empresa, ou respetiva subsidiária, tem de estar localizada num país em que as vendas sejam suportadas pelo marketplace.
* O produto tem de estar licenciado de forma a ser compatível com os modelos de faturação suportados pelo marketplace.
* Disponibilizar suporte técnico para os clientes de forma comercialmente razoável. O suporte pode ser gratuito, pago ou através da comunidade.
* Licencie o software e quaisquer dependências de software de terceiros.
* Forneça conteúdo que cumpra os critérios da sua oferta apresentado no Marketplace e no portal do Azure.
* Aceite os termos das Políticas de Participação e do Contrato de Publicação do Azure Marketplace.
* Aceite cumprir Termos de Utilização, a Declaração de Privacidade da Microsoft e o Contrato do Programa Microsoft Azure Certified.

## <a name="become-a-publisher"></a>Tornar-se um editor

Para se tornar um editor no Azure Marketplace, tem de:

1. Criar um ID da Microsoft - Crie a sua conta Microsoft com um endereço de e-mail que pertença ao domínio da sua empresa, mas não a um único indivíduo. Este endereço de e-mail é utilizado para o Centro para Programadores da Microsoft e o Cloud Partner Portal. Para obter mais informações, veja [Guia do Editor do Azure Marketplace](https://aka.ms/sellerguide).
1. Submeter o [Formulário de Nomeação do Azure Marketplace](https://aka.ms/ampnomination) - Para **Que solução quer publicar?**, selecione **Aplicação Gerida**. Depois de o formulário ser submetido, a equipa de inclusão do Marketplace revê a candidatura e valida o pedido. O processo de aprovação pode demorar entre um a três dias. Quando a nomeação for aprovada, receberá um código promocional para renunciar a taxa de registo do centro para programadores. Se **não** preencher o Formulário de Nomeação do Marketplace, é-lhe solicitado que pague uma taxa de registo de $99.
1. Registar-se no [Centro para Programadores](https://dev.windows.com/registration?accountprogram=azure) – A Microsoft valida que a sua organização é uma entidade legal válida com um NIF válido para o país no qual está registado. O processo de aprovação pode demorar entre 5 a 10 dias. Para evitar a taxa de registo, utilize o código promocional que recebeu no e-mail do processo de nomeação. Para obter mais informações, veja [Guia do Editor do Azure Marketplace](https://aka.ms/sellerguide).
1. Inicie sessão no [Cloud Partner Portal](https://cloudpartner.azure.com) – No perfil do editor, associe a sua conta do Centro para Programadores ao Perfil de Editor do Marketplace. Para obter mais informações, veja [Guia do Editor do Azure Marketplace](https://aka.ms/sellerguide).

## <a name="create-a-new-azure-application-offer"></a>Criar uma nova oferta de aplicação do Azure

Depois de criar a sua conta do portal de parceiros, está pronto para criar a sua oferta de aplicação gerida.

### <a name="set-up-an-offer"></a>Configurar uma oferta

A oferta de uma aplicação gerida corresponde a uma classe de oferta de produto de um editor. Se tiver um novo tipo de aplicação que queira disponibilizar no marketplace, pode configurá-lo como uma nova oferta. Uma oferta é uma coleção de SKUs. Cada oferta aparece como a sua própria entidade no marketplace.

1. Inicie sessão no [Cloud Partner Portal](https://cloudpartner.azure.com/).

1. No painel de navegação à esquerda, selecione **+ Nova oferta** > **Aplicações do Azure**.

1. Na vista **Editor**, verá os formulários necessários. Cada formulário é descrito mais à frente neste artigo.

## <a name="offer-settings-form"></a>Formulário de Definições da Oferta

Os campos do formulário **Definições da Oferta** são:

* **ID da oferta**: Este identificador exclusivo identifica a oferta de um perfil do publicador. Este ID é visível nos URLs do produto, nos modelos do Resource Manager e nos relatórios de faturação. Apenas pode ser constituído por carateres alfanuméricos em minúsculas ou hífenes (-). O ID não pode terminar com um hífen. Está limitado a um máximo de 50 carateres. Depois da publicação de uma oferta, este campo fica bloqueado.
* **ID de publicador**: Utilize esta lista pendente para escolher o perfil do publicador que pretende publicar esta oferta em. Depois da publicação de uma oferta, este campo fica bloqueado.
* **Nome**: Este nome a apresentar para a sua oferta será exibido no Marketplace e no portal. Pode ter um máximo de 50 carateres. Inclua um nome de marca reconhecível para o produto. Não inclua o nome da sua empresa aqui, a menos que seja como está comercializado. Se estiver a comercializar esta oferta no seu próprio site, certifique-se de que o nome é exatamente como aparece no seu site.

Quando terminar, selecione **Guardar** para guardar o seu progresso.

## <a name="skus-form"></a>Formulário de SKUs

O passo seguinte é adicionar SKUs à sua oferta.

Um SKU é a unidade de compra mais pequena de uma oferta. Pode utilizar um SKU na mesma classe de produto (oferta) para diferenciar entre:

* As diversas funcionalidades suportadas
* Se a oferta é gerida ou não gerida
* Os modelos de faturação suportados

Um SKU aparece na oferta principal no marketplace. É apresentado como a sua própria entidade de compra no portal do Azure.

1. Selecione **SKUs** > **Novo SKU**.

1. Introduza o **ID do SKU**. O ID do SKU é um identificador exclusivo do SKU numa oferta. Este ID é visível nos URLs do produto, nos modelos do Resource Manager e nos relatórios de faturação. Apenas pode ser constituído por carateres alfanuméricos em minúsculas ou hífenes (-). O ID não pode terminar com um hífen e está limitado a um máximo de 50 carateres. Depois da publicação de uma oferta, este campo fica bloqueado. Pode ter vários SKUs numa oferta. Precisa de um SKU para cada imagem que queira publicar.

1. Preencha a secção **Detalhes do SKU** no seguinte formulário:

   Preencha os seguintes campos:

   * **Título**: Introduza um título para este SKU. Este título é apresentado na galeria deste item.
   * **Resumo**: Introduza um breve resumo para este SKU. Este texto é apresentado abaixo do título.
   * **Descrição**: Introduza uma descrição detalhada sobre o SKU.
   * **Tipo de SKU**: Os valores permitidos são *Managed Application* e *modelos de soluções*. Neste caso, selecione *Aplicação Gerida*.
   * **Disponibilidade de país/região**: Selecione os países onde a aplicação gerida está disponível.
   * **Preços**: Forneça um preço para a gestão da aplicação. Selecione os países disponíveis antes de definir o preço.

1. Adicione um novo pacote. Preencha a secção **Detalhes do Pacote** no seguinte formulário:

   Preencha os seguintes campos:

   * **Versão**: Introduza uma versão para o pacote que carrega. Deverá estar no formato `{number}.{number}.{number}{number}`.
   * **Ficheiro de pacote (. zip)**: Este pacote contém ficheiros necessários dois compactados num pacote. zip. Um ficheiro é um modelo do Resource Manager que define os recursos a implementar para a aplicação gerida. O outro ficheiro define a [interface de utilizador](create-uidefinition-overview.md) para os consumidores implementarem a aplicação gerida através do portal. Na interface de utilizador, especifique os elementos que permitem aos consumidores fornecer valores de parâmetros.
   * **PrincipalId**: Esta propriedade é o identificador do Azure Active Directory (Azure AD) de um utilizador, o grupo de utilizadores ou a aplicação que é concedida acesso aos recursos na subscrição do cliente. A Definição de Função descreve as permissões.
   * **Definição de função**: Esta propriedade é uma lista de todos os as controlo de acesso baseado em funções (RBAC) funções incorporadas fornecidas pelo Azure AD. Pode selecionar a função mais adequada a utilizar para gerir os recursos em nome do cliente.
   * **Definições de política**: Aplicar uma [do Azure Policy](../governance/policy/overview.md) para seu aplicativo gerenciado para especificar os requisitos de conformidade para as soluções implementadas. De entre as opções disponíveis, selecione as políticas a aplicar. Para **Parâmetros de Política**, forneça uma cadeia de carateres JSON com os valores parâmetro. Para definições de política e o formato de valores de parâmetros, consulte [Exemplos de Política do Azure](../governance/policy/samples/index.md).

Pode adicionar várias autorizações. Recomendamos que crie um grupo de utilizadores do AD e especifique o respetivo ID em **PrincipalId**. Desta forma, pode adicionar mais utilizadores ao grupo de utilizadores sem a necessidade de atualizar o SKU.

Para obter mais informações sobre o RBAC, veja [Começar a utilizar o RBAC no portal do Azure](../role-based-access-control/overview.md).

## <a name="marketplace-form"></a>Formulário do Marketplace

O formulário do Marketplace pede campos que aparecem no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal do Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Pré-visualizar IDs de subscrição

Introduza uma lista de IDs de subscrição do Azure que possa aceder à oferta depois da sua publicação. Pode utilizar estas subscrições permitidas para testar a oferta pré-visualizada antes de publicá-la. Pode compilar uma lista de permissão com um máximo de 100 subscrições no portal de parceiros.

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

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para obter informações sobre como publicar uma aplicação gerida do Catálogo de Serviços, veja [Criar e publicar uma aplicação gerida do Catálogo de Serviços](publish-service-catalog-app.md).
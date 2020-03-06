---
title: Publique uma oferta de serviços geridos ao Azure Marketplace
description: Saiba como publicar uma oferta de serviço gerida que a bordo dos clientes para a gestão de recursos delegados do Azure.
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 6ae93759073be6b05d118ccf46f6b6367fff5fc6
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78328947"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publique uma oferta de serviços geridos ao Azure Marketplace

Neste artigo, você aprenderá a publicar uma oferta de serviços geridos públicos ou privados para o [Azure Marketplace](https://azuremarketplace.microsoft.com) usando o [Portal do Parceiro cloud](https://cloudpartner.azure.com/), permitindo a um cliente que adquira a oferta a recursos a bordo para a gestão de recursos delegados do Azure.

> [!NOTE]
> Precisa de ter uma conta válida [no Partner Center](../../marketplace/partner-center-portal/create-account.md) para criar e publicar estas ofertas. Se ainda não tiver uma conta, o processo de inscrição irá [levá-lo](https://aka.ms/joinmarketplace) através dos passos de criação de uma conta no Partner Center e de se inscrever no programa Mercado Comercial. O id da Microsoft Partner Network (MPN) será [automaticamente associado às](../../billing/billing-partner-admin-link-started.md) ofertas que publica para acompanhar o seu impacto através dos compromissos dos clientes.
>
> Se não quiser publicar uma oferta para o Azure Marketplace, pode embarcar nos clientes manualmente utilizando modelos do Gestor de Recursos Do Azure. Para mais informações, consulte [A bordo um cliente para a gestão de recursos delegados do Azure.](onboard-customer.md)

Publicar uma oferta de Serviços Geridos é semelhante à publicação de qualquer outro tipo de oferta ao Azure Marketplace. Para conhecer este processo, consulte o [Azure Marketplace e o AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md) e [gere as ofertas do Azure e do AppSource Marketplace.](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md) Deve também rever as políticas de certificação do [mercado comercial,](https://docs.microsoft.com/legal/marketplace/certification-policies)nomeadamente a secção [de Serviços Geridos.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Assim que um cliente adicionar a sua oferta, eles poderão delegar uma ou mais subscrições ou grupos de recursos específicos que serão então a bordo para a [gestão de recursos delegados do Azure.](#the-customer-onboarding-process) Note que antes de uma subscrição (ou grupos de recursos dentro de uma subscrição) pode ser a bordo, a subscrição deve ser autorizada para o embarque através do registo manual do fornecedor de recursos **Microsoft.ManagedServices.**

> [!IMPORTANT]
> Cada plano numa oferta de serviços geridos inclui uma secção **de Detalhes Manifestos,** onde define as entidades azure Ative Directory (Azure AD) no seu inquilino que terão acesso aos grupos de recursos delegados e/ou subscrições para clientes que adquiram esse plano. É importante estar ciente de que qualquer grupo (ou utilizador ou diretor de serviço) que você inclui aqui terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir diferentes grupos para trabalhar com cada cliente, terá de publicar um plano privado separado que seja exclusivo de cada cliente.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Crie a sua oferta no Portal do Parceiro cloud

1. Inscreva-se no Portal do [Parceiro cloud.](https://cloudpartner.azure.com/)
2. A partir do menu de navegação à esquerda, selecione **Nova oferta,** em seguida, selecione **serviços geridos**.
3. Verá uma secção **de Editor** para a sua oferta com quatro partes para preencher: Definições de **oferta,** **planos,** **marketplace**e **suporte.** Leia a informação sobre como completar estas secções.

## <a name="enter-offer-settings"></a>Insira as definições de oferta

Na secção **de definições de Oferta,** forneça o seguinte:

|Campo  |Descrição  |
|---------|---------|
|**Id oferta**     | Um identificador único para a sua oferta (dentro do perfil da sua editora). Este ID só pode conter caracteres alfanuméricos minúsculos, traços e sublinhados, com um máximo de 50 caracteres. Tenha em mente que o ID da Oferta pode ser visível para os clientes em locais como urLs de produto e relatórios de faturação. Uma vez publicada a oferta, não pode alterar este valor.        |
|**ID da editora**     | O ID da editora que será associado à oferta. Se tiver mais de um ID de editor, pode selecionar o que deseja utilizar para esta oferta.       |
|**Nome**     | O nome (até 50 caracteres) que os clientes vão ver para a sua oferta no Azure Marketplace e no portal Azure. Use uma marca reconhecível que os clientes compreenderão — se estiver a promover esta oferta através do seu próprio website, certifique-se de usar exatamente o mesmo nome aqui.        |

Quando terminar, selecione **Guardar**. Agora estás pronto para passar para a secção **de Planos.**

## <a name="create-plans"></a>Criar planos

Cada oferta deve ter um ou mais planos (por vezes referidos como SKUs). Você pode adicionar vários planos para suportar diferentes conjuntos de funcionalidades a diferentes preços ou para personalizar um plano específico para um público limitado de clientes específicos. Os clientes podem ver os planos que lhes estão disponíveis ao abrigo da oferta-mãe.

Na secção Planos, selecione **Novo Plano**. Em seguida, introduza um ID do **plano**. Este ID só pode conter caracteres alfanuméricos minúsculos, traços e sublinhados, com um máximo de 50 caracteres. O ID do plano pode ser visível para os clientes em locais como urls de produto e relatórios de faturação. Uma vez publicada a oferta, não pode alterar este valor.

### <a name="plan-details"></a>Detalhes do plano

Complete as seguintes secções na secção Detalhes do **Plano:**

|Campo  |Descrição  |
|---------|---------|
|**Title** (Título)     | Nome amigável para o plano de exibição. Comprimento máximo de 50 caracteres.        |
|**Resumo**     | Descrição sucinta do plano de exibição sob o título. Comprimento máximo de 100 caracteres.        |
|**Descrição**     | Texto de descrição que fornece uma explicação mais detalhada do plano.         |
|**Modelo de faturação**     | Existem 2 modelos de faturação mostrados aqui, mas você deve escolher **Trazer a sua própria licença** para ofertas de serviços geridos. Isto significa que irá cobrar diretamente aos seus clientes os custos relacionados com esta oferta, e a Microsoft não cobra quaisquer taxas a si.   |
|**Isto é um plano privado?**     | Indica se o SKU é privado ou público. O padrão é **Não** (público). Se deixar esta seleção, o seu plano não se restringirá a clientes específicos (ou a um determinado número de clientes); Depois de publicar um plano público, não pode depois mudá-lo para privado. Para disponibilizar este plano apenas a clientes específicos, selecione **Sim**. Quando o fizer, terá de identificar os clientes fornecendo as suas iDs de subscrição. Estes podem ser introduzidos um a um (para até 10 assinaturas) ou através do upload de um ficheiro .csv (para até 20.000 assinaturas). Certifique-se de incluir as suas próprias subscrições aqui para que possa testar e validar a oferta. Para mais informações, consulte [As SKUs e Planos Privados.](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)  |

> [!IMPORTANT]
> Uma vez que um plano é publicado como público, não pode mudá-lo para privado. Para controlar quais os clientes que podem aceitar a sua oferta e delegar recursos, utilize um plano privado. Com um plano público, não é possível restringir a disponibilidade a determinados clientes ou mesmo a um certo número de clientes (embora possa deixar de vender o plano completamente se optar por fazê-lo). Só pode [remover o acesso a uma delegação](onboard-customer.md#remove-access-to-a-delegation) depois de um cliente aceitar uma oferta apenas se incluir uma **Autorização** com a Definição de **Funções** definida para a Atribuição de Inscrição de [Serviços Geridos Eliminar funções](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando publicou a oferta. Também pode contactar o cliente e pedir-lhe que [remova o seu acesso](view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="manifest-details"></a>Detalhes manifestos

Preencha a secção **Dedetalhes Manifestos** para o seu plano. Isto cria um manifesto com informação de autorização para gestão de recursos do cliente. Esta informação é necessária para permitir a gestão de recursos delegados do Azure.

> [!NOTE]
> Como referido acima, os utilizadores e funções nas suas entradas de **Autorização** aplicar-se-ão a todos os clientes que adquirirem o plano. Se quiser limitar o acesso a um cliente específico, terá de publicar um plano privado para o seu uso exclusivo.

Primeiro, forneça uma **versão** para o manifesto. Utilize o formato *n.n.n* (por exemplo, 1.2.5).

Em seguida, insira a sua **identificação de inquilino.** Este é um GUID associado ao ID de inquilino do Azure Ative Diretório da sua organização (ou seja, o inquilino em que trabalhará para gerir os recursos dos seus clientes). Se não tiver isso à mão, pode encontrá-lo pairando sobre o nome da sua conta no lado superior direito do portal Azure, ou selecionando **o diretório Switch**.

Finalmente, adicione uma ou mais entradas de **Autorização** ao seu plano. As autorizações definem as entidades que podem aceder a recursos e subscrições para clientes que adquirem o plano, e atribuem funções que concedem níveis específicos de acesso.

> [!TIP]
> Na maioria dos casos, pretende atribuir permissões a um grupo de utilizadores ou prestador de serviços da Azure AD, em vez de uma série de contas individuais de utilizadores. Isto permite adicionar ou remover o acesso a utilizadores individuais sem ter de atualizar e republicar o plano quando os seus requisitos de acesso mudarem. Para recomendações adicionais, consulte [Inquilinos, funções e utilizadores em cenários do Farol Azure.](../concepts/tenants-users-roles.md)

Para cada **Autorização,** terá de fornecer o seguinte. Em seguida, pode selecionar **nova autorização** quantas vezes for em função do necessário para adicionar mais utilizadores e definições de papéis.

- **ID de objeto ad ad -** O identificador Azure AD de um utilizador, grupo de utilizadores ou aplicação que será concedida certas permissões (conforme descrito pela Definição de Funções) aos recursos dos seus clientes.
- Nome de **exibição de objeto saque a Azure AD**: Um nome amigável para ajudar o cliente a compreender o propósito desta autorização. O cliente verá este nome ao delegar recursos.
- **Definição de Funções**: Selecione uma das funções integradas do Azure AD disponível na lista. Esta função determinará as permissões que o utilizador no campo DeId de **Objeto AD Azure** terá nos recursos dos seus clientes. Para descrições destas funções, consulte [funções incorporadas](../../role-based-access-control/built-in-roles.md) e [suporte a papéis para a gestão de recursos delegados do Azure.](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)
  > [!NOTE]
  > À medida que forem adicionadas novas funções incorporadas ao Azure, estarão disponíveis aqui, embora possa haver algum atraso antes de aparecerem.
- **Funções atribuídas**: Isto só é necessário se tiver selecionado administrador de acesso ao utilizador na Definição de **Funções** para esta autorização. Em caso afirmativo, deve adicionar aqui um ou mais papéis atribuíveis. O utilizador no campo **Deidentificação de Objetos AD Azure** poderá atribuir estas **Funções Atribuídas** a [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md), o que é necessário para [implementar políticas que possam ser remediadas.](deploy-policy-remediation.md) Note que nenhuma outra permissão normalmente associada à função de Administrador de Acesso ao Utilizador será aplicada a este utilizador. Se não selecionar uma ou mais funções aqui, a sua submissão não passará pela certificação. (Se não selecionou o Administrador de Acesso ao Utilizador para a Definição de Funções deste utilizador, este campo não tem qualquer efeito.)

> [!TIP]
> Para garantir que pode [remover o acesso a uma delegação,](onboard-customer.md#remove-access-to-a-delegation) se necessário, inclua uma **Autorização** com a Definição de **Funções** definida para a [Atribuição de Inscrição de Serviços Geridos .](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Se esta função não for atribuída, os recursos delegados só podem ser removidos por um utilizador no inquilino do cliente.

Uma vez concluída a informação, pode selecionar **o Novo Plano** quantas vezes for necessário para criar planos adicionais. Quando terminar, selecione **Guardar**e, em seguida, continue na secção **Marketplace.**

## <a name="provide-marketplace-text-and-images"></a>Fornecer texto e imagens do Marketplace

A secção **Marketplace** é onde fornece o texto e as imagens que os clientes verão no Azure Marketplace e no portal Azure.

Complete os seguintes campos na secção **Visão Geral:**

|Campo  |Descrição  |
|---------|---------|
|**Title** (Título)     |  Título da oferta, muitas vezes o nome longo e formal. Este título será exibido de forma proeminente no mercado. Comprimento máximo de 50 caracteres. Na maioria dos casos, este deve ser o mesmo que o **Nome** que inseriu na secção Definições de **Oferta.**       |
|**Resumo**     | Breve propósito ou função da sua oferta. Isto é geralmente exibido sob o título. Comprimento máximo de 100 caracteres.        |
|**Resumo longo**     | Um resumo mais longo do propósito ou função da sua oferta. Comprimento máximo de 256 caracteres.        |
|**Descrição**     | Mais informações sobre a sua oferta. Este campo tem um comprimento máximo de 3000 caracteres e suporta formatação HTML simples. Deve incluir as palavras "serviço gerido" ou "serviços geridos" algures na sua descrição.       |
|**Identificador de Marketing**     | Um identificador único e amigável com URL. Este identificador só pode conter caracteres alfanuméricos minúsculos e traços. Será utilizado em URLs de Marketplace para esta oferta. Por exemplo, se o seu ID da editora for *contoso* e o seu identificador de marketing for *sampleApp*, o URL para a sua oferta no Azure Marketplace será *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp* .        |
|**IDs de subscrição de pré-visualização**     | Adicione um a 100 identificadores de subscrição. Os clientes associados a estas subscrições poderão ver a oferta no Azure Marketplace antes de entrar em funcionar. Sugerimos que inclua as suas próprias subscrições aqui para que possa pré-visualizar como a sua oferta aparece no Mercado Azure antes de a disponibilizar aos clientes.  (As equipas de suporte e engenharia da Microsoft também poderão ver a sua oferta durante este período de pré-visualização.)   |
|**Links úteis**     | URLs relacionados com a sua oferta, tais como documentação, notas de lançamento, PERGUNTAS, etc.        |
|**Categorias Sugeridas (Max 5)**     | Uma ou mais categorias (até cinco) que se aplicam à sua oferta. Estas categorias ajudam os clientes a descobrir a sua oferta no Azure Marketplace e no portal Azure.        |

Na secção Artefactos de **Marketing,** pode fazer upload de logotipos e outros ativos para serem mostrados com a sua oferta. Pode fazer o upload opcional de imagens ou links para vídeos que possam ajudar os clientes a compreender a sua oferta.

São necessários quatro tamanhos de logotipo: **Pequeno (40x40)** , **Médio (90x90)** , **Grande (115x115)** e **Largo (255x115)** . Siga estas orientações para os seus logotipos:

- O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias no logótipo.
- As cores do tema do portal são preto e branco. Não utilize estas cores como cor de fundo do logótipo. Utilize uma cor que realce o logótipo no portal. Recomendamos cores primárias simples.
- Se utilizar um fundo transparente, certifique-se de que o logotipo e o texto não são brancos, pretos ou azuis.
- O aspeto e funcionalidade do logótipo deve ser simples e evitar gradações. Não utilize um fundo de gradação no logótipo.
- Não coloque texto no logótipo, nem mesmo o nome da empresa ou da marca.
- Certifique-se que o logótipo não está ampliado.

O logótipo **Do Herói (815x290)** é opcional, mas recomendado. Se incluir um logotipo de herói, siga estas orientações:

- Não inclua nenhum texto no logotipo do herói, e certifique-se de deixar 415 pixels de espaço vazio no lado direito do logotipo. Isto é necessário para deixar espaço para elementos de texto que serão incorporados programáticamente: o nome da exibição do editor, o título do plano, oferecem um longo resumo.
- O passado do seu logotipo herói pode não ser preto, branco ou transparente. Certifique-se de que a cor de fundo não é muito clara, pois o texto incorporado será exibido em branco.
- Uma vez publicada a sua oferta com um ícone de herói, não pode removê-la (embora possa atualizá-la com uma versão diferente, se desejar).

Na secção **De Gestão** de Chumbo, pode selecionar o sistema CRM onde os seus cabos serão armazenados. Note que de acordo com as políticas de certificação de [Serviços Geridos,](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)é necessário um **Destino Principal.**

Por fim, forneça o seu URL e **Termos de Utilização da** Política de **Privacidade** na secção **Legal.** Também pode especificar aqui se deve ou não utilizar o [Contrato Padrão](../../marketplace/standard-contract.md) para esta oferta.

Certifique-se de que guarda as suas alterações antes de passar para a secção **de suporte.**

## <a name="add-support-info"></a>Adicionar informações de suporte

Na secção **suporte,** forneça o nome, e-mail e número de telefone para um contacto de engenharia e um contacto de apoio ao cliente. Também terá de fornecer URLs de apoio. A Microsoft poderá utilizar estas informações quando precisamos de o contactar sobre problemas de negócios e suporte.

Depois de adicionar esta informação, selecione **Save.**

## <a name="publish-your-offer"></a>Publique a sua oferta

Depois de ter concluído todas as secções, o seu próximo passo é publicar a oferta ao Azure Marketplace. Selecione o botão **Publicar** para iniciar o processo de tornar a sua oferta ao vivo. Para mais informações sobre este processo, consulte [as ofertas do Publish Azure Marketplace e da AppSource.](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md)

Pode [publicar uma versão atualizada da sua oferta](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) a qualquer momento. Por exemplo, pode querer adicionar uma nova definição de papel a uma oferta anteriormente publicada. Quando o fizer, os clientes que já adicionaram a oferta verão um ícone na página de fornecedores de [**Serviços**](view-manage-service-providers.md) no portal Azure que os permite saber que está disponível uma atualização. Cada cliente poderá [rever as alterações](view-manage-service-providers.md#update-service-provider-offers) e decidir se pretende atualizar para a nova versão. 

## <a name="the-customer-onboarding-process"></a>O processo de embarque do cliente

Depois de um cliente adicionar a sua oferta, eles poderão [delegar uma ou mais subscrições ou grupos de recursos específicos](view-manage-service-providers.md#delegate-resources), que serão depois a bordo para a gestão de recursos delegados do Azure. Se um cliente aceitou uma oferta mas ainda não delegou quaisquer recursos, verá uma nota no topo do **Fornecedor oferece** secção da página de fornecedores de [**serviços**](view-manage-service-providers.md) no portal Azure.

> [!IMPORTANT]
> A delegação deve ser feita por uma conta não hóspede no inquilino do cliente que tenha o [papel integrado](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) do Proprietário para a subscrição a bordo (ou que contenha os grupos de recursos que estão a bordo). Para ver todos os utilizadores que possam delegar a subscrição, um utilizador no inquilino do cliente pode selecionar a subscrição no portal Azure, abrir o controlo de **acesso (IAM),** e [ver todos os utilizadores com a função Proprietário.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)

Após o cliente delegar uma subscrição (ou um ou mais grupos de recursos dentro de uma subscrição), o fornecedor de recursos **Da Microsoft.ManagedServices** será registado para essa subscrição, e os utilizadores do seu inquilino poderão aceder aos recursos delegados de acordo com as autorizações na sua oferta.

## <a name="next-steps"></a>Passos seguintes

- Conheça [as experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
- [Ver e gerir os clientes](view-manage-customers.md) indo para os meus **clientes** no portal Azure.

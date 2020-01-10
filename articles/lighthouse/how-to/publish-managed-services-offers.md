---
title: Publicar uma oferta de serviços gerenciados no Azure Marketplace
description: Saiba como publicar uma oferta de serviço gerenciado que integra os clientes ao gerenciamento de recursos delegado do Azure.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: d1eb06794551be498e05e2b9c3b893013b718ce9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453541"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publicar uma oferta de serviços gerenciados no Azure Marketplace

Neste artigo, você aprenderá a publicar uma oferta pública ou privada de serviços gerenciados para o [Azure Marketplace](https://azuremarketplace.microsoft.com) usando o [portal do Cloud Partner](https://cloudpartner.azure.com/), permitindo que um cliente compre a oferta para recursos integrados para o gerenciamento de recursos delegado do Azure.

> [!NOTE]
> Você precisa ter uma conta válida [no Partner Center](../../marketplace/partner-center-portal/create-account.md) para criar e publicar essas ofertas. Se você ainda não tiver uma conta, o [processo de inscrição](https://aka.ms/joinmarketplace) o conduzirá pelas etapas de criação de uma conta no Partner Center e registro no programa do Marketplace comercial. Sua ID de Microsoft Partner Network (MPN) será [associada automaticamente](../../billing/billing-partner-admin-link-started.md) às ofertas que você publicar para controlar seu impacto nos compromissos do cliente.
>
> Se não quiser publicar uma oferta no Azure Marketplace, você poderá integrar os clientes manualmente usando modelos de Azure Resource Manager. Para obter mais informações, consulte integrar [um cliente ao gerenciamento de recursos delegado do Azure](onboard-customer.md).

A publicação de uma oferta de serviços gerenciados é semelhante à publicação de qualquer outro tipo de oferta no Azure Marketplace. Para saber mais sobre esse processo, consulte [Guia de publicação do Azure Marketplace e AppSource](../../marketplace/marketplace-publishers-guide.md) e [gerenciar ofertas do Azure e do AppSource Marketplace](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md). Você também deve examinar as [políticas de certificação do Marketplace comercial](https://docs.microsoft.com/legal/marketplace/certification-policies), especialmente a seção [Serviços gerenciados](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) .

Depois que um cliente adiciona sua oferta, ele poderá delegar uma ou mais assinaturas ou grupos de recursos específicos que serão [integrados ao gerenciamento de recursos delegado do Azure](#the-customer-onboarding-process). Observe que, antes que uma assinatura (ou grupos de recursos em uma assinatura) possa ser integrada, a assinatura deve ser autorizada para integração Registrando manualmente o provedor de recursos **Microsoft. managedservices** .

> [!IMPORTANT]
> Cada plano em uma oferta de serviços gerenciados inclui uma seção **detalhes do manifesto** , em que você define as entidades Azure Active Directory (Azure AD) em seu locatário que terão acesso aos grupos de recursos delegados e/ou assinaturas para clientes que compram esse plano. É importante estar ciente de que qualquer grupo (ou entidade de serviço ou usuário) que você incluir aqui terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir grupos diferentes para trabalhar com cada cliente, você precisará publicar um plano privado separado que seja exclusivo para cada cliente.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Crie sua oferta no Portal do Cloud Partner

1. Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2. No menu de navegação à esquerda, selecione **nova oferta**e, em seguida, selecione **Serviços gerenciados**.
3. Você verá uma seção do **Editor** para sua oferta com quatro partes a serem preenchidas: **configurações da oferta**, **planos**, **Marketplace**e **suporte**. Continue lendo para obter orientação sobre como concluir essas seções.

## <a name="enter-offer-settings"></a>Inserir configurações da oferta

Na seção **configurações da oferta** , forneça o seguinte:

|Campo  |Descrição  |
|---------|---------|
|**ID da oferta**     | Um identificador exclusivo para sua oferta (dentro do seu perfil de editor). Essa ID pode conter apenas caracteres alfanuméricos minúsculos, traços e sublinhados, com um máximo de 50 caracteres. Tenha em mente que a ID da oferta pode estar visível para clientes em locais como em URLs de produtos e relatórios de cobrança. Depois de publicar a oferta, você não poderá alterar esse valor.        |
|**ID do editor**     | A ID do Publicador que será associada à oferta. Se você tiver mais de uma ID de Publicador, poderá selecionar aquela que deseja usar para esta oferta.       |
|**Nome**     | O nome (até 50 caracteres) que os clientes verão para sua oferta no Azure Marketplace e no portal do Azure. Use um nome de marca reconhecível que os clientes compreendam — se você estiver promovendo essa oferta por meio de seu próprio site, certifique-se de usar exatamente o mesmo nome aqui.        |

Quando tiver terminado, selecione **salvar**. Agora você está pronto para passar para a seção **planos** .

## <a name="create-plans"></a>Criar planos

Cada oferta deve ter um ou mais planos (às vezes chamados de SKUs). Você pode adicionar vários planos para dar suporte a diferentes conjuntos de recursos em preços diferentes ou para personalizar um plano específico para um público limitado de clientes específicos. Os clientes podem exibir os planos disponíveis para eles na oferta pai.

Na seção planos, selecione **novo plano**. Em seguida, insira uma **ID de plano**. Essa ID pode conter apenas caracteres alfanuméricos minúsculos, traços e sublinhados, com um máximo de 50 caracteres. A ID do plano pode ser visível para clientes em locais como em URLs de produtos e relatórios de cobrança. Depois de publicar a oferta, você não poderá alterar esse valor.

### <a name="plan-details"></a>Detalhes do plano

Conclua as seções a seguir na seção **detalhes do plano** :

|Campo  |Descrição  |
|---------|---------|
|**Title** (Título)     | Nome amigável do plano para exibição. Comprimento máximo de 50 caracteres.        |
|**Resumo**     | Descrição sucinta do plano para exibição sob o título. Comprimento máximo de 100 caracteres.        |
|**Descrição**     | Texto de descrição que fornece uma explicação mais detalhada do plano.         |
|**Modelo de faturação**     | Há dois modelos de cobrança mostrados aqui, mas você deve escolher **traga sua própria licença** para ofertas de serviços gerenciados. Isso significa que você faturará seus clientes diretamente para custos relacionados a essa oferta, e a Microsoft não cobra nenhuma taxa para você.   |
|**Este é um plano privado?**     | Indica se a SKU é privada ou pública. O padrão é **não** (público). Se você deixar essa seleção, seu plano não será restrito a clientes específicos (ou a um determinado número de clientes); Depois de publicar um plano público, você não poderá alterá-lo posteriormente para privado. Para disponibilizar esse plano somente para clientes específicos, selecione **Sim**. Ao fazer isso, você precisará identificar os clientes fornecendo suas IDs de assinatura. Elas podem ser inseridas uma a uma (para até 10 assinaturas) ou ao carregar um arquivo. csv (para até 20.000 assinaturas). Certifique-se de incluir suas próprias assinaturas aqui para que você possa testar e validar a oferta. Para obter mais informações, consulte [SKUs e planos privados](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> Depois que um plano tiver sido publicado como público, você não poderá alterá-lo para privado. Para controlar quais clientes podem aceitar sua oferta e delegar recursos, use um plano privado. Com um plano público, não é possível restringir a disponibilidade para determinados clientes ou até mesmo para um determinado número de clientes (embora você possa deixar de vender completamente o plano se optar por fazê-lo). Atualmente, não há nenhum mecanismo para rejeitar ou remover delegações depois que um cliente aceita uma oferta, embora você sempre possa entrar em contato com um cliente e pedir que ele [remova seu acesso](view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="manifest-details"></a>Detalhes do manifesto

Preencha a seção de **detalhes do manifesto** para seu plano. Isso cria um manifesto com informações de autorização para gerenciar recursos do cliente. Essas informações são necessárias para habilitar o gerenciamento de recursos delegados do Azure.

> [!NOTE]
> Conforme observado acima, os usuários e as funções em suas entradas de **autorização** serão aplicados a todos os clientes que comprarem o plano. Se você quiser limitar o acesso a um cliente específico, será necessário publicar um plano privado para seu uso exclusivo.

Primeiro, forneça uma **versão** para o manifesto. Use o formato *n. n. n* (por exemplo, 1.2.5).

Em seguida, insira sua **ID de locatário**. Esse é um GUID associado à ID de locatário Azure Active Directory da sua organização (ou seja, o locatário no qual você trabalhará para gerenciar os recursos de seus clientes). Se você não tiver isso à mão, poderá encontrá-lo passando o mouse sobre o nome da sua conta no lado superior direito do portal do Azure ou selecionando o **diretório de comutador**.

Por fim, adicione uma ou mais entradas de **autorização** ao seu plano. As autorizações definem as entidades que podem acessar recursos e assinaturas para clientes que compram o plano e atribuem funções que concedem níveis específicos de acesso. Para obter detalhes sobre as funções com suporte, consulte [locatários, funções e usuários em cenários de Lighthouse do Azure](../concepts/tenants-users-roles.md).

Para cada **autorização**, você precisará fornecer o seguinte. Em seguida, você pode selecionar **nova autorização** quantas vezes forem necessárias para adicionar mais usuários e definições de função.

- **ID de objeto do Azure ad**: o identificador do Azure AD de um usuário, grupo de usuários ou aplicativo ao qual serão concedidas determinadas permissões (conforme descrito pela definição de função) aos recursos dos seus clientes.
- **Nome de exibição do objeto do Azure ad**: um nome amigável para ajudar o cliente a entender a finalidade dessa autorização. O cliente verá esse nome ao delegar recursos.
- **Definição de função**: selecione uma das funções internas do Azure ad disponíveis na lista. Essa função determinará as permissões que o usuário no campo **ID de objeto do Azure ad** terá nos recursos de seus clientes. Para obter descrições dessas funções, consulte [funções internas](../../role-based-access-control/built-in-roles.md) e [suporte de função para o gerenciamento de recursos delegado do Azure](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
- **Funções atribuíveis**: isso será necessário somente se você tiver selecionado administrador de acesso de usuário na **definição de função** para essa autorização. Nesse caso, você deve adicionar uma ou mais funções atribuíveis aqui. O usuário no campo **ID de objeto do Azure ad** poderá atribuir essas **funções atribuíveis** a [identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md), o que é necessário para [implantar políticas que podem ser corrigidas](deploy-policy-remediation.md). Observe que nenhuma outra permissão normalmente associada à função Administrador de acesso do usuário será aplicada a esse usuário. Se você não selecionar uma ou mais funções aqui, seu envio não passará na certificação. (Se você não selecionou administrador de acesso do usuário para a definição de função deste usuário, esse campo não terá efeito.)

> [!TIP]
> Na maioria dos casos, você desejará atribuir permissões a um grupo de usuários ou entidade de serviço do Azure AD, em vez de uma série de contas de usuário individuais. Isso permite que você adicione ou remova o acesso para usuários individuais sem precisar atualizar e publicar o plano novamente quando seus requisitos de acesso forem alterados. Para obter recomendações adicionais, consulte [locatários, funções e usuários em cenários de Lighthouse do Azure](../concepts/tenants-users-roles.md).

Depois de concluir as informações, você poderá selecionar **novo plano** quantas vezes precisar para criar planos adicionais. Quando terminar, selecione **salvar**e continue na seção **Marketplace** .

## <a name="provide-marketplace-text-and-images"></a>Fornecer texto e imagens do Marketplace

A seção **Marketplace** é onde você fornece o texto e as imagens que os clientes verão no Azure Marketplace e o portal do Azure.

Preencha os seguintes campos na seção **visão geral** :

|Campo  |Descrição  |
|---------|---------|
|**Title** (Título)     |  Título da oferta, geralmente o nome longo e formal. Este título será exibido em destaque no Marketplace. Comprimento máximo de 50 caracteres. Na maioria dos casos, isso deve ser o mesmo que o **nome** inserido na seção de **configurações da oferta** .       |
|**Resumo**     | Curto propósito ou função de sua oferta. Isso geralmente é exibido sob o título. Comprimento máximo de 100 caracteres.        |
|**Resumo longo**     | Um resumo mais longo da finalidade ou da função de sua oferta. Comprimento máximo de 256 caracteres.        |
|**Descrição**     | Mais informações sobre sua oferta. Este campo tem um comprimento máximo de 3000 caracteres e dá suporte à formatação HTML simples. Você deve incluir as palavras "serviço gerenciado" ou "serviços gerenciados" em algum lugar na sua descrição.       |
|**Identificador de marketing**     | Um identificador amigável de URL exclusivo. Esse identificador só pode conter caracteres alfanuméricos minúsculos e traços. Ele será usado em URLs do Marketplace para esta oferta. Por exemplo, se sua ID de editor for *contoso* e seu identificador de marketing for *SAMPLEAPP*, a URL para sua oferta no Azure Marketplace será *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp* .        |
|**Visualizar IDs de assinatura**     | Adicione um a 100 identificadores de assinatura. Os clientes associados a essas assinaturas poderão exibir a oferta no Azure Marketplace antes de entrar em tempo real. Sugerimos incluir suas próprias assinaturas aqui para que você possa visualizar como sua oferta aparece no Azure Marketplace antes de disponibilizá-la aos clientes.  (As equipes de suporte e engenharia da Microsoft também serão capazes de exibir sua oferta durante esse período de visualização.)   |
|**Links úteis**     | URLs relacionadas à sua oferta, como documentação, notas de versão, perguntas frequentes etc.        |
|**Categorias sugeridas (máx. 5)**     | Uma ou mais categorias (até cinco) que se aplicam à sua oferta. Essas categorias ajudam os clientes a descobrir sua oferta no Azure Marketplace e no portal do Azure.        |

Na seção **artefatos de marketing** , você pode carregar logotipos e outros ativos para serem mostrados com sua oferta. Opcionalmente, você pode carregar capturas de tela ou links para vídeos que podem ajudar os clientes a entender sua oferta.

São necessários quatro tamanhos de logotipo: **Small (40x40)** , **Medium (90x90)** , **Large (115x115)** e **Wide (255x115)** . Siga estas diretrizes para seus logotipos:

- O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias no logótipo.
- As cores do tema do portal são preto e branco. Não utilize estas cores como cor de fundo do logótipo. Utilize uma cor que realce o logótipo no portal. Recomendamos cores primárias simples.
- Se você usar um plano de fundo transparente, verifique se o logotipo e o texto não são branco, preto ou azul.
- O aspeto e funcionalidade do logótipo deve ser simples e evitar gradações. Não utilize um fundo de gradação no logótipo.
- Não coloque texto no logótipo, nem mesmo o nome da empresa ou da marca.
- Certifique-se que o logótipo não está ampliado.

O logotipo **Hero (815x290)** é opcional, mas recomendado. Se você incluir um logotipo Hero, siga estas diretrizes:

- Não inclua nenhum texto no logotipo Hero e lembre-se de deixar 415 pixels de espaço vazio no lado direito do logotipo. Isso é necessário para deixar espaço para elementos de texto que serão inseridos programaticamente: o nome de exibição do editor, o título do plano, o resumo longo da oferta.
- O segundo plano do seu logotipo Hero não pode ser preto, branco ou transparente. Verifique se a cor do plano de fundo não é muito clara, porque o texto inserido será exibido em branco.
- Depois de publicar sua oferta com um ícone de herói, você não poderá removê-la (embora seja possível atualizá-la com uma versão diferente, se desejar).

Na seção **Gerenciamento de leads** , você pode selecionar o sistema CRM no qual seus leads serão armazenados. Observe que, de acordo com as [políticas de certificação de serviços gerenciados](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), um **destino de Lead** é necessário.

Por fim, forneça a **URL da política de privacidade** e **termos de uso** na seção **legal** . Você também pode especificar aqui se deseja ou não usar o [contrato padrão](../../marketplace/standard-contract.md) para esta oferta.

Lembre-se de salvar as alterações antes de passar para a seção de **suporte** .

## <a name="add-support-info"></a>Adicionar informações de suporte

Na seção de **suporte** , forneça o nome, o email e o número de telefone de um contato de engenharia e um contato de suporte ao cliente. Você também precisará fornecer URLs de suporte. A Microsoft poderá usar essas informações quando precisar entrar em contato com você sobre problemas de negócios e de suporte.

Depois de adicionar essas informações, selecione **salvar.**

## <a name="publish-your-offer"></a>Publicar a sua oferta

Depois de concluir todas as seções, a próxima etapa é publicar a oferta no Azure Marketplace. Selecione o botão **publicar** para iniciar o processo de tornar sua oferta ativa. Para obter mais informações sobre esse processo, consulte [publicar o Azure Marketplace e ofertas do AppSource](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md).

Você pode [publicar uma versão atualizada da sua oferta](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) a qualquer momento. Por exemplo, talvez você queira adicionar uma nova definição de função a uma oferta publicada anteriormente. Quando você fizer isso, os clientes que já tiverem adicionado a oferta verão um ícone na página [**provedores de serviço**](view-manage-service-providers.md) no portal do Azure que lhes permite saber que uma atualização está disponível. Cada cliente poderá [revisar as alterações](view-manage-service-providers.md#update-service-provider-offers) e decidir se deseja atualizar para a nova versão. 

## <a name="the-customer-onboarding-process"></a>O processo de integração do cliente

Depois que um cliente adiciona sua oferta, ele poderá [delegar uma ou mais assinaturas ou grupos de recursos específicos](view-manage-service-providers.md#delegate-resources), que serão integrados ao gerenciamento de recursos delegado do Azure. Se um cliente tiver aceitado uma oferta, mas ainda não tiver delegado nenhum recurso, verá uma observação na parte superior da seção de **ofertas de provedor** da página [**provedores de serviços**](view-manage-service-providers.md) na portal do Azure.

> [!IMPORTANT]
> A delegação deve ser feita por uma conta que não seja de convidado no locatário do cliente que tem a [função interna de proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) para a assinatura que está sendo integrada (ou que contém os grupos de recursos que estão sendo integrados). Para ver todos os usuários que podem delegar a assinatura, um usuário no locatário do cliente pode selecionar a assinatura no portal do Azure, o **iam (Open Access Control)** , [listar todas as funções](../../role-based-access-control/role-definitions-list.md#list-all-roles)e, em seguida, selecionar **proprietário** para ver todos os usuários com essa função.

Depois que o cliente delega uma assinatura (ou um ou mais grupos de recursos em uma assinatura), o provedor de recursos **Microsoft. managedservices** será registrado para essa assinatura e os usuários em seu locatário poderão acessar os recursos delegados de acordo com as autorizações em sua oferta.

> [!NOTE]
> Neste momento, as assinaturas (ou grupos de recursos em uma assinatura) não poderão ser delegadas se a assinatura usar Azure Databricks. Da mesma forma, se uma assinatura (ou grupos de recursos dentro de uma assinatura) já tiver sido delegada, atualmente não será possível criar espaços de trabalho do databricks nessa assinatura.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- [Exiba e gerencie clientes](view-manage-customers.md) acessando **meus clientes** na portal do Azure.

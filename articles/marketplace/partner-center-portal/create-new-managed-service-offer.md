---
title: Criar uma oferta de Serviço Gerido no mercado comercial da Microsoft
description: Como criar uma nova oferta de Serviço Gerido para listagem no Azure Marketplace utilizando o portal Mercado Comercial no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.date: 08/07/2020
ms.openlocfilehash: 34af52b32d9e31ad003c5e95e288f88b157a944a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710622"
---
# <a name="create-a-managed-service-offer"></a>Criar uma oferta de Serviço Gerido

O Serviço Gerido oferece ajuda para ativar cenários [do Farol Azure.](../../lighthouse/overview.md) Quando um cliente aceita uma oferta de Serviço Gerido, pode então embarcar recursos para [a gestão de recursos delegados da Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md) Antes de iniciar, [crie uma conta de Mercado Comercial no Partner Center](create-account.md) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

Você deve ter um [nível](https://partner.microsoft.com/membership/cloud-platform-competency) de competência silver ou gold cloud platform ou ser um [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de Serviço Gerido.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página 'Vista Geral', selecione + Serviço Gerido **de Nova Oferta**  >  **Managed Service**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-managed-service.png)

>[!NOTE]
>Após a publicação de uma oferta, as edições feitas no Partner Center só aparecem nas lojas online depois de republicarem a oferta. Certifique-se de que é sempre republicante depois de escorção.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

* Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos de Gestor de Recursos Azure, se aplicável.
* Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir **a oferta de teste-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
* O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

* Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
* O pseudónimo Oferta não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

### <a name="customer-leads"></a>Ligações ao cliente

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

De acordo com as [políticas de certificação de Serviços Geridos,](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)é necessário um **Destino Líder.** Isto criará um registo no seu sistema CRM sempre que um cliente implementar a sua oferta.

Para obter mais informações, consulte [a visão geral da gestão do chumbo.](./commercial-marketplace-get-customer-leads.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias utilizadas para agrupar a sua oferta no mercado e os contratos legais que suportam a sua oferta.

### <a name="category"></a>Categoria

Selecione um mínimo de uma e um máximo de cinco categorias que serão usadas para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Certifique-se de chamar a atenção para o suporte da sua oferta nestas categorias na descrição da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça os seus próprios termos e condições legais no campo **termos e condições.** Também pode fornecer o URL onde os seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

**Selecione Guardar o projeto** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página permite definir detalhes do mercado (como nome de oferta, descrição e imagens) para a sua oferta.

> [!NOTE]
> A oferta de conteúdo de listagem (como a descrição, documentos, screenshots e termos de utilização) não é necessária para estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um *URL de Link Útil* para oferecer conteúdo em um idioma diferente do utilizado no conteúdo da listagem de Oferta.

Aqui está um exemplo de como a oferta de informação aparece no portal Azure:

:::image type="content" source="media/example-managed-services.png" alt-text="Ilustra como esta oferta aparece no portal Azure.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Título
2. Descrição
3. Ligações úteis
4. Capturas de ecrã

### <a name="name"></a>Nome

O nome que introduzir aqui será mostrado aos clientes como o título da sua listagem de oferta. Este campo é pré-povoado com o texto que inseriu para **o pseudónimo Oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser comercializado (e pode incluir símbolos de marca registada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta (até 100 caracteres), que pode ser usada em resultados de pesquisa no mercado.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta (até 256 caracteres). Este longo resumo também pode ser usado em resultados de pesquisa no mercado.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="privacy-policy-link"></a>Ligação de política de privacidade

Insira o URL para a política de privacidade da sua organização (hospedada no seu site). Você é responsável por garantir que a sua aplicação está em conformidade com as leis e regulamentos de privacidade, e por fornecer uma política de privacidade válida.

### <a name="useful-links"></a>Ligações úteis

Forneça documentos online suplementares opcionais sobre a sua solução. Adicione links úteis adicionais clicando **+ Adicione um link.**

### <a name="contact-information"></a>Informações de Contacto

Nesta secção, deve fornecer o nome, e-mail e número de telefone para um **contacto de Suporte** e um contacto de **Engenharia**. Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft, e poderá ser fornecida aos parceiros da CSP.

### <a name="support-urls"></a>UrLs de apoio

Se tiver websites de suporte para **clientes Azure Global** e/ou **clientes do Governo Azure,** forneça esses URLs aqui.

### <a name="marketplace-images"></a>Imagens do mercado

Nesta secção, pode fornecer logotipos e imagens que serão utilizados ao mostrar a sua oferta ao cliente. Todas as imagens devem estar em formato .png.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da loja

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center utilizará isto para criar um logótipo **Pequeno** e **Médio.** Pode substituir opcionalmente estas imagens por diferentes imagens mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, necessário)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Estes logótipos são utilizados em diferentes locais da listagem:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de ecrã

Adicione cinco imagens que mostram como a sua oferta funciona. Todas as imagens devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Pode, opcionalmente, adicionar até cinco vídeos que demonstrem a sua oferta. Estes vídeos devem ser apresentados no YouTube e/ou Vimeo. Para cada um, insira o nome do vídeo, o seu URL e uma imagem miniatura do vídeo (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Recursos de listagem de mercado adicionais

- [Melhores práticas para anúncios de oferta de mercado](../gtm-offer-listing-best-practices.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="preview"></a>Pré-visualizar

Antes de publicar a sua oferta ao vivo para a oferta de mercado mais ampla, primeiro terá de a disponibilizar a um público de pré-visualização limitado. Isto permite-lhe confirmar a forma como a oferta aparece no Azure Marketplace antes de a disponibilizar aos clientes. As equipas de suporte e engenharia da Microsoft também poderão ver a sua oferta durante este período de pré-visualização.

Pode definir o público de pré-visualização introduzindo IDs de subscrição Azure na secção **Preview Audience.** Pode introduzir até 10 IDs de subscrição manualmente ou carregar um ficheiro .csv com até 100 IDs de subscrição.

Qualquer cliente associado a estas subscrições poderá ver a oferta no Azure Marketplace antes de entrar em funcionamento. Certifique-se de incluir as suas próprias subscrições aqui para que possa pré-visualizar a sua oferta.

**Selecione Guardar o projeto** antes de continuar.

## <a name="plan-overview"></a>Visão geral do plano

Cada oferta deve ter um ou mais planos (anteriormente chamados SKUs). Pode adicionar vários planos para suportar diferentes conjuntos de funcionalidades a diferentes preços ou para personalizar um plano específico para um público limitado de clientes específicos. Os clientes podem ver os planos que estão disponíveis ao abrigo da oferta dos pais.

Pode criar até 100 planos para cada oferta: até 45 destes podem ser privados. Saiba mais sobre [planos privados em ofertas privadas no mercado comercial da Microsoft.](../private-offers.md)

Na página **de visão geral** do Plano, selecione + Criar novo **plano**. Em seguida, insira um **ID do plano** e um **nome de plano**. Ambos os valores só podem conter caracteres alfanuméricos minúsculos, traços e sublinhados, com um máximo de 50 caracteres. Estes valores podem ser visíveis para os clientes, e não podem ser alterados após a publicação da oferta.

**Selecione Criar** uma vez que tenha introduzido estes valores para continuar a trabalhar no seu plano. Há três secções a completar: **Listagem de planos,** **preços e disponibilidade,** e **configuração técnica.**

### <a name="plan-listing"></a>Listagem de planos

Primeiro, forneça um **resumo dos resultados** da pesquisa para o plano. Esta é uma breve descrição do seu plano (até 100 caracteres), que pode ser usado em resultados de pesquisa no mercado.

Em seguida, insira uma **Descrição** que fornece uma explicação mais detalhada do plano.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Atualmente, existe apenas um modelo de preços que pode ser usado para a oferta de Serviço Gerido: **Traga a sua própria licença (BYOL)**. Isto significa que irá cobrar diretamente aos seus clientes pelos custos relacionados com esta oferta, e a Microsoft não cobra quaisquer taxas a si.

A secção **de visibilidade do Plano** permite-lhe indicar se este plano deve ser [privado](../../marketplace/private-offers.md). Se sair da Esta é uma caixa **de plano privado** sem controlo, o seu plano não será restringido a clientes específicos (ou a um determinado número de clientes).

> [!NOTE]
> Os planos privados não são suportados com subscrições estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

Para disponibilizar este plano apenas a clientes específicos, selecione **Sim.** Quando o fizer, terá de identificar os clientes fornecendo os seus IDs de subscrição. Estes podem ser introduzidos um a um (para até 10 subscrições) ou através do upload de um ficheiro .csv (para um máximo de 10.000 subscrições em todos os planos). Certifique-se de incluir as suas próprias subscrições aqui para que possa testar e validar a oferta.

> [!IMPORTANT]
> Uma vez que um plano tenha sido publicado como público, não se pode mudá-lo para privado. Para controlar quais os clientes que podem aceitar a sua oferta e delegar recursos, utilize um plano privado. Com um plano público, não é possível restringir a disponibilidade a determinados clientes ou mesmo a um determinado número de clientes (embora possa parar de vender completamente o plano se optar por fazê-lo). Pode [remover o acesso a uma delegação](../../lighthouse/how-to/remove-delegation.md) depois de um cliente aceitar uma oferta apenas se tiver incluído uma **Autorização** com a **Definição de Função** definida para [Função de Atribuição de Registo de Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Quando publicou a oferta. Também pode contactar o cliente e pedir-lhe que [remova o seu acesso.](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

### <a name="technical-configuration"></a>Configuração técnica

Esta secção do seu plano cria um manifesto com informação de autorização para gerir os recursos dos clientes. Estas informações são necessárias para permitir a [gestão de recursos delegados do Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md)

Certifique-se de rever [inquilinos, papéis e utilizadores em cenários do Farol Azure](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) para entender quais as funções que são suportadas e as melhores práticas para definir as suas autorizações.

> [!NOTE]
> Como referido acima, os utilizadores e funções nas suas entradas de **Autorização** aplicar-se-ão a todos os clientes que adquirirem o plano. Se quiser limitar o acesso a um cliente específico, terá de publicar um plano privado para uso exclusivo.

#### <a name="manifest"></a>Manifesto

Primeiro, forneça uma **versão** para o manifesto. Utilize o formato *n.n.n* (por exemplo, 1.2.5).

Em seguida, insira o seu **ID do inquilino.** Este é um GUID associado ao Azure Ative Directory (Azure AD) ID do inquilino da sua organização; ou seja, o inquilino gerente a partir do qual você vai aceder aos recursos dos seus clientes. Se não tiver este útil, pode encontrá-lo pairando sobre o nome da sua conta no lado superior direito do portal Azure, ou selecionando o **diretório da Switch**.

Se publicar uma nova versão da sua oferta e precisar de criar um manifesto atualizado, selecione **+ Novo manifesto**. Certifique-se de aumentar o número de versão da versão anterior do manifesto.

#### <a name="authorization"></a>Autorização

As autorizações definem as entidades do seu inquilino gerente que podem aceder a recursos e subscrições para clientes que adquirem o plano. Cada uma destas entidades tem um papel incorporado que concede níveis específicos de acesso.

Pode criar até vinte autorizações para cada plano.

> [!TIP]
> Na maioria dos casos, irá querer atribuir funções a um grupo de utilizadores ou ao serviço principal do Azure, em vez de uma série de contas individuais de utilizador. Isto permite-lhe adicionar ou remover o acesso a utilizadores individuais sem ter de atualizar e reeditar o plano quando os seus requisitos de acesso mudarem. Ao atribuir funções a grupos AD Azure, [certifique-se de que o **tipo de grupo** é a **Segurança** e não o **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Para recomendações adicionais, consulte [Inquilinos, funções e utilizadores em cenários do Farol Azure.](../../lighthouse/concepts/tenants-users-roles.md)

Para cada **Autorização,** terá de fornecer o seguinte. Pode então selecionar **+ Adicionar a autorização** quantas vezes for necessário para adicionar mais utilizadores e definições de função.

- **ID do objeto Azure :** O identificador Azure AD de um utilizador, grupo de utilizadores ou aplicação que será concedida determinadas permissões (conforme definido pela Definição de Função) aos recursos dos seus clientes.
- **Nome de visualização de objeto Ad Azure**: Um nome amigável para ajudar o cliente a compreender o propósito desta autorização. O cliente verá este nome ao delegar recursos.
- **Definição de Função**: Selecione uma das funções incorporadas AZure AD disponíveis da lista. Esta função determinará as permissões que o utilizador no campo **Azure AD Object ID** terá nos recursos dos seus clientes. Para descrições destes papéis, consulte [funções incorporadas](../../role-based-access-control/built-in-roles.md) e [suporte de função para o Farol de Azure.](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)
  > [!NOTE]
  > Como as novas funções incorporadas aplicáveis são adicionadas ao Azure, elas ficarão disponíveis aqui, embora possa haver algum atraso antes de aparecerem.
- **Funções atribuíveis**: Esta opção só aparecerá se tiver selecionado o Administrador de Acesso ao Utilizador na **Definição de Função** para esta autorização. Em caso afirmativo, deve adicionar uma ou mais funções atribuíveis aqui. O utilizador no campo **Azure AD Object ID** poderá atribuir estas funções a [identidades geridas,](../../active-directory/managed-identities-azure-resources/overview.md)o que é necessário para [implementar políticas que possam ser remediadas.](../../lighthouse/how-to/deploy-policy-remediation.md) Note que nenhuma outra permissão normalmente associada à função de Administrador de Acesso ao Utilizador será aplicada a este utilizador.

> [!TIP]
> Para garantir que pode [remover o acesso a uma delegação,](../../lighthouse/how-to/remove-delegation.md) se necessário, inclua uma **Autorização** com a **Definição de Função** definida para [Atribuição de Registo de Serviços Geridos Eliminar Função](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Se esta função não for atribuída, os recursos delegados só podem ser removidos por um utilizador no arrendatário do cliente.

Uma vez concluídas todas as secções para o seu plano, pode selecionar **+ Criar um novo plano** quantas vezes precisar para criar planos adicionais. Quando tiver terminado, selecione **Guardar**.

**Selecione Guardar o projeto** antes de continuar.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Uma vez concluídas todas as secções necessárias da oferta, selecione **'Rever' e publicar** no canto superior direito do portal.

Se é a sua primeira vez publicando esta oferta, você pode:

- Consulte o estado de conclusão de cada secção da oferta.
  - **Não foi iniciado** - A secção não foi tocada e precisa de ser concluída.
  - **Incompleto** - A secção tem erros que precisam de ser corrigidos ou exigem mais informações a serem fornecidas. Volte para a secção(s) e atualize-a.
  - **Completo** - A secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado completo antes de poder submeter a oferta.
- Na secção **Notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação seja testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta de publicação selecionando Enviar por ela que **não se possa fazer uma publicação.** Enviaremos um e-mail quando estiver disponível uma versão de pré-visualização da oferta para que possa rever e aprovar. Volte ao Partner Center e selecione **Go-live** para a oferta de publicar a sua oferta ao público (ou se uma oferta privada, para o público privado).

### <a name="customer-experience-and-offer-management"></a>Experiência do cliente e gestão de ofertas

Quando um cliente implementar a sua oferta, poderá delegar subscrições ou grupos de recursos para [a gestão de recursos delegados da Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md) Para mais informações sobre este processo, consulte [o processo de embarque do cliente.](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)

Pode [publicar uma versão atualizada da sua oferta a](update-existing-offer.md) qualquer momento. Por exemplo, pode querer adicionar uma nova definição de papel a uma oferta publicada anteriormente. Quando o fizer, os clientes que já adicionaram a oferta verão um ícone na página de fornecedores de [**Serviços**](../../lighthouse/how-to/view-manage-service-providers.md) no portal Azure que lhes permite saber que uma atualização está disponível. Cada cliente poderá rever as alterações e decidir se pretende atualizar para a nova versão.

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)
- [Saiba mais sobre o Farol de Azure](../../lighthouse/overview.md)

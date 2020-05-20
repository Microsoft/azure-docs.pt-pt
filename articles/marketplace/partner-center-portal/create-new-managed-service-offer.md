---
title: Criar uma oferta de Serviço Gerido no mercado comercial da Microsoft
description: Como criar uma nova oferta de Serviço gerido para listagem no Azure Marketplace utilizando o portal Mercado Comercial no Partner Center.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 3d8806e34afe45e6cced3de4554b7dd365f4ddf9
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701783"
---
# <a name="create-a-managed-service-offer"></a>Criar uma oferta de Serviço Gerido

O Serviço Gerido oferece ajuda para ativar cenários do [Farol Azure.](../../lighthouse/overview.md) Quando um cliente aceita uma oferta de Serviço Gerido, eles são então capazes de embarcar recursos para a [gestão de recursos delegados do Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md) Antes de começar, [Crie uma conta de Marketplace Comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

Você deve ter um [nível](https://partner.microsoft.com/membership/cloud-platform-competency) de competência silver ou gold cloud platform ou ser um MSP Especialista [Azure](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de Serviço Gerido.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página 'Visão Geral', selecione **+ Novo Serviço**  >  **Gerido**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-managed-service.png)

>[!NOTE]
>Depois de publicada uma oferta, as edificações feitas no Partner Center só aparecem nas montras depois de reeditarem a oferta. Certifique-se de que republique sempre depois de fazer alterações.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

* Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos do Gestor de Recursos Azure, se aplicável.
* Utilize apenas letras minúsculas e números. Pode incluir hífenes e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir o **test-offer-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
* O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira um **pseudónimo da Oferta.** Este é o nome usado para a oferta no Partner Center.

* Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
* Th Offer alias não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

## <a name="connect-lead-management"></a>Ligar a gestão de chumbo

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

De acordo com as políticas de certificação de [Serviços Geridos,](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)é necessário um **Destino Principal.** Isto criará um registo no seu sistema CRM sempre que um cliente implementa a sua oferta.

Para mais informações, consulte a visão geral da [gestão de chumbo](./commercial-marketplace-get-customer-leads.md).

Selecione **guardar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias utilizadas para agrupar a sua oferta no mercado e os contratos legais que suportam a sua oferta.

### <a name="category"></a>Categoria

Selecione um mínimo de uma e um máximo de cinco categorias que serão usadas para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Certifique-se de que a sua oferta suporta estas categorias na descrição da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça os seus próprios termos e condições legais no campo **Termos e Condições.** Também pode fornecer o URL onde os seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

Selecione **guardar rascunho** antes de continuar.

## <a name="offer-listing"></a>Oferta listagem

Esta página permite definir detalhes do mercado (como nome, descrição e imagens de oferta) para a sua oferta.

> [!NOTE]
> Oferecer conteúdo de listagem (como a descrição, documentos, imagens e termos de utilização) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um URL de *Link Útil* para oferecer conteúdo num idioma diferente do utilizado no conteúdo de listagem de oferta.

### <a name="name"></a>Name

O nome que introduzir aqui será mostrado aos clientes como título da sua oferta. Este campo é pré-povoado com o texto que inscreveu para **o pseudónimo oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser marcado por marca (e pode incluir símbolos de marca ou direitos de autor). O nome não pode ser mais de 50 caracteres e não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta (até 100 caracteres), que pode ser usada em resultados de pesquisa de mercado.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta (até 256 caracteres). Este longo resumo também pode ser utilizado nos resultados da pesquisa no mercado.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="privacy-policy-link"></a>Ligação política de privacidade

Insira o URL na política de privacidade da sua organização (hospedada no seu site). É responsável por garantir que a sua aplicação cumpre as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="useful-links"></a>Ligações úteis

Forneça documentos online suplementares opcionais sobre a sua solução. Adicione links úteis adicionais clicando **+ Adicione um link**.

### <a name="contact-information"></a>Informações de Contacto

Nesta secção, deve fornecer o nome, e-mail e número de telefone para um contacto de **suporte** e um contacto de **Engenharia**. Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft, podendo ser fornecida aos parceiros CSP.

### <a name="support-urls"></a>URLs de suporte

Se tiver websites de suporte para **clientes Azure Global Customers** e/ou **Azure Government,** forneça esses URLs aqui.

### <a name="marketplace-images"></a>Imagens do mercado

Nesta secção, pode fornecer logótipos e imagens que serão utilizados ao mostrar a sua oferta ao cliente. Todas as imagens devem estar em formato .png.

>[!NOTE]
>Se tiver algum problema de envio de ficheiros, certifique-se de que a sua rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="marketplace-logos"></a>Logotipos do mercado

Forneça o logótipo da sua oferta em quatro tamanhos de pixel:

- **Pequeno** (40 x 40)
- **Médio** (90 x 90)
- **Grande** (115 x 115)
- **Ampla** (255 x 115)

Siga estas orientações para os seus logotipos:

- O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias no logótipo.
- As cores do tema do portal são preto e branco. Não utilize estas cores como cor de fundo do logótipo. Utilize uma cor que realce o logótipo no portal. Recomendamos cores primárias simples.
- Se utilizar um fundo transparente, certifique-se de que o logótipo e o texto não são brancos, pretos ou azuis.
- O aspeto e funcionalidade do logótipo deve ser simples e evitar gradações. Não utilize um fundo de gradação no logótipo.
- Não coloque texto no logótipo, nem mesmo o nome da empresa ou da marca.
- Certifique-se que o logótipo não está ampliado.

#### <a name="screenshots"></a>Capturas de ecrã

Adicione cinco imagens que mostram como a sua oferta funciona. Todas as imagens devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Pode adicionar opcionalmente cinco vídeos que demonstram a sua oferta. Estes vídeos devem ser hospedados no YouTube e/ou vimeo. Para cada um, introduza o nome do vídeo, o seu URL e uma imagem de miniatura do vídeo (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de mercado

- [Boas práticas para anúncios de oferta de mercado](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Selecione **guardar rascunho** antes de continuar.

## <a name="preview"></a>Pré-visualizar

Antes de publicar a sua oferta ao vivo para a oferta de mercado mais ampla, você primeiro terá que disponibilizá-la a um público de pré-visualização limitado. Isto permite-lhe confirmar como a sua oferta aparece no Mercado Azure antes de a disponibilizar aos clientes. As equipas de suporte e engenharia da Microsoft também poderão ver a sua oferta durante este período de pré-visualização.

Pode definir o público de pré-visualização introduzindo iDs de subscrição azure na secção **Preview Audience.** Pode introduzir até 10 IDs de subscrição manualmente ou carregar um ficheiro .csv com até 100 IDs de subscrição.

Quaisquer clientes associados a estas subscrições poderão ver a oferta no Azure Marketplace antes de entrar em funcionação. Certifique-se de incluir as suas próprias subscrições aqui para que possa fazer a pré-visualização da sua oferta.

Selecione **guardar rascunho** antes de continuar.

## <a name="plan-overview"></a>Visão geral do plano

Cada oferta deve ter um ou mais planos (por vezes referidos como SKUs). Você pode adicionar vários planos para suportar diferentes conjuntos de funcionalidades a diferentes preços ou para personalizar um plano específico para um público limitado de clientes específicos. Os clientes podem ver os planos que lhes estão disponíveis ao abrigo da oferta-mãe.

Na página de visão geral do **Plano,** selecione **+ Crie um novo plano**. Em seguida, introduza uma **identificação** do plano e um nome de **plano.** Ambos os valores só podem conter caracteres alfanuméricos minúsculos, traços e sublinhados, com um máximo de 50 caracteres. Estes valores podem ser visíveis para os clientes, e não podem ser alterados depois de publicar a oferta.

Selecione **Criar** uma vez que tenha entrado nestes valores para continuar a trabalhar no seu plano. Existem três secções a concluir: **Listagem de planos,** **preços e disponibilidade,** e **configuração técnica.**

### <a name="plan-listing"></a>Listagem de planos

Primeiro, forneça um resumo dos **resultados** da pesquisa para o plano. Esta é uma descrição curta do seu plano (até 100 caracteres), que pode ser usado em resultados de pesquisa de mercado.

Em seguida, insira uma **Descrição** que forneça uma explicação mais detalhada do plano.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Atualmente, existe apenas um modelo de preços que pode ser usado para oferta de Serviço Gerido: **Traga a sua própria licença (BYOL)**. Isto significa que irá cobrar diretamente aos seus clientes os custos relacionados com esta oferta, e a Microsoft não cobra quaisquer taxas a si.

A secção de **visibilidade do Plano** permite-lhe indicar se este plano deve ser [privado](../../marketplace/private-offers.md). Se deixar a **Caixa de Plano Privado** desmarcada, o seu plano não se restringirá a clientes específicos (ou a um certo número de clientes).

Para disponibilizar este plano apenas a clientes específicos, selecione **Sim**. Quando o fizer, terá de identificar os clientes fornecendo as suas iDs de subscrição. Estes podem ser introduzidos um a um (para até 10 assinaturas) ou através do upload de um ficheiro .csv (para um máximo de 10.000 subscrições em todos os planos). Certifique-se de incluir as suas próprias subscrições aqui para que possa testar e validar a oferta.

> [!IMPORTANT]
> Uma vez que um plano é publicado como público, não pode mudá-lo para privado. Para controlar quais os clientes que podem aceitar a sua oferta e delegar recursos, utilize um plano privado. Com um plano público, não é possível restringir a disponibilidade a determinados clientes ou mesmo a um certo número de clientes (embora possa deixar de vender o plano completamente se optar por fazê-lo). Só pode [remover o acesso a uma delegação](../../lighthouse/how-to/remove-delegation.md) depois de um cliente aceitar uma oferta apenas se incluir uma **Autorização** com a Definição de **Funções** definida para a Atribuição de Inscrição de [Serviços Geridos Eliminar funções](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando publicou a oferta. Também pode contactar o cliente e pedir-lhe que [remova o seu acesso](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Configuração técnica

Esta secção do seu plano cria um manifesto com informações de autorização para gestão de recursos do cliente. Estas informações são necessárias para permitir a [gestão de recursos delegados do Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md)

Certifique-se de rever [Inquilinos, papéis e utilizadores em cenários](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) do Farol Azure para entender quais as funções que são suportadas e as melhores práticas para definir as suas autorizações.

> [!NOTE]
> Como referido acima, os utilizadores e funções nas suas entradas de **Autorização** aplicar-se-ão a todos os clientes que adquirirem o plano. Se quiser limitar o acesso a um cliente específico, terá de publicar um plano privado para o seu uso exclusivo.

#### <a name="manifest"></a>Manifesto

Primeiro, forneça uma **versão** para o manifesto. Utilize o formato *n.n.n* (por exemplo, 1.2.5).

Em seguida, insira a sua **identificação de inquilino.** Este é um GUID associado ao Id de inquilino azure Ative (Azure AD) da sua organização; ou seja, o inquilino gerente a partir do qual acederá aos recursos dos seus clientes. Se não tiver isso à mão, pode encontrá-lo pairando sobre o nome da sua conta no lado superior direito do portal Azure, ou selecionando **o diretório Switch**.

Se publicar uma nova versão da sua oferta e precisar de criar um manifesto atualizado, **selecione + Novo manifesto**. Certifique-se de aumentar o número da versão da versão anterior.

#### <a name="authorization"></a>Autorização

As autorizações definem as entidades do seu inquilino gestor que podem aceder a recursos e subscrições para clientes que adquirem o plano. A cada uma destas entidades é atribuída a cada uma destas entidades um papel integrado que concede níveis específicos de acesso.

Pode criar até vinte autorizações para cada plano.

> [!TIP]
> Na maioria dos casos, pretende atribuir funções a um grupo de utilizadores ou prestador de serviços Da AD Azure, em vez de uma série de contas individuais de utilizadores. Isto permite adicionar ou remover o acesso a utilizadores individuais sem ter de atualizar e republicar o plano quando os seus requisitos de acesso mudarem. Ao atribuir funções aos grupos AD Azure, [certifique-se de que o tipo de **Grupo** é **Segurança** e não **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Para recomendações adicionais, consulte [Inquilinos, funções e utilizadores em cenários do Farol Azure.](../../lighthouse/concepts/tenants-users-roles.md)

Para cada **Autorização,** terá de fornecer o seguinte. Em seguida, pode selecionar **+ Adicionar autorização** quantas vezes for em função do necessário para adicionar mais utilizadores e definições de papéis.

- **ID de objeto ad ad -** O identificador Azure AD de um utilizador, grupo de utilizadores ou aplicação que será concedida certas permissões (conforme definido pela Definição de Funções) aos recursos dos seus clientes.
- Nome de **exibição de objeto saque a Azure AD**: Um nome amigável para ajudar o cliente a compreender o propósito desta autorização. O cliente verá este nome ao delegar recursos.
- **Definição de Funções**: Selecione uma das funções integradas do Azure AD disponível na lista. Esta função determinará as permissões que o utilizador no campo DeId de **Objeto AD Azure** terá nos recursos dos seus clientes. Para descrições destas funções, consulte [funções incorporadas](../../role-based-access-control/built-in-roles.md) e [suporte a papéis para a gestão de recursos delegados do Azure.](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)
  > [!NOTE]
  > À medida que forem adicionadas novas funções incorporadas ao Azure, estarão disponíveis aqui, embora possa haver algum atraso antes de aparecerem.
- **Funções atribuídas**: Esta opção só aparecerá se tiver selecionado administrador de acesso ao utilizador na Definição de **Funções** para esta autorização. Em caso afirmativo, deve adicionar aqui um ou mais papéis atribuíveis. O utilizador no campo **DeIdentificação de Objetos AD Azure** poderá atribuir estas funções a [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md), o que é necessário para [implementar políticas que possam ser remediadas.](../../lighthouse/how-to/deploy-policy-remediation.md) Note que nenhuma outra permissão normalmente associada à função de Administrador de Acesso ao Utilizador será aplicada a este utilizador.

> [!TIP]
> Para garantir que pode [remover o acesso a uma delegação,](../../lighthouse/how-to/remove-delegation.md) se necessário, inclua uma **Autorização** com a Definição de **Funções** definida para a [Atribuição de Inscrição de Serviços Geridos .](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Se esta função não for atribuída, os recursos delegados só podem ser removidos por um utilizador no inquilino do cliente.

Depois de ter concluído todas as secções para o seu plano, pode selecionar **+ Criar um novo plano** quantas vezes for necessário para criar planos adicionais. Quando tiver terminado, selecione **Guardar**.

Selecione **guardar rascunho** antes de continuar.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Depois de ter concluído todas as secções necessárias da oferta, selecione **Rever e publique** no canto superior direito do portal.

Se for a primeira vez que publica esta oferta, pode:

- Consulte o estado de conclusão de cada secção da oferta.
  - **Não começou** - A secção não foi tocada e precisa de ser concluída.
  - **Incompleto** - A secção tem erros que precisam de ser corrigidos ou exigemais informações a fornecer. Volte para a secção(s) e atualize-a.
  - **Completo** - A secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado total antes de poder submeter a oferta.
- Na secção **Notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta para publicação selecionando **Enviar**. Enviaremos um e-mail quando estiver disponível uma versão de pré-visualização da oferta para que possa rever e aprovar. Volte ao Partner Center e selecione **Go-live** para a oferta de publicar a sua oferta ao público (ou se uma oferta privada, para o público privado).

### <a name="customer-experience-and-offer-management"></a>Experiência do cliente e gestão de ofertas

Quando um cliente implementa a sua oferta, poderá delegar subscrições ou grupos de recursos para a [gestão de recursos delegados do Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md) Para mais informações sobre este processo, consulte o processo de [embarque do cliente.](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)

Pode [publicar uma versão atualizada da sua oferta](update-existing-offer.md) a qualquer momento. Por exemplo, pode querer adicionar uma nova definição de papel a uma oferta anteriormente publicada. Quando o fizer, os clientes que já adicionaram a oferta verão um ícone na página de fornecedores de [**Serviços**](../../lighthouse/how-to/view-manage-service-providers.md) no portal Azure que os permite saber que está disponível uma atualização. Cada cliente poderá rever as alterações e decidir se pretende atualizar para a nova versão.

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)
- [Conheça o Farol de Azure](../../lighthouse/overview.md)

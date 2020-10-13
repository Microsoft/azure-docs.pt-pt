---
title: Criar uma oferta de contentores Azure - Azure Marketplace
description: Saiba como criar e publicar uma oferta de contentores ao Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: cbaca0b9d608d7703beec559e5434f52308629ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709109"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Criar uma oferta de contentor do Azure no Azure Marketplace

Este artigo descreve como criar e publicar uma oferta de contentores para o Azure Marketplace. Antes de iniciar, [crie uma conta de Mercado Comercial no Partner Center](create-account.md) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.

3. Na página 'Vista Geral', selecione **+ Novo**recipiente  >  **Azure.**

   ![Ilustra o menu de navegação à esquerda.](./media/new-offer-azure-container.png)

> [!TIP]
> Após a publicação de uma oferta, as edições feitas no Partner Center só aparecem nas lojas online depois de republicarem a oferta. Certifique-se de que é sempre republicante depois de escorção.

### <a name="offer-id-and-alias"></a>ID de oferta e pseudónimo

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos de Gestor de Recursos Azure, se aplicável.
- Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir **a oferta de teste-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- Isto não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-overview"></a>Oferta geral

A página **geral da Oferta** mostra uma representação visual dos passos necessários para publicar esta oferta (concluída e futura) e quanto tempo cada passo deve demorar a ser concluído.

Esta página mostra diferentes links com base no estado atual da oferta. Por exemplo:

- Se a oferta for um rascunho - [Eliminar oferta de rascunho](update-existing-offer.md#delete-a-draft-offer)
- Se a oferta for ao vivo - [Pare de vender a oferta](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Se a oferta estiver em pré-visualização - [Go-live](publishing-status.md#publisher-approval)
- Se ainda não completou a assinatura da editora - [Cancele a publicação](update-existing-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Configuração de oferta

Siga estes passos para definir a sua oferta.

### <a name="customer-leads--optional"></a>Leads do cliente – opcional

Ao publicar a sua oferta no mercado comercial com o Partner Center, pode conectá-la ao seu sistema de Gestão de Relacionamento com o Cliente (CRM). Isto permite-lhe receber informações de contacto do cliente assim que alguém manifestar interesse ou utilizar o seu produto.

1. **Selecione um destino de chumbo para enviar pistas de clientes.** O Partner Center suporta os seguintes sistemas crm:

   - [Dinâmica 365](commercial-marketplace-lead-management-instructions-dynamics.md) para Envolvimento com o Cliente
   - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Se o seu sistema CRM não estiver listado acima, utilize [a Tabela Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou [o Ponto Final https](commercial-marketplace-lead-management-instructions-https.md) para armazenar dados de chumbo do cliente e exporte os dados para o seu sistema CRM.

2. Ligue a sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme que a ligação ao destino de chumbo está corretamente configurada. Depois de publicá-lo no Partner Center, validaremos a ligação e enviaremos um teste. Enquanto pré-visualiza a oferta antes de entrar em direto, também pode testar a sua ligação de chumbo tentando comprar a oferta no ambiente de pré-visualização.
4. Certifique-se de que a ligação ao destino principal permanece atualizada para não perder nenhuma pista.

Aqui estão alguns recursos adicionais de gestão de chumbo:

- [O cliente lidera a partir da sua oferta de mercado comercial](commercial-marketplace-get-customer-leads.md)
- [Questões comuns sobre gestão de chumbo](../lead-management-faq.md#common-questions-about-lead-management)
- [Erros de configuração de chumbo de resolução de problemas](../lead-management-faq.md#publishing-config-errors)
- [Visão geral da gestão de chumbo](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Certifique-se de que o seu bloqueador pop-up está desligado).

**Selecione Guardar o projeto** antes de continuar.

### <a name="properties"></a>Propriedades

Esta página permite definir as categorias utilizadas para agrupar a sua oferta no mercado e os contratos legais que suportam a sua oferta.

#### <a name="category"></a>Categoria

Selecione categorias e subcategorias para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Não se esqueça de descrever como a sua oferta suporta estas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se não for aplicável nenhuma subcategoria à sua oferta, selecione **Não aplicável**.

Consulte a lista completa de categorias e subcategorias na [Listagem de Ofertas Boas Práticas.](../gtm-offer-listing-best-practices.md) Os recipientes aparecem sempre sob **os contentores** e, em seguida, na categoria **Imagens de Contentores.**

#### <a name="legal"></a>Legal

Deve fornecer termos e condições para a oferta. Existem duas opções:

- Utilize o Contrato Padrão para o mercado comercial da Microsoft.
- Forneça os seus próprios termos e condições.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato padrão para o mercado comercial da Microsoft

Oferecemos um modelo de Contrato Padrão para ajudar a facilitar transações no mercado comercial. Pode optar por oferecer a sua solução ao abrigo do Contrato Padrão, que os clientes só precisam de verificar e aceitar uma vez. Esta é uma boa opção se não quiser criar termos e condições personalizados.

Para saber mais sobre o Contrato Padrão, consulte o [Contrato Standard para o mercado comercial da Microsoft.](../standard-contract.md) Também pode descarregar o PDF [Standard Contract](https://go.microsoft.com/fwlink/?linkid=2041178) (certifique-se de que o seu bloqueador pop-up está desligado).

Para utilizar o Contrato Padrão, selecione o contrato padrão para o mercado comercial da Microsoft.... /standard-contract.md)

> [!NOTE]
> Depois de publicar uma oferta utilizando o contrato Standard para o mercado comercial da Microsoft, não pode usar os seus próprios termos e condições personalizados. Ou oferece a sua solução nos termos do Contrato Padrão ou nos seus próprios termos e condições.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Seus próprios termos e condições

Para fornecer os seus próprios termos e condições personalizados, insira-os na caixa **de Termos e Condições.** Pode introduzir um número ilimitado de caracteres de texto nesta caixa. Os clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

**Selecione Guardar o rascunho** antes de continuar para a secção seguinte, Ofereça a listagem.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página permite definir os detalhes da oferta que são exibidos no mercado comercial. Isto inclui o nome da oferta, descrição e imagens.

> [!NOTE]
> Os detalhes da oferta não são necessários para estar em inglês se a descrição da oferta começar com a frase"Esta aplicação está disponível apenas em [língua não inglesa]." Também é normal fornecer um Link Útil para oferecer conteúdo num idioma diferente do usado na listagem de detalhes da Oferta.

### <a name="name"></a>Nome

O nome que aqui entra apresenta como título da sua oferta. Este campo está pré-preenchido com o texto que inseriu na caixa **de pseudónimos Oferta** quando criou a oferta. Pode alterar este nome posteriormente.

O nome:

- Pode ser registada (e pode incluir símbolos de marca registada e direitos de autor).
- Não deve ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Uma breve descrição da sua oferta. Isto pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa no mercado.

### <a name="long-summary"></a>Resumo longo

Uma descrição mais detalhada da sua oferta. Isto pode ter até 256 caracteres de comprimento e é usado em resultados de pesquisa no mercado.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Ligação de política de privacidade

Insira o endereço web da política de privacidade da sua organização. Você é responsável por garantir que a sua oferta está em conformidade com as leis e regulamentos de privacidade. Você também é responsável por publicar uma política de privacidade válida no seu site.

#### <a name="useful-links"></a>Ligações úteis

Forneça documentos online suplementares sobre a sua oferta. Pode adicionar até 25 links. Para adicionar um link, **selecione + Adicione um link** e, em seguida, complete os seguintes campos:

- **Título** – Os clientes verão isto na página de detalhes da sua oferta.
- **Link (URL)** – Introduza um link para os clientes visualizarem o seu documento online. A ligação deve começar com http:// ou https://.

### <a name="contact-information"></a>Informações de Contacto

Deve fornecer o nome, e-mail e número de telefone para um **contacto de suporte** e um contacto de **Engenharia.** Esta informação não é mostrada aos clientes, mas está disponível para a Microsoft. Também pode ser fornecido aos parceiros cloud Solution Provider (CSP).

- Contacto de apoio (obrigatório): Para questões gerais de apoio.
- Contacto de engenharia (obrigatório): Para questões técnicas e questões de certificação.
- Contacto do Programa CSP (opcional): Para revendedores questões relacionadas com o programa CSP.

Na secção **de contato de apoio,** forneça o **site de Apoio** onde os parceiros possam encontrar apoio para a sua oferta com base no facto de a oferta estar disponível no Global Azure, Azure Government, ou ambos.

Na secção de contacto do **Programa CSP,** forneça o link **(CSP Program Marketing Materials**) onde os parceiros da CSP possam encontrar materiais de marketing para a sua oferta.

#### <a name="additional-marketplace-listing-resources"></a>Recursos de listagem de mercado adicionais

Para saber mais sobre a criação de listas de ofertas, consulte [Offer listing best practices](../gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para utilizar com a sua oferta. Todas as imagens devem estar em formato PNG. Imagens desfocadas serão rejeitadas.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da loja

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center utilizará isto para criar um logótipo **Pequeno** e **Médio.** Pode substituir opcionalmente estas imagens por diferentes imagens mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, necessário)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Estes logótipos são utilizados em diferentes locais da listagem:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Screenshots (opcional)

Adicione cinco imagens que mostram como a sua oferta funciona. Cada um deve ter 1280 x 720 pixels em tamanho e em formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até cinco vídeos que demonstram a sua oferta. Insira o nome do vídeo, o seu endereço web e uma imagem PNG de miniatura do vídeo a 1280 x 720 pixels de tamanho.

#### <a name="offer-examples"></a>Exemplos de oferta

Os exemplos a seguir mostram como os campos de listagem de oferta aparecem em diferentes lugares da oferta.

Isto mostra a página **de listagem de ofertas** no Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft." :::

Isto mostra os resultados da pesquisa no Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.":::

Isto mostra a página **de listagem de Ofertas** no portal Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.":::

Isto mostra os resultados da pesquisa no portal Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.":::

## <a name="preview"></a>Pré-visualizar

No separador Pré-visualização, pode escolher um **Audience de pré-visualização** limitado para validar a sua oferta antes de publicá-la ao vivo.

> [!IMPORTANT]
> Depois de ver a sua oferta em **Preview,** tem de selecionar **Go live** para publicar a sua oferta ao público.

Especifique o seu público de pré-visualização usando GUIDs de subscrição Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes.

> [!NOTE]
> Pode encontrar o seu ID de subscrição Azure na página de Subscrições no portal Azure.

Adicione pelo menos um ID de subscrição Azure, individualmente (até 10) ou carregando um ficheiro CSV (até 100). Ao adicionar estes IDs de subscrição, você determina quem pode pré-visualizar a sua oferta antes de ser publicada ao vivo. Se a sua oferta já estiver ao vivo, pode escolher um público de pré-visualização para testar alterações ou atualizações da sua oferta.

**Selecione Guardar o projeto** antes de continuar.

## <a name="plan-overview"></a>Visão geral do plano

Este separador permite-lhe fornecer diferentes opções de plano dentro da mesma oferta. Os planos (anteriormente chamados SKUs) podem diferir em termos de que nuvens estão disponíveis, tais como nuvens globais, nuvens do governo, e a imagem referenciada pelo plano. Para listar a sua oferta no mercado comercial, tem de definir pelo menos um plano.

Pode criar até 100 planos para cada oferta: até 45 destes podem ser privados. Saiba mais sobre [planos privados em ofertas privadas no mercado comercial da Microsoft.](../private-offers.md)

Depois de criar os seus planos, o separador **De visão geral** do Plano mostra:

- Nomes do plano
- Modelo preços
- Regiões do Azul (Global ou Governo)
- Estado atual da publicação
- Quaisquer ações disponíveis

As ações disponíveis no Resumo do Plano variam consoante o estado atual do seu plano. Estas incluem:

- **Eliminar rascunho** - Se o estado do plano for um Projeto.
- **Parar o plano de venda** – Se o estado do plano for publicado em direto.

### <a name="create-new-plan"></a>Criar novo plano

**Selecione Criar novo plano.** Aparece a caixa de diálogo do **novo plano.**

Na caixa **de Identificação** do Plano, crie um identificador de plano único para cada plano nesta oferta. Este ID será visível para os clientes no endereço web do produto. Utilize apenas letras e números minúsculos, traços ou sublinhados, e um máximo de 50 caracteres.

> [!NOTE]
> O ID do plano não pode ser alterado depois de selecionar **Criar**.

Na caixa de nomes do **Plano,** insira um nome para este plano. Os clientes vêem este nome ao decidir qual o plano a selecionar dentro da sua oferta. Crie um nome único para cada plano nesta oferta. Por exemplo, pode utilizar um nome de oferta do **Windows Server** com os planos Windows **Server 2016** e **Windows Server 2019**.

### <a name="plan-setup"></a>Configuração do plano

Este separador permite-lhe escolher em que nuvens o plano está disponível. As suas respostas neste separador afetam quais os campos apresentados em outros separadores.

#### <a name="azure-regions"></a>Regiões do Azure

Todos os planos para ofertas de contentores Azure são automaticamente disponibilizados na **Azure Global.**  O seu plano pode ser usado por clientes em todas as regiões globais do Azure que utilizam o mercado comercial. Para mais informações, consulte [a disponibilidade geográfica e o suporte à moeda.](../marketplace-geo-availability-currencies.md)

Selecione a opção [do Governo Azure](../../azure-government/documentation-government-welcome.md) para fazer a sua solução aparecer aqui. Esta é uma nuvem governamental com acesso controlado para clientes de agências federais, estaduais e locais ou tribais, bem como parceiros elegíveis para servi-los. Como editor, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas para esta comunidade de nuvem. O Governo Azure utiliza centros e redes de dados fisicamente isolados (localizados apenas nos EUA). Antes [de publicar](../../azure-government/documentation-government-manage-marketplace-partners.md) no Governo Azure, teste e confirme a sua solução nessa área, uma vez que os resultados podem ser diferentes. Para criar e testar a sua solução, solicite uma conta experimental do julgamento do [Governo Microsoft Azure](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois de o seu plano ser publicado e disponível numa região específica, não pode remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Governo de Azure

Esta opção só pode ser vista se o **Governo Azure** for selecionado nas **regiões de Azure.**

Os serviços do Governo Azure tratam dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.

Para mostrar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para as suas listas no programa diretamente ou para o seu próprio site. Estas ligações são visíveis apenas para clientes do Governo Azure.

### <a name="plan-listing"></a>Listagem de planos

Este separador apresenta informações específicas para cada plano diferente dentro da oferta atual.

### <a name="plan-name"></a>Nome do plano

Isto está pré-preenchido com o nome que deu ao seu plano quando o criou. Pode mudar este nome conforme necessário. Pode ter até 50 caracteres de comprimento. Este nome aparece como o título deste plano no Azure Marketplace e no portal Azure. É usado como o nome do módulo predefinido depois do plano estar pronto para ser usado.

### <a name="plan-summary"></a>Resumo do plano

Um breve resumo do seu plano de software (não a oferta). Este resumo aparece nos resultados de pesquisa do Azure Marketplace e pode conter até 100 caracteres.

### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software único, bem como diferenças entre planos dentro da sua oferta. Não descreva a oferta, só o plano. Esta descrição aparecerá no Azure Marketplace e no portal Azure na página **de listagem da Oferta.** Pode ser o mesmo conteúdo que forneceu no resumo do plano e conter até 2.000 caracteres.

**Selecione Guardar** depois de completar estes campos.

#### <a name="plan-examples"></a>Exemplos do plano

Os exemplos a seguir mostram como os campos de listagem do plano aparecem em diferentes pontos de vista.

Estes são os campos no Azure Marketplace ao visualizar detalhes do plano:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.":::

Estes são detalhes do plano no portal Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.":::

### <a name="plan-availability"></a>Disponibilidade do plano

Se quiser ocultar a sua oferta publicada para que os clientes não possam pesquisar, navegar ou comprar no mercado, selecione a caixa de verificação do **plano Oculta** no separador **Disponibilidade.**

Este campo é utilizado quando:

- A oferta destina-se a ser utilizada indiretamente quando referenciada através de outra aplicação.
- A oferta não deve ser comprada individualmente.
- O plano foi utilizado para os testes iniciais e já não é relevante.
- O plano foi utilizado para ofertas temporárias ou sazonais e não deve ser mais oferecido.

## <a name="technical-configuration"></a>Configuração técnica

As imagens do contentor devem ser acolhidas num registo privado [do contentor Azure](https://azure.microsoft.com/services/container-registry/). No separador **Configuração Técnica,** forneça informações de referência para o repositório de imagem do seu contentor no interior do Registo do Contentor de Azure.

Após a publicação da oferta, a sua imagem de contentor é copiada para o Azure Marketplace num registo específico de contentores públicos. Todos os pedidos para usar a sua imagem de contentor são servidos a partir do registo público de contentores Azure Marketplace, não o seu privado. Para mais informações, consulte [prepare os seus ativos técnicos do Azure Container](create-azure-container-technical-assets.md).

### <a name="image-repository-details"></a>Detalhes do repositório de imagem

Forneça as seguintes informações no **separador detalhes do repositório de imagem.**

**ID de assinatura Azure** – Forneça o ID de subscrição onde o uso é reportado e os serviços são faturados para o Registo do Contentor Azure que inclui a sua imagem de recipiente. Pode encontrar este ID na [página de Subscrições](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal Azure.

**Nome do grupo de recursos Azure** – Forneça o nome do [grupo de recursos](../../azure-resource-manager/management/manage-resource-groups-portal.md) que contém o Registo do Contentor Azure com a sua imagem de recipiente. O grupo de recursos deve estar acessível no ID de assinatura (acima). Pode encontrar o nome na página [dos grupos de Recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) no portal Azure.

Nome do **Registo do Contentor Azure** – Forneça o nome do Registo do [Contentor Azure](../../container-registry/container-registry-intro.md) que tem a sua imagem de recipiente. O registo do contentor deve estar no grupo de recursos Azure que forneceu anteriormente. Inclua apenas o nome do registo, não o nome completo do servidor de login. Certifique-se de omitir **azurecr.io** do nome. Pode encontrar o nome do registo na [página de Registos de Contentores](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) no portal Azure.

**Nome de utilizador Admin para o Registo do Contentor Azure** – Forneça o [nome de utilizador administrativo](../../container-registry/container-registry-authentication.md#admin-account)) ligado ao Registo do Contentor Azure que tem a sua imagem de recipiente. O nome de utilizador e a palavra-passe são necessários para garantir que a sua empresa tem acesso ao registo. Para obter o nome de utilizador e a **palavra-passe** de administrador, desafine a propriedade ativada por administração para **True** utilizando a Interface Azure Command-Line (CLI). Pode configurar opcionalmente **o utilizador de Administração** para **ativar** no portal Azure.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.":::

**Palavra-passe para o Registo do Contentor Azure** – Forneça a palavra-passe para o nome de utilizador admin que está associado ao Registo do Contentor Azure e tem a sua imagem de contentor. O nome de utilizador e a palavra-passe são necessários para garantir que a sua empresa tem acesso ao registo. Pode obter a palavra-passe do portal Azure indo para as Chaves de Acesso ao **Registo de Contentores**  >  **Access Keys** ou com o Azure CLI utilizando o comando do [espetáculo](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.":::

**Nome do repositório no registo do contentor Azure**. Forneça o nome do repositório do Registo do Contentor Azure que tenha a sua imagem. Inclua o nome do repositório quando empurrar a imagem para o registo. Pode encontrar o nome do repositório [Container Registry](https://azure.microsoft.com/services/container-registry/)indo para a página de  >  **Repositórios de Registo de Contentores.** Para mais informações, consulte [os repositórios de registo de contentores no portal Azure.](../../container-registry/container-registry-repositories.md)

> [!NOTE]
> Depois do nome estar definido, não pode ser mudado. Use um nome único para cada oferta na sua conta.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Etiquetas de imagem para novas versões da sua oferta

Os clientes devem poder obter automaticamente atualizações do Mercado Azure quando publicar uma atualização. Se não quiserem atualizar, devem poder manter-se numa versão específica da sua imagem. Pode fazê-lo adicionando novas etiquetas de imagem sempre que fizer uma atualização à imagem.

### <a name="image-tag"></a>Etiqueta de imagem

Este campo deve incluir uma **etiqueta mais recente** que aponta para a versão mais recente da sua imagem em todas as plataformas suportadas. Deve também incluir uma etiqueta de versão (por exemplo, começando com xx.xx.xx, onde xx é um número). Os clientes devem usar [tags manifestas](https://github.com/estesp/manifest-tool) para direcionar várias plataformas. Todas as etiquetas referenciadas por uma etiqueta manifesto também devem ser adicionadas para que possamos carregá-las.

Todas as tags manifesto (exceto a última etiqueta) devem começar com X.Y **-** ou X.Y.Z- onde X, Y e Z são inteiros. Por exemplo, se uma **etiqueta mais recente** aponta para 1.0.1-linux-x64, 1.0.1-linux-arm32 e 1.0.1-windows-arm32, estas seis etiquetas precisam de ser adicionadas a este campo. Para mais informações, consulte [prepare os seus ativos técnicos do Azure Container](create-azure-container-technical-assets.md).

> [!NOTE]
> Lembre-se de adicionar uma etiqueta de teste à sua imagem para que possa identificar a imagem durante o teste.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter concluído todas as secções necessárias da oferta, pode submetê-la para revisão e publicação.

No canto superior direito do portal, selecione **'Rever e** **publicar**.

Na página de comentários pode:

- Consulte o estado de conclusão de cada secção da oferta. Não pode publicar até que todas as secções da oferta estejam marcadas como completas.
  - **Ainda** não foi iniciado – Ainda não foi iniciado e precisa de ser concluído.
  - **Incompleto** – Tem erros que precisam de ser corrigidos ou requer que forneça mais informações. Consulte as secções mais cedo neste documento para obter ajuda.
  - **Completo** – Inclui todos os dados necessários sem erros. Todas as secções da oferta devem estar completas antes de poder submeter a oferta.
- Forneça instruções de teste à equipa de certificação para garantir que a sua oferta é testada corretamente. Além disso, forneça quaisquer notas suplementares que sejam úteis para entender a sua oferta.

Para submeter a oferta de publicação, **selecione Publicar**.

Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta estiver disponível para revisão e aprovação.

Para publicar a sua oferta ao público, vá ao Partner Center e selecione **Go-live**.

## <a name="next-step"></a>Passo seguinte

- [Atualizar uma oferta existente no mercado comercial](update-existing-offer.md)

---
title: Criar uma oferta de serviço de Consultoria no Partner Center - Azure Marketplace
description: Saiba como publicar uma oferta de serviço de Consultoria para o Azure Marketplace ou appSource usando o Partner Center.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: f1354ce824bc4e57ab55846c714d4a0a73a46966
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873825"
---
# <a name="create-a-consulting-service-offer"></a>Criar uma oferta de serviço de Consultoria

> [!IMPORTANT]
> Estamos a mover a gestão das suas ofertas de serviço de Consultoria do Cloud Partner Portal para partner Center. Até que as suas ofertas sejam migradas, siga as instruções na oferta de serviço de [consultoria Azure e Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) para o Cloud Partner Portal para gerir as suas ofertas.

Este artigo descreve como publicar uma oferta de serviço de Consultoria para [o Azure Marketplace](https://azuremarketplace.microsoft.com/) ou [appSource](https://appsource.microsoft.com/). O serviço List Consulting oferece com base no Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e na Power Platform no AppSource. O serviço List Consulting oferece com base no Microsoft Azure no Azure Marketplace.

Para criar uma oferta de serviço de Consultoria nos serviços Azure Marketplace ou AppSource Consulting, tem primeiro de [ter uma conta de editor no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account), e a sua conta deve estar inscrita no programa de marketplace comercial. Antes de criar a sua oferta, reveja os pré-requisitos no [serviço de Consultoria.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)

## <a name="publishing-benefits"></a>Benefícios de publicação

Benefícios da publicação no mercado comercial:

- Promova a sua empresa utilizando a marca Microsoft.
- Potencialmente, alcançam mais de 100 milhões de utilizadores do Office 365 e da Dynamics 365 no AppSource e mais de 200.000 organizações através do Azure Marketplace.
- Receba pistas de alta qualidade destes mercados.
- Tenha os seus serviços promovidos pelas equipas de campo e televendas da Microsoft

## <a name="create-a-new-offer"></a>Criar uma nova oferta

Depois de cumprir os requisitos acima descritos, siga estes passos para criar uma oferta de serviço consultoria.

1. Inicie sessão no [Partner Center](https://partner.microsoft.com)e, em seguida, selecione **dashboard** a partir do menu superior.
2. Na barra de navegação à esquerda, selecione **Mercado Comercial,** em seguida, selecione **Visão Geral**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Ilustra o menu para o mercado comercial":::

3. **Selecione + Nova Oferta**e, em seguida, selecione o serviço de **Consultoria**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Ilustra o botão para criar uma nova oferta.":::

4. Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

    - Este ID é visível para os clientes no endereço web para a oferta de marketplace.
    - Use apenas letras minúsculas, números, traços e sublinhados, mas sem espaços. O comprimento é limitado a 50 caracteres. Por exemplo, se introduzir a **oferta de teste-1,** o URL de oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
    - O ID da Oferta não pode&#39;ser alterado depois de selecionar **Criar**.

5. Insira um **pseudónimo da Oferta.** Este é o nome usado para se referir à oferta no Partner Center.

    - Este nome não é&#39;usado no mercado. &#39;diferente do nome da oferta e de outros valores que são mostrados aos clientes. Pode utilizar este campo para atribuir um nome à oferta que lhe seja mais útil para identificar a oferta internamente; não é mostrado aos clientes.
    - O pseudónimo da oferta pode&#39;não ser alterado depois de selecionar **Criar**.

Depois de introduzir estes dois valores, selecione **Criar** para continuar na página de **configuração da Oferta.**

## <a name="offer-setup"></a>Configuração de oferta

Depois de introduzir um pseudónimo de Id e Oferta de Oferta, o Partner Center cria uma oferta de rascunho e exibe a página de **configuração da Oferta.** Siga estes passos para configurar a sua oferta.

### <a name="connect-lead-management"></a>Ligar a gestão de chumbo

Ao publicar a sua oferta no mercado com o Partner Center, _deve_ ligá-la a um sistema de Gestão de Relacionamento com O Cliente (CRM) ou a um sistema de automação de marketing. Isto permite-lhe receber informações de contacto do cliente assim que alguém expressa interesse ou utiliza o seu produto.

1. Selecione **Connect** para especificar onde pretende que enviemos os cabos do cliente. Partner Center suporta os seguintes sistemas:

    - [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para envolvimento com o cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o seu sistema CRM não&#39;estiver listado acima, utilize o [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou [o Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados de chumbo do cliente e, em seguida, exporte os dados para o seu sistema CRM.

2. Ligue a sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a ligação ao destino principal está corretamente configurada. Depois de publicá-lo no Partner Center,&#39;validará a ligação e enviar-lhe-á um teste. Enquanto faz a pré-visualização da oferta antes de ser transmitida ao vivo, também pode testar a sua ligação de chumbo tentando adquirir a oferta no ambiente de pré-visualização.
4. Certifique-se de que a ligação ao destino principal permanece atualizada para que não perca pistas&#39;.

Aqui estão alguns recursos adicionais de gestão de chumbo:

- [Visão geral da gestão de chumbo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [FAQs de gestão de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros de configuração comuns de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral da gestão de chumbo](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Certifique-se de que o seu bloqueador pop-up está desligado)

Selecione **Guardar rascunho** antes de continuar na secção seguinte, Propriedades.

### <a name="properties"></a>Propriedades

Esta página permite-lhe definir o produto primário que o seu serviço De consultoria oferece as melhores capas, definir um tipo de serviço Consultoria e escolher produtos aplicáveis.

1. Selecione um **produto Primário** a partir da lista de lançamentos.
2. Selecione um tipo de **serviço de Consultoria** na lista de entrega:

    - **Avaliação** : Uma avaliação do ambiente de&#39;do cliente para determinar a aplicabilidade de uma solução e fornecer uma estimativa de custo e tempo.
    - **Briefing** : Uma introdução a uma solução ou um serviço de Consultoria para atrair o interesse dos clientes utilizando quadros, demonstrações e exemplos de clientes.
    - **Implementação** : Uma instalação completa que resulte numa solução de pleno funcionamento. Limite para soluções que podem ser implementadas em duas semanas ou menos.
    - **Prova de conceito** : Uma implementação de âmbito limitado para determinar se uma solução satisfaz os requisitos do cliente.
    - **Workshop** : Um compromisso interativo realizado nas instalações de um cliente&#39;. Pode envolver formação, briefings, avaliações ou demonstrações construídas sobre os dados ou ambiente do cliente&#39;.

1. Se selecionar um produto primário do **Azure,** selecione até três Áreas de **Solução**. Estes facilitam aos clientes do Azure Marketplace encontrar a sua oferta. Se não escolheu&#39;Azure, ignore este passo.
2. Se selecionou um produto primário _que não_ o Azure, selecione até três **produtos aplicáveis.** Estes facilitam aos clientes do AppSource encontrar a sua oferta. Para mais detalhes, consulte as Diretrizes de Listagem de Serviços de [Consultoria microsoft AppSource](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Selecione até seis **Indústrias** a que a sua oferta se aplica. Isto facilitará aos clientes encontrar a sua oferta.
4. Adicione até três **Competências** que a sua empresa ganhou para exibir na sua lista de ofertas de serviço consultoria. É necessária pelo menos uma competência, com exceção da MSP&#39;s e da Azure Networking MSP&#39;s.

Selecione **Guardar rascunho** antes de continuar na secção seguinte, Oferta de listagem.

## <a name="offer-listing"></a>Oferta listagem

Aqui&#39;definirá os detalhes da oferta que são exibidos no mercado. Isto inclui o nome da oferta, descrição, imagens, e assim por diante. Certifique-se de seguir as políticas detalhadas na [página de política da Microsoft&#39;](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) enquanto configura esta oferta.

> [!NOTE]
> Os detalhes da oferta são&#39;não são necessários para &quot;estar em inglês se a descrição da oferta começar com a frase, esta aplicação está disponível apenas em [língua não inglesa]. &quot; Também&#39;é bom fornecer um Link Útil para oferecer conteúdo num idioma que&#39;é diferente do utilizado nos detalhes da listagem de oferta.

### <a name="name"></a>Nome

O nome que insere aqui mostra como o título da sua oferta. Este campo está pré-preenchido com o texto que inseriu na caixa **de pseudónimos Offer** quando criou a oferta. Pode alterar este nome posteriormente.

O nome:

- Pode ser marcado por marca (e pode incluir símbolos de marca ou direitos de autor).
- Não pode&#39;ter mais de 50 caracteres.
- Não pode&#39;incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta. Isto pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta, até 3.000 caracteres. Isto é apresentado aos clientes na visão geral da listagem do mercado.

Inclua um ou mais dos seguintes na sua descrição:

- O valor e os benefícios-chave que a sua oferta proporciona
- Categoria ou associações industriais, ou ambos
- Oportunidades de compra na aplicação
- Quaisquer divulgações necessárias

Aqui ficam algumas dicas para escrever a sua descrição:

- Descreva claramente o valor da sua oferta nas primeiras frases da sua descrição. Incluir os seguintes itens:
  - Descrição da oferta.
  - O tipo de utilizador que beneficia da oferta.
  - As necessidades do cliente ou emite os endereços da oferta.
- Lembre-se que as primeiras frases podem ser exibidas nos resultados da pesquisa.
- Não&#39;depender de funcionalidades e funcionalidades para vender o seu produto. Em vez disso, concentre-se no valor que a sua oferta proporciona.
- Tente utilizar vocabulário específico da indústria ou formulação baseada em benefícios.

Para tornar a sua descrição mais envolvente, use o rico editor de texto para formatar a sua descrição. O rico editor de texto permite-lhe adicionar números, balas, ousados, itálicos e travessos para tornar a sua descrição mais legível.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Ilustra o rico editor de texto para escrever a descrição da oferta." border="false":::

### <a name="keywords"></a>Palavras-chave

Insira até três palavras-chave de pesquisa relevantes para o seu produto primário e serviço de Consultoria. Isto vai facilitar a sua oferta.

### <a name="duration"></a>Duração

Detete a duração esperada deste compromisso com o seu cliente.

### <a name="contact-information"></a>Informações de Contacto

Deve fornecer o nome, e-mail e número de telefone para um contacto **primário** e **secundário**. Esta informação não é&#39;mostrada aos clientes. Está disponível para a Microsoft e pode ser fornecido aos parceiros do Cloud Solution Provider (CSP).

### <a name="supporting-documents"></a>Documentos comprovantes

Adicione até três (mas pelo menos um) documentos PDF comprovando para a sua oferta.

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para usar com a sua oferta. Todas as imagens devem estar em formato .png. Imagens desfocadas serão rejeitadas.

#### <a name="store-logos"></a>Logotipos de loja

Forneça ficheiros .png da sua oferta&#39;logotipo s em cada um dos seguintes tamanhos de pixel:

- **Pequeno (48 x 48)**
- **Grande (216 x 216)**

Todos os logótipos são necessários e são usados em diferentes lugares na listagem do mercado.

#### <a name="screenshots-optional"></a>Screenshots (opcional)

Adicione cinco imagens que mostram como a sua oferta funciona. Cada um deve ter 1280 x 720 pixels de tamanho e em formato .png.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até quatro vídeos que demonstram a sua oferta. Introduza o vídeo&#39;nome, o seu endereço web (URL) e uma imagem miniatura do vídeo a 1280 x 720 pixels de tamanho.

Selecione **guardar rascunho** antes de continuar para a próxima secção, Preços e disponibilidade.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Aqui irá definir elementos como preços, mercado e uma chave privada.

1. **Mercado**: Desloque o mercado onde a sua oferta estará disponível. Só pode selecionar um mercado por oferta.
    1. Para os mercados dos Estados Unidos ou canadá, selecione **Estados de Edição** (ou **províncias)** para especificar onde a sua oferta estará disponível.
2. **Preview Audience**: Configure a **chave de ocultação** utilizada para definir o público privado para a sua oferta.
3. **Preço**: Especifique se a sua oferta é uma oferta **gratuita** ou **paga.**

    > [!NOTE]
    > As ofertas de serviço de consultoria são apenas para a listagem. Quaisquer transações ocorrerão diretamente, fora do mercado comercial.

4. Para uma oferta paga, especifique o **Preço e a Moeda** e se o preço é **Fixo** ou **Estimado**. Se estimado, deve especificar na descrição quais os fatores que afetarão o preço.
5. Selecione **Guardar o projeto**.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter concluído todas as secções necessárias da oferta, pode submeter a sua oferta para rever e publicar.

1. Quando estiver pronto para publicar a sua oferta de serviço de Consultoria, clique em **Rever e publique**.
2. Reveja os detalhes na página final de submissão.
3. Se necessário, escreva uma nota à equipa de certificação se acreditar que algum dos detalhes da sua oferta requer explicação.
4. Quando estiver pronto, selecione **Submeter**.
5. A página geral da **Oferta** mostra em que fase de publicação está a sua oferta.

Para mais informações sobre quanto tempo pode esperar que a sua oferta esteja em cada fase editorial, consulte o estado editorial da sua oferta de [Mercado Comercial.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)

## <a name="update-your-existing-consulting-service-offers"></a>Atualize as ofertas de serviço seleções de Consultoria existentes

- [Atualizar uma oferta existente no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)

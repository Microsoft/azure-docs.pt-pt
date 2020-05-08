---
title: Criar uma oferta de serviço de consultoria - mercado comercial da Microsoft
description: Saiba como publicar uma oferta de serviço de consultoria para o Microsoft AppSource ou o Azure Marketplace usando o Partner Center.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 7e85175cbee53bd5984a38a587a90b98eaf8dcad
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791906"
---
# <a name="create-a-consulting-service-offer"></a>Criar uma oferta de serviço de consultoria

> [!IMPORTANT]
> Estamos a mover a gestão das suas ofertas de serviço de consultoria do Cloud Partner Portal para partner Center. Até que as suas ofertas sejam migradas, siga as instruções na oferta de serviço de [consultoria Azure e Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) para o Cloud Partner Portal para gerir as suas ofertas.

Este artigo descreve como publicar uma oferta de serviço de consultoria para o [Microsoft AppSource](https://appsource.microsoft.com/) ou [para o Azure Marketplace](https://azuremarketplace.microsoft.com/). Ofertas de serviço de consultoria de listas baseadas no Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e na Power Platform no AppSource. O serviço List Consulting oferece com base no Microsoft Azure no Azure Marketplace. Antes de começar, [Crie uma conta de marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

Antes de criar a sua oferta, reveja os pré-requisitos no [serviço de Consultoria.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)

## <a name="publishing-benefits"></a>Benefícios de publicação

Benefícios da publicação no mercado comercial:

- Promova a sua empresa utilizando a marca Microsoft.
- Potencialmente, alcançam mais de 100 milhões de utilizadores do Office 365 e da Dynamics 365 no AppSource e mais de 200.000 organizações através do Azure Marketplace.
- Receba pistas de alta qualidade destes mercados.
- Tenha os seus serviços promovidos pelas equipas de campo e televendas da Microsoft

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace** > **Overview**.
3. Na página 'Visão Geral', selecione + Novo Serviço de**Consultoria**de **Oferta.** > 

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Depois de publicada uma oferta, as edificações feitas no Partner Center só aparecem nas montras depois de reeditarem a oferta. Certifique-se de que republique sempre depois de fazer alterações.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de marketplace.
- Utilize apenas letras minúsculas e números. Pode incluir hífenes e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir o **test-offer-1,** `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`o endereço web da oferta será .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira um **pseudónimo da Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- O pseudónimo da Oferta não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

### <a name="connect-lead-management"></a>Ligar a gestão de chumbo

Ao publicar a sua oferta no mercado com o Partner Center, _deve_ ligá-la a um sistema de Gestão de Relacionamento com O Cliente (CRM) ou a um sistema de automação de marketing. Isto permite-lhe receber informações de contacto do cliente assim que alguém expressa interesse ou utiliza o seu produto.

1. Selecione **Connect** para especificar onde pretende que enviemos os cabos do cliente. Partner Center suporta os seguintes sistemas:

    - [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para envolvimento com o cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o seu sistema CRM não estiver listado acima, utilize [o Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou [o Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados de chumbo do cliente e, em seguida, exporte os dados para o seu sistema CRM.

2. Ligue a sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a ligação ao destino principal está corretamente configurada. Depois de publicá-lo no Partner Center, validamos a ligação e enviaremos-lhe um teste. Enquanto faz a pré-visualização da oferta antes de ser transmitida ao vivo, também pode testar a sua ligação de chumbo tentando adquirir a oferta no ambiente de pré-visualização.
4. Certifique-se de que a ligação ao destino principal permanece atualizada para que não perca nenhuma pista.

Aqui estão alguns recursos adicionais de gestão de chumbo:

- [Visão geral da gestão de chumbo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [FAQs de gestão de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros de configuração comuns de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [Visão geral da gestão de chumbo](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Certifique-se de que o seu bloqueador pop-up está desligado)

Selecione **guardar rascunho** antes de continuar.

### <a name="properties"></a>Propriedades

Esta página permite definir o produto primário que o seu serviço de consultoria oferece as melhores capas, definir um tipo de serviço de consultoria e escolher produtos aplicáveis.

1. Selecione um **produto Primário** a partir da lista de lançamentos.
2. Selecione um tipo de **serviço de Consultoria** na lista de entrega:

    - **Avaliação**: Uma avaliação do ambiente de um cliente para determinar a aplicabilidade de uma solução e fornecer uma estimativa de custo e tempo.
    - **Briefing**: Uma introdução a uma solução ou um serviço de Consultoria para atrair o interesse dos clientes utilizando quadros, demonstrações e exemplos de clientes.
    - **Implementação**: Uma instalação completa que resulte numa solução de pleno funcionamento. Limite para soluções que podem ser implementadas em duas semanas ou menos.
    - **Prova de conceito**: Uma implementação de âmbito limitado para determinar se uma solução satisfaz os requisitos do cliente.
    - **Workshop**: Um compromisso interativo realizado nas instalações de um cliente. Pode envolver formação, briefings, avaliações ou demonstrações construídas sobre os dados ou ambiente do cliente.

1. Se selecionar um produto primário do **Azure,** selecione até três Áreas de **Solução**. Estes facilitam aos clientes do Azure Marketplace encontrar a sua oferta. Se não escolheu o Azure, esquece este passo.
2. Se selecionou um produto primário _que não_ o Azure, selecione até três **produtos aplicáveis.** Estes facilitam aos clientes do AppSource encontrar a sua oferta. Para mais detalhes, consulte as Diretrizes de Listagem de Serviços de [Consultoria microsoft AppSource](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Selecione até seis **Indústrias** a que a sua oferta se aplica. Isto facilitará aos clientes encontrar a sua oferta.
4. Adicione até três **Competências** que a sua empresa ganhou para exibir na sua listagem de oferta de serviço de consultoria. É necessária pelo menos uma competência, com exceção dos Deputados especialistas do Azure e dos Deputados de Networking Azure.

Selecione **guardar rascunho** antes de continuar.

## <a name="offer-listing"></a>Oferta listagem

Aqui irá definir os detalhes da oferta que são exibidos no mercado. Isto inclui o nome da oferta, descrição, imagens, e assim por diante. Certifique-se de seguir as políticas detalhadas na página de políticas de [certificação](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) do mercado comercial enquanto configura esta oferta.

> [!NOTE]
> Os detalhes da oferta não são necessários para estar em &quot;inglês se a descrição da oferta começar com a frase, esta aplicação está disponível apenas em [língua não inglesa]. &quot; Também é normal fornecer um Link Útil para oferecer conteúdo num idioma diferente do usado nos detalhes da listagem de oferta.

### <a name="name"></a>Name

O nome que insere aqui mostra como o título da sua oferta. Este campo está pré-preenchido com o texto que inseriu na caixa **de pseudónimos Offer** quando criou a oferta. Pode alterar este nome posteriormente.

O nome:

- Pode ser marcado por marca (e pode incluir símbolos de marca ou direitos de autor).
- Não deve ter mais de 50 caracteres.
- Não posso incluir emojis.

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
- Não confie em funcionalidades e funcionalidades para vender o seu produto. Em vez disso, concentre-se no valor que a sua oferta proporciona.
- Tente utilizar vocabulário específico da indústria ou formulação baseada em benefícios.

Para tornar a sua descrição mais envolvente, use o rico editor de texto para aplicar formatação.

![Usando o rico editor de texto](./media/rich-text-editor.png)

| <center>Alterar o formato de texto | <center>Adicionar balas ou numerar | <center>Adicionar ou remover o travessão de texto |
| --- | --- | --- |
| <center>![Usando o rico editor de texto para alterar o formato de texto](./media/text-editor3.png) |  <center>![Usando o rico editor de texto para adicionar listas](./media/text-editor4.png) |  <center>![Usando o rico editor de texto para o travessão](./media/text-editor5.png) |

### <a name="keywords"></a>Palavras-chave

Insira até três palavras-chave de pesquisa relevantes para o seu produto primário e serviço de consultoria. Isto vai facilitar a sua oferta.

### <a name="duration"></a>Duração

Detete a duração esperada deste compromisso com o seu cliente.

### <a name="contact-information"></a>Informações de Contacto

Deve fornecer o nome, e-mail e número de telefone para um contacto **primário** e **secundário**. Esta informação não é mostrada aos clientes. Está disponível para a Microsoft e pode ser fornecido aos parceiros do Cloud Solution Provider (CSP).

### <a name="supporting-documents"></a>Documentos comprovantes

Adicione até três (mas pelo menos um) documentos PDF comprovando para a sua oferta.

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para usar com a sua oferta. Todas as imagens devem estar em formato .png. Imagens desfocadas serão rejeitadas.

>[!Note]
>Se tiver algum problema de envio de ficheiros, https://upload.xboxlive.com certifique-se de que a sua rede local não bloqueia o serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos de loja

Forneça ficheiros PNG do logótipo da sua oferta em cada um dos seguintes tamanhos de pixel:

- **Pequeno (48 x 48)**
- **Grande (216 x 216)**

Todos os logótipos são necessários e são usados em diferentes lugares na listagem do mercado.

#### <a name="screenshots-optional"></a>Screenshots (opcional)

Adicione cinco imagens que mostram como a sua oferta funciona. Cada um deve ter 1280 x 720 pixels em tamanho e em formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até quatro vídeos que demonstram a sua oferta. Introduza o nome do vídeo, o seu endereço web (URL) e uma imagem PNG miniatura do vídeo a 1280 x 720 pixels de tamanho.

Selecione **guardar rascunho** antes de continuar.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Aqui irá definir elementos como preços, mercado e uma chave privada.

1. **Mercado**: Desloque o mercado onde a sua oferta estará disponível. Só pode selecionar um mercado por oferta.
    1. Para os mercados dos Estados Unidos ou canadá, selecione **Estados de Edição** (ou **províncias)** para especificar onde a sua oferta estará disponível.
2. **Preview Audience**: Configure a **chave de ocultação** utilizada para definir o público privado para a sua oferta.
3. **Preço**: Especifique se a sua oferta é uma oferta **gratuita** ou **paga.**

    > [!NOTE]
    > As ofertas de serviço de consultoria são apenas para a listagem. Quaisquer transações ocorrerão diretamente, fora do mercado comercial.

4. Para uma oferta paga, especifique o **Preço e a Moeda** e se o preço é **Fixo** ou **Estimado**. Se estimado, deve especificar na descrição quais os fatores que afetarão o preço.
5. Selecione **guardar rascunho** antes de continuar.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter concluído todas as secções necessárias da oferta, pode submeter a sua oferta para rever e publicar.

1. Quando estiver pronto para publicar a sua oferta de serviço de consultoria, clique em **Rever e publique**.
2. Reveja os detalhes na página final de submissão.
3. Se necessário, escreva uma nota à equipa de certificação se acreditar que algum dos detalhes da sua oferta requer explicação.
4. Quando estiver pronto, selecione **Submeter**.
5. A página geral da **Oferta** mostra em que fase de publicação está a sua oferta.

Para mais informações sobre quanto tempo pode esperar que a sua oferta esteja em cada fase editorial, consulte o estado editorial da sua oferta de [Mercado Comercial.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)

## <a name="update-your-existing-consulting-service-offers"></a>Atualize as suas ofertas de serviço de consultoria existentes

- [Atualizar uma oferta existente no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)

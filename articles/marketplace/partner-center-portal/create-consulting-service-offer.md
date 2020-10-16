---
title: Criar uma oferta de serviços de consultoria - Mercado comercial da Microsoft
description: Saiba como publicar uma oferta de serviço de consultoria para o Microsoft AppSource ou para o Azure Marketplace utilizando o Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: 464e75e55bc67ce619134be01ba00f2606a271a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709075"
---
# <a name="create-a-consulting-service-offer"></a>Criar uma oferta de serviço de consultoria

Este artigo descreve como publicar uma oferta de serviço de consultoria para o [Microsoft AppSource](https://appsource.microsoft.com/) ou [para o Azure Marketplace.](https://azuremarketplace.microsoft.com/) Ofertas de serviços de consultoria de listas baseadas no Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e na Power Platform no AppSource. O serviço List Consulting oferece-se com base no Microsoft Azure no Azure Marketplace. Antes de começar, [crie uma conta de marketplace comercial no Partner Center](create-account.md) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

Antes de criar a sua oferta, reveja os pré-requisitos em [pré-requisitos de serviço de Consultoria.](consulting-service-prerequisites.md)

## <a name="publishing-benefits"></a>Benefícios de publicação

Benefícios da publicação no mercado comercial:

- Promova a sua empresa utilizando a marca Microsoft.
- Potencialmente chegam a mais de 100 milhões de utilizadores microsoft 365 e Dynamics 365 no AppSource e mais de 200.000 organizações através do Azure Marketplace.
- Receba pistas de alta qualidade destes mercados.
- Tenha os seus serviços promovidos pelo campo microsoft e equipas de televendas

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página 'Vista Geral', selecione + Serviço de Consultoria **de oferta nova.**  >  **Consulting Service**

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Após a publicação de uma oferta, as edições feitas no Partner Center só aparecem nas lojas online depois de republicarem a oferta. Certifique-se de que é sempre republicante depois de escorção.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de mercado.
- Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir **a oferta de teste-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- O pseudónimo Oferta não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

### <a name="connect-lead-management"></a>Ligar gestão de chumbo

Ao publicar a sua oferta no mercado com o Partner Center, _deve_ conectá-la a um sistema de Gestão de Relacionamento com o Cliente (CRM) ou sistema de automação de marketing. Isto permite-lhe receber informações de contacto do cliente assim que alguém manifestar interesse ou utilizar o seu produto.

1. Selecione **Connect** para especificar para onde deseja que enviemos os leads do cliente. O Partner Center suporta os seguintes sistemas:

    - [Dinâmica 365](commercial-marketplace-lead-management-instructions-dynamics.md) para Envolvimento com o Cliente
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se o seu sistema CRM não estiver listado acima, utilize [a Tabela Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou [o Ponto Final https](commercial-marketplace-lead-management-instructions-https.md) para armazenar dados de chumbo do cliente e exporte os dados para o seu sistema CRM.

2. Ligue a sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a ligação ao destino de chumbo está configurada corretamente. Depois de publicá-lo no Partner Center, validaremos a ligação e enviaremos um teste. Enquanto pré-visualiza a oferta antes de entrar em direto, também pode testar a sua ligação de chumbo tentando comprar a oferta no ambiente de pré-visualização.
4. Certifique-se de que a ligação ao destino principal permanece atualizada para não perder nenhuma pista.

Aqui estão alguns recursos adicionais de gestão de chumbo:

- [O cliente lidera a partir da sua oferta de mercado comercial](commercial-marketplace-get-customer-leads.md)
- [Questões comuns sobre gestão de chumbo](../lead-management-faq.md#common-questions-about-lead-management)
- [Erros de configuração de chumbo de resolução de problemas](../lead-management-faq.md#publishing-config-errors)
- [Visão geral da gestão de chumbo](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Certifique-se de que o seu bloqueador pop-up está desligado).

**Selecione Guardar o projeto** antes de continuar.

### <a name="properties"></a>Propriedades

Esta página permite definir o produto primário que o seu serviço de consultoria oferece melhor cobertura, definir um tipo de serviço de consultoria e escolher os produtos aplicáveis.

1. Selecione um **produto primário** da lista de drop-down.
2. Selecione um tipo de **serviço de Consultoria** na lista de drop-down:

    - **Avaliação**: Uma avaliação do ambiente de um cliente para determinar a aplicabilidade de uma solução e fornecer uma estimativa de custo e tempo.
    - **Briefing**: Uma introdução a uma solução ou um serviço de Consultoria para atrair o interesse do cliente utilizando quadros, demonstrações e exemplos de clientes.
    - **Implementação**: Uma instalação completa que resulte numa solução totalmente funcionada. Limite para soluções que podem ser implementadas em duas semanas ou menos.
    - **Prova de conceito**: Uma implementação de âmbito limitado para determinar se uma solução satisfaz os requisitos do cliente.
    - **Workshop**: Um compromisso interativo realizado nas instalações de um cliente. Pode envolver formação, briefings, avaliações ou demonstrações construídas com base nos dados ou ambiente do cliente.

3. Se selecionar **o Azure** como produto primário, selecione até três **Áreas de Solução**. Estes facilitam a vida dos clientes no Azure Marketplace. Se não escolheste o Azure, salta este passo.

    - Análise
    - Modernização da App
    - Arquivo
    - IA + Machine Learning
    - Backup
    - Macrodados
    - Plataforma de Dados
    - Gestão de Datacenter
    - DevOps
    - Recuperação Após Desastre
    - Identidade
    - Internet das Coisas
    - Migração
    - Rede
    - Segurança
    - Armazenamento

1. Se selecionou **o Azure** como produto primário, tem a opção de selecionar até seis **Indústrias**. Estes facilitam a vida dos clientes no Azure Marketplace. Consulte a lista completa de indústrias na [Listagem de Ofertas Boas Práticas.](../gtm-offer-listing-best-practices.md) Se não escolheste o Azure, salta este passo.
1. Se selecionar um produto primário *diferente* do Azure, selecione até três **produtos aplicáveis.** Estes facilitam a vida dos clientes no AppSource. Para mais detalhes, consulte [as Diretrizes de Listagem de Serviços de Consultoria de Consultoria da Microsoft AppSource](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
1. Se selecionou um produto primário *diferente* do Azure, tem a opção de selecionar até **duas Indústrias** e **duas Verticais** para cada indústria. Estes facilitam a vida dos clientes no AppSource. Consulte a lista completa de indústrias e verticais na [Listagem de Ofertas Boas Práticas.](../gtm-offer-listing-best-practices.md)
1. Adicione três **Competências** que a sua empresa ganhou para exibir na sua listagem de oferta de serviço de consultoria. Pelo menos uma competência é necessária, com exceção dos MSPs expert da Azure e dos MSPs de rede Azure.

**Selecione Guardar o projeto** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui definirá os detalhes da oferta que são apresentados no mercado. Isto inclui o nome da oferta, descrição, imagens, e assim por diante. Certifique-se de seguir as políticas detalhadas na página de [políticas de certificação](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) do mercado comercial enquanto configura esta oferta.

> [!NOTE]
> Os detalhes da oferta não são necessários para estar em inglês se a descrição da oferta começar com a frase, &quot; Esta aplicação está disponível apenas em [língua não inglesa]. &quot; Também é normal fornecer um Link Útil para oferecer conteúdo num idioma diferente do usado na listagem de detalhes da Oferta.

Aqui está um exemplo de como a informação de oferta aparece no Azure Marketplace (quaisquer preços listados são apenas para fins e não se destinam a refletir custos reais):

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logótipo
2. Preço
3. Áreas de solução
4. Indústrias
5. Nome da oferta
6. Resumo
7. Descrição
8. Screenshots/vídeos

<br>Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (quaisquer preços listados são apenas para fins e não se destinam a refletir custos reais):

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logótipo
2. Preço
3. Produtos
4. Indústrias
5. Nome da oferta
6. Resumo
7. Descrição
8. Screenshots/vídeos
9. Documentos

### <a name="name"></a>Nome

O nome que aqui entra apresenta como título da sua oferta. Este campo está pré-preenchido com o texto que inseriu na caixa **de pseudónimos Oferta** quando criou a oferta. Pode alterar este nome posteriormente.

O nome:

- Pode ser registada (e pode incluir símbolos de marca registada ou de direitos autorais).
- Não deve ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta. Isto pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa no mercado.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="keywords"></a>Palavras-chave

Introduza até três palavras-chave de pesquisa que sejam relevantes para o seu produto primário e serviço de consultoria. Isto vai facilitar a procura da sua oferta.

### <a name="duration"></a>Duração

Desaça a duração prevista deste compromisso com o seu cliente.

### <a name="contact-information"></a>Informações de Contacto

Deve fornecer o nome, e-mail e número de telefone para um contacto **primário** e **secundário.** Esta informação não é mostrada aos clientes. Está disponível para a Microsoft e pode ser fornecido aos parceiros cloud Solution Provider (CSP).

### <a name="supporting-documents"></a>Documentos comprovativos

Adicione até três (mas pelo menos um) documentos PDF comprovativos para a sua oferta.

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para utilizar com a sua oferta. Todas as imagens devem estar em formato .png. Imagens desfocadas serão rejeitadas.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da loja

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center irá usá-lo para criar um logótipo **pequeno.** Pode substituir opcionalmente isto por uma imagem diferente mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, necessário)
- **Pequeno** (48 x 48 px, opcional)

Estes logótipos são utilizados em diferentes locais da listagem.

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Screenshots (opcional)

Adicione cinco imagens que mostram como a sua oferta funciona. Cada um deve ter 1280 x 720 pixels em tamanho e em formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione quatro vídeos que demonstrem a sua oferta. Insira o nome do vídeo, o seu endereço web (URL) e uma imagem PNG de miniatura do vídeo em tamanho 1280 x 720 pixels.

**Selecione Guardar o projeto** antes de continuar.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Aqui você definirá elementos como preços, mercado e uma chave privada.

1. **Mercado**: Desemaça o mercado em que a sua oferta estará disponível. Só pode selecionar um mercado por oferta.
    1. Para os mercados dos Estados Unidos ou canadá, **selecione Estados de Edição** (ou **províncias)** para especificar onde a sua oferta estará disponível.
2. **Pré-visualização Audiência**: Configurar a **Chave Oculta** usada para definir o público privado para a sua oferta.
3. **Preços**: Especifique se a sua oferta é uma oferta **gratuita** ou **paga.**

    > [!NOTE]
    > As ofertas de serviços de consultoria são apenas para a listagem. Quaisquer transações ocorrerão diretamente, fora do mercado comercial.

4. Para uma oferta paga, especifique o **Preço e a Moeda** e se o preço é **Fixo** ou **Estimado.** Se Estimado, deve especificar na descrição quais os fatores que irão afetar o preço.
5. **Selecione Guardar o projeto** antes de continuar.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter completado todas as secções necessárias da oferta, pode submeter a sua oferta para rever e publicar.

1. Quando estiver pronto para publicar a sua oferta de serviço de consultoria, selecione **Review e publique.**
2. Reveja os detalhes na página final de submissão.
3. Se necessário, escreva uma nota à equipa de certificação se acreditar que algum dos detalhes da sua oferta requer explicação.
4. Quando estiver pronto, **selecione Submeter.**
5. A página **geral da Oferta** mostra em que fase de publicação está a sua oferta.

Para obter mais informações sobre quanto tempo pode esperar que a sua oferta esteja em cada fase editorial, consulte [o estado de publicação da sua oferta de Mercado Comercial.](publishing-status.md)

## <a name="update-your-existing-consulting-service-offers"></a>Atualize as suas ofertas de serviços de consultoria existentes

- [Atualizar uma oferta existente no mercado comercial](update-existing-offer.md)

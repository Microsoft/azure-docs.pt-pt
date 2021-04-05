---
title: Criar uma oferta de aplicativo Power BI no Microsoft AppSource
description: Saiba como criar e publicar uma oferta de aplicação Power BI ao Microsoft AppSource.
author: navits09
ms.author: navits
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/22/2020
ms.openlocfilehash: bff20468e8185073f5c192c1e115bc405dd089eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693621"
---
# <a name="create-a-power-bi-app-offer"></a>Criar uma oferta da aplicação Power BI

Este artigo descreve como criar e publicar uma oferta de aplicação Power BI ao [Microsoft AppSource.](https://appsource.microsoft.com/)

Antes de iniciar, [crie uma conta de Mercado Comercial no Partner Center](create-account.md) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página 'Vista Geral', selecione **+ Nova oferta** Power BI Service  >  **App**.

   ![Ilustra o menu de navegação à esquerda.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> Após a publicação de uma oferta, as edições feitas no Partner Center só aparecem nas lojas online depois de republicarem a oferta. Certifique-se de que é sempre republicante depois de escorção.

> [!IMPORTANT]
> Se a **Power BI Service App** não for mostrada ou ativada, a sua conta não tem permissão para criar este tipo de oferta. Por favor, verifique se cumpriu todos os [requisitos](create-power-bi-app-overview.md) para este tipo de oferta, incluindo o registo de uma conta de desenvolvedor.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos de Gestor de Recursos Azure, se aplicável.
- Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir **aqui o test-offer-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- O pseudónimo Oferta não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-overview"></a>Oferta geral

Esta página mostra uma representação visual dos passos necessários para publicar esta oferta (concluída e futura) e quanto tempo cada passo deve demorar a ser concluído.

Inclui links para realizar operações nesta oferta com base na seleção que faz. Por exemplo:

- Se a oferta for um rascunho - Eliminar oferta de rascunho
- Se a oferta for ao vivo - [Pare de vender a oferta](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Se a oferta estiver em pré-visualização - [Go-live](../review-publish-offer.md#previewing-and-approving-your-offer)
- Se ainda não tiver concluído a assinatura do editor - [Cancele a publicação.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Configuração de oferta

### <a name="customer-leads"></a>Ligações ao cliente

Ao publicar a sua oferta no mercado com o Partner Center, deve conectá-la ao seu sistema de Gestão de Relacionamento com o Cliente (CRM). Isto permite-lhe receber informações de contacto do cliente assim que alguém manifestar interesse ou utilizar o seu produto.

1. Selecione um destino de oportunidades potenciais para onde quer que enviemos as oportunidades potenciais de clientes. O Partner Center suporta os seguintes sistemas crm:

    - [Dinâmica 365](commercial-marketplace-lead-management-instructions-dynamics.md) para Envolvimento com o Cliente
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se o seu sistema CRM não estiver nesta lista, utilize [a Tabela Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou [o ponto final HTTPS](commercial-marketplace-lead-management-instructions-https.md) para armazenar dados de chumbo do cliente. Em seguida, exporte os dados para o seu sistema crm.

2. Ligue a sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a ligação ao destino de chumbo está configurada corretamente. Depois de publicá-lo no Partner Center, validaremos a ligação e enviaremos um teste. Enquanto pré-visualiza a oferta antes de entrar em direto, também pode testar a sua ligação de chumbo tentando comprar a oferta no ambiente de pré-visualização.
4. Certifique-se de que a ligação ao destino principal permanece atualizada para não perder nenhuma pista.

Aqui estão alguns recursos adicionais de gestão de chumbo:

- [Oportunidades potenciais da oferta do marketplace comercial](commercial-marketplace-get-customer-leads.md)
- [Questões comuns sobre gestão de chumbo](../lead-management-faq.md#common-questions-about-lead-management)
- [Erros de configuração de chumbo de resolução de problemas](../lead-management-faq.md#publishing-config-errors)
- [Visão geral da gestão de chumbo](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Certifique-se de que o seu bloqueador pop-up está desligado).

**Selecione Guardar o projeto** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias e indústrias usadas para agrupar a sua oferta no mercado, a sua versão de aplicação e os contratos legais que suportam a sua oferta.

### <a name="category"></a>Categoria

Selecione categorias e subcategorias para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Não se esqueça de descrever como a sua oferta suporta estas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se não for aplicável nenhuma subcategoria à sua oferta, selecione **Não aplicável**.

Consulte a lista completa de categorias e subcategorias na [Listagem de Ofertas Boas Práticas.](../gtm-offer-listing-best-practices.md)

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>Legal

#### <a name="terms-and-conditions"></a>Termos e condições

Para fornecer os seus próprios termos e condições personalizados, insira até 10.000 caracteres na caixa **de Termos e Condições.** Os clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

**Selecione Guardar o rascunho** antes de continuar para a secção seguinte, Ofereça a listagem.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui definirá os detalhes da oferta que são apresentados no mercado. Isto inclui o nome da oferta, descrição, imagens, e assim por diante.

### <a name="language"></a>Linguagem

Selecione o idioma no qual a sua oferta será listada. Atualmente, **inglês (Estados Unidos)** é a única opção disponível.

Defina detalhes do mercado (como nome de oferta, descrição e imagens) para cada idioma/mercado. Selecione o nome idioma/mercado para fornecer esta informação.

> [!NOTE]
> Os detalhes da oferta não são necessários para estar em inglês se a descrição da oferta começar com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é normal fornecer um Link Útil para oferecer conteúdo num idioma diferente do usado na listagem de ofertas.

Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (quaisquer preços listados são apenas para fins e não se destinam a refletir custos reais):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Ilustra como esta oferta aparece no Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logótipo
2. Produtos
3. Categorias
4. Indústrias
5. Endereço de suporte (link)
6. Termos de utilização
7. Política de privacidade
8. Nome da oferta
9. Resumo
10. Description
11. Screenshots/vídeos

### <a name="name"></a>Name

O nome que aqui entra apresenta como título da sua oferta. Este campo está pré-preenchido com o texto que inseriu na caixa **de pseudónimos Oferta** quando criou a oferta. Pode alterar este nome posteriormente.

O nome:

- Pode ser registada (e pode incluir símbolos de marca registada ou de direitos autorais).
- Não deve ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta. Isto pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa no mercado.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Pesquisar palavras-chave

Introduza até três palavras-chave de pesquisa opcionais para ajudar os clientes a encontrar a sua oferta no mercado. Para obter melhores resultados, utilize também estas palavras-chave na sua descrição.

### <a name="helpprivacy-web-addresses"></a>Endereços Web de ajuda/Privacidade

Forneça links para ajudar os clientes a entender melhor a sua oferta.

#### <a name="help-link"></a>Ligação de ajuda

Insira o endereço web onde os clientes podem saber mais sobre a sua oferta.

#### <a name="privacy-policy-url"></a>URL de política de privacidade

Insira o endereço web para a política de privacidade da sua organização. Você é responsável por garantir que a sua oferta está em conformidade com as leis e regulamentos de privacidade. Você também é responsável por publicar uma política de privacidade válida no seu site.

### <a name="contact-information"></a>Informações de Contacto

Deve fornecer o nome, e-mail e número de telefone para um **contacto de suporte** e um contacto de **Engenharia.** Esta informação não é mostrada aos clientes. Está disponível para a Microsoft e pode ser fornecido aos parceiros cloud Solution Provider (CSP).

- Contacto de apoio (obrigatório): Para questões gerais de apoio.
- Contacto de engenharia (obrigatório): Para questões técnicas e questões de certificação.
- Contacto do Programa CSP (opcional): Para revendedores questões relacionadas com o programa CSP.

Na secção **de contacto de Apoio,** forneça o endereço web do **site de Apoio** onde os parceiros possam encontrar suporte para a sua oferta.

### <a name="supporting-documents"></a>Documentos comprovativos

Fornecer pelo menos um e até três documentos de marketing relacionados em formato PDF. Por exemplo, livros brancos, brochuras, listas de verificação ou apresentações.

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para utilizar com a sua oferta. Todas as imagens devem estar em formato PNG. Imagens desfocadas serão rejeitadas.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o `https://upload.xboxlive.com` serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da loja

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center irá usá-lo para criar um logótipo **pequeno.** Pode substituir opcionalmente isto por uma imagem diferente mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, necessário)
- **Pequeno** (48 x 48 px, opcional)

Estes logótipos são utilizados em diferentes locais da listagem:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de ecrã

Adicione pelo menos uma e até cinco imagens que mostram como a sua oferta funciona. Cada um deve ter 1280 x 720 pixels em tamanho e em formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até cinco vídeos que demonstram a sua oferta. Insira o nome do vídeo, o seu endereço web e a imagem PNG do vídeo em 1280 x 720 pixels de tamanho.

#### <a name="additional-marketplace-listing-resources"></a>Recursos de listagem de mercado adicionais

Para saber mais sobre a criação de listas de ofertas, consulte [Offer listing best practices](../gtm-offer-listing-best-practices.md).

## <a name="technical-configuration"></a>Configuração técnica

Promova a sua aplicação no Serviço Power BI para a produção e forneça a ligação de instalador de aplicações Power BI que permite aos clientes instalarem a sua aplicação. Para obter mais informações, consulte [as aplicações publicar com dashboards e relatórios no Power BI](/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Conteúdo suplementar

Forneça informações adicionais sobre a sua oferta para nos ajudar a validá-la. Esta informação não é mostrada aos clientes ou publicada no mercado.

### <a name="validation-assets"></a>Ativos de validação

Opcionalmente, adicione instruções (até 3.000 caracteres) para ajudar a equipa de validação da Microsoft a configurar, conectar e testar a sua aplicação. Inclua configurações típicas, contas, parâmetros ou outras informações que possam ser usadas para testar a opção De Ligar Dados. Esta informação é visível apenas para a equipa de validação e é utilizada apenas para fins de validação.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter completado todas as secções necessárias da oferta, pode submeter a sua oferta para rever e publicar.

No canto superior direito do portal, selecione **'Rever e publicar**.

Na página de comentários pode:

- Consulte o estado de conclusão de cada secção da oferta. Não pode publicar até que todas as secções da oferta estejam marcadas como completas.
  - **Não começou** - A secção ainda não foi iniciada e precisa de ser concluída.
  - **Incompleto** - A secção tem erros que precisam de ser corrigidos ou requer que forneça mais informações. Consulte as secções anteriormente neste documento para obter orientação.
  - **Completo** - A secção tem todos os dados necessários e não há erros. Todas as secções da oferta devem estar completas antes de poder submeter a oferta.
- Forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente. Além disso, forneça quaisquer notas suplementares que sejam úteis para entender a sua oferta.

Para submeter a oferta de publicação, **selecione Publicar**.

Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta estiver disponível para revisão e aprovação. Para publicar a sua oferta ao público, vá ao Partner Center e selecione **Go-live**.

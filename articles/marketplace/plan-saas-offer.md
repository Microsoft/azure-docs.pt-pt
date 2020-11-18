---
title: Como planear uma oferta SaaS para o mercado comercial da Microsoft
description: Como planear um novo software como uma oferta de serviço (SaaS) para listagem ou venda no Microsoft AppSource, Azure Marketplace, ou através do programa Cloud Solution Provider (CSP) utilizando o programa de marketplace comercial no Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2020
ms.openlocfilehash: 5a646f7f42a2af8dcf15f7b7b2cd700e159fe88d
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734400"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>Como planear uma oferta saaS para o mercado comercial

Este artigo explica as diferentes opções e requisitos para a publicação de um software como um serviço (SaaS) ao mercado comercial da Microsoft. A SaaS oferece soluções de software de entrega e licença aos seus clientes através de uma subscrição online em vez de instalação local em computadores individuais. Este artigo irá ajudá-lo a preparar a sua oferta de publicação no mercado comercial com o Partner Center.

## <a name="listing-options"></a>Opções de listagem

À medida que se prepara para publicar uma nova oferta SaaS, tem de decidir qual a opção _de listagem_ a escolher. A opção de listagem que escolher determina as informações adicionais que necessitará de fornecer à medida que cria a sua oferta no Partner Center. Definirá a sua opção de listagem na página **de configuração da Oferta** como explicado em Como criar uma oferta [SaaS no mercado comercial.](create-new-saas-offer.md)

A tabela seguinte mostra as opções de listagem para ofertas saas no mercado comercial.

| Opção de listagem | Processo de transação |
| ------------ | ------------- |
| Contacte-me | O cliente contacta-o diretamente a partir de informações na sua listagem.``*`` |
| Avaliação gratuita | O cliente é redirecionado para o seu URL-alvo através do Azure Ative Directory (Azure AD).``*`` |
| Obtê-lo agora (Grátis) | O cliente é redirecionado para o seu URL alvo via Azure AD.``*`` |
| Venda através da Microsoft  | As ofertas vendidas através da Microsoft são chamadas de ofertas _transacionáveis._ Uma oferta que é transacionável é aquela em que a Microsoft facilita a troca de dinheiro por uma licença de software em nome da editora. Cobramos ofertas ao SaaS utilizando o modelo de preços que escolhe e gerimos transações de clientes em seu nome. As taxas de utilização da infraestrutura Azure são cobradas diretamente a si, o parceiro. Deve ter em conta os custos de infraestrutura no seu modelo de preços. Isto é explicado mais detalhadamente na [faturação do SaaS abaixo.](#saas-billing)  |
|||

``*`` Os editores são responsáveis por apoiar todos os aspetos da transação de licença de software, incluindo, mas não se limitando à ordem, realização, medição, faturação, faturação, pagamento e cobrança.

Para obter mais informações sobre estas opções de listagem, consulte [as capacidades de transação do mercado comercial.](marketplace-commercial-transaction-capabilities-and-considerations.md)

Após a publicação da sua oferta, a opção de listagem que escolheu para a sua oferta aparece como um botão no canto superior esquerdo da página de listagem da sua oferta. Por exemplo, a imagem que se segue mostra uma página de listagem de oferta no Azure Marketplace com os botões **Contacte-me** e **test drive.**

![Ilustra uma listagem de oferta na loja online.](./media/listing-options.png)

## <a name="technical-requirements"></a>Requisitos técnicos

Os requisitos técnicos diferem consoante a opção de listagem que escolher para a sua oferta.

A opção de listagem _Contacte-me_ não tem requisitos técnicos. Tem a opção de ligar um sistema de gestão de relacionamento com o cliente (CRM) para gerir os leads de clientes. Isto é descrito na secção [de condutores de clientes,](#customer-leads) mais tarde neste artigo.

O _Get it now (Free)_, Free _trial_, and Sell _through Microsoft_ listing options têm os seguintes requisitos técnicos:

- A sua aplicação SaaS deve ser uma solução multitenante.
- Pode ativar tanto as Contas Microsoft (MSA) como [o Azure Ative Directory (AZure AD)](https://azure.microsoft.com/services/active-directory/) para autenticar utilizadores.
- Tens de criar uma página de aterragem. Depois de um utilizador comprar a sua oferta, são direcionados para a página de aterragem. Isto ajuda-os a completar qualquer provisão adicional ou configuração que seja necessária. Para obter orientações sobre a criação da página de aterragem, consulte estes artigos:
  - [Construa a página de aterragem para a sua oferta de SaaS transacionável no mercado comercial](azure-ad-transactable-saas-landing-page.md)
  - [Construa a página de aterragem para a sua oferta saas gratuita ou experimental no mercado comercial](azure-ad-free-or-trial-landing-page.md)

Estes requisitos técnicos adicionais aplicam-se apenas à opção de listagem _Sell através_ da Microsoft (transactable):

- A AZure AD com uma única gestão de identidade e autenticação de assinatura (SSO) é necessária para o utilizador de compra aceder à página de aterragem. Para obter orientações [detalhadas, consulte a Azure AD e as ofertas transacionáveis da SaaS no mercado comercial.](azure-ad-saas.md)
- Tem de utilizar as [APIs de Realização saaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md) para integrar-se com o Azure Marketplace e o Microsoft AppSource. Tem de expor um serviço que possa interagir com a subscrição do SaaS para criar, atualizar e eliminar uma conta de utilizador e um plano de serviço. As alterações da API crítica devem ser suportadas dentro de 24 horas. As alterações não críticas da API serão lançadas periodicamente. Diagramas e explicações detalhadas que descrevem a utilização dos campos recolhidos estão disponíveis em documentação para as [APIs](./partner-center-portal/pc-saas-fulfillment-api-v2.md).
- Deve criar pelo menos um plano para a sua oferta. O seu plano tem um preço baseado no modelo de preços que seleciona antes de publicar: _taxa fixa_ ou _por utilizador._ Mais detalhes sobre [os planos](#plans) são fornecidos mais tarde neste artigo.
- O cliente pode cancelar a sua oferta a qualquer momento.

### <a name="technical-information"></a>Informação técnica

Se estiver a criar uma oferta transacionável, terá de recolher as seguintes informações para a página **de configuração técnica.** Se optar por processar transações de forma independente em vez de criar uma oferta transacionável, ignore esta secção e vá a [Test drives](#test-drives).

- **URL da página de aterragem**: O URL do site SaaS (por exemplo: `https://contoso.com/signup` ) para o que os utilizadores serão direcionados após a aquisição da sua oferta no mercado comercial, desencadeando o processo de configuração a partir da recém-criada subscrição SaaS. Este URL receberá um token que pode ser usado para chamar as APIs de cumprimento para obter detalhes de provisionamento para a sua página de registo interativo.

  Este URL será chamado com o parâmetro de identificação de compra de mercado que identifica exclusivamente a compra específica do cliente SaaS. Tem de trocar este token pelos correspondentes dados de subscrição do SaaS utilizando a [API de resolução](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription). Esses detalhes e quaisquer outros que deseje recolher devem ser usados como parte de uma página web interativa do cliente, construída na sua experiência para completar o registo do cliente e ativar a sua compra. Nesta página, o utilizador deve inscrever-se através da autenticação de um clique utilizando o Azure Ative Directory (Azure AD).

  Este URL com parâmetro de identificação de compra de mercado também será chamado quando o cliente lançar uma experiência Gerida SaaS a partir do portal Azure ou M365 Admin Center. Deve lidar com ambos os fluxos: quando o token é fornecido pela primeira vez após uma nova compra de cliente, e quando é fornecido novamente para um cliente existente que gere a sua solução SaaS.

    A página de Desembarque que configurar deve estar a funcionar 24 horas por dia, 7 horas por dia. Esta é a única forma de ser notificado sobre novas compras das suas ofertas SaaS feitas no mercado comercial, ou pedidos de configuração para uma subscrição ativa de uma oferta.

- **Connection webhook**: Para todos os eventos assíncronos que a Microsoft precisa de enviar para si (por exemplo, quando uma subscrição saaS foi cancelada), exigimos que forneça um URL webhook de ligação. Vamos chamar este URL para notificá-lo sobre o evento.

  O webhook que fornece deve estar a funcionar 24 horas por dia, 7 horas por dia. Esta é a única forma de ser notificado sobre as atualizações sobre as assinaturas SaaS dos seus clientes adquiridas através do mercado comercial.

  > [!NOTE]
  > Dentro do portal Azure, exigimos que crie uma aplicação de [diretório ativo Azure (Azure AD)](../active-directory/develop/howto-create-service-principal-portal.md) para permitir que um ID da App Azure seja usado para autenticar a ligação entre os nossos dois serviços. Para encontrar o ID do [inquilino,](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)vá ao seu Diretório Ativo Azure e selecione **Propriedades,** em seguida, procure o número de identificação do diretório listado. Por exemplo, `50c464d3-4930-494c-963c-1e951d15360e`.

- **ID do inquilino do Azure Ative Directory:**(também conhecido como ID do diretório). Dentro do portal Azure, exigimos que [registe uma aplicação Azure Ative Directory (AD)](../active-directory/develop/howto-create-service-principal-portal.md) para que possamos adicioná-la à lista de controlo de acesso (ACL) da API para garantir que está autorizado a chamá-la. Para encontrar o ID do inquilino para a sua aplicação Azure Ative Directory (AD), vá à lâmina [de registos](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App em Azure Ative Directory. Na coluna **'Mostrar' o nome,** selecione a aplicação. Em seguida, procure o número de ID do **Diretório (inquilino)** listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

- ID de aplicação do **Azure Ative Directory**: Também precisa do seu [ID de aplicação](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Para obter o seu valor, aceda à lâmina de [registos](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App no Azure Ative Directory. Na coluna **'Mostrar' o nome,** selecione a aplicação. Em seguida, procure o número de ID de aplicação (cliente) listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

  O ID da aplicação Azure está associado com o seu ID de editor na sua conta Partner Center. Deve utilizar o mesmo ID de aplicação para todas as ofertas nessa conta.

  > [!NOTE]
  > Se a editora tiver duas ou mais contas diferentes no Partner Center, devem ser utilizados dois ou mais IDs de aplicações AD Azure diferentes, cada um para uma das contas. Cada conta de parceiro no Partner Center deve usar um ID de aplicação AD AZure único para todas as ofertas saaS que são publicadas através desta conta.

## <a name="test-drives"></a>Versões de teste
Pode optar por ativar um test drive para a sua aplicação SaaS. Os test drives dão aos clientes acesso a um ambiente pré-configurado por um número fixo de horas. Pode ativar as unidades de teste para qualquer opção de publicação, no entanto esta funcionalidade tem requisitos adicionais. Para saber mais sobre test drives, veja [o que é um test drive?](what-is-test-drive.md) Para obter informações sobre a configuração de diferentes tipos de test drives, consulte a [configuração técnica do test drive](test-drive-technical-configuration.md).

> [!TIP]
> Um test drive é diferente de um [teste gratuito.](plans-pricing.md#free-trials) Pode oferecer um test drive, teste gratuito, ou ambos. Ambos fornecem aos seus clientes a sua solução para um período de tempo fixo. Mas, um test drive também inclui uma visita prática e auto-guiada às principais características e benefícios do seu produto sendo demonstrados num cenário de implementação real.

## <a name="customer-leads"></a>Ligações ao cliente

Tem de ligar a sua oferta ao seu sistema de gestão de relacionamento com o cliente (CRM) para recolher informações do cliente. O cliente será solicitado permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e loja online onde encontraram a sua oferta, serão enviados para o sistema CRM que configuraste. O mercado comercial suporta uma variedade de sistemas CRM, juntamente com a opção de usar uma tabela Azure ou configurar um ponto final HTTPS utilizando o Power Automate.

Pode adicionar ou modificar uma ligação CRM a qualquer momento durante ou após a criação da oferta. Para obter orientações detalhadas, consulte [os leads do Cliente da sua oferta de mercado comercial.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

## <a name="selecting-an-online-store"></a>Selecionando uma loja online

Quando publicar uma oferta SaaS, será listada no Microsoft AppSource, Azure Marketplace, ou ambos. Cada loja online serve requisitos exclusivos do cliente. O AppSource é para soluções de negócio e o Azure Marketplace é para soluções de TI. O seu tipo de oferta, capacidades de transação e categorias determinarão onde a sua oferta será publicada. As categorias e subcategorias são mapeadas para cada loja online com base no tipo de solução. Para obter informações detalhadas sobre opções de listagem suportadas por lojas online, consulte [opções de Listagem e preços por loja online.](determine-your-listing-type.md#listing-and-pricing-options-by-online-store) Para obter mais informações sobre categorias e subcategorias, consulte [categorias e subcategorias no mercado comercial.](categories.md)

## <a name="legal-contracts"></a>Contratos legais

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para os fornecedores de software, a Microsoft oferece um contrato padrão que pode usar para as suas ofertas no mercado comercial. Quando oferece o seu software ao abrigo do contrato padrão, os clientes só precisam de o ler e aceitar uma vez, e não tem de criar termos e condições personalizados.

Se optar por utilizar o contrato padrão, tem a opção de adicionar termos de emenda universal e até 10 alterações personalizadas ao contrato padrão. Também pode usar os seus próprios termos e condições em vez do contrato padrão. Você vai gerir estes detalhes na página **Propriedades.** Para obter informações detalhadas, consulte [o contrato Standard para o mercado comercial da Microsoft.](standard-contract.md)

> [!NOTE]
> Depois de publicar uma oferta utilizando o contrato padrão para o mercado comercial, não pode utilizar os seus próprios termos e condições personalizados. É um cenário "ou". Ou oferece a sua solução de acordo com o contrato padrão ou os seus próprios termos e condições. Se quiser modificar os termos do contrato padrão, pode fazê-lo através de Alterações Padrão contratuais.

## <a name="offer-listing-details"></a>Oferecer detalhes da listagem

Quando [criar uma nova oferta SaaS](create-new-saas-offer.md) no Partner Center, introduzirá texto, imagens, vídeos opcionais e outros detalhes na página **de listagem da Oferta.** Esta é a informação que os clientes verão quando descobrirem a sua oferta no mercado comercial, como mostra o exemplo seguinte.

:::image type="content" source="./media/example-saas-1.png" alt-text="Ilustra como esta oferta aparece no Microsoft AppSource.":::

**Descrições de chamadas**

1. Logótipo
2. Categorias
3. Indústrias
4. Endereço de suporte (link)
5. Termos de utilização
6. Política de privacidade
7. Nome da oferta
8. Resumo
9. Description
10. Screenshots/vídeos
11. Documentos

O exemplo a seguir mostra uma listagem de oferta no portal Azure.

![Ilustra uma listagem de oferta no portal Azure.](./media/example-managed-services.png)

**Chame as descrições**

1. Título
1. Descrição
1. Ligações úteis
1. Capturas de ecrã

> [!NOTE]
> O conteúdo da listagem de ofertas não é necessário para estar em inglês se a descrição da oferta começar com a frase "Esta aplicação está disponível apenas em [língua não inglesa]".

Para ajudar a criar a sua oferta mais facilmente, prepare alguns destes itens com antecedência. Os seguintes itens são necessários, salvo indicação em contrário.

- **Nome**: Este nome aparecerá como o título da sua listagem de oferta no mercado comercial. O nome pode ser marcado. Não pode conter emojis (a menos que sejam símbolos de marca e direitos de autor) e deve ser limitado a 50 caracteres.
- Resumo dos **resultados** da pesquisa : Descreva a finalidade ou função da sua oferta como uma única frase sem quebras de linha em 100 caracteres ou menos. Este resumo é utilizado nos resultados de pesquisa de anúncios de mercado comercial.
- **Descrição**: Esta descrição será exibida na lista(s) visão geral da listagem do mercado comercial. Considere incluir uma proposta de valor, benefícios-chave, base de utilizadores pretendida, qualquer categoria ou associações do setor, oportunidades de compra in-app, quaisquer divulgações necessárias e um link para saber mais.

    Esta caixa de texto tem controlos de editores de texto ricos que pode usar para tornar a sua descrição mais envolvente. Também pode utilizar tags HTML para formatar a sua descrição. Pode introduzir até 3.000 caracteres de texto nesta caixa, incluindo marcação HTML. Para obter dicas adicionais, consulte [Escrever uma excelente descrição da aplicação.](/windows/uwp/publish/write-a-great-app-description)

- **Instruções de início**: Se optar por vender a sua oferta através da Microsoft (oferta transacionável), este campo é necessário. Estas instruções ajudam os clientes a ligarem-se à sua oferta SaaS. Pode adicionar até 3.000 caracteres de texto e links para documentação online mais detalhada.
- **Procurar palavras-chave** (opcional): Forneça até três palavras-chave de pesquisa que os clientes podem usar para encontrar a sua oferta nas lojas online. Não é necessário incluir a oferta **Nome** e **Descrição**: esse texto está automaticamente incluído na pesquisa.
- **Ligação de política de privacidade**: O URL para a política de privacidade da sua empresa. Você deve fornecer uma política de privacidade válida e é responsável por garantir que a sua aplicação está em conformidade com as leis e regulamentos de privacidade.
- **Informações de contacto**: Deve fornecer os seguintes contactos da sua organização:
  - **Contacto de suporte**: Forneça o nome, telefone e e-mail para os parceiros da Microsoft usarem quando os seus clientes abrirem os bilhetes. Também deve incluir o URL para o seu site de suporte.
  - **Contacto de engenharia**: Forneça o nome, telefone e e-mail para que a Microsoft utilize diretamente quando houver problemas com a sua oferta. Esta informação de contacto não está listada no mercado comercial.
  - **Contacto do Programa CSP** (opcional): Forneça o nome, telefone e e-mail se optar pelo programa CSP, para que esses parceiros possam contactá-lo com quaisquer questões. Também pode incluir um URL nos seus materiais de marketing.
- **Links úteis** (opcional): Pode fornecer links para vários recursos para os utilizadores da sua oferta. Por exemplo, fóruns, PERGUNTAS Frequentes e notas de lançamento.
- **Documentos comprovativos**: Pode fornecer até três documentos voltados para o cliente, tais como papel branco, brochuras, checklists ou apresentações de PowerPoint.
- **Meios – Logotipos**: Forneça um ficheiro PNG para o logotipo **grande.** O Partner Center utilizará isto para criar um logótipo **Pequeno** e **Médio.** Pode substituir opcionalmente estas imagens por diferentes imagens mais tarde.

   - Grande (de 216 x 216 a 350 x 350 px, necessário)
   - Médio (90 x 90 px, opcional)
   - Pequeno (48 x 48 px, opcional)

  Estes logótipos são utilizados em diferentes locais nas lojas online:

  - O logótipo Small aparece nos resultados de pesquisa do Azure Marketplace e na página principal do Microsoft AppSource e na página de resultados de pesquisa.
  - O logótipo Medium aparece quando cria um novo recurso no Microsoft Azure.
  - O logotipo Large aparece na sua página de listagem de ofertas no Azure Marketplace e microsoft AppSource.

- **Meios de comunicação - Screenshots**: Deve adicionar pelo menos uma e até cinco imagens com os seguintes requisitos, que mostram como a sua oferta funciona:
  - 1280 x 720 pixels
  - .png ficheiro
  - Deve incluir uma legenda
- **Meios - Vídeos** (opcional): Pode adicionar até quatro vídeos com os seguintes requisitos, que demonstram a sua oferta:
  - Name
  - URL: Deve ser hospedado apenas no YouTube ou Vimeo.
  - Miniatura: ficheiro de .png 1280 x 720

> [!Note]
> A sua oferta deve atender às [políticas gerais de certificação](/legal/marketplace/certification-policies#100-general) do mercado comercial e ao [software como uma política de serviços](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) a publicar no mercado comercial.

## <a name="preview-audience"></a>Audiência de pré-visualização
Um público de pré-visualização pode aceder à sua oferta antes de ser publicado ao vivo nas lojas online de forma a testar a funcionalidade de ponta a ponta antes de a publicar ao vivo. Na página de **audiência de pré-visualização,** pode definir um público de pré-visualização limitado. Esta definição não está disponível se optar por processar transações de forma independente em vez de vender a sua oferta através da Microsoft. Em caso afirmativo, pode saltar esta secção e ir a [oportunidades de venda adicionais.](#additional-sales-opportunities)

> [!NOTE]
> Um público de pré-visualização difere de um plano privado. Um plano privado é aquele que disponibiliza apenas para um público específico que escolher. Isto permite-lhe negociar um plano personalizado com clientes específicos. Para mais informações, consulte a secção seguinte: Planos.

Pode enviar convites para endereços de e-mail da Conta Microsoft (MSA) ou do Azure Ative Directory (Azure AD). Adicione até 10 endereços de e-mail manualmente ou importe até 20 com um ficheiro .csv. Se a sua oferta já estiver ao vivo, ainda pode definir um público de pré-visualização para testar quaisquer alterações ou atualizações da sua oferta.

## <a name="plans"></a>Planos

Ofertas transacionáveis requerem pelo menos um plano. Um plano define o âmbito e os limites da solução, e os preços associados. Pode criar vários planos para a sua oferta para dar aos seus clientes diferentes opções técnicas e de preços. Se optar por processar transações de forma independente em vez de criar uma oferta transacionável, a página **Planos** não é visível. Em caso afirmativo, ignore esta secção e vá a [oportunidades de venda adicionais.](#additional-sales-opportunities)

Consulte [planos e preços para ofertas de mercado comercial](plans-pricing.md) para orientação geral sobre planos, incluindo modelos de preços, testes gratuitos e planos privados. As seguintes secções discutem informações adicionais específicas às ofertas do SaaS.

### <a name="saas-pricing-models"></a>Modelos de preços de SaaS

As ofertas da SaaS podem utilizar um de dois modelos de preços com cada plano: taxa _fixa_ ou _por utilizador._ Todos os planos da mesma oferta devem ser associados ao mesmo modelo de preços. Por exemplo, uma oferta não pode ter um plano que seja fixo e outro plano que seja por utilizador.

**Taxa fixa** – Permitir o acesso à sua oferta com um preço fixo único mensal ou anual. Isto é por vezes referido como preços baseados no site. Com este modelo de preços, pode definir opcionalmente planos medidos que usam o serviço de medição de mercado API para cobrar aos clientes pelo uso que não está coberto pela taxa fixa. Para obter mais informações sobre a faturação medido, consulte [a faturação medido para o SaaS utilizando o serviço de medição do mercado comercial.](./partner-center-portal/saas-metered-billing.md) Também deve utilizar esta opção se o comportamento de utilização do seu serviço SaaS estiver em rajadas.

**Por utilizador** – Ative o acesso à sua oferta com um preço baseado no número de utilizadores que podem aceder à oferta ou ocupar lugares. Com este modelo baseado no utilizador, pode definir o número mínimo e máximo de utilizadores suportados pelo plano. Pode criar vários planos para configurar diferentes pontos de preço com base no número de utilizadores. Estes campos são opcionais. Se não for selecionado, o número de utilizadores será interpretado como não tendo um limite (min de 1 e máximo de tantos quantos o seu serviço pode suportar). Estes campos podem ser editados como parte de uma atualização do seu plano.

> [!IMPORTANT]
> Após a publicação da sua oferta, não pode alterar o modelo de preços. Além disso, todos os planos para a mesma oferta devem partilhar o mesmo modelo de preços.

### <a name="saas-billing"></a>Faturação de SaaS

Para aplicações SaaS que funcionam na subscrição do Azure (da editora), o uso da infraestrutura é faturado diretamente para si; os clientes não vêem taxas reais de utilização da infraestrutura. Deve juntar as taxas de utilização da infraestrutura Azure nos preços da licença de software para compensar o custo da infraestrutura que implementou para executar a solução.

A aplicação SaaS oferece ofertas que são vendidas através do suporte da Microsoft, faturação mensal ou anual, com base numa taxa fixa, por utilizador ou taxas de consumo utilizando o [serviço de faturação medido.](./partner-center-portal/saas-metered-billing.md) O mercado comercial opera num modelo de agência, em que os editores fixam preços, a Microsoft cobra aos clientes, e a Microsoft paga receitas aos editores enquanto retém uma taxa de agência.

O exemplo a seguir mostra uma repartição de custos e pagamentos para demonstrar o modelo da agência. Neste exemplo, a Microsoft fatura $100,00 ao cliente pela sua licença de software e paga $80,00 à editora.

| O custo da sua licença | $100 por mês |
| ------------ | ------------- |
| Custo de utilização azul (D1/1-Core) | Cobrado diretamente para o editor, não o cliente |
| O cliente é faturado pela Microsoft | $100,00 por mês (A Editora deve ter em conta quaisquer custos de infraestrutura incorridas ou pass-through na taxa de licença) |
| **Contas da Microsoft** | **$100 por mês** |
| Microsoft paga-lhe 80% do custo da sua licença<br>`*` Para aplicações SaaS qualificadas, a Microsoft paga 90% do custo da sua licença| $80,00 por mês<br>``*`` $90,00 por mês |
|||

**`*` Taxa reduzida de serviço no Mercado** – Para certas ofertas da SaaS que publicou no mercado comercial, a Microsoft reduzirá a sua Taxa de Serviço de Marketplace de 20% (conforme descrito no Microsoft Publisher Agreement) para 10%. Para que a sua(s) oferta(s) se qualifique, a sua(s) oferta(s) deve ter sido designada pela Microsoft como Azure IP Co-venda incentivado. A elegibilidade deve ser cumprida pelo menos cinco (5) dias úteis antes do final de cada mês civil para receber a Taxa reduzida de Serviço do Mercado para o mês. A Taxa reduzida de Serviço de Marketplace também se aplica à Azure IP Co-vender VMs, Apps Geridas e quaisquer outras ofertas de ias transacionáveis qualificadas disponibilizadas através do mercado comercial.

## <a name="additional-sales-opportunities"></a>Oportunidades de venda adicionais

Pode optar por optar por canais de marketing e vendas suportados pela Microsoft. Ao criar a sua oferta no Partner Center, verá dois separadores no final do processo:

- **Revender através de CSPs**: Utilize esta opção para permitir que os parceiros da Microsoft Cloud Solution Providers (CSP) revendam a sua solução como parte de uma oferta agregada. Consulte [o programa Cloud Solution Provider](cloud-solution-providers.md) para obter mais informações.

- **Co-vender com** a Microsoft: Esta opção permite que as equipas de vendas da Microsoft considerem a sua solução elegível para co-venda ip ao avaliar as necessidades dos seus clientes. Consulte [a opção de Co-venda no Partner Center](./partner-center-portal/commercial-marketplace-co-sell.md) para obter informações detalhadas sobre como preparar a sua oferta para avaliação.

## <a name="next-steps"></a>Passos seguintes

- [Como criar uma oferta SaaS no mercado comercial](create-new-saas-offer.md)
- [Melhores práticas de listagem de ofertas](gtm-offer-listing-best-practices.md)

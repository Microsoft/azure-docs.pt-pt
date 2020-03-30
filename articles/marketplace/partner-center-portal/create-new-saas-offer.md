---
title: Criar uma nova oferta SaaS no Mercado Comercial
description: Como criar uma nova oferta de Software como Serviço (SaaS) para listagem ou venda no Azure Marketplace, AppSource ou através do programa Cloud Solution Provider (CSP) utilizando o portal Mercado Comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 3f2201db4b78ba61ba15608a676a8b9f8f5e612a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286205"
---
# <a name="create-a-new-saas-offer"></a>Criar uma nova oferta SaaS

Para começar a criar software como um Serviço (SaaS) oferece, certifique-se de que [primeiro cria uma conta partner center](./create-account.md) e abre o dashboard do Mercado [Comercial,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)com o separador **Overview** selecionado.

![Dashboard do Mercado Comercial no Partner Center](./media/new-offer-overview.png)

>[!Note]
> Uma vez publicada uma oferta, as edimas para a oferta feita no Partner Center só serão atualizadas no sistema e as fachadas da loja após a republicação. Certifique-se de submeter a oferta para publicação depois de fazer alterações.

Selecione a + **Nova oferta...** botão, em seguida, selecione o Software como um item de menu **de serviço.**

Se selecionar outro tipo de oferta, poderá ser redirecionado para o portal de [parceiros](https://cloudpartner.azure.com/)de nuvem mais antigo . Apenas as ofertas SaaS e Dynamics 365 estão disponíveis no portal Mercado Comercial no Partner Center neste momento.

![Criar janela de oferta no Partner Center](./media/new-offer-click.png)

A caixa de diálogo **new offer** é exibida.

![Nova caixa de diálogo de oferta](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>Oferecer ID e pseudónimo

- **ID oferta**: Identificador único para cada oferta na sua conta. Este ID será visível para os clientes no endereço URL para a oferta de mercado e modelos do Gestor de Recursos Azure (se aplicável). O ID da oferta deve ser minúsculo, alfanumérico (incluindo hífenes e sublinhados, mas sem espaço branco). O **ID da Oferta** está limitado a 50 caracteres e não pode ser alterado depois de selecionar *Criar*.  
Exemplo: oferta de teste-1
<br>Resultando no URL:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Oferta pseudónimo**: O nome usado para se referir à oferta dentro do portal Partner Center. Este nome não será usado no mercado e é diferente do Nome da *Oferta* e outros valores que serão mostrados aos clientes. Este valor não pode ser alterado depois de selecionar *Criar*.

<br>Exemplo: Oferta de teste 1&#8482;

Selecione **Criar**.  É criada uma página **de visão geral** da Oferta para esta oferta.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Visão geral da oferta

A página **geral da Oferta** inclui:

- O **estado editorial** apresenta uma representação visual dos passos necessários para publicar esta oferta e quanto tempo cada passo demorará a ser concluído. Os ícones incompletos do passo da publicação serão acinzentados.

- O menu **de visão geral da Oferta** contém uma lista de links para a realização de operações nesta oferta. Esta lista de operações mudará com base na seleção que fizer para a sua oferta.  
    - Se a oferta for um rascunho - Eliminar o projeto
    - Se a oferta estiver ao vivo - Pare de vender oferta
    - Se a oferta estiver em pré-visualização - Go-live
    - Se ainda não tiver concluído o signo da editora - Cancele a publicação

## <a name="offer-setup"></a>Configuração de oferta

O separador **de configuração da Oferta** pede as seguintes informações. Selecione **Guardar** depois de completar estes campos.

- **Gostaria de vender através da Microsoft?** (Sim/Não)
    - **Sim,** gostaria de vender a sua oferta através da Microsoft, com a Microsoft a hospedar transações de marketplace em seu nome; ou 
    - **Não,** prefere apenas listar a sua oferta através dos mercados, processando quaisquer transações monetárias independentemente da Microsoft.

### <a name="sell-through-microsoft"></a>Vender através da Microsoft

A venda através da Microsoft proporciona uma melhor descoberta e aquisição de clientes, permite que a Microsoft apresente transações de marketplace em seu nome, e aproveita as capacidades de comércio globalmente disponíveis da Microsoft.

#### <a name="saas-offer-requirements"></a>Requisitos de oferta SaaS

Para listar o Software como um Serviço (SaaS) oferece com o Mercado Comercial no Partner Center, devem ser cumpridos os seguintes critérios:

- A sua oferta deve utilizar o [Azure Ative Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) para gestão de identidade e autenticação.
- A sua oferta deve utilizar APIs de [cumprimento saas](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para integrar com o Mercado Azure.
- Para obter requisitos mais extensos, consulte o Guia editorial da [Oferta SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Opções de preços e faturação saaS
Com as soluções SaaS a funcionar na subscrição do Azure da editora, as taxas de licença pagas pelos clientes incluem o custo da infraestrutura em que o software é implementado. O uso da infraestrutura Azure é gerido e faturado para si, o parceiro, diretamente. As taxas reais de utilização da infraestrutura não são vistas pelo cliente. Os editores devem agregar as taxas de utilização da infraestrutura Azure nos seus preços de licença de software. 

O SaaS oferece faturação mensal ou anual de suporte com base numa taxa fixa, por utilizador ou taxas de consumo utilizando o serviço de faturação medido. O mercado comercial da Microsoft opera num modelo de agência, através do qual as editoras fixam preços, a Microsoft fatura clientes e a Microsoft paga receitas à editora enquanto retém uma taxa de agência.

A tabela que se segue mostra um exemplo de repartição de custos e pagamentos para demonstrar o modelo da agência.

|**O custo da sua licença**|**$100 por mês**|
|:---|:---|
|Custo de utilização do Azure (D1/1-Core)|Faturado diretamente para o editor, não para o cliente|
|Cliente é cobrado pela Microsoft|$100,00 por mês (A Editora deve prestar contas de quaisquer custos de infraestrutura incorridos ou transitados na taxa de licença)|

|**Contas da Microsoft**|**$100 por mês**|
|:---|:---|
|Microsoft paga-lhe 80% do custo da sua licença <br>**Para aplicações SaaS qualificadas, a Microsoft paga 90% do custo da sua licença*|$80,00 por mês <br>*$* 90,00 por mês*|

- Neste exemplo, a Microsoft cobra 100,00 dólares ao cliente pela sua licença de software e paga 80,00 dólares à editora.
- Os parceiros que se qualificaram para a **Taxa de Serviço** de Mercado Reduzido verão uma taxa de transação reduzida nas ofertas saaS de maio de 2019 a junho de 2020. Neste cenário, a Microsoft cobra $100,00 pela sua licença de software e paga $90,00 à editora.

> [!NOTE]
> **Taxa reduzida de serviço no mercado**: Para certas ofertas saaS que publicou no nosso Mercado Comercial, a Microsoft reduzirá a sua Taxa de Serviço de Mercado de 20% (conforme descrito no Microsoft Publisher Agreement) para 10%. Para que a sua oferta se qualifique, pelo menos uma das suas ofertas deve ter sido designada pela Microsoft como sendo a co-venda ip pronta ou a co-venda IP priorizada.  A elegibilidade deve ser satisfeita pelo menos cinco (5) dias úteis antes do final de cada mês civil, a fim de receber esta taxa reduzida de serviço ao mercado para o mês.  A Taxa de Serviço de Mercado Reduzido não se aplica a VMs, Aplicações Geridas ou quaisquer outros produtos disponibilizados através do nosso Mercado Comercial.  A Taxa de Serviço de Mercado Reduzido só estará disponível para ofertas qualificadas para taxas de licença cobradas pela Microsoft entre 1 de maio de 2019 e 30 de junho de 2020.  Após este tempo, a Taxa de Serviço do Mercado voltará ao seu valor normal.

### <a name="list-through-microsoft"></a>Lista através da Microsoft

Promova o seu negócio com a Microsoft criando uma listagem de marketplace. Selecionar apenas para listar a sua oferta e não transacionar através da Microsoft significa que a Microsoft não participa diretamente em transações de licenças de software. Não existe uma taxa de transação associada e a editora mantém 100% de quaisquer taxas de licenciamento de software cobradas ao cliente. No entanto, a editora é responsável por apoiar todos os aspetos da transação de licença de software, incluindo, mas não se limitando a: cumprimento da encomenda, medição, faturação, faturação, pagamento e cobrança.

- **Como pretende que potenciais clientes interajam com esta oferta de listagem?**

##### <a name="get-it-now-free"></a>Obtê-lo agora (grátis)
Enumere a sua oferta gratuitamente aos clientes, fornecendo um URL válido (começando por *http* ou *https)* onde possam obter um teste através da autenticação de um clique utilizando o [Azure Ative Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Por exemplo: `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>Julgamento gratuito (listagem)
Enumere a sua oferta aos clientes com um link para um teste gratuito, fornecendo um URL válido (começando por *http* ou *https),* onde eles podem obter um teste através [da autenticação de um clique usando o Azure Ative Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Por exemplo: `https://contoso.com/trial/saas-app`. Os testes gratuitos de listagem de ofertas são criados, geridos e configurados pelo seu serviço e não têm subscrições geridas pela Microsoft.

> [!NOTE]
> Os tokens que a sua aplicação receberá através do seu link experimental só podem ser utilizados para obter informações do utilizador através do Azure AD para automatizar a criação de conta na sua aplicação. As Contas Microsoft (MSA) não são suportadas para autenticação utilizando este símbolo.

##### <a name="contact-me"></a>Contacte-me
Recolha informações de contacto com o cliente ligando o seu sistema de Gestão de Relacionamento com o Cliente (CRM). Será solicitado ao cliente permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e fonte de mercado onde encontraram a sua oferta, serão enviados para o sistema CRM que configura. Para mais informações sobre a configuração do seu CRM, consulte [Connect lead management](#connect-lead-management).

## <a name="example-marketplace-offer-listing"></a>Exemplo de listagem de oferta do marketplace

![Lista de ofertas de mercado de exemplo com notas](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Ativar um test drive

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "tentar antes de comprar", resultando numa conversão acrescida e na geração de leads altamente qualificados. [Saiba mais sobre test drives.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Ativar um test drive** (caixa de verificação)

Ao permitir o test drive, será-lhe pedido que configure um ambiente de demonstração para os clientes experimentarem a sua oferta por um período de tempo fixo. 

#### <a name="test-drive-resources"></a>Recursos de test drive
- [Test Drive Técnicas Boas Práticas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Test Drive Marketing Boas Práticas](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral do test drive uma página](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Ligar a gestão de chumbo

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Recursos adicionais de gestão de chumbo
- [FAQs de gestão de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros de configuração comuns de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral da gestão de chumbo Um Pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Lembre-se de **Guardar** antes de passar para a secção seguinte.

## <a name="properties"></a>Propriedades

O separador **Properties** pede-lhe para definir as categorias e indústrias usadas para agrupar a sua oferta nos mercados, os contratos legais que suportam a sua oferta e a sua versão de app.

Selecione **Guardar** depois de completar estes campos.

### <a name="category"></a>Categoria

Selecione um mínimo de uma (1) e um máximo de três (3) categorias utilizadas para agrupar a sua oferta nas áreas de pesquisa de mercado apropriadas. Chame a atenção para a forma como a sua oferta suporta estas categorias na descrição da oferta.

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versão da aplicação

Este campo é opcional e utilizado no mercado appSource para identificar o número de versão da sua oferta.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Padrão para o mercado comercial da Microsoft

A Microsoft fornece um modelo de Contrato Padrão.

- **Usar o Contrato Padrão para o mercado comercial da Microsoft?**

Para simplificar o processo de aquisição para os clientes e reduzir a complexidade jurídica dos fornecedores de software, a Microsoft oferece um Contrato Padrão para o mercado comercial da Microsoft para ajudar a facilitar transações no mercado. Em vez de elaborar em condições e condições personalizadas, os editores de marketplace comercial podem optar por oferecer o seu software ao abrigo do Contrato Padrão, que os clientes só precisam de vetar e aceitar uma vez. O Contrato Padrão pode https://go.microsoft.com/fwlink/?linkid=2041178ser consultado aqui: .

Pode selecionar para utilizar o Contrato Padrão em vez de fornecer os seus próprios termos e condições personalizados selecionando a caixa de verificação "Use o Contrato Padrão para o mercado comercial".

![Utilização da caixa de verificação Standard Contract](./media/use-standard-contract.png)

> [!NOTE]
> Uma vez publicada uma oferta utilizando o contrato Standard para o mercado comercial da Microsoft, não poderá utilizar os seus próprios termos e condições personalizados. É um cenário de "ou". Ou oferece a sua solução ao abrigo do Contrato Padrão **ou** dos seus próprios termos e condições. Se quiser modificar os termos do Contrato Padrão, pode fazê-lo através de Alterações De Contrato Padrão.

#### <a name="standard-contract-amendments"></a>Alterações-padrão do contrato

As Alterações-Padrão do Contrato permitem aos editores selecionar os termos do Contrato Padrão para a simplicidade e personalizar os termos para o seu produto ou negócio. Os clientes só precisam de rever as alterações ao contrato, caso já tenham revisto e aceite o Contrato Padrão da Microsoft.

Existem dois tipos de alterações disponíveis para editores de mercado comercial:

- Alterações Universais: Estas alterações são aplicadas universalmente ao Contrato Padrão para todos os clientes. As alterações universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e a alteração antes de poderem utilizar a sua oferta.
- Alterações Personalizadas: Estas alterações são alterações especiais ao Contrato Padrão que são dirigidas a clientes específicos apenas através de IDs de inquilinos Azure. Os editores podem escolher o inquilino que querem atingir. Apenas os clientes do inquilino serão apresentados com os termos de emenda personalizados no fluxo de compra da oferta.  Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta.

>[!NOTE]
> Estes dois tipos de emendas empilham-se em cima uns dos outros. Os clientes visados com alterações personalizadas também receberão a alteração universal ao Contrato Padrão durante a compra.

Termos de **alteração universal do Contrato Padrão para o mercado comercial da Microsoft**: Introduza os termos de alteração universal nesta caixa. Pode fornecer uma única emenda universal por oferta. Pode introduzir um número ilimitado de caracteres nesta caixa. Estes termos são apresentados aos clientes no portal AppSource, Azure Marketplace e/ou Azure durante o fluxo de descoberta e compra.

Termos de **alteração personalizados ao Contrato Padrão para o mercado comercial da Microsoft**: Comece por selecionar termos de emenda **personalizados**. Você pode fornecer até 10 termos de emenda personalizado por oferta.

- Termos de **emenda personalizados**: Introduza os termos da emenda personalizada na caixa de termos de emenda personalizada. Pode introduzir um número ilimitado de caracteres nesta caixa. Apenas os clientes das IDs do inquilino que especifica para estes termos personalizados serão apresentados com os termos de alteração personalizados no fluxo de compra da oferta no portal Azure.  
- **IDs** de inquilino (obrigatório): Cada emenda personalizada pode ser direcionada a até 20 IDs de inquilino. Se adicionar uma emenda personalizada, deve fornecer pelo menos uma identificação de inquilino. A identificação do inquilino identifica o seu cliente em Azure. Pode pedir ao seu cliente este ID e podem encontrá-lo navegando para portal.azure.com > Diretório Ativo azure > Properties. O valor de ID do diretório é o ID do inquilino (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Também pode procurar a identificação do seu cliente com o nome de domínio da organização, utilizando o seu URL de nome de domínio no Qual é o meu Microsoft Azure e o [Office 365 tenant ID?](https://www.whatismytenantid.com)
- **Descrição** (opcional): Opcionalmente fornece uma descrição amigável para o ID do inquilino que o ajuda a identificar o cliente que está a visar com a alteração.

#### <a name="terms-and-conditions"></a>Termos e condições

Se quiser fornecer os seus próprios termos e condições personalizados, pode optar por inscrevê-los no campo de termos e condições. Pode introduzir até 10.000 caracteres de texto neste campo. Se os seus termos e condições requerem uma descrição mais longa, introduza uma única ligação URL neste campo onde os seus termos e condições possam ser encontrados. Mostrará aos clientes como um link ativo.

Os clientes são obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

Lembre-se de **Guardar** antes de passar para a secção seguinte.

## <a name="offer-listing"></a>Oferta listagem

O separador de listagem oferta exibe os idiomas (e mercados) onde a sua oferta está disponível, atualmente o inglês (Estados Unidos) é o único local disponível. Além disso, esta página apresenta o estado da listagem específica da língua e a data/hora que foi adicionada. Terá de definir os detalhes do mercado (nome, descrição, termos de pesquisa, etc.) para cada idioma/mercado.

> [!NOTE]
> Oferecer conteúdo de listagem (como descrição da oferta, documentos, imagens, termos de uso e política de privacidade) não é obrigado a estar em inglês desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um URL de *Link Útil* para oferecer conteúdo num idioma diferente do utilizado no conteúdo de listagem de oferta.

### <a name="offer-listings"></a>Oferta de anúncios

Forneça detalhes a mostrar no mercado, incluindo descrições da sua oferta e ativos de marketing.

- **Nome** (obrigatório): O nome aqui definido aparecerá como título da sua oferta de listagem no ou no mercado que escolheu. O nome é pré-povoado com base na sua entrada anterior da **Nova Oferta.** O nome pode ser marcado. Não pode conter emojis (a não ser que sejam símbolos de marca e direitos de autor) e deve ser limitado a 50 caracteres.
- **Resumo** (necessário): Forneça uma breve descrição da sua oferta para ser utilizada nos resultados de pesquisa de listagem de mercado. Até 100 caracteres de texto podem ser inseridos neste campo.
- **Descrição** (requerida): Forneça uma descrição da sua oferta para ser exibida na(s) síntese do mercado. Considere incluir uma proposta de valor, benefícios-chave, qualquer categoria ou associações do setor, oportunidades de compra na app, quaisquer divulgações necessárias, e um link para saber mais.
Até 3.000 caracteres de texto podem ser inseridos neste campo, incluindo marcação. Para obter dicas adicionais, consulte o artigo [Escreva uma grande descrição da aplicação](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Pesquisar palavras-chave**: Introduza até três palavras-chave de pesquisa que os clientes podem usar para encontrar a sua oferta no mercado ou no mercado.
- **Iniciar instruções (necessárias):** Explique como configurar e comece a usar a sua aplicação para potenciais clientes.  Este quickstart pode conter links para documentação online mais detalhada. Até 3.000 caracteres de texto podem ser inseridos neste campo.

#### <a name="description"></a>**Descrição**

Este campo é obrigatório. Itens a incluir na **Descrição:**

* Descreva claramente a proposta de valor da sua oferta nas primeiras frases da sua descrição.  
* Tenha em mente que as primeiras frases podem ser exibidas nos resultados do motor de busca.  
* Não confie em funcionalidades e funcionalidades para vender o seu produto. Em vez disso, concentre-se no valor que entregar.  
* Utilize o vocabulário específico da indústria ou a formulação baseada em benefícios o máximo possível.

Os componentes fundamentais da sua proposta de valor devem incluir as seguintes informações:

* Descrição do produto.
* Tipo de utilizador que beneficia do produto.
* Necessidades ou dor do cliente que o produto aborda.

Para tornar a sua oferta **Descrição** mais envolvente, use o rico editor de texto para formatar a sua descrição.

![Usando o rico editor de texto](./media/text-editor2.png)

Utilize as seguintes instruções para utilizar o rico editor de texto:

- Para alterar o formato do seu conteúdo, realce o texto que pretende formatar e selecionar um estilo de texto, como mostrado abaixo:

     ![Usando o rico editor de texto para alterar o formato de texto](./media/text-editor3.png)

- Para adicionar uma lista com bala ou numerada ao texto, utilize as opções abaixo:

     ![Usando o rico editor de texto para adicionar listas](./media/text-editor4.png)

- Para adicionar ou remover o recuo ao texto, utilize as opções abaixo:

     ![Usando o rico editor de texto para o travessão](./media/text-editor5.png)

#### <a name="links"></a>Ligações

- **Política de Privacidade** (necessária): Ligação à política de privacidade da sua organização. Você é responsável por garantir que a sua aplicação cumpre as leis e regulamentos de privacidade, e por fornecer uma política de privacidade válida
- **CSP Program Marketing Materials** (opcional): Fornecer um link para materiais de marketing se optar por estender a sua oferta ao programa [Cloud Solution Provider (CSP).](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) A CSP alarga a sua oferta a um leque mais alargado de clientes qualificados, permitindo aos parceiros da CSP agregar, comercializar e revender a sua oferta. Estes revendedores precisarão de acesso a materiais para comercializar a sua oferta. Para mais informações, consulte [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Links Úteis** (opcional): Documentos online suplementares opcionais sobre a sua app ou serviços relacionados listados por fornecer um **Título** e **URL**. Adicione links úteis adicionais clicando **+ Adicione um URL**.

#### <a name="contact-information"></a>Informações de contacto

- **Contactos**: Para cada contacto com o cliente, forneça um **nome**de empregado, **número de telefone**e endereço de **e-mail.**  (Estes *não serão* exibidos publicamente). É também necessário um URL de **suporte** para o grupo de Contacto de **Suporte.**  (Esta informação *será* exibida publicamente).

**Contacto de suporte** (obrigatório): Para questões de apoio geral.

**Contacto de engenharia** (obrigatório): Para questões técnicas.

**Contato do Channel Manager** (obrigatório): Para questões de revendedor relacionadas com o programa CSP.

#### <a name="files-and-images"></a>Arquivos e Imagens

- **Documentos** (obrigatórios): Adicione documentos de marketing relacionados para a sua oferta, em formato PDF, fornecendo um mínimo de um (1) e máximo de três (3) documentos por oferta.
- **Imagens** (opcional): Existem vários locais onde as imagens de logótipo da sua oferta podem aparecer em todo o mercado(s), requerendo os seguintes tamanhos -- Small: 48 x 48 pixels _(necessário),_ Médio: 90 x 90 pixels _(necessário)_, Grande: 216 x 216 pixels _(necessários),_ Largo: 255 x 115 pixels, e Hero: 815 x 290 pixels. Todas as imagens devem estar dentro. Formato PNG.
- **Screenshots** (necessário): Adicione imagens demonstrando a sua oferta. Um máximo de cinco (5) imagens podem ser adicionadas e devem ser dimensionadas a 1280 x 720 pixels. Todas as imagens devem estar dentro. Formato PNG.
- **Vídeos** (opcional): Adicione links a vídeos que demonstrem a sua oferta. Pode utilizar links para vídeos do YouTube e/ou Da Vimeo, que são mostrados juntamente com a sua oferta aos clientes. Também terá de introduzir uma imagem de miniatura do vídeo, dimensionado para 1280 x 720 pixels em formato PNG. Pode exibir um máximo de quatro vídeos por oferta.

Lembre-se de **Guardar** antes de passar para a secção seguinte.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de mercado

- [Boas práticas para anúncios de oferta de mercado](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Pré-visualização

O separador **Preview** permite-lhe definir um Público de **Pré-visualização** limitado para lançar a sua oferta antes de publicar a sua oferta ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Depois de verificar a sua oferta em Preview, selecione **Ir ao vivo** para que a sua oferta possa ser publicada em direto para o público do mercado.

- **Defina um Público de Pré-visualização: Adicione um único e-mail de conta AAD/MSA por linha, juntamente com uma descrição opcional.**

Adicione até dez (10) endereços de e-mail manualmente, ou vinte (20) se carregar um ficheiro CSV, para as contas existentes da Conta Microsoft (MSA) ou do Azure Ative Diretório para ajudar a validar a sua oferta antes de publicar em direto. Ao adicionar estas contas, está a definir um público que será permitido pré-visualizar o acesso à sua oferta antes de ser publicado no mercado ou no mercado. Se a sua oferta já estiver ao vivo, poderá ainda definir um público de pré-visualização para testar quaisquer alterações ou atualizações da sua oferta.

> [!NOTE]
> O público de pré-visualização difere de um público privado. Um público pré-visualizado é permitido o acesso à sua oferta _antes_ de ser publicado ao vivo nos mercados. Você também pode optar por criar um plano e disponibilizá-lo apenas para um público privado. No separador de listagem de **planos,** pode definir uma audiência privada com o **This is a private plan** checkbox. Em seguida, pode definir uma audiência privada de até 20.000 clientes usando IDs de Inquilino Azure.

## <a name="technical-configuration"></a>Configuração técnica

O separador de **configuração técnica** define os detalhes técnicos (url path, webhook, ID do inquilino e ID da aplicação) utilizados para se ligar em sua oferta. Esta ligação permite-nos fornecer a sua oferta para o cliente final se optarem por adquiri-la. Os diagramas que descrevem a utilização dos campos recolhidos estão disponíveis na documentação para apis de cumprimento do [SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- URL da página de **aterragem** (necessário): Defina o URL do site em que os clientes irão aterrar depois de adquirir a sua oferta no mercado. Este URL será o ponto final que recebe um símbolo quando um cliente é encaminhado para a página. Esta ficha pode ser trocada por fornecer detalhes utilizando a determinação nas APIs de cumprimento. Esses detalhes e quaisquer outros que recolher podem ser usados como parte de uma página web interativa do cliente incorporada na sua experiência para completar o registo e ativar a sua compra.

- **Ligação webhook** (necessário): Para todos os eventos assíncronos que a Microsoft precisa enviar-lhe em nome do cliente (exemplo: A Subscrição SaaS ficou inválida), exigimos que forneça um webhook de ligação. Se ainda não tiver um sistema de webhook no lugar, a configuração mais simples é ter uma App Lógica HTTP Endpoint que irá\/ouvir quaisquer eventos que lhe sejam publicados e, em seguida, manuseá-los adequadamente (por exemplo, https: /prod-1westus.logic.azure.com:443/work). Para mais informações, consulte [Call, trigger ou nest workflows com pontos finais HTTP em aplicações lógicas](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Id de inquilino azure AD** (obrigatório): Dentro do portal Azure, exigimos que [crie uma app azure Ative Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para que possamos validar a ligação entre os nossos dois serviços por trás de uma comunicação autenticada. Para encontrar o ID do [inquilino,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)vá ao seu Diretório Ativo Azure e selecione **Propriedades,** em seguida, procure o número de ID do **Diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- ID da **aplicação Azure AD** (necessário): Também precisa do seu ID de [aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e de uma chave de autenticação. Para obter esses valores, vá ao seu Diretório Ativo Azure e selecione registos de **Aplicações,** em seguida, procure o número de ID da **aplicação** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Para encontrar a chave de autenticação, vá a **Definições** e selecione **Keys**. Terá de fornecer uma descrição e duração e, em seguida, será-lhe fornecido um valor numéno.

>[!Note]
>O ID da aplicação Azure está associado ao seu ID de editor, por isso certifique-se de que o mesmo ID de aplicação é usado em todas as suas ofertas.

## <a name="plan-overview"></a>Visão geral do plano

O separador **de visão geral** do Plano permite-lhe fornecer uma variedade de opções de plano dentro da mesma oferta. Estes planos (por vezes referidos como SKUs) podem diferir em termos de versão, rentabilização ou níveis de serviço. Deve configurar pelo menos um plano para vender a sua oferta no mercado.

Uma vez criado, você verá os seus nomes de plano, IDs, modelos de preços, disponibilidade (Público ou Privado), estado de publicação atual, e quaisquer ações disponíveis.

**As ações** disponíveis na **visão geral** do Plano variam consoante o estado atual do seu plano e podem incluir:

- Se o estado do plano for **Rascunho** - Eliminar o projeto
- Se o estado do plano for **Live** - Pare de vender o plano ou sincronizar o público privado

**Criar um novo plano** (mínimo de um plano para quem seleciona vender através da Microsoft)

- **ID do plano:** Crie um plano único de ID para cada plano nesta oferta. Este ID será visível para os clientes nos modelos URL do produto e Do Gestor de Recursos Azure (se aplicável). Use apenas caracteres minúsculos, alfanuméricos, traços ou sublinhados. Um máximo de 50 caracteres são permitidos para este plano id. O ID não pode ser modificado após a seleção criar.
- Nome do **plano:** Os clientes verão este nome ao decidir qual o plano a selecionar dentro da sua oferta. Crie um nome de oferta único para cada plano nesta oferta. O nome do plano é usado para diferenciar planos de software que podem fazer parte da mesma oferta (por exemplo, nome oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Listagem de planos

O separador de listagem de **planos** exibe os idiomas (e mercados) onde o seu plano está disponível, atualmente o inglês (Estados Unidos) é o único local disponível. Além disso, esta página apresenta o estado da listagem específica da língua e a data/hora que foi adicionada. Terá de definir os detalhes do mercado (nome, descrição, termos de pesquisa, etc.) para cada idioma/mercado.

#### <a name="plan-listing-details"></a>Detalhes da listagem de planos

A seleção de um dos idiomas do plano apresentará as informações de listagem do **plano,** incluindo **nome** e **descrição.**

- **Nome**: Pré-povoado com base na sua pré-visualização Nova entrada de **plano** e aparecerá como o título do "plano de software" da sua oferta exibido no mercado.
- **Descrição:** Esta descrição é uma oportunidade para explicar o que torna este plano de software único e quaisquer diferenças de outros planos de software dentro da sua oferta. Pode conter até 500 caracteres.

Selecione **Guardar** depois de completar estes campos.

#### <a name="plan-pricing-and-availability"></a>Preços e disponibilidade de planos

O separador **de preços e disponibilidade** permite-lhe configurar os mercados em que este plano estará disponível, o modelo de rentabilização pretendido, preço e prazo de faturação. Além disso, pode indicar se torna o plano visível para todos ou apenas para clientes específicos (um público privado).

##### <a name="enabling-free-trials"></a>Habilitar ensaios gratuitos

As ofertas da SaaS através do mercado comercial permitem-lhe fornecer um teste gratuito de um mês ao vender através da Microsoft. Para todos os modelos e termos de faturação, com exceção dos planos medidos, são apoiados testes gratuitos. Esta opção permite que os clientes tenham uma barreira baixa à entrada através de um mês de acesso gratuito.  Se optar por permitir um teste gratuito para planos dentro da sua oferta, o cliente não poderá converter-se a uma subscrição paga antes do final do período inicial de um mês.  Durante este tempo, os clientes que adquirem a sua oferta podem experimentar qualquer um dos planos suportados que tenham o teste gratuito habilitado e convertidoentre eles.  A conversão para uma subscrição paga é feita automaticamente no final do período.

>[!Note]
>Se o cliente optar por se converter num plano sem testes gratuitos, a conversão acontecerá, mas o teste gratuito será perdido imediatamente.  Além disso, uma vez que um cliente começa a pagar por um plano, eles não podem mais obter teste gratuito na mesma subscrição novamente, mesmo que eles se convertam em um SKU que suporta testes gratuitos.

A capacidade de configurar um teste gratuito está disponível para cada plano na sua oferta. Basta navegar para o Preço e Disponibilidade para cada oferta e verificar a caixa para permitir um ensaio de um mês.

![Caixa de verificação de teste gratuita de um mês](./media/free-trial-enable.png)

>[!Note]
>Uma vez que a sua oferta transactiva tenha sido publicada com um teste gratuito, não pode ser desativada para esse plano. Certifique-se de que esta definição está correta para a primeira publicação para evitar ter de recriar o plano.

Para obter informações sobre subscrições de clientes atualmente participadas `isFreeTrial`num teste gratuito, utilize a nova propriedade API, que será marcada como verdadeira ou falsa. Para mais informações, consulte a [API de Subscrição SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!Note]
>Os ensaios gratuitos não são suportados para planos que alavancam o serviço de medição do mercado.

#### <a name="markets"></a>Mercados

- **Editar mercados** (opcional)

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de verificação para qualquer local de mercado onde pretenda disponibilizar este plano. Uma caixa de pesquisa e um botão para selecionar países "Tax Remitted", no qual a Microsoft atribui as vendas e o imposto usado em seu nome, estão incluídos para ajudar.

Se já definiu preços para o seu plano em Dólares dos Estados Unidos (USD) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as taxas de câmbio atuais. Deve sempre rever o preço de cada mercado antes de publicar. Os preços podem ser revistos utilizando o link "Preços de Exportação (xlsx)" depois de guardar as suas alterações.

#### <a name="pricing"></a>Preços

- **Modelo de preços**: Taxa fixa ou sede em assento

**Taxa fixa:** Ative o acesso à sua oferta com um preço fixo mensal ou anual. Isto é por vezes referido como preços baseados no site. Com este modelo de preços, pode definir opcionalmente planos medidos que utilizam o serviço de medição do mercado API para cobrar aos clientes de acordo com unidades não standard.  Para obter mais informações sobre a faturação medição, consulte a [faturação medição utilizando o serviço](./saas-metered-billing.md)de medição do mercado .

**Por utilizador:** Ative o acesso à sua oferta com o preço baseado no número de utilizadores que acedem à oferta ou ocupam lugares. Este modelo baseado no utilizador permite-lhe definir o número mínimo e máximo de utilizadores permitidos com base no preço. Desta forma, diferentes pontos de preço podem ser configurados com base no número de utilizadores configurando vários planos.  Estes campos são opcionais. Se não for selecionado, o número de utilizadores será interpretado como não tendo um limite (min de 1 e máximo de quantos o sistema pode suportar). Estes campos podem ser editados como parte de uma atualização do seu plano.

Uma vez publicado, a escolha do modelo de preços de faturação não pode ser alterada. Além disso, todos os planos para a mesma oferta devem partilhar o mesmo modelo de preços.

- **Prazo de faturação**: Mensal ou Anual

Selecione a frequência que os clientes devem pagar o preço listado. Deve ser fornecido pelo menos um preço mensal ou anual, ou ambas as opções podem ser disponibilizadas aos clientes.

- **Preço**: USD por mês ou USD por ano

Os preços estabelecidos em moeda local (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados utilizando as taxas de câmbio atuais disponíveis durante a configuração. Valide estes preços antes de publicar exportando a folha de cálculo dos preços e revendo o preço em cada mercado. Se quiser definir preços personalizados num mercado individual, modifique e importe a folha de cálculo dos preços. É responsável por validar este preço e possuir estas configurações.
*\*Primeiro, tem de poupar as alterações de preços para permitir a exportação de dados de preços.*

Reveja cuidadosamente os seus preços antes de publicar, uma vez que existem algumas restrições sobre o que pode mudar após a publicação de um plano:

- Uma vez publicado um plano, o modelo de preços não pode ser alterado.
- Uma vez que um termo de faturação é publicado para um plano, não pode ser removido mais tarde.
- Uma vez publicado um preço por um mercado no seu plano, não pode ser alterado mais tarde.

### <a name="plan-audience"></a>Audiência de Plano

Tem a opção de configurar cada plano para ser visível para todos ou apenas para um público específico à sua escolha. Você pode atribuir adesão a este público restrito usando iDs de inquilino azure ad.

#### <a name="privacy"></a>Privacidade

- **Este é um plano privado** (caixa de verificação opcional)

Verifique esta caixa para tornar o seu plano privado e visível apenas para o público restrito à sua escolha. Uma vez publicado como um plano privado, pode atualizar o público ou optar por disponibilizar o plano a todos. Uma vez que um plano é publicado como visível para todos, deve permanecer visível para todos. (O plano não pode ser configurado novamente como um plano privado).

- **Audiência Restrita (IDs de Inquilino)**

Atribua o público que terá acesso a este plano privado. O acesso é atribuído usando iDs de inquilino com a opção de incluir uma descrição de cada ID de inquilino atribuído. Pode adicionar um máximo de 10 IDs de inquilino, ou 20.000 clientes de iDs de inquilino se importar um ficheiro de folha de cálculo .csv.

Um inquilino é uma representação de uma organização, com um ID representado como GUID (Globalmente Unique Identifier, um número inteiro de 128 bits usado para identificar recursos). É uma instância dedicada do Azure AD que uma organização ou um programador de aplicações recebe quando a organização ou o programador de aplicações cria uma relação com o Microsoft – como inscrever-se no Azure, no Microsoft Intune ou no Microsoft 365. Cada inquilino do Azure AD é distinto e separado dos outros inquilinos do Azure AD. Para verificar o inquilino, inicie sessão no Portal do Azure com a conta que pretende utilizar para gerir a sua aplicação. Se tiver um inquilino, irá ter automaticamente sessão iniciada no mesmo e verá o nome do inquilino imediatamente por baixo do nome da sua conta. Se passar com o rato por cima do nome da sua conta, no canto superior direito do portal do Azure, verá o seu nome, e-mail, diretório e ID de inquilino (um GUID) e o seu domínio. Se a sua conta estiver associada a vários inquilinos, pode selecionar o nome da sua conta para abrir um menu onde pode alternar entre inquilinos. Cada inquilino tem o seu próprio ID de inquilino. Você também pode procurar o ID do inquilino da [https://www.whatismytenantid.com](https://www.whatismytenantid.com)sua organização usando um URL de nome de domínio em: .

Enquanto o SaaS oferece iDs de inquilino para definir um público privado, outros tipos de oferta podem usar IDs de subscrição Azure (que também são representados como GUIDs).

> [!NOTE]
> O público privado (ou público restrito) difere de um público de pré-visualização. No separador **[Preview,](#preview)** pode definir um público de pré-visualização. Um público pré-visualizado é permitido o acesso à sua oferta *antes* da oferta ser publicada ao vivo no mercado. Embora a designação do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos (privados ou não), mas apenas durante o período de pré-visualização limitado enquanto o plano é testado e validado.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Lista de exemplo de planos dentro de uma oferta de mercado

![Lista de plano de mercado de exemplo com notas](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Test drive

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Cloud Solution Provider (CSP) Reseller Audience

Optar por disponibilizar a sua oferta no programa CSP permite que os Fornecedores de Soluções cloud vendam o seu produto como parte de uma solução agregada aos seus clientes. Para mais informações, consulte [Cloud Solution Providers](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publicar

Depois de ter concluído todas as secções necessárias da oferta, **selecione publicar** no canto superior direito do portal. Será redirecionado para a página **Review e publicação.**

#### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Se esta for a sua primeira publicação desta oferta, pode:

- Consulte o estado de conclusão de cada secção da oferta.
    - *Não começou* - significa que a secção não foi tocada e precisa de ser concluída.
    - *Incompleto* - significa que a secção tem erros que precisam de ser corrigidos ou exige mais informações a fornecer. Tens de voltar à secção e atualizá-la.
    - *Completo* - significa que a secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado total antes de poder submeter a oferta.
- Forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta para publicação selecionando **Enviar**. Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta está disponível para que você reveja e aprove. Você deve voltar ao Partner Center e selecionar **Go-live** para a oferta de publicar a sua oferta ao público (ou se uma oferta privada, para o público privado).

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

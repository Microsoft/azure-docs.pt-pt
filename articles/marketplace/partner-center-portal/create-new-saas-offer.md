---
title: Criar uma oferta SaaS, Azure Marketplace e Microsoft AppSource
description: Como criar uma oferta de Software como serviço (SaaS) para listagem ou venda no Microsoft AppSource, Azure Marketplace ou através do programa Cloud Solution Provider (CSP) utilizando o programa de marketplace comercial da Microsoft no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2b12eb88f0297c44fabb62e40d315517f3d3b954
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800359"
---
# <a name="create-a-saas-offer"></a>Criar uma oferta SaaS

Para começar a criar software como um Serviço (SaaS) oferece no mercado comercial, certifique-se de que primeiro [cria uma conta Partner Center](./create-account.md) e abre o painel de [instrumentos do Mercado Comercial,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)com o **separador Overview** selecionado.

> [!NOTE]
> Se estiver a criar uma oferta de SaaS transacionável, certifique-se de que implementa a integração com [APIs de cumprimento saaS.](./pc-saas-fulfillment-apis.md)  A integração com as APIs é a única forma de a transação no Mercado funcionar corretamente. Também precisa de se certificar de que a sua aplicação utiliza a autenticação Azure AD com um único sinal (SSO). Consulte [a Azure AD e as ofertas transacionáveis da SaaS no mercado comercial.](../azure-ad-saas.md)

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página 'Vista Geral', selecione **+ Software de oferta nova**como  >  **serviço**.

   ![Ilustra o menu de navegação à esquerda.](./media/new-offer-saas.png)

> [!NOTE]
> Após a publicação de uma oferta, as edições feitas no Partner Center só aparecem nas montras depois de republicarem a oferta. Certifique-se de que é sempre republicante depois de escorção.

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

**O estado de publicação** apresenta uma representação visual dos passos necessários para publicar esta oferta e quanto tempo cada passo demorará a ser concluído. Os ícones incompletos do passo de publicação serão acinzentados.

O menu **de visão geral da Oferta** contém uma lista de links para realizar operações nesta oferta. Esta lista de operações mudará com base na seleção que fizer para a sua oferta.  

- Se a oferta for um rascunho - Eliminar rascunho
- Se a oferta for ao vivo - Pare de vender oferta
- Se a oferta estiver em pré-visualização - Go-live
- Se ainda não completou a assinatura da editora - Cancele a publicação

## <a name="offer-setup"></a>Configuração de oferta

Esta página pede as seguintes informações.

- **Gostaria de vender através da Microsoft?** (Sim/Não)
  - **Sim,** gostaria de vender através da Microsoft e ter transações de anfitriões da Microsoft em meu nome.
  - **Não,** prefiro apenas enumerar a minha oferta através dos mercados e processar transações de forma independente.

### <a name="sell-through-microsoft"></a>Venda através da Microsoft

A venda através da Microsoft proporciona uma melhor descoberta e aquisição de clientes, permite à Microsoft hospedar transações de marketplace em seu nome, e aproveita as capacidades de comércio globalmente disponíveis da Microsoft.

#### <a name="saas-offer-requirements"></a>Requisitos de oferta saaS

Para listar o Software como um Serviço (SaaS) oferece com o Mercado Comercial no Partner Center, devem ser cumpridos os seguintes critérios:

- A sua oferta deve utilizar [o Azure Ative Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) para gestão de identidade e autenticação.
- A sua oferta deve utilizar [APIs de Cumprimento saas](pc-saas-fulfillment-api-v2.md) para integrar-se no Azure Marketplace.

#### <a name="saas-pricing-and-billing-options"></a>Opções de preços e faturação de SaaS

Com as soluções SaaS em execução na subscrição Azure da editora, as taxas de licença pagas pelos clientes incluem o custo da infraestrutura em que o software é implantado. O uso da infraestrutura Azure é gerido e cobrado diretamente para si, o parceiro. As taxas reais de utilização da infraestrutura não são vistas pelo cliente. Os editores devem agregar as taxas de utilização da infraestrutura Azure nos seus preços de licença de software.

O SaaS oferece suporte mensal ou anual de faturação com base numa taxa fixa, por utilizador ou taxas de consumo utilizando o serviço de faturação medido. O mercado comercial da Microsoft opera num modelo de agência, pelo que os editores definem os preços, os clientes da Microsoft e a Microsoft paga receitas à editora enquanto retém uma taxa de agência.

Trata-se de uma repartição de custos e pagamentos para demonstrar o modelo da agência (os preços cotados são, por exemplo, apenas para efeitos de 2018 e não se destinam a refletir os custos reais):

|**O custo da sua licença**|**$100 por mês**|
|:---|:---|
|Custo de utilização azul (D1/1-Core)|Cobrado diretamente para o editor, não o cliente|
|O cliente é faturado pela Microsoft|$100,00 por mês (A Editora deve ter em conta quaisquer custos de infraestrutura incorridas ou pass-through na taxa de licença)|

|**Contas da Microsoft**|**$100 por mês**|
|:---|:---|
|Microsoft paga-lhe 80% do custo da sua licença <br>**Para aplicações SaaS qualificadas, a Microsoft paga 90% do custo da sua licença*|$80,00 por mês <br>*$* 90.00 por mês*|

- Neste exemplo, a Microsoft fatura $100,00 ao cliente pela sua licença de software e paga $80,00 à editora.

> [!NOTE]
> **Taxa reduzida de serviço no Mercado** – Para certas ofertas da SaaS que publicou no mercado comercial, a Microsoft reduzirá a sua Taxa de Serviço de Marketplace de 20% (conforme descrito no Microsoft Publisher Agreement) para 10%. Para que a sua(s) oferta(s) se qualifique, a sua(s) oferta(s) deve ter sido designada pela Microsoft como Azure IP Co-venda incentivado. A elegibilidade deve ser cumprida pelo menos cinco (5) dias úteis antes do final de cada mês civil para receber a Taxa reduzida de Serviço do Mercado para o mês. A Taxa reduzida de Serviço de Marketplace também se aplica à Azure IP Co-vender VMs, Apps Geridas e quaisquer outras ofertas de ias transacionáveis qualificadas disponibilizadas através do mercado comercial.

### <a name="list-through-microsoft"></a>Lista através da Microsoft

Promova o seu negócio com a Microsoft criando uma listagem de marketplace. Selecionar para listar a sua oferta apenas e não transacionar através da Microsoft significa que a Microsoft não participa diretamente em transações de licenças de software. Não existe uma taxa de transação associada e a editora mantém 100% de quaisquer taxas de licenciamento de software cobradas ao cliente. No entanto, a editora é responsável por apoiar todos os aspetos da transação de licença de software, incluindo, mas não se limitando a: cumprimento da encomenda, medição, faturação, faturação, pagamento e cobrança.

#### <a name="get-it-now-free"></a>Obtê-lo agora (grátis)

Enuncia gratuitamente a sua oferta aos *clientes,* fornecendo um endereço válido (a começar por *http* ou https ), onde podem obter um teste através [da autenticação de um clique utilizando o Azure Ative Directory (Azure AD)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)). Por exemplo, `https://contoso.com/saas-app`.

#### <a name="free-trial-listing"></a>Ensaio gratuito (listagem)

Enuncia a sua oferta aos clientes com um link para um teste gratuito, fornecendo um endereço válido (a começar por *http* ou *https*), onde podem obter um teste através [de autenticação de um clique utilizando o Azure Ative Directory (Azure AD)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)). Por exemplo, `https://contoso.com/trial/saas-app`. Os testes gratuitos de listagem de ofertas são criados, geridos e configurados pelo seu serviço e não têm subscrições geridas pela Microsoft.

> [!NOTE]
> Os tokens que a sua aplicação receberá através do seu link de teste só podem ser utilizados para obter informações do utilizador através do Azure AD para automatizar a criação de conta na sua app. As Contas Microsoft (MSA) não são suportadas para autenticação utilizando este token.

#### <a name="contact-me"></a>Contacte-me

Recolher informações de contacto com o cliente ligando o seu sistema de Gestão de Relacionamento com o Cliente (CRM). O cliente será solicitado permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e fonte de mercado onde encontraram a sua oferta, serão enviados para o sistema CRM que configuraste. Para obter mais informações sobre a configuração do seu CRM, consulte [os leads do Cliente](#customer-leads).

#### <a name="example-marketplace-offer-listing"></a>Exemplo de listagem de oferta do marketplace

Aqui está um exemplo de como a informação da oferta aparece no Microsoft AppSource:

:::image type="content" source="media/example-appsource-saas.png" alt-text="Ilustra como esta oferta aparece no Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logotipo grande
2. Categorias
3. Indústrias
4. Endereço de suporte (link)
5. Termos de utilização
6. Política de privacidade
7. Nome da oferta
8. Resumo
9. Descrição
10. Screenshots/vídeos
11. Documentos

<br>Aqui está um exemplo de como a oferta de informação aparece no portal Azure:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Ilustra como esta oferta aparece no portal Azure.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Título
2. Descrição
3. Ligações úteis
4. Capturas de ecrã

## <a name="enable-a-test-drive"></a>Ativar uma unidade de teste

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "experimentar antes de comprar", resultando numa maior conversão e na geração de leads altamente qualificados. [Saiba mais sobre test drives](../what-is-test-drive.md).

Para ativar uma unidade de teste durante um período de tempo fixo, selecione a caixa de **verificação de acionamento de test drive.** Para remover o test drive da sua oferta, limpe esta caixa de verificação.

Para obter informações adicionais, consulte [Test drive a sua oferta no mercado comercial.](test-drive.md)

### <a name="test-drive-resources"></a>Recursos de test drive

- [O que é uma versão de teste?](../what-is-test-drive.md)
- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; certifique-se de que o seu bloqueador pop-up está desligado)

### <a name="customer-leads"></a>Ligações ao cliente

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Recursos adicionais de gestão de chumbo
- [Perguntas frequentes de gestão de chumbo)](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)
- [Erros comuns de configuração de chumbo](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [Visão geral da gestão de chumbo Um Pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

**Selecione Guardar o projeto** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página pede-lhe que defina as categorias e indústrias usadas para agrupar a sua oferta nos mercados, os contratos legais que suportam a sua oferta e a sua versão de aplicação.

### <a name="category"></a>Categoria

A sua oferta será publicada no AppSource ou no Azure Marketplace, dependendo das capacidades de transação associadas à sua oferta e à sua seleção de categorias. Consulte [a Comparação do Microsoft AppSource e do Azure Marketplace](../comparing-appsource-azure-marketplace.md) para obter mais detalhes. Selecione categorias e subcategorias que melhor se alinham com a sua oferta e o seu público pretendido. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se não for aplicável nenhuma subcategoria à sua oferta, selecione **Não aplicável**.

Consulte a lista completa de categorias e subcategorias aplicáveis a cada Montra em [Ofertas Listando As Melhores Práticas.](../gtm-offer-listing-best-practices.md)

### <a name="industries"></a>Indústrias

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

A seleção da indústria aplica-se apenas às ofertas publicadas no AppSource.

### <a name="app-version"></a>Versão da aplicação

Este campo é opcional e utilizado no mercado AppSource para identificar o número de versão da sua oferta.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Padrão para o mercado comercial da Microsoft

A Microsoft fornece um modelo de Contrato Padrão.

- **Utilizar o Contrato Padrão para o mercado comercial da Microsoft?**

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal dos fornecedores de software, a Microsoft oferece um Contrato Standard para o mercado comercial da Microsoft para ajudar a facilitar transações no mercado. Em vez de criarem termos e condições personalizados, os editores de marketplace comercial podem optar por oferecer o seu software ao abrigo do Contrato Padrão, que os clientes só precisam de vetar e aceitar uma vez. O Contrato Padrão pode ser encontrado em https://go.microsoft.com/fwlink/?linkid=2041178 .

Pode selecionar para utilizar o Contrato Padrão em vez de fornecer os seus próprios termos e condições personalizados selecionando a caixa de verificação "Use o Contrato Padrão para o mercado comercial".

![Utilização da caixa de verificação Standard Contract](./media/use-standard-contract.png)

> [!NOTE]
> Uma vez que publique uma oferta usando o contrato Standard para o mercado comercial da Microsoft, não poderá utilizar os seus próprios termos e condições personalizados. É um cenário de "ou". Ou oferece a sua solução nos termos **e** condições padrão. Se quiser modificar os termos do Contrato Padrão, pode fazê-lo através de Alterações Padrão contratuais.

#### <a name="standard-contract-amendments"></a>Alterações padrão do contrato

As Alterações Padrão do Contrato permitem que os editores selecionem os termos do Contrato Padrão para a simplicidade e personalizem os termos para o seu produto ou negócio. Os clientes só precisam de rever as alterações ao contrato, caso já tenham revisto e aceite o Contrato Padrão da Microsoft.

Existem dois tipos de alterações disponíveis para os editores de mercado comercial:

- Alterações universais: Estas alterações são aplicadas universalmente ao Contrato-Tipo para todos os clientes. As alterações universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e a alteração antes de poderem utilizar a sua oferta.
- Alterações personalizadas: Estas alterações são alterações especiais ao Contrato Padrão que são dirigidas a clientes específicos apenas através de IDs de inquilinos Azure. Os editores podem escolher o inquilino que querem atingir. Apenas os clientes do arrendatário serão apresentados com os termos de alteração personalizados no fluxo de compra da oferta.  Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta.

>[!NOTE]
> Estes dois tipos de emendas empilham-se em cima uns dos outros. Os clientes visados com alterações personalizadas também receberão a alteração universal ao Contrato Padrão durante a compra.

Termos de alteração universal **ao Contrato Padrão para o mercado comercial da Microsoft** – Introduza os termos de emenda universal nesta caixa. Pode fornecer uma única emenda universal por oferta. Pode introduzir um número ilimitado de caracteres nesta caixa. Estes termos são apresentados aos clientes no AppSource, Azure Marketplace e/ou no portal Azure durante o fluxo de descoberta e compra.

**Termos de alteração personalizada ao Contrato Padrão para o mercado comercial da Microsoft** – Comece por selecionar os termos de alteração **personalizada**. Pode fornecer até 10 termos de emenda personalizados por oferta.

- **Termos de emenda personalizada** - Introduza os termos de alteração personalizada na caixa de termos de emenda personalizada. Pode introduzir um número ilimitado de caracteres nesta caixa. Apenas os clientes dos IDs do inquilino que especifique para estes termos personalizados serão apresentados com os termos de alteração personalizados no fluxo de compra da oferta no portal Azure.  
- **IDs de inquilino** (obrigatório) – Cada alteração personalizada pode ser direcionada até 20 IDs de inquilino. Se adicionar uma emenda personalizada, deve fornecer pelo menos uma identificação de inquilino. A identificação do inquilino identifica o seu cliente em Azure. Pode pedir ao seu cliente este ID e eles podem encontrá-lo navegando para portal.azure.com > Azure Ative Directy > Properties. O valor de ID do diretório é o ID do inquilino (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar o iD do inquilino da organização do seu cliente usando o seu nome de domínio URL em [Qual é o meu ID de inquilino Microsoft Azure e Office 365?](https://www.whatismytenantid.com)
- **Descrição** (opcional) – Opcionalmente, forneça uma descrição amigável para o ID do inquilino que o ajuda a identificar o cliente que está a visar com a emenda.

#### <a name="terms-and-conditions"></a>Termos e condições

Se quiser fornecer os seus próprios termos e condições personalizados, pode optar por inscrevê-los no campo de termos e condições. Pode introduzir até 10.000 caracteres de texto neste campo. Se os seus termos e condições requerem uma descrição mais longa, insira um único link URL neste campo onde os seus termos e condições podem ser encontrados. Será apresentado aos clientes como um link ativo.

Os clientes são obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

**Selecione Guardar o projeto** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página exibe os idiomas (e mercados) onde a sua oferta está disponível, atualmente inglês (Estados Unidos) é a única localização disponível. Além disso, esta página apresenta o estado da listagem específica da linguagem e a data/hora que foi adicionada. Você precisará definir os detalhes do mercado (nome de oferta, descrição, termos de pesquisa, etc.) para cada idioma/mercado.

> [!NOTE]
> Oferecer conteúdo sonoro (como descrição de oferta, documentos, screenshots, termos de uso e política de privacidade) não é necessário estar em inglês desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um *URL de Link Útil* para oferecer conteúdo em um idioma diferente do utilizado no conteúdo da listagem de Oferta.

### <a name="offer-listings"></a>Oferta de anúncios

Forneça detalhes a serem exibidos no mercado, incluindo descrições da sua oferta e ativos de marketing.

- **Nome** (obrigatório) – O nome aqui definido aparecerá como o título da sua lista de ofertas no mercado(s) que escolheu. O nome é pré-voizado com base na sua entrada anterior **da Nova Oferta.** O nome pode ser marcado. Não pode conter emojis (a menos que sejam símbolos de marca e direitos de autor) e deve ser limitado a 50 caracteres.
- **Resumo** (obrigatório) – Forneça uma breve descrição da sua oferta para ser usada nos resultados de pesquisa de listagem de mercado. Pode entrar até 100 caracteres de texto neste campo.
- **Descrição** (obrigatória) – Forneça uma descrição da sua oferta a ser exibida na(s) lista(s) visão geral do mercado. Considere incluir uma proposta de valor, benefícios-chave, qualquer categoria ou associações do setor, oportunidades de compra na aplicação, quaisquer divulgações necessárias e um link para aprender mais. Podem ser introduzidos até 3.000 caracteres de texto neste campo, incluindo marcação. Para obter dicas adicionais, consulte [Escrever uma excelente descrição da aplicação.](/windows/uwp/publish/write-a-great-app-description)
- **Pedir palavras-chave** de pesquisa – Introduza até três palavras-chave de pesquisa que os clientes podem usar para encontrar a sua oferta no mercado(s).
- **Iniciar instruções (necessárias)** – Explique como configurar e começar a usar a sua app para potenciais clientes.  Este quickstart pode conter links para documentação online mais detalhada. Podem ser inseridos até 3.000 caracteres de texto neste campo.

#### <a name="description"></a>Descrição

Este campo é obrigatório.

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>Ligações

- **Política de Privacidade** (obrigatório) – Ligue-se à política de privacidade da sua organização. Você é responsável por garantir que a sua app cumpre as leis e regulamentos de privacidade, e por fornecer uma política de privacidade válida
- **CSP Program Marketing Materials** (opcional) – Forneça uma ligação aos materiais de marketing se optar por alargar a sua oferta ao programa [Cloud Solution Provider (CSP).](../cloud-solution-providers.md) A CSP alarga a sua oferta a um leque mais alargado de clientes qualificados, permitindo aos parceiros da CSP agregar, comercializar e revender a sua oferta. Estes revendedores precisarão de acesso a materiais para comercializar a sua oferta. Para mais informações, consulte [os Serviços Go-To-Market.](https://partner.microsoft.com/reach-customers/gtm)
- **Links Úteis** (opcional) – Documentos complementares opcionais sobre a sua aplicação ou serviços relacionados listados através da prestação de um **Título** e **URL.** Adicione links úteis adicionais clicando **+ Adicione um URL**.

#### <a name="contact-information"></a>Informações de contacto

- **Contactos** – Para cada contacto com o cliente, forneça um **nome**de colaborador, **número de telefone**e endereço de **e-mail** (estes *não serão* apresentados publicamente). É necessário **um URL de suporte** para o grupo de contacto de **suporte** (este *será* apresentado publicamente).

  - **Contacto de apoio** (obrigatório) – Para questões gerais de apoio.
  - **Contacto de engenharia** (obrigatório) – Para questões técnicas.
  - **Contacto channel manager** (obrigatório) – Para revendedores questões relacionadas com o programa CSP.

#### <a name="files-and-images"></a>Ficheiros e Imagens

- **Documentos** (obrigatórios) – Adicione documentos de marketing relacionados para a sua oferta, em formato PDF, de pelo menos um e até três documentos por oferta.
- **Imagens** (necessárias) – Existem vários locais onde as imagens do logotipo da sua oferta podem aparecer em todo o mercado. Forneça um ficheiro PNG para o logotipo de tamanho **grande** (entre 216 x 216 e 350 x 350 pixels). O Partner Center utilizará isto para criar um logótipo de tamanho **Pequeno** (48 x 48 pixels) e um logotipo de tamanho **Médio** (90 x 90 pixels). Pode substituir opcionalmente estas imagens diferentes. Adicionar um logotipo **Wide** é opcional.

    São necessários três tamanhos de logotipo para utilização em diferentes locais da listagem; uma é opcional:

    - **Pequeno** (48 x 48)
    - **Médio** (90 x 90)
    - **Grande** (entre 216 x 216 e 350 x 350)
    - **Largo** (255 x 115), *opcional*

    [!INCLUDE [Logo suggestions](./includes/graphics-suggestions.md)]

- **Screenshots** (obrigatórios) – Adicione um máximo de cinco imagens que demonstrem a sua oferta, dimensionadas a 1280 x 720 pixels. Todas as imagens devem estar dentro . Formato PNG.
- **Vídeos** (opcional) – Adicione links a vídeos que demonstrem a sua oferta. Pode utilizar links para vídeos do YouTube e/ou Vimeo, que são mostrados juntamente com a sua oferta aos clientes. Também terá de introduzir uma imagem miniatura do vídeo, dimensionada para 1280 x 720 pixels em formato PNG. Pode exibir um máximo de quatro vídeos por oferta.

>[!NOTE]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="additional-marketplace-listing-resources"></a>Recursos de listagem de mercado adicionais

- [Melhores práticas para anúncios de oferta de mercado](../gtm-offer-listing-best-practices.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="preview-audience"></a>Audiência de pré-visualização

Esta página permite-lhe definir um **Público de pré-visualização** limitado para lançar a sua oferta antes de publicar a sua oferta ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Depois de consultar a sua oferta em Preview, selecione **Go live** para que a sua oferta possa ser publicada em direto para o público do mercado.

Adicione um único e-mail de conta AAD/MSA por linha, juntamente com uma descrição opcional.

Adicione até 10 endereços de e-mail manualmente, ou 20 se carregar um ficheiro CSV, para contas existentes da Microsoft Account (MSA) ou Azure Ative Directory para ajudar a validar a sua oferta antes de publicar ao vivo. Ao adicionar estas contas, está a definir um público que será autorizado a pré-visualizar o acesso à sua oferta antes de ser publicado no mercado(s). Se a sua oferta já estiver ao vivo, poderá ainda definir um público de pré-visualização para testar quaisquer alterações ou atualizações da sua oferta.

> [!NOTE]
> O público de pré-visualização difere de uma audiência privada. Um público de pré-visualização tem acesso à sua oferta _antes_ de ser publicado ao vivo nos mercados. Você também pode optar por criar um plano e disponibilizá-lo apenas para um público privado. No **separador de listagem** de planos, pode definir uma audiência privada com a Caixa de verificação **de plano privado.** Em seguida, pode definir uma audiência privada de até 20.000 clientes usando IDs de Inquilino Azure.

**Selecione Guardar o projeto** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

O separador **de configuração técnica** define os detalhes técnicos utilizados pelo mercado para comunicar ao seu serviço SaaS. Esta ligação permite-nos oferecer a sua oferta para o cliente final se optarem por adquiri-la e geri-la.

>[!NOTE]
>Deve implementar a integração com [as APIs de cumprimento do SaaS](./pc-saas-fulfillment-api-v2.md) antes de configurar estes detalhes nos detalhes da oferta. Também tem de criar uma página de aterragem e a sua aplicação deve utilizar a autenticação AD AZure com um único sinal (SSO). Para mais informações, consulte [a Azure AD e as ofertas transacionáveis da SaaS no mercado comercial.](../azure-ad-saas.md)

Diagramas e explicações detalhadas que descrevem a utilização dos campos recolhidos estão disponíveis em documentação para [as APIs](./pc-saas-fulfillment-api-v2.md).

- **URL da página de aterragem** (obrigatório) – Defina o URL do site SaaS (por exemplo: `https://contoso.com/signup` ) que os clientes finais aterrarão após adquirirem a sua oferta no mercado e desencadeando o processo de configuração a partir da recém-criada subscrição SaaS.  Este URL será chamado com o parâmetro de identificação de compra de mercado que identifica exclusivamente a compra específica do cliente final SaaS.  Tem de trocar este token pelos correspondentes dados de subscrição do SaaS utilizando a API [de resolução.](./pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)  Esses detalhes e quaisquer outros que deseje recolher devem ser usados como parte de uma página web interativa do cliente construída na sua experiência para completar o registo final do cliente e ativar a sua compra.  Nesta página, o utilizador deve inscrever-se através de uma autenticação de um clique utilizando o Azure Ative Directory (Azure AD). <br> <br> Este URL com o parâmetro de identificação de compra de mercado também será chamado quando o cliente final lançar a experiência Managed SaaS do portal Azure ou M365 Admin Center. Você deve lidar com ambos os fluxos, quando o token é fornecido pela primeira vez após a compra para novos clientes e quando é fornecido para o cliente existente que gere o seu SaaS. <br> <br> A página de Aterragem que configura aqui deve estar a funcionar 24 horas por dia, 7 horas por dia. Esta é a única forma de ser notificado sobre novas compras das suas ofertas SaaS efetuadas no mercado, ou pedidos de configuração de uma subscrição ativa de uma oferta.

- **Connection webhook** (obrigatório) – Para todos os eventos assíncronos que a Microsoft precisa de lhe enviar (por exemplo, a subscrição do SaaS foi cancelada), exigimos que forneça um URL webhook de ligação. Vamos chamar este URL para notificá-lo sobre o evento. <br> <br> O webhook que fornece deve estar a funcionar 24 horas por dia, 7 dias por semana, pois esta é a única forma de ser notificado sobre atualizações sobre as subscrições saaS dos seus clientes adquiridas através do mercado.  Se ainda não tiver um sistema webhook no lugar, a configuração mais simples é ter uma App lógica http Endpoint que irá ouvir quaisquer eventos que lhe sejam publicados e, em seguida, manuseá-los adequadamente (por exemplo, `https://prod-1westus.logic.azure.com:443/work` ). Para obter mais informações, consulte [Call, Trigger ou nestflows com pontos finais HTTP em aplicações lógicas.](../../logic-apps/logic-apps-http-endpoint.md)

- **ID do inquilino Azure AD** (obrigatório) – Dentro do portal Azure, exigimos que [crie uma app Azure Ative Directory (AD)](../../active-directory/develop/howto-create-service-principal-portal.md) para que possamos validar a ligação entre os nossos dois serviços por trás de uma comunicação autenticada. Para encontrar o ID do [inquilino),](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)vá ao seu Diretório Ativo Azure e selecione **Propriedades,** em seguida, procure o número **de ID** do Diretório listado (como 50c464d3-4930-494c-963c-1e951d15360e).

- **ID da aplicação AD AZure** (obrigatório) – Também precisa do seu [ID de aplicação).](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) Para obter o seu valor, vá ao seu Diretório Ativo Azure e selecione **registos de Aplicações,** em seguida, procure o número **de ID da aplicação** listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

>[!NOTE]
>O ID da aplicação AZure está associado ao seu ID de editor na sua conta Partner Center.  Certifique-se de que o mesmo ID de aplicação é usado em todas as suas ofertas.

>[!NOTE]
>Se a editora tiver duas ou mais contas diferentes no Partner Center, devem ser utilizados dois ou mais IDs de aplicações AD Azure diferentes, cada um para uma das contas. Cada conta de parceiro no Partner Center deve usar iD de aplicação AD AZure única para todas as ofertas saaS que são publicadas através desta conta.

**Selecione Guardar o projeto** antes de continuar.

## <a name="plan-overview"></a>Visão geral do plano

Esta página permite-lhe fornecer uma variedade de opções de plano dentro da mesma oferta. Estes planos (por vezes referidos como SKUs) poderiam diferir em termos de versão, rentabilização ou níveis de serviço. Tem de criar pelo menos um plano para vender a sua oferta no mercado.

Uma vez criado, verá os nomes do seu plano, IDs, modelos de preços, disponibilidade (Público ou Privado), estado de publicação atual e quaisquer ações disponíveis.

**As ações** disponíveis no Resumo do **Plano** variam consoante o estado atual do seu plano e podem incluir:

- Se o estado do plano for **Rascunho** - Eliminar rascunho
- Se o estado do plano for **Live** - Pare de vender plano ou sync público privado

**Criar um novo plano** (mínimo de um plano para quem escolher para vender através da Microsoft)

- **ID do plano:** Crie um ID de plano único para cada plano nesta oferta. Este ID será visível para os clientes nos modelos URL e Azure Resource Manager (se aplicável). Utilize apenas caracteres minúsculos, alfanuméricos, traços ou sublinhados. Um máximo de 50 caracteres são permitidos para este iD do plano. O ID não pode ser modificado após a seleção criar.
- **Nome do plano:** Os clientes verão este nome ao decidir qual o plano a selecionar dentro da sua oferta. Crie um nome de oferta único para cada plano nesta oferta. O nome do plano é usado para diferenciar planos de software que podem fazer parte da mesma oferta (por exemplo, nome de oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Listagem de planos

Esta página permite definir o nome e descrição do plano. <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **Nome** – Prepopulado com base na sua pré-visualização Nova entrada no **plano** e aparecerá como o título do "Plano de Software" da sua oferta exibido no mercado.
- **Descrição** – Esta descrição é uma oportunidade para explicar o que torna este plano de software único e quaisquer diferenças em parte de outros planos de software dentro da sua oferta. Pode conter até 500 caracteres.

**Selecione Guardar o projeto** antes de continuar.

#### <a name="pricing-and-availability"></a>Preços e disponibilidade

Esta página permite configurar os mercados em que este plano estará disponível, o modelo de rentabilização, preço e prazo de faturação pretendido. Além disso, pode indicar se deve tornar o plano visível para todos ou apenas para clientes específicos (um público privado).

#### <a name="markets-optional"></a>Mercados (opcional)

Todos os planos devem estar disponíveis em pelo menos um mercado. **Selecione os mercados editar** e selecione a caixa de verificação para qualquer local de mercado onde gostaria de disponibilizar este plano. Esta página inclui uma caixa de pesquisa e opção para selecionar países/regiões "Tax Remitted", em que a Microsoft remete as vendas e usa o imposto em seu nome.

Se já estabeleceu preços para o seu plano em Dólares dos Estados Unidos (USD) e adiciona outra localização de mercado, o preço do novo mercado será calculado de acordo com as taxas de câmbio atuais. Reveja o preço de cada mercado antes de publicar. Ver preços utilizando o link "Preços de exportação (xlsx)" depois de guardar as suas alterações.

**Selecione Guardar** antes de continuar.

#### <a name="pricing"></a>Preços

##### <a name="pricing-model"></a>Modelo preços

**Taxa fixa** – Permitir o acesso à sua oferta com um preço fixo único mensal ou anual. Isto é por vezes referido como preços baseados no site. Com este modelo de preços, pode definir opcionalmente planos medidos que usam o serviço de medição de mercado API para cobrar aos clientes de acordo com unidades não padrão.  Para obter mais informações sobre a faturação medido, consulte [a faturação medido utilizando o serviço de medição do mercado.](./saas-metered-billing.md)  Também deve utilizar esta opção se o comportamento de utilização estiver em rajadas para o seu serviço SaaS.  Não recomendamos ao cliente que altere frequentemente os planos diariamente ou de hora em hora.

**Por utilizador** – Ative o acesso à sua oferta com o preço baseado no número de utilizadores que acedem à oferta ou ocupam lugares. Este modelo baseado no utilizador permite-lhe definir o número mínimo e máximo de utilizadores permitidos com base no preço. Desta forma, diferentes pontos de preço podem ser configurados com base no número de utilizadores configurando vários planos.  Estes campos são opcionais. Se não for selecionado, o número de utilizadores será interpretado como não tendo um limite (min de 1 e máximo de tantos quantos o sistema pode suportar). Estes campos podem ser editados como parte de uma atualização do seu plano.

Uma vez publicado, a escolha do modelo de preços de faturação não pode ser alterada. Além disso, todos os planos para a mesma oferta devem partilhar o mesmo modelo de preços.

##### <a name="user-limits"></a>Limites de utilizador

Se aplicável, selecione e estabeleça limites mínimos e máximos de utilização.

##### <a name="billing-term-and-price"></a>Prazo de faturação e preço

Selecione o **termo** e **o preço** que os clientes devem pagar o preço listado. Pelo menos um preço mensal ou anual deve ser fornecido, ou ambas as opções podem ser disponibilizadas aos clientes.

Os preços fixados em USD (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados utilizando as taxas de câmbio atuais quando guardados. Validar estes preços antes de publicar exportando a folha de cálculo de preços e revendo o preço em cada mercado. Se quiser definir preços personalizados num mercado individual, modifique e importe a folha de cálculo de preços. Você é responsável por validar este preço e possuir estas configurações.
*\*Primeiro, tem de guardar as suas alterações de preços para permitir a exportação de dados de preços.*

Reveja cuidadosamente os seus preços antes de publicar, pois existem algumas restrições sobre o que pode mudar após a publicação de um plano:

- Uma vez publicado um plano, o modelo de preços não pode ser alterado.
- Uma vez que um termo de faturação é publicado para um plano, não pode ser removido mais tarde.
- Uma vez que um preço para um mercado no seu plano é publicado, não pode ser alterado mais tarde.

#### <a name="free-trial"></a>Avaliação Gratuita

As ofertas da SaaS através do mercado comercial permitem-lhe proporcionar um teste gratuito de um mês ao vender através da Microsoft. Para todos os modelos e termos de faturação, exceto planos medidos, os testes gratuitos são suportados. Esta opção permite que os clientes tenham uma barreira baixa à entrada através de um mês de acesso gratuito.  Se optar por permitir um teste gratuito para planos dentro da sua oferta, o cliente não poderá converter-se a uma subscrição paga antes do final do período inicial de um mês.  Durante este período, os clientes que adquirem a sua oferta podem experimentar qualquer um dos planos suportados que tenham o teste gratuito habilitado e convertido entre eles.  A conversão para uma subscrição paga é feita automaticamente no final do prazo.

>[!NOTE]
>Se o cliente optar por se converter num plano sem testes gratuitos, a conversão acontecerá, mas o teste gratuito será perdido imediatamente. Além disso, uma vez que um cliente começa a pagar por um plano, eles não podem mais obter teste gratuito na mesma subscrição novamente, mesmo que eles se convertam para um SKU que suporta testes gratuitos.

Pode configurar um teste gratuito para cada plano da sua oferta aqui. Selecione a caixa de verificação para permitir um ensaio de um mês.

![Caixa de verificação de um mês grátis](./media/free-trial-enable.png)

>[!NOTE]
>Uma vez publicada a sua oferta transacionável com um teste gratuito, não pode ser desativada para esse plano. Certifique-se de que esta definição está correta para a primeira publicação para evitar ter de recriar o plano.

Para obter informações sobre subscrições de clientes que atualmente participam num teste gratuito, utilize a nova propriedade API `isFreeTrial` , que será marcada como verdadeira ou falsa. Para mais informações, consulte a [API de Assinatura SaaS Get](pc-saas-fulfillment-api-v2.md#get-subscription).

>[!NOTE]
>Os ensaios gratuitos não são apoiados para planos que alavancam o serviço de medição do mercado.

#### <a name="plan-visibility"></a>Visibilidade do plano

Pode configurar cada plano para ser visível para todos ou apenas para um público específico à sua escolha. Você pode atribuir adesão a este público restrito usando IDs de inquilino Azure.

##### <a name="privacy"></a>Privacidade

Selecione **Este é um plano privado** para tornar o seu plano privado e visível apenas para o público restrito à sua escolha. Uma vez publicado como um plano privado, pode atualizar o público ou optar por disponibilizar o plano a todos. Uma vez publicado um plano como visível para todos, deve permanecer visível para todos (não pode ser configurado novamente como um plano privado).

##### <a name="restricted-audience-tenant-ids"></a>**Público Restrito (IDs de inquilino)**

Atribua ao público que terá acesso a este plano privado. O acesso é atribuído usando iDs de inquilino com a opção de incluir uma descrição de cada ID do inquilino atribuído. Um máximo de 10 IDs de inquilino pode ser adicionado, ou 20.000 clientes iDs inquilinos se importar um arquivo de folha de cálculo .csv.

Um inquilino é uma representação de uma organização, com um ID representado como um GUID (Globalmente Unique Identifier, um número inteiro de 128 bits usado para identificar recursos). É um caso dedicado de Azure AD que uma organização ou programador de aplicações recebe quando a organização ou desenvolvedor de aplicações cria uma relação com a Microsoft, por exemplo, ao inscrever-se para a Azure, Microsoft Intune ou Microsoft 365. Cada inquilino do Azure AD é distinto e separado dos outros inquilinos do Azure AD. Para verificar o inquilino, inicie sessão no Portal do Azure com a conta que pretende utilizar para gerir a sua aplicação. Se tiver um inquilino, irá ter automaticamente sessão iniciada no mesmo e verá o nome do inquilino imediatamente por baixo do nome da sua conta. Se passar com o rato por cima do nome da sua conta, no canto superior direito do portal do Azure, verá o seu nome, e-mail, diretório e ID de inquilino (um GUID) e o seu domínio. Se a sua conta estiver associada a vários inquilinos, pode selecionar o nome da sua conta para abrir um menu onde pode alternar entre inquilinos. Cada inquilino tem o seu próprio ID de inquilino. Você também pode procurar o ID do inquilino da sua organização usando um URL de nome de domínio em [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

Enquanto o SaaS oferece iDs de inquilino para definir um público privado, outros tipos de oferta podem usar IDs de assinatura Azure (que também são representados como GUIDs).

> [!NOTE]
> O público privado (ou público restrito) difere de uma audiência de pré-visualização. Na página **[pré-visualização,](#preview-audience)** pode definir um público de pré-visualização. Um público de pré-visualização tem acesso à sua oferta *antes* da oferta ser publicada ao vivo no mercado. Embora a designação do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos (privados ou não), mas apenas durante o período limitado de pré-visualização enquanto o plano é testado e validado.

**Selecione Guardar o projeto** antes de continuar.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Exemplo lista de planos dentro de uma oferta de mercado

:::image type="content" source="media/marketplace-plan.png" alt-text="Exemplo de anúncio de plano de mercado com notas.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Nome do plano
2. Descrição do plano

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Público revendedor de fornecedor de solução em nuvem (CSP)

Optar por disponibilizar a sua oferta no programa CSP permite que os Fornecedores de Soluções Cloud vendam o seu produto como parte de uma solução agregada aos seus clientes. Para mais informações, consulte [os Fornecedores de Soluções cloud.](https://go.microsoft.com/fwlink/?linkid=2111109)

## <a name="publish"></a>Publicar

Uma vez concluídas todas as secções necessárias da oferta, selecione **'Rever' e publicar** no canto superior direito do portal.

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Se esta é a sua primeira vez publicando esta oferta, você pode:

- Consulte o estado de conclusão de cada secção da oferta.
  - **Não foi iniciado** – A secção não foi tocada e precisa de ser concluída.
  - **Incompleto** – A secção tem erros que precisam de ser corrigidos ou exigem mais informações a serem fornecidas. Tens de voltar à secção e atualizá-la.
  - **Completo** – A secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado completo antes de poder submeter a oferta.
- Forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente, além de quaisquer notas suplementares úteis para entender a sua aplicação.
- Submeta a oferta de publicação selecionando Enviar por ela que **não se possa fazer uma publicação.** Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta estiver disponível para que você reveja e aprove. Volte ao Partner Center e selecione **Go-live** para publicar a sua oferta ao público (ou se uma oferta privada, para o público privado).

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

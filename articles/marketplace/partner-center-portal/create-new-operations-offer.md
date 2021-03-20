---
title: Criar uma oferta Dynamics 365 para Operações no Microsoft AppSource
description: Como criar uma oferta Dynamics 365 para Operações no Microsoft AppSource. Enuprova ou venda a sua oferta no AppSource ou através do programa Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: bcb7f2d008c54981223b4839f9a750e6ee9ac5cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97355155"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Criar uma oferta do Dynamics 365 for Operations

Este artigo descreve como criar uma nova oferta Dynamics 365 para Operações. [O Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) é um serviço de planeamento de recursos empresariais (ERP) que suporta financiamento avançado, operações, fabricação e gestão de cadeias de fornecimento. Todas as ofertas para a Dynamics 365 for Operations devem passar pelo nosso processo de certificação.

Antes de iniciar, [crie uma conta de Mercado Comercial no Partner Center](create-account.md) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

>[!NOTE]
> Uma vez publicada uma oferta, as edições à oferta só serão atualizadas no Partner Center e na loja online depois de reenviar a oferta para publicação.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página 'Vista Geral', selecione **+ Nova oferta**  >  **Dynamics 365 para operações**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-dynamics-365-operations.png)

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos de Gestor de Recursos Azure, se aplicável.
- O ID da oferta combinado com o ID da Editora deve ter menos de 40 caracteres de comprimento.
- Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços. Por exemplo, se o seu ID de editor estiver `testpublisherid` e introduzir a oferta de **teste-1,** o endereço web da oferta será `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Este ID não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- Este nome não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

### <a name="alias"></a>Alias

Introduza um nome descritivo que usaremos para se referir a esta oferta apenas dentro do Partner Center. Este nome (pré-povoado com o que entrou quando criou a oferta) não será usado no mercado e é diferente do nome da oferta mostrado aos clientes. Se pretender atualizar o nome da oferta mais tarde, aceda à página ['Lista de](#offer-listing) Ofertas'.

### <a name="setup-details"></a>Detalhes da configuração

Para **Como pretende que os potenciais clientes interajam com esta oferta de listagem?**

- **Obtenha-o agora (grátis)** – Liste a sua oferta aos clientes gratuitamente.
- **Teste gratuito (listagem)** – Liste a sua oferta aos clientes com um link para um teste gratuito. Os testes gratuitos de listagem de ofertas são criados, geridos e configurados pelo seu serviço e não têm subscrições geridas pela Microsoft.

    > [!NOTE]
    > Os tokens que a sua aplicação receberá através do seu link de teste só podem ser utilizados para obter informações do utilizador através do Azure Ative Directory (Azure AD) para automatizar a criação de conta na sua app. As contas da Microsoft não são suportadas para autenticação utilizando este token.

- **Contacte-me** – Colete informações de contacto com o cliente ligando o seu sistema de Gestão de Relacionamento com o Cliente (CRM). O cliente será solicitado permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e fonte de mercado onde encontraram a sua oferta, serão enviados para o sistema CRM que configuraste. Para obter mais informações sobre a configuração do seu CRM, consulte [os leads do Cliente](#customer-leads).

### <a name="test-drive"></a>Unidade de teste

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "experimentar antes de comprar", resultando numa maior conversão e na geração de leads altamente qualificados. Para saber mais, comece com [o que é test drive](../what-is-test-drive.md).

Para ativar uma unidade de teste durante um período de tempo fixo, selecione a caixa de **verificação de acionamento de test drive.** Para remover o test drive da sua oferta, limpe esta caixa de verificação.

### <a name="customer-leads"></a>Ligações ao cliente

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, consulte [a visão geral da gestão do chumbo.](./commercial-marketplace-get-customer-leads.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias e indústrias usadas para agrupar a sua oferta no mercado, a sua versão de aplicação e os contratos legais que suportam a sua oferta.

### <a name="categories"></a>Categorias

Selecione categorias e subcategorias para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Não se esqueça de descrever como a sua oferta suporta estas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se não for aplicável nenhuma subcategoria à sua oferta, selecione **Não aplicável**.

Consulte a lista completa de categorias e subcategorias na [Listagem de Ofertas Boas Práticas.](../gtm-offer-listing-best-practices.md)

### <a name="industries"></a>Indústrias

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versão da aplicação

Insira o número de versão da sua oferta. Os clientes verão esta versão listada na página de detalhes da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça os seus próprios termos e condições legais aqui. Também pode fornecer o endereço onde os seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

**Selecione Guardar o projeto** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

<!--This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.

Define marketplace details such as offer name, description, and images for each language/market. Select the language/market name to provide this info.-->This page lets you define offer details such as offer name, description, links, and contacts.

> [!NOTE]
> Forneça detalhes de listagem de ofertas apenas em um idioma. Não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um *URL de ligação útil* para oferecer conteúdo em um idioma diferente do utilizado no conteúdo da listagem de Oferta.

Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (quaisquer preços listados são apenas para fins e não se destinam a refletir custos reais):
<!-- update screen? -->
:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Ilustra como esta oferta aparece no Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logótipo
2. Produtos
3. Categorias
4. Indústrias
5. Endereço de suporte (link)
6. Termos de utilização
7. Política de privacidade
8. Nome da oferta
9. Description
10. Screenshots/vídeos

### <a name="marketplace-details"></a>Detalhes do mercado

O **Nome** que entrar aqui será mostrado aos clientes como o título da sua listagem de oferta. Este campo é pré-povoado com o texto que inseriu para **o pseudónimo Oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser comercializado (e pode incluir símbolos de marca registada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

Forneça uma breve descrição da sua oferta, até 100 caracteres, para o resumo dos **resultados da Pesquisa.** Esta descrição pode ser usada em resultados de pesquisa no mercado.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Pode introduzir opcionalmente até três **palavras-chave de pesquisa** para ajudar os clientes a encontrar a sua oferta no mercado. Para obter melhores resultados, utilize também estas palavras-chave na sua descrição.

Se quiser informar os clientes com os **quais a sua aplicação funciona,** insira até três nomes de produtos.

### <a name="helpprivacy-urls"></a>URLs de ajuda/privacidade

Introduza o **link Ajuda para a sua aplicação** (URL) onde os clientes podem saber mais sobre a sua oferta. O URL de ajuda não pode ser o mesmo que o URL de suporte.

Insira o **link de política de privacidade** (URL) para a política de privacidade da sua organização. Você é responsável por garantir que a sua aplicação está em conformidade com as leis e regulamentos de privacidade, e por fornecer uma política de privacidade válida.

### <a name="contact-information"></a>Informações de contacto

Forneça o nome, e-mail e número de telefone para um **contacto de suporte** e um contacto de **Engenharia.** Estas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e poderão ser fornecidas aos parceiros da CSP.

Na secção **de contacto de Suporte,** forneça o **URL de suporte** onde os parceiros da CSP possam encontrar suporte para a sua oferta. O URL de suporte não pode ser o mesmo que o URL de ajuda.

### <a name="supporting-documents"></a>Documentos comprovativos

Forneça pelo menos um (e até três) documentos de marketing relacionados aqui, tais como livros brancos, brochuras, checklists ou apresentações, em formato PDF.

### <a name="marketplace-media"></a>Meios de comunicação do mercado

Forneça logotipos e imagens que serão utilizados ao mostrar a sua oferta aos clientes. Todas as imagens devem estar em formato PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="logos"></a>Logotipos

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center utilizará isto para criar outros tamanhos necessários. Pode substituir opcionalmente isto por uma imagem diferente mais tarde.

Estes logótipos são utilizados em diferentes locais da listagem:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de ecrã

Adicione imagens que mostram como a sua oferta funciona. Pelo menos uma imagem é necessária, e pode somar até cinco. Todas as imagens devem ser de 1280 x 720 pixels e em formato PNG.

#### <a name="videos"></a>Vídeos

Pode, opcionalmente, adicionar até quatro vídeos que demonstrem a sua oferta. Os vídeos devem ser apresentados num site externo. Para cada um, insira o nome do vídeo, o seu endereço e uma imagem miniatura do vídeo (1280 x 720 pixels).

Para obter recursos adicionais de listagem de mercado, consulte [as melhores práticas para anúncios de oferta de mercado.](../gtm-offer-listing-best-practices.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="availability"></a>Disponibilidade

Esta página permite definir onde e como disponibilizar a sua oferta.

### <a name="markets"></a>Mercados

Para especificar os mercados onde a sua oferta deve estar disponível, **selecione mercados editar** para exibir a janela popup **de seleção de Mercado.**

Selecione pelo menos um mercado. **Escolha Selecione tudo** para disponibilizar a sua oferta em todos os mercados possíveis, ou selecione apenas os mercados específicos que deseja. Quando terminar, **selecione Save**.

As suas seleções aqui aplicam-se apenas a novas aquisições; se alguém já tiver a sua app num determinado mercado e depois remover esse mercado, as pessoas que já têm a oferta nesse mercado podem continuar a usá-la, mas nenhum cliente novo nesse mercado poderá receber a sua oferta.

> [!IMPORTANT]
> É da sua responsabilidade cumprir quaisquer requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center. Mesmo que selecione todos os mercados, leis locais, restrições ou outros fatores podem impedir que certas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Audiência de pré-visualização

Antes de publicar a sua oferta ao vivo para a oferta de mercado mais ampla, primeiro terá de a disponibilizar a um público de **pré-visualização** limitado. Introduza uma **tecla Oculta** (qualquer fio utilizando apenas letras minúsculas e/ou números) aqui. Os membros do seu público de pré-visualização podem usar esta chave de ocultação como um símbolo para visualizar uma pré-visualização da sua oferta no mercado.

Em seguida, quando estiver pronto para disponibilizar a sua oferta e remover a restrição de pré-visualização, terá de remover a **tecla Oculta** e publicar novamente.

**Selecione Guardar o projeto** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Esta página define os detalhes técnicos utilizados para se ligar à sua oferta. Esta ligação permite-nos oferecer a sua oferta para o cliente final se optarem por adquiri-la.

### <a name="solution-identifier"></a>Identificador de solução

Forneça o identificador de solução (GUID) para a sua solução.

Para encontrar o seu identificador de solução:

1. Nos Serviços de Ciclo de Vida da Microsoft Dynamics (LCS), selecione **Gestão de Soluções.**
2. Selecione a sua solução e, em seguida, procure o **identificador de solução** na **visão geral** do pacote. Se o identificador estiver em branco, **selecione Editar** e volte a publicar o seu pacote e tente novamente.

### <a name="release-version"></a>Versão de lançamento

Selecione a versão da Dynamics 365 para Finanças e Operações com a qual esta solução funciona.

**Selecione Guardar o projeto** antes de continuar.

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing.
-->
## <a name="supplemental-content"></a>Conteúdo suplementar

Esta página permite-lhe fornecer informações adicionais para nos ajudar a validar a sua oferta. Esta informação não é mostrada aos clientes ou publicada no mercado.

### <a name="validation-assets"></a>Ativos de validação

Faça o upload de um [Relatório de Análise de Personalização (CAR)](/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) nesta secção. Este relatório é gerado analisando os seus modelos de personalização e extensão, com base num conjunto predefinido de regras de boas práticas.

Este ficheiro deve estar .xls ou .xlsx formato. Se tiver mais de um relatório, pode enviar um ficheiro .zip contendo todos os relatórios.

### <a name="does-solution-include-localizations"></a>A solução inclui as localizações?

Selecione **Sim** se a solução permitir a utilização de normas e políticas locais (por exemplo, se acomodar as diferentes regras de folha de pagamento exigidas por diferentes países/regiões). Caso contrário, selecione **Não**.

### <a name="does-solution-enable-translations"></a>A solução permite a tradução?

Responder **Sim** se o texto da sua solução puder ser traduzido para outras línguas. Caso contrário, selecione **Não**.

**Selecione Guardar o projeto** antes de continuar.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Depois de completar todas as secções necessárias da oferta, selecione **'Rever' e publicar** no canto superior direito do portal.

Se é a sua primeira vez publicando esta oferta, você pode:

- Consulte o estado de conclusão de cada secção da oferta.
    - **Não foi iniciado** – A secção não foi tocada e precisa de ser concluída.
    - **Incompleto** – A secção tem erros que precisam de ser corrigidos ou requer mais informações. Volte para a secção(s) e atualize-a.
    - **Completo** – A secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado completo antes de poder submeter a oferta.
- Na secção **Notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação seja testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação. Para obter mais informações sobre instruções de teste e completar uma primeira publicação, consulte [a validação funcional de Finanças e Operações da AppSource Dynamics 365](../dynamics-365-finance-operations-functional-validation.md).
- Submeta a oferta de publicação selecionando Enviar por ela que **não se possa fazer uma publicação.** Enviaremos um e-mail quando uma versão de pré-visualização da oferta estiver disponível para revisão e aprovação. Volte ao Partner Center e selecione **Go-live** para publicar a sua oferta ao público.

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)
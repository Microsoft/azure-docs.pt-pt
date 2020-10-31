---
title: Criar uma oferta Dynamics 365 Business Central - Mercado comercial da Microsoft
description: Conheça os passos e considerações para criar uma nova oferta Dynamics 365 Business Central no mercado comercial da Microsoft no Partner Center. Pode listar ou vender a sua oferta no Azure Marketplace ou através do programa Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 08/07/2020
ms.openlocfilehash: 71cbc09f21e21c325665809953f209615d835124
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130158"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Criar uma oferta do Dynamics 365 Business Central

Este artigo explica como criar uma nova oferta da Dynamics 365 Business Central. [O Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) é um sistema de planeamento de recursos empresariais (ERP) que lida com uma vasta gama de processos de negócio, incluindo finanças, operações, cadeia de fornecimento, CRM e gestão de projetos e comércio eletrónico. Os pacotes premium também suportam modelo de implantação clássico e fabricação. Todas as ofertas para a Dynamics 365 Business Central devem passar pelo nosso processo de certificação.

Antes de iniciar, [crie uma conta de Mercado Comercial no Partner Center](create-account.md) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview** .
3. Na página 'Visão Geral', selecione **+ Nova oferta**  >  **Dynamics 365 business central** .

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> Após a publicação de uma oferta, as edições feitas no Partner Center só aparecem nas lojas online depois de republicarem a oferta. Certifique-se de que é sempre republicante depois de escorção.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta** . Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos de Gestor de Recursos Azure, se aplicável.
- O ID da oferta combinado com o ID da Editora deve ter menos de 40 caracteres de comprimento.
- Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços. Por exemplo, se o seu ID editor for testpublisherid e introduzir **test-offer-1** aqui, o endereço web da oferta será `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar** .

Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- O pseudónimo Oferta não pode ser alterado depois de selecionar **Criar** .

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

Siga estes passos para definir a sua oferta.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Como pretende que os potenciais clientes interajam com esta oferta de listagem?

Selecione a opção que gostaria de usar para esta oferta.

#### <a name="get-it-now-free"></a>Obtê-lo agora (grátis)

Enuse a sua oferta aos clientes gratuitamente.

#### <a name="free-trial-listing"></a>Ensaio gratuito (listagem)

Enuse a sua oferta aos clientes com um link para um teste gratuito. Os testes gratuitos de listagem de ofertas são criados, geridos e configurados pelo seu serviço e não têm subscrições geridas pela Microsoft.

> [!NOTE]
> Os tokens que a sua aplicação receberá através do seu link de teste só podem ser utilizados para obter informações do utilizador através do Azure Ative Directory (Azure AD) para automatizar a criação de conta na sua app. As contas da Microsoft não são suportadas para autenticação utilizando este token.

#### <a name="contact-me"></a>Contacte-me

Recolher informações de contacto com o cliente ligando o seu sistema de Gestão de Relacionamento com o Cliente (CRM). O cliente será solicitado permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e fonte de mercado onde encontraram a sua oferta, serão enviados para o sistema CRM que configuraste. Para obter mais informações sobre a configuração do seu CRM, consulte [os leads do Cliente](#customer-leads).

### <a name="test-drive"></a>Unidade de teste

Neste ponto, as ofertas da Dynamics 365 Business Central não suportam test drive. Para remover o test drive da sua oferta, limpe a caixa **de verificação de test drive.**

### <a name="customer-leads"></a>Ligações ao cliente

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, consulte [a visão geral da gestão do chumbo.](./commercial-marketplace-get-customer-leads.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias e indústrias usadas para agrupar a sua oferta no mercado, a sua versão de aplicação e os contratos legais que suportam a sua oferta.

### <a name="category"></a>Categoria

Selecione categorias e subcategorias para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Não se esqueça de descrever como a sua oferta suporta estas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se não for aplicável nenhuma subcategoria à sua oferta, selecione **Não aplicável** .

Consulte a lista completa de categorias e subcategorias na [Listagem de Ofertas Boas Práticas.](../gtm-offer-listing-best-practices.md)

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versão da aplicação

Insira o número de versão da sua oferta. Os clientes verão esta versão listada na página de detalhes da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça os seus próprios termos e condições legais no campo **termos e condições.** Também pode fornecer o URL onde os seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

**Selecione Guardar o projeto** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

É aqui que define detalhes para a sua oferta, como nome, descrição e imagens.

> [!NOTE]
> Você pode fornecer detalhes de listagem de oferta em apenas um idioma. Não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um *URL de ligação de ajuda* para oferecer conteúdo em um idioma diferente do utilizado no conteúdo da listagem de Oferta.

Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (quaisquer preços listados são apenas para fins e não se destinam a refletir custos reais):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Ilustra como esta oferta aparece no Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logótipo
2. Produtos
3. Categorias
4. Endereço de suporte (link)
5. Termos de utilização
6. Política de privacidade
7. Nome da oferta
8. Resumo
9. Description
10. Screenshots/vídeos

### <a name="name"></a>Name

O nome que introduzir aqui será mostrado aos clientes como o título da sua listagem de oferta. Este campo é pré-povoado com o texto que inseriu para **o pseudónimo Oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser comercializado (e pode incluir símbolos de marca registada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Breve descrição

Forneça uma breve descrição da sua oferta, até 100 caracteres. Esta descrição pode ser usada em resultados de pesquisa no mercado.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Pesquisar palavras-chave

Pode introduzir opcionalmente até três palavras-chave de pesquisa para ajudar os clientes a encontrar a sua oferta no mercado. Para obter os melhores resultados, tente usar estas palavras-chave na sua descrição também.

### <a name="products-your-app-works-with"></a>Produtos com os seus produtos funcionam

Se quiser informar os clientes de que a sua aplicação funciona com produtos específicos, insira até três nomes de produtos aqui.

### <a name="helpprivacy-urls"></a>URLs de ajuda/privacidade

Esta secção permite-lhe fornecer links para ajudar os clientes a entender mais sobre a sua oferta.

#### <a name="help-link"></a>Ligação de ajuda

Insira o URL onde os clientes podem saber mais sobre a sua oferta. O **seu link de ajuda** não pode ser o mesmo que o SEU URL de **suporte** (explicado abaixo).

#### <a name="privacy-policy-link"></a>Ligação de política de privacidade

Insira o URL na política de privacidade da sua organização. Você é responsável por garantir que a sua aplicação está em conformidade com as leis e regulamentos de privacidade, e por fornecer uma política de privacidade válida.

### <a name="contact-information"></a>Informações de Contacto

Nesta secção, deve fornecer o nome, e-mail e número de telefone para um **contacto de Suporte** e um contacto de **Engenharia** . Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft, e poderá ser fornecida aos parceiros da CSP.

Na secção **de contacto de Suporte,** também deve fornecer o **URL de suporte** onde os parceiros da CSP podem encontrar suporte para a sua oferta. O URL de suporte não pode ser o mesmo que o link **ajuda.**

### <a name="supporting-documents"></a>Documentos comprovativos

Forneça aqui pelo menos um (e até três) documentos de marketing relacionados, tais como livros brancos, brochuras, listas de verificação ou apresentações. Estes documentos devem estar em formato .pdf.

### <a name="marketplace-images"></a>Imagens do mercado

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center irá usá-lo para criar um logótipo **pequeno.** Pode substituir opcionalmente isto por uma imagem diferente mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, necessário)
- **Pequeno** (48 x 48 px, opcional)

Estes logótipos são utilizados em diferentes locais da listagem:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o `https://upload.xboxlive.com` serviço utilizado pelo Partner Center.

#### <a name="screenshots"></a>Capturas de ecrã

Adicione imagens que mostram como a sua oferta funciona. São necessárias pelo menos três imagens, e pode somar até cinco. Todas as imagens devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Pode, opcionalmente, adicionar até cinco vídeos que demonstrem a sua oferta. Estes vídeos devem ser apresentados no YouTube e/ou Vimeo. Para cada um, insira o nome do vídeo, o seu URL e uma imagem miniatura do vídeo (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Recursos de listagem de mercado adicionais

[Melhores práticas para anúncios de oferta de mercado](../gtm-offer-listing-best-practices.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="availability"></a>Disponibilidade

Esta página dá-lhe opções sobre onde e como disponibilizar a sua oferta.

### <a name="markets"></a>Mercados

Esta secção permite especificar os mercados em que a sua oferta deve estar disponível. Para tal, **selecione mercados editar ,** que irão exibir a janela popup **de seleção de mercado.**

Selecione pelo menos um mercado para publicar a sua oferta. **Escolha Selecione tudo** para disponibilizar a sua oferta em todos os mercados possíveis ou selecione os mercados específicos que pretende adicionar.

As suas seleções aqui aplicam-se apenas a novas aquisições; se alguém já tiver a sua app num determinado mercado, e mais tarde remover esse mercado, as pessoas que já têm a oferta nesse mercado podem continuar a usá-la, mas nenhum cliente novo nesse mercado poderá receber a sua oferta.

> [!IMPORTANT]
> É da sua responsabilidade cumprir quaisquer requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center.

Tenha em mente que mesmo que selecione todos os mercados, leis locais, restrições ou outros fatores podem impedir que certas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Audiência de pré-visualização

Antes de publicar a sua oferta ao vivo para a oferta de mercado mais ampla, primeiro terá de a disponibilizar a um público de **pré-visualização** limitado. Introduza uma **tecla Oculta** (qualquer fio utilizando apenas letras minúsculas e/ou números) aqui. Os membros do seu público de pré-visualização podem usar esta chave de ocultação como um símbolo para visualizar uma pré-visualização da sua oferta no mercado.

Em seguida, quando estiver pronto para disponibilizar a sua oferta e remover a restrição de pré-visualização, terá de remover a **tecla Oculta** e publicar novamente.

**Selecione Guardar o projeto** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Esta página define os detalhes técnicos utilizados para se ligar à sua oferta. Esta ligação permite-nos oferecer a sua oferta para o cliente final se optarem por adquiri-la.

### <a name="package-type"></a>Tipo de pacote

Selecione a opção que se aplica à sua oferta:

* **Add On** – Uma aplicação Add-on alarga a experiência e a funcionalidade existente da Dynamics 365 Business Central. Para mais detalhes, consulte [as aplicações Add-on](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
* **Connect** – Uma aplicação Connect pode ser utilizada no cenário em que deve ser estabelecida uma ligação ponto-a-ponto entre a Dynamics 365 Business Central e uma solução ou serviço de terceiros. Para mais detalhes, consulte [Connect Apps](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Upload de arquivo

Se selecionou **Add On** acima, onde irá carregar o ficheiro de pacote da sua oferta, juntamente com os ficheiros do pacote para qualquer extensão em que tenha dependências.

#### <a name="extensions-package-file"></a>Ficheiro de pacote de extensões

Faça o upload do ficheiro de pacote de extensão (.app) para a sua oferta.

#### <a name="library-package-file"></a>Arquivo de pacote de biblioteca

Necessário se a sua oferta tiver de ser instalada juntamente com outra extensão que não será publicada no mercado. Se assim for, faça o upload do seu ficheiro .app aqui.

>[!NOTE]
>O ficheiro do pacote de dependência já não é utilizado. Em vez disso, faça o upload de um ficheiro de pacote de biblioteca.

### <a name="url-to-app-installation"></a>URL para instalação de aplicativos

Se selecionou **Connect** acima, forneça o endereço para a instalação da sua aplicação aqui. Para serviços conectados que não necessitem de instalação, forneça o endereço para a sua página de aterragem de serviço ou página de inscrição.

**Selecione Guardar o projeto** antes de continuar.

## <a name="test-drive-technical-configuration"></a>Configuração técnica da versão de teste

Esta página permite-lhe configurar uma demonstração ("test drive") que permite que os clientes experimentem a sua oferta antes de a comprar. Saiba mais no [que é test drive](../what-is-test-drive.md).

Para ativar uma unidade de teste, selecione a caixa **de verificação de unidade de teste** no separador de [configuração 'Oferta'.](#test-drive) Para remover o test drive da sua oferta, limpe esta caixa de verificação.

Quando terminar de configurar o seu test drive, **selecione Guardar o rascunho** antes de continuar.

## <a name="supplemental-content"></a>Conteúdo suplementar

Esta página permite-lhe fornecer informações adicionais sobre a sua oferta para nos ajudar a validar a sua oferta. Esta informação não é mostrada aos clientes ou publicada no mercado.

### <a name="target-release"></a>Versão de destino

Indicar qual o lançamento do Microsoft Dynamics Business Central os seus objetivos de solução: **Current** , **Next major** ou Next **minor** . Esta informação permite-nos testar a sua solução adequadamente.

### <a name="supported-editions"></a>Edições apoiadas

Se a sua oferta necessitar da edição Premium do Microsoft Dynamics 365 Business Central, selecione **Premium** apenas. Caso contrário, selecione **Essentials** e **Premium** .

### <a name="key-usage-scenario"></a>Cenário de utilização chave

Tem de carregar um ficheiro PDF que lista os cenários-chave de utilização da sua oferta listados num documento (formato.pdf). Todos os cenários listados aqui podem ser verificados pela nossa equipa de validação antes de aprovarmos a sua oferta para o mercado.

### <a name="app-tests-automation"></a>App testa automatização

Se a sua oferta for uma aplicação Add-on, tem de carregar um ficheiro de automatização de **testes de aplicação** (.app). Este ficheiro não é aplicável às aplicações Connect.

### <a name="test-accounts"></a>Contas de teste

Se for necessária uma conta de teste para que a nossa equipa de certificação reveja corretamente a sua oferta, faça o upload de um ficheiro .pdf, .doc ou .docx com as informações das suas **contas de Teste.**

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Depois de ter completado todas as secções necessárias da oferta, selecione **publicar** no canto superior direito do portal. Será redirecionado para a página **de Revisão e publicação.** 

Se é a sua primeira vez publicando esta oferta, você pode:

- Consulte o estado de conclusão de cada secção da oferta.
    - *Não iniciada* - significa que a secção não foi tocada e precisa de ser concluída.
    - *Incompleto* - significa que a secção tem erros que precisam de ser corrigidos ou exigem mais informações a fornecer. Volte para a secção(s) e atualize-a.
    - *Completo* - significa que a secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado completo antes de poder submeter a oferta.
- Na secção **Notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação seja testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta de publicação selecionando Enviar por ela que **não se possa fazer uma publicação.** Enviaremos um e-mail quando estiver disponível uma versão de pré-visualização da oferta para que possa rever e aprovar. Volte ao Partner Center e selecione **Go-live** para a oferta de publicar a sua oferta ao público.

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)
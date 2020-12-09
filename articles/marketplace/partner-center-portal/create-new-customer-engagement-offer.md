---
title: Criar uma oferta dinâmica 365 para o envolvimento do cliente & PowerApps no mercado comercial da Microsoft
description: Como criar uma nova Oferta dinâmica 365 para o envolvimento do cliente & oferta de PowerApps para listagem ou venda no Azure Marketplace, AppSource ou através do programa Cloud Solution Provider (CSP) no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: f20405add698c64ecedfd7375063ba934a98e158
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922430"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Criar uma oferta do Dynamics 365 for Customer Engagement & PowerApps

Este tópico explica como criar uma nova oferta Dynamics 365 para o Envolvimento do Cliente & PowerApps. Todas as aplicações para Dynamics 365 para Envolvimento do Cliente (PowerApps, Vendas, Serviço, Serviço de Projeto e Serviço de Campo) devem passar pelo nosso processo de certificação e apoiar uma experiência experimental. O processo de certificação verifica a sua solução para requisitos padrão, compatibilidade e práticas adequadas. A experiência experimental permite que os utilizadores implementem a sua solução para um ambiente Live Dynamics 365.

Antes de iniciar, [crie uma conta de Mercado Comercial no Partner Center](create-account.md) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página 'Visão Geral', selecione **+ Nova oferta**  >  **Dynamics 365 para Envolvimento do Cliente & PowerApps**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Após a publicação de uma oferta, as edições feitas no Partner Center só aparecem nas lojas online depois de republicarem a oferta. Certifique-se de que é sempre republicante depois de escorção.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos de Gestor de Recursos Azure, se aplicável.
- O ID da oferta combinado com o ID da Editora deve ter menos de 40 caracteres de comprimento.
- Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços. Por exemplo, se o seu ID editor for testpublisherid e introduzir **test-offer-1,** o endereço web da oferta será `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- Isto não pode ser alterado depois de selecionar **Criar**.

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

**Selecione Guardar o projeto** antes de continuar.

### <a name="test-drive"></a>Unidade de teste

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "experimentar antes de comprar", resultando numa maior conversão e na geração de leads altamente qualificados. Para saber mais, comece com [o que é test drive](../what-is-test-drive.md).

Para ativar uma unidade de teste durante um período de tempo fixo, selecione a caixa de **verificação de acionamento de test drive.** Para remover o test drive da sua oferta, limpe esta caixa de verificação.

### <a name="customer-leads"></a>Ligações ao cliente

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, consulte [a visão geral da gestão do chumbo.](./commercial-marketplace-get-customer-leads.md)

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

### <a name="applicable-dynamics-365-products"></a>Dinâmica aplicável 365 produtos

Selecione todos os produtos Dynamics 365 a que esta oferta se aplica.

### <a name="app-version"></a>Versão da aplicação

Insira o número de versão da sua oferta. Os clientes verão esta versão listada na página de detalhes da oferta. Se estiver apenas a atualizar o número da versão devido a alterações de marketing/descritivos, verifique apenas a caixa **de alteração de Marketing.** Esta opção permite que a oferta ignore as fases de certificação e provisionamento.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça os seus próprios termos e condições legais aqui. Também pode fornecer o endereço onde os seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

**Selecione Guardar o projeto** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Esta página apresenta os idiomas em que a sua oferta será listada. Atualmente, **inglês (Estados Unidos)** é a única opção disponível.

Defina detalhes do mercado para cada idioma/mercado aqui, como nome de oferta, descrição e imagens. Selecione o nome idioma/mercado para fornecer esta informação.

> [!NOTE]
> A oferta de conteúdo de listagem (como a descrição, documentos, screenshots e termos de utilização) não é necessária para estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um *URL de Link Útil* para oferecer conteúdo em um idioma diferente do utilizado no conteúdo da listagem de Oferta.

Aqui está um exemplo de como as informações de oferta aparecem no Microsoft AppSource (quaisquer preços listados são apenas para fins e não se destinam a refletir custos reais):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Ilustra como esta oferta aparece no Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logótipo
2. Produtos
3. Categorias
4. Endereço de suporte (link)
5. Endereço de termos de utilização (link)
6. Nome da oferta
7. Descrição
8. Screenshots/vídeos

### <a name="name"></a>Nome

O nome que introduzir aqui será mostrado aos clientes como o título da sua listagem de oferta. Este campo é pré-povoado com o texto que inseriu para **o pseudónimo Oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser comercializado (e pode incluir símbolos de marca registada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Breve descrição

Forneça uma breve descrição da sua oferta, até 100 caracteres. Esta descrição pode ser usada em resultados de pesquisa no mercado.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Pesquisar palavras-chave

Pode introduzir opcionalmente até três palavras-chave de pesquisa para ajudar os clientes a encontrar a sua oferta no mercado. Para obter melhores resultados, utilize também estas palavras-chave na sua descrição.

### <a name="products-your-app-works-with"></a>Produtos com os seus produtos funcionam

Se quiser informar os clientes de que a sua aplicação funciona com produtos específicos, insira até três nomes de produtos aqui.

### <a name="support-urls"></a>UrLs de apoio

Esta secção permite-lhe fornecer links para ajudar os clientes a entender mais sobre a sua oferta.

#### <a name="help-link"></a>Ligação de ajuda

Insira o endereço onde os clientes podem saber mais sobre a sua oferta.

#### <a name="privacy-policy-url"></a>URL de política de privacidade

Insira o endereço para a política de privacidade da sua organização. Você é responsável por garantir que a sua aplicação está em conformidade com as leis e regulamentos de privacidade, e por fornecer uma política de privacidade válida.

### <a name="contacts"></a>Contactos

Forneça o nome, e-mail e número de telefone para um **contacto de suporte** e um contacto de **Engenharia.** Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft e poderá ser fornecida aos parceiros da CSP.

Na secção **de contacto de Suporte,** também deve fornecer o **URL de suporte** onde os parceiros da CSP podem encontrar suporte para a sua oferta.

### <a name="supporting-documents"></a>Documentos comprovativos

Forneça pelo menos um (e até três) documentos de marketing relacionados aqui, tais como livros brancos, brochuras, checklists ou apresentações, em formato PDF.

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para a sua oferta. Todas as imagens devem estar em formato PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da loja

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center irá usá-lo para criar um logótipo **pequeno.** Pode substituir opcionalmente isto por uma imagem diferente mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, necessário)
- **Pequeno** (48 x 48 px, opcional)

Estes logótipos são utilizados em diferentes locais da listagem:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de ecrã

Adicione imagens que mostram como a sua oferta funciona. Pelo menos uma imagem é necessária, e pode somar até cinco. Todas as imagens devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Pode, opcionalmente, adicionar até quatro vídeos que demonstrem a sua oferta. Estes vídeos devem ser apresentados no YouTube e/ou Vimeo. Para cada um, insira o nome do vídeo, o seu URL e uma imagem miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos de listagem de mercado adicionais

[Melhores práticas para anúncios de oferta de mercado](../gtm-offer-listing-best-practices.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="availability"></a>Disponibilidade

Esta página permite definir onde e como disponibilizar a sua oferta.

### <a name="markets"></a>Mercados

Especifique os mercados em que a sua oferta deve estar disponível. Para tal, **selecione mercados editar ,** que irão exibir a janela popup **de seleção de mercado.**

Por padrão, não são selecionados mercados, mas tem de selecionar pelo menos um mercado para publicar a sua oferta. **Escolha Selecione tudo** para disponibilizar a sua oferta em todos os mercados possíveis ou selecione os mercados específicos que pretende adicionar.

As suas seleções aqui aplicam-se apenas a novas aquisições; se alguém já tiver a sua app num determinado mercado e mais tarde remover esse mercado, as pessoas que já têm a oferta nesse mercado podem continuar a usá-la, mas nenhum cliente novo nesse mercado poderá receber a sua oferta.

> [!IMPORTANT]
> É da sua responsabilidade cumprir quaisquer requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center. Mesmo que selecione todos os mercados, leis locais e restrições ou outros fatores podem impedir que determinadas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Audiência de pré-visualização

Antes de publicar a sua oferta ao vivo para a oferta de mercado mais ampla, primeiro terá de a disponibilizar a um público de **pré-visualização** limitado. Introduza uma **tecla Oculta** (qualquer fio utilizando apenas letras minúsculas e/ou números) aqui. Os membros do seu público de pré-visualização podem usar esta chave de ocultação como um símbolo para visualizar uma pré-visualização da sua oferta no mercado.

Em seguida, quando estiver pronto para disponibilizar a sua oferta e remover a restrição de pré-visualização, terá de remover a **tecla Oculta** e publicar novamente.

**Selecione Guardar o projeto** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Esta página define os detalhes técnicos utilizados para se ligar à sua oferta. Esta ligação permite-nos oferecer a sua oferta para o cliente final se optarem por adquiri-la.

### <a name="base-license-model"></a>Modelo de licença base

O modelo de licença base determina como os clientes são atribuídos a sua aplicação no CrM Admin Center. Selecione **recurso** por exemplo licenciamento baseado ou **Utilizador** se as licenças forem atribuídas uma por inquilino.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Requer saída S2S e acesso à loja segura de CRM

Verifique esta caixa para ativar a configuração do acesso de saída CRM Secure Store ou Server-to-Server (S2S). Esta funcionalidade requer consideração especializada da Equipa Dinâmica 365 durante a fase de certificação. A Microsoft entrará em contacto consigo para completar os passos adicionais para suportar esta funcionalidade.

### <a name="application-configuration-url"></a>URL de configuração de aplicação

Certifique-se de que este campo permanece em branco. Será utilizado para uso futuro. 

### <a name="crm-package"></a>Pacote CRM

No URL do seu campo **de localização do pacote,** insira o URL de uma conta de Armazenamento Azure Blob que contém o pacote de CRM carregado .zip ficheiro. Inclua uma chave SAS apenas de leitura no URL para que a Microsoft possa recolher o seu pacote para verificação.

> [!IMPORTANT]
> Para evitar um bloco de publicação, certifique-se de que a data de validade no URL do seu armazenamento Blob não expirou. Pode rever a data acedendo à sua política. Recomendamos que o **prazo de expiração** seja pelo menos um mês no futuro.

Selecione a caixa com a etiqueta **Há mais de um pacote crm no meu ficheiro de pacote,** se aplicável. Em caso afirmativo, certifique-se de incluir todos os pacotes no seu ficheiro .zip.

Para obter informações detalhadas sobre como construir o seu pacote e atualizar a sua estrutura, consulte [o Passo 3: Criar um pacote AppSource para a sua aplicação.](/powerapps/developer/common-data-service/create-package-app-appsource)

### <a name="crm-package-availability"></a>Disponibilidade de pacotes CRM

Nesta secção, selecione **+ Adicionar região** para especificar as regiões geográficas em que o seu pacote CRM estará disponível para os clientes. A implantação nas seguintes regiões soberanas requer uma autorização e validação especiais durante o processo de certificação: [Alemanha,](../../germany/index.yml) [Nuvem do Governo dos EUA](../../azure-government/documentation-government-welcome.md)e TIP.

Por predefinição, o **URL de configuração de aplicação** que introduziu acima será utilizado para cada região. Se preferir, pode introduzir um URL de configuração de aplicação separado para uma ou mais regiões específicas. 

**Selecione Guardar o projeto** antes de continuar.

## <a name="test-drive-technical-configuration"></a>Configuração técnica da versão de teste

Esta página permite-lhe configurar uma demonstração ("test drive") que permite que os clientes experimentem a sua oferta antes de a comprar. Saiba mais no [que é test drive](../what-is-test-drive.md).

Para ativar uma unidade de teste, selecione a caixa **de verificação de unidade de teste** no separador de [configuração 'Oferta'.](#test-drive) Para remover o test drive da sua oferta, limpe esta caixa de verificação.

Quando terminar de configurar o seu test drive, **selecione Guardar o rascunho** antes de continuar.

## <a name="supplemental-content"></a>Conteúdo suplementar

Esta página permite-lhe fornecer informações adicionais sobre a sua oferta para nos ajudar a validar a sua oferta. Esta informação não é mostrada aos clientes ou publicada no mercado.

### <a name="key-usage-scenario"></a>Cenário de utilização chave

Faça upload de um ficheiro PDF que lista os cenários-chave de utilização da sua oferta. Todos os cenários podem ser verificados pela nossa equipa de validação antes de aprovarmos a sua oferta para o mercado.

**Selecione Guardar o projeto** antes de continuar.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Depois de ter concluído todas as secções necessárias da oferta, selecione **'Rever' e publicar** no canto superior direito do portal.

Se é a sua primeira vez publicando esta oferta, você pode:

- Consulte o estado de conclusão de cada secção da oferta.
    - **Não foi iniciada** - A secção não foi tocada e deve ser concluída.
    - **Incompleto** - A secção tem erros que precisam de ser corrigidos ou exigem mais informações a serem fornecidas. Volte para a secção(s) e atualize-a.
    - **Completo** - A secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado completo antes de poder submeter a oferta.
- Na secção **Notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação seja testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta de publicação selecionando Enviar por ela que **não se possa fazer uma publicação.** Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta estiver disponível para que você reveja e aprove. Volte ao Partner Center e selecione **Go-live** para a oferta de publicação ao público.

## <a name="next-step"></a>Passo seguinte

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

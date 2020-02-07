---
title: Criar uma nova oferta do Dynamics 365 Business central no Marketplace comercial
description: Como criar uma nova oferta do Dynamics 365 Business central para listagem ou venda no Azure Marketplace, AppSource ou por meio do programa CSP (provedor de soluções na nuvem) usando o portal do Marketplace comercial no Microsoft Partner Center.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 4c0467039cf4fefd7625f1146c4bade99b49304d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048733"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Criar uma nova oferta do Dynamics 365 Business central

Este tópico explica como criar uma nova oferta do Dynamics 365 Business central. O [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) é um sistema de planeamento de recursos empresariais (ERP) que lida com uma vasta gama de processos de negócio, incluindo finanças, operações, cadeia de fornecimento, CRM, e gestão de projetos e comércio eletrónico. Os pacotes Premium também dão suporte ao modelo de implantação clássico e fabricação. Todas as ofertas do Dynamics 365 Business central devem passar pelo nosso processo de certificação.

Para começar a criar ofertas Da Dynamics 365 Business Central, certifique-se de que primeiro [cria uma conta partner center](./create-account.md) e abre o dashboard do Mercado [Comercial,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)com a página **de visão geral** selecionada.

![Painel do Marketplace comercial no Partner Center](./media/new-offer-overview.png)

>[!Note]
> Depois que uma oferta tiver sido publicada, edições para a oferta feita no Partner Center serão atualizadas somente no sistema e armazenará os frontais após a republicação. Certifique-se de enviar a oferta para publicação depois de fazer alterações.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

Selecione o botão **+ Nova oferta** e, em seguida, selecione o item do menu Dynamics **365 Business Central.** A nova caixa de diálogo da **oferta** aparecerá.

### <a name="offer-id-and-alias"></a>ID da oferta e alias

- **ID oferta**: Identificador único para cada oferta na sua conta. Essa ID será visível para os clientes no endereço URL para a oferta do Marketplace e os modelos de Azure Resource Manager (se aplicável). O ID da oferta deve ser caracteres alfanuméricos minúsculos (incluindo hífenes e sublinhados, mas sem espaço branco), limitado a 50 caracteres, e não pode ser alterado depois de selecionar **Criar**.  Por exemplo, se introduzir aqui a *oferta de teste-1,* o URL de oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Oferta pseudónimo**: O nome usado para se referir à oferta dentro do Partner Center. Esse nome não será usado no Marketplace e será diferente do nome da oferta e outros valores que serão mostrados aos clientes. Este valor não pode ser alterado depois de selecionar **Criar**.

Assim que introduzir o seu **Id de Oferta** e oferecer **pseudónimo,** selecione **Criar**. Em seguida, você poderá trabalhar em todas as diferentes partes da sua oferta.

## <a name="offer-setup"></a>Instalação da oferta

A página de **configuração da Oferta** pede as seguintes informações. Certifique-se de selecionar **Guardar** depois de completar estes campos.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Como você deseja que clientes potenciais interajam com esta oferta de listagem?

Selecione a opção que você deseja usar para esta oferta.

#### <a name="get-it-now-free"></a>Obtenha agora (gratuito)

Liste gratuitamente a sua oferta aos clientes, fornecendo um URL válido (a partir de *http* ou *https)* onde possam aceder à sua aplicação.  Por exemplo: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Avaliação gratuita (listagem)

Enumere a sua oferta aos clientes com um link para um teste gratuito, fornecendo um URL válido (começando com *http* ou *https)* onde possam obter um teste.  Por exemplo: `https://contoso.com/trial/my-app`. As avaliações gratuitas de listagem de ofertas são criadas, gerenciadas e configuradas pelo seu serviço e não têm assinaturas gerenciadas pela Microsoft.

> [!NOTE]
> Os tokens que seu aplicativo receberá por meio do link de avaliação só podem ser usados para obter informações do usuário por meio do Azure Active Directory (Azure AD) para automatizar a criação da conta em seu aplicativo. Não há suporte para contas da Microsoft para autenticação usando esse token.

#### <a name="contact-me"></a>Contacte-me

Colete informações de contato do cliente conectando seu sistema de gerenciamento de relacionamento com o cliente (CRM). O cliente será solicitado a fornecer permissão para compartilhar suas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a origem do Marketplace onde encontraram sua oferta, serão enviados para o sistema CRM que você configurou. Para mais informações sobre a configuração do seu CRM, consulte [Connect lead management](#connect-lead-management). 

### <a name="test-drive"></a>Test drive

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, fornecendo a eles a opção "tentar antes de comprar", resultando em uma maior conversão e na geração de clientes potenciais altamente qualificados. [Saiba mais sobre test drives.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para ativar uma unidade de teste, verifique a **caixa de acionamento ativa.** Em seguida, terá de configurar um ambiente de demonstração na [configuração técnica](#test-drive-technical-configuration) test drive para permitir que os clientes experimentem a sua oferta por um período de tempo fixo. 

#### <a name="type-of-test-drive"></a>Tipo de test drive

Selecione entre as seguintes opções:

- **[Gestor de Recursos Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Um modelo de implantação que contém todos os recursos Azure que compõem a sua solução. Os produtos que se ajustam a esse cenário usam apenas os recursos do Azure.
- **[Dinâmica 365 para o Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : A Microsoft acolhe e mantém o serviço de test drive (incluindo o provisionamento e a implementação) para um sistema de planeamento de recursos empresariais da Business Central (finanças, operações, cadeia de fornecimento, CRM, etc.).  
- **[Dinâmica 365 para envolvimento com](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** o cliente : A Microsoft acolhe e mantém o serviço de test drive (incluindo o fornecimento e a implementação) para um sistema de envolvimento com o cliente (vendas, serviço, serviço de projeto, serviço de campo, etc.).  
- **[Dinâmica 365 para Operações](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : A Microsoft acolhe e mantém o serviço de test drive (incluindo o fornecimento e a implantação) para um sistema de planeamento de recursos empresariais de Finanças e Operações (finanças, operações, fabrico, cadeia de fornecimento, etc.). 
- **[Aplicativo lógico](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Um modelo de implementação que abrange todas as arquiteturas de soluções complexas. Todos os produtos personalizados devem usar esse tipo de unidade de teste.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Uma ligação incorporada a um dashboard personalizado. Os produtos que desejam demonstrar um visual interativo Power BI devem usar esse tipo de unidade de teste. Tudo o que você precisa carregar aqui é a URL de Power BI inserida.

#### <a name="additional-test-drive-resources"></a>Recursos de test drive adicionais

- [Test Drive Técnicas Boas Práticas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Test Drive Marketing Boas Práticas](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral do test drive uma página](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Conectar gerenciamento de leads

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para mais informações, consulte a visão geral da [gestão de chumbo](./commercial-marketplace-get-customer-leads.md).

Lembre-se de **Guardar** antes de passar para a próxima secção!

## <a name="properties"></a>Propriedades

A página **Propriedades** permite definir as categorias e indústrias usadas para agrupar a sua oferta no mercado, na sua versão de app e nos contratos legais de apoio à sua oferta. Selecione **Guardar** depois de completar esta página.

### <a name="category"></a>Categoria

Selecione no mínimo um e no máximo três categorias, que serão usadas para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Lembre-se de chamar como sua oferta dá suporte a essas categorias na descrição da oferta. 

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versão da aplicação

Insira o número de versão da sua oferta. Os clientes verão essa versão listada na página de detalhes da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça os seus próprios termos e condições legais no campo **Termos e Condições.** Também pode fornecer o URL onde os seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

## <a name="offer-listing"></a>Listagem de ofertas

A página de listagem da oferta exibe os idiomas nos quais sua oferta será listada. Atualmente, **o inglês (Estados Unidos)** é a única opção disponível.

Você precisará definir detalhes do Marketplace (nome da oferta, descrição, imagens, etc.) para cada idioma/mercado. Selecione o idioma/nome do mercado para fornecer essas informações.

> [!NOTE]
> Oferecer conteúdo de listagem (como a descrição, documentos, screenshots, termos de uso, etc.) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação só está disponível em [língua não inglesa]." Também é aceitável fornecer um URL de *Link Útil* para oferecer conteúdo num idioma diferente do utilizado no conteúdo de listagem de oferta.

### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Este campo é pré-povoado com o texto que inscreveu para **o pseudónimo oferta** quando criou a oferta, mas pode alterar este valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Descrição curta

Forneça uma breve descrição da sua oferta (até 100 caracteres), que pode ser usada nos resultados da pesquisa do Marketplace.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta (até 3.000 caracteres). Essa descrição será exibida aos clientes na visão geral de listagem do Marketplace. Inclua a proposta de valor da sua oferta, os principais benefícios, a categoria e/ou as associações do setor, as oportunidades de compra no aplicativo e as divulgações necessárias. 

Algumas dicas para escrever sua descrição:  

- Descreva claramente a proposta de valor da sua oferta nas primeiras frases da sua descrição. Inclua os seguintes itens em sua proposta de valor:
  - Descrição do produto
  - O tipo de usuário que se beneficia do produto
  - O cliente precisa ou dificuldade de que os endereços do produto
- Tenha em mente que as primeiras frases podem ser exibidas nos resultados do mecanismo de pesquisa.  
- Não confie em recursos e funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que você fornece.  
- Use vocabulário específico do setor ou palavras com base no benefício o máximo possível.
- Considere o uso de marcas HTML para formatar sua descrição e torná-la mais atraente.

Para tornar a descrição da sua oferta mais atraente, use o editor de Rich Text para formatar sua descrição.

![Usando o editor de Rich Text](./media/text-editor2.png)

Use as instruções a seguir para usar o editor de Rich Text:

- Para alterar o formato do seu conteúdo, realce o texto que você deseja formatar e selecione um estilo de texto, conforme mostrado abaixo:

     ![Usando o editor de Rich Text para alterar o formato de texto](./media/text-editor3.png)

- Para adicionar uma lista com marcadores ou numerada ao texto, use as opções abaixo:

     ![Usando o editor de Rich Text para adicionar listas](./media/text-editor4.png)

- Para adicionar ou remover o recuo para o texto, use as opções abaixo:

     ![Usando o editor de Rich Text para recuar](./media/text-editor5.png)

### <a name="search-keywords"></a>Pesquisar palavras-chave

Opcionalmente, você pode inserir até três palavras-chave de pesquisa para ajudar os clientes a localizar sua oferta no Marketplace. Para obter melhores resultados, tente usar essas palavras-chave em sua descrição também.

### <a name="products-your-app-works-with"></a>Produtos com os quais seu aplicativo trabalha

Se você quiser permitir que os clientes saibam que seu aplicativo funciona com produtos específicos, insira até três nomes de produto aqui.

### <a name="support-urls"></a>URLs de suporte

Esta seção permite que você forneça links para ajudar os clientes a entenderem mais sobre sua oferta.

#### <a name="help-link"></a>Link de ajuda

Insira a URL onde os clientes podem saber mais sobre sua oferta.

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Insira a URL para a política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e para fornecer uma política de privacidade válida.

### <a name="contacts"></a>Contactos

Nesta secção, deve fornecer o nome, e-mail e número de telefone para um contacto de **suporte** e um contacto de **Engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP.

Na secção de contacto de **suporte,** deve também fornecer o URL de **Suporte** onde os parceiros de CSP podem encontrar suporte para a sua oferta.

### <a name="supporting-documents"></a>Documentos comprovantes

Forneça pelo menos um (e até três) documentos de marketing relacionados aqui, como White papers, folhetos, listas de verificação ou apresentações. Esses documentos devem estar no formato. pdf.

### <a name="marketplace-images"></a>Imagens do Marketplace

Nesta seção, você pode fornecer logotipos e imagens que serão usados ao mostrar sua oferta ao cliente. Todas as imagens devem estar no formato. png.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça o logótipo da sua oferta em dois tamanhos: **Pequeno (48 x 48)** e **Large (216 x 216)** .

#### <a name="hero"></a>Herói

A imagem do Hero é opcional. Se você fornecer um, ele deve medir 815 x 290 pixels.

#### <a name="screenshots"></a>Capturas de Ecrã

Adicione capturas de tela que mostram como sua oferta funciona. Pelo menos três capturas de tela são necessárias e você pode adicionar até cinco. Todas as capturas de tela devem ser 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Opcionalmente, você pode adicionar até cinco vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou no Vimeo. Para cada um, insira o nome do vídeo, sua URL e uma imagem em miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

- [Boas práticas para anúncios de oferta de mercado](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Disponibilidade

A página **de Disponibilidade** dá-lhe opções sobre onde e como disponibilizar a sua oferta.

### <a name="markets"></a>Mercados

Esta seção permite que você especifique os mercados nos quais sua oferta deve estar disponível. Para isso, selecione **mercados Edit**, que mostrarão a janela popup de seleção de **mercado.**

Por padrão, não há mercados selecionados, mas você deve selecionar pelo menos um mercado para publicar sua oferta. Clique em **Selecionar tudo** para disponibilizar a sua oferta em todos os mercados possíveis ou selecione os mercados específicos que pretende adicionar. Uma vez terminado, selecione **Guardar**.

Suas seleções aqui se aplicam somente a novas aquisições; Se alguém já tiver seu aplicativo em um determinado mercado e você remover esse mercado posteriormente, as pessoas que já têm a oferta desse mercado poderão continuar a usá-lo, mas nenhum novo cliente nesse mercado poderá obter sua oferta.

> [!IMPORTANT]
> É sua responsabilidade atender a requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center.

Tenha em mente que, mesmo que você selecione todos os mercados, leis locais e restrições ou outros fatores podem impedir que determinadas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Público de visualização

Antes de publicar a sua oferta ao vivo para a oferta de mercado mais ampla, primeiro terá de a disponibilizar a um público de **pré-visualização**limitado. Introduza aqui uma **tecla Ocultar** (qualquer corda que utilize apenas letras minúsculas e/ou números) aqui. Os membros do seu público de visualização podem usar essa tecla Hide como um token para exibir uma visualização da sua oferta no Marketplace.

Em seguida, quando estiver pronto para disponibilizar a sua oferta e remover a restrição de pré-visualização, terá de remover a **tecla Ocultar** e publicar novamente.

## <a name="technical-configuration"></a>Configuração técnica

A página de **configuração técnica** define os detalhes técnicos utilizados para ligar à sua oferta. Essa conexão nos permite provisionar sua oferta para o cliente final se ele optar por adquiri-lo.

### <a name="package-type"></a>Tipo de pacote

Selecione a opção que se aplica à sua oferta:

- **Add On**: Uma aplicação Add-on alarga a experiência e a funcionalidade existente da Dynamics 365 Business Central. Para mais informações, consulte [aplicações Add-on](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- **Connect**: A Connect pode ser utilizada no cenário em que deve ser estabelecida uma ligação ponto a ponto entre a Dynamics 365 Business Central e uma solução ou serviço de terceiros. Para mais informações, consulte [Connect](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Upload de arquivo

Se selecionar **Add On** acima, onde irá carregar o ficheiro de pacote da sua oferta, juntamente com os ficheiros do pacote para qualquer extensão em que tenha dependências.

#### <a name="extensions-package-file"></a>Arquivo de pacote de extensões

Carregue o arquivo de pacote de extensão (. app) para sua oferta.

#### <a name="library-package-file"></a>Arquivo de pacote de biblioteca

Necessário se sua oferta deve ser instalada junto com outra extensão que não será publicada no Marketplace. Nesse caso, carregue seu arquivo. app aqui.

#### <a name="dependency-package-file"></a>Arquivo de pacote de dependência

Necessário se sua oferta deve ser instalada junto com outra extensão que já foi publicada no Marketplace. Em caso afirmativo, faça o upload do seu ficheiro `.app` ou `.zip` aqui.

### <a name="url-to-app-installation"></a>URL para instalação do aplicativo

Se selecionou **O Connect** acima, forneça o URL para a instalação da sua aplicação aqui.

## <a name="test-drive-technical-configuration"></a>Testar a configuração técnica

Se selecionar **Enable a test drive** na página de [configuração da Oferta,](#offer-setup) terá de fornecer detalhes aqui para que os clientes experimentem um test drive da sua oferta.

A página **test drive** permite-lhe configurar uma demonstração (ou "test drive") que permitirá aos clientes experimentar a sua oferta antes de se comprometer em comprá-la. Saiba mais no artigo [O que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) Se já não quiser fornecer um test drive para a sua oferta, volte à página de **[configuração da Oferta](#offer-setup)** e desmarque **o test drive Enable**.

Os seguintes tipos de unidades de teste estão disponíveis, cada um com seus próprios requisitos de configuração técnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicação lógica](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuração técnica não necessária)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para Azure Resource Manager test drive

Um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Os produtos que se ajustam a esse cenário usam apenas os recursos do Azure. Saiba mais sobre a criação de um [test drive Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões** (necessárias): Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Normalmente, você desejará disponibilizar seus test drive nas regiões em que você prevê o maior número de clientes, para que eles possam selecionar a região mais próxima para o melhor desempenho. Você precisará certificar-se de que sua assinatura tem permissão para implantar todos os recursos necessários em cada uma das regiões que você está selecionando.

- **Casos**: Selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões onde a sua oferta está disponível.

**Quente**: Este tipo de instância é implantado e aguarda acesso por região selecionada. Os clientes podem aceder instantaneamente a casos *quentes* de um test drive, em vez de terem de esperar por uma implementação. A desvantagem é que estas instâncias são sempre em execução na sua subscrição do Azure, para que eles incorre num tempo de atividade maior custos. É altamente recomendado ter pelo menos uma instância *Hot,* uma vez que a maioria dos clientes não quer esperar por implementações completas, resultando numa entrega no uso do cliente se não houver uma instância *hot* disponível.

**Frio**: Este tipo de instância representa o número total de casos que podem ser eventualmente implantados por região. As instâncias frias requerem que todo o modelo de Gestor de Recursos test drive seja implantado quando um cliente solicita a unidade de teste, por isso as instâncias *frias* são muito mais lentas a carregar do que as instâncias *hot.* A contrapartida é que só tem de pagar a duração do test drive, *nem* sempre está a funcionar na sua subscrição Azure como com uma instância *Hot.*

- Modelo de gestor de **recursos Azure de tração**de teste: Faça upload do .zip contendo o seu modelo de Gestor de Recursos Azure.  Saiba mais sobre a criação de um modelo de Gestor de Recursos Azure no artigo quickstart Criar e implementar modelos de [Gestor de Recursos Azure utilizando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração da unidade** de teste (necessária): Introduza o tempo de permanência da Test Drive, em # de horas. O Test-Drive automaticamente termina após o final deste período de tempo. Essa duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica do Dynamics 365 test drive

A Microsoft pode remover a complexidade de configurar um test drive hospedando e mantendo o provisionamento e a implantação de serviços usando esse tipo de test drive. A configuração para esse tipo de test drive hospedada é a mesma, independentemente de o test drive ter como alvo um centro de negócios, um compromisso com o cliente ou um público de operações.

- **Unidades de teste simultâneas max** (necessárias): Detete o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Cada usuário simultâneo consumirá uma licença do Dynamics 365 enquanto o test drive estiver ativo, portanto, será necessário garantir que você tenha licenças suficientes disponíveis para dar suporte ao limite máximo definido. Valor recomendado de 3-5.

- **Duração** da unidade de teste (necessária): Introduza o tempo de permanência da Unidade de Teste, definindo o número de horas. Depois disso, a sessão será encerrada e não consumirá mais uma de suas licenças. Recomendamos um valor de 2-24 horas, dependendo da complexidade da sua oferta. Essa duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).  O usuário pode solicitar uma nova sessão se ela ficar sem tempo e desejar acessar a test drive novamente.

- **URL de instância** (necessário): O URL onde o cliente iniciará o seu test drive. Tipicamente, o URL da sua instância Dynamics 365 executando a sua aplicação com dados de amostra instalados (por exemplo, https://testdrive.crm.dynamics.com).

- **Por exemplo Web API URL** (necessário): Recupere o URL Da Web API para a sua instância Dynamics 365, iniciando sessão na sua conta Microsoft 365 e navegando para **Definições** \&gt; **Personalização** \&gt; **Recursos de Desenvolvimento** \&gt; **Por exemplo, Web API (URL raiz**de serviço) , copie o URL aqui encontrado (por exemplo, https://testdrive.crm.dynamics.com/api/data/v9.0).

- Nome de **função** (obrigatório): Forneça o nome de função de segurança que definiu no seu test drive personalizado Dynamics 365, que será atribuído ao utilizador durante o seu test drive (por exemplo, função de test-drive).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para o aplicativo lógico test drive

Todos os produtos personalizados devem usar esse tipo de test drive modelo de implantação que abrange uma variedade de arquiteturas de solução complexas. Para mais informações sobre a configuração de unidades de teste de Aplicações Lógicas, visite [Operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Envolvimento do Cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (necessária, lista de abandono de seleção única): Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Os recursos para seu aplicativo lógico serão implantados na região que você selecionar. Se seu aplicativo lógico tiver qualquer recurso personalizado armazenado em uma região específica, verifique se a região está selecionada aqui. A melhor maneira é implantar totalmente seu aplicativo lógico localmente em sua assinatura do Azure no portal e verificar se ele funciona corretamente antes de fazer essa seleção.

- **Unidades de teste simultâneas max** (necessárias): Detete o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Essas unidades de teste já estão implantadas, permitindo que os clientes as acessem instantaneamente sem esperar por uma implantação.

- **Duração da unidade** de teste (necessária): Introduza o tempo de permanência da Test Drive, em # de horas. O test drive é encerrado automaticamente após o término desse período de tempo.

- Nome de grupo de **recursos Azure** (necessário): Introduza o nome do grupo de [recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) onde o seu test drive de aplicação lógica é guardado.

- Nome da **aplicação azure logic** (necessário): Introduza o nome da aplicação Logic que atribui o test drive ao utilizador. Este aplicativo lógico deve ser salvo no grupo de recursos do Azure acima.

- **Deprovisionamento lógica nome de aplicação** (necessário): Introduza o nome da aplicação Logic que desprovisiona o test drive uma vez que o cliente esteja terminado. Este aplicativo lógico deve ser salvo no grupo de recursos do Azure acima.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para unidades de teste de Power BI

Os produtos que desejam demonstrar um visual interativo Power BI podem usar um link incorporado para compartilhar um Dashboard personalizado como seu test drive, nenhuma configuração técnica adicional é necessária. Saiba mais sobre a configuração de aplicações de modelo[Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Detalhes da assinatura da implantação

Para implantar o Test Drive em seu nome, crie e forneça uma assinatura do Azure separada e exclusiva. (Não é necessário para Power BI unidades de teste).

- **ID de subscrição Azure** (necessário para o Gestor de Recursos Azure e aplicações lógicas): Introduza o ID de subscrição para conceder acesso aos serviços da sua conta Azure para relatórios de utilização de recursos e faturação. Recomendamos que considere [criar uma subscrição Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar para test drives se ainda não tiver uma. Pode encontrar o seu ID de subscrição Azure iniciando sessão no [portal Azure](https://portal.azure.com/) e navegando no separador **Subscrições** do menu do lado esquerdo. A seleção da guia exibirá sua ID de assinatura (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID de inquilino Azure AD** (obrigatório): Insira o seu Id de [inquilino](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure,](https://portal.azure.com/)selecione o separador Ative Directy no menu esquerdo, selecione **Properties, em seguida, procure o número de ID do **Diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Também pode procurar o ID do inquilino da sua organização usando o seu URL de nome de domínio em: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- Nome de **inquilino Azure AD** (necessário para Dinâmico 365): Insira o nome de Diretório Ativo Azure (AD). Para encontrar este nome, inscreva-se no [portal Azure,](https://portal.azure.com/)no canto superior direito o seu nome de inquilino será listado sob o nome da sua conta.

- **ID da aplicação Azure AD** (obrigatório): Introduza o id da [aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu esquerdo, selecione **registos**de Aplicações, em seguida, procure o número de ID de **aplicação** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- Segredo de cliente da **aplicação Azure AD** (necessário): Insira o [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)da aplicação Azure AD . Para encontrar este valor, inscreva-se no [portal Azure.](https://portal.azure.com/) Selecione o separador **Diretório Ativo Azure** no menu esquerdo, selecione **as inscrições**da App e, em seguida, selecione a sua aplicação de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo do cliente,** introduza uma descrição, selecione **Never** under **Expires,** em seguida, escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para fora da página antes de fazer isso, ou você não terá acesso ao valor.)

Lembre-se de **Guardar** antes de passar para a próxima secção!

### <a name="test-drive-marketplace-listings"></a>Testar listagens do Marketplace

A opção **de listagem marketplace** encontrada no separador test **drive** apresenta os idiomas em que a sua unidade de teste está disponível. Atualmente **o inglês (Estados Unidos)** é o único local disponível. Selecione o nome do idioma para inserir informações que descrevam a experiência de test drive.

- **Descrição** (necessária): Descreva o seu test drive, o que será demonstrado, objetivos para o utilizador experimentar, funcionalidades a explorar e qualquer informação relevante para ajudar o utilizador a determinar se adquire a sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informação de acesso** (necessária para o Gestor de Recursos Azure e unidades de teste lógicas): Explique o que um cliente precisa de saber para aceder e utilizar este test drive. Percorra um cenário para usar sua oferta e exatamente o que o cliente deve saber para acessar recursos em todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do Utilizador** (obrigatório): Uma passagem aprofundada da sua experiência de test drive. O manual do usuário deve abranger exatamente o que você deseja que o cliente tenha de apresentar o test drive e servir como uma referência para quaisquer perguntas que possam ter. O arquivo deve estar no formato PDF e ter o nome (máximo de 255 caracteres) após o carregamento.

- **Vídeos: Adicionar vídeos** (opcional): Os vídeos podem ser enviados para o YouTube ou Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa ver um walk-through de informação para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso as funcionalidades da sua oferta e entender cenários que realçam os seus benefícios.
  - **Nome** (obrigatório)
  - **URL (apenas YouTube ou Vimeo) (obrigatório)**
  - **Miniatura (533 x 324 px)** : O ficheiro de imagem deve estar no formato PNG.

## <a name="supplemental-content"></a>Conteúdo complementar

Esta página permite que você forneça informações adicionais sobre sua oferta para nos ajudar a validar sua oferta. Essas informações não são mostradas aos clientes ou publicadas no Marketplace.

### <a name="target-release"></a>Versão de destino

Indique qual o lançamento do Microsoft Dynamics Business Central os seus alvos de solução: **Current**, **Next major**, ou Next **minor**. Essas informações nos permitem testar sua solução adequadamente.

### <a name="supported-editions"></a>Edições suportadas

Se a sua oferta necessitar da edição Premium do Microsoft Dynamics 365 Business Central, selecione **apenas Premium.** Caso contrário, selecione tanto **o Essencial como** o **Premium.**

### <a name="key-usage-scenario"></a>Cenário de uso de chave

Deve fazer o upload de um ficheiro `.pdf` que lista os principais cenários de utilização da sua oferta listados num documento (.formato pdf). Todos os cenários listados aqui podem ser verificados por nossa equipe de validação antes de aprovarmos sua oferta para o Marketplace.

### <a name="app-tests-automation"></a>Automação de testes de aplicativo

Pode fazer o upload opcional de um ficheiro de **automatização** de testes da App aqui (.app).

### <a name="test-accounts"></a>Contas de teste

Se for necessária uma conta de teste para que a nossa equipa de certificação reveja corretamente a sua oferta, faça upload de um ficheiro .pdf, .doc ou .docx com as informações **das suas contas de Teste.**

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para visualização

Depois de ter concluído todas as secções necessárias da oferta, **selecione publicar** no canto superior direito do portal. Será redirecionado para a página **De Revisão e publicação.** 

Se for a primeira vez que publicar essa oferta, você poderá:

- Consulte o status de conclusão de cada seção da oferta.
    - *Não começou* - significa que a secção não foi tocada e precisa de ser concluída.
    - *Incompleto* - significa que a secção tem erros que precisam de ser corrigidos ou exige mais informações a fornecer. Volte para a (s) seção (ões) e atualize-a.
    - *Completo* - significa que a secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta devem estar em um estado completo antes que você possa enviar a oferta.
- Na secção **Notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta para publicação selecionando **Enviar**. Nós lhe enviaremos um email quando uma versão prévia da oferta estiver disponível para revisão e aprovação. Volte ao Partner Center e selecione **Go-live** para a oferta de publicar a sua oferta ao público (ou se uma oferta privada, para o público privado).

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

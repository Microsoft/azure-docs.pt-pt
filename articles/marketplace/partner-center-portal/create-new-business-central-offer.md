---
title: Criar uma nova oferta Da Dynamics 365 Business Central no mercado comercial da Microsoft
description: Como criar uma nova oferta Dynamics 365 Business Central para listagem ou venda no Azure Marketplace, Microsoft AppSource ou através do programa Cloud Solution Provider (CSP) utilizando o portal de marketplace comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 1c99af1927f39e2dc6fecd7a6cae43e3d4f4503f
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208453"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Criar uma nova oferta Da Dynamics 365 Business Central

Este artigo explica como criar uma nova oferta Da Dynamics 365 Business Central. O [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) é um sistema de planeamento de recursos empresariais (ERP) que lida com uma vasta gama de processos de negócio, incluindo finanças, operações, cadeia de fornecimento, CRM, e gestão de projetos e comércio eletrónico. Os pacotes premium também suportam o modelo e o fabrico clássicos. Todas as ofertas para a Dynamics 365 Business Central devem passar pelo nosso processo de certificação.

Para começar a criar ofertas Da Dynamics 365 Business Central, certifique-se de que primeiro [cria uma conta partner center](./create-account.md) e abre o dashboard do Mercado [Comercial,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)com a página **de visão geral** selecionada.

![Dashboard do Mercado Comercial no Partner Center](./media/new-offer-overview.png)

>[!Note]
> Uma vez publicada uma oferta, as edimas para a oferta feita no Partner Center só serão atualizadas no sistema e as fachadas da loja após a republicação. Certifique-se de que submete a oferta para publicação depois de fazer alterações.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

Selecione o botão **+ Nova oferta** e, em seguida, selecione o item do menu Dynamics **365 Business Central.** A nova caixa de diálogo da **oferta** aparecerá.

### <a name="offer-id-and-alias"></a>Oferecer ID e pseudónimo

- **ID oferta**: Identificador único para cada oferta na sua conta. Este ID será visível para os clientes no endereço URL para a oferta de mercado e modelos do Gestor de Recursos Azure (se aplicável). O ID da oferta deve ser caracteres alfanuméricos minúsculos (incluindo hífenes e sublinhados, mas sem espaço branco), limitado a 50 caracteres, e não pode ser alterado depois de selecionar **Criar**.  Por exemplo, se introduzir aqui a *oferta de teste-1,* o URL de oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Oferta pseudónimo**: O nome usado para se referir à oferta dentro do Partner Center. Este nome não será usado no mercado, e é diferente do nome da oferta e outros valores que serão mostrados aos clientes. Este valor não pode ser alterado depois de selecionar **Criar**.

Assim que introduzir o seu **Id de Oferta** e oferecer **pseudónimo,** selecione **Criar**. Em seguida, poderá trabalhar em todas as diferentes partes da sua oferta.

## <a name="offer-setup"></a>Configuração de oferta

A página de **configuração da Oferta** pede as seguintes informações. Certifique-se de selecionar **Guardar** depois de completar estes campos.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Como pretende que potenciais clientes interajam com esta oferta de listagem?

Selecione a opção que deseja utilizar para esta oferta.

#### <a name="get-it-now-free"></a>Obtê-lo agora (grátis)

Liste gratuitamente a sua oferta aos clientes, fornecendo um URL válido (a partir de *http* ou *https)* onde possam aceder à sua aplicação.  Por exemplo: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Julgamento gratuito (listagem)

Enumere a sua oferta aos clientes com um link para um teste gratuito, fornecendo um URL válido (começando com *http* ou *https)* onde possam obter um teste.  Por exemplo: `https://contoso.com/trial/my-app`. Os testes gratuitos de listagem de ofertas são criados, geridos e configurados pelo seu serviço e não têm subscrições geridas pela Microsoft.

> [!NOTE]
> Os tokens que a sua aplicação receberá através do seu link experimental só podem ser utilizados para obter informações de utilizador através do Azure Ative Directory (Azure AD) para automatizar a criação de conta na sua app. As contas da Microsoft não são suportadas para autenticação utilizando este símbolo.

#### <a name="contact-me"></a>Contacte-me

Recolha informações de contacto com o cliente ligando o seu sistema de Gestão de Relacionamento com o Cliente (CRM). Será solicitado ao cliente permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e fonte de mercado onde encontraram a sua oferta, serão enviados para o sistema CRM que configura. Para mais informações sobre a configuração do seu CRM, consulte [Connect lead management](#connect-lead-management). 

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "tentar antes de comprar", resultando numa conversão acrescida e na geração de leads altamente qualificados. [Saiba mais sobre test drives.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para ativar uma unidade de teste, verifique a **caixa de acionamento ativa.** Em seguida, terá de configurar um ambiente de demonstração na [configuração técnica](#test-drive-technical-configuration) test drive para permitir que os clientes experimentem a sua oferta por um período de tempo fixo. 

#### <a name="type-of-test-drive"></a>Tipo de test drive

Selecione entre as seguintes opções:

- **[Gestor de Recursos Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**: Um modelo de implantação que contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure.
- **[Dinâmica 365 para o Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: A Microsoft acolhe e mantém o serviço de test drive (incluindo o provisionamento e a implementação) para um sistema de planeamento de recursos empresariais da Business Central (finanças, operações, cadeia de fornecimento, CRM, etc.).  
- **[Dinâmica 365 para envolvimento com](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** o cliente : A Microsoft acolhe e mantém o serviço de test drive (incluindo o fornecimento e a implementação) para um sistema de envolvimento com o cliente (vendas, serviço, serviço de projeto, serviço de campo, etc.).  
- **[Dinâmica 365 para Operações](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: A Microsoft acolhe e mantém o serviço de test drive (incluindo o fornecimento e a implantação) para um sistema de planeamento de recursos empresariais de Finanças e Operações (finanças, operações, fabrico, cadeia de fornecimento, etc.). 
- **[Aplicativo lógico](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**: Um modelo de implementação que abrange todas as arquiteturas de soluções complexas. Qualquer produto personalizado deve utilizar este tipo de Test Drive.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**: Uma ligação incorporada a um dashboard personalizado. Os produtos que pretendam demonstrar um visual interativo do Power BI devem utilizar este tipo de Test Drive. Tudo o que precisa fazer upload aqui é o seu URL power BI incorporado.

#### <a name="additional-test-drive-resources"></a>Recursos adicionais de test drive

- [Test Drive Técnicas Boas Práticas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Test Drive Marketing Boas Práticas](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral do test drive uma página](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Ligar a gestão de chumbo

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para mais informações, consulte a visão geral da [gestão de chumbo](./commercial-marketplace-get-customer-leads.md).

Lembre-se de **Guardar** antes de passar para a próxima secção!

## <a name="properties"></a>Propriedades

A página **Propriedades** permite definir as categorias e indústrias usadas para agrupar a sua oferta no mercado, na sua versão de app e nos contratos legais de apoio à sua oferta. Selecione **Guardar** depois de completar esta página.

### <a name="category"></a>Categoria

Selecione um mínimo de uma e um máximo de três categorias, que serão usadas para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Certifique-se de que a sua oferta suporta estas categorias na descrição da oferta. 

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versão da aplicação

Insira o número da versão da sua oferta. Os clientes verão esta versão listada na página de detalhes da oferta.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça os seus próprios termos e condições legais no campo **Termos e Condições.** Também pode fornecer o URL onde os seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

## <a name="offer-listing"></a>Oferta listagem

A página de listagem da Oferta é onde define detalhes para a sua oferta (como nome, descrição, imagens, etc.).

> [!NOTE]
> Você pode fornecer detalhes de listagem de oferta em um só idioma. Não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação só está disponível em [língua não inglesa]." Também é aceitável fornecer um URL de *link de ajuda* para oferecer conteúdo num idioma diferente do utilizado no conteúdo de listagem de oferta.

### <a name="name"></a>Nome

O nome que introduzir aqui será mostrado aos clientes como título da sua oferta. Este campo é pré-povoado com o texto que inscreveu para **o pseudónimo oferta** quando criou a oferta, mas pode alterar esse valor. Este nome pode ser marcado por marca (e pode incluir símbolos de marca ou direitos de autor). O nome não pode ser mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Descrição curta

Forneça uma breve descrição da sua oferta (até 100 caracteres), que pode ser usada em resultados de pesquisa de mercado.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta (até 3.000 caracteres). Esta descrição será exibida aos clientes na visão geral da listagem do mercado. Inclua a proposta de valor da sua oferta, benefícios-chave, associações de categorias e/ou industriais, oportunidades de compra na aplicação e quaisquer divulgações necessárias. 

Algumas dicas para escrever a sua descrição:  

- Descreva claramente a proposta de valor da sua oferta nas primeiras frases da sua descrição. Inclua os seguintes itens na sua proposta de valor:
  - Descrição do produto
  - O tipo de utilizador que beneficia do produto
  - Necessidades do cliente ou dor que o produto aborda
- Tenha em mente que as primeiras frases podem ser exibidas nos resultados do motor de busca.  
- Não confie em funcionalidades e funcionalidades para vender o seu produto. Em vez disso, concentre-se no valor que entregar.  
- Utilize o vocabulário específico da indústria ou a formulação baseada em benefícios o máximo possível.
- Considere usar tags HTML para formatar a sua descrição e torná-la mais envolvente.

Para tornar a sua descrição mais envolvente, use o rico editor de texto para formatar a sua descrição.

![Usando o rico editor de texto](./media/text-editor2.png)

Utilize as seguintes instruções para utilizar o rico editor de texto:

- Para alterar o formato do seu conteúdo, realce o texto que pretende formatar e selecionar um estilo de texto, como mostrado abaixo:

     ![Usando o rico editor de texto para alterar o formato de texto](./media/text-editor3.png)

- Para adicionar uma lista com bala ou numerada ao texto, utilize as opções abaixo:

     ![Usando o rico editor de texto para adicionar listas](./media/text-editor4.png)

- Para adicionar ou remover o recuo ao texto, utilize as opções abaixo:

     ![Usando o rico editor de texto para o travessão](./media/text-editor5.png)

### <a name="search-keywords"></a>Pesquisar palavras-chave

Pode inserir opcionalmente até três palavras-chave de pesquisa para ajudar os clientes a encontrar a sua oferta no mercado. Para obter os melhores resultados, tente utilizar estas palavras-chave na sua descrição também.

### <a name="products-your-app-works-with"></a>Produtos com os que a sua app trabalha

Se quiser informar os clientes de que a sua aplicação funciona com produtos específicos, insira até três nomes de produtos aqui.

### <a name="helpprivacy-urls"></a>URLs de Ajuda/Privacidade

Esta secção permite-lhe fornecer links para ajudar os clientes a entender mais sobre a sua oferta.

#### <a name="help-link"></a>Link de ajuda

Introduza o URL onde os clientes podem saber mais sobre a sua oferta. A **sua ligação de ajuda** não pode ser a mesma que o URL de **Suporte** (explicado abaixo).

#### <a name="privacy-policy-link"></a>Ligação política de privacidade

Insira o URL na política de privacidade da sua organização. É responsável por garantir que a sua aplicação cumpre as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="contact-information"></a>Informações de Contacto

Nesta secção, deve fornecer o nome, e-mail e número de telefone para um contacto de **suporte** e um contacto de **Engenharia**. Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft, podendo ser fornecida aos parceiros CSP.

Na secção de contacto de **suporte,** deve também fornecer o URL de **Suporte** onde os parceiros de CSP podem encontrar suporte para a sua oferta. O URL de Suporte não pode ser o mesmo que o seu **link ajuda**.

### <a name="supporting-documents"></a>Documentos comprovantes

Forneça aqui pelo menos um (e até três) documentos de marketing relacionados, tais como papéis brancos, brochuras, listas de verificação ou apresentações. Estes documentos devem estar em formato .pdf.

### <a name="marketplace-images"></a>Imagens do mercado

Nesta secção, pode fornecer logótipos e imagens que serão utilizados ao mostrar a sua oferta ao cliente. Todas as imagens devem estar em formato .png. Faça upload do seu logotipo de oferta em dois tamanhos: **Pequeno (48 x 48)** e **Grande (216 x 216)**.

>[!Note]
>Se tiver algum problema de envio de ficheiros, https://upload.xboxlive.com certifique-se de que a sua rede local não bloqueia o serviço utilizado pelo Partner Center.

#### <a name="screenshots"></a>Capturas de Ecrã

Adicione imagens que mostrem como funciona a sua oferta. São necessárias pelo menos três imagens, e pode somar até cinco. Todas as imagens devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Pode adicionar opcionalmente cinco vídeos que demonstram a sua oferta. Estes vídeos devem ser hospedados no YouTube e/ou vimeo. Para cada um, introduza o nome do vídeo, o seu URL e uma imagem de miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de mercado

- [Boas práticas para anúncios de oferta de mercado](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Disponibilidade

A página **de Disponibilidade** dá-lhe opções sobre onde e como disponibilizar a sua oferta.

### <a name="markets"></a>Mercados

Esta secção permite especificar os mercados em que a sua oferta deve estar disponível. Para isso, selecione **mercados Edit**, que mostrarão a janela popup de seleção de **mercado.**

Por padrão, não são selecionados mercados, mas deve selecionar pelo menos um mercado para publicar a sua oferta. Clique em **Selecionar tudo** para disponibilizar a sua oferta em todos os mercados possíveis ou selecione os mercados específicos que pretende adicionar. Uma vez terminado, selecione **Guardar**.

As suas seleções aplicam-se apenas a novas aquisições; se alguém já tiver a sua app num determinado mercado, e mais tarde remover esse mercado, as pessoas que já têm a oferta nesse mercado podem continuar a usá-la, mas nenhum novo cliente nesse mercado poderá obter a sua oferta.

> [!IMPORTANT]
> É da sua responsabilidade cumprir quaisquer requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center.

Tenha em mente que, mesmo que selecione todos os mercados, leis locais e restrições ou outros fatores, pode impedir que determinadas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Pré-visualização do público

Antes de publicar a sua oferta ao vivo para a oferta de mercado mais ampla, primeiro terá de a disponibilizar a um público de **pré-visualização**limitado. Introduza aqui uma **tecla Ocultar** (qualquer corda que utilize apenas letras minúsculas e/ou números) aqui. Os membros do seu público de pré-visualização podem usar esta chave de ocultação como um símbolo para ver uma pré-visualização da sua oferta no mercado.

Em seguida, quando estiver pronto para disponibilizar a sua oferta e remover a restrição de pré-visualização, terá de remover a **tecla Ocultar** e publicar novamente.

## <a name="technical-configuration"></a>Configuração técnica

A página de **configuração técnica** define os detalhes técnicos utilizados para ligar à sua oferta. Esta ligação permite-nos fornecer a sua oferta para o cliente final se optarem por adquiri-la.

### <a name="package-type"></a>Tipo de pacote

Selecione a opção que se aplica à sua oferta:

- **Add On**: Uma aplicação Add-on alarga a experiência e a funcionalidade existente da Dynamics 365 Business Central. Para mais informações, consulte [aplicações Add-on](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- **Connect**: A Connect pode ser utilizada no cenário em que deve ser estabelecida uma ligação ponto a ponto entre a Dynamics 365 Business Central e uma solução ou serviço de terceiros. Para mais informações, consulte [Connect](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Upload de ficheiros

Se selecionar **Add On** acima, onde irá carregar o ficheiro de pacote da sua oferta, juntamente com os ficheiros do pacote para qualquer extensão em que tenha dependências.

#### <a name="extensions-package-file"></a>Arquivo de pacote de extensões

Faça upload do ficheiro de ficheiro de pacote de extensão (.app) para a sua oferta.

#### <a name="library-package-file"></a>Arquivo de pacote de biblioteca

Necessária se a sua oferta tiver de ser instalada juntamente com outra extensão que não será publicada no mercado. Em caso afirmativo, faça upload do seu ficheiro .app aqui.

#### <a name="dependency-package-file"></a>Arquivo de pacote de dependência

Necessária se a sua oferta tiver de ser instalada juntamente com outra extensão que já tenha sido publicada no mercado. Em caso afirmativo, `.app` `.zip` faça o upload ou o arquivo aqui.

### <a name="url-to-app-installation"></a>URL para instalação de aplicativos

Se selecionou **O Connect** acima, forneça o URL para a instalação da sua aplicação aqui. Para serviços conectados que não necessitem de instalação, forneça o URL para a sua página de aterragem de serviço ou página de inscrição.

## <a name="test-drive-technical-configuration"></a>Configuração técnica de test drive

Se selecionar **Enable a test drive** na página de [configuração da Oferta,](#offer-setup) terá de fornecer detalhes aqui para que os clientes experimentem um test drive da sua oferta.

A página **test drive** permite-lhe configurar uma demonstração (ou "test drive") que permitirá aos clientes experimentar a sua oferta antes de se comprometer em comprá-la. Saiba mais no artigo [O que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) Se já não quiser fornecer um test drive para a sua oferta, volte à página de **[configuração da Oferta](#offer-setup)** e desmarque **o test drive Enable**.

Estão disponíveis os seguintes tipos de test drives, cada um com os seus próprios requisitos de configuração técnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicação lógica](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuração técnica não necessária)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para unidade de teste Do Gestor de Recursos Azure

Um modelo de implantação que contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure. Saiba mais sobre a criação de um [test drive Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões** (necessárias): Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Normalmente, você vai querer disponibilizar o seu test drive nas regiões onde você antecipa o maior número de clientes, para que eles possam selecionar a região mais próxima para o melhor desempenho. Terá de se certificar de que a sua subscrição é autorizada a disponibilizar todos os recursos necessários em cada uma das regiões que está a selecionar.

- **Casos**: Selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões onde a sua oferta está disponível.

**Quente**: Este tipo de instância é implantado e aguarda acesso por região selecionada. Os clientes podem aceder instantaneamente a casos *quentes* de um test drive, em vez de terem de esperar por uma implementação. A contrapartida é que estes casos estão sempre a decorrer na sua subscrição Azure, pelo que incorrerão num custo de uptime maior. É altamente recomendado ter pelo menos uma instância *Hot,* uma vez que a maioria dos clientes não quer esperar por implementações completas, resultando numa entrega no uso do cliente se não houver uma instância *hot* disponível.

**Frio**: Este tipo de instância representa o número total de casos que podem ser eventualmente implantados por região. As instâncias frias requerem que todo o modelo de Gestor de Recursos test drive seja implantado quando um cliente solicita a unidade de teste, por isso as instâncias *frias* são muito mais lentas a carregar do que as instâncias *hot.* A contrapartida é que só tem de pagar a duração do test drive, *nem* sempre está a funcionar na sua subscrição Azure como com uma instância *Hot.*

- Modelo de gestor de **recursos Azure de tração**de teste: Faça upload do .zip contendo o seu modelo de Gestor de Recursos Azure.  Saiba mais sobre a criação de um modelo de Gestor de Recursos Azure no artigo quickstart Criar e implementar modelos de [Gestor de Recursos Azure utilizando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração da unidade** de teste (necessária): Introduza o tempo de permanência da Test Drive, em # de horas. O Test Drive termina automaticamente após o fim deste período de tempo. Esta duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válida).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica para unidade de teste Dynamics 365

A Microsoft pode remover a complexidade da instalação de um test drive hospedando e mantendo o fornecimento e implementação do serviço utilizando este tipo de test drive. A configuração para este tipo de test drive hospedado é a mesma, independentemente de o test drive estar direcionado para um público de Business Central, Customer Engagement ou Operations.

- **Unidades de teste simultâneas max** (necessárias): Detete o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Cada utilizador simultâneo consumirá uma licença Dynamics 365 enquanto o test drive estiver ativo, pelo que terá de garantir que tem licenças suficientes para suportar o limite máximo definido. Valor recomendado de 3-5.

- **Duração** da unidade de teste (necessária): Introduza o tempo de permanência da Unidade de Teste, definindo o número de horas. Após tantas horas, a sessão terminará e deixará de consumir uma das suas licenças. Recomendamos um valor de 2 a 24 horas dependendo da complexidade da sua oferta. Esta duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válida).  O utilizador pode solicitar uma nova sessão se ficar sem tempo e pretender aceder novamente ao test drive.

- **URL de instância** (necessário): O URL onde o cliente iniciará o seu test drive. Tipicamente, o URL da sua instância Dynamics 365 executando `https://testdrive.crm.dynamics.com`a sua aplicação com dados de amostra instalados (por exemplo, ).

- **Por exemplo Web API URL** (necessário): Recupere o URL Da Web API para a sua instância Dynamics 365, iniciando sessão na sua conta Microsoft 365 e navegando para **Definições** \&gt; **Personalização** \&gt; **Recursos** \&de Desenvolvimento gt; **Por exemplo, Web API (URL raiz**de serviço) `https://testdrive.crm.dynamics.com/api/data/v9.0`, copiar o URL encontrado aqui (por exemplo, ).

- Nome de **função** (obrigatório): Forneça o nome de função de segurança que definiu no seu test drive personalizado Dynamics 365, que será atribuído ao utilizador durante o seu test drive (por exemplo, função de test-drive).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para unidade de teste de aplicativo lógica

Qualquer produto personalizado deve usar este tipo de modelo de implementação de test drive que engloba uma variedade de arquiteturas de solução complexas. Para mais informações sobre a configuração de unidades de teste de Aplicações Lógicas, visite [Operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Envolvimento do Cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (necessária, lista de abandono de seleção única): Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Os recursos para a sua aplicação Logic serão implantados na região que selecionar. Se a sua App Lógica tiver algum recursos personalizados armazenados numa região específica, certifique-se de que a região é selecionada aqui. A melhor maneira é implementar totalmente a sua App Lógica localmente na sua subscrição Azure no portal e verificar se funciona corretamente antes de fazer esta seleção.

- **Unidades de teste simultâneas max** (necessárias): Detete o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Estes test drives já estão implantados, permitindo aos clientes acederem instantaneamente sem esperar por uma implementação.

- **Duração da unidade** de teste (necessária): Introduza o tempo de permanência da Test Drive, em # de horas. O test drive termina automaticamente após o fim deste período de tempo.

- Nome de grupo de **recursos Azure** (necessário): Introduza o nome do grupo de [recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) onde o seu test drive de aplicação lógica é guardado.

- Nome da **aplicação azure logic** (necessário): Introduza o nome da aplicação Logic que atribui o test drive ao utilizador. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

- **Deprovisionamento lógica nome de aplicação** (necessário): Introduza o nome da aplicação Logic que desprovisiona o test drive uma vez que o cliente esteja terminado. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para unidades de teste Power BI

Os produtos que queiram demonstrar um visual interativo do Power BI podem usar um link incorporado para partilhar um dashboard personalizado como seu test drive, não sendo necessária nenhuma configuração técnica adicional. Saiba mais sobre a configuração de aplicações de modelo[Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Detalhes da subscrição de implementação

Para implementar o Test Drive em seu nome, crie e forneça uma subscrição Azure separada e única. (Não é necessário para as unidades de teste Power BI).

- **ID de subscrição Azure** (necessário para o Gestor de Recursos Azure e aplicações lógicas): Introduza o ID de subscrição para conceder acesso aos serviços da sua conta Azure para relatórios de utilização de recursos e faturação. Recomendamos que considere [criar uma subscrição Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar para test drives se ainda não tiver uma. Pode encontrar o seu ID de subscrição Azure iniciando sessão no [portal Azure](https://portal.azure.com/) e navegando no separador **Subscrições** do menu do lado esquerdo. A seleção do separador irá exibir o seu ID de subscrição (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID de inquilino Azure AD** (obrigatório): Insira o seu Id de [inquilino](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure,](https://portal.azure.com/)selecione o separador Ative Directy no menu esquerdo, selecione **Properties, em seguida, procure o número de ID do **Diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Também pode procurar o ID do inquilino da sua [https://www.whatismytenantid.com](https://www.whatismytenantid.com)organização usando o seu URL de nome de domínio em: .

- Nome de **inquilino Azure AD** (necessário para Dinâmico 365): Insira o nome de Diretório Ativo Azure (AD). Para encontrar este nome, inscreva-se no [portal Azure,](https://portal.azure.com/)no canto superior direito o seu nome de inquilino será listado sob o nome da sua conta.

- **ID da aplicação Azure AD** (obrigatório): Introduza o id da [aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu esquerdo, selecione **registos**de Aplicações, em seguida, procure o número de ID de **aplicação** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- Segredo de cliente da **aplicação Azure AD** (necessário): Insira o [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)da aplicação Azure AD . Para encontrar este valor, inscreva-se no [portal Azure.](https://portal.azure.com/) Selecione o separador **Diretório Ativo Azure** no menu esquerdo, selecione **as inscrições**da App e, em seguida, selecione a sua aplicação de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo do cliente,** introduza uma descrição, selecione **Never** under **Expires,** em seguida, escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para longe da página antes de fazer isto, caso contrário não terá acesso ao valor.)

Lembre-se de **Guardar** antes de passar para a próxima secção!

### <a name="test-drive-marketplace-listings"></a>Listas de mercado de test drive

A opção **de listagem do Marketplace** encontrada no separador test **drive** é onde define detalhes para a experiência de test drive.

> [!NOTE]
> Pode fornecer detalhes de listagem de test drive apenas numa língua. Não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação só está disponível em [língua não inglesa]." Também é aceitável fornecer um URL de *ligação de ajuda* para oferecer conteúdo numa língua diferente da utilizada no conteúdo de listagem de unidade de teste.


- **Descrição** (necessária): Descreva o seu test drive, o que será demonstrado, objetivos para o utilizador experimentar, funcionalidades a explorar e qualquer informação relevante para ajudar o utilizador a determinar se adquire a sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informação de acesso** (necessária para o Gestor de Recursos Azure e unidades de teste lógicas): Explique o que um cliente precisa de saber para aceder e utilizar este test drive. Caminhe por um cenário de utilização da sua oferta e exatamente o que o cliente deve saber para aceder a funcionalidades durante todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do Utilizador** (obrigatório): Uma passagem aprofundada da sua experiência de test drive. O Manual do Utilizador deve cobrir exatamente o que pretende que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer questões que possam ter. O ficheiro deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.

- **Vídeos: Adicionar vídeos** (opcional): Os vídeos podem ser enviados para o YouTube ou Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa ver um walk-through de informação para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso as funcionalidades da sua oferta e entender cenários que realçam os seus benefícios.
  - **Nome** (obrigatório)
  - **URL (apenas YouTube ou Vimeo) (obrigatório)**
  - **Miniatura (533 x 324 px)**: O ficheiro de imagem deve estar no formato PNG.

## <a name="supplemental-content"></a>Conteúdo suplementar

Esta página permite-lhe fornecer informações adicionais sobre a sua oferta para nos ajudar a validar a sua oferta. Esta informação não é mostrada aos clientes nem publicada no mercado.

### <a name="target-release"></a>Libertação do alvo

Indique qual o lançamento do Microsoft Dynamics Business Central os seus alvos de solução: **Current**, **Next major**, ou Next **minor**. Esta informação permite-nos testar a sua solução de forma adequada.

### <a name="supported-editions"></a>Edições apoiadas

Se a sua oferta necessitar da edição Premium do Microsoft Dynamics 365 Business Central, selecione **apenas Premium.** Caso contrário, selecione tanto **o Essencial como** o **Premium.**

### <a name="key-usage-scenario"></a>Cenário de utilização chave

Deve fazer `.pdf` o upload de um ficheiro que lista os principais cenários de utilização da sua oferta listados num documento (formato pdf). Todos os cenários listados aqui podem ser verificados pela nossa equipa de validação antes de aprovarmos a sua oferta para o mercado.

### <a name="app-tests-automation"></a>App testa automatização

Se a sua oferta for uma aplicação Add-on, tem de carregar um ficheiro de **automatização** de testes da App (.app). Este ficheiro não é aplicável às aplicações Connect.

### <a name="test-accounts"></a>Contas de teste

Se for necessária uma conta de teste para que a nossa equipa de certificação reveja corretamente a sua oferta, faça upload de um ficheiro .pdf, .doc ou .docx com as informações **das suas contas de Teste.**

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Depois de ter concluído todas as secções necessárias da oferta, **selecione publicar** no canto superior direito do portal. Será redirecionado para a página **De Revisão e publicação.** 

Se for a primeira vez que publica esta oferta, pode:

- Consulte o estado de conclusão de cada secção da oferta.
    - *Não começou* - significa que a secção não foi tocada e precisa de ser concluída.
    - *Incompleto* - significa que a secção tem erros que precisam de ser corrigidos ou exige mais informações a fornecer. Volte para a secção(s) e atualize-a.
    - *Completo* - significa que a secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado total antes de poder submeter a oferta.
- Na secção **Notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta para publicação selecionando **Enviar**. Enviaremos um e-mail quando estiver disponível uma versão de pré-visualização da oferta para que possa rever e aprovar. Volte ao Partner Center e selecione **Go-live** para a oferta de publicar a sua oferta ao público (ou se uma oferta privada, para o público privado).

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

---
title: Criar um novo Dynamics 365 for Customer Engagement & a oferta do PowerApps no Marketplace comercial
description: Como criar um novo Dynamics 365 para o envolvimento do cliente & oferta do PowerApps para listagem ou venda no Azure Marketplace, AppSource ou por meio do programa CSP (provedor de soluções na nuvem) usando o portal do Marketplace comercial no Microsoft Partner Center.
author: JnHs
manager: evansma
ms.author: jenhayes
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 7cda51870a98b2606a0133a61ad6f786e7e9324a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900143"
---
# <a name="create-a-new-dynamics-365-for-customer-engagement--powerapps-offer"></a>Criar um novo Dynamics 365 for Customer Engagement & a oferta do PowerApps

Este tópico explica como criar um novo Dynamics 365 para o envolvimento do cliente & oferta do PowerApps. Todos os aplicativos para o Dynamics 365 for Customer Engagement (PowerApps, Sales, Service, Project Service e Field Service) devem passar pelo nosso processo de certificação e dar suporte a uma experiência de avaliação. O processo de certificação verifica sua solução para obter os requisitos padrão, a compatibilidade e as práticas apropriadas. A experiência de avaliação permite que os usuários implantem sua solução em um ambiente dinâmico do Dynamics 365.

Para começar a criar o Dynamics 365 para o envolvimento do cliente & ofertas do PowerApps, certifique-se de primeiro [criar uma conta do Partner Center](./create-account.md) e abrir o [painel do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), com a guia **visão geral** selecionada.

![Painel do Marketplace comercial no Partner Center](./media/new-offer-overview.png)

## <a name="create-a-new-offer"></a>Criar uma nova oferta

Selecione o botão **+ nova oferta** e, em seguida, selecione o item de menu **Dynamics 365 for Customer Engagement & PowerApps** . A caixa de diálogo **nova oferta** será exibida.

### <a name="offer-id-and-alias"></a>ID da oferta e alias

- **ID da oferta**: Identificador exclusivo para cada oferta em sua conta. Essa ID será visível para os clientes no endereço URL para a oferta do Marketplace e os modelos de Azure Resource Manager (se aplicável). A ID da oferta deve conter caracteres alfanuméricos minúsculos (incluindo hifens e sublinhados, mas sem espaço em branco). Isso é limitado a 50 caracteres e não pode ser alterado depois que você seleciona **criar**.  Por exemplo, se você inserir *Test-offer-1* aqui, a URL da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Alias da oferta**: O nome usado para fazer referência à oferta no Partner Center. Esse nome não será usado no Marketplace e será diferente do nome da oferta e outros valores que serão mostrados aos clientes. Esse valor não pode ser alterado depois que você seleciona **criar**.

Depois de inserir sua **ID de oferta** e **alias de oferta**, selecione **criar**. Em seguida, você poderá trabalhar em todas as diferentes partes da sua oferta.

## <a name="offer-setup"></a>Instalação da oferta

A página **instalação da oferta** solicita as seguintes informações. Certifique-se de selecionar **salvar** depois de concluir esses campos.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Como você deseja que clientes potenciais interajam com esta oferta de listagem?

Selecione a opção que você deseja usar para esta oferta.

#### <a name="get-it-now-free"></a>Obtenha agora (gratuito)

Liste sua oferta aos clientes gratuitamente fornecendo uma URL válida (começando com *http* ou *https*) em que eles podem acessar seu aplicativo.  Por exemplo: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Avaliação gratuita (listagem)

Liste sua oferta aos clientes com um link para uma avaliação gratuita fornecendo uma URL válida (começando com *http* ou *https*) em que eles podem obter uma avaliação.  Por exemplo: `https://contoso.com/trial/my-app`. As avaliações gratuitas de listagem de ofertas são criadas, gerenciadas e configuradas pelo seu serviço e não têm assinaturas gerenciadas pela Microsoft.

> [!NOTE]
> Os tokens que seu aplicativo receberá por meio do link de avaliação só podem ser usados para obter informações do usuário por meio do Azure Active Directory (Azure AD) para automatizar a criação da conta em seu aplicativo. Não há suporte para contas da Microsoft para autenticação usando esse token.

#### <a name="contact-me"></a>Contactar-me

Colete informações de contato do cliente conectando seu sistema de gerenciamento de relacionamento com o cliente (CRM). O cliente será solicitado a fornecer permissão para compartilhar suas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a origem do Marketplace onde encontraram sua oferta, serão enviados para o sistema CRM que você configurou. Para obter mais informações sobre como configurar seu CRM, consulte [Connect Lead Management](#connect-lead-management). 

### <a name="test-drive"></a>Versão de teste

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, fornecendo a eles a opção "tentar antes de comprar", resultando em uma maior conversão e na geração de clientes potenciais altamente qualificados. [Saiba mais sobre test drives.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para habilitar uma test drive, marque a caixa **habilitar um test drive** . Em seguida, você precisará configurar um ambiente de demonstração no [test drive de configuração técnica](#test-drive-technical-configuration) configure para permitir que os clientes experimentem sua oferta por um período de tempo fixo. 

#### <a name="type-of-test-drive"></a>Tipo de test drive

Selecione uma das seguintes opções:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Os produtos que se ajustam a esse cenário usam apenas os recursos do Azure.
- **[Central do Dynamics 365 for Business](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : A Microsoft hospeda e mantém o serviço de test drive (incluindo provisionamento e implantação) para um sistema de planejamento de recursos empresariais do Business central (finanças, operações, Cadeia de fornecedores, CRM, etc.).  
- **[Dynamics 365 para compromisso com o cliente](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : A Microsoft hospeda e mantém o serviço de test drive (incluindo provisionamento e implantação) para um sistema de envolvimento do cliente (vendas, serviço, serviço de projeto, serviço de campo, etc.).  
- **[Dynamics 365 para operações](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : A Microsoft hospeda e mantém o serviço de test drive (incluindo provisionamento e implantação) para um sistema de planejamento de recursos corporativos de finanças e operações (finanças, operações, manufatura, Cadeia de fornecedores, etc.). 
- **[Aplicativo lógico](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Um modelo de implantação que abrange todas as arquiteturas de solução complexas. Todos os produtos personalizados devem usar esse tipo de unidade de teste.
- **[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Um link inserido para um painel personalizado. Os produtos que desejam demonstrar um visual interativo Power BI devem usar esse tipo de unidade de teste. Tudo o que você precisa carregar aqui é a URL de Power BI inserida.

#### <a name="additional-test-drive-resources"></a>Recursos de test drive adicionais

- [Práticas recomendadas técnicas do Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Práticas recomendadas de marketing do Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral do Test Drive One pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Conectar gerenciamento de leads

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

Consulte [visão geral do gerenciamento de Lead](./commercial-marketplace-get-customer-leads.md) para obter mais informações.

Lembre-se de **salvar** antes de passar para a próxima seção!

## <a name="properties"></a>properties

A página **Propriedades** permite que você defina as categorias e os setores usados para agrupar sua oferta no Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta. Selecione **salvar** depois de concluir esta página. 

### <a name="category"></a>Category

Selecione no mínimo um e no máximo três categorias. Eles serão usados para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Lembre-se de chamar como sua oferta dá suporte a essas categorias na descrição da oferta. 

### <a name="industry"></a>Sector

Opcionalmente, você pode selecionar até dois setores para ajudar a categorizar sua oferta no Marketplace. Se sua oferta não for específica a um setor, deixe esta seção em branco. Lembre-se de destacar como sua oferta dá suporte aos setores selecionados na descrição da oferta. 

### <a name="applicable-dynamics-365-products"></a>Produtos do Dynamics 365 aplicáveis

Selecione todos os produtos Dynamics 365 aos quais esta oferta se aplica.

### <a name="app-version"></a>Versão da aplicação

Insira o número de versão da sua oferta. Os clientes verão essa versão listada na página de detalhes da oferta. Se você estiver atualizando apenas o número de versão devido a alterações de marketing/descritivas, marque a caixa **alteração somente de marketing** . Isso permite que a oferta ignore os estágios de certificação e provisionamento.

### <a name="standard-contract"></a>Contrato padrão

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um modelo de contrato padrão para ajudar a facilitar uma transação no Marketplace. 

Em vez de criar termos e condições personalizados, você pode optar por oferecer seu software sob o contrato padrão, que os clientes precisam apenas examinaremos e aceitar uma vez. 

O contrato padrão pode ser encontrado aqui: https://go.microsoft.com/fwlink/?linkid=2041178

Para usar o contrato padrão, marque a caixa **usar contrato padrão?** .

#### <a name="terms-of-use"></a>Termos de utilização

Se você não marcar a caixa **usar contrato padrão?** , você precisará fornecer seus próprios termos legais de uso no campo **termos de uso** . Insira até 10.000 caracteres de texto ou, se os termos de uso exigirem uma descrição mais longa, forneça a URL em que os termos de licença adicionais podem ser encontrados. Os clientes serão solicitados a aceitar esses termos antes que possam experimentar seu aplicativo.

## <a name="offer-listing"></a>Listagem de ofertas

A página de listagem da oferta exibe os idiomas nos quais sua oferta será listada. Observe que, no momento, o **Inglês (Estados Unidos)** é a única opção disponível.

Você precisará definir detalhes do Marketplace (nome da oferta, descrição, imagens, etc.) para cada idioma/mercado. Selecione o idioma/nome do mercado para fornecer essas informações.

> [!NOTE]
> A oferta de conteúdo de listagem (como descrição, documentos, capturas de tela, termos de uso etc.) não precisa estar em inglês, desde que a descrição da oferta comece com a frase ", este aplicativo está disponível apenas em [idioma diferente do inglês]". Também é aceitável fornecer uma URL de *Link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem da oferta.

### <a name="name"></a>Name

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto inserido para o **alias de oferta** quando você criou a oferta, mas pode alterar esse valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Descrição resumida

Forneça uma breve descrição da sua oferta (até 100 caracteres). Isso pode ser usado nos resultados da pesquisa do Marketplace.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta (até 3.000 caracteres). Essa descrição será exibida aos clientes na visão geral de listagem do Marketplace. Inclua a proposta de valor da sua oferta, os principais benefícios, a categoria e/ou as associações do setor, as oportunidades de compra no aplicativo e as divulgações necessárias. 

Algumas dicas para escrever sua descrição:  

- Descreva claramente a proposta de valor da sua oferta nas primeiras frases da sua descrição. Inclua o seguinte em sua proposta de valor:
  - Descrição do produto
  - O tipo de usuário que se beneficia do produto
  - O cliente precisa ou dificuldade de que os endereços do produto
- Tenha em mente que as primeiras frases podem ser exibidas nos resultados do mecanismo de pesquisa.  
- Não confie em recursos e funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que você fornece.  
- Use vocabulário específico do setor ou palavras com base no benefício o máximo possível. 
- Considere o uso de marcas HTML para formatar sua descrição e torná-la mais atraente.

### <a name="search-keywords"></a>Pesquisar palavras-chave

Opcionalmente, você pode inserir até três palavras-chave de pesquisa para ajudar os clientes a localizar sua oferta no Marketplace. Para obter melhores resultados, tente usar essas palavras-chave em sua descrição também.

### <a name="products-your-app-works-with"></a>Produtos com os quais seu aplicativo trabalha

Se você quiser permitir que os clientes saibam que seu aplicativo funciona com produtos específicos, insira até três nomes de produto aqui.

### <a name="support-urls"></a>URLs de suporte

Esta seção permite que você forneça links para ajudar os clientes a entenderem mais sobre sua oferta.

#### <a name="help-link"></a>Hiperligação de ajuda

Insira a URL onde os clientes podem saber mais sobre sua oferta.

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Insira a URL para a política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e para fornecer uma política de privacidade válida.

### <a name="contacts"></a>Contactos

Nesta seção, você deve fornecer o nome, o email e o número de telefone para um **contato de suporte** e um contato de **engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP.

Na seção **contato de suporte** , você também deve fornecer a **URL de suporte** em que os parceiros do CSP podem encontrar suporte para sua oferta.

### <a name="supporting-documents"></a>Documentos de suporte

Você deve fornecer pelo menos um (e até três) documentos de marketing relacionados aqui, como White papers, folhetos, listas de verificação ou apresentações. Esses documentos devem estar no formato. pdf.

### <a name="marketplace-images"></a>Imagens do Marketplace

Nesta seção, você pode fornecer logotipos e imagens que serão usados ao mostrar sua oferta ao cliente. Todas as imagens devem estar no formato. png.

#### <a name="store-logos"></a>Armazenar logotipos

Você pode fornecer o logotipo de sua oferta em três tamanhos: **Pequeno (48 x 48)** , **grande (216 x 216)** e **largo (255 x 115)** . Os tamanhos pequenos e grandes são necessários.

#### <a name="hero"></a>Destaque

A imagem do Hero é opcional. Se você fornecer um, ele deve medir 815 x 290 pixels.

#### <a name="screenshots"></a>Capturas de ecrã

Adicione capturas de tela que mostram como sua oferta funciona. Pelo menos uma captura de tela é necessária e você pode adicionar até cinco. Todas as capturas de tela devem ser 1280 x 720 pixels.

#### <a name="videos"></a>Os meus vídeos

Opcionalmente, você pode adicionar até quatro vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou no Vimeo. Para cada um, insira o nome do vídeo, sua URL e uma imagem em miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

- [Práticas recomendadas para listagens de ofertas do Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Disponibilidade

A página **disponibilidade** fornece opções sobre onde e como tornar sua oferta disponível.

### <a name="markets"></a>Mercados

Esta seção permite que você especifique os mercados nos quais sua oferta deve estar disponível. Para fazer isso, selecione **Editar mercados.** Isso exibirá a janela pop-up **seleção do mercado** .

Por padrão, não há mercados selecionados, mas você deve selecionar pelo menos um mercado para publicar sua oferta. Clique em **selecionar tudo** para disponibilizar sua oferta em todos os possíveis mercados ou selecione os mercados específicos que você deseja adicionar. Depois de concluir, selecione **salvar**.

Observe que suas seleções se aplicam somente a novas aquisições; Se alguém já tiver seu aplicativo em um determinado mercado e você remover esse mercado posteriormente, as pessoas que já têm a oferta desse mercado poderão continuar a usá-lo, mas nenhum novo cliente nesse mercado poderá obter sua oferta.

> [!IMPORTANT]
> É sua responsabilidade atender a requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center.

Tenha em mente que, mesmo que você selecione todos os mercados, as leis e restrições locais ou outros fatores podem impedir que determinadas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Público de visualização

Antes de publicar sua oferta em tempo real para a oferta mais ampla do Marketplace, primeiro você precisará disponibilizá-la para um **público de visualização**limitado. Insira uma **tecla Hide** (qualquer cadeia de caracteres que use apenas letras minúsculas e/ou números) aqui. Os membros do seu público de visualização podem usar essa tecla Hide como um token para exibir uma visualização da sua oferta no Marketplace.

Em seguida, quando estiver pronto para disponibilizar sua oferta e remover a restrição de visualização, você precisará remover a **chave Hide** e publicar novamente.

## <a name="technical-configuration"></a>Configuração técnica

A página de **configuração técnica** define os detalhes técnicos usados para se conectar à sua oferta. Essa conexão nos permite provisionar sua oferta para o cliente final se ele optar por adquiri-lo.

### <a name="base-license-model"></a>Modelo de licença base

O modelo de licença base determina como os clientes são atribuídos ao seu aplicativo no centro de administração do CRM. Selecione **recurso** para licenciamento baseado em instância ou **usuário** se as licenças forem atribuídas uma por locatário.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Requer acesso de entrada S2S e repositório seguro do CRM

Marque esta caixa para habilitar a configuração do repositório seguro do CRM ou o acesso de saída do servidor a servidor (S2S). Observe que esse recurso requer uma consideração especializada da equipe do Dynamics 365 durante a fase de certificação. A Microsoft entrará em contato com você para concluir etapas adicionais para dar suporte a esse recurso.

### <a name="application-configuration-url"></a>URL de configuração do aplicativo

Forneça a URL para a página da Web de configuração que permite ao cliente configurar seu aplicativo.

### <a name="crm-package"></a>Pacote CRM

Na **URL do campo local do pacote** , insira a URL de uma conta de armazenamento do Azure que contém o arquivo. zip do pacote do CRM carregado. Essa URL deve incluir uma chave SAS somente leitura para permitir que a Microsoft pegue seu pacote para verificação.

Marque a caixa denominada **há mais de um pacote CRM no arquivo de pacote**, se aplicável. Nesse caso, certifique-se de incluir todos os pacotes em seu arquivo. zip.

### <a name="crm-package-availability"></a>Disponibilidade do pacote CRM

Nesta seção, selecione **+ Adicionar região** para especificar as regiões geográficas em que o pacote do CRM estará disponível para os clientes. A implantação para as seguintes regiões soberanas exige permissão especial e validação durante o processo de certificação: [Alemanha](https://docs.microsoft.com/azure/germany/), [nuvem do governo dos EUA](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)e Tip.

Por padrão, a **URL de configuração de aplicativo** que você inseriu acima será usada para cada região. Se preferir, você pode inserir uma URL de configuração de aplicativo separada para uma ou mais regiões específicas. 

## <a name="test-drive-technical-configuration"></a>Testar a configuração técnica

Se você selecionou **habilitar um test drive** na página [instalação da oferta](#offer-setup) , será necessário fornecer detalhes aqui para permitir que os clientes experimentem uma test drive de sua oferta.

A página **Test Drive** permite que você configure uma demonstração (ou "Test Drive"), que permitirá que os clientes experimentem sua oferta antes de confirmar a compra. Saiba mais no artigo [o que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se você não quiser mais fornecer um test drive para sua oferta, retorne à página de **[instalação da oferta](#offer-setup)** e desmarque **habilitar Test Drive**.

Os seguintes tipos de unidades de teste estão disponíveis, cada um com seus próprios requisitos de configuração técnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicativo lógico](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (Configuração técnica não necessária)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para Azure Resource Manager test drive

Um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Os produtos que se ajustam a esse cenário usam apenas os recursos do Azure. Saiba mais sobre como configurar um [test drive de Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões** do (obrigatório): Atualmente, há 26 regiões com suporte do Azure em que seu test drive pode ser disponibilizado. Normalmente, você desejará disponibilizar seus test drive nas regiões em que você prevê o maior número de clientes, para que eles possam selecionar a região mais próxima para o melhor desempenho. Você precisará certificar-se de que sua assinatura tem permissão para implantar todos os recursos necessários em cada uma das regiões que você está selecionando.

- **Instâncias**: Selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões em que sua oferta está disponível.

**Quente**: Esse tipo de instância é implantado e aguardando acesso por região selecionada. Os clientes podem acessar instantaneamente as instâncias *quentes* de um Test Drive, em vez de ter que esperar por uma implantação. A desvantagem é que estas instâncias são sempre em execução na sua subscrição do Azure, para que eles incorre num tempo de atividade maior custos. É altamente recomendável ter pelo menos uma instância de *acesso* , pois a maioria dos clientes não deseja esperar por implantações completas, resultando em uma retirada no uso do cliente, se nenhuma instância de *acesso* estiver disponível.

**Frio**: Esse tipo de instância representa o número total de instâncias que podem possivelmente ser implantadas por região. As instâncias frias exigem todo o modelo do Gerenciador de recursos da unidade de teste para implantar quando um cliente solicita a Test Drive, portanto, as instâncias frias são muito mais lentas para serem carregadas do que as instâncias *quentes* . A compensação é que você só precisa pagar pela duração da test drive, *nem* sempre está em execução em sua assinatura do Azure como com uma instância de *Hot* .

- **Testar unidade de Azure Resource Manager modelo**: Carregue o. zip que contém seu modelo de Azure Resource Manager.  Saiba mais sobre como criar um modelo de Azure Resource Manager no artigo de início rápido [criar e implantar modelos de Azure Resource Manager usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração do teste de unidade** (obrigatório): Insira o período de tempo que a unidade de teste permanecerá ativa, em número de horas. O Test-Drive automaticamente termina após o final deste período de tempo. Essa duração pode apenas apostar definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica do Dynamics 365 test drive

A Microsoft pode remover a complexidade de configurar um test drive hospedando e mantendo o provisionamento e a implantação de serviços usando esse tipo de test drive. A configuração para esse tipo de test drive hospedada é a mesma, independentemente de o test drive ter como alvo um centro de negócios, um compromisso com o cliente ou um público de operações.

- **Máximo de unidades de teste simultâneas** (obrigatório): Defina o número máximo de clientes que podem usar seu test drive ao mesmo tempo. Cada usuário simultâneo consumirá uma licença do Dynamics 365 enquanto o test drive estiver ativo, portanto, será necessário garantir que você tenha licenças suficientes disponíveis para dar suporte ao limite máximo definido. Valor recomendado de 3-5.

- **Duração do teste de unidade** (obrigatório): Insira o período de tempo que a unidade de teste permanecerá ativa definindo o número de horas. Depois disso, a sessão será encerrada e não consumirá mais uma de suas licenças. Recomendamos um valor de 2-24 horas, dependendo da complexidade da sua oferta. Essa duração pode apenas apostar definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).  O usuário pode solicitar uma nova sessão se ela ficar sem tempo e desejar acessar a test drive novamente.

- **URL da instância** (obrigatório): A URL em que o cliente começará sua test drive. Normalmente, a URL da instância do Dynamics 365 que executa seu aplicativo com os dados de exemplo https://testdrive.crm.dynamics.com) instalados (por exemplo,.

- **URL da API Web da instância** (obrigatório): Recupere a URL da API da Web para sua instância do Dynamics 365 fazendo logon em sua conta do Microsoft 365 e navegando para **as configurações** \&gt; **Personalização** do \&gt; **Recursos para desenvolvedores** \&gt; **API Web da instância (URL da raiz do serviço)** , copie a URL encontrada aqui https://testdrive.crm.dynamics.com/api/data/v9.0) (por exemplo,.

- **Nome da função** (obrigatório): Forneça o nome da função de segurança que você definiu em seu test drive personalizado do Dynamics 365. Isso será atribuído ao usuário durante seu test drive (por exemplo, Test-Drive-Role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para o aplicativo lógico test drive

Todos os produtos personalizados devem usar esse tipo de test drive modelo de implantação que abrange uma variedade de arquiteturas de solução complexas. Para obter mais informações sobre como configurar unidades lógicas de teste de aplicativo, visite [operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [envolvimento do cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no github.

- **Região** do (requerido, lista suspensa de seleção única): Atualmente, há 26 regiões com suporte do Azure em que seu test drive pode ser disponibilizado. Os recursos para seu aplicativo lógico serão implantados na região que você selecionar. Se seu aplicativo lógico tiver qualquer recurso personalizado armazenado em uma região específica, verifique se a região está selecionada aqui. A melhor maneira de fazer isso é implantar totalmente seu aplicativo lógico localmente em sua assinatura do Azure no portal e verificar se ele funciona corretamente antes de fazer essa seleção.

- **Máximo de unidades de teste simultâneas** (obrigatório): Defina o número máximo de clientes que podem usar seu test drive ao mesmo tempo. Essas unidades de teste já estão implantadas, permitindo que os clientes as acessem instantaneamente sem esperar por uma implantação.

- **Duração do teste de unidade** (obrigatório): Insira o período de tempo que a unidade de teste permanecerá ativa, em número de horas. O test drive é encerrado automaticamente após o término desse período de tempo.

- **Nome do grupo de recursos do Azure** (obrigatório): Insira o nome do [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) em que seu aplicativo lógico Test Drive é salvo.

- **Nome do aplicativo lógico do Azure** (obrigatório): Insira o nome do aplicativo lógico que atribui o test drive ao usuário. Este aplicativo lógico deve ser salvo no grupo de recursos do Azure acima.

- **Desprovisionar nome do aplicativo lógico** (obrigatório): Insira o nome do aplicativo lógico que desprovisiona o test drive quando o cliente é concluído. Este aplicativo lógico deve ser salvo no grupo de recursos do Azure acima.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para unidades de teste de Power BI

Os produtos que desejam demonstrar um visual interativo Power BI podem usar um link incorporado para compartilhar um Dashboard personalizado como seu test drive, nenhuma configuração técnica adicional é necessária. Saiba mais sobre como configurar aplicativos de modelo de[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) .

### <a name="deployment-subscription-details"></a>Detalhes da assinatura da implantação

Para implantar o Test Drive em seu nome, crie e forneça uma assinatura do Azure separada e exclusiva. (Não é necessário para Power BI unidades de teste).

- **ID da assinatura do Azure** (necessário para Azure Resource Manager e aplicativos lógicos): Insira a ID da assinatura para conceder acesso aos serviços de conta do Azure para relatório e cobrança de uso de recursos. Recomendamos que você considere a [criação de uma assinatura do Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) a ser usada para unidades de teste, se você ainda não tiver uma. Você pode encontrar sua ID de assinatura do Azure fazendo logon no [portal do Azure](https://portal.azure.com/) e navegando até a guia **assinaturas** do menu do lado esquerdo. A seleção da guia exibirá sua ID de assinatura (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID do locatário do Azure ad** (obrigatório): Insira sua [ID de locatário](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)do Azure Active Directory (AD). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **Propriedades** e procure o número de **ID de diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode pesquisar a ID de locatário da sua organização usando sua URL de nome de [https://www.whatismytenantid.com](https://www.whatismytenantid.com)domínio em:.

- **Nome do locatário do Azure ad** (necessário para o Dynamic 365): Insira seu nome de Azure Active Directory (AD). Para localizar esse nome, entre no [portal do Azure](https://portal.azure.com/), no canto superior direito, o nome do locatário será listado em seu nome de conta.

- **ID do aplicativo do Azure ad** (obrigatório): Insira sua [ID do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **registros de aplicativo**e procure o número de **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **Segredo do cliente de aplicativo do Azure ad** (obrigatório): Insira o [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)do aplicativo Azure AD. Para localizar esse valor, entre no [portal do Azure](https://portal.azure.com/). Selecione a guia **Azure Active Directory** no menu à esquerda, selecione **registros de aplicativo**e, em seguida, selecione seu aplicativo Test Drive. Em seguida, **Selecione certificados e segredos**, **Selecione novo segredo do cliente**, insira uma descrição, selecione **nunca** em **expirar**e, em seguida, escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para fora da página antes de fazer isso, ou você não terá acesso ao valor.)

### <a name="test-drive-marketplace-listings"></a>Testar listagens do Marketplace

A opção de **listagem do Marketplace** encontrada na guia **Test Drive** exibe os idiomas em que seu Test Drive está disponível. Observe que atualmente o **Inglês (Estados Unidos)** é o único local disponível. Selecione o nome do idioma para inserir informações que descrevam a experiência de test drive.

- **Descrição** do (obrigatório): Descreva sua test drive, o que será demonstrado, os objetivos para o usuário experimentar, os recursos a serem explorados e quaisquer informações relevantes para ajudar o usuário a determinar se deve adquirir sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informações de acesso** (necessário para unidades de teste de Azure Resource Manager e lógica): Explique o que um cliente precisa saber para acessar e usar essa test drive. Percorra um cenário para usar sua oferta e exatamente o que o cliente deve saber para acessar recursos em todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do usuário** (obrigatório): Uma explicação detalhada de sua experiência de test drive. O manual do usuário deve abranger exatamente o que você deseja que o cliente tenha de apresentar o test drive e servir como uma referência para quaisquer perguntas que possam ter. O arquivo deve estar no formato PDF e ter o nome (máximo de 255 caracteres) após o carregamento.

- **Explica Adicionar vídeos** (opcional): Os vídeos podem ser carregados no YouTube ou no Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa exibir uma orientação sobre as informações para ajudá-los a entender melhor o test drive, incluindo como usar com êxito os recursos do seu ofereça e entenda os cenários que destacam seus benefícios.
  - **Nome** do necessária
  - **URL (somente YouTube ou Vimeo)** necessária
  - **Miniatura (533 x 324px)** : O arquivo de imagem deve estar no formato PNG.

## <a name="supplemental-content"></a>Conteúdo complementar

Esta página permite que você forneça informações adicionais necessárias sobre sua oferta.

### <a name="key-usage-scenario"></a>Cenário de uso de chave

Você deve carregar um arquivo. pdf que lista os principais cenários de uso de sua oferta listados em um documento (formato. pdf). Todos os cenários listados aqui podem ser verificados por nossa equipe de validação antes de aprovarmos sua oferta para o Marketplace.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para visualização

Depois de concluir todas as seções necessárias da oferta, selecione **publicar** no canto superior direito do Portal. Você será redirecionado para a página **revisar e publicar** . 

Se esta for a primeira vez que você publica essa oferta, você pode:

- Consulte o status de conclusão de cada seção da oferta.
    - *Não iniciado* – significa que a seção não foi tocada e precisa ser concluída.
    - *Incompleto* – significa que a seção tem erros que precisam ser corrigidos ou que requer mais informações a serem fornecidas. Volte para a (s) seção (ões) e atualize-a.
    - *Concluído* – significa que a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta devem estar em um estado completo antes que você possa enviar a oferta.
- Na seção **notas de certificação** , forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de qualquer nota suplementar útil para entender seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Enviaremos um email para que você saiba quando uma versão prévia da oferta está disponível para revisão e aprovação. Você deve retornar ao Partner Center e selecionar **Go-Live** para a oferta para publicar sua oferta no público (ou se uma oferta privada, para o público privado).

## <a name="next-steps"></a>Passos Seguintes

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)

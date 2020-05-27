---
title: Crie uma Dinâmica 365 para envolvimento com o cliente & oferta powerApps no mercado comercial da Microsoft
description: Como criar uma nova Dinâmica 365 para envolvimento com o cliente & oferta PowerApps para listagem ou venda no Azure Marketplace, AppSource ou através do programa Cloud Solution Provider (CSP) no Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e49ed542af140b28e8ebe58e5c8920d0959c9387
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848909"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Criar uma oferta do Dynamics 365 for Customer Engagement & PowerApps

Este tópico explica como criar uma nova Dinâmica 365 para o envolvimento com o cliente & oferta powerApps. Todas as aplicações para a Dynamics 365 para envolvimento com o cliente (PowerApps, Vendas, Serviço, Serviço de Projetos e Serviço de Campo) devem passar pelo nosso processo de certificação e apoiar uma experiência experimental. O processo de certificação verifica a sua solução para requisitos padrão, compatibilidade e práticas adequadas. A experiência experimental permite que os utilizadores implementem a sua solução para um ambiente live Dynamics 365.

Antes de começar, [Crie uma conta de Marketplace Comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se ainda não o fez. Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página Overview, selecione **+ Nova oferta**Dynamics  >  **365 para envolvimento com clientes & PowerApps**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-dynamics-365-cepa.png)

> [!NOTE]
> Depois de publicada uma oferta, as edificações feitas no Partner Center só aparecem nas montras depois de reeditarem a oferta. Certifique-se de que republique sempre depois de fazer alterações.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos do Gestor de Recursos Azure, se aplicável.
- Utilize apenas letras minúsculas e números. Pode incluir hífenes e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir o **test-offer-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira um **pseudónimo da Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- Isto não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

Siga estes passos para configurar a sua oferta.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Como pretende que potenciais clientes interajam com esta oferta de listagem?

Selecione a opção que deseja utilizar para esta oferta.

#### <a name="get-it-now-free"></a>Obtê-lo agora (grátis)

Liste gratuitamente a sua oferta aos clientes, fornecendo um URL válido (a partir de *http* ou *https)* onde possam aceder à sua aplicação.  Por exemplo, `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Julgamento gratuito (listagem)

Enumere a sua oferta aos clientes com um link para um teste gratuito, fornecendo um URL válido (começando com *http* ou *https)* onde possam obter um teste.  Por exemplo, `https://contoso.com/trial/my-app`. Os testes gratuitos de listagem de ofertas são criados, geridos e configurados pelo seu serviço e não têm subscrições geridas pela Microsoft.

> [!NOTE]
> Os tokens que a sua aplicação receberá através do seu link experimental só podem ser utilizados para obter informações de utilizador através do Azure Ative Directory (Azure AD) para automatizar a criação de conta na sua app. As contas da Microsoft não são suportadas para autenticação utilizando este símbolo.

#### <a name="contact-me"></a>Contacte-me

Recolha informações de contacto com o cliente ligando o seu sistema de Gestão de Relacionamento com o Cliente (CRM). Será solicitado ao cliente permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e fonte de mercado onde encontraram a sua oferta, serão enviados para o sistema CRM que configura. Para mais informações sobre a configuração do seu CRM, consulte [os leads do Cliente](#customer-leads).

Selecione **guardar rascunho** antes de continuar.

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "tentar antes de comprar", resultando num aumento da conversão e na geração de pistas altamente qualificadas. [Saiba mais sobre test drives](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Para ativar um test drive durante um período de tempo fixo, selecione a caixa de verificação **de test drive.** Para remover o test drive da sua oferta, limpe esta caixa de verificação. Configure o ambiente de acionamento de teste na secção de [configuração técnica](#test-drive-technical-configuration) test drive mais tarde neste tópico.

Para obter informações adicionais, consulte [Test drive a sua oferta no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>Tipo de test drive

Selecione entre as seguintes opções:

- **[Gestor de Recursos Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – Um modelo de implantação que contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure.
- **[Dynamics 365 para business central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – Microsoft acolhe e mantém o serviço de test drive (incluindo provisionamento e implementação) para um sistema de planeamento de recursos empresariais da Business Central (finanças, operações, cadeia de fornecimento, CRM, etc.).  
- **[Dinâmica 365 para envolvimento com](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** o cliente – A Microsoft acolhe e mantém o serviço de test drive (incluindo o fornecimento e a implementação) para um sistema de envolvimento com o cliente (vendas, serviço, serviço de projeto, serviço de campo, etc.).  
- **[Dinâmica 365 para Operações](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – A Microsoft acolhe e mantém o serviço de test drive (incluindo o fornecimento e a implementação) para um sistema de planeamento de recursos empresariais de Finanças e Operações (finanças, operações, fabrico, cadeia de fornecimento, etc.). 
- **[Aplicativo lógico](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** – Um modelo de implementação que abrange todas as arquiteturas de soluções complexas. Qualquer produto personalizado deve utilizar este tipo de Test Drive.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – Uma ligação incorporada a um dashboard personalizado. Os produtos que pretendam demonstrar um visual interativo do Power BI devem utilizar este tipo de Test Drive. Tudo o que precisa fazer upload aqui é o seu URL power BI incorporado.

#### <a name="additional-test-drive-resources"></a>Recursos adicionais de test drive

- [Boas Práticas Técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Práticas de Marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral das unidades de teste](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (certifique-se de que o seu bloqueador pop-up está desligado)

### <a name="customer-leads"></a>Lideranças de clientes

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para mais informações, consulte a visão geral da [gestão de chumbo](./commercial-marketplace-get-customer-leads.md).

Selecione **guardar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias e indústrias usadas para agrupar a sua oferta no mercado, na sua versão de aplicação e nos contratos legais de apoio à sua oferta.

### <a name="category"></a>Categoria

Selecione pelo menos uma e até três categorias. Estes são usados para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Certifique-se de descrever como a sua oferta suporta estas categorias na descrição da oferta.

### <a name="industry"></a>Setor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Produtos Dinâmica 365 aplicáveis

Selecione todos os produtos Dynamics 365 a que esta oferta se aplica.

### <a name="app-version"></a>Versão da aplicação

Insira o número da versão da sua oferta. Os clientes verão esta versão listada na página de detalhes da oferta. Se estiver apenas a atualizar o número da versão devido a alterações de marketing/descrição, verifique **apenas a caixa de alteração de Marketing.** Esta opção permite que a oferta contorne as fases de certificação e provisionamento.

### <a name="terms-and-conditions"></a>Termos e condições

Forneça os seus próprios termos e condições legais aqui. Pode também fornecer o endereço onde os seus termos e condições podem ser encontrados. Os clientes serão obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

Selecione **guardar rascunho** antes de continuar.

## <a name="offer-listing"></a>Oferta listagem

Esta página exibe os idiomas em que a sua oferta será listada. Atualmente, **o inglês (Estados Unidos)** é a única opção disponível.

Defina detalhes do mercado para cada idioma/mercado aqui, como nome, descrição e imagens de oferta. Selecione o nome de idioma/mercado para fornecer esta informação.

> [!NOTE]
> Oferecer conteúdo de listagem (como a descrição, documentos, imagens e termos de utilização) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um URL de *Link Útil* para oferecer conteúdo num idioma diferente do utilizado no conteúdo de listagem de oferta.

### <a name="name"></a>Name

O nome que introduzir aqui será mostrado aos clientes como título da sua oferta. Este campo é pré-povoado com o texto que inscreveu para **o pseudónimo oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser marcado por marca (e pode incluir símbolos de marca ou direitos de autor). O nome não pode ser mais de 50 caracteres e não pode incluir emojis.

### <a name="short-description"></a>Descrição curta

Forneça uma breve descrição da sua oferta, até 100 caracteres. Esta descrição pode ser utilizada nos resultados da pesquisa no mercado.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Pesquisar palavras-chave

Pode inserir opcionalmente até três palavras-chave de pesquisa para ajudar os clientes a encontrar a sua oferta no mercado. Para obter os melhores resultados, use também estas palavras-chave na sua descrição.

### <a name="products-your-app-works-with"></a>Produtos com os que a sua app trabalha

Se quiser informar os clientes de que a sua aplicação funciona com produtos específicos, insira até três nomes de produtos aqui.

### <a name="support-urls"></a>URLs de suporte

Esta secção permite-lhe fornecer links para ajudar os clientes a entender mais sobre a sua oferta.

#### <a name="help-link"></a>Link de ajuda

Insira o endereço onde os clientes podem saber mais sobre a sua oferta.

#### <a name="privacy-policy-url"></a>URL de política de privacidade

Insira o endereço da política de privacidade da sua organização. É responsável por garantir que a sua aplicação cumpre as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="contacts"></a>Contactos

Forneça o nome, e-mail e número de telefone para um **contacto de suporte** e um contacto de **Engenharia.** Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft e poderá ser fornecida aos parceiros CSP.

Na secção de contacto de **suporte,** deve também fornecer o URL de **Suporte** onde os parceiros de CSP podem encontrar suporte para a sua oferta.

### <a name="supporting-documents"></a>Documentos comprovantes

Forneça aqui pelo menos um (e até três) documentos de marketing relacionados, tais como papéis brancos, brochuras, listas de verificação ou apresentações, em formato PDF.

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para a sua oferta. Todas as imagens devem estar em formato PNG.

>[!NOTE]
>Se tiver algum problema de envio de ficheiros, certifique-se de que a sua rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos de loja

Forneça o logótipo da sua oferta em três tamanhos de pixel:
- **Pequeno** (obrigatório; 48 x 48)
- **Grande** (obrigatório; 216 x 216)
- **Ampla** (opcional; 255 x 115)

#### <a name="hero"></a>Herói

A imagem de herói é opcional. Se fornecer um, deve medir 815 x 290 pixels.

#### <a name="screenshots"></a>Capturas de ecrã

Adicione imagens que mostrem como funciona a sua oferta. Pelo menos uma imagem é necessária, e pode somar até cinco. Todas as imagens devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Pode adicionar opcionalmente quatro vídeos que demonstram a sua oferta. Estes vídeos devem ser hospedados no YouTube e/ou vimeo. Para cada um, introduza o nome do vídeo, o seu URL e uma imagem de miniatura do vídeo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de mercado

[Boas práticas para anúncios de oferta de mercado](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Selecione **guardar rascunho** antes de continuar.

## <a name="availability"></a>Disponibilidade

Esta página permite definir onde e como disponibilizar a sua oferta.

### <a name="markets"></a>Mercados

Especifique os mercados em que a sua oferta deve estar disponível. Para isso, selecione **mercados Edit**, que mostrarão a janela popup de seleção de **mercado.**

Por padrão, não são selecionados mercados, mas deve selecionar pelo menos um mercado para publicar a sua oferta. Escolha **selecione tudo** para disponibilizar a sua oferta em todos os mercados possíveis ou selecione os mercados específicos que pretende adicionar.

As suas seleções aplicam-se apenas a novas aquisições; Se alguém já tiver a sua app num determinado mercado e mais tarde remover esse mercado, as pessoas que já têm a oferta nesse mercado podem continuar a usá-la, mas nenhum novo cliente nesse mercado poderá obter a sua oferta.

> [!IMPORTANT]
> É da sua responsabilidade cumprir quaisquer requisitos legais locais, mesmo que esses requisitos não estejam listados aqui ou no Partner Center. Mesmo que você selecione todos os mercados, leis e restrições locais ou outros fatores podem impedir que certas ofertas sejam listadas em alguns países e regiões.

### <a name="preview-audience"></a>Pré-visualização do público

Antes de publicar a sua oferta ao vivo para a oferta de mercado mais ampla, primeiro terá de a disponibilizar a um público de **pré-visualização**limitado. Introduza aqui uma **tecla Ocultar** (qualquer corda que utilize apenas letras minúsculas e/ou números) aqui. Os membros do seu público de pré-visualização podem usar esta chave de ocultação como um símbolo para ver uma pré-visualização da sua oferta no mercado.

Em seguida, quando estiver pronto para disponibilizar a sua oferta e remover a restrição de pré-visualização, terá de remover a **tecla Ocultar** e publicar novamente.

Selecione **guardar rascunho** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Esta página define os detalhes técnicos utilizados para se ligar à sua oferta. Esta ligação permite-nos fornecer a sua oferta para o cliente final se optarem por adquiri-la.

### <a name="base-license-model"></a>Modelo de licença base

O modelo de licença base determina como os clientes são atribuídos a sua aplicação no CrM Admin Center. Selecione **Recurso** para licenciamento por exemplo ou **Utilizador** se as licenças forem atribuídas uma por inquilino.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Requer acesso à loja S2S e à CRM Secure Store

Verifique esta caixa para ativar a configuração do acesso de saída da CRM Secure Store ou do Servidor para servidor (S2S). Esta funcionalidade requer consideração especializada da Equipa Dinâmica 365 durante a fase de certificação. A Microsoft entrará em contacto consigo para completar passos adicionais para suportar esta funcionalidade.

### <a name="application-configuration-url"></a>URL de configuração de aplicação

Forneça o URL para a página web de configuração que permite ao cliente configurar a sua aplicação.

### <a name="crm-package"></a>Pacote CRM

No **URL do seu** campo de localização do pacote, introduza o URL de uma conta de Armazenamento Azure Blob que contenha o pacote CRM carregado .zip file. Inclua uma tecla SAS apenas para ler no URL para que a Microsoft possa recolher o seu pacote para verificação.

> [!IMPORTANT]
> Para evitar um bloco de publicação, certifique-se de que a data de validade no URL do seu armazenamento Blob não expirou. Pode rever a data acedendo à sua apólice. Recomendamos que o **prazo de validade** seja de pelo menos um mês no futuro.

Selecione a caixa rotulada **Há mais de um pacote CRM no meu ficheiro**de pacote, se aplicável. Em caso afirmativo, certifique-se de incluir todos os pacotes no seu ficheiro .zip.

Para obter informações detalhadas sobre como construir o seu pacote e atualizar a sua estrutura, consulte [passo 3: Crie um pacote AppSource para a sua aplicação](https://docs.microsoft.com/powerapps/developer/common-data-service/create-package-app-appsource).

### <a name="crm-package-availability"></a>Disponibilidade de pacote crm

Nesta secção, selecione **+ Adicione a região** para especificar as regiões geográficas em que o seu pacote CRM estará disponível para os clientes. A implantação para as seguintes regiões soberanas requer autorização e validação especiais durante o processo de certificação: [Alemanha,](https://docs.microsoft.com/azure/germany/) [Nuvem do Governo dos EUA](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)e TIP.

Por predefinição, o URL de configuração da **aplicação** que introduziu acima será utilizado para cada região. Se preferir, pode introduzir um URL de Configuração de Aplicação separado para uma ou mais regiões específicas. 

Selecione **guardar rascunho** antes de continuar.

## <a name="test-drive-technical-configuration"></a>Configuração técnica de test drive

Esta página permite-lhe configurar uma demonstração ("test drive") que permite aos clientes experimentarem a sua oferta antes de a adquirirem. Saiba mais no artigo [O que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para ativar um test drive, selecione a caixa de verificação de test drive no separador [de configuração oferta.](#test-drive) Para remover o test drive da sua oferta, limpe esta caixa de verificação.

Saiba mais no artigo [O que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Estão disponíveis os seguintes tipos de test drives, cada um com os seus próprios requisitos de configuração técnica:

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicação lógica](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuração técnica não necessária)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para unidade de teste Do Gestor de Recursos Azure

Um modelo de implantação que contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure. Saiba mais sobre a criação de um [test drive Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões** (necessárias) – Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Normalmente, você vai querer disponibilizar o seu test drive nas regiões onde você antecipa o maior número de clientes, para que eles possam selecionar a região mais próxima para o melhor desempenho. Terá de se certificar de que a sua subscrição é autorizada a disponibilizar todos os recursos necessários em cada uma das regiões que está a selecionar.
- **Instâncias** – Selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões onde a sua oferta está disponível.

    **Quente** – Este tipo de instância é implantado e aguarda acesso por região selecionada. Os clientes podem aceder instantaneamente a casos *quentes* de um test drive, em vez de terem de esperar por uma implementação. A contrapartida é que estes casos estão sempre a decorrer na sua subscrição Azure, pelo que incorrerão num custo de uptime maior. É altamente recomendado ter pelo menos uma instância *Hot,* uma vez que a maioria dos clientes não quer esperar por implementações completas, resultando numa entrega no uso do cliente se não houver uma instância *hot* disponível.

    **Frio** – Este tipo de instância representa o número total de casos que podem ser eventualmente implantados por região. As instâncias frias requerem que todo o modelo de Gestor de Recursos test drive seja implantado quando um cliente solicita a unidade de teste, por isso as instâncias *frias* são muito mais lentas a carregar do que as instâncias *hot.* A contrapartida é que só tem de pagar a duração do test drive, *nem* sempre está a funcionar na sua subscrição Azure como com uma instância *Hot.*

- Modelo de Gestor de **Recursos Azure** de tração de teste – Faça upload do .zip contendo o seu modelo de Gestor de Recursos Azure. Saiba mais sobre a criação de um modelo de Gestor de Recursos Azure no artigo quickstart Criar e implementar modelos de [Gestor de Recursos Azure utilizando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração do test drive** (necessária) – Introduza o número de horas em que o test drive permanecerá ativo. O testdDrive termina automaticamente após o fim deste período de tempo. Esta duração só pode ser definida em horas inteiras (por exemplo, as "2" horas são válidas; "1,5" não é).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica para unidade de teste Dynamics 365

A Microsoft pode remover a complexidade da instalação de um test drive hospedando e mantendo o fornecimento e implementação do serviço utilizando este tipo de test drive. A configuração para este tipo de test drive hospedado é a mesma, independentemente de o test drive estar direcionado para um público de Business Central, Customer Engagement ou Operations.

- **Max simultâneo test drives** (necessário) – Dete o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Cada utilizador simultâneo consumirá uma licença Dynamics 365 enquanto o test drive estiver ativo, pelo que terá de garantir que tem licenças suficientes para suportar o limite máximo definido. O valor recomendado é 3-5.

- **Duração do test drive** (necessária) – Introduza o número de horas em que o test drive permanecerá ativo. Após este tempo, a sessão terminará e deixará de consumir uma das suas licenças. Recomendamos um valor de 2 a 24 horas dependendo da complexidade da sua oferta. Esta duração só pode ser definida em horas inteiras (por exemplo, as "2" horas são válidas; "1,5" não é). O utilizador pode solicitar uma nova sessão se ficar sem tempo e pretender aceder novamente ao test drive.

- **URL de instância** (necessário) – O URL onde o cliente iniciará o seu test drive. Tipicamente, o URL da sua instância Dynamics 365 executando a sua aplicação com dados de amostra instalados (por exemplo, `https://testdrive.crm.dynamics.com` ).

- **Exemplo Web API URL** (necessário) – Recupere o URL Web API para a sua instância Dynamics 365, iniciando sessão na sua conta Microsoft 365 e navegando para **Definições** \& gt; **Personalização** \& gt; Recursos de **Desenvolvimento** \& gt; **Por exemplo, Web API (URL raiz**de serviço) , copiar o URL encontrado aqui (por exemplo, `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- Nome de **função** (obrigatório) – Forneça o nome de papel de segurança que definiu no seu test drive personalizado Dynamics 365. Este nome de função de segurança será atribuído ao utilizador durante o seu test drive (por exemplo, função test-drive).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para unidade de teste de aplicativo lógica

Qualquer produto personalizado deve usar este tipo de modelo de implantação de test drive, que engloba uma variedade de arquiteturas de solução complexas. Para mais informações sobre a configuração de unidades de teste de Aplicações Lógicas, visite [Operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Envolvimento do Cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (lista de abandono obrigatório e de seleção única) – Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Os recursos para a sua aplicação Logic serão implantados na região que selecionar. Se a sua App Lógica tiver algum recursos personalizados armazenados numa região específica, certifique-se de que a região é selecionada aqui. A melhor maneira de o fazer é implementar totalmente a sua Aplicação Lógica localmente na sua subscrição Azure no portal e verificar se funciona corretamente antes de fazer esta seleção.

- **Max simultâneo test drives** (necessário) – Dete o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Estes test drives já estão implantados, permitindo aos clientes acederem instantaneamente sem esperar por uma implementação.

- **Duração** da unidade de teste (necessária) – Introduza o tempo de permanência da Test Drive, em # de horas. O test drive termina automaticamente após o fim deste período de tempo.

- Nome de grupo de **recursos Azure** (necessário) – Introduza o nome do grupo de [recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) onde o seu test drive de Aplicação Lógica é guardado.

- Nome da **aplicação azure logic** (necessário) – Introduza o nome da aplicação Logic que atribui o test drive ao utilizador. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

- **Deprovisionando** o nome da aplicação lógica (necessária) – Introduza o nome da aplicação Logic que desprovisiona o test drive assim que o cliente esteja terminado. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para unidades de teste Power BI

Os produtos que queiram demonstrar um visual interativo do Power BI podem usar um link incorporado para partilhar um dashboard personalizado como seu test drive, não sendo necessária nenhuma configuração técnica adicional. Saiba mais sobre a configuração de aplicações de modelo[Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Detalhes da subscrição de implementação

Para permitir que a Microsoft implemente o test drive em seu nome, crie e forneça uma subscrição Azure separada e única (não necessária para unidades de teste Power BI).

- Id de **subscrição Azure** (necessário para o Gestor de Recursos Azure e aplicações Lógica) – Introduza o ID de subscrição para conceder acesso aos serviços da sua conta Azure para relatórios de utilização de recursos e faturação. Recomendamos que considere [criar uma subscrição Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar para test drives se ainda não tiver uma. Pode encontrar o seu ID de subscrição Azure iniciando sessão no [portal Azure](https://portal.azure.com/) e navegando no separador **Subscrições** do menu do lado esquerdo. A seleção do separador irá exibir o seu ID de subscrição (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID de inquilino azure AD** (obrigatório) – Insira o seu Id de [inquilino](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu esquerdo, selecione **Properties,** procure o número de ID do **Diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar o ID do inquilino da sua organização usando o seu url de nome de domínio em – [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- Nome de **inquilino Azure AD** (necessário para Dinâmico 365) – Introduza o nome de Diretório Ativo Azure (AD). Para encontrar este nome, inscreva-se no [portal Azure,](https://portal.azure.com/)no canto superior direito o seu nome de inquilino será listado sob o nome da sua conta.

- ID da **aplicação Azure AD** (obrigatório) – Introduza o id da [aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu esquerdo, selecione **registos**de Aplicações, em seguida, procure o número de ID de **aplicação** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- Segredo de cliente de cliente da **aplicação Azure AD** (necessário) – Insira o segredo do [cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)da aplicação Azure AD . Para encontrar este valor, inscreva-se no [portal Azure.](https://portal.azure.com/) Selecione o separador **Diretório Ativo Azure** no menu esquerdo, selecione **as inscrições**da App e, em seguida, selecione a sua aplicação de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo do cliente,** introduza uma descrição, selecione **Never** under **Expires,** em seguida, escolha **Adicionar**. Certifique-se de copiar o valor. Não navegue para longe da página antes de copiar o valor ou não terá acesso ao valor.)

### <a name="test-drive-marketplace-listings"></a>Listas de mercado de test drive

A opção **de listagem marketplace** encontrada no separador test **drive** apresenta os idiomas em que a sua unidade de teste está disponível. Atualmente, **o inglês (Estados Unidos)** é o único local disponível. Selecione o nome do idioma para introduzir informações que descrevam a experiência de test drive.

- **Descrição** (necessária) – Descreva o seu test drive, o que será demonstrado, objetivos para o utilizador experimentar, funcionalidades a explorar e qualquer informação relevante para ajudar o utilizador a determinar se adquire a sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informação de acesso** (necessária para o Gestor de Recursos Azure e unidades de teste lógica) – Explique o que um cliente precisa de saber para aceder e utilizar este test drive. Caminhe por um cenário de utilização da sua oferta e exatamente o que o cliente deve saber para aceder a funcionalidades durante todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do Utilizador** (obrigatório) – Um walk-through aprofundado da sua experiência de test drive. O Manual do Utilizador deve cobrir exatamente o que pretende que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer questões que possam ter. O ficheiro deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.

- **Vídeos** (opcional) – Faça upload de vídeos para um site de hospedagem externo referenciando o link e a imagem de miniatura (533 x 324 pixels) aqui. Isto permite ao cliente visualizar um walk-through de informação para ajudá-lo a entender melhor o test drive, incluindo como usar com sucesso as funcionalidades da sua oferta e entender cenários que realçam os seus benefícios.
  - **Nome** (obrigatório)
  - **Endereço** (obrigatório; YouTube ou Vimeo apenas)
  - Imagem **de miniatura** (ficheiro de imagem deve estar em formato PNG e 533 x 324 pixels)

Selecione **guardar rascunho** antes de continuar.

## <a name="supplemental-content"></a>Conteúdo suplementar

Esta página permite-lhe fornecer informações adicionais sobre a sua oferta para nos ajudar a validar a sua oferta. Esta informação não é mostrada aos clientes nem publicada no mercado.

### <a name="key-usage-scenario"></a>Cenário de utilização chave

Faça upload de um ficheiro PDF que lista os principais cenários de utilização da sua oferta. Todos os cenários podem ser verificados pela nossa equipa de validação antes de aprovarmos a sua oferta para o mercado.

Selecione **guardar rascunho** antes de continuar.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Depois de ter concluído todas as secções necessárias da oferta, selecione **Review e publique** no canto superior direito do portal.

Se for a primeira vez que publica esta oferta, pode:

- Consulte o estado de conclusão de cada secção da oferta.
    - **Não começou** - A secção não foi tocada e deve ser concluída.
    - **Incompleto** - A secção tem erros que precisam de ser corrigidos ou exigemais informações a fornecer. Volte para a secção(s) e atualize-a.
    - **Completo** - A secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado total antes de poder submeter a oferta.
- Na secção **Notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta para publicação selecionando **Enviar**. Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta está disponível para que você reveja e aprove. Volte ao Partner Center e selecione **Go-live** para a oferta de publicar ao público (ou se uma oferta privada, para o público privado).

## <a name="next-step"></a>Passo seguinte

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

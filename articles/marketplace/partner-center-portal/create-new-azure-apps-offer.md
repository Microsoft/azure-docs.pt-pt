---
title: Criar uma nova oferta de aplicativos do Azure no Marketplace comercial
description: Como criar uma nova oferta de aplicativos do Azure para listagem ou venda no Azure Marketplace, AppSource ou por meio do programa CSP (provedor de soluções na nuvem) usando o portal do Marketplace comercial no Microsoft Partner Center.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 69d7bc81f5adc4797be86f946fdb656b4b6cc682
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760909"
---
# <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicações do Azure

As etapas para publicar uma oferta de aplicativo do Azure no Marketplace comercial são descritas aqui.

## <a name="azure-application-offer-type"></a>Tipo de oferta de aplicativo do Azure

Este tópico descreve os conceitos básicos sobre as ofertas de aplicativos do Azure.  Você deve estar familiarizado com esses conceitos antes de iniciar o processo de publicação de uma nova oferta de aplicativo do Azure no Marketplace.

### <a name="publishing-overview"></a>Visão geral da publicação

Os modelos de solução de criação de vídeo [e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) são uma introdução ao tipo de oferta de aplicativo do Azure:

* Quais tipos de oferta estão disponíveis;
* Quais ativos técnicos são necessários;
* Como criar um modelo de Azure Resource Manager;
* Desenvolvendo e testando a interface do usuário do aplicativo;
* Como publicar a oferta de aplicativo;
* O processo de revisão do aplicativo.

### <a name="types-of-azure-application-plans"></a>Tipos de planos de aplicativo do Azure

Há dois tipos de planos de aplicativo do Azure, aplicativos gerenciados e modelos de solução.

* O **modelo de solução** é uma das principais maneiras de publicar uma solução no Marketplace. Esse tipo de plano é usado quando sua solução requer implantação adicional e automação de configuração além de uma única máquina virtual (VM).  Com um modelo de solução, você pode automatizar o fornecimento de mais de um recurso, incluindo VMs, redes e recursos de armazenamento para fornecer soluções complexas de IaaS.  Para obter mais informações sobre como criar modelos de solução, consulte a documentação do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) .

* O **aplicativo gerenciado** é semelhante aos modelos de solução, com uma diferença importante. Numa aplicação gerida, os recursos são implementados num grupo de recursos que é gerido pelo editor da aplicação. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos. Como editor, tem de especificar o custo do suporte contínuo da solução. Use aplicativos gerenciados para criar e entregar facilmente aplicativos completos e totalmente gerenciados aos seus clientes.  Para obter mais informações sobre as vantagens e os tipos de aplicativos gerenciados, consulte [visão geral dos aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/overview).

Todos os aplicativos do Azure incluem pelo menos dois arquivos na pasta raiz de um arquivo de `.zip`:

* Um arquivo de modelo do Resource Manager chamado [MainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Esse é o modelo que define os recursos a serem implantados na assinatura do Azure do cliente.  Para obter exemplos de modelos do Resource Manager, consulte a [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) ou o repositório de [modelos do github: Azure Resource Manager QuickStart](https://github.com/azure/azure-quickstart-templates) correspondente.

* Uma definição de interface do usuário para a experiência de criação do aplicativo do Azure chamada [createUiDefinition. JSON](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Na interface de utilizador, especifique os elementos que permitem aos consumidores fornecer valores de parâmetros.

Todas as novas ofertas de aplicativos do Azure devem incluir um [GUID de atribuição de uso do cliente de parceiro do Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution).

### <a name="before-you-begin"></a>Antes de começar

Examine a seguinte documentação do aplicativo do Azure, que fornece guias de início rápido, tutoriais e exemplos.

* [Entender modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Guias de Início Rápido:

    * [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/)
    * [Modelos de início rápido do Azure para GitHub](https://github.com/azure/azure-quickstart-templates)
    * [Publicar definição de aplicativo](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [Implantar aplicativo do catálogo de serviços](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Tutoriais:

    * [Criar arquivos de definição](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publicar a aplicação do mercado](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Amostras

    * [CLI do Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [O Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Soluções de aplicativos gerenciados](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Conceitos básicos no conhecimento técnico

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma Azure e das tecnologias usadas para criar a oferta.

Sua equipe de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

* Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/).
* Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/).
* Conhecimento de trabalho de [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage#storage)e [rede do Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Conhecimento de trabalho do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Conhecimento de trabalho do [JSON](https://www.json.org/).

### <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerenciar seu aplicativo do Azure:

* [O Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [CLI do Azure](https://docs.microsoft.com/cli/azure)

É recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) com as seguintes extensões:
    * Extensão: [ferramentas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extensão: [melhorar JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Você pode examinar as ferramentas disponíveis na página [ferramentas para desenvolvedores do Azure](https://azure.microsoft.com/tools/) .  Além disso, se você estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicações do Azure

Para poder criar uma oferta de aplicativo do Azure, você deve primeiro [criar uma conta do Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) e abrir o [painel do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), com a guia **visão geral** selecionada.

>[!Note]
>Depois que uma oferta for publicada, as edições feitas na oferta feita no Partner Center serão atualizadas somente no sistema e as vitrines após a nova publicação.  Certifique-se de enviar a oferta para publicação depois que as alterações forem feitas.

### <a name="create-a-new-offer"></a>Criar uma nova oferta

Selecione o botão **+ nova oferta** e, em seguida, selecione o item de menu **aplicativo Azure** . A caixa de diálogo **nova oferta** será exibida.

### <a name="offer-id-and-alias"></a>ID da oferta e alias

* **ID da oferta**: um identificador exclusivo para cada oferta em sua conta. Essa ID será visível para os clientes no endereço URL para a oferta do Marketplace e os modelos de Azure Resource Manager (se aplicável). <br> <br> Sua ID de oferta deve conter caracteres alfanuméricos minúsculos (incluindo hifens e sublinhados, mas sem espaço em branco). Ele é limitado a 50 caracteres e não pode ser alterado depois que você seleciona criar. <br> <br> Por exemplo, se você inserir `test-offer-1` aqui, a URL da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`. 

* **Alias da oferta**: o nome usado para fazer referência à oferta no Partner Center. Esse nome não será usado no Marketplace e será diferente do nome da oferta e de outros valores que serão mostrados aos clientes. Esse valor não pode ser alterado depois que você seleciona **criar**.

Depois de inserir sua **ID de oferta** e **alias de oferta**, selecione **criar**. Em seguida, você poderá trabalhar em todas as outras partes da sua oferta.

## <a name="offer-setup"></a>Instalação da oferta

A página **instalação da oferta** solicita as seguintes informações. Certifique-se de selecionar **salvar** depois de concluir esses campos.

### <a name="test-drive"></a>Versão de teste

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, fornecendo a eles a opção "tentar antes de comprar", resultando em uma maior conversão e na geração de clientes potenciais altamente qualificados. [Saiba mais sobre test drives.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para habilitar uma test drive, marque a caixa **habilitar um test drive** . Em seguida, você precisará configurar um ambiente de demonstração no [test drive de configuração técnica](#types-of-azure-application-plans) configure para permitir que os clientes tentem sua oferta por um período de tempo fixo. 

>[!Note]
>Como todos os aplicativos do Azure são implementados usando um modelo de Azure Resource Manager, o único tipo de test drive que pode ser configurado para uma Aplicativo Azure é uma [Test Drive baseada em Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

#### <a name="additional-test-drive-resources"></a>Recursos de test drive adicionais

- [Práticas recomendadas técnicas do Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Práticas recomendadas de marketing do Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral do Test Drive One pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Conectar gerenciamento de leads

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, consulte [visão geral do gerenciamento de Lead](./commercial-marketplace-get-customer-leads.md).

Lembre-se de **salvar** antes de passar para a próxima seção!

## <a name="properties"></a>Propriedades

A página **Propriedades** permite que você defina as categorias e os setores usados para agrupar sua oferta no Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta. Selecione **salvar** depois de concluir esta página.

### <a name="category"></a>Categoria

Selecione no mínimo um e no máximo três categorias, que serão usadas para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Lembre-se de chamar como sua oferta dá suporte a essas categorias na descrição da oferta. 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato padrão para o Marketplace comercial da Microsoft

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Listagem de ofertas

A página de listagem da oferta exibe os idiomas nos quais sua oferta será listada. Atualmente, o **Inglês (Estados Unidos)** é a única opção disponível.

Você precisará definir detalhes do Marketplace (nome da oferta, descrição, imagens, etc.) para cada idioma/mercado. Selecione o idioma/nome do mercado para fornecer essas informações.

> [!NOTE]
> Oferecer conteúdo de listagem (como a descrição, documentos, screenshots, termos de uso, etc.) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação só está disponível em [língua não inglesa]." Também é aceitável fornecer uma URL de *Link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem da oferta.

### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido previamente com o texto inserido para o **alias de oferta** quando você criou a oferta, mas pode alterar esse valor. Esse nome pode ser marcado (e você pode incluir os símbolos de marca registrada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

### <a name="summary"></a>Resumo

Forneça uma breve descrição da sua oferta (até 100 caracteres), que pode ser usada nos resultados da pesquisa do Marketplace.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta (até 256 caracteres). A descrição pode ser usada nos resultados da pesquisa do Marketplace.

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

### <a name="search-keywords"></a>Pesquisar palavras-chave

Opcionalmente, você pode inserir até três palavras-chave de pesquisa para ajudar os clientes a localizar sua oferta no Marketplace. Para obter melhores resultados, tente usar essas palavras-chave em sua descrição também.

### <a name="support-urls"></a>URLs de suporte

Esta seção permite que você forneça links para ajudar os clientes a entenderem mais sobre sua oferta.

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Insira a URL para a política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e para fornecer uma política de privacidade válida.

#### <a name="useful-links"></a>Ligações úteis

Forneça documentos complementares opcionais online sobre sua solução.  Adicione links úteis adicionais clicando em **+ Adicionar um link**.

### <a name="contacts"></a>Contactos

Nesta seção, você deve fornecer o nome, o email e o número de telefone para um **contato de suporte** e um contato de **engenharia**. Essas informações não são mostradas aos clientes, mas estarão disponíveis para a Microsoft e podem ser fornecidas aos parceiros CSP.

Na seção **contato de suporte** , você também deve fornecer a **URL de suporte** em que os parceiros do CSP podem encontrar suporte para sua oferta.

### <a name="marketplace-images"></a>Imagens do Marketplace

Nesta seção, você pode fornecer logotipos e imagens que serão usados ao mostrar sua oferta ao cliente. Todas as imagens devem estar no formato. png.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça o logotipo de sua oferta em três tamanhos: **pequeno (48 x 48)** , **médio (90 x 90)** e **grande (216 x 216)** .

#### <a name="hero"></a>Herói

A imagem do Hero é opcional. Se você fornecer um, ele deve medir 815 x 290 pixels.

#### <a name="screenshots"></a>Capturas de Ecrã

Adicione capturas de tela que mostram como sua oferta funciona. Você pode adicionar até cinco capturas de tela. Todas as capturas de tela devem ser 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Opcionalmente, você pode adicionar até cinco vídeos que demonstram sua oferta. Esses vídeos devem ser hospedados no YouTube e/ou no Vimeo. Para cada um, insira o nome do vídeo, sua URL e uma imagem em miniatura do vídeo (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

- [Práticas recomendadas para listagens de ofertas do Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Pré-visualização

A guia **Visualização** permite que você defina um **público-alvo** limitado para validar sua oferta antes de publicar sua oferta em tempo real para o público mais amplo do Marketplace.

> [!IMPORTANT]
> Você deve selecionar **entrar em ativação** antes que sua oferta seja publicada em tempo real para o público público do Marketplace depois de verificar sua oferta em versão prévia.

Seu público-alvo de visualização é identificado por GUIDs de ID de assinatura do Azure, emparelhados com uma descrição opcional para cada um.  Nenhum desses campos é visível para os clientes.

Adicione até 10 IDs de assinatura do Azure manualmente ou até 100 se estiver carregando um arquivo CSV.  Ao adicionar essas assinaturas, você está definindo um público que terá permissão para visualizar o acesso à sua oferta antes que ela seja totalmente publicada.  Se sua oferta já estiver ativa, você ainda poderá definir um público de visualização para testar quaisquer alterações ou atualizações na sua oferta.

>[!Note]
>O público de visualização difere de um público privado. Um público de visualização tem permissão de acesso à sua oferta *antes* de ser publicado em tempo real nos Marketplaces. Você também pode optar por criar um plano e disponibilizá-lo somente para um público privado (usando a guia disponibilidade do plano).  Seu público-alvo poderá ver e validar todos os planos, incluindo planos que estarão disponíveis somente para um público privado depois que sua oferta for totalmente publicada no Marketplace.

## <a name="plan-overview"></a>Visão geral do plano

A guia **visão geral do plano** permite que você forneça opções de plano diferentes na mesma oferta. Esses planos (chamados de SKUs no Portal do Cloud Partner) podem diferir em termos de tipo de plano (modelo de solução versus aplicativo gerenciado), monetização ou público.  Configure pelo menos um plano para listar sua oferta no Marketplace.

Depois de criado, você verá seus nomes de plano, IDs, tipo de plano, disponibilidade (pública ou privada), status de publicação atual e quaisquer ações disponíveis nessa guia.

As **ações** disponíveis na **visão geral do plano** variam de acordo com o status atual do seu plano e podem incluir:

* Se o status do plano for **rascunho** – excluir rascunho.
* Se o status do plano for **Live** – parar vender plano ou sincronizar público privado.

### <a name="create-new-plan"></a>Criar novo plano

***ID do plano*** -crie uma ID de plano exclusiva para cada plano nesta oferta. Essa ID será visível para os clientes na URL do produto.  Use apenas caracteres minúsculos, alfanuméricos, traços ou sublinhados. São permitidos no máximo 50 caracteres para essa ID de plano. Essa ID não pode ser modificada após a seleção de Create.

***Nome do plano*** -os clientes verão esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome de oferta exclusivo para cada plano nesta oferta. O nome do plano é usado para diferenciar os planos de software que podem fazer parte da mesma oferta (por exemplo, Nome da oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configuração do plano

A guia **configuração do plano** permite que você defina a configuração de alto nível para o tipo de plano, se ele reutiliza pacotes de outro plano e em quais nuvens o plano deve estar disponível.  Suas respostas nessa guia afetarão quais campos são exibidos em outras guias para o mesmo plano.

#### <a name="plan-type"></a>Tipo de plano

Conforme descrito nos [tipos de planos de aplicativo do Azure](#types-of-azure-application-plans), selecione se seu plano conterá um modelo de solução ou um aplicativo gerenciado.

#### <a name="this-plan-reuses-packages"></a>Este plano reutiliza pacotes

Se você tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, você poderá selecionar **este plano reutiliza pacotes de outro plano**.  Ao selecionar essa opção, você poderá selecionar um dos outros planos do mesmo tipo para esta oferta para reutilizar pacotes do. 

>[!Note]
>Quando você reutiliza pacotes de outro plano, a guia de configuração técnica inteira desaparecerá deste plano.  Os detalhes de configuração técnica do outro plano, incluindo todas as atualizações feitas no futuro, também serão usados para esse plano. <br> <br> Além disso, essa configuração não pode ser alterada depois que este plano é publicado.

#### <a name="cloud-availability"></a>Disponibilidade na nuvem

Esse plano deve ser disponibilizado em pelo menos uma nuvem. 

Selecione a opção **pública do Azure** para tornar sua solução implantável para clientes em todas as regiões públicas do Azure que têm integração com o Marketplace.  Saiba mais sobre a [disponibilidade geográfica](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selecione a opção de **nuvem do Azure governamental** para tornar sua solução implantável na [nuvem do Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), uma nuvem do governo da Comunidade com acesso controlado para clientes dos EUA Federal, estadual, local ou tribal e parceiros qualificados para atender a essas entidades.  Você, como Publicador, é responsável por qualquer controle de conformidade, medidas de segurança e práticas recomendadas para atender a essa comunidade de nuvem.  O Azure governamental usa data centers isolados fisicamente e redes (localizadas apenas nos EUA).  Antes de publicar no [Azure governamental](https://aka.ms/azuregovpublish), a Microsoft recomenda que você teste e valide sua solução no ambiente, pois determinados pontos de extremidade podem ser diferentes. Para preparar e testar sua solução, solicite uma conta de avaliação deste [link](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>Depois que um plano é publicado como disponível em uma nuvem específica, essa nuvem não pode ser removida.

**Certificações de nuvem do Azure governamental**

Essa opção só estará visível se a **nuvem do Azure governamental** estiver selecionada em **disponibilidade da nuvem**.

Os serviços do Azure governamental lidam com os dados que estão sujeitos a determinadas normas e requisitos governamentais, como FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.  Para dar reconhecimento às suas certificações para esses programas, você pode fornecer até 100 links que descrevem suas certificações.  Esses links podem ser links para sua listagem diretamente no programa ou um link para descrições de sua conformidade com eles em seus próprios sites.  Esses links só estarão visíveis para os clientes de nuvem do Azure governamental.

## <a name="plan-listing"></a>Lista de planos

A guia **lista de planos** exibe as informações de listagem específicas do plano que podem ser diferentes entre planos diferentes para a mesma oferta.

### <a name="name"></a>Nome

Preenchido previamente com base em seu nome, você atribuiu seu plano quando o criou.  Esse nome será exibido como o título desse "plano de software" exibido no Marketplace.  Pode conter até 100 caracteres.

### <a name="summary"></a>Resumo

Forneça um breve resumo do seu plano de software.  Pode conter até 100 caracteres.

### <a name="description"></a>Descrição

Essa descrição é uma oportunidade para explicar o que torna este plano de software exclusivo e quaisquer diferenças de outros planos de software dentro de sua oferta. Pode conter até 2.000 caracteres.

Selecione **salvar** depois de concluir esses campos.

## <a name="availability"></a>Disponibilidade

A guia **disponibilidade** é visível apenas para os planos de modelo de solução.  Você pode tornar o plano visível para todos, somente para clientes específicos (um público privado) e se deseja tornar o plano oculto para uso somente por outros modelos de solução ou aplicativos gerenciados.

### <a name="plan-audience"></a>Planejar público

Você tem a opção de configurar cada plano para ser visível para todos ou apenas para um público específico de sua escolha. Você pode atribuir associação a esse público restrito usando as IDs de assinatura do Azure.

**Privacidade/este é um plano privado** (caixa de seleção opcional)-Marque esta caixa para tornar seu plano privado e visível somente para o público restrito de sua escolha. Depois de publicado como um plano privado, você pode atualizar o público ou optar por disponibilizar o plano para todos. Depois que um plano é publicado como visível para todos, ele deve permanecer visível para todos. (O plano não pode ser configurado como um plano privado novamente).

**Público-alvo restrito (IDs de assinatura do Azure)** – atribua o público que terá acesso a esse plano privado. O acesso é atribuído usando as IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura do Azure atribuída. Um máximo de 10 IDs de assinatura pode ser adicionado ou 20.000 IDs de assinatura de clientes se importar um arquivo de planilha. csv.  As IDs de assinatura do Azure são representadas como GUIDs e as letras devem ser minúsculas.

>[!Note]
>O público privado (ou público restrito) difere do público de visualização que você definiu na guia [**Visualização**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Um público de visualização tem permissão de acesso à sua oferta *antes* que a oferta seja publicada ao vivo no Marketplace. Embora a designação de público privado se aplique apenas a um plano específico, o público-alvo da visualização pode exibir todos os planos (privados ou não) para fins de validação.

### <a name="hide-plan"></a>Ocultar plano

Se o seu modelo de solução pretende ser implantado apenas indiretamente quando for referenciado em outro modelo de solução ou aplicativo gerenciado, marque esta caixa para publicar o modelo de solução, mas ocultá-lo de clientes pesquisando e navegando diretamente.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

A guia **preços e disponibilidade** é visível somente para planos de aplicativo gerenciados.  Você pode configurar os mercados nos quais esse plano estará disponível, o preço por mês do gerenciamento da solução e se deseja tornar o plano visível para todos ou apenas para clientes específicos (um público privado).

### <a name="markets"></a>Mercados

Cada plano deve estar disponível em pelo menos um mercado. Marque a caixa de seleção de qualquer local de mercado onde você gostaria de disponibilizar esse plano. Uma caixa de pesquisa e um botão para selecionar os países "impostos remetidos", no qual a Microsoft remete as vendas e o imposto sobre o uso em seu nome, estão incluídas para ajudar.

Se você já tiver definido preços para seu plano em dólares de Estados Unidos (USD) e adicionar outro local de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Sempre examine o preço de cada mercado antes de publicar. Os preços podem ser revisados usando o link "exportar preços (xlsx)" depois de salvar as alterações.

### <a name="pricing"></a>Preços

Forneça o preço por mês para este plano.  Esse preço é além de qualquer infraestrutura do Azure ou custos de software pago conforme o uso incorridos pelos recursos implantados por essa solução.

Os preços definidos na moeda local (USD = Estados Unidos dólar) são convertidos na moeda local de todos os mercados selecionados usando as tarifas de câmbio atuais disponíveis durante a instalação. Valide esses preços antes de publicar exportando a planilha de preços e revisando o preço em cada mercado. Se você quiser definir preços personalizados em um mercado individual, modifique e importe a planilha de preços. 

>[!Note]
>Você deve primeiro salvar as alterações de preços para habilitar a exportação de dados de preços.

Examine seus preços cuidadosamente antes de publicar, pois há algumas restrições sobre o que pode ser alterado depois que um plano é publicado.  

>[!Note]
>Depois que o preço de um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente.

### <a name="plan-audience"></a>Planejar público

Você tem a opção de configurar cada plano para ser visível para todos ou apenas para um público específico de sua escolha. Você pode atribuir associação a esse público restrito usando as IDs de assinatura do Azure.

**Privacidade/este é um plano privado** (caixa de seleção opcional)-Marque esta caixa para tornar seu plano privado e visível somente para o público restrito de sua escolha. Depois de publicado como um plano privado, você pode atualizar o público ou optar por disponibilizar o plano para todos. Depois que um plano é publicado como visível para todos, ele deve permanecer visível para todos. (O plano não pode ser configurado como um plano privado novamente).

**Público-alvo restrito (IDs de assinatura do Azure)** – atribua o público que terá acesso a esse plano privado. O acesso é atribuído usando as IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura do Azure atribuída. Um máximo de 10 IDs de assinatura pode ser adicionado ou 20.000 IDs de assinatura de clientes se importar um arquivo de planilha. csv.  As IDs de assinatura do Azure são representadas como GUIDs e as letras devem ser minúsculas.

>[!Note]
>O público privado (ou público restrito) difere do público de visualização que você definiu na guia [**Visualização**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Um público de visualização tem permissão de acesso à sua oferta *antes* que a oferta seja publicada ao vivo no Marketplace. Embora a designação de público privado se aplique apenas a um plano específico, o público-alvo da visualização pode exibir todos os planos (privados ou não) para fins de validação.

## <a name="technical-configuration"></a>Configuração técnica 

A guia **configuração técnica** permite que você carregue o pacote de implantação que permitirá que os clientes implantem seu plano.

>[!Note]
>Essa guia não estará visível se você tiver configurado este plano para reutilizar pacotes de outro plano na guia **configuração do plano** .

### <a name="package-details"></a>Detalhes do pacote

Os **detalhes do pacote** subtab permitem que você edite a versão de rascunho de sua configuração técnica.

***Versão*** – atribua a versão atual da configuração técnica.  Aumente essa versão sempre que publicar uma alteração nesta página. A versão deve estar no formato `{integer}.{integer}.{integer}`.

***Arquivo de pacote*** (`.zip`)-Este pacote contém todos os arquivos de modelo necessários para esse plano, bem como quaisquer recursos adicionais, empacotados como um arquivo de `.zip`.

Todos os pacotes de plano de aplicativo do Azure devem incluir esses dois arquivos na pasta raiz de um arquivo de `.zip`:

* Um arquivo de modelo do Resource Manager chamado [MainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Este modelo automatiza a implantação de recursos na assinatura do Azure dos clientes.  Para obter exemplos de modelos do Resource Manager, consulte a [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) ou o repositório de [modelos do github: Azure Resource Manager QuickStart](https://github.com/azure/azure-quickstart-templates) correspondente.

* Uma definição de interface do usuário para a experiência de criação do aplicativo do Azure chamada [createUiDefinition. JSON](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Todas as novas ofertas de aplicativos do Azure também devem incluir um GUID de [atribuição de uso do cliente de parceiro do Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) .

### <a name="previously-published-packages"></a>Pacotes publicados anteriormente 

Os **pacotes publicados anteriormente** subtab permitem que você exiba todas as versões publicadas de sua configuração técnica.

## <a name="technical-configuration-managed-application-plans-only"></a>Configuração técnica (somente planos de aplicativo gerenciados)

Os planos de aplicativos gerenciados têm complexidade adicional na guia **configuração técnica** além dos campos de arquivo de **versão** e **pacote** descritos acima. 

### <a name="enable-just-in-time-jit-access"></a>Habilitar o acesso JIT (just-in-time)

Selecione esta opção para habilitar o acesso JIT (just-in-time) para este plano.  O acesso JIT permite que você solicite acesso elevado a recursos de um aplicativo gerenciado para solução de problemas ou manutenção. Você sempre tem acesso somente leitura aos recursos, mas, para um período de tempo específico, você pode ter mais acesso.  Para obter mais informações, consulte [habilitar e solicitar o acesso just-in-time para aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Para exigir que os consumidores do seu aplicativo gerenciado conceda acesso permanente à sua conta, deixe essa opção desmarcada.

>[!Note]
>Certifique-se de atualizar o arquivo de `createUiDefinition.json` para dar suporte a esse recurso.  

### <a name="deployment-mode"></a>Modo de implantação

Selecione se deseja configurar o **modo de implantação** **completo** ou incremental ao implantar este plano: 

* No **modo completo**, uma reimplantação do aplicativo pelo cliente resultará na remoção de recursos no grupo de recursos gerenciados se os recursos não estiverem definidos no `mainTemplate.json`. 
* No **modo incremental**, uma reimplantação do aplicativo deixa os recursos existentes inalterados.

Para saber mais sobre modos de implantação, consulte [Azure Resource Manager modos de implantação](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>URL do ponto de extremidade de notificação

Especifique um ponto de extremidade de webhook HTTPS para receber notificações sobre todas as operações CRUD em instâncias de aplicativo gerenciado desta versão do plano.

### <a name="customize-allowed-customer-actions"></a>Personalizar ações de clientes permitidas

Selecione esta opção para especificar quais ações os clientes podem executar nos recursos gerenciados, além das ações "`*/read`" que estão disponíveis por padrão. 

Liste as ações adicionais que você gostaria de permitir que o cliente execute aqui, separado por ponto-e-vírgula.  Para obter mais informações, consulte [noções básicas sobre atribuições de negação para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Para as ações disponíveis, consulte [Azure Resource Manager operações do provedor de recursos](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Por exemplo, para permitir que os consumidores reiniciem máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` às ações permitidas.

### <a name="global-azure--azure-government-cloud"></a>Nuvem global do Azure/Azure governamental

Indique quem deve ter acesso de gerenciamento a esse aplicativo gerenciado em cada nuvem com suporte.  Usuários, grupos ou aplicativos para os quais você deseja receber permissão para o grupo de recursos gerenciados são identificados usando as identidades do AAD (Azure Active Directory).

***ID de locatário Azure Active Directory*** -a ID de locatário do AAD (também conhecida como ID de diretório) que contém as identidades dos usuários, grupos ou aplicativos aos quais você deseja conceder permissões.  Você pode encontrar sua ID de locatário do AAD na portal do Azure, em [Propriedades para Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Autorizações*** – adicione a ID de objeto Azure Active Directory do usuário, grupo ou aplicativo para o qual você deseja receber permissão para o grupo de recursos gerenciado. Identifique o usuário por sua ID de entidade de segurança, que pode ser encontrada na [folha Azure Active Directory usuários no portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Para cada entidade de segurança, selecione uma das funções internas do Azure AD na lista (proprietário ou colaborador). A função selecionada descreverá as permissões que a entidade de segurança terá sobre os recursos na assinatura do cliente. Para obter mais informações, veja as [Funções incorporadas dos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Para obter mais informações sobre o RBAC (controle de acesso baseado em função), consulte Introdução [ao RBAC no portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Embora você possa adicionar até 100 autorizações por nuvem, geralmente é mais fácil criar um grupo de usuários Active Directory e especificar sua ID na "ID da entidade de segurança".  Isso permitirá que você adicione mais usuários ao grupo de gerenciamento depois que o plano for implantado e reduza a necessidade de atualizar o plano apenas para adicionar mais autorizações.

### <a name="policy-settings"></a>Definições de política

Aplique [políticas do Azure](https://docs.microsoft.com/azure/governance/policy/overview) ao seu aplicativo gerenciado para especificar os requisitos de conformidade para a solução implantada.  Para definições de política e o formato de valores de parâmetros, consulte [Exemplos de Política do Azure](https://docs.microsoft.com/azure/governance/policy/samples/index).  Você pode configurar um máximo de cinco políticas e apenas uma instância de cada opção de políticas.  Algumas políticas exigem parâmetros adicionais.  O SKU Standard é necessário para as políticas de auditoria.  O nome da política está limitado a 50 caracteres.

## <a name="co-sell"></a>Co-Venda

Fornecer informações sobre a guia de televenda é totalmente opcional para publicar sua oferta. É necessário atingir o status pronto para venda de covenda pronta e de IP. As informações fornecidas serão usadas pelas equipes de vendas da Microsoft para saber mais sobre sua solução ao avaliar seu ajuste para as necessidades do cliente. Ele não está disponível diretamente aos clientes.

Para obter mais informações sobre como concluir essa guia, consulte [a opção de venda de cooperação no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Versão de teste

A guia **Test Drive** permite que você configure uma demonstração (ou "Test Drive"), que permitirá que os clientes experimentem sua oferta antes de confirmar a compra. Saiba mais no artigo [o que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Se você não quiser mais fornecer um test drive para sua oferta, retorne à página de **instalação da oferta** e desmarque **habilitar Test Drive**.

### <a name="technical-configuration"></a>Configuração técnica

Os aplicativos do Azure usam inerentemente o tipo de test drive Azure Resource Manager.  Consulte [configuração técnica para Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) para obter mais informações.

### <a name="deployment-subscription-details"></a>Detalhes da assinatura da implantação

Para implantar o Test Drive em seu nome, crie e forneça uma assinatura do Azure separada e exclusiva. (Não é necessário para Power BI unidades de teste).

- **ID da assinatura do Azure** (necessária para Azure Resource Manager e aplicativos lógicos): Insira a ID da assinatura para conceder acesso aos serviços de conta do Azure para relatório e cobrança de uso de recursos. Recomendamos que você considere a [criação de uma assinatura do Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) a ser usada para unidades de teste, se você ainda não tiver uma. Você pode encontrar sua ID de assinatura do Azure fazendo logon no [portal do Azure](https://portal.azure.com/) e navegando até a guia **assinaturas** do menu do lado esquerdo. A seleção da guia exibirá sua ID de assinatura (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID do locatário do Azure ad** (obrigatório): Insira sua ID de [locatário](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)do Azure Active Directory (AD). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione * * Propriedades e procure o número de **ID de diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode pesquisar a ID de locatário da sua organização usando sua URL de nome de domínio em: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nome do locatário do Azure ad** (necessário para o Dynamic 365): Insira seu nome de Azure Active Directory (AD). Para localizar esse nome, entre no [portal do Azure](https://portal.azure.com/), no canto superior direito, o nome do locatário será listado em seu nome de conta.

- **ID do aplicativo do Azure ad** (obrigatório): Insira sua ID do [aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)de Azure Active Directory (AD). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **registros de aplicativo**e procure o número de **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **Segredo do cliente de aplicativo do Azure ad** (obrigatório): Insira o [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)de aplicativo do Azure AD. Para localizar esse valor, entre no [portal do Azure](https://portal.azure.com/). Selecione a guia **Azure Active Directory** no menu à esquerda, selecione **registros de aplicativo**e, em seguida, selecione seu aplicativo Test Drive. Em seguida, **Selecione certificados e segredos**, selecione **novo segredo do cliente**, insira uma descrição, selecione **nunca** em **expirar**e, em seguida, escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para fora da página antes de copiar o valor ou, caso contrário, você não terá acesso ao valor.)

Lembre-se de **salvar** antes de passar para a próxima seção!

### <a name="test-drive-listings-optional"></a>Testar listagens de unidade (opcional)

A opção de **lista de unidades de teste** encontrada na guia **Test Drive** exibe os idiomas (e os mercados) em que o Test Drive está disponível, atualmente em inglês (Estados Unidos) é o único local disponível. Além disso, essa página exibe o status da listagem específica do idioma e a data/hora em que ela foi adicionada. Você precisará definir os detalhes da test drive (descrição, manual do usuário, vídeos, etc.) para cada idioma/mercado.

- **Descrição** (obrigatório): descreva sua Test Drive, o que será demonstrado, os objetivos para o usuário experimentar, os recursos a serem explorados e todas as informações relevantes para ajudar o usuário a determinar se deseja adquirir sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informações de acesso** (necessárias para unidades de teste de Azure Resource Manager e lógica): explique o que um cliente precisa saber para acessar e usar esse Test Drive. Percorra um cenário para usar sua oferta e exatamente o que o cliente deve saber para acessar recursos em todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do usuário** (obrigatório): uma explicação detalhada de sua experiência de Test Drive. O manual do usuário deve abranger exatamente o que você deseja que o cliente tenha de apresentar o test drive e servir como uma referência para quaisquer perguntas que possam ter. O arquivo deve estar no formato PDF e ter o nome (máximo de 255 caracteres) após o carregamento.

- **Vídeos: adicionar vídeos** (opcional): os vídeos podem ser carregados no YouTube ou Vimeo e mencionados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa exibir um passo a passo de informações para ajudá-los a entender melhor os Test Drive, incluindo como usar com êxito os recursos de sua oferta e entender os cenários que destacam seus benefícios.
  - **Nome** (obrigatório)
  - **URL (somente YouTube ou Vimeo)** (obrigatório)
  - **Miniatura (533 x 324 px)** : o arquivo de imagem deve estar no formato png.

Selecione **salvar** depois de concluir esses campos.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para visualização

Depois de concluir todas as seções necessárias da oferta, selecione **publicar** no canto superior direito do Portal. Você será redirecionado para a página **revisar e publicar** . 

Se for a primeira vez que publicar essa oferta, você poderá:

- Consulte o status de conclusão de cada seção da oferta.
    - *Não iniciado* -significa que a seção não foi tocada e precisa ser concluída.
    - *Incompleto* -significa que a seção tem erros que precisam ser corrigidos ou que requer mais informações a serem fornecidas. Volte para a (s) seção (ões) e atualize-a.
    - *Concluir* -significa que a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta devem estar em um estado completo antes que você possa enviar a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de qualquer nota suplementar útil para entender seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Enviaremos um email para que você saiba quando uma versão prévia da oferta está disponível para revisão e aprovação. Retorne ao Partner Center e selecione **Go-Live** para a oferta para publicar sua oferta no público (ou se uma oferta privada, para o público privado).

### <a name="errors-and-review-feedback"></a>Erros e comentários de revisão

A etapa de **validação manual** no processo de publicação representa uma análise extensiva de sua oferta e seus ativos técnicos associados (especialmente o modelo de Azure Resource Manager), os problemas normalmente são apresentados como links de solicitação de pull (PR). Uma explicação de como exibir e responder a esses PRs, consulte [lidando com comentários de revisão](./azure-apps-review-feedback.md).

Se você encontrou erros em uma ou mais das etapas de publicação, você deve corrigi-los e republicar sua oferta.

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

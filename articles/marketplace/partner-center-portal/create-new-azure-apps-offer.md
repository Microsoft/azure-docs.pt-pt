---
title: Criar uma oferta de aplicação Azure - mercado comercial da Microsoft
description: Conheça os passos e considerações para a criação de uma nova oferta de aplicações Azure no portal de marketplace comercial no Partner Center. Pode listar ou vender a sua oferta de aplicação Azure no Azure Marketplace ou através do programa Cloud Solution Provider (CSP).
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 55d544ecf6c96335ca65255e1af695f7757b818c
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699515"
---
# <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicações do Azure

Este artigo explica os passos e considerações para a criação de uma nova oferta de aplicação Azure no mercado comercial. Deve estar familiarizado com estes conceitos antes de criar uma nova oferta de aplicação Azure.

Antes de poder publicar uma nova oferta de aplicação Azure, [crie uma conta de marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) e certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="before-you-begin"></a>Antes de começar

Conceber, construir e testar ofertas de aplicação Azure requer conhecimentos técnicos tanto da plataforma Azure como das tecnologias utilizadas para construir a oferta. A sua equipa de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

* Compreensão básica dos [Serviços Azure.](https://azure.microsoft.com/services/)
* Como [conceber e arquiteto sintetizar aplicações Azure.](https://azure.microsoft.com/solutions/architecture/)
* Conhecimento de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage#storage)e [Networking Azure.](https://azure.microsoft.com/services/?filter=networking#networking)
* Conhecimento de trabalho do Gestor de [Recursos Azure.](https://azure.microsoft.com/features/resource-manager/)
* Conhecimento de trabalho da [JSON.](https://www.json.org/)

### <a name="technical-documentation-and-resources"></a>Documentação técnica e recursos

Reveja os seguintes recursos enquanto prepara a sua oferta de aplicação Azure para o mercado comercial.

* [Compreender os modelos de gestor de recursos azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

* Guias de Início Rápido:

    * [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/)
    * [Modelos GitHub Azure Quickstart](https://github.com/azure/azure-quickstart-templates)
    * [Publicar a definição da aplicação](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Implementar a aplicação do catálogo de serviços](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Tutoriais:

    * [Criar ficheiros de definição](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publicar a aplicação do marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Amostras:

    * [CLI do Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Soluções de aplicação gerida](https://docs.microsoft.com/azure/managed-applications/sample-projects)

Os modelos de solução de construção de vídeo [e aplicações geridas para o Mercado Azure](https://channel9.msdn.com/Events/Build/2018/BRK3603) dá uma introdução abrangente ao tipo de oferta de aplicação Azure:

* Que tipos de oferta estão disponíveis;
* Que ativos técnicos são necessários;
* Como autoria de um modelo de Gestor de Recursos Azure;
* Desenvolver e testar a aplicação UI;
* Como publicar a oferta da aplicação;
* O processo de revisão da aplicação.

### <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de scripts para ajudar a gerir a sua aplicação Azure:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [CLI do Azure](https://docs.microsoft.com/cli/azure)

Recomendamos adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Explorador do Storage do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Código de Estúdio Visual](https://code.visualstudio.com/) com as seguintes extensões:
    * Extensão: Ferramentas de [Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extensão: [Embelezar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Pode rever as ferramentas disponíveis na página Ferramentas de [Desenvolvimento Do Azure.](https://azure.microsoft.com/tools/) Também se estiver a usar o Visual Studio, o [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="types-of-azure-application-plans"></a>Tipos de planos de aplicação Azure

Existem dois tipos de planos de aplicação Azure: modelos de soluções e aplicações geridas.

* O modelo de **solução** é uma das principais formas de publicar uma solução no Mercado. Utilize este tipo de plano quando a sua solução necessitar de automatização adicional de implementação e configuração para além de uma única máquina virtual (VM). Com um modelo de solução, pode automatizar o fornecimento de mais de um recurso, incluindo VMs, networking e recursos de armazenamento para fornecer soluções iaaS complexas.  Para obter mais informações sobre modelos de solução de construção, consulte [O Gestor de Recursos Do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

* **A aplicação gerida** é semelhante aos modelos de solução, com uma diferença fundamental. Numa aplicação gerida, os recursos são implementados num grupo de recursos que é gerido pelo editor da aplicação. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos. Como editor, tem de especificar o custo do suporte contínuo da solução. Utilize aplicações geridas para construir e entregar aplicações chave na mão totalmente geridas aos seus clientes.  Para obter mais informações sobre as vantagens e tipos de aplicações geridas, consulte a visão geral das [aplicações geridas pelo Azure.](https://docs.microsoft.com/azure/managed-applications/overview)

## <a name="technical-requirements"></a>Requisitos técnicos

Todas as aplicações do Azure incluem pelo menos dois ficheiros na pasta raiz de um `.zip` arquivo:

* Um ficheiro de modelo de gestor de recursos chamado [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Este modelo define os recursos para implantar na subscrição do Azure do cliente.  Por exemplo, nos modelos de Gestor de Recursos, consulte a [galeria de modelos De Quickstart Azure](https://azure.microsoft.com/resources/templates/) ou o [correspondente GitHub: Modelos quickstart do Gestor](https://github.com/azure/azure-quickstart-templates) de Recursos Azure repo.

* Uma definição de interface de utilizador para a experiência de criação de aplicações Azure chamada [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Na interface de utilizador, especifique os elementos que permitem aos consumidores fornecer valores de parâmetros.

Todas as novas ofertas de aplicações Azure devem incluir uma atribuição de uso de [cliente parceiro Azure GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution). 

Para conhecer os requisitos de publicação de cada plano de aplicação, consulte o [modelo Solution oferecer requisitos](../marketplace-solution-templates.md) de publicação e oferecer requisitos de publicação de oferta de [aplicações geridas.](../marketplace-managed-apps.md)

## <a name="create-a-new-offer"></a>Criar uma nova oferta

>[!NOTE]
>Depois de publicada uma oferta, as edificações que lhe for emparceiras não aparecerão nas montras até que volte a publicar a oferta. Certifique-se de que republique sempre a sua oferta depois de fazer alterações.

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. No menu esquerdo, selecione **Commercial Marketplace**  >  **Overview**.

1. Na página 'Visão Geral', selecione **+ Nova oferta**  >  **Aplicação Azure**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-azure-app.png)

1. Na página **new offer,** insira um **ID da Oferta**. Este é um identificador único para cada oferta na sua conta.

     * Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos do Gestor de Recursos Azure, se aplicável.
     * Utilize apenas letras minúsculas e números. Pode incluir hífenes e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir o **test-offer-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
     * O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

1. Insira um **pseudónimo da Oferta.** Este é o nome usado para a oferta no Partner Center.

     * Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
     * O pseudónimo da Oferta não pode ser alterado depois de selecionar **Criar**.

1. Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

A página de **configuração da Oferta** é onde pode configurar um test drive e gestão de chumbo para a sua oferta. 

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "tentar antes de comprar", resultando num aumento da conversão e na geração de pistas altamente qualificadas. [Saiba mais sobre test drives](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Para ativar um test drive durante um período de tempo fixo, selecione a caixa de verificação **de test drive.** Para remover o test drive da sua oferta, limpe esta caixa de verificação. Configure o ambiente de acionamento de teste na secção de [configuração técnica](#test-drive-technical-configuration) test drive mais tarde neste tópico.

Para obter informações adicionais, consulte [Test drive a sua oferta no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive). Também pode ler sobre [as melhores práticas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) de test drive e baixar a visão geral das [unidades de teste EM PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (certifique-se de que o seu bloqueador pop-up está desligado)

>[!Note]
>Como todas as aplicações Azure são implementadas usando um modelo de Gestor de Recursos Azure, o único tipo de test drive disponível para uma aplicação Azure é um [test drive baseado em Recursos Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

## <a name="lead-management"></a>Gestão de oportunidades potenciais

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para mais informações, consulte a visão geral da [gestão de chumbo](./commercial-marketplace-get-customer-leads.md).

Selecione **guardar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

A página **Propriedades** é onde define as categorias e indústrias usadas para agrupar a sua oferta no mercado, na sua versão de app e nos contratos legais de apoio à sua oferta.

Selecione pelo menos uma e até três categorias para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Certifique-se de descrever como a sua oferta suporta estas categorias na descrição da oferta.

### <a name="legal"></a>Legal

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Oferta listagem

Esta página é onde gere a cópia e as imagens para a sua oferta de marketplace comercial. 

### <a name="marketplace-details"></a>Detalhes do mercado

> [!NOTE]
> Oferecer conteúdo de listagem (como a descrição, documentos, imagens e termos de utilização) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um URL de *Link Útil* para oferecer conteúdo num idioma diferente do utilizado no conteúdo de listagem de oferta.

#### <a name="name"></a>Name

O nome que introduzir aqui será mostrado aos clientes como título da sua oferta. Este campo é pré-povoado com o texto que inscreveu para **o pseudónimo oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser marcado por marca (e pode incluir símbolos de marca ou direitos de autor). O nome não pode ser mais de 50 caracteres e não pode incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta, até 100 caracteres. Esta descrição pode ser utilizada nos resultados da pesquisa.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta, até 256 caracteres. Esta descrição pode ser utilizada nos resultados da pesquisa.

#### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Pesquisar palavras-chave

Pode inserir opcionalmente até três palavras-chave de pesquisa para ajudar os clientes a encontrar a sua oferta no mercado. Para obter os melhores resultados, use também estas palavras-chave na sua descrição.

#### <a name="privacy-policy-link"></a>Ligação política de privacidade

Insira o URL na política de privacidade da sua organização. É responsável por garantir que a sua aplicação cumpre as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida.

### <a name="useful-links"></a>Ligações úteis

Adicione links a documentos online suplementares opcionais sobre a sua solução selecionando **+ Adicione um link**.

### <a name="contact-information"></a>Informações de contacto

Forneça o nome, e-mail e número de telefone para um contacto de **suporte,** **contacto de engenharia**e contacto do Programa **CSP.** Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft e poderá ser fornecida aos parceiros CSP. Alguns contactos podem requerer informações adicionais.

### <a name="marketplace-media"></a>Meios de comunicação do mercado

Forneça logotipos e imagens para usar com a sua oferta. Todas as imagens devem estar em formato PNG. Imagens desfocadas farão com que a sua submissão seja rejeitada.

>[!Note]
>Se tiver algum problema de upload de ficheiros, certifique-se de que a sua rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos de loja

Forneça ficheiros PNG do logótipo da sua oferta nos seguintes três tamanhos de pixel:

- **Pequeno** (48 x 48)
- **Médio** (90 x 90)
- **Grande** (216 x 216)
- **Ampla** (255 x 115)

Todos os três logótipos são necessários e são usados em diferentes lugares na listagem.

#### <a name="screenshots"></a>Capturas de ecrã

Adicione cinco imagens que mostram como a sua oferta funciona. Cada screenshot deve ter 1280 x 720 pixels em tamanho e em formato PNG. Cada imagem deve incluir uma legenda.

#### <a name="videos"></a>Vídeos

Adicione cinco vídeos que demonstram a sua oferta. Estes devem ser hospedados num serviço de vídeo externo. Introduza o nome de cada vídeo, endereço web e uma imagem PNG miniatura do vídeo em 1280 x 720 pixels.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de mercado

- [Boas práticas para anúncios de oferta de mercado](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Selecione **guardar rascunho** antes de continuar.

## <a name="preview-audience"></a>Pré-visualização do público

No separador Preview, escolha um Público de **Pré-visualização** limitado para validar a sua oferta antes de publicá-la ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Depois de verificar a sua oferta em Preview, selecione **Ir ao vivo** para publicar a sua oferta ao público do mercado comercial.

O seu público de pré-visualização é identificado por GUIDs de assinatura Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes. Pode encontrar o seu ID de subscrição Azure na página **de Subscrições** no portal Azure.

Adicione pelo menos um ID de subscrição Azure, individualmente (até 10) ou carregando um ficheiro CSV (até 100). Ao adicionar estes IDs de subscrição, define quem pode pré-visualizar a sua oferta antes de ser publicado ao vivo. Se a sua oferta já estiver ao vivo, poderá ainda definir um público de pré-visualização para testes, oferecendo alterações ou atualizações à sua oferta.

> [!NOTE]
> Um público de pré-visualização difere de um público privado. Um público pré-visualizado pode aceder à sua oferta _antes_ de ser publicada ao vivo nos mercados. Eles podem ver e validar todos os planos, incluindo aqueles que estarão disponíveis apenas para um público privado depois da sua oferta ser totalmente publicada no mercado. Um público privado (definido no separador **de preços e disponibilidade** do plano) tem acesso exclusivo a um determinado plano.

Selecione **guardar rascunho** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Complete esta secção apenas se a sua oferta incluir uma aplicação gerida que emitirá eventos de medição utilizando a API do Serviço de Medição do Mercado. Insira o ID do **inquilino do Diretório Ativo Azure** e o ID de aplicação do **Diretório Ativo Azure** que o seu serviço utilizará ao emitir eventos de medição.

Selecione **guardar rascunho** antes de continuar.

## <a name="technical-configuration-offer-level"></a>Configuração técnica (nível de oferta)

>[!Note]
>Os detalhes técnicos ao nível da oferta são opcionais.  Só precisa de configurar estes detalhes se estiver a publicar uma aplicação gerida com faturação medição e tiver um serviço que irá autenticar com um token de segurança Azure AD. Para mais informações, consulte estratégias de autenticação sobre as [diferentes](./marketplace-metering-service-authentication.md) opções de autenticação.

A configuração técnica define os detalhes (ID do inquilino e ID da aplicação) usados para identificar o seu serviço, que emitirá eventos de medição para uma aplicação gerida utilizando o serviço de [medição do Marketplace APIs](./marketplace-metering-service-apis.md).  Introduza a identidade que o seu serviço utilizará ao emitir eventos de medição.

* **Id de inquilino azure AD** (obrigatório): Dentro do portal Azure, exigimos que [crie uma app azure Ative Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para que possamos validar a ligação entre os nossos dois serviços por trás de uma comunicação autenticada. Para encontrar o ID do [inquilino,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)vá ao seu Diretório Ativo Azure e selecione **Propriedades,** em seguida, procure o número de ID do **Diretório** listado (por exemplo 50c464d3-4930-494c-963c-1e951d15360e).
* ID da **aplicação Azure AD** (necessário): Também precisa do seu ID de [aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e de uma chave de autenticação. Para obter esses valores, vá ao seu Diretório Ativo Azure e selecione registos de **Aplicações,** em seguida, procure o número de ID de **aplicação** listado (por exemplo 50c464d3-4930-494c-963c-1e951d15360e). Para encontrar a chave de autenticação, vá a **Definições** e selecione **Keys**. Terá de fornecer uma descrição e duração e, em seguida, será-lhe fornecido um valor numéno.

>[!Note]
>O ID da aplicação Azure será associado ao seu ID de editor, e só pode ser reutilizado dentro desta conta de editor.

>[!Note]
>Esta configuração é necessária se desejar utilizar o evento de [utilização](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#batch-usage-event)do Lote .  Caso pretenda submeter o evento de [utilização,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#usage-event)também pode utilizar o serviço de [metadados](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) de instância para obter o token do portador da [Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)

## <a name="plan-overview"></a>Visão geral do plano

Este separador permite-lhe fornecer diferentes opções de plano dentro da mesma oferta. Estes planos (referidos como SKUs no Portal do Parceiro cloud) podem diferir em termos de tipo de plano (modelo de solução vs. aplicação gerida), rentabilização ou público.  Configure pelo menos um plano para listar a sua oferta no mercado.

Uma vez criado, você verá os nomes do seu plano, IDs, tipo de plano, disponibilidade (Público ou Privado), estado de publicação atual, e quaisquer ações disponíveis neste separador.

**As ações** disponíveis na **visão geral** do Plano variam consoante o estado atual do seu plano e podem incluir:

* Se o estado do plano for **Rascunho** – Apagar o projeto.
* Se o estado do plano for **Live** – Pare de vender o plano ou sincronizar o público privado.

### <a name="create-new-plan"></a>Criar novo plano

***Id do plano*** – Crie um id de plano único para cada plano nesta oferta. Este ID será visível para os clientes no URL do produto.  Use apenas caracteres minúsculos, alfanuméricos, traços ou sublinhados. Um máximo de 50 caracteres são permitidos para este plano id. Este ID não pode ser modificado após a seleção criar.

***Nome*** do plano - Os clientes verão este nome ao decidir qual o plano a selecionar dentro da sua oferta. Crie um nome de oferta único para cada plano nesta oferta. O nome do plano é usado para diferenciar planos de software que podem fazer parte da mesma oferta (por exemplo, nome de oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configuração do plano

Este separador permite definir a configuração de alto nível para o tipo de plano, quer reutilize pacotes de outro plano, e em que nuvens o plano deve estar disponível. As suas respostas neste separador afetarão quais os campos apresentados em outros separadores para o mesmo plano.

#### <a name="plan-type"></a>Tipo de plano
Selecione o tipo de plano para a sua oferta. Um plano de modelo de **solução** é gerido inteiramente pelo cliente. Um plano de **aplicação gerido** permite aos editores gerir a aplicação em nome do cliente. Para mais detalhes, consulte [os planos de aplicação do Azure.](#types-of-azure-application-plans)

#### <a name="re-use-technical-configuration"></a>Reconfiguração técnica de reutilização

Se tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, poderá selecionar **este plano reutilizar pacotes de outro plano**.  Ao selecionar esta opção, poderá selecionar um dos outros planos do mesmo tipo para esta oferta para reutilizar pacotes. 

>[!Note]
>Quando reutilizar os pacotes de outro plano, todo o separador de configuração técnica desaparecerá deste plano. Os detalhes técnicos de configuração do outro plano, incluindo quaisquer atualizações que fizer no futuro, também serão utilizados para este plano.<br><br>Esta definição não pode ser alterada após a publicação deste plano.

#### <a name="azure-regions-cloud"></a>Regiões de Azure (nuvem)

O seu plano deve ser disponibilizado em pelo menos uma região de Azure.

Selecione a opção **Azure Global** para disponibilizar o seu plano aos clientes de todas as regiões da Azure Global que tenham integração no mercado comercial. Para mais detalhes, consulte disponibilidade [geográfica e suporte cambial.](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)

Selecione a opção **do Governo Azure** para disponibilizar o seu plano na região do [Governo de Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) Esta região fornece acesso controlado a clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como parceiros elegíveis para os servir. Você, como editor, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas. O Governo azure utiliza centros e redes de dados fisicamente isolados (localizados apenas nos EUA).

Antes de publicar ao [Governo Azure,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)teste e valide o seu plano no ambiente, uma vez que certos pontos finais podem diferir. Para configurar e testar o seu plano, solicite uma conta de teste do julgamento do [Governo do Microsoft Azure](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Depois de o seu plano ser publicado e disponível numa região específica de Azure, não pode remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Governo Azure

Esta opção só é visível se selecionar **o Governo Azure.**

Os serviços do Governo Azure tratam de dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para sensibilizar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para a sua listagem no programa diretamente ou links para descrições da sua conformidade com os mesmos nos seus próprios websites. Estas ligações são visíveis apenas aos clientes do Governo Azure.

Selecione **guardar rascunho** antes de continuar.

### <a name="plan-listing"></a>Listagem de planos

É aqui que configura os detalhes da listagem do plano. Este separador apresenta informações específicas que podem diferir entre planos na mesma oferta.

#### <a name="plan-name"></a>Nome do plano

Isto está preenchido com o nome que deu ao seu plano quando o criou. Este nome aparece no mercado como o título deste plano e está limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano (não a oferta). Este resumo está limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software único, bem como diferenças entre planos dentro da sua oferta. Não descreva a oferta, só o plano. A descrição do plano pode conter até 2.000 caracteres.

Selecione **guardar rascunho** antes de continuar.

### <a name="availability-solution-template-plans-only"></a>Disponibilidade (apenas planos de modelo de solução)

Pode tornar o plano visível para todos, apenas para clientes específicos (um público privado), e se deve tornar o plano escondido para ser usado apenas por outro modelo de solução ou aplicações geridas.

#### <a name="plan-visibility"></a>Visibilidade do plano

Pode configurar cada plano para ser visível para todos ou apenas para um público específico à sua escolha. Você pode atribuir adesão a este público restrito usando IDs de subscrição Azure.

Selecione **Este é um plano privado** para tornar o seu plano privado e visível apenas para o público restrito da sua escolha. Uma vez publicado como um plano privado, pode atualizar o público ou optar por disponibilizar o plano a todos. Uma vez que um plano é publicado como visível para todos, deve permanecer visível para todos; não pode ser reconfigurado como um plano privado.

Se tornar a fábrica privada, insira uma identificação de **assinatura Azure** e a sua descrição. Cada um é um público que terá acesso a este plano privado. O acesso é atribuído utilizando IDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição Azure atribuído. Adicione até 10 iDs de subscrição de clientes individualmente, ou 20.000 importando um ficheiro CSV. Os IDs de subscrição azure são representados como GUIDs e as letras devem ser minúsculas.

>[!Note]
>Um público privado ou restrito é diferente do público de pré-visualização que definiu no separador **Preview.** Um público pré-visualizado pode aceder à sua oferta _antes_ da sua publicação ao vivo no mercado. Embora a escolha do público privado se aplique apenas a um plano específico, o público pré-visualização pode ver todos os planos (privados ou não) para fins de validação.

#### <a name="hide-plan"></a>Plano de ocultação

Se o seu modelo de solução se destina a ser implantado apenas indiretamente quando referenciado apesar de outro modelo de solução ou aplicação gerida, verifique esta caixa para publicar o seu modelo de solução, mas esconda-o dos clientes que procuram e navegam diretamente para ele.

Selecione **guardar rascunho** antes de continuar.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Preços e disponibilidade (apenas planos de candidatura geridos)

Utilize-o para configurar os **Mercados** em que este plano estará disponível, o **Preço** por mês da gestão da solução, e a visibilidade do **Plano** se apenas clientes específicos o vissem (um público privado).

Selecione **guardar rascunho** antes de continuar.

#### <a name="markets"></a>Mercados

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de verificação para qualquer local de mercado onde pretenda disponibilizar este plano. Uma caixa de pesquisa e um botão para selecionar países "Tax Remitted", no qual a Microsoft atribui as vendas e o imposto usado em seu nome, estão incluídos para ajudar.

Se já definiu preços para o seu plano em Dólares dos Estados Unidos (USD) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as taxas de câmbio atuais. Reveja sempre o preço de cada mercado antes de publicar. Os preços podem ser revistos utilizando o link "Preços de Exportação (xlsx)" depois de guardar as suas alterações.

#### <a name="pricing"></a>Preços

Forneça o preço por mês para este plano.  Este preço além de qualquer infraestrutura Azure ou custos de software pay-as-you-go incorridos pelos recursos implementados por esta solução.

Além do preço por mês, também pode fixar preços para consumo de unidades não standard utilizando [faturação medida.](./azure-app-metered-billing.md)  Pode fixar o preço por mês para zero e cobrar exclusivamente usando faturação medição, se quiser. 

Os preços estabelecidos em USD (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados utilizando as taxas de câmbio atuais quando poupadas. Valide estes preços antes de publicar exportando a folha de cálculo dos preços e revendo o preço em cada mercado. Se quiser definir preços personalizados num mercado individual, modifique e importe a folha de cálculo dos preços. 

>[!Note]
>Primeiro, tem de poupar as alterações de preços para permitir a exportação de dados de preços.

Reveja cuidadosamente os seus preços antes de publicar, uma vez que existem algumas restrições sobre o que pode mudar após a publicação de um plano.  

>[!Note]
>Uma vez publicado um preço por um mercado no seu plano, não pode ser alterado mais tarde.

#### <a name="plan-visibility"></a>Visibilidade do plano

Pode configurar cada plano para ser visível para todos ou apenas para um público específico à sua escolha. Você pode atribuir adesão a este público restrito usando IDs de subscrição Azure.

Selecione **Este é um plano privado** para tornar o seu plano privado e visível apenas para o público restrito da sua escolha. Uma vez publicado como um plano privado, pode atualizar o público ou optar por disponibilizar o plano a todos. Uma vez que um plano é publicado como visível para todos, deve permanecer visível para todos; não pode ser reconfigurado como um plano privado.

Se tornar a fábrica privada, insira uma identificação de **assinatura Azure** e a sua descrição. Cada um é um público que terá acesso a este plano privado. O acesso é atribuído utilizando IDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição Azure atribuído. Adicione até 10 iDs de subscrição de clientes individualmente, ou 20.000 importando um ficheiro CSV. Os IDs de subscrição azure são representados como GUIDs e as letras devem ser minúsculas.

>[!Note]
>Um público privado ou restrito é diferente do público de pré-visualização que definiu no separador **Preview.** Um público pré-visualizado pode aceder à sua oferta _antes_ da sua publicação ao vivo no mercado. Embora a escolha do público privado se aplique apenas a um plano específico, o público pré-visualização pode ver todos os planos (privados ou não) para fins de validação.

### <a name="technical-configuration"></a>Configuração técnica 

Este separador permite-lhe fazer o upload do pacote de implementação que permitirá aos clientes implementar o seu plano.

>[!Note]
>Este separador não será visível se configurar este plano para reutilizar pacotes de outro plano no separador **de configuração do Plano.**

#### <a name="package-details"></a>Detalhes do pacote

Este separador permite-lhe editar a versão de rascunho da sua configuração técnica.

**Versão** – Atribuir a versão atual da configuração técnica.  Incremente esta versão sempre que publicar uma alteração nesta página. A versão deve estar no formato `{integer}.{integer}.{integer}` .

**Ficheiro** de pacote (.zip) – Este pacote contém todos os ficheiros de modelo necessários para este plano, bem como quaisquer recursos adicionais, embalados como um `.zip` ficheiro.

Todos os pacotes do plano de aplicação Azure devem incluir estes dois ficheiros na pasta raiz de um `.zip` arquivo:

* Um ficheiro de modelo de gestor de recursos chamado [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Este modelo automatiza a implantação de recursos na subscrição do Azure dos clientes.  Por exemplo, nos modelos de Gestor de Recursos, consulte a [galeria de modelos De Quickstart Azure](https://azure.microsoft.com/documentation/templates/) ou o [correspondente GitHub: Modelos quickstart do Gestor](https://github.com/azure/azure-quickstart-templates) de Recursos Azure repo.
* Uma definição de interface de utilizador para a experiência de criação de aplicações Azure chamada [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Os tamanhos máximos de ficheiro suportados são:

* Até 1 Gb em tamanho total de arquivo comprimido `.zip`
* Até 1 Gb para qualquer ficheiro individual não comprimido dentro do `.zip` arquivo  

Todas as novas ofertas de aplicações Azure também devem incluir uma atribuição de uso de [cliente parceiro Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) GUID.

>[!Note]
>Se tiver algum problema de envio de ficheiros, certifique-se de que a sua rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

Os planos de aplicação geridos requerem informações adicionais sobre este separador.

#### <a name="previously-published-packages"></a>Pacotes anteriormente publicados

O subseparador de **pacotes anteriormente** publicadopermite-lhe visualizar todas as versões publicadas da sua configuração técnica.

#### <a name="enable-just-in-time-jit-access"></a>Ativar o acesso just-in-time (JIT)

Selecione esta opção para permitir o acesso just-in-time (JIT) para este plano.  O acesso ao JIT permite-lhe solicitar um acesso elevado aos recursos de uma aplicação gerida para resolução ou manutenção de problemas. Você sempre tem acesso apenas a leitura aos recursos, mas por um período de tempo específico você pode ter um maior acesso.  Para mais informações, consulte [Enable e solicite acesso just-in-time para Aplicações Geridas azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Para exigir que os consumidores da sua aplicação gerida concedam acesso permanente à sua conta, deixe esta opção desmarcada.

>[!Note]
>Certifique-se de atualizar o seu `createUiDefinition.json` ficheiro para suportar esta funcionalidade.  

#### <a name="deployment-mode"></a>Modo de implantação

Selecione se configura o modo de **implementação** **Completo** ou Incremental ao implementar este plano: 

* Em **modo completo,** uma reafectação da aplicação pelo cliente resultará na remoção de recursos no grupo de recursos geridos se os recursos não forem definidos no `mainTemplate.json` . 
* No **modo incremental,** uma redistribuição da aplicação deixa os recursos existentes inalterados.

Para saber mais sobre os modos de implementação, consulte os modos de [implementação do Gestor de Recursos Do Azure](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

#### <a name="notification-endpoint-url"></a>URL final de ponto de notificação

Especifique um ponto final https webhook para receber notificações sobre todas as operações crud em instâncias de aplicação geridas desta versão do plano.

#### <a name="customize-allowed-customer-actions"></a>Personalize as ações permitidas ao cliente

Selecione esta opção para especificar quais as ações que os clientes podem executar sobre os recursos geridos, além das `*/read` " ações " que estão disponíveis por padrão. 

Enumere as ações adicionais que pretende permitir que o seu cliente realize aqui, separados por pontos evívias.  Para mais informações, consulte [Understanding negar atribuições para os recursos do Azure.](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)  Para as ações disponíveis, consulte as operações de fornecedor de recursos do Gestor de [Recursos do Azure.](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations) Por exemplo, permitir que os consumidores reiniciem as máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` às ações permitidas.

#### <a name="global-azure--azure-government-cloud"></a>Nuvem global do governo azure / Azure

Indique quem deve ter acesso de gestão a esta aplicação gerida em cada nuvem suportada. Os utilizadores, grupos ou aplicações que pretende ser autorizadas ao grupo de recursos geridos são identificados usando identidades de Azure Ative Directory (AAD).

Id de Inquilino de **Diretório Ativo Azure** – O ID do Inquilino AAD (também conhecido como ID do diretório) contendo as identidades dos utilizadores, grupos ou aplicações a que pretende conceder permissões. Você pode encontrar o seu ID de Inquilino AAD no portal Azure, em [Propriedades para Diretório Ativo Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

**Autorizações** – Adicione o ID de objeto de objeto de Diretório Ativo Azure do utilizador, grupo ou aplicação que pretende ser autorizado ao grupo de recursos geridos. Identifique o utilizador pelo seu ID principal, que pode ser encontrado na lâmina de utilizadores do [Diretório Ativo Azure no portal Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Para cada diretor, selecione uma das funções integradas da Azure AD da lista (Proprietário ou Contribuinte). O papel que selecionar descreverá as permissões que o principal terá sobre os recursos na subscrição do cliente. Para obter mais informações, veja as [Funções incorporadas dos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), consulte [Iniciar com RBAC no portal Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Embora possa adicionar até 100 autorizações por nuvem, é geralmente mais fácil criar um grupo de utilizadores de Diretório Ativo e especificar o seu ID no "ID principal". Isto permitirá adicionar mais utilizadores ao grupo de gestão depois de o plano ser implementado e reduzir a necessidade de atualizar o plano apenas para adicionar mais autorizações.

#### <a name="policy-settings"></a>Definições de política

Aplique [políticas Azure](https://docs.microsoft.com/azure/governance/policy/overview) na sua aplicação gerida para especificar os requisitos de conformidade para a solução implementada. Para definições de política e o formato de valores de parâmetros, consulte [Exemplos de Política do Azure](https://docs.microsoft.com/azure/governance/policy/samples/index). Pode configurar um máximo de cinco políticas, e apenas um exemplo de cada opção Políticas. Algumas políticas requerem parâmetros adicionais. O SKU Padrão é necessário para políticas de auditoria. O Nome da Apólice está limitado a 50 caracteres.

Selecione **guardar rascunho** antes de continuar.

## <a name="co-sell-with-microsoft"></a>Co-sell with Microsoft (Venda conjunta com a Microsoft)

Fornecer informações sobre o separador Co-sell é totalmente opcional para publicar a sua oferta. É necessário alcançar o co-sell Ready e ip co-sell Ready status. As informações que fornecer serão utilizadas pelas equipas de vendas da Microsoft para saber mais sobre a sua solução ao avaliar o seu ajuste para as necessidades do cliente. Não está disponível diretamente para os clientes.

Para mais detalhes sobre este separador, consulte [a opção Co-sell no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="resell-through-csps"></a>Revenda através de CSPs

Expanda o alcance da sua oferta, disponibilizando-a aos parceiros do programa [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Isto permite que os revendedores vendam a sua oferta aos seus clientes e criem soluções agregadas.

Selecione **guardar rascunho** antes de continuar.

## <a name="test-drive"></a>Test drive

Configurar uma demonstração (test drive) que permite aos clientes experimentarem a sua oferta antes de a comprarem. Para criar um ambiente de demonstração que permita aos clientes experimentarem a sua oferta por um período de tempo fixo, consulte test [Drive a sua oferta no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Para ativar um test drive, selecione a caixa de verificação **de test drive** no separador de [configuração oferta.](#test-drive) Para remover o test drive da sua oferta, limpe esta caixa de verificação.

### <a name="test-drive-technical-configuration"></a>Configuração técnica de test drive

- **ID da aplicação Azure AD** (obrigatório): Introduza o id da [aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu esquerdo, selecione **registos**de Aplicações, em seguida, procure o número de ID de **aplicação** listado (por exemplo 50c464d3-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Detalhes da subscrição de implementação

Para permitir que o Test Drive seja implantado em seu nome, crie e forneça uma subscrição Azure separada e única (não necessária para unidades de teste Power BI).

* Id de **subscrição Azure** (necessário para o Gestor de Recursos Azure e aplicações Lógica) – Introduza o ID de subscrição para conceder acesso aos serviços da sua conta Azure para relatórios de utilização de recursos e faturação. Recomendamos que considere [criar uma subscrição Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar para test drives se ainda não tiver uma. Pode encontrar o seu ID de subscrição Azure iniciando sessão no [portal Azure](https://portal.azure.com/) e navegando no separador **Subscrições** do menu do lado esquerdo. A seleção do separador irá exibir o seu ID de subscrição (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").
* **ID de inquilino azure AD** (obrigatório) – Insira o seu Id de [inquilino](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu esquerdo, selecione **Properties,** procure o número de ID do **Diretório** listado (como 50c464d3-4930-494c-963c-1e951d15360e). Também pode procurar o ID do inquilino da sua organização usando o seu URL de nome de domínio em: [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .
* Nome de **inquilino Azure AD** (necessário para Dinâmico 365) – Introduza o nome de Diretório Ativo Azure (AD). Para encontrar este nome, inscreva-se no [portal Azure,](https://portal.azure.com/)no canto superior direito o seu nome de inquilino será listado sob o nome da sua conta.
* ID da **aplicação Azure AD** (obrigatório) – Introduza o id da [aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu de navegação à esquerda, selecione **registos**de Aplicações, procure o número de ID de **aplicação** listado (como 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure Ative Directory aplicação segredo** cliente (necessário) – Insira o [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)da aplicação Azure AD . Para encontrar este valor, inscreva-se no [portal Azure.](https://portal.azure.com/) Selecione o separador **Diretório Ativo Azure** no menu de navegação à esquerda, selecione **as inscrições**da App e, em seguida, selecione a sua aplicação de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo do cliente,** introduza uma descrição, selecione **Never** under **Expires,** em seguida, escolha **Adicionar**. Certifique-se de copiar o valor antes de sair desta página.)

Selecione **guardar rascunho** antes de continuar.

### <a name="marketplace-listing-optional"></a>Listagem de mercado (opcional)

Descreva a experiência de test drive.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Descrição** (necessária) – Descreva o seu test drive, o que será demonstrado, objetivos para o utilizador experimentar, funcionalidades a explorar e qualquer informação relevante para ajudar o utilizador a determinar se adquire a sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 
* **Informação de acesso** (necessária para o Gestor de Recursos Azure e unidades de teste lógica) – Explique o que um cliente precisa de saber para aceder e utilizar este test drive. Caminhe por um cenário de utilização da sua oferta e exatamente o que o cliente deve saber para aceder a funcionalidades durante todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.
* **Manual do Utilizador** (obrigatório) – Um walk-through aprofundado da sua experiência de test drive. O Manual do Utilizador deve cobrir exatamente o que pretende que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer questões que possam ter. O ficheiro deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.
* **Vídeos: Adicionar vídeos** (opcional) – Os vídeos podem ser enviados para o YouTube ou Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa ver um walk-through de informação para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso as funcionalidades da sua oferta e entender cenários que realçam os seus benefícios.
  * **Nome** (obrigatório)
  * **Endereço** (apenas YouTube ou Vimeo; obrigatório)
  * **Miniatura** (Ficheiro de imagem deve estar em formato PNG e 533 x 324 px).

Selecione **guardar rascunho** antes de continuar.

## <a name="publish"></a>Publicar

Quando tiver concluído todas as secções necessárias da oferta, selecione **Rever e publique** no canto superior direito do portal.

Reveja o estado de conclusão de cada secção da oferta.
    - *Não começou* - significa que a secção não foi tocada e precisa de ser concluída.
    - *Incompleto* - significa que a secção tem erros que precisam de ser corrigidos ou exige mais informações a fornecer. Volte para a secção(s) e atualize-a.
    - *Completo* - significa que a secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado total antes de poder submeter a oferta.

Se esta for a sua primeira publicação desta oferta, pode fornecer instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.

Selecione **Submeter** para submeter a sua oferta para publicação. Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta está disponível para que você reveja e aprove.

Volte ao Partner Center e selecione **Go-live** para a oferta de publicar a sua oferta ao público (ou se uma oferta privada, para o público privado).

### <a name="errors-and-review-feedback"></a>Erros e feedback de revisão

A etapa de **validação manual** no processo de publicação representa uma revisão extensiva da sua oferta e dos seus ativos técnicos associados (especialmente o modelo do Gestor de Recursos Azure), as questões são normalmente apresentadas como links de pedido de puxão (PR). Uma explicação de como ver e responder a estes PRs, ver feedback de [revisão de handling](./azure-apps-review-feedback.md).

Se tiver erros numa ou mais das etapas de publicação, corrija-os antes de voltar a publicar a sua oferta.

## <a name="next-steps"></a>Passos seguintes

* [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

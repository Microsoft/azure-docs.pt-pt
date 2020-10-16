---
title: Criar uma oferta de aplicações Azure - Mercado comercial da Microsoft
description: Conheça os passos e considerações para a criação de uma nova oferta de aplicações Azure no portal do mercado comercial no Partner Center. Pode listar ou vender a sua oferta de aplicação Azure no Azure Marketplace ou através do programa Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: AarathiN
ms.author: aarathin
ms.date: 07/14/2020
ms.openlocfilehash: fb3a3ab5339186d8fa4e347d9d13e66940457f8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710724"
---
# <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicações do Azure

Este artigo explica os passos e considerações para a criação de uma nova oferta de aplicações Azure no mercado comercial. Você deve estar familiarizado com estes conceitos antes de criar uma nova oferta de aplicação Azure.

Antes de poder publicar uma nova oferta de aplicação Azure, [crie uma conta de marketplace comercial no Partner Center](create-account.md) e certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="before-you-begin"></a>Antes de começar

Projetar, construir e testar as ofertas de aplicações Azure requer conhecimento técnico tanto da plataforma Azure como das tecnologias usadas para construir a oferta. A sua equipa de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft:

* Compreensão básica dos [Serviços Azure.](https://azure.microsoft.com/services/)
* Como [conceber e arquitetá-lo Azure aplicações.](https://azure.microsoft.com/solutions/architecture/)
* Conhecimentos de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)e [Azure Networking.](https://azure.microsoft.com/services/?filter=networking#networking)
* Conhecimento de trabalho da [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Conhecimento de trabalho da [JSON.](https://www.json.org/)

### <a name="technical-documentation-and-resources"></a>Documentação técnica e recursos

Reveja os seguintes recursos enquanto prepara a sua oferta de candidatura a Azure para o mercado comercial.

* [Compreenda os modelos do gestor de recursos do Azure](../../azure-resource-manager/resource-group-authoring-templates.md)

* Guias de Início Rápido:

    * [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/)
    * [Guia de boas práticas de modelos de Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [Publicar a definição da aplicação](../../managed-applications/publish-service-catalog-app.md)
    * [Implementar a aplicação do catálogo de serviços](../../managed-applications/deploy-service-catalog-quickstart.md)

* Tutoriais:

    * [Criar ficheiros de definição](../../managed-applications/publish-service-catalog-app.md)
    * [Publicar a aplicação do marketplace](../../managed-applications/publish-marketplace-app.md)

* Amostras:

    * [CLI do Azure](../../managed-applications/cli-samples.md)
    * [Azure PowerShell](../../managed-applications/powershell-samples.md)
    * [Soluções de aplicação gerida](../../managed-applications/sample-projects.md)

Os [modelos de solução de construção de vídeo e aplicações geridas para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) dão uma introdução abrangente ao tipo de oferta de aplicação Azure:

* Que tipos de oferta estão disponíveis
* Que ativos técnicos são necessários
* Como autor de um modelo de Gestor de Recursos Azure
* Desenvolvimento e teste da UI da aplicação
* Como publicar a oferta da app
* O processo de revisão de candidaturas

### <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerir a sua aplicação Azure:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [CLI do Azure](https://docs.microsoft.com/cli/azure)

Recomendamos adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Explorador de Armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Código do Estúdio Visual](https://code.visualstudio.com/) com as seguintes extensões:
    * Extensão: [Ferramentas do gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extensão: [Embelezamento](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Pode rever as ferramentas disponíveis na página [Azure Developer Tools.](https://azure.microsoft.com/tools/) Além disso, se estiver a utilizar o Visual Studio, o [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="types-of-azure-application-plans"></a>Tipos de planos de aplicação Azure

Existem dois tipos de planos de aplicação Azure: modelos de solução e aplicações geridas.

* **O modelo de solução** é uma das principais formas de publicar uma solução no Mercado. Utilize este tipo de plano quando a sua solução necessitar de uma automatização adicional de implantação e configuração para além de uma única máquina virtual (VM). Com um modelo de solução, pode automatizar o fornecimento de mais de um recurso, incluindo VMs, networking e recursos de armazenamento para fornecer soluções complexas de IaaS.  Para obter mais informações sobre modelos de solução de construção, consulte [o Gestor de Recursos Azure.](../../azure-resource-manager/resource-group-overview.md)

* **A aplicação gerida** é semelhante aos modelos de solução, com uma diferença fundamental. Numa aplicação gerida, os recursos são implementados num grupo de recursos que é gerido pelo editor da aplicação. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos. Como editor, tem de especificar o custo do suporte contínuo da solução. Utilize aplicações geridas para facilmente construir e entregar aplicações chave na mão e totalmente geridas aos seus clientes.  Para obter mais informações sobre as vantagens e tipos de aplicações geridas, consulte a visão geral das [aplicações geridas pelo Azure.](../../managed-applications/overview.md)

## <a name="technical-requirements"></a>Requisitos técnicos

Todas as aplicações Azure incluem pelo menos dois ficheiros na pasta raiz de um `.zip` arquivo:

* Um ficheiro de modelo do Gestor de Recursos nomeado [mainTemplate.jsem](../../azure-resource-manager/resource-group-overview.md).  Este modelo define os recursos para implantar na subscrição Azure do cliente. Para exemplos de modelos de Gestor de Recursos, consulte a [galeria de modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/) ou o [gitHub correspondente: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) repo.

* Uma definição de interface de utilizador para a experiência de criação de aplicações Azure chamada [createUiDefinition.jsem](../../managed-applications/create-uidefinition-overview.md).  Na interface de utilizador, especifique os elementos que permitem aos consumidores fornecer valores de parâmetros.

Todas as novas ofertas de aplicações da Azure devem incluir uma [atribuição de utilização do cliente parceiro Azure GUID](../azure-partner-customer-usage-attribution.md). 

Para conhecer os requisitos de publicação de cada plano de aplicação, consulte [os requisitos de publicação do modelo de solução](../marketplace-solution-templates.md) e [os requisitos de publicação de aplicações geridas.](../marketplace-managed-apps.md)

## <a name="create-a-new-offer"></a>Criar uma nova oferta

>[!NOTE]
>Depois de uma oferta ser publicada, as edições que fizer no Partner Center não aparecerão nas lojas online até que volte a publicar a oferta. Certifique-se de que sempre reeditou a oferta depois de fazer alterações.

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. No menu esquerdo, selecione **Commercial Marketplace**  >  **Overview**.

1. Na página 'Vista Geral', selecione **+ Nova oferta**  >  **Aplicação Azure**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-azure-app.png)

1. Na página **Nova oferta,** insira um **ID de oferta.** Este é um identificador único para cada oferta na sua conta.

     * Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos de Gestor de Recursos Azure, se aplicável.
     * Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir **a oferta de teste-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
     * O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

1. Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

     * Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
     * O pseudónimo Oferta não pode ser alterado depois de selecionar **Criar**.

1. Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

A página **de configuração da Oferta** é onde pode configurar um test drive e gestão de chumbo para a sua oferta.

### <a name="test-drive"></a>Unidade de teste

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "experimentar antes de comprar", resultando numa maior conversão e na geração de leads altamente qualificados. [Saiba mais sobre test drives](../what-is-test-drive.md).

Para ativar uma unidade de teste durante um período de tempo fixo, selecione a caixa de **verificação de acionamento de test drive.** Para remover o test drive da sua oferta, limpe esta caixa de verificação. Configure o ambiente de test drive na secção [técnica de configuração da unidade de teste](#test-drive-technical-configuration) mais tarde neste tópico.

Para obter informações adicionais, consulte [Test drive a sua oferta no mercado comercial.](test-drive.md) Também pode ler sobre [as melhores práticas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) de test drive e baixar o [PDF de unidades de teste](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (certifique-se de que o seu bloqueador pop-up está desligado)

>[!Note]
>Como todas as aplicações Azure são implementadas usando um modelo de Gestor de Recursos Azure, o único tipo de unidade de teste disponível para uma aplicação Azure é uma [unidade de teste baseada em recursos Azure.](../azure-resource-manager-test-drive.md)

### <a name="customer-leads"></a>Ligações ao cliente

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Para obter mais informações, consulte [a visão geral da gestão do chumbo.](./commercial-marketplace-get-customer-leads.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="properties"></a>Propriedades

A página **Propriedades** é onde define as categorias usadas para agrupar a sua oferta no mercado, a sua versão de aplicação e os contratos legais que suportam a sua oferta.

### <a name="category"></a>Categoria

Selecione categorias e subcategorias para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Não se esqueça de descrever como a sua oferta suporta estas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se não for aplicável nenhuma subcategoria à sua oferta, selecione **Não aplicável**.

Consulte a lista completa de categorias e subcategorias na [Listagem de Ofertas Boas Práticas.](../gtm-offer-listing-best-practices.md)

### <a name="legal"></a>Legal

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Listagem de ofertas

Esta página é onde você gere a cópia e imagens para a sua oferta de mercado comercial.

### <a name="marketplace-details"></a>Detalhes do mercado

> [!NOTE]
> A oferta de conteúdo de listagem (como a descrição, documentos, screenshots e termos de utilização) não é necessária para estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também é aceitável fornecer um *URL de Link Útil* para oferecer conteúdo em um idioma diferente do utilizado no conteúdo da listagem de Oferta.

Aqui está um exemplo de como a informação de oferta aparece no Azure Marketplace (quaisquer preços listados são apenas para fins e não se destinam a refletir custos reais):

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logótipo
2. Categorias
3. Endereço de suporte (link)
4. Termos de utilização
5. Endereço de política de privacidade (link)
6. Nome da oferta
7. Resumo
8. Descrição
9. Screenshots/vídeos

<br>Aqui está um exemplo de como a oferta de informação aparece no portal Azure:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Título
2. Descrição
3. Ligações úteis
4. Capturas de ecrã

#### <a name="name"></a>Nome

O nome que introduzir aqui será mostrado aos clientes como o título da sua listagem de oferta. Este campo é pré-povoado com o texto que inseriu para **o pseudónimo Oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser comercializado (e pode incluir símbolos de marca registada ou de direitos autorais). O nome não pode ter mais de 50 caracteres e não pode incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta, até 100 caracteres. Esta descrição pode ser utilizada nos resultados da pesquisa.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta, até 256 caracteres. Esta descrição pode ser utilizada nos resultados da pesquisa.

#### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Pesquisar palavras-chave

Pode introduzir opcionalmente até três palavras-chave de pesquisa para ajudar os clientes a encontrar a sua oferta no mercado. Para obter melhores resultados, utilize também estas palavras-chave na sua descrição.

#### <a name="privacy-policy-link"></a>Ligação de política de privacidade

Insira o URL na política de privacidade da sua organização. Você é responsável por garantir que a sua aplicação está em conformidade com as leis e regulamentos de privacidade, e por fornecer uma política de privacidade válida.

### <a name="useful-links"></a>Ligações úteis

Adicione links a documentos online suplementares opcionais sobre a sua solução selecionando **+ Adicione um link.**

### <a name="contact-information"></a>Informações de contacto

Forneça o nome, e-mail e número de telefone para um **contacto de suporte,** **contacto de engenharia**e contacto do Programa **CSP.** Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft e poderá ser fornecida aos parceiros da CSP. Alguns contactos podem requerer informações adicionais.

### <a name="marketplace-media"></a>Meios de comunicação do mercado

Forneça logotipos e imagens para utilizar com a sua oferta. Todas as imagens devem estar em formato PNG. Imagens desfocadas farão com que a sua submissão seja rejeitada.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se tiver um problema de upload de ficheiros, certifique-se de que a sua rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="store-logos"></a>Logotipos da loja

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center utilizará isto para criar um logótipo **Pequeno** e **Médio.** Pode substituir opcionalmente estas imagens por diferentes imagens mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, necessário)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Estes logótipos são utilizados em diferentes locais da listagem:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de ecrã

Adicione cinco imagens que mostram como a sua oferta funciona. Cada screenshot deve ter 1280 x 720 pixels em tamanho e em formato PNG. Cada imagem deve incluir uma legenda.

#### <a name="videos"></a>Vídeos

Adicione até cinco vídeos que demonstram a sua oferta. Estes devem ser hospedados num serviço de vídeo externo. Insira o nome de cada vídeo, endereço web e uma imagem PNG de miniatura do vídeo a 1280 x 720 pixels.

#### <a name="additional-marketplace-listing-resources"></a>Recursos de listagem de mercado adicionais

- [Melhores práticas para anúncios de oferta de mercado](../gtm-offer-listing-best-practices.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="preview-audience"></a>Audiência de pré-visualização

No separador Pré-visualização, escolha um **Audiência de Pré-visualização** limitado para validar a sua oferta antes de publicá-la ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Depois de verificar a sua oferta em Preview, selecione **Go live** para publicar a sua oferta para o público do mercado comercial.

O seu público de pré-visualização é identificado por GUIDs de assinatura Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes. Pode encontrar o seu ID de subscrição Azure na página **de Subscrições** no portal Azure.

Adicione pelo menos um ID de subscrição Azure, individualmente (até 10) ou carregando um ficheiro CSV (até 100). Ao adicionar estes IDs de subscrição, define quem pode pré-visualizar a sua oferta antes de ser publicada ao vivo. Se a sua oferta já estiver ao vivo, poderá ainda definir um público de pré-visualização para testar alterações ou atualizações da sua oferta.

> [!NOTE]
> Um público de pré-visualização difere de uma audiência privada. Um público de pré-visualização pode aceder à sua oferta *antes* de ser publicada ao vivo nos mercados. Eles podem ver e validar todos os planos, incluindo aqueles que estarão disponíveis apenas para um público privado após a sua oferta ser totalmente publicada no mercado. Um público privado (definido no **separador de preços e disponibilidade** do plano) tem acesso exclusivo a um determinado plano.

**Selecione Guardar o projeto** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Complete esta secção apenas se a sua oferta incluir uma aplicação gerida que emitirá eventos de medição usando a API do Serviço de Medição do Mercado. Insira o ID do inquilino do **Azure Ative e** **o ID da aplicação Azure Ative Directory que** o seu serviço utilizará ao emitir eventos de medição.

**Selecione Guardar o projeto** antes de continuar.

## <a name="technical-configuration-offer-level"></a>Configuração técnica (nível de oferta)

>[!Note]
>Os detalhes técnicos ao nível da oferta são opcionais.  Só precisa de configurar estes detalhes se estiver a publicar uma aplicação gerida com faturação e tiver um serviço que será autenticado com um token de segurança AZure. Para mais informações, consulte [estratégias de autenticação](./marketplace-metering-service-authentication.md) nas diferentes opções de autenticação.

A configuração técnica define os detalhes (ID do inquilino e ID de aplicativo) usados para identificar o seu serviço, que emitirá eventos de medição para uma aplicação gerida usando o [serviço de medição do Marketplace APIs](./marketplace-metering-service-apis.md).  Insira a identidade que o seu serviço utilizará ao emitir eventos de medição.

* **ID do inquilino Azure AD** (obrigatório): Dentro do portal Azure, você deve [criar uma app Azure Ative Directory (AD)](../../active-directory/develop/howto-create-service-principal-portal.md) para que possamos validar a ligação entre os nossos dois serviços está por trás de uma comunicação autenticada. Para encontrar o ID do [inquilino),](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)vá ao seu Diretório Ativo Azure e selecione **Propriedades,** em seguida, procure o número **de ID** do Diretório listado (por exemplo 50c464d3-4930-494c-963c-1e951d15360e).
* **ID da aplicação AD Azure** (obrigatório): Também precisa do seu [ID de aplicação)](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)e uma chave de autenticação. Para obter esses valores, vá ao seu Diretório Ativo Azure e selecione **registos de Aplicações**, em seguida, procure o número **de ID da aplicação** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Para encontrar a chave de autenticação, vá a **Definições** e selecione **Teclas**. Terá de fornecer uma descrição e duração e, em seguida, será fornecido um valor numer numer.

>[!Note]
>O ID da aplicação Azure será associado ao ID da sua editora, e só pode ser reutilizado dentro desta conta de editor.

>[!Note]
>Esta configuração é necessária se pretender utilizar o [evento de utilização do Lote).](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)  No caso de pretender submeter [o evento de utilização),](marketplace-metering-service-apis.md#metered-billing-single-usage-event)também pode utilizar o [serviço de metadados de exemplo](../../active-directory/managed-identities-azure-resources/overview.md) para obter o [token web JSON (JWT).](pc-saas-registration.md#how-to-get-the-publishers-authorization-token)

## <a name="plan-overview"></a>Visão geral do plano

Este separador permite-lhe fornecer diferentes opções de plano dentro da mesma oferta. Estes planos (anteriormente chamados SKUs) podem diferir em termos de tipo de plano (modelo de solução vs aplicação gerida), monetização ou audiência. Configure pelo menos um plano para listar a sua oferta no mercado.

Pode criar até 100 planos para cada oferta: até 45 destes podem ser privados. Saiba mais sobre [planos privados em ofertas privadas no mercado comercial da Microsoft.](../private-offers.md)

Uma vez criado, verá os nomes do seu plano, IDs, tipo de plano, disponibilidade (Público ou Privado), estado de publicação atual e quaisquer ações disponíveis neste separador.

**As ações** disponíveis no Resumo do **Plano** variam consoante o estado atual do seu plano e podem incluir:

* Se o estado do plano for **Rascunho** – Eliminar rascunho.
* Se o estado do plano for **Live** – Pare de vender o plano ou o público privado Sync.

### <a name="create-new-plan"></a>Criar novo plano

***ID do plano*** – Criar um ID de plano único para cada plano nesta oferta. Este ID será visível para os clientes no URL do produto.  Utilize apenas caracteres minúsculos, alfanuméricos, traços ou sublinhados. Um máximo de 50 caracteres são permitidos para este iD do plano. Este ID não pode ser modificado após a seleção criar.

***Nome do*** plano – Os clientes verão este nome ao decidir qual o plano a selecionar dentro da sua oferta. Crie um nome de oferta único para cada plano nesta oferta. O nome do plano é usado para diferenciar planos de software que podem fazer parte da mesma oferta (por exemplo, nome de oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configuração do plano

Este separador permite definir a configuração de alto nível para o tipo de plano, quer reutilie os pacotes de outro plano, e quais as nuvens em que o plano deve estar disponível. As suas respostas neste separador afetarão quais os campos apresentados em outros separadores para o mesmo plano.

#### <a name="plan-type"></a>Tipo de plano
Selecione o tipo de plano para a sua oferta. Um plano **de modelo de solução** é gerido inteiramente pelo cliente. Um plano **de aplicação gerido** permite aos editores gerir a aplicação em nome do cliente. Para mais informações, consulte [os planos de aplicação do Azure](#types-of-azure-application-plans).

#### <a name="re-use-technical-configuration"></a>Reutilizar a configuração técnica

Se tiver mais do que um plano do mesmo tipo e as embalagens forem idênticas entre eles, poderá selecionar **este plano reutiliza pacotes de outro plano**.  Ao selecionar esta opção, poderá selecionar um dos outros planos do mesmo tipo para esta oferta de reutilização de pacotes a partir de.

>[!Note]
>Quando reutilizar os pacotes a partir de outro plano, todo o separador de configuração técnica desaparecerá deste plano. Os detalhes de configuração técnica do outro plano, incluindo quaisquer atualizações que fizer no futuro, também serão utilizados para este plano.<br><br>Esta definição não pode ser alterada após a publicação deste plano.

#### <a name="azure-regions-cloud"></a>Regiões de Azul (nuvem)

O seu plano deve ser disponibilizado em pelo menos uma região de Azure.

Selecione a opção **Azure Global** para disponibilizar o seu plano aos clientes de todas as regiões da Azure Global que tenham integração comercial no mercado. Para mais informações, consulte [a disponibilidade geográfica e o suporte à moeda.](../marketplace-geo-availability-currencies.md)

Selecione a opção **do Governo Azure** para disponibilizar o seu plano na região do [Governo Azure.](../../azure-government/documentation-government-welcome.md) Esta região fornece acesso controlado a clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como parceiros elegíveis para os servir. Você, como editora, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas. O Governo Azure utiliza centros e redes de dados fisicamente isolados (localizados apenas nos EUA).

Antes de publicar para o [Governo Azure,](../../azure-government/documentation-government-manage-marketplace-partners.md)teste e valide o seu plano no ambiente, uma vez que certos pontos finais podem diferir. Para configurar e testar o seu plano, solicite uma conta de teste do julgamento do [Governo Microsoft Azure](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Depois de o seu plano ser publicado e disponível numa região específica de Azure, não pode remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Governo de Azure

Esta opção só é visível se selecionar **o Governo Azure.**

Os serviços do Governo Azure tratam dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para consciencializar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para a sua listagem diretamente no programa ou links para descrições da sua conformidade com eles nos seus próprios websites. Estas ligações visíveis apenas aos clientes do Governo Azure.

**Selecione Guardar o projeto** antes de continuar.

### <a name="plan-listing"></a>Listagem de planos

É aqui que configura os detalhes do plano. Este separador apresenta informações específicas que podem diferir entre os planos na mesma oferta.

#### <a name="plan-name"></a>Nome do plano

Isto está pré-preenchido com o nome que deu o seu plano quando o criou. Este nome aparece no mercado como o título deste plano e está limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano (não a oferta). Este resumo está limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software único, bem como diferenças entre planos dentro da sua oferta. Não descreva a oferta, só o plano. A descrição do plano pode conter até 2.000 caracteres.

**Selecione Guardar o projeto** antes de continuar.

### <a name="availability-solution-template-plans-only"></a>Disponibilidade (apenas planos de modelo de solução)

Pode tornar o plano visível para todos, apenas para clientes específicos (um público privado), e se deve fazer o plano escondido para uso por outro modelo de solução ou aplicações geridas apenas.

#### <a name="plan-visibility"></a>Visibilidade do plano

Pode configurar cada plano para ser visível para todos ou apenas para um público específico à sua escolha. Pode atribuir adesão a este público restrito usando IDs de subscrição Azure.

Selecione **Este é um plano privado** para tornar o seu plano privado e visível apenas para o público restrito à sua escolha. Uma vez publicado como um plano privado, pode atualizar o público ou optar por disponibilizar o plano a todos. Uma vez publicado um plano como visível para todos, deve permanecer visível para todos; não pode ser reconfigurado como um plano privado.

Se tornar o plano privado, insira um **ID de assinatura Azure** e a sua descrição. Cada um é um público que terá acesso a este plano privado. O acesso é atribuído utilizando iDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição Azure atribuído. Adicione até 10 iDs de subscrição de clientes individualmente, ou 20.000 importando um ficheiro CSV. Os IDs de assinatura Azure são representados como GUIDs e as letras devem ser minúsculas.

>[!Note]
>Um público privado ou restrito é diferente do público de pré-visualização que definiu no **separador Pré-visualização.** Um público de pré-visualização pode aceder à sua oferta _antes_ da sua publicação ao vivo no mercado. Embora a escolha do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos (privados ou não) para fins de validação.

#### <a name="hide-plan"></a>Plano de ocultação

Se o seu modelo de solução se destinar a ser implementado apenas indiretamente quando referenciado embora outro modelo de solução ou aplicação gerida, verifique esta caixa para publicar o seu modelo de solução, mas esconda-o dos clientes que procuram e navegam diretamente.

**Selecione Guardar o projeto** antes de continuar.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Preços e disponibilidade (apenas planos de candidatura geridos)

Utilize-o para configurar os **Mercados** em que este plano estará disponível, o **Preço** por mês da gestão da solução, e a **visibilidade** do Plano se apenas os clientes específicos o deverem ver (uma audiência privada).

**Selecione Guardar o projeto** antes de continuar.

#### <a name="markets"></a>Mercados

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de verificação para qualquer local de mercado onde gostaria de disponibilizar este plano. Uma caixa de pesquisa e um botão para selecionar países/regiões "Tax Remitted", em que a Microsoft remete as vendas e usa o imposto em seu nome, estão incluídos para ajudar.

Se já estabeleceu preços para o seu plano em Dólares dos Estados Unidos (USD) e adiciona outra localização de mercado, o preço do novo mercado será calculado de acordo com as taxas de câmbio atuais. Reveja sempre o preço de cada mercado antes de publicar. Os preços podem ser revistos utilizando o link "Preços de exportação (xlsx)" depois de guardar as suas alterações.

#### <a name="pricing"></a>Preços

Forneça o preço mensal para este plano.  Este preço é além de qualquer infraestrutura Azure ou custos de software pay-as-you-go incorridos pelos recursos implementados por esta solução.

Além do preço mensal, também pode definir preços para consumo de unidades não standard usando [faturação medido.](./azure-app-metered-billing.md)  Pode definir o preço por mês para zero e cobrar exclusivamente usando faturação medido, se quiser.

Os preços fixados em USD (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados utilizando as taxas de câmbio atuais quando guardados. Validar estes preços antes de publicar exportando a folha de cálculo de preços e revendo o preço em cada mercado. Se quiser definir preços personalizados num mercado individual, modifique e importe a folha de cálculo de preços.

>[!Note]
>Primeiro, tem de guardar as suas alterações de preços para permitir a exportação de dados de preços.

Reveja cuidadosamente os seus preços antes de publicar, pois existem algumas restrições sobre o que pode mudar após a publicação de um plano.  

>[!Note]
>Uma vez que um preço para um mercado no seu plano é publicado, não pode ser alterado mais tarde.

#### <a name="plan-visibility"></a>Visibilidade do plano

Pode configurar cada plano para ser visível para todos ou apenas para um público específico à sua escolha. Pode atribuir adesão a este público restrito usando IDs de subscrição Azure.

Selecione **Este é um plano privado** para tornar o seu plano privado e visível apenas para o público restrito à sua escolha. Uma vez publicado como um plano privado, pode atualizar o público ou optar por disponibilizar o plano a todos. Uma vez publicado um plano como visível para todos, deve permanecer visível para todos; não pode ser reconfigurado como um plano privado.

>[!Note]
>Um público privado ou restrito é diferente do público de pré-visualização que definiu no **separador Pré-visualização.** Um público de pré-visualização pode aceder à sua oferta _antes_ da sua publicação ao vivo no mercado. Embora a escolha do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos (privados ou não) para fins de validação.

Se tornar o plano privado, insira um **ID de assinatura Azure** e a sua descrição. Cada um é um público que terá acesso a este plano privado. O acesso é atribuído utilizando iDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição Azure atribuído. Adicione até 10 iDs de subscrição de clientes individualmente, ou 20.000 importando um ficheiro CSV. Os IDs de assinatura Azure são representados como GUIDs e as letras devem ser minúsculas.

>[!Note]
>As ofertas privadas não são suportadas com subscrições Azure estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

### <a name="technical-configuration"></a>Configuração técnica

Este separador permite-lhe fazer o upload do pacote de implementação que permitirá aos clientes implementar o seu plano.

>[!Note]
>Este separador não será visível se configurar este plano para reutilizar as encomendas a partir de outro plano no **separador de configuração** do Plano.

#### <a name="package-details"></a>Detalhes do pacote

Este separador permite-lhe editar a versão de projeto da sua configuração técnica.

**Versão** – Atribuir a versão atual da configuração técnica.  Incremente esta versão sempre que publicar uma alteração nesta página. A versão deve estar no formato `{integer}.{integer}.{integer}` .

**Ficheiro de pacote** (.zip) – Este pacote contém todos os ficheiros de modelo necessários para este plano, bem como quaisquer recursos adicionais, embalados como `.zip` um ficheiro.

Todos os pacotes do plano de aplicações Azure devem incluir estes dois ficheiros na pasta raiz de um `.zip` arquivo:

* Um ficheiro de modelo do Gestor de Recursos nomeado [mainTemplate.jsem](../../azure-resource-manager/resource-group-overview.md). Este modelo automatiza a implantação de recursos na subscrição do Azure dos clientes.  Para exemplos de modelos de Gestor de Recursos, consulte a [galeria de modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates/) ou o [gitHub correspondente: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) repo.
* Uma definição de interface de utilizador para a experiência de criação de aplicações Azure chamada [createUiDefinition.jsem](../../azure-resource-manager/managed-application-createuidefinition-overview.md).

Os tamanhos máximos de ficheiro suportados são:

* Até 1 Gb no tamanho total do arquivo comprimido `.zip`
* Até 1 Gb para qualquer ficheiro individual não comprimido dentro do `.zip` arquivo  

Todas as novas ofertas de aplicações da Azure também devem incluir uma [atribuição de utilização do cliente parceiro Azure](../azure-partner-customer-usage-attribution.md) GUID.

>[!Note]
>Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

Os planos de aplicação geridos requerem informações adicionais sobre este separador.

#### <a name="previously-published-packages"></a>Pacotes previamente publicados

O sub-separador de **pacotes previamente publicado** permite-lhe visualizar todas as versões publicadas da sua configuração técnica.

#### <a name="enable-just-in-time-jit-access"></a>Ativar o acesso just-in-time (JIT)

Selecione esta opção para ativar o acesso just-in-time (JIT) para este plano.  O acesso ao JIT permite-lhe solicitar um acesso elevado aos recursos de uma aplicação gerida para resolução de problemas ou manutenção. Sempre tem acesso apenas de leitura aos recursos, mas por um período específico pode ter um maior acesso.  Para obter mais informações, consulte [Ativar e solicitar acesso just-in-time para aplicações geridas azure](../../managed-applications/request-just-in-time-access.md).  Para exigir que os consumidores da sua aplicação gerida concedam acesso permanente à sua conta, deixe esta opção desmarcada.

>[!Note]
>Certifique-se de atualizar o seu `createUiDefinition.json` ficheiro de forma a suportar esta funcionalidade.  

#### <a name="deployment-mode"></a>Modo de implantação

Selecione se configurar **o modo de implantação** **completo** ou incremental ao implementar este plano: 

* Em **modo completo,** uma redistribuição da aplicação pelo cliente resultará na remoção de recursos no grupo de recursos geridos se os recursos não forem definidos no `mainTemplate.json` . 
* Em **modo incremental,** uma redistribuição da aplicação deixa os recursos existentes inalterados.

Para saber mais sobre os modos de implementação, consulte os [modos de implementação do Gestor de Recursos Azure](../../azure-resource-manager/deployment-modes.md).

#### <a name="notification-endpoint-url"></a>URL de ponto final de notificação

Especifique um ponto final https Webhook para receber notificações sobre todas as operações crud em instâncias de aplicação geridas desta versão do plano.

#### <a name="customize-allowed-customer-actions"></a>Personalizar ações permitidas ao cliente

Selecione esta opção para especificar quais as ações que os clientes podem executar nos recursos geridos, além das `*/read` " ações " que estão disponíveis por padrão.

Enuse as ações adicionais que pretende permitir que o seu cliente realize aqui, separadas por pontos de e vírgão.  Para obter mais informações, consulte [Understanding negar atribuições para recursos Azure](../../role-based-access-control/deny-assignments.md). Para as ações disponíveis, consulte [as operações do fornecedor de recursos do Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md). Por exemplo, permitir que os consumidores reiniciem as máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` às ações permitidas.

#### <a name="global-azure--azure-government-cloud"></a>Global Azure / Azure Government Cloud

Indique quem deve ter acesso à gestão a esta aplicação gerida em cada nuvem suportada. Os utilizadores, grupos ou aplicações que pretende ser autorizados ao grupo de recursos geridos são identificados usando identidades do Azure Ative Directory (AD).

**Azure Ative Directory Tenanty ID** – The Azure AD Tenant ID (também conhecido como ID de diretório) contendo as identidades dos utilizadores, grupos ou aplicações a que pretende conceder permissões. Você pode encontrar o seu ID de Inquilino Azure no portal Azure, em [Propriedades para Diretório Ativo Azure.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)

**Autorizações** – Adicione o ID do objeto Azure Ative Do utilizador, grupo ou aplicação que pretende ser autorizado ao grupo de recursos geridos. Identifique o utilizador pelo seu ID principal, que pode ser encontrado na lâmina de utilizadores do [Azure Ative Directory no portal Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Para cada principal, selecione uma das funções incorporadas AZure da lista (Proprietário ou Contribuinte). A função selecionada descreverá as permissões que o principal terá sobre os recursos na subscrição do cliente. Para obter mais informações, veja [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md). Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), consulte [Começar com o RBAC no portal Azure](../../role-based-access-control/overview.md).

>[!Note]
>Embora possa adicionar até 100 autorizações por nuvem, é geralmente mais fácil criar um grupo de utilizadores do Ative Directory e especificar o seu ID no "ID principal". Isto permitir-lhe-á adicionar mais utilizadores ao grupo de gestão após a implementação do plano e reduzir a necessidade de atualizar o plano apenas para adicionar mais autorizações.

#### <a name="policy-settings"></a>Definições de política

Aplique [as Políticas Azure](../../governance/policy/overview.md) na sua aplicação gerida para especificar os requisitos de conformidade para a solução implementada. Para definições de política e o formato de valores de parâmetros, consulte [Exemplos de Política do Azure](../../governance/policy/samples/index.md). Pode configurar um máximo de cinco políticas e apenas um exemplo de cada opção Políticas. Algumas políticas requerem parâmetros adicionais. O SKU Standard é necessário para as políticas de auditoria. O Nome da Política está limitado a 50 caracteres.

**Selecione Guardar o projeto** antes de continuar.

## <a name="co-sell-with-microsoft"></a>Co-sell with Microsoft (Venda conjunta com a Microsoft)

Fornecer informações sobre o separador Co-sell é totalmente opcional para a publicação da sua oferta. É necessário obter o estatuto de Co-venda Ready e IP Co-sell Ready. As informações que fornecer serão utilizadas pelas equipas de vendas da Microsoft para saber mais sobre a sua solução ao avaliar o seu ajuste às necessidades dos clientes. Não está disponível diretamente para os clientes.

Para mais detalhes neste separador, consulte [a opção Co-venda no Partner Center](commercial-marketplace-co-sell.md).

## <a name="resell-through-csps"></a>Revender através de CSPs

Expanda o alcance da sua oferta disponibilizando-a aos parceiros do programa [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Isto permite que os revendedores vendam a sua oferta aos seus clientes e criem soluções agregadas.

**Selecione Guardar o projeto** antes de continuar.

## <a name="test-drive"></a>Unidade de teste

Crie uma demonstração (test drive) que permita que os clientes experimentem a sua oferta antes de a comprar. Para criar um ambiente de demonstração que permita aos clientes experimentarem a sua oferta por um período de tempo fixo, consulte [a Test Drive a sua oferta no mercado comercial.](test-drive.md)

Para ativar uma unidade de teste, selecione a caixa **de verificação de unidade de teste** no separador de [configuração 'Oferta'.](#test-drive) Para remover o test drive da sua oferta, limpe esta caixa de verificação.

### <a name="test-drive-technical-configuration"></a>Configuração técnica da versão de teste

- **ID da aplicação AZure AD** (obrigatório): Introduza o seu ID de aplicação Azure Ative [(AD).](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directory no menu esquerdo, selecione **as inscrições da App**, em seguida, procure o número de **ID da aplicação** listado (como 50c464d3-4930-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Detalhes da subscrição de implementação

Para permitir que a Unidade de Teste seja implantada em seu nome, crie e forneça uma assinatura Azure separada e única (não necessária para as unidades de teste Power BI).

* **ID de subscrição Azure** (obrigatório para o Azure Resource Manager e aplicações Lógica) – Introduza o ID de subscrição para garantir o acesso aos serviços de conta Azure para reportar e faturar o uso de recursos. Recomendamos que considere [criar uma subscrição Azure separada](../../billing/billing-create-subscription.md) para usar para test drives se ainda não tiver uma. Pode encontrar o seu ID de subscrição Azure iniciando sessão no [portal Azure](https://portal.azure.com/) e navegando no separador **Subscrições** do menu do lado esquerdo. A seleção do separador apresentará o seu ID de subscrição (como "a83645ac-1234-5ab6-6789-1h234g764ghty").
* **ID do inquilino Azure AD** (obrigatório) – Insira o seu ID do inquilino Azure Ative [(AD).](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directory no menu esquerdo, selecione **Propriedades**, em seguida, procure o número de ID do **diretório** listado (como 50c464d3-4930-494c-963c-1e951d15360e). Também pode procurar o ID do inquilino da sua organização usando o seu url de nome de domínio em:  [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .
* **Nome do inquilino Azure AD** (obrigatório para Dynamic 365) – Insira o seu nome Azure Ative Directory (AD). Para encontrar este nome, inscreva-se no [portal Azure,](https://portal.azure.com/)no canto superior direito o nome do seu inquilino será listado no nome da sua conta.
* **ID da aplicação AD AZure** (obrigatório) – Introduza o ID da aplicação Azure Ative [(AD).](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directory no menu de navegação à esquerda, selecione **registos de aplicações**, em seguida, procure o número **de ID da aplicação** listado (como 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure Ative Directory application cliente secret** (obrigatório) – Insira o [segredo do seu cliente de](../../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)aplicação Azure AD). Para encontrar este valor, inscreva-se no [portal Azure](https://portal.azure.com/). Selecione o separador **Azure Ative Directory** no menu de navegação à esquerda, selecione **registos de Aplicações**e, em seguida, selecione a sua aplicação de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo de cliente,** introduza uma descrição, selecione **Nunca** em **Expiração,** em seguida, escolha **Adicionar**. Certifique-se de copiar o valor antes de sair desta página.)

**Selecione Guardar o projeto** antes de continuar.

### <a name="marketplace-listing-optional"></a>Listagem de mercado (opcional)

Descreva a experiência do test drive.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Descrição** (obrigatória) – Descreva o seu test drive, o que será demonstrado, objetivos para o utilizador experimentar, funcionalidades a explorar e quaisquer informações relevantes para ajudar o utilizador a determinar se deve adquirir a sua oferta. Podem ser inseridos até 3.000 caracteres de texto neste campo. 
* **Informação de acesso** (necessária para o Azure Resource Manager e unidades de teste lógica) – Explique o que um cliente precisa de saber para aceder e utilizar este test drive. Caminhe por um cenário para usar a sua oferta e exatamente o que o cliente deve saber para aceder a funcionalidades ao longo do test drive. Podem ser inseridos até 10.000 caracteres de texto neste campo.
* **Manual do Utilizador** (obrigatório) – Uma análise aprofundada da sua experiência de test drive. O Manual do Utilizador deve cobrir exatamente o que pretende que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer questões que possam ter. O ficheiro deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.
* **Vídeos: Adicionar vídeos** (opcional) – Os vídeos podem ser carregados para o YouTube ou Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa ver um walk-through de informação para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso as funcionalidades da sua oferta e entender cenários que realçam os seus benefícios.
  * **Nome** (obrigatório)
  * **Endereço** (apenas YouTube ou Vimeo; necessário)
  * **Miniatura** (O ficheiro de imagem deve estar no formato PNG e 533 x 324 pixels).

**Selecione Guardar o projeto** antes de continuar.

## <a name="publish"></a>Publicar

Quando tiver concluído todas as secções necessárias da oferta, selecione **'Reveja' e publique** no canto superior direito do portal.

Reveja o estado de conclusão de cada secção da oferta.
    - *Não iniciada* – significa que a secção não foi tocada e precisa de ser concluída.
    - *Incompleto* – significa que a secção tem erros que precisam de ser corrigidos ou exigem mais informações a serem fornecidas. Volte para a secção(s) e atualize-a.
    - *Completo* – significa que a secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado completo antes de poder submeter a oferta.

Se esta for a primeira vez que publica esta oferta, pode fornecer instruções de teste à equipa de certificação para garantir que a sua aplicação seja testada corretamente, além de quaisquer notas suplementares úteis para entender a sua app.

**Selecione Submeter** para submeter a sua oferta para publicação. Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta estiver disponível para que você reveja e aprove.

Volte ao Partner Center e selecione **Go-live** para a oferta de publicar a sua oferta ao público (ou se uma oferta privada, para o público privado).

### <a name="errors-and-review-feedback"></a>Erros e feedback de revisão

O passo **de validação manual** no processo de publicação representa uma revisão extensiva da sua oferta e dos seus ativos técnicos associados (especialmente o modelo do Gestor de Recursos Azure), os problemas são normalmente apresentados como links de pedido de pull (PR). Uma explicação de como ver e responder a estes PRs, ver [feedback da revisão handling](./azure-apps-review-feedback.md).

Se tiver erros numa ou mais das etapas de publicação, corrija-as antes de voltar a publicar a sua oferta.

## <a name="next-steps"></a>Passos seguintes

* [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

---
title: Planeie uma oferta de aplicação Azure para o mercado comercial
description: Saiba como planear uma nova oferta de aplicações Azure para listagem ou venda no Azure Marketplace, ou através do programa Cloud Solution Provider (CSP) utilizando o portal de marketplace comercial no Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 4cb707896aa7874aa2bf287723e8a53d7d6d974c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577792"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Planeie uma oferta de aplicação Azure para o mercado comercial

Este artigo explica as diferentes opções e requisitos para a publicação de uma oferta de aplicação Azure ao mercado comercial da Microsoft.

## <a name="before-you-begin"></a>Antes de começar

Projetar, construir e testar as ofertas de aplicações Azure requer conhecimento técnico tanto da plataforma Azure como das tecnologias usadas para construir a oferta. A sua equipa de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft:

- Compreensão básica dos [Serviços Azure.](https://azure.microsoft.com/services/)
- Como [conceber e arquitetá-lo Azure aplicações.](https://azure.microsoft.com/solutions/architecture/)
- Conhecimentos de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)e [Azure Networking.](https://azure.microsoft.com/services/?filter=networking#networking)
- Conhecimento de trabalho da [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
- Conhecimento de trabalho da [JSON.](https://www.json.org/)

### <a name="technical-documentation-and-resources"></a>Documentação técnica e recursos

Reveja os seguintes recursos enquanto planeia a sua oferta de candidatura a Azure para o mercado comercial.

- [Compreenda os modelos do gestor de recursos do Azure](/azure/azure-resource-manager/templates/template-syntax)
- Guias de Início Rápido:
    - [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/)
    - [Guia de boas práticas de modelos de Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Publicar a definição da aplicação](/azure/managed-applications/publish-service-catalog-app)
    - [Implementar a aplicação do catálogo de serviços](/azure/managed-applications/deploy-service-catalog-quickstart)
- Tutoriais:
    - [Criar ficheiros de definição](/azure/managed-applications/publish-service-catalog-app)
- Amostras:
    - [CLI do Azure](/azure/managed-applications/cli-samples)
    - [Azure PowerShell](/azure/managed-applications/powershell-samples)
    - [Soluções de aplicação gerida](/azure/managed-applications/sample-projects)

Os [modelos de solução de construção de vídeo e aplicações geridas para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) dão uma introdução abrangente ao tipo de oferta de aplicação Azure:

- Que tipos de oferta estão disponíveis
- Que ativos técnicos são necessários
- Como autor de um modelo de Gestor de Recursos Azure
- Desenvolvimento e teste da UI da aplicação
- Como publicar a oferta da app
- O processo de revisão de candidaturas

### <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerir a sua aplicação Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
- [CLI do Azure](https://docs.microsoft.com/cli/azure)

Recomendamos adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

- [Explorador de Armazenamento do Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Código do Estúdio Visual](https://code.visualstudio.com/) com as seguintes extensões:
    - Extensão: [Ferramentas do gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Extensão: [Embelezamento](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Pode rever as ferramentas disponíveis na página [Azure Developer Tools.](https://azure.microsoft.com/tools/) Se estiver a utilizar o Visual Studio, consulte o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="listing-options"></a>Opções de listagem

Após a publicação da sua oferta, as opções de listagem da sua oferta aparecem como um botão no canto superior esquerdo da página de listagem da sua oferta. Por exemplo, a imagem que se segue mostra uma página de listagem de ofertas no Azure Marketplace com o botão _Get It Now._ Se tivesse optado por oferecer um test drive, o botão _Test Drive_ também seria mostrado.

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Ilustra uma página de listagem no Azure Marketplace.":::

## <a name="test-drive"></a>Unidade de teste

Pode optar por ativar um test drive para a sua oferta de Aplicação Azure que permite que os clientes experimentem a sua oferta antes de a comprar. Para saber mais sobre test drives, veja [o que é um test drive?](what-is-test-drive.md) Para obter informações sobre a configuração de diferentes tipos de test drives, consulte a [configuração técnica do test drive](test-drive-technical-configuration.md).

Também pode ler sobre [as melhores práticas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) de test drive e baixar o [PDF de unidades de teste](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (certifique-se de que o seu bloqueador pop-up está desligado).

> [!NOTE]
> Informação que o utilizador deve notar mesmo que skimmingSescamos todas as aplicações Azure são implementadas usando um modelo de Gestor de Recursos Azure, o único tipo de unidade de teste disponível para uma [Aplicação Azure é uma unidade de teste baseada em Recursos Azure.](azure-resource-manager-test-drive.md)

## <a name="customer-leads"></a>Ligações ao cliente

Tem de ligar a sua oferta ao seu sistema de gestão de relacionamento com o cliente (CRM) para recolher informações do cliente. O cliente será solicitado permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e loja online onde encontraram a sua oferta, serão enviados para o sistema CRM que configuraste. O mercado comercial suporta uma variedade de sistemas CRM, juntamente com a opção de usar uma tabela Azure ou configurar um ponto final HTTPS utilizando o Power Automate.

Pode adicionar ou modificar uma ligação CRM a qualquer momento durante ou após a criação da oferta. Para obter orientações detalhadas, consulte [os leads do Cliente da sua oferta de mercado comercial.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

## <a name="categories-and-subcategories"></a>Categorias e subcategorias

Pode escolher pelo menos uma e até duas categorias para agrupar a sua oferta nas áreas de pesquisa de mercado comercial apropriadas. Pode escolher até duas subcategorias para cada categoria primária e secundária. Para obter uma lista completa de categorias e subcategorias, consulte [Oferta Listando As Melhores Práticas](gtm-offer-listing-best-practices.md#categories).

## <a name="legal-contracts"></a>Contratos legais

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para os fornecedores de software, a Microsoft oferece um contrato padrão que pode usar para as suas ofertas no mercado comercial. Quando oferece o seu software ao abrigo do contrato padrão, os clientes só precisam de o ler e aceitar uma vez, e não tem de criar termos e condições personalizados.

Se optar por utilizar o contrato padrão, tem a opção de adicionar termos de emenda universal e até 10 alterações personalizadas ao contrato padrão. Também pode usar os seus próprios termos e condições em vez do contrato padrão. Você vai gerir estes detalhes na página **Propriedades.** Para obter informações detalhadas, consulte [o contrato Standard para o mercado comercial da Microsoft.](standard-contract.md)

> [!NOTE]
> Depois de publicar uma oferta utilizando o contrato padrão para o mercado comercial, não pode utilizar os seus próprios termos e condições personalizados. É um cenário "ou". Ou oferece a sua solução de acordo com o contrato padrão ou os seus próprios termos e condições. Se quiser modificar os termos do contrato padrão, pode fazê-lo através de Alterações Padrão contratuais.

## <a name="offer-listing-details"></a>Oferecer detalhes da listagem

Quando criar uma nova oferta de aplicação Azure no Partner Center, introduzirá texto, imagens, vídeos opcionais e outros detalhes na página de listagem da Oferta. Esta é a informação que os clientes verão quando descobrirem a sua oferta no Azure Marketplace, como mostra o exemplo seguinte.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

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

A imagem que se segue mostra como as informações da oferta aparecem no portal Azure:

[![Ilustra como esta oferta aparece no portal Azure.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Título
2. Descrição
3. Ligações úteis
4. Capturas de ecrã

> [!NOTE]
> O conteúdo da listagem de ofertas não é necessário para estar em inglês se a descrição da oferta começar com a frase "Esta aplicação está disponível apenas em [língua não inglesa]".

Para ajudar a criar a sua oferta mais facilmente, prepare alguns destes itens com antecedência. Os seguintes itens são necessários, salvo indicação em contrário.

- **Nome** : Este nome aparecerá como o título da sua listagem de oferta no mercado comercial. O nome pode ser marcado. Não pode conter emojis (a menos que sejam símbolos de marca e direitos de autor) e deve ser limitado a 50 caracteres.
- Resumo dos **resultados** da pesquisa : Descreva a finalidade ou função da sua oferta como uma única frase, em texto simples sem quebras de linha, em 100 caracteres ou menos. Este resumo é utilizado nos resultados de pesquisa de anúncios de mercado comercial.
- **Descrição curta** : Forneça até 256 caracteres de texto simples. Este resumo aparecerá na página de detalhes da sua oferta.
- **Descrição** : Esta descrição será exibida na lista(s) lista geminada do Azure Marketplace. Considere incluir uma proposta de valor, benefícios-chave, base de utilizadores pretendida, qualquer categoria ou associação do setor, oportunidades de compra na aplicação, necessidade ou dor que a oferta endereço, quaisquer divulgações necessárias, e um link para aprender mais.

    Esta caixa de texto tem controlos de editores de texto ricos que pode usar para tornar a sua descrição mais envolvente. Também pode utilizar tags HTML para formatar a sua descrição. Pode introduzir até 3.000 caracteres de texto nesta caixa, que inclui marcação HTML e espaços. Para obter dicas adicionais, consulte [Escrever uma excelente descrição da aplicação](/windows/uwp/publish/write-a-great-app-description) e [as tags HTML suportadas no mercado comercial oferecem descrições.](supported-html-tags.md)

- **Procurar palavras-chave** (opcional): Forneça até três palavras-chave de pesquisa que os clientes podem usar para encontrar a sua oferta na loja online. Para obter melhores resultados, utilize também estas palavras-chave na sua descrição. Não precisa de incluir a oferta **Nome** e **Descrição.** Este texto é automaticamente incluído na pesquisa.
- **Ligação de política de privacidade** : O URL para a política de privacidade da sua empresa. Você deve fornecer uma política de privacidade válida e é responsável por garantir que a sua aplicação está em conformidade com as leis e regulamentos de privacidade.
- **Links úteis** (opcional): Pode fornecer links para vários recursos para os utilizadores da sua oferta. Por exemplo, fóruns, PERGUNTAS Frequentes e notas de lançamento.
- **Informações de contacto** : Deve designar os seguintes contactos da sua organização:
  - **Contacto de suporte** : Forneça o nome, telefone e e-mail para os parceiros da Microsoft usarem quando os seus clientes abrirem os bilhetes. Também deve incluir o URL para o seu site de suporte.
  - **Contacto de engenharia** : Forneça o nome, telefone e e-mail para que a Microsoft utilize diretamente quando houver problemas com a sua oferta. Esta informação de contacto não está listada no mercado comercial.
  - **Contacto do Programa CSP** (opcional): Forneça o nome, telefone e e-mail se optar pelo programa Cloud Solution Provider (CSP), para que esses parceiros possam contactá-lo com quaisquer questões. Também pode incluir um URL nos seus materiais de marketing.
- **Mídia – Logotipos** : Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center utilizará isto para criar um logótipo **Pequeno** e **Médio.** Pode substituir opcionalmente estas imagens por diferentes imagens mais tarde.
  - Grande (de 216 x 216 a 350 x 350 px, necessário)
  - Médio (90 x 90 px, opcional)
  - Pequeno (48 x 48 px, opcional)

  Estes logótipos são utilizados em diferentes locais nas lojas online:
  - O logótipo Small aparece nos resultados de pesquisa do Azure Marketplace.
  - O logótipo Medium aparece quando cria um novo recurso no Microsoft Azure.
  - O logotipo Large aparece na sua página de listagem de oferta no Azure Marketplace.

  Siga estas diretrizes para os seus logotipos:

  - O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias no logótipo.
  - As cores do tema do portal são preto e branco. Não utilize estas cores como cor de fundo do logótipo. Utilize uma cor que realce o logótipo no portal. Recomendamos cores primárias simples.
  - Se utilizar um fundo transparente, certifique-se de que o logótipo e o texto não são brancos, pretos ou azuis.
  - O aspeto e a sensação do seu logotipo devem ser planos e evitar gradientes no logotipo ou no fundo. Não coloque texto no logótipo, nem mesmo o nome da empresa ou da marca. Imagens desfocadas farão com que a sua submissão seja rejeitada.
  - Certifique-se que o logótipo não está ampliado.

- **Meios de comunicação - Screenshots** (opcional): Recomendamos que adicione imagens que mostrem como a sua oferta funciona. Pode adicionar até cinco imagens com os seguintes requisitos, que mostram como a sua oferta funciona:
  - 1280 x 720 pixels
  - .png ficheiro
  - Deve incluir uma legenda
- **Meios – Vídeos** (opcional): Pode adicionar até cinco vídeos com os seguintes requisitos, que demonstram a sua oferta:
  - Name
  - URL: Deve ser hospedado apenas no YouTube ou Vimeo.
  - Miniatura: ficheiro de .png 1280 x 720

> [!NOTE]
> A sua oferta deve atender às [políticas gerais de certificação do mercado comercial](/legal/marketplace/certification-policies#100-general.md) a publicar no mercado comercial.

## <a name="preview-audience"></a>Audiência de pré-visualização

Um público de pré-visualização pode aceder à sua oferta antes de ser publicado ao vivo nas lojas online de forma a testar a funcionalidade de ponta a ponta antes de a publicar ao vivo.

> [!NOTE]
> Um público de pré-visualização difere de um plano privado. Um plano privado é aquele que disponibiliza apenas para um público específico que escolher. Isto permite-lhe negociar um plano personalizado com clientes específicos.

Você define o público de pré-visualização usando IDs de subscrição Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes.

## <a name="technical-configuration"></a>Configuração técnica

Para aplicações geridas que emitem eventos de medição utilizando o [serviço de medição do Marketplace APIs,](partner-center-portal/marketplace-metering-service-apis.md)deve fornecer a identidade que o seu serviço utilizará ao emitir eventos de medição.

Esta configuração é necessária se pretender utilizar o [evento de utilização do Lote](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event). Caso pretenda submeter [o evento de utilização,](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event)também pode utilizar o serviço de [metadados de exemplo](/azure/active-directory/managed-identities-azure-resources/overview) para obter o [token web JSON (JWT).](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)

- **ID do inquilino do Azure Ative Directory** (obrigatório): Dentro do portal Azure, você deve [criar uma app Azure Ative Directory (AD)](/azure/active-directory/develop/howto-create-service-principal-portal) para que possamos validar a ligação entre os nossos dois serviços está por trás de uma comunicação autenticada. Para encontrar o [ID](/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-and-app-id-values-for-signing-in) do inquilino para a sua aplicação Azure Ative Directory (Azure AD), para a lâmina [de registos](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App no seu Diretório Ativo Azure. Na coluna **'Mostrar' o nome,** selecione a aplicação. Em seguida, procure **propriedades** , e depois para o **Diretório (inquilino) ID** (por `50c464d3-4930-494c-963c-1e951d15360e` exemplo).
- ID de aplicação do **Azure Ative (obrigatório):** Também precisa do seu [ID de aplicação](/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-and-app-id-values-for-signing-in.md) e de uma chave de autenticação. Para encontrar o seu ID de aplicação, vá à lâmina de [registos](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App em que é o seu Diretório Ativo Azure. Na coluna **'Nome do Visor',** selecione a aplicação e, em seguida, procure o **ID da Aplicação (cliente)** (por `50c464d3-4930-494c-963c-1e951d15360e` exemplo). Para encontrar a chave de autenticação, vá a **Definições** e selecione **Teclas**. Terá de fornecer uma descrição e duração e, em seguida, será fornecido um valor numer numer.

> [!NOTE]
> O ID da aplicação Azure será associado ao ID da sua editora e só pode ser reutilizado dentro desta conta de editor.

## <a name="additional-sales-opportunities"></a>Oportunidades de venda adicionais

Pode optar por optar por canais de marketing e vendas suportados pela Microsoft. Ao criar a sua oferta no Partner Center, verá dois separadores no final do processo:

- **Revender através de CSPs** : Utilize esta opção para permitir que os parceiros da Microsoft Cloud Solution Providers (CSP) revendam a sua solução como parte de uma oferta agregada. Consulte [o programa Cloud Solution Provider](/azure/marketplace/cloud-solution-providers) para obter mais informações.
- **Co-vender com** a Microsoft: Esta opção permite que as equipas de vendas da Microsoft considerem a sua solução elegível para co-venda ip ao avaliar as necessidades dos seus clientes. Consulte [a opção de Co-venda no Partner Center](partner-center-portal/commercial-marketplace-co-sell.md) para obter informações detalhadas sobre como preparar a sua oferta para avaliação. Para obter mais informações sobre o marketing da sua oferta através dos canais parceiros da Microsoft CSP, consulte [os Fornecedores de Soluções Cloud.](cloud-solution-providers.md)

Para saber mais, veja [o Crescimento do seu negócio na nuvem com o Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)

## <a name="plans"></a>Planos

As ofertas da Azure Application requerem pelo menos um plano. Um plano define o âmbito e os limites da solução, e os preços associados, se aplicável. Pode criar vários planos para a sua oferta para dar aos seus clientes diferentes opções técnicas e de preços.

Para orientações gerais sobre planos, incluindo modelos de preços e planos [privados, consulte planos e preços para ofertas de mercado comercial.](plans-pricing.md) As secções seguintes discutem informações adicionais específicas dos planos de aplicação da Azure.

### <a name="types-of-plans"></a>Tipos de planos

Existem dois tipos de planos de aplicação Azure: _modelo de solução_ e _aplicação gerida._ Ambos os tipos de planos suportam automatizar a implementação e configuração de uma solução para além de uma única máquina virtual (VM). Pode automatizar o processo de disponibilização de múltiplos recursos, incluindo VMs, networking e recursos de armazenamento para fornecer soluções complexas, como soluções IaAS. Ambos os tipos de planos podem empregar muitos tipos diferentes de recursos Azure, incluindo, mas não se limitando a VMs.

- **Os** planos de modelo de solução são uma das principais formas de publicar uma solução no mercado comercial. Os planos de modelo de solução não são transacionáveis no mercado comercial, mas podem ser usados para implementar ofertas de VM pagas que são faturadas através do mercado comercial. Utilize o tipo de plano de modelo de solução quando o cliente irá gerir a solução e as transações são faturadas através de outro plano. Para obter mais informações sobre modelos de solução de construção, consulte [O que é O Gestor de Recursos Azure?](/azure/azure-resource-manager/resource-group-overview)
- Os planos **de aplicação geridos** permitem-lhe construir e entregar aplicações chave na mão e geridas para os seus clientes. Têm as mesmas capacidades que os planos de modelo de solução, com algumas diferenças fundamentais:
    - Os recursos são implantados para um grupo de recursos e são geridos pelo editor da app. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos. 
    - Como editor, especifica o custo para o apoio contínuo da solução e as transações são suportadas através do mercado comercial.
 
    Utilize o tipo de plano de aplicação gerido quando você ou o seu cliente requer que a solução seja gerida por um parceiro ou que implemente uma solução baseada em subscrição. Para obter mais informações sobre as vantagens e tipos de aplicações geridas, consulte [a visão geral das aplicações geridas pela Azure.](/azure/managed-applications/overview)

## <a name="next-steps"></a>Passos seguintes

- Para planear um modelo de solução, consulte [Plano um modelo de solução para uma oferta de aplicação Azure](plan-azure-app-solution-template.md).
- Para planear uma aplicação gerida pela Azure, consulte [o Plano de um pedido gerido pela Azure para uma oferta de candidatura da Azure](plan-azure-app-managed-app.md).

---
title: Criar uma nova oferta de Apps Azure no Mercado Comercial
description: Como criar uma nova oferta de Aplicações Azure para listagem ou venda no Azure Marketplace, AppSource ou através do programa Cloud Solution Provider (CSP) utilizando o portal Mercado Comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 0ff1bbd976273a7d0cbfb22effebdf45c84d2f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277245"
---
# <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicações do Azure

As etapas para a publicação de uma oferta de aplicação Azure no mercado comercial estão aqui descritas.

## <a name="azure-application-offer-type"></a>Tipo de oferta de aplicação Azure

Este tópico descreve os fundamentos sobre as ofertas de aplicação azure.  Deve estar familiarizado com estes conceitos antes de iniciar o processo de publicação de uma nova oferta de aplicação Azure no Marketplace.

### <a name="publishing-overview"></a>Visão geral de publicação

Os modelos de solução de construção de vídeo [e aplicações geridas para o Mercado Azure](https://channel9.msdn.com/Events/Build/2018/BRK3603) é uma introdução ao tipo de oferta de aplicação Azure:

* Que tipos de oferta estão disponíveis;
* Que ativos técnicos são necessários;
* Como autoria de um modelo de Gestor de Recursos Azure;
* Desenvolver e testar a aplicação UI;
* Como publicar a oferta da aplicação;
* O processo de revisão da aplicação.

### <a name="types-of-azure-application-plans"></a>Tipos de planos de aplicação Azure

Existem dois tipos de planos de aplicação Azure, aplicações geridas e modelos de solução.

* O modelo de **solução** é uma das principais formas de publicar uma solução no Mercado. Este tipo de plano é utilizado quando a sua solução requer uma implementação adicional e automatização de configuração para além de uma única máquina virtual (VM).  Com um modelo de solução, pode automatizar o fornecimento de mais de um recurso, incluindo VMs, networking e recursos de armazenamento para fornecer soluções iaaS complexas.  Para obter mais informações sobre os modelos de solução de construção, consulte a documentação do [Gestor de Recursos Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

* **A aplicação gerida** é semelhante aos modelos de solução, com uma diferença fundamental. Numa aplicação gerida, os recursos são implementados num grupo de recursos que é gerido pelo editor da aplicação. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos. Como editor, tem de especificar o custo do suporte contínuo da solução. Utilize aplicações geridas para construir e entregar aplicações chave na mão totalmente geridas aos seus clientes.  Para obter mais informações sobre as vantagens e tipos de aplicações geridas, consulte a visão geral das [aplicações geridas pelo Azure.](https://docs.microsoft.com/azure/managed-applications/overview)

Todas as aplicações do Azure incluem pelo `.zip` menos dois ficheiros na pasta raiz de um arquivo:

* Um ficheiro de modelo de gestor de recursos chamado [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Este é o modelo que define os recursos para implantar na subscrição do Cliente Azure.  Por exemplo, nos modelos de Gestor de Recursos, consulte a [galeria de modelos De Quickstart Azure](https://azure.microsoft.com/resources/templates/) ou o [correspondente GitHub: Modelos quickstart do Gestor](https://github.com/azure/azure-quickstart-templates) de Recursos Azure repo.

* Uma definição de interface de utilizador para a experiência de criação de aplicações Azure chamada [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Na interface de utilizador, especifique os elementos que permitem aos consumidores fornecer valores de parâmetros.

Todas as novas ofertas de aplicações Azure devem incluir uma atribuição de uso de [cliente parceiro Azure GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution).

### <a name="before-you-begin"></a>Antes de começar

Reveja a seguinte documentação da aplicação Azure, que fornece Quickstarts, Tutoriais e Amostras.

* [Compreender os modelos de gestor de recursos azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Guias de Início Rápido:

    * [Modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates/)
    * [Modelos GitHub Azure Quickstart](https://github.com/azure/azure-quickstart-templates)
    * [Publicar a definição da aplicação](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [Implementar a aplicação do catálogo de serviços](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Tutoriais:

    * [Criar ficheiros de definição](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publicar a aplicação do mercado](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Exemplos:

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Soluções de aplicação gerida](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Fundamentos do conhecimento técnico

Conceber, construir e testar estes ativos leva tempo e requer conhecimentos técnicos tanto da plataforma Azure como das tecnologias utilizadas para construir a oferta.

A sua equipa de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

* Compreensão básica dos [Serviços Azure.](https://azure.microsoft.com/services/)
* Como [conceber e arquiteto sintetizar aplicações Azure.](https://azure.microsoft.com/solutions/architecture/)
* Conhecimento de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage#storage)e [Networking Azure.](https://azure.microsoft.com/services/?filter=networking#networking)
* Conhecimento de trabalho do Gestor de [Recursos Azure.](https://azure.microsoft.com/features/resource-manager/)
* Conhecimento de trabalho da [JSON.](https://www.json.org/)

### <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de scripts para ajudar a gerir a sua aplicação Azure:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Recomendamos adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Código de Estúdio Visual](https://code.visualstudio.com/) com as seguintes extensões:
    * Extensão: Ferramentas de [Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extensão: [Embelezar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Pode rever as ferramentas disponíveis na página Ferramentas de [Desenvolvimento Do Azure.](https://azure.microsoft.com/tools/)  Também se estiver a usar o Visual Studio, o [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="create-an-azure-application-offer"></a>Criar uma oferta de aplicações do Azure

Antes de poder criar uma oferta de aplicação Azure, primeiro deve [criar uma conta partner center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) e abrir o dashboard Do Mercado [Comercial,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)com o separador **Overview** selecionado.

>[!Note]
>Uma vez publicada uma oferta, as edas para a oferta feita no Partner Center só serão atualizadas no sistema e nas montras após a republicação.  Certifique-se de que submete a oferta para publicação depois de serem feitas alterações.

### <a name="create-a-new-offer"></a>Criar uma nova oferta

Selecione o botão **+ Nova oferta** e, em seguida, selecione o item do menu **'Aplicação Azure'.** A nova caixa de diálogo da **oferta** aparecerá.

### <a name="offer-id-and-alias"></a>Oferecer ID e pseudónimo

* **ID oferta**: Um identificador único para cada oferta na sua conta. Este ID será visível para os clientes no endereço URL para a oferta de mercado e modelos do Gestor de Recursos Azure (se aplicável). <br> <br> O ID da oferta deve ser caracteres alfanuméricos minúsculos (incluindo hífenes e sublinhados, mas sem espaço branco). Está limitado a 50 caracteres e não pode ser alterado depois de selecionar Criar. <br> <br> Por exemplo, se `test-offer-1` você entrar aqui, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`o URL de oferta será . 

* **Oferta pseudónimo**: O nome usado para se referir à oferta dentro do Partner Center. Este nome não será usado no mercado e é diferente do nome da oferta e outros valores que serão mostrados aos clientes. Este valor não pode ser alterado depois de selecionar **Criar**.

Depois de ter introduzido o seu **'Id' de oferta** e **do seu pseudónimo,** selecione **Criar**. Em seguida, poderá trabalhar em todas as outras partes da sua oferta.

## <a name="offer-setup"></a>Configuração de oferta

A página de **configuração da Oferta** pede as seguintes informações. Certifique-se de selecionar **Guardar** depois de completar estes campos.

### <a name="test-drive"></a>Test drive

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de "tentar antes de comprar", resultando numa conversão acrescida e na geração de leads altamente qualificados. [Saiba mais sobre test drives.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para ativar uma unidade de teste, verifique a **caixa de acionamento ativa.** Em seguida, terá de configurar um ambiente de demonstração na [configuração técnica](#types-of-azure-application-plans) test drive para permitir que os clientes experimentem a sua oferta por um período de tempo fixo. 

>[!Note]
>Como todas as aplicações Azure são implementadas usando um modelo de Gestor de Recursos Azure, o único tipo de test drive que pode ser configurado para uma Aplicação Azure é um [test drive baseado em Recursos Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

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

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Padrão para o mercado comercial da Microsoft

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Oferta listagem

A página de listagem de oferta seleções mostra os idiomas em que a sua oferta será listada. Atualmente, **o inglês (Estados Unidos)** é a única opção disponível.

Terá de definir detalhes do mercado (nome, descrição, imagens, etc.) para cada idioma/mercado. Selecione o nome idioma/mercado para fornecer esta informação.

> [!NOTE]
> Oferecer conteúdo de listagem (como a descrição, documentos, screenshots, termos de uso, etc.) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação só está disponível em [língua não inglesa]." Também é aceitável fornecer um URL de *Link Útil* para oferecer conteúdo num idioma diferente do utilizado no conteúdo de listagem de oferta.

### <a name="name"></a>Nome

O nome que introduzir aqui será mostrado aos clientes como título da sua oferta. Este campo é pré-povoado com o texto que inscreveu para **o pseudónimo oferta** quando criou a oferta, mas pode alterar este valor. Este nome pode ser marcado por marca (e pode incluir símbolos de marca ou direitos de autor). O nome não pode ser mais de 50 caracteres e não pode incluir emojis.

### <a name="summary"></a>Resumo

Forneça uma breve descrição da sua oferta (até 100 caracteres), que pode ser usada em resultados de pesquisa de mercado.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta (até 256 caracteres). A descrição pode ser utilizada nos resultados da pesquisa no mercado.

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

### <a name="search-keywords"></a>Pesquisar palavras-chave

Pode inserir opcionalmente até três palavras-chave de pesquisa para ajudar os clientes a encontrar a sua oferta no mercado. Para obter os melhores resultados, tente utilizar estas palavras-chave na sua descrição também.

### <a name="support-urls"></a>URLs de suporte

Esta secção permite-lhe fornecer links para ajudar os clientes a entender mais sobre a sua oferta.

#### <a name="privacy-policy-url"></a>URL de política de privacidade

Insira o URL na política de privacidade da sua organização. É responsável por garantir que a sua aplicação cumpre as leis e regulamentos de privacidade e por fornecer uma política de privacidade válida.

#### <a name="useful-links"></a>Ligações úteis

Forneça documentos online suplementares opcionais sobre a sua solução.  Adicione links úteis adicionais clicando **+ Adicione um link**.

### <a name="contacts"></a>Contactos

Nesta secção, deve fornecer o nome, e-mail e número de telefone para um contacto de **suporte** e um contacto de **Engenharia**. Esta informação não é mostrada aos clientes, mas estará disponível para a Microsoft, podendo ser fornecida aos parceiros CSP.

Na secção de contacto de **suporte,** deve também fornecer o URL de **Suporte** onde os parceiros de CSP podem encontrar suporte para a sua oferta.

### <a name="marketplace-images"></a>Imagens do mercado

Nesta secção, pode fornecer logótipos e imagens que serão utilizados ao mostrar a sua oferta ao cliente. Todas as imagens devem estar em formato .png.

#### <a name="store-logos"></a>Logotipos de loja

Forneça o logótipo da sua oferta em três tamanhos: **Pequeno (48 x 48)**, **Médio (90 X 90)** e **Grande (216 x 216)**.

#### <a name="hero"></a>Herói

A imagem de herói é opcional. Se fornecer um, deve medir 815 x 290 pixels.

#### <a name="screenshots"></a>Capturas de Ecrã

Adicione imagens que mostrem como funciona a sua oferta. Pode adicionar até cinco imagens. Todas as imagens devem ser de 1280 x 720 pixels.

#### <a name="videos"></a>Vídeos

Pode adicionar opcionalmente cinco vídeos que demonstram a sua oferta. Estes vídeos devem ser hospedados no YouTube e/ou vimeo. Para cada um, introduza o nome do vídeo, o seu URL e uma imagem de miniatura do vídeo (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de mercado

- [Boas práticas para anúncios de oferta de mercado](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Pré-visualização

O separador **Preview** permite-lhe definir um Público de **Pré-visualização** limitado para validar a sua oferta antes de publicar a sua oferta ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Você deve selecionar **Ir ao vivo** antes que a sua oferta seja publicada em direto para o público do mercado depois de verificar a sua oferta em Preview.

O seu público de pré-visualização é identificado por GUIDs de assinatura Azure, emparelhadocom uma descrição opcional para cada um.  Nenhum destes campos é visível para os clientes.

Adicione manualmente até 10 IDs de subscrição Azure, ou até 100 se fizer o upload de um ficheiro CSV.  Ao adicionar estas subscrições, está a definir um público que será permitido aceder à sua oferta antes de ser totalmente publicado.  Se a sua oferta já estiver ao vivo, poderá ainda definir um público de pré-visualização para testar quaisquer alterações ou atualizações da sua oferta.

>[!Note]
>O público de pré-visualização difere de um público privado. Um público pré-visualizado é permitido o acesso à sua oferta *antes* de ser publicado ao vivo nos mercados. Pode também optar por criar um plano e disponibilizá-lo apenas a um público privado (utilizando o separador Disponibilidade do plano).  O seu público de pré-visualização poderá ver e validar todos os planos, incluindo planos que estarão disponíveis apenas para um público privado assim que a sua oferta for totalmente publicada no mercado.

## <a name="plan-overview"></a>Visão geral do plano

O separador **de visão geral** do Plano permite-lhe fornecer diferentes opções de plano dentro da mesma oferta. Estes planos (referidos como SKUs no Portal do Parceiro cloud) podem diferir em termos de tipo de plano (modelo de solução vs. aplicação gerida), rentabilização ou público.  Configure pelo menos um plano para listar a sua oferta no mercado.

Uma vez criado, você verá os nomes do seu plano, IDs, tipo de plano, disponibilidade (Público ou Privado), estado de publicação atual, e quaisquer ações disponíveis neste separador.

**As ações** disponíveis na **visão geral** do Plano variam consoante o estado atual do seu plano e podem incluir:

* Se o estado do plano for **Rascunho** – Apagar o projeto.
* Se o estado do plano for **Live** – Pare de vender o plano ou sincronizar o público privado.

### <a name="create-new-plan"></a>Criar novo plano

***Id do plano*** - Crie um id de plano único para cada plano nesta oferta. Este ID será visível para os clientes no URL do produto.  Use apenas caracteres minúsculos, alfanuméricos, traços ou sublinhados. Um máximo de 50 caracteres são permitidos para este plano id. Este ID não pode ser modificado após a seleção criar.

***Nome*** do plano - Os clientes verão este nome ao decidir qual o plano a selecionar dentro da sua oferta. Crie um nome de oferta único para cada plano nesta oferta. O nome do plano é usado para diferenciar planos de software que podem fazer parte da mesma oferta (Por exemplo. Nome da oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configuração do plano

O separador de **configuração do Plano** permite-lhe definir a configuração de alto nível para o tipo de plano, quer reutilize pacotes de outro plano, e em que nuvens o plano deve estar disponível.  As suas respostas neste separador afetarão quais os campos apresentados em outros separadores para o mesmo plano.

#### <a name="plan-type"></a>Tipo de plano

Tal como descrito nos [planos de aplicação Do Azure,](#types-of-azure-application-plans)selecione se o seu plano conterá um modelo de solução ou uma aplicação gerida.

#### <a name="this-plan-reuses-packages"></a>Este plano reutiliza pacotes

Se tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, poderá selecionar **este plano reutilizar pacotes de outro plano**.  Ao selecionar esta opção, poderá selecionar um dos outros planos do mesmo tipo para esta oferta para reutilizar pacotes. 

>[!Note]
>Quando reutilizar os pacotes de outro plano, todo o separador de configuração técnica desaparecerá deste plano.  Os detalhes técnicos de configuração do outro plano, incluindo quaisquer atualizações que fizer no futuro, também serão utilizados para este plano. <br> <br> Além disso, esta definição não pode ser alterada uma vez que este plano seja publicado.

#### <a name="cloud-availability"></a>Disponibilidade de nuvem

Este plano deve ser disponibilizado em pelo menos uma nuvem. 

Selecione a opção **Public Azure** para tornar a sua solução implantável para clientes em todas as regiões públicas do Azure que tenham integração no Marketplace.  Saiba mais sobre [disponibilidade geográfica.](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)

Selecione a opção **Azure Government Cloud** para tornar a sua solução implantável na [Nuvem do Governo Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), uma nuvem governamental-comunidade com acesso controlado para clientes do Estado Federal, Estadual, local ou tribal dos EUA e parceiros elegíveis para servir estas entidades.  Você, como editor, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas para servir esta comunidade de nuvem.  O Governo azure utiliza centros e redes de dados fisicamente isolados (localizados apenas nos EUA).  Antes de publicar no [Governo Do Azure,](https://aka.ms/azuregovpublish)a Microsoft recomenda que teste e valide a sua solução no ambiente, uma vez que determinados pontos finais podem diferir. Para encenar e testar a sua solução, solicite uma conta de teste a partir deste [link](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>Uma vez que um plano é publicado como disponível numa nuvem específica, essa nuvem não pode ser removida.

**Certificações Azure Government Cloud**

Esta opção só é visível se **a Nuvem governamental Azure** for selecionada sob **disponibilidade**cloud .

Os serviços do Governo azure tratam dados que estão sujeitos a certos regulamentos e requisitos governamentais, tais como FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.  Para sensibilizar as suas certificações para estes programas, pode fornecer até 100 links descrevendo as suas certificações.  Estes links podem ser links para a sua listagem diretamente no programa, ou um links para descrições da sua conformidade com os mesmos nos seus próprios websites.  Estas ligações só serão visíveis para os clientes da Azure Government Cloud.

## <a name="plan-listing"></a>Listagem de planos

O separador de listagem de **planos** exibe as informações de listagem específicas do plano que podem ser diferentes entre diferentes planos para a mesma oferta.

### <a name="name"></a>Nome

Pré-povoado com base no seu nome, atribuiu o seu plano quando o criou.  Este nome aparecerá como o título deste "plano de software" apresentado no mercado.  Pode conter até 100 caracteres.

### <a name="summary"></a>Resumo

Forneça um breve resumo do seu plano de software.  Pode conter até 100 caracteres.

### <a name="description"></a>Descrição

Esta descrição é uma oportunidade para explicar o que torna este plano de software único e quaisquer diferenças de outros planos de software dentro da sua oferta. Pode conter até 2.000 caracteres.

Selecione **Guardar** depois de completar estes campos.

## <a name="availability"></a>Disponibilidade

O separador **Disponibilidade** é visível apenas para planos de modelo de solução.  Pode tornar o plano visível para todos, apenas para clientes específicos (um público privado), e se deve tornar o plano escondido para ser usado apenas por outro modelo de solução ou aplicações geridas.

### <a name="plan-audience"></a>Audiência de Plano

Tem a opção de configurar cada plano para ser visível para todos ou apenas para um público específico à sua escolha. Você pode atribuir adesão a este público restrito usando IDs de subscrição Azure.

**Privacidade / Este é um plano privado** (Caixa de verificação opcional) - Verifique esta caixa para tornar o seu plano privado e visível apenas para o público restrito à sua escolha. Uma vez publicado como um plano privado, pode atualizar o público ou optar por disponibilizar o plano a todos. Uma vez que um plano é publicado como visível para todos, deve permanecer visível para todos. (O plano não pode ser configurado novamente como um plano privado).

**Audiência Restrita (IDs de subscrição Azure)** - Atribua o público que terá acesso a este plano privado. O acesso é atribuído utilizando IDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição Azure atribuído. Pode adicionar um máximo de 10 IDs por subscrição, ou 20.000 iDs de subscrição de clientes se importar um ficheiro de folha de cálculo .csv.  Os IDs de subscrição azure são representados como GUIDs, e as letras devem ser minúsculas.

>[!Note]
>O público privado (ou público restrito) difere do público de pré-visualização que definiu no separador [**Preview.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Um público pré-visualizado é permitido o acesso à sua oferta *antes* da oferta ser publicada ao vivo no mercado. Embora a designação do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos (privados ou não) para fins de validação.

### <a name="hide-plan"></a>Plano de ocultação

Se o seu modelo de solução se destina a ser implantado apenas indiretamente quando referenciado apesar de outro modelo de solução ou aplicação gerida, verifique esta caixa para publicar o seu modelo de solução, mas esconda-o dos clientes que procuram e navegam diretamente para ele.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

O separador **de preços e disponibilidade** é visível apenas para planos de aplicação geridos.  Pode configurar os mercados em que este plano estará disponível, o preço por mês da gestão da solução, e se tornar o plano visível para todos ou apenas para clientes específicos (um público privado).

### <a name="markets"></a>Mercados

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de verificação para qualquer local de mercado onde pretenda disponibilizar este plano. Uma caixa de pesquisa e um botão para selecionar países "Tax Remitted", no qual a Microsoft atribui as vendas e o imposto usado em seu nome, estão incluídos para ajudar.

Se já definiu preços para o seu plano em Dólares dos Estados Unidos (USD) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as taxas de câmbio atuais. Reveja sempre o preço de cada mercado antes de publicar. Os preços podem ser revistos utilizando o link "Preços de Exportação (xlsx)" depois de guardar as suas alterações.

### <a name="pricing"></a>Preços

Forneça o preço por mês para este plano.  Este preço além de qualquer infraestrutura Azure ou custos de software pay-as-you-go incorridos pelos recursos implementados por esta solução.

Os preços estabelecidos em moeda local (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados utilizando as taxas de câmbio atuais disponíveis durante a configuração. Valide estes preços antes de publicar exportando a folha de cálculo dos preços e revendo o preço em cada mercado. Se quiser definir preços personalizados num mercado individual, modifique e importe a folha de cálculo dos preços. 

>[!Note]
>Primeiro, tem de poupar as alterações de preços para permitir a exportação de dados de preços.

Reveja cuidadosamente os seus preços antes de publicar, uma vez que existem algumas restrições sobre o que pode mudar após a publicação de um plano.  

>[!Note]
>Uma vez publicado um preço por um mercado no seu plano, não pode ser alterado mais tarde.

### <a name="plan-audience"></a>Audiência de Plano

Tem a opção de configurar cada plano para ser visível para todos ou apenas para um público específico à sua escolha. Você pode atribuir adesão a este público restrito usando IDs de subscrição Azure.

**Privacidade / Este é um plano privado** (Caixa de verificação opcional) - Verifique esta caixa para tornar o seu plano privado e visível apenas para o público restrito à sua escolha. Uma vez publicado como um plano privado, pode atualizar o público ou optar por disponibilizar o plano a todos. Uma vez que um plano é publicado como visível para todos, deve permanecer visível para todos. (O plano não pode ser configurado novamente como um plano privado).

**Audiência Restrita (IDs de subscrição Azure)** - Atribua o público que terá acesso a este plano privado. O acesso é atribuído utilizando IDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição Azure atribuído. Pode adicionar um máximo de 10 IDs por subscrição, ou 20.000 iDs de subscrição de clientes se importar um ficheiro de folha de cálculo .csv.  Os IDs de subscrição azure são representados como GUIDs, e as letras devem ser minúsculas.

>[!Note]
>O público privado (ou público restrito) difere do público de pré-visualização que definiu no separador [**Preview.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Um público pré-visualizado é permitido o acesso à sua oferta *antes* da oferta ser publicada ao vivo no mercado. Embora a designação do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos (privados ou não) para fins de validação.

## <a name="technical-configuration"></a>Configuração técnica 

O separador de **configuração técnica** permite-lhe fazer o upload do pacote de implementação que permitirá aos clientes implementar o seu plano.

>[!Note]
>Este separador não será visível se configurar este plano para reutilizar pacotes de outro plano no separador **de configuração do Plano.**

### <a name="package-details"></a>Detalhes do pacote

O subseparador **de detalhes** do Pacote permite-lhe editar a versão de rascunho da sua configuração técnica.

***Versão*** - Atribuir a versão atual da configuração técnica.  Incremente esta versão sempre que publicar uma alteração nesta página. A versão deve `{integer}.{integer}.{integer}`estar no formato .

***Ficheiro*** de`.zip`pacote ( ) - Este pacote contém todos os ficheiros de modelo `.zip` necessários para este plano, bem como quaisquer recursos adicionais, embalados como um ficheiro.

Todos os pacotes do plano de aplicação Azure `.zip` devem incluir estes dois ficheiros na pasta raiz de um arquivo:

* Um ficheiro de modelo de gestor de recursos chamado [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Este modelo automatiza a implantação de recursos na subscrição do Azure dos clientes.  Por exemplo, nos modelos de Gestor de Recursos, consulte a [galeria de modelos De Quickstart Azure](https://azure.microsoft.com/documentation/templates/) ou o [correspondente GitHub: Modelos quickstart do Gestor](https://github.com/azure/azure-quickstart-templates) de Recursos Azure repo.

* Uma definição de interface de utilizador para a experiência de criação de aplicações Azure chamada [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Todas as novas ofertas de aplicações Azure também devem incluir uma atribuição de uso de [cliente parceiro Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) GUID.

### <a name="previously-published-packages"></a>Pacotes anteriormente publicados 

O subseparador de **pacotes anteriormente** publicadopermite-lhe visualizar todas as versões publicadas da sua configuração técnica.

## <a name="technical-configuration-managed-application-plans-only"></a>Configuração técnica (apenas planos de aplicação geridos)

Os planos de aplicação geridos têm complexidade adicional no separador de **configuração técnica** para além dos campos de **ficheiros** **versão** e pacote acima descritos. 

### <a name="enable-just-in-time-jit-access"></a>Ativar o acesso just-in-time (JIT)

Selecione esta opção para permitir o acesso just-in-time (JIT) para este plano.  O acesso ao JIT permite-lhe solicitar um acesso elevado aos recursos de uma aplicação gerida para resolução ou manutenção de problemas. Você sempre tem acesso apenas a leitura aos recursos, mas por um período de tempo específico você pode ter um maior acesso.  Para mais informações, consulte [Enable e solicite acesso just-in-time para Aplicações Geridas azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Para exigir que os consumidores da sua aplicação gerida concedam acesso permanente à sua conta, deixe esta opção desmarcada.

>[!Note]
>Certifique-se de `createUiDefinition.json` atualizar o seu ficheiro para suportar esta funcionalidade.  

### <a name="deployment-mode"></a>Modo de implantação

Selecione se configura o modo de **implementação** **Completo** ou Incremental ao implementar este plano: 

* Em **modo completo,** uma reafectação da aplicação pelo cliente resultará na remoção de recursos `mainTemplate.json`no grupo de recursos geridos se os recursos não forem definidos no . 
* No **modo incremental,** uma redistribuição da aplicação deixa os recursos existentes inalterados.

Para saber mais sobre os modos de implementação, consulte os modos de [implementação do Gestor de Recursos Do Azure](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>URL final de ponto de notificação

Especifique um ponto final https webhook para receber notificações sobre todas as operações crud em instâncias de aplicação geridas desta versão do plano.

### <a name="customize-allowed-customer-actions"></a>Personalize as ações permitidas ao cliente

Selecione esta opção para especificar quais as ações que`*/read`os clientes podem executar sobre os recursos geridos, além das " ações " que estão disponíveis por padrão. 

Enumere as ações adicionais que pretende permitir que o seu cliente realize aqui, separados por pontos evívias.  Para mais informações, consulte [Understanding negar atribuições para os recursos do Azure.](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)  Para as ações disponíveis, consulte as operações de fornecedor de recursos do Gestor de [Recursos do Azure.](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations) Por exemplo, permitir que os consumidores `Microsoft.Compute/virtualMachines/restart/action` reiniciem as máquinas virtuais, adicione às ações permitidas.

### <a name="global-azure--azure-government-cloud"></a>Nuvem global do governo azure / Azure

Indique quem deve ter acesso de gestão a esta aplicação gerida em cada nuvem suportada.  Os utilizadores, grupos ou aplicações que pretende ser autorizadas ao grupo de recursos geridos são identificados usando identidades de Azure Ative Directory (AAD).

***Azure Ative Directory Tenant ID*** - O ID do Inquilino AAD (também conhecido como ID do diretório) contendo as identidades dos utilizadores, grupos ou aplicações a que pretende conceder permissões.  Você pode encontrar o seu ID de Inquilino AAD no portal Azure, em [Propriedades para Diretório Ativo Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Autorizações*** - Adicione o ID de objeto de objeto de Diretório Ativo Azure do utilizador, grupo ou aplicação que pretende ser autorizado ao grupo de recursos geridos. Identifique o utilizador pelo seu ID principal, que pode ser encontrado na lâmina de utilizadores do [Diretório Ativo Azure no portal Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Para cada diretor, selecione uma das funções integradas da Azure AD da lista (Proprietário ou Contribuinte). O papel que selecionar descreverá as permissões que o principal terá sobre os recursos na subscrição do cliente. Para obter mais informações, veja as [Funções incorporadas dos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), consulte [Iniciar com RBAC no portal Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Embora possa adicionar até 100 autorizações por nuvem, é geralmente mais fácil criar um grupo de utilizadores de Diretório Ativo e especificar o seu ID no "ID principal".  Isto permitir-lhe-á adicionar mais utilizadores ao grupo de gestão depois de o plano ser implementado e reduzir a necessidade de atualizar o plano apenas para adicionar mais autorizações.

### <a name="policy-settings"></a>Definições de política

Aplique [políticas Azure](https://docs.microsoft.com/azure/governance/policy/overview) na sua aplicação gerida para especificar os requisitos de conformidade para a solução implementada.  Para definições de política e o formato de valores de parâmetros, consulte [Exemplos de Política do Azure](https://docs.microsoft.com/azure/governance/policy/samples/index).  Pode configurar um máximo de cinco políticas, e apenas um exemplo de cada opção Políticas.  Algumas políticas requerem parâmetros adicionais.  O SKU Padrão é necessário para políticas de auditoria.  O Nome da Apólice está limitado a 50 caracteres.

## <a name="co-sell"></a>Venda Conjunta

Fornecer informações sobre o separador Cosell é totalmente opcional para a publicação da sua oferta. É necessário alcançar o co-sell Ready e ip co-sell Ready status. As informações que fornecer serão utilizadas pelas equipas de vendas da Microsoft para saber mais sobre a sua solução ao avaliar o seu ajuste para as necessidades do cliente. Não está disponível diretamente para os clientes.

Para mais informações sobre a conclusão deste separador, consulte [a opção Co-sell no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Test drive

O separador **test drive** permite-lhe configurar uma demonstração (ou "test drive") que permitirá aos clientes experimentar a sua oferta antes de se comprometerem a comprá-la. Saiba mais no artigo [O que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Se já não quiser fornecer um test drive para a sua oferta, volte à página de **configuração da Oferta** e desmarque **o test drive Enable**.

### <a name="technical-configuration"></a>Configuração técnica

As Aplicações Azure utilizam inerentemente o tipo de unidade de test-drive do Gestor de Recursos Azure.  Consulte [a configuração técnica para](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) o test drive do Gestor de Recursos Azure para obter mais informações.

### <a name="deployment-subscription-details"></a>Detalhes da subscrição de implementação

Para implementar o Test Drive em seu nome, por favor crie e forneça uma assinatura Azure separada e única. (Não é necessário para as unidades de teste Power BI).

- **ID de subscrição Azure** (necessário para o Gestor de Recursos Azure e aplicações lógicas): Introduza o ID de subscrição para conceder acesso aos serviços da sua conta Azure para relatórios de utilização de recursos e faturação. Recomendamos que considere [criar uma subscrição Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar para test drives se ainda não tiver uma. Pode encontrar o seu ID de subscrição Azure iniciando sessão no [portal Azure](https://portal.azure.com/) e navegando no separador **Subscrições** do menu do lado esquerdo. A seleção do separador irá exibir o seu ID de subscrição (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID de inquilino Azure AD** (obrigatório): Insira o seu Id de [inquilino](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure,](https://portal.azure.com/)selecione o separador Ative Directy no menu esquerdo, selecione **Properties, em seguida, procure o número de ID do **Diretório** listado (por exemplo 50c464d3-4930-494c-963c-1e951d1d15360e). Também pode procurar o ID do inquilino da sua [https://www.whatismytenantid.com](https://www.whatismytenantid.com)organização usando o seu URL de nome de domínio em: .

- Nome de **inquilino Azure AD** (necessário para Dinâmico 365): Insira o nome de Diretório Ativo Azure (AD). Para encontrar este nome, inscreva-se no [portal Azure,](https://portal.azure.com/)no canto superior direito o seu nome de inquilino será listado sob o nome da sua conta.

- **ID da aplicação Azure AD** (obrigatório): Introduza o id da [aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu esquerdo, selecione registos de **Aplicações,** em seguida, procure o número de ID de **aplicação** listado (por exemplo 50c464d3-4930-494c-963c-1e951d15360e).

- Segredo de cliente da **aplicação Azure AD** (necessário): Insira o [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)da aplicação Azure AD . Para encontrar este valor, inscreva-se no [portal Azure.](https://portal.azure.com/) Selecione o separador **Diretório Ativo Azure** no menu esquerdo, selecione **as inscrições**da App e, em seguida, selecione a sua aplicação de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo do cliente,** introduza uma descrição, selecione **Never** under **Expires,** em seguida, escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para longe da página antes de ter copiado o valor, caso contrário não terá acesso ao valor.)

Lembre-se de **Guardar** antes de passar para a próxima secção!

### <a name="test-drive-listings-optional"></a>Listas de test drive (opcional)

A opção de **listagem Test Drive** encontrada no separador Test **drive** mostra os idiomas (e mercados) onde o seu test drive está disponível, atualmente o inglês (Estados Unidos) é o único local disponível. Além disso, esta página apresenta o estado da listagem específica da língua e a data/hora que foi adicionada. Terá de definir os detalhes do test drive (descrição, manual do utilizador, vídeos, etc.) para cada idioma/mercado.

- **Descrição** (necessária): Descreva o seu test drive, o que será demonstrado, objetivos para o utilizador experimentar, funcionalidades a explorar e qualquer informação relevante para ajudar o utilizador a determinar se adquire a sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informação de acesso** (necessária para o Gestor de Recursos Azure e unidades de teste lógicas): Explique o que um cliente precisa de saber para aceder e utilizar este test drive. Caminhe por um cenário de utilização da sua oferta e exatamente o que o cliente deve saber para aceder a funcionalidades durante todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do Utilizador** (obrigatório): Uma passagem aprofundada da sua experiência de test drive. O Manual do Utilizador deve cobrir exatamente o que pretende que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer questões que possam ter. O ficheiro deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.

- **Vídeos: Adicionar vídeos** (opcional): Os vídeos podem ser enviados para o YouTube ou Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa ver um walk-through de informação para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso as funcionalidades da sua oferta e entender cenários que realçam os seus benefícios.
  - **Nome** (obrigatório)
  - **URL (apenas YouTube ou Vimeo) (obrigatório)**
  - **Miniatura (533 x 324 px)**: O ficheiro de imagem deve estar no formato PNG.

Selecione **Guardar** depois de completar estes campos.

## <a name="publish"></a>Publicar

### <a name="submit-offer-to-preview"></a>Enviar oferta para pré-visualização

Depois de ter concluído todas as secções necessárias da oferta, **selecione publicar** no canto superior direito do portal. Será redirecionado para a página **De Revisão e publicação.** 

Se for a sua primeira publicação desta oferta, pode:

- Consulte o estado de conclusão de cada secção da oferta.
    - *Não começou* - significa que a secção não foi tocada e precisa de ser concluída.
    - *Incompleto* - significa que a secção tem erros que precisam de ser corrigidos ou exige mais informações a fornecer. Por favor, volte à secção(s) e atualize-a.
    - *Completo* - significa que a secção está completa, todos os dados necessários foram fornecidos e não há erros. Todas as secções da oferta devem estar em estado total antes de poder submeter a oferta.
- Forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente, além de quaisquer notas suplementares úteis para a compreensão da sua aplicação.
- Submeta a oferta para publicação selecionando **Enviar**. Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta está disponível para que você reveja e aprove. Volte ao Partner Center e selecione **Go-live** para a oferta de publicar a sua oferta ao público (ou se uma oferta privada, para o público privado).

### <a name="errors-and-review-feedback"></a>Erros e feedback de revisão

A etapa de **validação manual** no processo de publicação representa uma revisão extensiva da sua oferta e dos seus ativos técnicos associados (especialmente o modelo do Gestor de Recursos Azure), as questões são normalmente apresentadas como links de pedido de puxão (PR). Uma explicação de como ver e responder a estes PRs, ver feedback de [revisão de handling](./azure-apps-review-feedback.md).

Se encontrou erros numa ou mais das etapas editoriais, deve corrigi-los e reeditar a sua oferta.

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)

---
title: Configurar seu aplicativo no portal de publicação
description: Instruções sobre como configurar seu aplicativo no portal de publicação em nuvem.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 1bf89f94d91e0d809428cf8098db0fb37afdb47a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814688"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Configurar seu aplicativo no portal de publicação

Agora você está pronto para configurar seu aplicativo no portal de publicação.

## <a name="login-and-create-a-new-offer"></a>Faça logon e crie uma nova oferta

1. Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Na barra de navegação à esquerda, clique em "+ nova oferta" e selecione "Dynamics 365 para compromisso com o cliente".

![Selecionando uma nova oferta](./media/CRMScreenShot14.png)

1. Uma nova exibição da oferta "Editor" agora está aberta para você e estamos prontos para começar a criar.

![Nova tela de oferta](./media/CRMScreenShot15.png)

1. Os "formulários" que precisam ser preenchidos são visíveis à esquerda na exibição "Editor". Cada "forma" consiste em um conjunto de campos que devem ser preenchidos. Os campos obrigatórios são marcados com um asterisco vermelho (\*).

Há quatro formulários principais para criar uma oferta de compromisso do Dynamics 365 para o cliente

* Configurações da oferta
* Informações técnicas
* Detalhes da vitrine
* Contactos

## <a name="fill-out-the-offer-settings-form"></a>Preencher o formulário de configurações da oferta

O formulário de configurações da oferta é um formulário básico para especificar as configurações da oferta. Os campos diferentes são descritos abaixo.

### <a name="offer-id"></a>ID da oferta

Este é um identificador exclusivo para a oferta em um perfil do Publicador. Essa ID será visível em URLs de produto. Apenas pode ser constituído por carateres alfanuméricos em minúsculas ou hífenes (-). A ID não pode terminar com um traço e pode ter, no máximo, 50 caracteres. Esse campo é bloqueado quando uma oferta é ativada.

por exemplo, se um Publicador **"contoso"** criar uma oferta com a ID **de oferta "Sample-webapp"** , ele aparecerá em AppSource como "https:\//appsource.Microsoft.com/Marketplace/apps/contoso.Sample-webapp?Tab=Overview"

### <a name="publisher-id"></a>ID do editor

Essa lista suspensa permite que você escolha o perfil do Publicador no qual deseja publicar essa oferta. Esse campo é bloqueado quando uma oferta é ativada.

### <a name="name"></a>Nome

Este é o nome de exibição para sua oferta. Esse é o nome que será exibido em [AppSource](https://appsource.microsoft.com/). Pode ter um máximo de 50 carateres.

Clique em "salvar" para salvar seu progresso. A próxima etapa seria adicionar informações técnicas para sua oferta.

## <a name="fill-out-the-technical-info-form"></a>Preencha o formulário de informações técnicas


O formulário informações técnicas é onde você preencherá informações específicas para sua solução Dynamics 365 for Customer Engagement. Passar o mouse sobre o apresentará mais informações. Veja o exemplo abaixo.

![Tela de informações técnicas](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informações do aplicativo

A maioria dos editores deixará esses campos com os valores padrão, User, no, no e uma URL de configuração de aplicativo em branco, de acordo com a captura de tela acima.

### <a name="crm-package"></a>Pacote CRM

![Informações do pacote CRM](./media/CRMScreenShot17.png)

Aqui está uma explicação para esses campos:

* Nome do arquivo do seu pacote: o nome do arquivo que você criou na etapa anterior ao criar o arquivo zip que é seu pacote de AppSource do CRM. No exemplo acima, é "Microsoft\_SamplePackage. zip".
* URL do local do pacote: essa é a URL para a conta de armazenamento do Azure que contém o nome do arquivo de pacote especificado acima. É a URL criada na etapa 9 da seção acima.
* Há mais de um pacote CRM no arquivo de pacote: selecione Sim **somente** se você estiver dando suporte a várias versões do CRM com pacotes diferentes. Para a maioria dos parceiros, isso será "não". Se você selecionar Sim, precisará criar pacotes AppSource para cada versão da sua solução. _Observação: isso não pergunta se você tem vários arquivos **zip** . Se você tiver vários arquivos. zip de solução, mas apenas uma versão, você ainda deverá selecionar "não". A ferramenta de empacotamento as reunirá automaticamente._

### <a name="crm-package-availability"></a>Disponibilidade do pacote CRM

Nesta seção, selecione em quais regiões do CRM seu pacote será disponibilizado. Para obter informações sobre quais regiões do CRM atendem a quais países/regiões, consulte o link: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Observação: a implantação na Alemanha "soberanas e US Gov Cloud" soberanas requer permissão especial e validação durante a certificação

## <a name="storefront-details"></a>Detalhes da vitrine

### <a name="offer-summary"></a>Resumo da oferta

Este é um resumo da proposta de valor da sua oferta. Ele será exibido na página de pesquisa da sua oferta. Deve ter no máximo 100 caracteres.

### <a name="offer-description"></a>Descrição da oferta

Esta é a descrição que será exibida na página de detalhes do aplicativo. O máximo permitido é de 1300 caracteres

### <a name="industries"></a>Setores

Selecione o setor ao qual seu aplicativo está mais alinhado. Se o seu aplicativo estiver relacionado a vários setores, você poderá deixá-lo em branco.

### <a name="categories"></a>Categorias

Selecione as categorias que são relevantes para seu aplicativo. Selecione um máximo de 3.

### <a name="app-type"></a>Tipo de aplicativo

Selecione o tipo de avaliação que seu aplicativo habilitará no AppSource. ' Free ' significa que seu aplicativo é gratuito. "Avaliação" significa que os clientes podem experimentar seu aplicativo por um curto período de AppSource. Não há suporte para a ' solicitação de avaliação ' no Dynamics 365 para aplicativos do engajamento do cliente. Não selecione essa opção.

### <a name="help-link-for-your-app"></a>Link de ajuda para seu aplicativo

Insira a URL para uma página que tenha informações relacionadas à ajuda para seu aplicativo.

### <a name="supported-countriesregions"></a>Países/regiões com suporte

Este campo determina os países/regiões nos quais sua oferta estará disponível para avaliação.

### <a name="supported-languages"></a>Linguagens suportadas

Selecione os idiomas aos quais seu aplicativo dá suporte. Se seu aplicativo der suporte a idiomas adicionais que não estão nesta lista, continue a publicar sua oferta e envie um email para: [appsource@microsoft.com](mailto:appsource@microsoft.com) para nos informar.

### <a name="app-version"></a>Versão da aplicação

Insira o número de versão do seu aplicativo

### <a name="app-release-date"></a>Data de lançamento do aplicativo

Insira a data de lançamento do seu aplicativo

### <a name="products-your-app-works-with-max-3"></a>Produtos com os quais seu aplicativo trabalha (máx. 3)

Produtos específicos da lista com os quais seu aplicativo funciona. Você pode listar o máximo de três produtos. Para listar um produto, clique no sinal de adição (ao lado de novo) e um novo campo de texto aberto será criado para que você insira o nome de um produto com o qual seu aplicativo trabalha.

### <a name="search-keywords-max-3"></a>Pesquisar palavras-chave (máx. 3)

O AppSource permite que o cliente faça a pesquisa com base em palavras-chave. Você pode inserir o conjunto de palavras-chave para as quais seu aplicativo será mostrado aos clientes.

Por exemplo, se o aplicativo for "meus emails do serviço de email", a correspondência, o serviço de email poderá ser de algumas palavras-chave. Escolha as palavras que os usuários provavelmente usarão para pesquisar seu aplicativo na caixa de pesquisa AppSource.

### <a name="hide-key"></a>Ocultar chave

Essa é uma chave que será combinada com a URL de visualização da oferta para ocultá-la da exibição pública. Não é uma senha. Você pode inserir qualquer cadeia de caracteres aqui.

### <a name="offer-logo-png-format-48x48"></a>Logotipo da oferta (formato PNG, 48x48)

Isso será exibido na página de pesquisa do seu aplicativo. **Somente o formato PNG é permitido.** Carregar uma imagem png com a resolução de 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Logotipo da oferta (formato PNG, 216x216)

Isso será exibido na página de detalhes do aplicativo. **Somente o formato PNG é permitido.** Carregar uma imagem png com a resolução de 216PX\*216PX

### <a name="videos"></a>Vídeos

Você pode carregar no máximo quatro vídeos. Para cada vídeo que você deseja carregar, você precisa preencher o nome do vídeo, a URL (YouTube ou Vimeo somente) e a miniatura para associar ao vídeo. A miniatura deve estar no formato PNG e deve ser 1280PX\*720PX. Para adicionar novos vídeos, clique no sinal de mais. Miniaturas de vídeos serão exibidas na página de detalhes do aplicativo.

### <a name="documents"></a>Documentos

Você pode carregar no máximo três documentos no formato PDF. Para cada documento que você deseja carregar, você precisa preencher o nome do documento e carregar o documento. O documento deve estar no formato PDF.

Para adicionar novos documentos, clique no sinal de mais

### <a name="screenshots"></a>Capturas de Ecrã

Essas são capturas de tela que serão exibidas na página de detalhes do AppSource para seu aplicativo.

### <a name="privacy-policy"></a>Política de Privacidade

Insira a URL para a política de privacidade do aplicativo

### <a name="terms-of-use"></a>Termos de utilização

Insira os termos de uso do seu aplicativo. Os clientes do AppSource precisam aceitar esses termos antes de tentarem seu aplicativo

### <a name="support-url"></a>URL de suporte

Insira a URL de suporte para seu aplicativo.

### <a name="lead-destination"></a>Destino do cliente potencial

Selecione um sistema CRM no qual você poderá ser armazenado. Selecione "tabela do Azure" aqui se você tiver um dos seguintes sistemas de CRM: Salesforce, Marketo, Microsoft Dynamics CRM. O sistema CRM selecionado aqui é onde escreveremos os detalhes dos usuários finais que tentam seu aplicativo no AppSource (leads). Dependendo do sistema CRM que você selecionar, clique na URL correspondente abaixo para obter informações sobre como concluir o próximo conjunto de campos

* [Tabela do Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Detalhes da vitrine

Os detalhes de contato são usados somente para comunicação interna entre o parceiro e a Microsoft. Observação: é importante usar um endereço de email que é monitorado nesses campos. Usaremos este email para se comunicar com você sobre seu progresso na publicação no AppSource. Somente a URL de suporte será visível para os clientes.

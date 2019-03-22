---
title: Configurar a sua aplicação no Portal de publicação | Documentos da Microsoft
description: Instruções de como configurar a sua aplicação no Portal de publicação de Cloud.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 8ac0fbb1c62e4162e1c4ad040365a16d055e4552
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310022"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Configurar a sua aplicação no Portal de publicação

Está agora preparado para configurar a sua aplicação no portal de publicação.

## <a name="login-and-create-a-new-offer"></a>Início de sessão e criar uma nova oferta

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Na barra de navegação esquerdo, clique em "+ nova da oferta" e selecione "Dynamics 365 para envolvimento do cliente."

![Selecionar uma nova oferta](./media/CRMScreenShot14.png)

1. Uma nova oferta vista de "Editor" agora é aberta para, e estamos prontos para começar a criar.

![Novo ecrã de oferta](./media/CRMScreenShot15.png)

1. O "forms" que têm de ser preenchidos são visível no lado esquerdo do modo de "Editor" exibição. Cada "form" consiste num conjunto de campos que devem ser preenchidos. Necessário campos são marcados com um asterisco vermelho (\*).

Existem quatro formas principais para a criação de um do Dynamics 365 para a oferta de envolvimento do cliente

* Definições da oferta
* Informações técnicas
* Vitrine detalhes
* Contactos

## <a name="fill-out-the-offer-settings-form"></a>Preencha o formulário de definições da oferta

O formulário de definições da oferta é um formulário básico para especificar as definições da oferta. Os diferentes campos são descritos abaixo.

### <a name="offer-id"></a>ID de Oferta

Este é um identificador exclusivo para a oferta de um perfil do publicador. Este ID será visível em URLs de produto. Apenas pode ser constituído por carateres alfanuméricos em minúsculas ou hífenes (-). O ID não pode terminar com um travessão e pode ter um máximo de 50 carateres. Este campo está bloqueado assim que uma oferta entra no ar.

Por exemplo, se um publicador **"contoso"** publicadores cria uma oferta com o ID de oferta **"WebApp de exemplo"**, que será apresentado no AppSource como "https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>ID de publicador

Essa área suspensa permite-lhe escolher o perfil do publicador que pretende publicar esta oferta em. Este campo está bloqueado assim que uma oferta entra no ar.

### <a name="name"></a>Name

Este é o nome a apresentar para a sua oferta. Este é o nome que aparecerá na [AppSource](https://appsource.microsoft.com/). Pode ter um máximo de 50 carateres.

Clique em "Guardar" para guardar o seu progresso. Próxima etapa seria adicionar informações técnicas para a sua oferta.

## <a name="fill-out-the-technical-info-form"></a>Preencha o formulário de informações técnicas


O formulário de informações técnicas é onde será Preencha informações específicas para o Dynamics 365 para a solução de envolvimento do cliente. Pairar o rato sobre o irá apresentar-lhe obter mais informações. Veja o exemplo abaixo.

![Ecrã de informações técnicas](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informação sobre a aplicação

A maioria dos editores irão deixá-las campos com valores predefinidos, utilizador, não, não e um URL de configuração de aplicação em branco de acordo com a captura de ecrã acima.

### <a name="crm-package"></a>Pacote CRM

![Informações de pacote CRM](./media/CRMScreenShot17.png)

Aqui está uma explicação para estes campos:

* Nome do ficheiro do pacote: O nome de ficheiro que criou no passo acima quando criar o ficheiro zip que é o seu pacote de AppSource de CRM. No exemplo acima, é "Microsoft\_SamplePackage.zip".
* URL de sua localização de pacote: Este é o URL para a conta de armazenamento do Azure que contém o nome de ficheiro do pacote especificado acima. É o URL que criou no passo 9 da secção acima.
* Existe mais do que um pacote de crm no seu ficheiro de pacote: Selecione Sim **apenas** se estiver dando suporte a várias versões do crm com diferentes pacotes. Para a maioria dos parceiros, esta será "Não". Se selecionar Sim, terá de criar pacotes de AppSource para cada versão da sua solução. _Nota: Isso não estamos a pedir se tiver múltiplos **zip** ficheiros. Se tiver vários arquivos de solution.zip, mas apenas uma versão, deve ainda selecionar "não". A ferramenta de empacotamento irá reunir estas de automaticamente._

### <a name="crm-package-availability"></a>Disponibilidade do pacote CRM

Nesta secção, selecione em que regiões do seu pacote será disponibilizado para CRM. Para obter informações em que regiões servem que países, consulte o link: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Nota: Implementar Alemanha permissão especial de REQUIRES garante a soberania: "Independentes e Cloud de Governo dos E.U.A." e a validação durante a certificação

## <a name="storefront-details"></a>Vitrine detalhes

### <a name="offer-summary"></a>Resumo da oferta

Este é um resumo da proposta de valor da sua oferta. Ele será exibido na página de pesquisa da sua oferta. Deve ser um máximo de 100 carateres.

### <a name="offer-description"></a>Descrição da oferta

Esta é a descrição que aparecerá na página de detalhes da aplicação. Máximo permitido é de carateres de 1300

### <a name="industries"></a>Setores

Selecione o setor melhor alinhado à sua aplicação. Se tiver a sua aplicação está relacionado com vários setores, pode deixar em branco.

### <a name="categories"></a>Categorias

Selecione as categorias que são relevantes para a sua aplicação. Selecione um máximo de 3.

### <a name="app-type"></a>Tipo de aplicação

Selecione o tipo de versão de avaliação que permitirá que a aplicação no AppSource. "Gratuitas" significa que a aplicação é gratuita. "Versão de avaliação" significa que os clientes podem experimentar a sua aplicação durante um curto período no AppSource. 'Pedir avaliação' não é suportada para o Dynamics 365 para aplicações de envolvimento do cliente. Não selecione esta opção.

### <a name="help-link-for-your-app"></a>Ligação de ajuda para a sua aplicação

Introduza o URL para uma página que tem a ajudar a obter informações relacionadas para a sua aplicação.

### <a name="supported-countriesregions"></a>Países/regiões suportados

Este campo determina os países/regiões em que a oferta estará disponível para avaliação.

### <a name="supported-languages"></a>Linguagens suportadas

Selecione os idiomas que oferece suporte a sua aplicação. Se a sua aplicação suportar outros idiomas que não estejam na lista, continuar a publicar a sua oferta e enviar-nos para: [ appsource@microsoft.com ](mailto:appsource@microsoft.com) para nos informar.

### <a name="app-version"></a>Versão da aplicação

Introduza o número de versão para a sua aplicação

### <a name="app-release-date"></a>Data de lançamento de aplicação

Introduza a data de lançamento para a sua aplicação

### <a name="products-your-app-works-with-max-3"></a>Produtos a que sua aplicação funciona com (máx. 3)

Produtos específicos de lista que a sua aplicação funciona com. Pode listar o máximo de três produtos. Para listar um produto, clique no sinal de adição (junto ao novo) e será criado um novo campo de texto em aberto para que insira o nome de um produto que a sua aplicação funciona com.

### <a name="search-keywords-max-3"></a>Palavras a procurar (máx. 3)

AppSource permite que o cliente para procurar as palavras-chave com base no. Pode inserir o conjunto de palavras-chave para os quais seu aplicativo será mostrado aos clientes.

Por exemplo se o aplicativo for "Serviço de envio por email My" mensagens de E-Mail, de correspondência, o serviço de correio pode ser algumas palavras-chave. Escolha palavras que os usuários provavelmente usarão a pesquisa para a sua aplicação na caixa de pesquisa do AppSource.

### <a name="hide-key"></a>Ocultar chave

Esta é uma chave que será combinada com o URL de pré-visualização de oferta para ocultá-lo da visualização pública. Não é uma palavra-passe. Pode introduzir qualquer cadeia de caracteres aqui.

### <a name="offer-logo-png-format-48x48"></a>Logótipo da oferta (formato png, 48 x 48)

Isto irá aparecer na página de pesquisa da sua aplicação. **É permitido apenas o formato png.** Carregar uma imagem png com resolução de 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Logótipo da oferta (formato png, 216 x 216)

Isto irá aparecer na página de detalhes da sua aplicação. **É permitido apenas o formato png.** Carregar uma imagem png com resolução de 216PX\*216PX

### <a name="videos"></a>Vídeos

Pode carregar o máximo de quatro vídeos. Para cada vídeo que pretende carregar, terá de preencher o nome do vídeo, URL (YouTube ou Vimeo apenas) e miniatura para associar o vídeo. Miniatura tem de estar no formato png e tem de ser 1280PX\*720PX. Para adicionar novos video(s), clique no sinal de adição. Vídeos thumbnail(s) será é apresentada na página de detalhes da sua aplicação.

### <a name="documents"></a>Documentos

Pode carregar o máximo de três documentos em formato PDF. Para cada documento que pretende carregar, terá de preencher o nome do documento e carregar o documento. Documento tem de estar no formato pdf.

Para adicionar novos document(s), clique no sinal de adição

### <a name="screenshots"></a>Capturas de Ecrã

Estes são capturas de ecrã que aparecerão na página de detalhes do AppSource para a sua aplicação.

### <a name="privacy-policy"></a>Política de Privacidade

Introduza o URL para a política de privacidade da sua aplicação

### <a name="terms-of-use"></a>Termos de utilização

Introduza os termos de utilização da sua aplicação. Os clientes do AppSource precisam aceitar estes termos antes de eles experimentam a sua aplicação

### <a name="support-url"></a>URL de suporte

Introduza o URL de suporte para a sua aplicação.

### <a name="lead-destination"></a>Líder de destino

Selecione um sistema CRM onde levar serão armazenados. Selecione "Tabelas do Azure" aqui, se tiver um dos seguintes sistemas CRM: Salesforce, Marketo, Microsoft Dynamics CRM. O sistema CRM que selecione aqui é onde podemos irá escrever os detalhes dos utilizadores finais que tenta a sua aplicação no AppSource (oportunidades potenciais). Dependendo do sistema CRM que selecionou, clique no URL correspondente abaixo para obter informações sobre como executar o próximo conjunto de campos

* [Tabela do Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Vitrine detalhes

Os detalhes de contactos são utilizados para comunicação interna entre os parceiros e a Microsoft apenas. Nota: É importante utilizar um endereço de e-mail que é monitorizado nestes campos. Utilizaremos este e-mail para se comunicar com em seu progresso em publicar no AppSource. Apenas o URL de suporte serão visível para os clientes.

---
title: Configurar a sua aplicação no Portal editorial
description: Instruções sobre como configurar a sua aplicação no Portal de Publicação da Cloud.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280206"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Configurar a sua aplicação no Portal editorial

Está agora pronto para configurar a sua aplicação no portal editorial.

## <a name="login-and-create-a-new-offer"></a>Iniciar sessão e criar uma nova oferta

1. Inscreva-se no Portal do [Parceiro cloud.](https://cloudpartner.azure.com/)
2. A partir da barra de navegação à esquerda, clique em "+ Nova oferta" e selecione "Dynamics 365 for Customer Engagement".

![Selecionar uma nova oferta](./media/CRMScreenShot14.png)

1. Uma nova oferta "Editor" está agora aberta para si, e estamos prontos para começar a ser autores.

![Ecrã new offer](./media/CRMScreenShot15.png)

1. Os "formulários" que precisam de ser preenchidos são visíveis à esquerda na vista "Editor". Cada "forma" consiste num conjunto de campos que devem ser preenchidos. Os campos necessários são marcados\*com um asterisco vermelho ( ).

Existem quatro formulários principais para a autoria de uma oferta de Participação do Cliente Da Dynamics 365

* Oferta De Definições
* Informação Técnica
* Detalhes da montra
* Contactos

## <a name="fill-out-the-offer-settings-form"></a>Preencha o formulário de Definições de Oferta

O formulário de definições de oferta é um formulário básico para especificar as definições de oferta. Os diferentes campos são descritos abaixo.

### <a name="offer-id"></a>ID da oferta

Este é um identificador único para a oferta dentro de um perfil de editor. Este ID será visível em URLs do produto. Apenas pode ser constituído por carateres alfanuméricos em minúsculas ou hífenes (-). O ID não pode terminar num traço e pode ter um máximo de 50 caracteres. Este campo está bloqueado assim que uma oferta for ao vivo.

por exemplo, se uma editora **"contoso"** criar uma oferta com oferta de ID **"sample-WebApp",** aparecerá no AppSource como "https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>ID da editora

Este dropdown permite-lhe escolher o perfil da editora que pretende publicar esta oferta. Este campo está bloqueado assim que uma oferta for ao vivo.

### <a name="name"></a>Nome

Este é o nome de exibição para a sua oferta. Este é o nome que aparecerá no [AppSource](https://appsource.microsoft.com/). Pode ter um máximo de 50 carateres.

Clique em "Guardar" para salvar o seu progresso. O próximo passo seria adicionar Informações Técnicas para a sua oferta.

## <a name="fill-out-the-technical-info-form"></a>Preencha o formulário de Informação Técnica


O formulário de informação técnica é onde irá preencher informações específicas da sua Dinâmica 365 para a solução de Envolvimento do Cliente. Pairando sobre o testamento lhe presenteando com mais informações. Veja o exemplo abaixo.

![Tela de informação técnica](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informação de Aplicação

A maioria dos editores deixará estes campos com os valores padrão, Utilizador, Não, Não, Não, e um URL de Configuração de Aplicação em branco de acordo com a imagem acima.

### <a name="crm-package"></a>Pacote CRM

![Informações sobre pacotes CRM](./media/CRMScreenShot17.png)

Aqui está uma explicação para estes campos:

* Nome de ficheiro do seu pacote: O nome de ficheiro que criou no passo acima ao criar o ficheiro zip que é o seu Pacote CRM AppSource. No exemplo acima, é\_"Microsoft SamplePackage.zip".
* Url da sua localização do pacote: Este é o URL da conta De armazenamento Azure que contém o nome de ficheiro do pacote acima especificado. É o URL criado no passo 9 da secção acima.
* Existe mais de um pacote crm no seu ficheiro de pacote: Selecione Yes **ONLY** se estiver a suportar várias versões de crm com diferentes pacotes. Para a maioria dos parceiros este será "Não". Se selecionar Sim, precisa de criar Pacotes AppSource para cada versão da sua solução. _Nota: Isto não é perguntar se tem vários ficheiros **zip.** Se tiver vários ficheiros de solução.zip mas apenas uma versão, deve ainda selecionar "Não". A ferramenta de embalagem irá juntá-las automaticamente._

### <a name="crm-package-availability"></a>Disponibilidade de pacote crm

Nesta secção, selecione quais as regiões de CRM para as quais o seu pacote será disponibilizado. Para obter informações sobre quais as regiões de CRM que servem quais países/regiões, consulte a ligação:[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Nota: Implantação para a Alemanha "Sovereign and US Gov Cloud" Soberano requer permissão e validação especiais durante certificação

## <a name="storefront-details"></a>Detalhes da montra

### <a name="offer-summary"></a>Resumo da Oferta

Este é um resumo da proposta de valor da sua oferta. Aparecerá na página de pesquisa da sua oferta. Deve ser um máximo de 100 caracteres.

### <a name="offer-description"></a>Descrição da Oferta

Esta é a descrição que aparecerá na página de detalhes da sua aplicação. O máximo permitido é de 1300 caracteres

### <a name="industries"></a>Setores

Selecione a indústria a que a sua aplicação está mais alinhada. Se a sua aplicação tiver a ver com várias indústrias, pode deixar isto em branco.

### <a name="categories"></a>Categorias

Selecione as categorias relevantes para a sua aplicação. Selecione um máximo de 3.

### <a name="app-type"></a>Tipo de aplicação

Selecione o tipo de teste que a sua aplicação irá ativar no AppSource. 'Grátis' significa que a sua aplicação é gratuita. 'Trial' significa que os clientes podem experimentar a sua aplicação por um curto período no AppSource. O 'Pedido de Teste' não é suportado para a Dynamics 365 para aplicações de envolvimento com o cliente. Não selecione esta opção.

### <a name="help-link-for-your-app"></a>Link de ajuda para a sua aplicação

Introduza URL numa página que tenha informações relacionadas com a ajuda para a sua aplicação.

### <a name="supported-countriesregions"></a>Países/regiões apoiados

Este campo determina os países/regiões em que a sua oferta estará disponível para julgamento.

### <a name="supported-languages"></a>Linguagens suportadas

Selecione os idiomas que a sua aplicação suporta. Se a sua aplicação suporta idiomas adicionais que não estão nesta [appsource@microsoft.com](mailto:appsource@microsoft.com) lista, continue a publicar a sua oferta e envie-nos um e-mail para: para nos informar.

### <a name="app-version"></a>Versão da aplicação

Insira o número da versão para a sua aplicação

### <a name="app-release-date"></a>Data de Lançamento de Apps

Insira a data de lançamento da sua aplicação

### <a name="products-your-app-works-with-max-3"></a>Produtos com os que a sua aplicação trabalha (Max 3)

Produtos específicos da lista com os que a sua aplicação trabalha. Pode listar o máximo de três produtos. Para listar um produto, clique no sinal de mais (ao lado de novo) e será criado um novo campo de texto aberto para que introduza o nome de um produto com o qual a sua aplicação trabalha.

### <a name="search-keywords-max-3"></a>Pesquisar palavras-chave (Max 3)

O AppSource permite que o cliente faça uma pesquisa com base em palavras-chave. Pode introduzir o conjunto de palavras-chave para as quais a sua aplicação será mostrada aos clientes.

Por exemplo, se a aplicação for "My Emailing Service" Emails, Mailing, Mailing, Mailservice pode ser algumas palavras-chave. Escolha as palavras que os utilizadores provavelmente utilizarão para procurar a sua aplicação na caixa de pesquisa AppSource.

### <a name="hide-key"></a>Ocultar a chave

Esta é uma chave que será combinada com o URL de pré-visualização da oferta para escondê-la da opinião pública. Não é uma senha. Pode entrar em qualquer corda aqui.

### <a name="offer-logo-png-format-48x48"></a>Logotipo de oferta (formato png, 48x48)

Isto aparecerá na página de pesquisa da sua aplicação. **Só é permitido o formato png.** Faça upload de uma imagem png\*com resolução de 48PX 48PX

### <a name="offer-logo-png-format-216x216"></a>Logotipo de oferta (formato png, 216x216)

Isto aparecerá na página de detalhes da sua aplicação. **Só é permitido o formato png.** Faça upload de uma imagem png\*com resolução de 216PX 216PX

### <a name="videos"></a>Vídeos

Pode carregar o máximo de quatro vídeos. Para cada vídeo que pretende carregar, tem de preencher o nome de vídeo, URL (apenas no YouTube ou Vimeo) e Miniatura para se associar ao vídeo. A miniatura deve estar em formato png\*e deve ser 1280PX 720PX. Para adicionar novos vídeos, clique no sinal de mais. Os vídeos da miniatura aparecerão na página de detalhes da sua aplicação.

### <a name="documents"></a>Documentos

Pode fazer o upload máximo de três documentos em formato PDF. Para cada documento que pretende fazer upload, tem de preencher o nome do documento e fazer o upload do documento. O documento deve estar em formato pdf.

Para adicionar novos documentos, clique no sinal de mais

### <a name="screenshots"></a>Capturas de Ecrã

Estas são imagens que aparecerão na página de detalhes do AppSource para a sua aplicação.

### <a name="privacy-policy"></a>Política de Privacidade

Insira URL na política de privacidade da sua aplicação

### <a name="terms-of-use"></a>Termos de utilização

Insira os termos de utilização da sua app. Os clientes appSource são obrigados a aceitar estes termos antes de poderem experimentar a sua aplicação

### <a name="support-url"></a>URL de suporte

Introduza o URL de suporte para a sua aplicação.

### <a name="lead-destination"></a>Destino principal

Selecione um sistema CRM onde o seu chumbo será armazenado. Selecione "Azure Table" aqui se tiver um dos seguintes sistemas CRM: Salesforce, Marketo, Microsoft Dynamics CRM. O sistema CRM que seleciona aqui é onde escreveremos detalhes dos utilizadores finais que experimentam a sua aplicação no AppSource (leads). Dependendo do sistema CRM que selecionar, clique no URL correspondente abaixo para obter informações sobre como completar o próximo conjunto de campos

* [Mesa Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Detalhes da montra

Os dados de contacto são utilizados apenas para comunicação interna entre o parceiro e a Microsoft. Nota: É importante utilizar um endereço de e-mail que é monitorizado nestes campos. Usaremos este e-mail para comunicar consigo sobre o seu progresso na publicação no AppSource. Apenas o URL de suporte será visível para os clientes.

---
title: Como criar o Dynamics 365 para operações oferecem através do portal de parceiros da Cloud | Documentos da Microsoft
description: Como criar o Dynamics 365 para operações oferecem através do portal de parceiro de Cloud
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
ms.openlocfilehash: 93c70e038589667ae97acb86663a6179dcc81637
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877333"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Como criar o Dynamics 365 para operações oferecem através do portal de parceiro de Cloud

Portal de publicação fornece acesso baseado em funções no portal, permitindo que vários indivíduos possam colaborar em direção à publicação de uma oferta. Ver [gerir utilizadores do Portal Cloud](./cloud-partner-portal-manage-users.md) para obter mais informações.

Antes de uma oferta pode ser publicada em nome de um fabricante de contas, uma das pessoas com \"proprietário\" função precisam concorda em estar em conformidade com o [termos de utilização](https://azure.microsoft.com/support/legal/website-terms-of-use/), [declaraçãodeprivacidadedaMicrosoft](https://www.microsoft.com/privacystatement/default.aspx), e [contrato do programa do Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Como criar um novo do Dynamics 365 para operações da oferta

Depois de todos os pré-requisitos tiverem sido cumpridos, está pronto para começar a criar seu Dynamics 365 para a oferta de operações.

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Na barra de navegação esquerdo, clique em \"+ nova oferta\" e selecione \"Dynamics 365 para operações\".
3. Uma nova oferta \"Editor\" Vista agora é aberta para, e estamos prontos para começar a criar.
4. O \"formulários\" que têm de ser preenchidos são visíveis no lado esquerdo dentro os \"Editor\" vista. Cada \"formulário\" consiste num conjunto de campos que devem ser preenchidos. Necessário campos são marcados com um asterisco vermelho (\*).

Existem quatro formas principais para a criação de um do Dynamics 365 para a oferta de operações:

- Definições da oferta
- Informações técnicas
- Vitrine detalhes
- Contactos

## <a name="how-to-fill-out-the-offer-settings-form"></a>Como preencher o formulário de definições da oferta

O formulário de definições da oferta é um formulário básico para especificar as definições da oferta. Os diferentes campos são descritos abaixo.

### <a name="offer-id"></a>ID de Oferta

Este é um identificador exclusivo para a oferta de um perfil do publicador. Este ID será visível em URLs de produto. Apenas pode ser constituído por carateres alfanuméricos em minúsculas ou hífenes (-). O ID não pode terminar com um travessão e pode ter um máximo de 50 carateres. Este campo está bloqueado assim que uma oferta entra no ar.

Por exemplo, se um publicador de contoso publicador cria uma oferta com o ID da oferta *dynamics365 de exemplo para operações*, que será apresentado no AppSource como `https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics365 for operations*?tab=Overview\`.

### <a name="publisher-id"></a>ID de publicador

Essa área suspensa permite-lhe escolher o perfil do publicador que pretende publicar esta oferta em. Este campo está bloqueado assim que uma oferta entra no ar.

Name

Este é o nome a apresentar para a sua oferta. Este é o nome que aparecerá na [AppSource](https://appsource.microsoft.com). Pode ter um máximo de 50 carateres.

Clique em \"guardar\" para guardar o seu progresso. Próxima etapa seria preencher as informações técnicas para a sua oferta.

## <a name="how-to-fill-out-the-technical-info-form"></a>Como preencher o formulário de informações técnicas

O formulário de informações técnicas contém informações que serão apresentadas na sua página da oferta. Instruções para os diferentes campos são descritas abaixo.

![Novo ecrã de oferta](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>Identificador de solução

O primeiro é o identificador de solução.

1. Para localizar este identificador, aceda a serviços de ciclo de vida e selecione a solução de gestão.
2. Após escolher a solução apropriada, verá o identificador de solução na descrição geral do pacote. \*\*Se o identificador estiver em branco, selecione editar e voltar a publicar o pacote para o identificador da solução a aparecer.

### <a name="validation-assets"></a>Ativos de validação

Carregue seu carro (relatórios de análise de personalização) aqui.

### <a name="does-solution-enable-translations"></a>Solução de ativar o translation(s)?

Selecione \'Sim\' ou \'não\'.

### <a name="does-solution-include-localizations"></a>Solução está incluído na Localization(s)?

Selecione \'Sim\' ou \'não\'.

### <a name="product-version"></a>Versão do Produto

Selecione o novo AX. Por fim clique em Guardar.

## <a name="how-to-fill-out-storefront-details-form"></a>Como a preencher o formulário de detalhes da loja.

Em primeiro lugar é detalhes da sua oferta.

1. **Resumo da oferta**

    \- Introduza um breve resumo da sua solução (máximo de 100 carateres).

2. **Descrição da oferta**

    \- Introduza uma breve descrição da sua solução. A descrição deve ter os requisitos de espaço funcional da sua solução e deve alinhar diretamente com a sua biblioteca BPM. Por exemplo, se diz que tem de funcionalidades x, y, z no seu conteúdo de marketing, durante a revisão final, verificamos se estes estão documentados na biblioteca de BPM dentro do LCS.

![ecrã de detalhes vitrine](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>Detalhes da lista

![ecrã de detalhes vitrine](./media/publish_d365_new_offer/storefront_details.png)

1. **Setores** -verificar um máximo de dois setores entre as opções de determinado.
2. **Categorias** -verificar um máximo de três categorias entre as opções de determinado.
3. **Tipo de aplicação** -selecionar de entre as opções de determinado.
4. **Hiperligação para a sua aplicação de ajuda** -introduza uma ligação de ajuda para a sua solução.
5. **Países/regiões suportadas** -verificar entre as opções de determinado.
6. **Idiomas suportados** -verificar entre as opções de determinado.
7. **Versão da aplicação** -introduza a versão da solução, que está a ser lançada. (por exemplo, 1.0.0.0)
8. **Data de lançamento de aplicação** -introduza a versão data de sua solution(mm/dd/yyyy).
9. **Produtos a sua aplicação funciona com** -produtos específicos de lista que a sua aplicação funciona com. Pode listar o máximo de três produtos. Para listar um produto, clique no sinal de adição (junto ao novo) e será criado um novo campo de texto em aberto para que insira o nome de um produto que a sua aplicação funciona com.
10. **Procurar palavras-chave** -introduza termos comuns utilizadores podem utilizar para encontrar a sua solução durante uma pesquisa. \*\*Estas palavras-chave não serão apresentados no marketplace.
11. **Ocultar chave** -este é o que chave que deve ser combinada com o URL de pré-visualização de oferta para ocultá-lo da visualização pública. Não é uma palavra-passe. Pode introduzir qualquer cadeia de caracteres aqui.

### <a name="marketing-artifacts"></a>Artefactos de marketing

1. Em seguida está a carregar seus **logótipos**, **capturas de ecrã**. \*\*Tenha em atenção os tamanhos para cada carregamento e todas as imagens devem estar no formato PNG.
2. **Vídeos de demonstração** -clique em \"+ nova\". Carregar um vídeo de demonstração da sua solução (apenas ligações YouTube ou Vimeo). \*\*. Tenha em atenção de que deve carregar uma miniatura de tamanho especificado para tornar o vídeo aparecem no teste.
3. **Documentos** - carregar todos os documentos relacionados com a sua solução e introduza um nome para o documento.

### <a name="legal"></a>Informações Legais

Esta informação vai ligar à sua política de privacidade e termos de utilização. Introduza a URL de política de privacidade da solução e aos termos de utilização. \*\*O cliente será capaz de ver estas políticas no portal.

### <a name="customer-support"></a>Suporte ao Cliente

O URL de suporte só será visto no portal pelos seus utilizadores.

### <a name="leads-management"></a>Gestão de oportunidades potenciais

Selecione um sistema CRM onde levar serão armazenados. Selecione \"tabelas do Azure\" aqui, se tiver um dos seguintes sistemas CRM: Salesforce, Marketo, Microsoft Dynamics CRM. O sistema CRM que selecione aqui é onde podemos irá escrever os detalhes dos utilizadores finais que tenta a sua aplicação no AppSource (oportunidades potenciais). Dependendo do sistema CRM que selecionou, clique no URL correspondente abaixo para obter informações sobre como executar o próximo conjunto de campos.

![Levar os detalhes de gestão](./media/publish_d365_new_offer/leads.png)

1. Para tabelas do Azure, consulte [aqui](https://aka.ms/leadsettingforazuretable)
2. Para o Dynamics CRM online, consulte [aqui](https://aka.ms/leadsettingfordynamicscrm)
3. Para o Marketo [aqui](https://aka.ms/leadsettingformarketo)
4. Para Salesforce consulte [aqui](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>Como preencher o formulário de contactos.

Estas informações serão utilizadas para a Microsoft e o cliente suportar. Introduza o contacto de engenharia e o suporte ao cliente para a sua empresa e o URL de suporte para a sua solução. Estas informações serão utilizadas, como um único ponto de contacto, se a Microsoft tem uma pergunta sobre a sua solução e também para suporte ao cliente.

![Ecrã de contactos de oferta](./media/publish_d365_new_offer/Contacts.png)

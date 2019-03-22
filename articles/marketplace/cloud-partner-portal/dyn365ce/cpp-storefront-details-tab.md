---
title: Dynamics 365 for Customer Engagement oferece separador de detalhes da loja - Azure Marketplace | Documentos da Microsoft
description: Utilize o separador de detalhes de loja para especificar as informações de vendas e marketing para um de Dynamics 365 para a oferta de envolvimento do cliente.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: pbutlerm
ms.openlocfilehash: f9cbf5033d0ffb84959de94db052626dd1d131c8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997946"
---
# <a name="dynamics-365-for-customer-engagement-storefront-details-tab"></a>Dynamics 365 para o separador de detalhes de Vitrine de envolvimento do cliente

Utilize o **detalhes de loja** separador para fornecer informações de vendas e marketing e artefactos.  Este separador contém as seguintes secções de seis: **Detalhes da oferta**, **detalhes da lista**, **artefactos de Marketing**, **legais**, **suporte ao cliente**,  **Numa gestão**.  Uma etiqueta anexada com um asterisco (*) indica que é necessário.


## <a name="offer-details-section"></a>Secção de detalhes da oferta

Deve fornecer o resumo e a descrição da sua oferta nesta secção.  Esta informação é apresentada em destaque no mercado.
 
![Secção de detalhes de oferta da guia detalhes de loja](./media/storefront-tab-offer-details.png)

Em que a tabela seguinte descreve os campos neste separador: 

|    **Campo**          |  **Descrição**               |
|  ---------------      |  ---------------               |
|  Resumo da oferta        | Resumo da proposta de valor da sua oferta. Ele será exibido na página de pesquisa da sua oferta. O formato é texto sem formatação com um máximo de 100 carateres. |
|  Descrição da oferta    | Descrição que aparecerá na página de detalhes da aplicação. O formato é HTML simple (incluindo p, em, ul, li, ol e etiquetas de cabeçalho) com um máximo de carateres de 1300.  |
|  |  | 


## <a name="listing-details-section"></a>Secção de detalhes de listagem

Deve fornecer detalhes sobre o marketplace listagem nesta secção.

![Secção de detalhes de listagem da guia detalhes de loja](./media/storefront-tab-listing-details.png)

Em que a tabela seguinte descreve os campos neste separador: 

|    **Campo**          |  **Descrição**               |
|  ---------------      |  ---------------               |
|  Setores           | Selecione os setores (dois máximo), a aplicação melhor ajustado a. Se nenhum aplica-se, pode selecionar as opções de zero.  |
|  Categorias           | Selecione as categorias que são relevantes para a aplicação. Selecione entre um e três opções.  |
|  Tipo de aplicação             | Selecione o tipo de versão de avaliação que está ativada para a aplicação no AppSource. **Gratuito** indica a sua aplicação pode ser utilizada sem custos; **Avaliação** indica que os clientes podem experimentar a aplicação durante um curto período sem custos; e **contactar-me** indica que os clientes podem solicitar uma versão de avaliação da aplicação no AppSource.  |
| Ligação de ajuda para a sua aplicação | URL para a documentação online ou outro recurso de ajuda para a aplicação.  |
| Países/regiões suportados | Uma caixa de diálogo de pop-up permite a seleção das regiões geográficas em que a aplicação está disponível.  Distribuição em todo o mundo é o padrão. |
| Linguagens suportadas   | Selecione os idiomas que oferece suporte a sua aplicação. Atualmente apenas alguns idiomas as opções são suportadas.  (Se a sua aplicação suportar outros idiomas que não estejam na lista, continuar a publicar a sua oferta e enviar estas informações para: appsource\@microsoft.com.)  |
| Versão da aplicação           | Número de versão da aplicação    |
| Data de lançamento de aplicação      | Data de lançamento/publicação da aplicação |
| Produtos a sua aplicação funciona com | Produtos ou serviços que a aplicação entre opera com. Pode listar o máximo de três produtos. Para listar um produto, clique em **+ novo** e introduza o nome de um produto na caixa de texto.  |
| Palavras a procurar       | Conjunto de palavras-chave associadas a aplicação, com um máximo de palavras-três chave.  AppSource permite que o cliente procurar as palavras-chave com base no.  Escolha palavras de gerais, é comuns que os usuários provavelmente usarão a pesquisa para a sua aplicação.  |
|  Ocultar chave             | Uma chave secreta que é combinada com o URL de oferta para permitem-lhe visualizar a oferta antes de entrar no ar.  Esta chave é *não* uma palavra-passe.  Só pode conter carateres alfanuméricos.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Secção de artefactos de marketing

Nesta secção, irá fornecer colaterais de marketing: logótipo imagens, vídeos de instruções ou promocionais, documentação do produto e capturas de ecrã de interface de utilizador.

![Secção de artefactos de marketing da guia detalhes de loja](./media/storefront-tab-market-artifacts.png)

Em que a tabela seguinte descreve os campos neste separador: 

|      Campo            |    Descrição                 |
|  ---------------      |  ---------------               |
| Logótipo da oferta (formato png, 48 x 48) | Carregar uma imagem que será apresentado na página de pesquisa da sua aplicação. O formato é PNG e o tamanho deve ser 48x48 pixels. |
| Logótipo da oferta (formato png, 216 x 216) | Carregar uma imagem que será apresentado na página de detalhes da sua aplicação. O formato é PNG e o tamanho deve ser 216 x 216 pixel. |
| Vídeos                | Carrega vídeos promocionais ou instruções para a aplicação, com um máximo de quatro.  Para cada vídeo, preencha o nome do vídeo, o URL (YouTube ou Vimeo apenas) e a miniatura associada no formato PNG e o tamanho de 1280 x 720 de pixel.  | 
| Documentos             | Carregar documentos de promocionais ou instruções de produto no formato PDF, com um máximo de três.  Para cada documento, forneça um nome de documento.  |
| Capturas de Ecrã           | Carregar um máximo de cinco imagens da aplicação da interface do Usuário no formato PNG e o tamanho de 1280 x 720 de pixels.  Estas capturas de ecrã serão apresentado na página de detalhes do AppSource para a sua aplicação. |
|  |  |


## <a name="legal-section"></a>Secção legal

Irá fornecer *política de privacidade* e *termos de utilização* para a sua aplicação nesta secção.

![Secção legal da guia detalhes de loja](./media/storefront-tab-legal.png)

Em que a tabela seguinte descreve os campos neste separador: 

|      Campo            |    Descrição                 |
|  ---------------      |  ---------------               |
| URL de política de privacidade    | URL da política de privacidade online para a sua aplicação.  |
| Termos de utilização          | Termos de utilização para a sua aplicação em texto simples.  Os clientes do AppSource precisam aceitar estes termos antes de eles experimentam a sua aplicação. |
|  |  |


## <a name="customer-support-section"></a>Secção de suporte de cliente

Nesta secção, irá fornecer a **URL de suporte** para a sua aplicação.


## <a name="lead-management-section"></a>Levar a secção sobre gestão

Nesta secção, irá fornecer o mecanismo para lidar com oportunidades potenciais geradas pela sua listagem de oferta.  Normalmente, oportunidades potenciais são armazenados nos sistemas (CRM gestão) de relação do cliente.  São suportados os seguintes destinos: **NONE**, **tabelas do Azure**, **Dynamics CRM Online**, **ponto final de HTTPS**, **Marketo**, e **Salesforce** .  Para obter mais informações, consulte [obter oportunidades potenciais](../../cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads.md).


## <a name="next-steps"></a>Passos Seguintes

Em seguida, irá fornecer informações de suporte de técnicas e de cliente do [guia Contacts](./cpp-contacts-tab.md). 

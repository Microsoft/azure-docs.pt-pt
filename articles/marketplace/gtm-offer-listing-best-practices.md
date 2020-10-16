---
title: Oferta listando as melhores práticas - Mercado comercial da Microsoft
description: Saiba mais sobre as melhores práticas de listagem do mercado para as suas ofertas microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/06/2020
ms.openlocfilehash: 33198f95ef0309cada6f5f11c809811f23d7f826
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708650"
---
# <a name="offer-listing-best-practices"></a>Melhores práticas de listagem de ofertas

Este artigo dá sugestões para criar e envolver ofertas de mercado comercial da Microsoft. As tabelas que se seguem descrevem as melhores práticas para completar a informação da oferta no Partner Center. Para uma análise do desempenho das suas ofertas, vá ao [painel Marketplace Insights](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights) no Partner Center. 

## <a name="online-store-offer-details"></a>Detalhes da oferta de loja online

| Definição | Melhores práticas |
|:--- |:--- |  
| Nome da oferta | Para aplicações, forneça um título claro que inclua palavras-chave de pesquisa para ajudar os clientes a descobrir a sua oferta. <br> <br> Para serviços de consultoria, siga este formato: [Nome da Oferta: [Tipo de Oferta] (por exemplo, Contoso: 2 Semanas de Implementação) |
| Descrição da oferta | Forneça uma descrição clara que descreva a proposta de valor da sua oferta nas primeiras frases.  Tenha em mente que estas frases podem ser usadas nos resultados do motor de busca. Os componentes fundamentais da sua proposta de valor devem incluir: <ul> <li>Descrição do produto ou solução. </li> <li> Persona do utilizador que beneficia do produto ou solução. </li> <li> O cliente precisa ou dor nos endereços do produto ou da solução. </li> </ul> <br> Utilize o vocabulário padrão da indústria ou a redação baseada em benefícios, sempre que possível.  Não confie nas funcionalidades e funcionalidades para vender o seu produto.  Em vez disso, concentre-se no valor que entrega. <br> <br> Para anúncios de Serviços de Consultoria, indicar claramente o serviço profissional que presta. |

> [!IMPORTANT]
> Certifique-se de que o nome da sua oferta e a descrição da oferta aderem às **[Diretrizes de Marca e Marca da Microsoft](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** e outras diretrizes relevantes e específicas do produto ao referir-se às marcas comerciais da Microsoft e aos nomes de software, produtos e serviços da Microsoft.

## <a name="online-store-listing-details"></a>Detalhes da listagem de lojas online

As categorias e indústrias para uma loja online diferente serão aplicáveis a diferentes tipos de oferta.

| Loja online | Categorias <br>por loja online | Categorias <br>por loja online | Indústrias <br> para AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| **Tipo de Oferta**   |  **Azure Marketplace**  | **AppSource**  |
| Aplicação Azure | X | |
| Contentor | X | |
| Serviços de Consultoria | | | X |
| Dinâmica 365 Envolvimento com clientes & Plataforma de Potência | | X | X |
| Dinâmica 365 Gestão da Cadeia de & Finanças | | X | X | 
| Dynamics 365 Business Central | | X | X |
| Módulos IoT Edge | X | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Máquina Virtual do Azure |  X |    |

### <a name="categories"></a>Categorias

Microsoft AppSource e Azure Marketplace são lojas online que oferecem diferentes tipos de soluções. O Azure Marketplace oferece soluções de TI construídas sobre ou para o Azure.  O Microsoft AppSource oferece soluções de negócio, como aplicações SaaS da indústria, add-ins Dynamics 365, add-ins microsoft 365 e aplicações power platform.

As categorias e subcategorias são mapeadas para cada loja online com base no tipo de solução. A sua oferta será publicada no Microsoft AppSource ou no Azure Marketplace, dependendo do tipo de oferta, capacidades de transação da oferta e da seleção categoria/subcategoria. 

Selecione categorias e subcategorias que melhor se alinham com o seu tipo de solução. Pode selecionar:

* Até duas categorias, incluindo uma categoria primária e secundária (opcional).
* Até duas subcategorias para cada categoria primária e/ou secundária. Se não for selecionada nenhuma subcategoria, a sua oferta continuará a ser detetável apenas na categoria selecionada.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>IMPORTANTE: Ofertas SaaS e Add-ins Microsoft 365

Consulte [a Transação no mercado comercial](marketplace-commercial-transaction-capabilities-and-considerations.md) para obter detalhes específicos sobre como as capacidades de transação podem afetar a forma como a sua oferta pode ser vista e comprada pelos clientes do mercado. Para as ofertas da SaaS, a capacidade de transação da oferta, bem como a seleção de categorias determinarão a loja online onde a sua oferta será publicada.


| Oferta SaaS    | Oferta SaaS   | Oferta SaaS  | Oferta SaaS   | Oferta SaaS   | Oferta SaaS   | Oferta SaaS    | Loja online aplicável| Loja online aplicável |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| Faturação medido | Suplementos Microsoft 365 | Contacte-me | Transact (pelo menos 1 plano) | Plano privado | Plano apenas público | Planos privados de & pública | AppSource | Azure Marketplace |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | X<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | X<sup>1</sup> | X<sup>1</sup> |
|  |  |  | X |  |  | X | X<sup>1</sup> | X<sup>1,2</sup> |
|  |  | X |  |  |  |  | x<sup>1</sup> | X<sup>1</sup> | 

1. Dependendo da categoria/subcategoria e da seleção da indústria
2. Ofertas com planos privados serão publicadas no portal Azure


### <a name="industries"></a>Indústrias

A seleção do setor aplica-se apenas às ofertas publicadas no AppSource e serviços de consultoria publicados no Azure Marketplace.  Selecione indústrias e/ou verticais se a sua oferta aborda necessidades específicas da indústria, chamando a ação de capacidades específicas da indústria na descrição da sua oferta. Pode selecionar até duas (2) indústrias e duas (2) verticais por indústria selecionada.

>[!Note]
>Para ofertas de serviços de consultoria no Azure Marketplace, não existem verticais da indústria.

| **Indústrias** |  **Verticais** |
| :------------------- | :----------------|
| **Agricultura** | |
| **Construção de & arquitetura** | |
| **Automóvel** | |
| **Distribuição** | Atacado <br> Envio de pacotes & de encomendas |  
| **Education** | Ensino Superior <br> Edu secundário & Primário / K-12 <br> Bibliotecas & Museus |
| **Serviços Financeiros** | Mercados de Capitais & Bancários <br> Seguro | 
| **Administração Pública** |  Inteligência & Defesa <br> Governo Civil <br> Segurança Pública & Justiça |
| **Cuidados de saúde** | Pagador de Saúde <br> Provedor de Saúde <br> Produtos farmacêuticos | 
| **Hospitalidade & Viagem** | Transporte & de viagem <br> Hotéis & Lazer <br> Restaurantes & Serviços Alimentares | 
| **Recursos & De Fabrico** | Químico & Agroquímico <br> Fabrico discreto <br> Energia | 
| **Comunicação & Media** | Media & Entertainment <br> Telecomunicações | 
| **Outras Indústrias do Sector Público** | Pesca & Florestal <br> Sem Fins Lucrativos | 
| **Serviços Profissionais** | Serviços Profissionais de Parceiros <br> Legal | 
| **Setor Imobiliário** | |

Indústria apenas para o Microsoft AppSource:

| **Setor** |  **Verticais** |
| :------------------- | :----------------|
| **Retalho & Bens de Consumo** | Retalhistas <br> Bens de Consumo |

### <a name="applicable-products"></a>Produtos aplicáveis

Selecione os produtos aplicáveis com que a sua aplicação trabalha para que a oferta apareça em produtos selecionados no AppSource.

### <a name="search-keywords"></a>Pesquisar palavras-chave

As palavras-chave podem ajudar os clientes a encontrar a sua oferta quando pesquisam. Identifique as palavras-chave de pesquisa superior para a sua oferta, incorpore-as no resumo da sua oferta e descrição, bem como na secção palavra-chave da secção de detalhes da listagem de oferta.

## <a name="online-store-marketing-details"></a>Detalhes de marketing da loja online
| Definição | Melhores práticas |
|:--- |:--- |  
| Logotipo da oferta (formato PNG, de 216 × 216 a 350 x 350 px): página de detalhes da aplicação | Desenhe e otimize o seu logótipo para um meio digital:<br>Faça o upload do logótipo em formato PNG para a página de listagem de detalhes da aplicação da sua oferta. O Partner Center irá redimensioná-lo para os tamanhos de logotipo necessários. |
| Logotipo de oferta (formato PNG, 48 × 48 pixels): página de pesquisa | O Partner Center gerará este logótipo a partir do logotipo Grande que carregou. Pode substituir opcionalmente isto por uma imagem diferente mais tarde. |
| Documentos "Saiba mais" | Incluir ativos de vendas e marketing de apoio em "Saiba mais", alguns exemplos são:<ul><li>papéis brancos</li><li> brochuras</li><li>listas de verificação, ou</li><li> Apresentações de PowerPoint</li></ul><br>Guarde todos os ficheiros em formato PDF. O seu objetivo aqui deve ser educar os clientes, não vendê-los.<br><br>Adicione um link à sua página de aterragem da aplicação a todos os seus documentos e adicione parâmetros de URL para ajudá-lo a rastrear visitantes e testes. |
| Vídeos: AppSource, serviços de consultoria e SaaS oferece apenas | Os vídeos mais fortes comunicam o valor da sua oferta em forma narrativa:<ul> <li> Faça do seu cliente, não da sua empresa, o herói da história. </li> <li> O seu vídeo deve abordar os principais desafios e objetivos do seu cliente alvo. </li> <li> Comprimento recomendado: 60-90 segundos.</li> <li> Incorpore palavras de pesquisa chave que usam o nome dos vídeos. </li> <li> Considere adicionar vídeos adicionais, como como fazer, começar ou testemunhos de clientes. </li> </ul> |
| Screenshots (1280 &nbsp; &times; &nbsp; 720) | Adicione até cinco imagens:<br>Incorpore palavras de pesquisa chave nos nomes dos ficheiros. |

## <a name="link-to-your-offer-page-from-your-website"></a>Link para a sua página de oferta a partir do seu site

Quando liga do crachá appSource ou Azure Marketplace no seu site à sua listagem no mercado comercial, pode suportar análises e relatórios fortes, incluindo os seguintes parâmetros de consulta no final do URL:
* **src**: Inclua a fonte a partir da qual o tráfego é encaminhado para AppSource (por exemplo, website, LinkedIn ou Facebook).
* **mktcmpid**: O seu ID de campanha de marketing, que pode conter até 16 caracteres em qualquer combinação de letras, números, sublinhados e hífenes (por exemplo, *blogpost_12).*

O url de exemplo a seguir contém ambos os parâmetros de consulta anteriores: `https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

Ao adicionar os parâmetros ao seu URL AppSource, pode rever a eficácia da sua campanha no painel de análise no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os seus [benefícios no mercado comercial.](./gtm-your-marketplace-benefits.md)

Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar e configurar a sua oferta.

---
title: Oferecer a lista de verificação de criação - Marketplace comercial para o Azure
description: Os detalhes pode fornecer no processo de criação de oferta. -Marketplace comercial para o Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: eb824eb67e84ec4bdb93bc355ac6a6afa844ceb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701158"
---
# <a name="offer-creation-checklist"></a>Lista de verificação de criação de ofertas

O processo de criação de oferta leva-o através de várias páginas. Aqui estão os detalhes que pode fornecer em cada página, com ligações para obter mais informações sobre cada item.

Itens que é necessárias para fornecer ou especificar são indicados abaixo. Algumas áreas são opcionais ou valores padrão fornecidos, que é possível alterar conforme pretendido. Não tem que trabalhar nestas secções na ordem listada aqui.

| **Item**    | **Objetivo**  |
| :---------- | :-------------------|
| [**Novo Modal de oferta**](#new-offer-modal) | Recolhe oferece informações de identidade.  |
| [Página de configuração de oferta](#offer-setup-page) | Pode optar por utilizar os principais recursos e escolha como vender a sua oferta através da Microsoft.  |
| [Página de propriedades](#properties-page) | Defina as categorias e setores utilizados para agrupar a sua oferta no Marketplace, os contratos legais, dar suporte a sua oferta e a versão da aplicação. |
| [Página de lista da oferta](#offer-listing-page) | Defina os detalhes de oferta que serão apresentados no marketplace, incluindo descrições da sua oferta e recursos de marketing. |
| [Página de pré-visualização](#preview-page) | Defina um público de pré-visualização limitada para lançar a sua oferta antes de publicar a sua oferta em direto para o distribuirão mais abrangente do mercado. |
| [Página de configuração técnica da oferta](#technical-configuration-page)  | Apenas disponível se selecionar para vender a oferta da Microsoft. Defina os detalhes técnicos (caminho de URL, webhook, ID de inquilino e ID da aplicação) utilizados para ligar à sua oferta. |
| [**Novo plano Modal**](#plan-identity-modal) | Planear a recolhe informações de identidade.  |
| [Planear a página de lista](#plan-listing-page)  | Apenas disponível se selecionar para vender a oferta da Microsoft. Defina os detalhes utilizados para listar o plano no marketplace.  |
| [Preços do plano e de página de disponibilidade](#plan-pricing--availability-page)  | Apenas disponível se selecionar para vender a oferta da Microsoft.  Recolhe as características de negócios (modelo de preços), a disponibilidade de público-alvo e do mercado para cada plano (versão) da sua oferta.  |
| [Página de lista de unidade de teste](#test-drive-listing-page)  | Apenas disponível se selecionar a fim de oferecer uma versão de teste para a sua oferta. Defina os detalhes utilizados para lista o teste de unidade no marketplace.  |
| Página de configuração de técnicas de unidade de teste  | Apenas disponível se selecionar a fim de oferecer uma versão de teste para a sua oferta. Defina os detalhes técnicos para a demonstração (ou "unidade de teste") que permitem aos clientes experimentar a sua oferta antes de consolidar comprá-la.  |
| [Reveja e publicar a página](#review-and-publish-page)  | Selecione as alterações que pretende publicar, ver o estado de cada página e introduza notas para a equipe de certificação.  |


## <a name="new-offer-modal"></a>Nova oferta modal 

A primeira informações que será solicitado a fornecer são uma ID e um alias para a sua oferta. 

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| ID de oferta  | Obrigatório, não pode ser alterado após a criação. Máximo de 50 carateres e deve ser composto apenas carateres em minúsculas, de alfanuméricos, travessões e carateres de sublinhado. |
| Alias de oferta  | Necessário. |

## <a name="offer-setup-page"></a>Página de configuração de oferta

A página de configuração de oferta é onde pode optar ativamente por participar em diferentes canais e propostas de vendas, bem como declarar que leva a utilização dos principais recursos, tais como a versão de teste e de clientes. 

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------|  
| Gostaria de venda através de Microsoft?  | Necessário. predefinição: Sim |
| Como pretende que os clientes potenciais para interagir com a oferta listagem? (Plano de ação)  | Necessário se não de vendas por meio da Microsoft. predefinição: Avaliação gratuita, opções: "Obter agora", "Avaliação gratuita", "Contactar-me". |
| URL de avaliação  | Necessário se "Avaliação gratuita" estiver selecionada, uma vez que os clientes de forma devem interagir com a listagem de oferta. |
| URL da oferta  | Necessário se "Obter agora" estiver selecionada, uma vez que os clientes de forma devem interagir com a listagem de oferta |
| Canais  | Opcional. predefinição: Não optou pelo canal CSP (revendedor).  |
| Versão de Teste | Opcional. predefinição: Não existem test-drive ativado.  |
| Tipo de versão de teste | Necessário se estiver ativada uma versão de teste. predefinição: Nenhum selecionado. Opções: O Azure Resource Manager, o Dynamics 365 para empresas Central, o Dynamics 365 for Customer Engagement, Dynamics 365 para operações, a aplicação lógica, o Power BI.  |
| Numa gestão – ligar a um sistema CRM | Necessário se estivermos vendendo através da Microsoft, ou se listagem oferece uma vez que "Contactar-me". Predefinição: nenhum sistema CRM ligado. Opções de CRM: De tabelas do Azure, BLOBs do Azure, Dynamics CRM online, dos ponto final de HTTPs, Marketo, Salesforce  |

## <a name="properties-page"></a>Página Propriedades

A página de propriedades é onde define as categorias e setores utilizados para agrupar a sua oferta no Marketplace, os contratos legais, dar suporte a sua oferta e a versão da aplicação. Certifique-se de que fornecem detalhes de completos e precisos sobre a oferta nesta página, para que ele tem apresentado corretamente e oferecidos para o conjunto correto de clientes. 

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------|  
| Categoria e subcategoria | 3 de 1 e máx. é necessário. predefinição: Nenhum selecionado. |
| Setores e subindustries | Opcional. número máximo de 2 setores de L1 e máximo de 2 subindustries dentro de cada setor L1, predefinição: Nenhum selecionado |
| Versão da aplicação  | Opcional. predefinição: Nenhum. |
| Contrato de padrão de utilização  | Opcional. Predefinição: não selecionado.  | |
| Termos de utilização  | Necessário se o padrão de contrato não está selecionado.  |

## <a name="offer-listing-page"></a>Página de lista de oferta

A página de listagem é onde fornecer o texto e imagens que os clientes veem ao visualizar a lista da sua oferta no marketplace. 

| **Nome do campo**    | **Notas**   |
| :---------------- | :-----------| 
| Nome  | Necessárias, máximos 50 carateres. |
| Resumo  | Necessárias, máximos 100 carateres. | 
| Descrição  | Necessárias, Máx 3000 carateres. |
| Instruções de introdução  | Necessárias, Máx 3000 carateres. |
| Instruções de introdução  | Necessárias, Máx 3000 carateres. |
| Pesquisar palavras-chave  | Opcional, mas recomendado, as 3 máximas palavras-chave. |
| URL de política de privacidade  | Necessário. |
| URL de materiais de Marketing de programa CSP  | Opcional. |
| Ligações úteis Title + URL  | Opcional. |
| Suporte documenta Title + do ficheiro  | Necessária, min 1 e o número máximo de 3. Devem ter o formato de ficheiro PDF. |
| Capturas de Ecrã  | Necessário, captura de ecrã do min 1 e 5 máximo; quatro ou mais recomendados. Tem de ser 1280 X 720 em formato PNG. |
| Store logotipos (pequeno, médio, grande, toda a, Hero)  | Pequeno (48 X 48) e grande (216 X 216) necessário; outros tamanhos de opcionais mas recomendado: Média (90 x 90), toda a (255 x 115), o Hero (815 x 290). Tem de estar no formato PNG. |
| Nome de vídeos + URL + miniatura  | Opcional, mas recomendado máximos 4 vídeos. A miniatura deve ser 1280 x 720 no formato PNG. Vídeo tem de estar alojado no YouTube ou Vimeo. |
| Contactos (programa CSP, engenharia, suporte)  | Contacto de suporte e de engenharia necessárias (nome, e-mail e número de telefone); Contacto do programa CSP opcional mas recomendado. |
| URL de suporte  | Necessário. |

## <a name="preview-page"></a>Página de pré-visualização

Na página de demonstração é onde pode especificar o público-alvo para ter acesso a sua oferta de pré-visualização, para verificar se a oferta cumpre todos os seus requisitos antes de entrar no ar. 

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| E-mail do AAD/MSA + descrição | Necessário, min 1 e 10 máximo se inserido manualmente, ou até 20 se carregar um ficheiro CSV. |

## <a name="technical-configuration-page"></a>Página de configuração técnicos 

A página de configuração técnico é onde pode especificar os detalhes técnicos utilizados pela Microsoft para ligar à sua oferta. Esta página não é visível para si se decidiu não venda através de Microsoft.

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| URL da página de destino | Necessário se estivermos vendendo através da Microsoft. |
| Webhook de ligação | Necessário se estivermos vendendo através da Microsoft. |
| ID de inquilino do Azure AD | Necessário se estivermos vendendo através da Microsoft. |
| ID da aplicação do Azure AD | Necessário se estivermos vendendo através da Microsoft. |

## <a name="plan-identity-modal"></a>Plano de identidade modal

A primeira informações que é-lhe perguntado para fornecer são um nome e uma ID para o seu plano. Esta página não é visível para si se decidiu não venda através de Microsoft.

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| ID do plano  | Necessário se estivermos vendendo através da Microsoft. Ele não pode ser alterado após a criação. Máximo de 50 carateres e deve ser composto apenas carateres em minúsculas, de alfanuméricos, travessões e carateres de sublinhado. |
| Nome do plano  | Necessário se estivermos vendendo através da Microsoft. Tem de ser exclusivo em todos os planos da oferta. Máx. de 50 carateres. |

## <a name="plan-listing-page"></a>Página de lista de plano

O plano de página da lista é onde fornecer o texto para os clientes ver quando visualizar o plano no marketplace. Esta página não é visível para si se decidiu não venda através de Microsoft.

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| Descrição do plano   | Necessário se estivermos vendendo através da Microsoft. Máx. 500 caracteres. | |

## <a name="plan-pricing--availability-page"></a>Página de preços e a disponibilidade do plano

A página de preços e a disponibilidade do plano é onde define as características de negócios, o público-alvo e a disponibilidade de mercado para cada plano (versão) da sua oferta. Esta página não é visível para si se decidiu não venda através de Microsoft.

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| Disponibilidade do mercado  | Necessário, min 1 e max 141. |
| Modelo de Determinação de Preço  | Necessário. predefinição: Taxa fixa. Opções: Taxa fixa, por utilizador. |
| Mínimo e máximos postos de trabalho  | Opcional, disponível apenas se posto de trabalho com base no modelo de preços selecionado. |
| Período de faturação  | Necessário. predefinição: Mensal. Opções: Mensais, anuais. |
| Preço  | Necessário USD por mês, se a fatura mensal termo selecionado; ou USD por ano se anual de faturação termo selecionado. |
| Planear o público-alvo  | Opcional. predefinição: Plano de público. Opções: Público, privado por ID de inquilino |
| Restrito planear público-alvo (ID de inquilino + descrição)  | Necessário se o plano privado selecionada. Mínimo de 1 e 10 máximo IDs de inquilino se introduzido manualmente. Máx. 20000 se importar de ficheiro CSV. |

## <a name="test-drive-listing-page"></a>Página de lista de unidade de teste

Apenas disponível se selecionar a fim de oferecer uma versão de teste para a sua oferta. Defina os detalhes utilizados para lista o teste de unidade no marketplace.

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| Descrição  | Necessário. |
| Nome de utilizador Manual + do ficheiro  | Documento de 1 necessário, Máx. Tem de ser formato PDF. |
| Nome do vídeo, URL + miniatura  | Opcional, mas recomendado. A miniatura deve ser 533 x 324 no formato JPGP ou PNG. Vídeo tem de estar alojado no YouTube ou Vimeo. |

## <a name="review-and-publish-page"></a>Reveja e publicar a página

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| Notas para certificação  | Opcional. |

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma nova oferta de SaaS](./create-new-saas-offer.md)

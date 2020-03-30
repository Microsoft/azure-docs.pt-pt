---
title: SaaS oferecem lista de verificação de criação - Mercado Comercial para Azure
description: Os detalhes que pode fornecer no processo de criação de oferta saaS. - Mercado Comercial para o Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 60111b6a23c76314383c5f95be3eb4b38f90d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281345"
---
# <a name="saas-offer-creation-checklist"></a>Lista de verificação de criação de ofertas de SaaS

O processo de criação de oferta SaaS irá levá-lo através de várias páginas. Aqui estão os detalhes que pode fornecer em cada página, com links para saber mais sobre cada item.

Os itens que você é obrigado a fornecer ou especificar são anotados abaixo. Algumas áreas são opcionais ou têm valores predefinidos fornecidos, que pode mudar conforme desejado. Não tens de trabalhar nestas secções na ordem listada aqui.

| **Item**    | **Objetivo**  |
| :---------- | :-------------------|
| [**Nova Oferta Modal**](#new-offer-modal) | As coleções oferecem informações de identidade.  |
| [Página de configuração de oferta](#offer-setup-page) | Permite-lhe optar por utilizar funcionalidades chave e escolher como vender a sua oferta através da Microsoft.  |
| [Página de Propriedades](#properties-page) | Defina as categorias e indústrias utilizadas para agrupar a sua oferta nos mercados, os contratos legais que suportam a sua oferta e a sua versão de aplicações. |
| [Página de listagem de oferta](#offer-listing-page) | Defina os detalhes da oferta a exibir no mercado, incluindo descrições da sua oferta e ativos de marketing. |
| [Página de pré-visualização](#preview-page) | Defina um público de pré-visualização limitado para lançar a sua oferta antes de publicar a sua oferta ao vivo para o público de marketplace mais amplo. |
| [Oferecer página de configuração técnica](#technical-configuration-page)  | Só disponível se selecionar vender a oferta através da Microsoft. Defina os detalhes técnicos (url path, webhook, ID do inquilino e ID da aplicação) utilizados para se conectar à sua oferta. |
| [**Novo Plano Modal**](#plan-identity-modal) | Recolhe informações de identidade do plano.  |
| [Página de listagem de planos](#plan-listing-page)  | Só disponível se selecionar vender a oferta através da Microsoft. Defina os detalhes utilizados para listar o Plano no mercado.  |
| [Página de disponibilidade de preços de plano&](#plan-pricing--availability-page)  | Só disponível se selecionar vender a oferta através da Microsoft.  Recolhe as características do negócio (modelo de preços), público e disponibilidade de mercado para cada plano (versão) da sua oferta.  |
| [Página de listagem de unidade de teste](#test-drive-listing-page)  | Só disponível se selecionar oferecer um test drive para a sua oferta. Defina os detalhes utilizados para listar o test drive no mercado.  |
| Página de configuração técnica test drive  | Só disponível se selecionar oferecer um test drive para a sua oferta. Defina os detalhes técnicos para a demonstração (ou "test drive") que permitirá aos clientes experimentar a sua oferta antes de se comprometerem a comprá-la.  |
| [Página de Revisão e Publicação](#review-and-publish-page)  | Selecione as alterações que pretende publicar, consulte o estado de cada página e forneça notas à equipa de certificação.  |


## <a name="new-offer-modal"></a>Nova oferta modal 

As primeiras informações que lhe serão solicitadas são uma identificação e pseudónimo para a sua oferta. 

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| ID da oferta  | Obrigatório, não pode ser mudado após a criação. Max 50 caracteres e deve consistir apenas em caracteres minúsculos, alfanuméricos, traços ou sublinhados. |
| Oferta de pseudónimos  | Necessário. |

## <a name="offer-setup-page"></a>Página de configuração de oferta

A página de configuração da oferta é onde você pode optar por diferentes canais e movimentos de venda, bem como declarar o uso de funcionalidades chave, tais como test drive e leads do cliente. 

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------|  
| Gostaria de vender através da Microsoft?  | Necessário. Padrão: Sim |
| Como quer que os potenciais clientes interajam com a listagem de ofertas? (Chamada à ação)  | Necessário se não for vendido através da Microsoft. Padrão: Teste gratuito, Opções: "Obtenha-o agora", "Teste Gratuito", "Contacte-me". |
| URL de teste  | Necessário se for selecionado "Free Trial", como a forma como os clientes devem interagir com a listagem de ofertas. |
| Oferecer URL  | Necessário se "Get it Now" for selecionado, como a forma como os clientes devem interagir com a listagem de ofertas |
| Canais  | Opcional. Predefinição: Não optou pelo canal CSP (revendedor).  |
| Versão de Teste | Opcional. Predefinição: Sem acionamento de teste ativado.  |
| Tipo de Test Drive | Necessário se ativar um test drive. Predefinição: Nenhum selecionado. Opções: Gestor de Recursos Azure, Dynamics 365 para Business Central, Dynamics 365 para envolvimento com clientes, Dinâmica 365 para Operações, Aplicação Lógica, Power BI.  |
| Lead Management - ligue-se a um sistema CRM | Necessário se vender através da Microsoft, ou se a listagem de ofertas como "Contacte-me". Predefinição: sem sistema CRM ligado. Opções CRM: Mesa Azure, Blob Azure, Dynamics CRM on-line, Ponto final da HTTPs, Marketo, Salesforce  |

## <a name="properties-page"></a>Página Propriedades

A página de propriedades é onde define as categorias e indústrias usadas para agrupar a sua oferta nos mercados, os contratos legais que suportam a sua oferta e a sua versão de app. Certifique-se de fornecer detalhes completos e precisos sobre a sua oferta nesta página, para que seja exibido adequadamente e oferecido ao conjunto certo de clientes. 

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------|  
| Categoria e subcategoria | Necessário 1 e máximo 3. Predefinição: Nenhum selecionado. |
| Indústrias e subindústrias | Opcional. max 2 L1 Industries e max 2 subindústrias em cada indústria L1, Default: Nenhuma selecionada |
| Versão da aplicação  | Opcional. Padrão: Nenhum. |
| Utilizar contrato padrão  | Opcional. Predefinição: não selecionado.  | |
| Termos de utilização  | Obrigatório se o Contrato Padrão não for selecionado.  |

## <a name="offer-listing-page"></a>Página de listagem de oferta

A página de listagem é onde fornece o texto e as imagens que os clientes vêem ao ver a listagem da sua oferta no mercado. 

| **Nome do campo**    | **Notas**   |
| :---------------- | :-----------| 
| Nome  | Obrigatório, máximo 50 chars. |
| Resumo  | Obrigatório, máximo 100 chars. | 
| Descrição  | Obrigatório, no máximo 3000 chars. |
| Iniciar instruções  | Obrigatório, no máximo 3000 chars. |
| Iniciar instruções  | Obrigatório, no máximo 3000 chars. |
| Pesquisar palavras-chave  | Opcional, recomendado, max 3 palavras-chave. |
| URL de política de privacidade  | Necessário. |
| URL de Materiais de Marketing do Programa CSP  | Opcional. |
| Links úteis Título + URL  | Opcional. |
| Título de documentos comprovando + arquivo  | Obrigatório, min 1 e máx. Deve ser formato de ficheiro PDF. |
| Capturas de Ecrã  | Necessário, min 1 screenshot e máx. quatro ou mais recomendados. Deve ser 1280 X 720 em formato PNG. |
| Logotipos de loja (Pequeno, Médio, Grande, Largo, Herói)  | Pequeno (48 X 48) e Grande (216 X 216) necessários; outros tamanhos opcionais, mas recomendados: Médio (90 x 90), Largura (255 x 115), Herói (815 x 290). Deve estar em formato PNG. |
| Nome dos vídeos + URL + miniatura  | Opcional, recomendado, max 4 vídeos. A miniatura deve ser de 1280 x 720 em formato PNG. O vídeo deve ser hospedado no YouTube ou no Vimeo. |
| Contactos (Programa CSP, Engenharia, Suporte)  | Contacto de engenharia e suporte necessário (Nome, e-mail e número de telefone); Contacto do Programa CSP opcional, mas recomendado. |
| URL de suporte  | Necessário. |

## <a name="preview-page"></a>Página de pré-visualização

A página de pré-visualização é onde especifica o público para ter acesso à sua pré-visualização de oferta, para verificar se a oferta cumpre todos os seus requisitos antes de entrar em funcionação. 

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| E-mail AAD/MSA + descrição | Necessário, min 1 e máx 10 se introduzido manualmente, ou até 20 se fizer o upload de um ficheiro CSV. |

## <a name="technical-configuration-page"></a>Página de configuração técnica 

A página de configuração técnica é onde especifica os detalhes técnicos utilizados pela Microsoft para se conectar à sua oferta. Esta página não é visível para si se decidiu não vender através da Microsoft.

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| URL da página de aterragem | Necessário se vender através da Microsoft. |
| Webhook de ligação | Necessário se vender através da Microsoft. |
| ID de inquilino do Azure AD | Necessário se vender através da Microsoft. |
| Id da aplicação Azure AD | Necessário se vender através da Microsoft. |

## <a name="plan-identity-modal"></a>Plano de identidade modal

As primeiras informações que lhe são solicitadas são um nome e uma identificação para o seu Plano. Esta página não é visível para si se decidiu não vender através da Microsoft.

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| ID do plano  | Necessário se vender através da Microsoft. Não pode ser mudado depois da criação. Max 50 caracteres e deve consistir apenas em caracteres minúsculos, alfanuméricos, traços ou sublinhados. |
| Nome do Plano  | Necessário se vender através da Microsoft. Deve ser único em todos os planos da oferta. Max 50 caracteres. |

## <a name="plan-listing-page"></a>Página de listagem de planos

A página de listagem de planos é onde fornece o texto para os clientes verem ao ver o plano no mercado. Esta página não é visível para si se decidiu não vender através da Microsoft.

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| Descrição do plano   | Necessário se vender através da Microsoft. Max 500 caracteres. | |

## <a name="plan-pricing--availability-page"></a>Página de disponibilidade de preços de & de planos

A página de preços e disponibilidade do plano é onde define as características do negócio, o público e a disponibilidade do mercado para cada plano (versão) da sua oferta. Esta página não é visível para si se decidiu não vender através da Microsoft.

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| Disponibilidade do mercado  | Obrigatório, min 1 e máx. |
| Modelo de Preços  | Necessário. Predefinição: Taxa fixa. Opções: Taxa fixa, por utilizador. |
| Lugares mínimos e máximos  | Opcional, só disponível se for selecionado o modelo de preços baseado em assentos. |
| Prazo de Faturação  | Necessário. Padrão: Mensal. Opções: Mensal, Anual. |
| Preço  | USD obrigatório por mês, se o prazo mensal de faturação for selecionado; ou USD por ano se o prazo anual de faturação for selecionado. |
| Audiência de Plano  | Opcional. Padrão: Plano público. Opções: Público, Privado por ID inquilino |
| Audiência de plano restrito (ID do inquilino + descrição)  | Necessário se o plano privado for selecionado. Min 1 e max 10 iDs de inquilino se introduzidomanualmente. Max 20000 se a importação de ficheiros CSV. |

## <a name="test-drive-listing-page"></a>Página de listagem test drive

Só disponível se selecionar oferecer um test drive para a sua oferta. Defina os detalhes utilizados para listar o test drive no mercado.

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| Descrição  | Necessário. |
| Nome manual do utilizador + ficheiro  | Necessário, max 1 doc. Deve ser formato PDF. |
| Nome de vídeo, URL + miniatura  | Opcional, recomendado. A miniatura deve ser 533 x 324 em formato JPGP ou PNG. O vídeo deve ser hospedado no YouTube ou no Vimeo. |

## <a name="review-and-publish-page"></a>Rever e publicar página

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| Notas para certificação  | Opcional. |

## <a name="next-steps"></a>Passos seguintes

- [Criar uma nova oferta SaaS](./create-new-saas-offer.md)

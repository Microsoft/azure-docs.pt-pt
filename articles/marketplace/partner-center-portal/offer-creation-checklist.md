---
title: SaaS oferece lista de verificação de criação no mercado comercial da Microsoft
description: Os detalhes que pode fornecer no processo de criação de oferta saaS no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: e17edacf45583283d53c5484417f6a2dd7c6e012
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799927"
---
# <a name="saas-offer-creation-checklist-in-partner-center"></a>SaaS oferecem lista de verificação de criação no Partner Center

O processo de criação de oferta saaS leva-o através de várias páginas.  Este artigo descreve os detalhes que pode fornecer em cada página, com links para saber mais sobre cada item.

> [!NOTE]
> Se estiver a criar uma oferta de SaaS transacionável, certifique-se de que implementa a integração com [APIs de cumprimento saaS.](./pc-saas-fulfillment-apis.md)  A integração com as APIs é a única forma de a transação no Mercado funcionar corretamente. Também precisa de se certificar de que a sua aplicação utiliza a autenticação Azure AD com um único sinal (SSO). Consulte [a Azure AD e as ofertas transacionáveis da SaaS no mercado comercial.](../azure-ad-saas.md)

Os itens que é obrigado a fornecer ou especificar são anotados abaixo.  Algumas áreas são opcionais ou têm valores predefinidos fornecidos, que pode alterar conforme desejado.  Não tens de trabalhar nestas secções na ordem aqui listada.

| **Item**    | **Objetivo**  |
| :---------- | :-------------------|
| [**Nova Oferta Modal**](#new-offer-modal) | As recolhas oferecem informações de identidade.  |
| [Página de configuração de oferta](#offer-setup-page) | Permite-lhe optar por usar as principais funcionalidades e escolher como vender a sua oferta através da Microsoft.  |
| [Página de propriedades](#properties-page) | Defina as categorias e indústrias usadas para agrupar a sua oferta nos mercados, os contratos legais que suportam a sua oferta e a sua versão de aplicação. |
| [Página de listagem de oferta](#offer-listing-page) | Defina os detalhes da oferta a serem exibidos no mercado, incluindo descrições da sua oferta e ativos de marketing.|
| [Página de pré-visualização](#preview-page) | Defina um Público de pré-visualização limitado para lançar a sua oferta antes de publicar a sua oferta ao vivo para o público(s) mais amplo do mercado.|
| [Página de configuração técnica](#technical-configuration-page)  |  Só disponível se escolher para vender a oferta através da Microsoft.  Defina os detalhes técnicos (URL da página de aterragem, URL de webhook de ligação, ID do inquilino Azure AD e ID da aplicação AD Azure) usados pelo marketplace para se conectar à sua oferta.  Estes parâmetros são necessários para se integrar corretamente com a realização do SaaS e com as APIs de faturação medidos pelo Marketplace.|
| [**Novo Plano Modal**](#plan-identity-modal) | Recolhe informações de identidade do plano.  |
| [Página de listagem de planos](#plan-listing-page)  | Só disponível se escolher para vender a oferta através da Microsoft. Defina os detalhes utilizados para listar o Plano no mercado.  |
| [Página de disponibilidade de preços de plano &](#plan-pricing--availability-page)  | Só disponível se escolher para vender a oferta através da Microsoft.  Recolhe as características do negócio (modelo de preços), público e disponibilidade de mercado para cada plano (versão) da sua oferta.  |
| [Página de listagem de unidade de teste](#test-drive-listing-page)  | Só disponível se escolher para oferecer um test drive para a sua oferta. Defina os detalhes utilizados para listar o test drive no mercado.  |
| Página de configuração técnica do test drive  | Só disponível se escolher para oferecer um test drive para a sua oferta. Defina os detalhes técnicos para a demonstração (ou "test drive") que permitirá que os clientes experimentem a sua oferta antes de se comprometerem a comprá-la.  |
| [Página de comentário e publicação](#review-and-publish-page)  | Selecione as alterações que pretende publicar, consulte o estado de cada página e forneça notas à equipa de certificação.  |

## <a name="new-offer-modal"></a>Nova oferta modal

As primeiras informações que lhe serão solicitadas são um ID e um pseudónimo para a sua oferta.

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| ID da oferta  | Obrigatório, não pode ser mudado após a criação. Max 50 caracteres e deve consistir apenas em caracteres minúsculos, alfanuméricos, traços ou sublinhados. |
| Oferecer pseudónimo  | Necessário. |

## <a name="offer-setup-page"></a>Oferta página de configuração

A página de configuração da oferta é onde você pode optar por diferentes canais e vender movimentos, bem como declarar o uso de funcionalidades chave, tais como test drive e leads de cliente. 

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------|  
| Gostaria de vender através da Microsoft?  | Necessário. Padrão: Sim |
| Como pretende que os potenciais clientes interajam com a listagem de ofertas? (Chamada à ação)  | Obrigatório se não vender através da Microsoft. Padrão: Teste gratuito, Opções: "Get it now", "Free Trial", "Contact me". |
| URL de julgamento  | Requerido se o "Teste Gratuito" for selecionado, como a forma como os clientes devem interagir com a listagem de oferta. |
| Oferta URL  | Requerido se "Get it Now" for selecionado, como a forma como os clientes devem interagir com a listagem de oferta |
| Canais  | Opcional. Predefinição: Não optou pelo canal CSP (revendedor).  |
| Versão de Teste | Opcional. Predefinido: Sem acionamento de teste.  |
| Tipo de Unidade de Teste | Necessário se ativar um test drive. Predefinição: Nenhum selecionado. Opções: Azure Resource Manager, Dynamics 365 para Business Central, Dynamics 365 para Envolvimento do Cliente, Dinâmica 365 para Operações, App Lógica, Power BI.  |
| Ligação ao cliente - ligue-se a um sistema CRM | Necessário se vender através da Microsoft, ou se a listagem oferece como "Contacte-me". Predefinição: nenhum sistema CRM ligado. Opções CRM: Tabela Azure, Blob Azure, Dynamics CRM on-line, ponto final da HTTPs, Marketo, Salesforce  |

## <a name="properties-page"></a>Página Propriedades

A página de propriedades é onde você define as categorias e indústrias usadas para agrupar a sua oferta nos mercados, os contratos legais que suportam a sua oferta, e a sua versão de aplicação. Certifique-se de fornecer detalhes completos e precisos sobre a sua oferta nesta página, de modo que seja exibido adequadamente e oferecido ao conjunto de clientes certo. 

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------|  
| Categoria e subcategoria | Necessário 1 e máximo 3. Predefinição: Nenhum selecionado. |
| Indústrias e subindústrias | Opcional. max 2 Indústrias L1 e subindustrias max 2 dentro de cada indústria L1, Padrão: Nenhum selecionado |
| Versão da aplicação  | Opcional. Padrão: Nenhum. |
| Use contrato padrão  | Opcional. Predefinição: não selecionado.  | |
| Termos de utilização  | Obrigatório se o Contrato Padrão não for selecionado.  |

## <a name="offer-listing-page"></a>Página de listagem de oferta

A página de listagem é onde fornece o texto e imagens que os clientes vêem ao visualizar a listagem da sua oferta no mercado. 

| **Nome do campo**    | **Notas**   |
| :---------------- | :-----------|
| Nome  | Obrigatório, máximo 50 chars. |
| Resumo  | Obrigatório, máximo 100 chars. |
| Descrição  | Obrigatório, máximo 3000 chars. |
| Instruções de início  | Obrigatório, máximo 3000 chars. |
| Instruções de início  | Obrigatório, máximo 3000 chars. |
| Pesquisar palavras-chave  | Opcional, recomendado, máx. |
| URL de política de privacidade  | Necessário. |
| URL de materiais de marketing do programa CSP  | Opcional. |
| Links úteis Título + URL  | Opcional. |
| Título de documentos comprovativos + arquivo  | Obrigatório, min 1 e máx. Deve ser o formato de ficheiro PDF. |
| Capturas de ecrã  | Necessário, imagem de min 1 e máx. quatro ou mais recomendados. Deve ser 1280 X 720 em formato PNG. |
| Logotipos da loja (Pequeno, Médio, Grande)  | É necessário o logotipo grande (216 x 216). O Partner Center utilizará isto para criar um logótipo de tamanho Pequeno (48 x 48 pixels) e um logotipo de tamanho Médio (90 x 90 pixels). Pode substituir opcionalmente estas imagens diferentes. Os logótipos devem estar no formato PNG. |
| Nome de vídeos + URL + miniatura  | Opcional, recomendado, máximo 4 vídeos. Miniatura deve ser de 1280 x 720 em formato PNG. O vídeo deve ser apresentado no YouTube ou no Vimeo. |
| Contactos (Programa CSP, Engenharia, Apoio)  | Contacto de Engenharia e Apoio necessário (Nome, e-mail e número de telefone); CSP Programa contacto opcional, mas recomendado. |
| URL de apoio  | Necessário. |

## <a name="preview-page"></a>Página de pré-visualização

A página de pré-visualização é onde especifica o público para ter acesso à pré-visualização da sua oferta, para verificar se a oferta cumpre todos os seus requisitos antes de entrar em direto. 

| **Nome do campo**    | **Notas**   |
| :---------------- | :-----------|
| E-mail AAD/MSA + descrição | Necessário, min 1 e max 10 se introduzido manualmente, ou até 20 se carregar um ficheiro CSV. |

## <a name="technical-configuration-page"></a>Página de configuração técnica

A página de configuração técnica é onde especifica os detalhes técnicos utilizados pela Microsoft para se ligar à sua oferta. Esta página não é visível para si se decidiu não vender através da Microsoft.

> [!NOTE]
> Para ofertas transtáveis, você deve criar uma página de aterragem e sua aplicação deve usar a autenticação AD AZure com um único sinal em (SSO). Para mais informações, consulte [a Azure AD e as ofertas transacionáveis da SaaS no mercado comercial.](../azure-ad-saas.md)

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| URL da página de aterragem | Obrigatório se vender através da Microsoft. |
| Webhook de conexão | Obrigatório se vender através da Microsoft. |
| ID de inquilino do Azure AD | Obrigatório se vender através da Microsoft. |
| ID da aplicação AD Azure | Obrigatório se vender através da Microsoft. |

## <a name="plan-identity-modal"></a>Plano de identidade modal

As primeiras informações que lhe são solicitadas são um nome e uma identificação para o seu Plano. Esta página não é visível para si se decidiu não vender através da Microsoft.

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| ID do plano  | Obrigatório se vender através da Microsoft. Não pode ser mudado após a criação. Max 50 caracteres e deve consistir apenas em caracteres minúsculos, alfanuméricos, traços ou sublinhados. |
| Nome do Plano  | Obrigatório se vender através da Microsoft. Deve ser único em todos os planos da oferta. Max 50 caracteres. |

## <a name="plan-listing-page"></a>Página de listagem de plano

A página de listagem do plano é onde fornece o texto para os clientes verem ao visualizar o plano no mercado. Esta página não é visível para si se decidiu não vender através da Microsoft.

| **Nome do campo**    | **Notas**   |  
| :---------------- | :-----------| 
| Descrição do plano   | Obrigatório se vender através da Microsoft. Max 500 caracteres. | |

## <a name="plan-pricing--availability-page"></a>Plano de preços & página de disponibilidade

A página de preços e disponibilidade do plano é onde você define as características do negócio, o público e a disponibilidade de mercado para cada plano (versão) da sua oferta. Esta página não é visível para si se decidiu não vender através da Microsoft.

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| Disponibilidade do mercado  | Obrigatório, min 1 e máx. |
| Modelo de preços  | Necessário. Predefinição: Taxa fixa. Opções: Taxa fixa, por utilizador. |
| Lugares mínimos e máximos  | Opcional, disponível apenas se o modelo de preços baseado em assentos for selecionado. |
| Prazo de faturação  | Necessário. Padrão: Mensalmente. Opções: Mensal, Anual. |
| Preço  | USD exigido por mês, se for selecionado mensalmente; ou USD por ano se o termo de faturação anual for selecionado. |
| Plan Audience  | Opcional. Padrão: Plano público. Opções: Público, Privado por ID do inquilino |
| Audiência de plano restrito (ID + descrição do inquilino)  | Necessário se o plano privado for selecionado. Min 1 e max 10 identificações de inquilinos se introduzido manualmente. Max 20000 se importação de ficheiros CSV. |

## <a name="test-drive-listing-page"></a>Página de listagem de Unidade de Teste

Só disponível se escolher para oferecer um test drive para a sua oferta. Defina os detalhes utilizados para listar o test drive no mercado.

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| Descrição  | Necessário. |
| Nome manual do utilizador + ficheiro  | Obrigatório, max 1 doc. Deve ser formato PDF. |
| Nome de vídeo, URL + miniatura  | Opcional, recomendado. Miniatura deve ser 533 x 324 em formato JPGP ou PNG. O vídeo deve ser apresentado no YouTube ou no Vimeo. |

## <a name="review-and-publish-page"></a>Rever e publicar página

| **Nome do campo**    | **Notas**   | 
| :---------------- | :-----------| 
| Notas para certificação  | Opcional. |

## <a name="next-steps"></a>Passos seguintes

- [Criar uma nova oferta SaaS](./create-new-saas-offer.md)

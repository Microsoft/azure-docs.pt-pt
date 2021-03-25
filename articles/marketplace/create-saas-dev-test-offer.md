---
title: Criar uma oferta de teste
description: Como criar uma oferta de desenvolvimento separada para testar a sua oferta de produção no programa de marketplace comercial no Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 58649e9a864e64ab5781cff3b663e190dac50cb6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050666"
---
# <a name="create-a-test-offer"></a>Criar uma oferta de teste

Para se desenvolver num ambiente separado da sua oferta de produção, criará uma oferta separada de teste e desenvolvimento (DEV) e uma oferta de produção separada (PROD). Para obter informações sobre os benefícios da utilização de uma oferta separada de DEV, consulte [a oferta do Plano SaaS.](plan-saas-offer.md#test-offer)

Configurar a maioria das definições da mesma forma nas ofertas DEV e PROD. Por exemplo, a linguagem e os ativos oficiais de marketing, tais como imagens e logotipos, devem ser os mesmos. Nos casos em que a configuração é a mesma, pode copiar e colar campos dos planos da oferta DEV aos planos da oferta PROD.

As seguintes secções descrevem as diferenças de configuração entre as ofertas DEV e PROD.

## <a name="offer-setup-page"></a>Oferta página de configuração

Recomendamos que use o mesmo pseudónimo na caixa **Alias** de ambas as ofertas, mas apê -lo "_test" ao pseudónimo da oferta DEV. Por exemplo, se o pseudónimo da sua oferta PROD for "contososolution" então o pseudónimo da oferta DEV deve ser "contososolution_test". Desta forma, pode identificar facilmente qual a sua oferta DEV da sua oferta PROD.

Na secção **de condutores** do Cliente, utilize e a mesa Azure ou um ambiente DEV de teste para a oferta DEV. Utilize o sistema de gestão de chumbo pretendido para a oferta PROD.

## <a name="properties-page"></a>Página Propriedades

Configure esta página da mesma forma nas ofertas DEV e PROD.

## <a name="offer-listing-page"></a>Página de listagem de oferta

Configure esta página da mesma forma nas ofertas DEV e PROD.

## <a name="preview-audience"></a>Audiência de pré-visualização

Na oferta DEV, inclua o nome principal do utilizador (AAD) do Azure Ative Directory (AAD) ou o endereço de e-mail da conta Microsoft (MSA) de desenvolvedores e testadores, incluindo você mesmo. Por favor, note que o nome principal do utilizador de um utilizador no AAD pode ser diferente do e-mail do utilizador. Por exemplo, jane.doe@contoso.com não funcionará, mas janedoe@contoso.com funcionará. Estas são as pessoas que terão acesso à oferta DEV quando partilhar o link **Preview** durante a fase de desenvolvimento e teste.

Na oferta PROD, inclua o nome principal do utilizador Azure ou o e-mail da Conta Microsoft dos utilizadores que validarão a oferta antes de selecionar o **botão Go Live** para publicar a oferta ao vivo.

## <a name="technical-configuration-page"></a>Página de configuração técnica

Esta tabela descreve as diferenças entre as definições para ofertas DEV e ofertas PROD.

***Quadro 1: Diferenças técnicas de configuração***

| Definição | Oferta DEV | Oferta PROD |
| ------------ | ------------- | ------------- |
| URL da página de aterragem | Introduza o seu ponto final dev/teste. | Insira o seu ponto final de produção. |
| Webhook de conexão | Introduza o seu ponto final dev/teste. | Insira o seu ponto final de produção. |
| ID do inquilino do Diretório Ativo Azure | Insira o seu ID de registo de aplicativo de teste (ID de diretório AAD). | Insira o seu ID de registo de aplicativo de produção. |
| ID de aplicação de diretório ativo Azure | Introduza o ID do seu pedido de inscrição de aplicação de teste (ID do cliente). | Insira o ID do seu aplicativo de produção. |
||||

## <a name="plan-overview-page"></a>Página geral do plano

Ao criar os seus planos, recomendamos que utilize o mesmo nome _de Identificação_ e _Plano_ de Plano nas ofertas DEV e PROD, exceto anexar o ID do plano na oferta DEV com **_test**. Por exemplo, se o ID do Plano na oferta PROD for "empresa", então o plano de identificação na oferta DEV deve ser "enterprise_test". Desta forma, pode identificar facilmente qual a sua oferta DEV da sua oferta PROD. Você vai criar planos na oferta PROD com os modelos de preços e preços que você decide são os melhores para a sua oferta.

### <a name="plan-listing"></a>Listagem de planos

No separador de lista do Plano de **Visão**  >   Geral, introduza a mesma descrição do plano nos planos DEV e PROD.

### <a name="pricing-and-availability-page"></a>Página de preços e disponibilidade

Esta secção fornece orientações para completar a página de preços e disponibilidade **do Plano.**  >  

#### <a name="markets"></a>Mercados

Selecione os mesmos mercados para as ofertas DEV e PROD.

#### <a name="pricing"></a>Preços

Use a oferta DEV para experimentar modelos de preços. Depois de verificar qual o modelo de preços ou modelos que funcionam melhor, criará os planos na oferta PROD com os modelos de preços e preços que deseja.

A oferta de DEV deve ter planos com preços zero ou muito baixos nos planos. A oferta PROD terá os preços que pretende cobrar aos clientes.

> [!NOTE]
> Informação que o utilizador deve notar mesmo que os skimmingPurchases efetivos efetivos feitos em Preview serão processados tanto para ofertas DEV como PROD. Se uma oferta tiver um preço de $100/mo, a sua empresa será cobrada $100. Se isso acontecer, você pode abrir um [bilhete de apoio](support.md) e nós emitiremos um pagamento pelo valor total (e não aceitaremos nenhuma taxa de agência).

#### <a name="pricing-model"></a>Modelo preços

Utilize o mesmo modelo de preços nos planos das ofertas DEV e PROD. Por exemplo, se o plano na oferta PROD for fixo, com um prazo de faturação mensal, então configufique o plano na oferta DEV usando o mesmo modelo.

Para reduzir o seu custo para testar os modelos de preços, incluindo as dimensões dos contadores personalizados do Marketplace, recomendamos que configuure a secção de **Preços** do **separador preços e disponibilidade,** na oferta DEV com preços mais baixos do que a oferta PROD. Aqui ficam algumas diretrizes que pode seguir ao definir preços para os planos na oferta DEV.

***Quadro 2: Orientações sobre preços***

| Preço | Comentário |
| ------------ | ------------- |
| 0,00 € | Desa fixar um custo total de transação de zero para não ter impacto financeiro. Use este preço ao fazer chamadas para as APIs de medição, ou para testar planos de compra na sua oferta enquanto desenvolve a sua solução. |
| $0,01 - $49.99 | Utilize esta gama de preços para testar análises, relatórios e o processo de compra. |
| $50,00 e superior | Use esta gama de preços para testar o pagamento. Para obter informações sobre o nosso horário de pagamento, consulte [os horários e processos do Payout.](/partner-center/payout-policy-details) |
|||

Para evitar ser cobrada uma taxa de processamento no seu teste, abra um [bilhete de apoio](support.md).

#### <a name="free-trial"></a>Avaliação gratuita

Não permita um teste gratuito para a oferta de DEV.

## <a name="co-sell-with-microsoft-page"></a>Co-venda com página da Microsoft

Não configuure a **Co-venda com** o separador Microsoft da oferta DEV.

## <a name="resell-through-csps"></a>Revenda através de CSP

Não configuure a revenda através do separador **CSPs** da oferta DEV.

## <a name="next-steps"></a>Passos seguintes

- [Testar e publicar uma oferta SaaS](test-publish-saas-offer.md)

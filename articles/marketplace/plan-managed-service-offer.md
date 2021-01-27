---
title: Planeie uma oferta de Serviço Gerido para o mercado comercial da Microsoft
description: Como planear uma nova oferta de Serviço Gerido para o Azure Marketplace utilizando o programa de marketplace comercial no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: 57a6883a2c4fff14507df27b55ac0f6621306bf5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879280"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Como planear uma oferta de Serviço Gerido para o mercado comercial da Microsoft

Este artigo introduz os requisitos para a publicação de uma oferta de Serviço Gerido ao mercado comercial da Microsoft através do Partner Center.

Os Serviços Geridos são ofertas do Azure Marketplace que permitem a gestão de inquilinos e multi-inquilinos com o Farol Azure. Para saber mais, veja [o que é o Farol de Azure?](../lighthouse/overview.md) Quando um cliente compra uma oferta de Serviço Gerido, é capaz de delegar uma ou mais subscrições ou grupo de recursos

## <a name="eligibility-requirements"></a>Requisitos de elegibilidade

Para publicar um Serviço Gerido, deve ter ganho uma Competência Gold ou Silver Microsoft na Cloud Platform. Esta competência demonstra a sua experiência aos clientes. Para obter mais informações, consulte [as Competências da Rede de Parceiros da Microsoft.](https://partner.microsoft.com/membership/competencies)

As ofertas devem satisfazer todas as políticas de [certificação do mercado comercial aplicáveis](/legal/marketplace/certification-policies) a publicar no Azure Marketplace.

## <a name="customer-leads"></a>Ligações ao cliente

Tem de ligar a sua oferta ao seu sistema de gestão de relacionamento com o cliente (CRM) para recolher informações do cliente. O cliente será solicitado permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e loja online onde encontraram a sua oferta, serão enviados para o sistema CRM que configuraste. O mercado comercial suporta diferentes tipos de sistemas CRM, juntamente com a opção de utilizar uma tabela Azure ou configurar um ponto final HTTPS utilizando o Power Automate.

Pode adicionar ou modificar uma ligação CRM a qualquer momento durante ou após a criação da oferta. Para obter orientações detalhadas, consulte [os leads do Cliente da sua oferta de mercado comercial.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

## <a name="legal-contracts"></a>Contratos legais

Na página propriedades do Partner Center, ser-lhe-á pedido que forneça **termos e condições** para o uso da sua oferta. Pode introduzir os seus termos diretamente no Partner Center ou fornecer o URL onde podem ser encontrados. Os clientes serão obrigados a aceitar estes termos e condições antes de adquirir a sua oferta.

## <a name="offer-listing-details"></a>Oferecer detalhes da listagem

Quando criar a sua oferta de Serviço Gerido no Partner Center, introduzirá texto, imagens, documentos e outros detalhes da oferta. Isto é o que os clientes verão quando descobrirem a sua oferta no Azure Marketplace. Veja o seguinte exemplo:

:::image type="content" source="media/example-managed-service.png" alt-text="Ilustra como uma oferta de Serviço Gerido aparece no Azure Marketplace.":::

**Descrições de chamadas**

1. Logótipo
1. Name
1. Breve descrição
1. Categorias
1. Contratos legais e política de privacidade
1. Descrição
1. Screenshots/vídeos
1. Ligações úteis

Aqui está um exemplo de como a listagem de ofertas aparece no portal Azure:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Ilustra como esta oferta aparece no portal Azure.":::

**Descrições de chamadas**

1. Nome
2. Descrição
3. Ligações úteis
4. Screenshots/vídeos

> [!NOTE]
> Se a sua oferta estiver em outra língua que não o inglês, a listagem de oferta pode estar nesse idioma, mas a descrição deve começar ou terminar com a frase em inglês "Este serviço está disponível em &lt; linguagem do conteúdo da sua oferta>". Também pode fornecer documentos comprovativos num idioma diferente daquele usado na listagem de detalhes da oferta.

Para ajudar a criar a sua oferta mais facilmente, prepare alguns destes itens com antecedência. Os seguintes itens são necessários, salvo indicação em contrário.

**Nome:** este será o título da sua listagem de oferta no mercado comercial. O nome pode ser marcado. Não pode conter emojis (a menos que sejam os símbolos da marca e dos direitos de autor) e deve estar limitado a 50 caracteres.

**Resumo dos resultados** da pesquisa : descreva o propósito ou objetivo da sua oferta em 100 caracteres ou menos. Este resumo é utilizado no mercado comercial que enumera os resultados da pesquisa. Não devia ser idêntico ao título. Considere incluir as suas palavras-chave seo de topo.

**Breve descrição**: forneça uma breve descrição da sua oferta (até 256 caracteres). Será exibido na sua lista de ofertas no portal Azure.

**Descrição:** descreva a sua oferta em 3.000 caracteres ou menos. Esta descrição será exibida na listagem do mercado comercial. Considere incluir uma proposta de valor, benefício chave, categoria ou associações do setor, e quaisquer divulgações necessárias.

Aqui ficam algumas dicas para escrever a sua descrição:

* Descreva claramente o valor da sua oferta nas primeiras frases, incluindo:
    * O tipo de utilizador que beneficia da oferta.
    * O que o cliente precisa ou emite os endereços da oferta.
* Lembre-se que as primeiras frases podem ser exibidas nos resultados da pesquisa.
* Use o vocabulário específico da indústria.

Pode utilizar tags HTML para formatar a sua descrição. Para obter informações sobre formatação HTML, consulte [as tags HTML suportadas no mercado comercial para apresentar descrições](./supported-html-tags.md).

**Ligação política de** privacidade : forneça um URL à política de privacidade, hospedada no seu site. Você é responsável por garantir que a sua oferta está em conformidade com as leis e regulamentos de privacidade, e por fornecer uma política de privacidade válida.

**Links úteis** (opcional): faça upload de documentos online suplementares sobre a sua oferta.

**Informações de contacto**: forneça nome, endereço de e-mail e número de telefone de duas pessoas na sua empresa (pode ser uma delas): um contacto de suporte e um contacto de engenharia. Usaremos esta informação para comunicar consigo sobre a sua oferta. Esta informação não é mostrada aos clientes, mas pode ser fornecida aos parceiros cloud Solution Provider (CSP)

**Suporte URLs** (opcional): se tiver websites de suporte para clientes Azure Global e/ou clientes do Governo Azure, forneça esses URLs.

**Marketplace media – logotipos**: forneça um ficheiro PNG para o logotipo de grande dimensão da sua oferta. O Partner Center irá usá-lo para criar logótipos médios e pequenos. Pode substituir opcionalmente estes logótipos por uma imagem diferente mais tarde.

* O grande logótipo (de 216 x 216 a 350 x 350 px) aparece na sua lista de ofertas no Azure Marketplace.
* O logótipo médio (90 x 90 px) é mostrado quando um novo recurso é criado.
* O pequeno logótipo (48 x 48 px) é utilizado nos resultados de pesquisa do Azure Marketplace.

Siga estas diretrizes para os seus logotipos:

* Certifique-se de que a imagem não está esticada.
* O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias no logótipo.
* As cores do portal Azure são brancas e pretas. Não use isto como pano de fundo do seu logotipo. Recomendamos cores primárias simples que tornem o seu logotipo proeminente.
* Se utilizar um fundo transparente, certifique-se de que o logótipo e o texto não são brancos, pretos ou azuis.
* O aspeto e a sensação do seu logotipo devem ser planos. Evite gradientes. Não coloque texto no logótipo, nem mesmo o nome da empresa ou da marca.

**Marketplace media – screenshots** (opcional): Adicione até cinco imagens que demonstram como a sua oferta funciona. Todas as imagens devem ter 1280 x 720 pixels de tamanho e em . Formato PNG.

**Marketplace media – vídeos** (opcional): faça upload até cinco vídeos que demonstrem a sua oferta. Os vídeos devem ser apresentados no YouTube ou Vimeo e ter uma miniatura (ficheiro PNG 1280 x 720).

## <a name="preview-audience"></a>Audiência de pré-visualização

Um público de pré-visualização pode aceder à sua oferta antes de ser publicada no Azure Marketplace para testá-la. Na página de **audiência de pré-visualização** do Partner Center, pode definir um público de pré-visualização limitado.

> [!NOTE]
> Um público de pré-visualização difere de um plano privado. Um plano privado é aquele que disponibiliza apenas para um público específico que escolher. Isto permite-lhe negociar um plano personalizado com clientes específicos.

Pode enviar convites para endereços de e-mail da Conta Microsoft (MSA) ou do Azure Ative Directory (Azure AD). Adicione até 10 endereços de e-mail manualmente ou importe até 20 com um ficheiro .csv. Se a sua oferta já estiver ao vivo, ainda pode definir um público de pré-visualização para testar quaisquer alterações ou atualizações da sua oferta.

## <a name="plans-and-pricing"></a>Planos e preços

As ofertas de Serviço Gerido requerem pelo menos um plano. Um plano define o âmbito da solução, os limites e os preços associados, se aplicável. Pode criar vários planos para a sua oferta para dar aos seus clientes diferentes opções técnicas e de preços. Para orientações gerais sobre planos, incluindo planos [privados, consulte planos e preços para ofertas de mercado comercial.](plans-pricing.md)

Serviços Geridos suportam apenas um modelo de preços: **Traga a sua própria licença (BYOL)**. Isto significa que vai cobrar diretamente aos seus clientes, e a Microsoft não lhe cobrará quaisquer taxas.

## <a name="next-steps"></a>Próximos passos

* [Criar uma oferta de Serviço Gerido](./create-managed-service-offer.md)
* [Melhores práticas de listagem de ofertas](./gtm-offer-listing-best-practices.md)
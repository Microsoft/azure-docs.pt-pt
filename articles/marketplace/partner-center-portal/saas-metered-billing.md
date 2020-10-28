---
title: Faturação medido para ofertas de SaaS utilizando o serviço de medição de mercado comercial da Microsoft
description: Saiba mais sobre modelos de faturação flexível para ofertas SaaS utilizando o serviço de medição de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9f72d54fda8f66c2fce35f0520b51406aa276bb0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892758"
---
# <a name="metered-billing-for-saas-using-the-commercial-marketplace-metering-service"></a>Faturação medido para SaaS utilizando o serviço de medição de mercado comercial

Com o serviço de medição de mercado comercial, pode criar software como um serviço (SaaS) que é cobrado de acordo com unidades não padrão. Antes de publicar uma oferta SaaS para o mercado comercial, você define as dimensões de faturação como largura de banda, bilhetes ou e-mails processados.  Os clientes pagam então de acordo com o seu consumo destas dimensões, com o seu sistema a informar a Microsoft através do serviço de medição de mercado comercial API de eventos faturados à medida que ocorrem.  

## <a name="prerequisites-for-metered-billing"></a>Pré-requisitos para faturação medido

Para uma oferta saaS para usar faturação medido, deve primeiro:

- Cumpra todos os requisitos de oferta para uma [venda através da oferta da Microsoft,](../plan-saas-offer.md#listing-options) conforme delineado na Create a [SaaS offer no mercado comercial.](../create-new-saas-offer.md)
- Integre as [APIs de Cumprimento saaS](./pc-saas-fulfillment-api-v2.md) para os clientes procurá-lo e ligar-se à sua oferta.  
- Esteja configurado para o modelo de preços **fixos** ao cobrar aos clientes pelo seu serviço.  As dimensões são uma extensão opcional ao modelo de preços fixos. 

Em seguida, a oferta SaaS pode integrar-se com o [serviço de medição de mercado comercial APIs](./marketplace-metering-service-apis.md) para informar a Microsoft de eventos faturantes.

>[!Note]
>O serviço de medição do mercado está disponível apenas para o modelo de faturação forfedária, e não se aplica ao modelo de faturação por utilizador.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Como a faturação medido se encaixa com os preços

Compreender a hierarquia da oferta é importante, quando se trata de definir a oferta juntamente com os seus modelos de preços.

- Cada oferta SaaS está configurada para vender através da Microsoft ou não.  Uma vez publicada uma oferta, esta opção não pode ser alterada.
- Cada oferta saaS, configurada para vender através da Microsoft, pode ter um ou mais planos.  Um utilizador subscreve a oferta saaS, mas é adquirido através da Microsoft no contexto de um plano.
- Cada plano tem um modelo de preços associado: **taxa fixa** ou **por utilizador.** Todos os planos de uma oferta devem ser associados ao mesmo modelo de preços. Por exemplo, não pode haver uma oferta com planos para um modelo de preços fixos, e outro modelo de preços por utilizador.
- Dentro de cada plano configurado para um modelo de faturação forfetária, está incluída pelo menos uma taxa recorrente (que pode ser $0):
    - Taxa **mensal** recorrente: taxa mensal fixa que é pré-paga numa recorrência mensal quando o utilizador compra o plano.
    - Taxa **anual** recorrente: taxa anual fixa que é pré-paga numa recorrência anual quando o utilizador compra o plano.
- Além das taxas recorrentes, um plano de taxa fixa também pode incluir dimensões personalizadas opcionais usadas para cobrar aos clientes pelo uso excessivo não incluído na taxa fixa.  Cada dimensão representa uma unidade faturada que o seu serviço comunicará à Microsoft utilizando o serviço de medição de [mercado comercial API](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Oferta de amostras

Como exemplo, a Contoso é uma editora com um serviço SaaS chamado Contoso Notification Services (CNS). O CNS permite que os seus clientes enviem notificações por e-mail ou texto. A Contoso está registada como editora no Partner Center para o programa de marketplace comercial para publicar ofertas da SaaS aos clientes da Azure.  Existem dois planos associados ao SNC, descritos abaixo:

- Plano básico
    - Envie 10000 e-mails e 1000 textos por $0/mês (taxa mensal fixa)
    - Além dos 10000 e-mails, pague $1 por cada 100 e-mails
    - Além dos 1000 textos, pague $0,02 por cada texto

    [![Preços básicos do plano](./media/saas-basic-pricing.png "Clique para ver ampliada")](./media/saas-basic-pricing.png)

- Plano Premium
    - Envie 50000 e-mails e 10000 textos para e-mails de $350/mês ou 5M e textos 1M por $3500 por ano
    - Além dos 50000 e-mails, pague $0,5 por cada 100 e-mails
    - Além dos 10.000 textos, pague $0,01 por cada texto

    [![Preços premium do plano](./media/saas-premium-pricing.png "Clique para ver ampliada")](./media/saas-premium-pricing.png)

- Plano empresarial
    - Envie um número ilimitado de e-mails e 50000 textos por $400/mês
    - Além dos 50.000 textos pagam $0.005 por cada txt

    [![Preços do plano empresarial](./media/saas-enterprise-pricing.png "Clique para ver ampliada")](./media/saas-enterprise-pricing.png)

Com base no plano selecionado, uma subscrição de compra de clientes Azure à oferta do CNS SaaS poderá enviar a quantidade incluída de texto e e-mails por período de subscrição (mês ou ano como aparece nos detalhes da subscrição - startDate e endDate).  Contoso conta o uso até à quantidade incluída na base sem enviar quaisquer eventos de utilização para a Microsoft. Quando os clientes consomem mais do que a quantidade incluída, não têm de alterar planos ou fazer nada de diferente.  A Contoso medirá o excesso de tempo para além da quantidade incluída e começará a emitir eventos de utilização à Microsoft para cobrar o uso excessivo utilizando o serviço de medição de [mercado comercial API](./marketplace-metering-service-apis.md).  Por sua vez, a Microsoft cobrará ao cliente a utilização por excesso de consumo, conforme especificado pelo editor nas dimensões personalizadas. A faturação excessiva é feita no próximo ciclo de faturação (mensalmente, mas pode ser trimestral ou precoce para alguns clientes).  Para um plano de taxa fixa mensal, a faturação excessiva será feita para cada mês em que tenha ocorrido excesso de peso.  Para um plano de taxa fixa anual, uma vez consumida a quantidade incluída na base por ano, todo o uso adicional emitido pelo contador personalizado será faturado como overage durante cada ciclo de faturação (mensal) até ao final do período de ano da subscrição.

## <a name="billing-dimensions"></a>Dimensões de faturação

Cada dimensão de faturação define uma unidade personalizada através da qual o ISV pode emitir eventos de utilização.  As dimensões de faturação também são usadas para comunicar ao cliente sobre como serão faturadas para usar o software.  São definidos da seguinte forma:

- **ID** : o identificador de dimensão imutável referenciado ao emitir eventos de utilização.
- **Nome do visor** : o nome de visualização associado à dimensão, por exemplo , "mensagens de texto enviadas".
- **Unidade de Medida** : descrição da unidade de faturação, por exemplo "por mensagem de texto" ou "por 100 e-mails".
- **Preço por unidade em USD:** o preço de uma unidade da dimensão.  Pode ser 0. 
- **Quantidade mensal incluída na base:** quantidade de dimensão incluída por mês para os clientes que pagam a mensalidade recorrente, deve ser um número inteiro. Pode ser 0 ou ilimitado.
- **Quantidade anual incluída na base:** quantidade de dimensão incluída por ano para os clientes que pagam a taxa anual recorrente, deve ser um número inteiro. Pode ser 0 ou ilimitado.

As dimensões da faturação são partilhadas em todos os planos para uma oferta.  Alguns atributos aplicam-se à dimensão em todos os planos, e outros atributos são específicos do plano.

Os atributos, que definem a dimensão em si, são partilhados em todos os planos para uma oferta.  Antes de publicar a oferta, uma alteração feita a estes atributos a partir do contexto de qualquer plano irá afetar a definição de dimensão em todos os planos.  Uma vez publicada a oferta, estes atributos deixarão de ser editáveis.  Estes atributos são:

- ID
- Nome a Apresentar
- Unidade de Medida

Os outros atributos de uma dimensão são específicos de cada plano e podem ter valores diferentes de plano a plano.  Antes de publicar o plano, pode editar estes valores e só este plano será afetado.  Uma vez publicado o plano, estes atributos deixarão de ser editáveis.  Estes atributos são:

- Preço por unidade em USD
- Quantidade mensal incluída na base  
- Quantidade anual incluída na baseIncluded  

As dimensões também têm dois conceitos especiais, "habilitados" e "infinitos":

- **Ativado** indica que este plano participa nesta dimensão.  Se estiver a criar um novo plano que não envie eventos de utilização com base nesta dimensão, talvez queira deixar esta opção descontrolada.  Além disso, quaisquer novas dimensões adicionadas após a publicação de um plano pela primeira vez aparecerão como "não habilitadas" no plano já publicado.  Uma dimensão desativada não aparecerá em nenhuma lista de dimensões para um plano visto pelos clientes.
- **Infinitamente** representada pelo símbolo do infinito "∞", indica que este plano participa nesta dimensão, mas não emite uso contra esta dimensão.  Se quiser indicar aos seus clientes que a funcionalidade representada por esta dimensão está incluída no plano, mas sem limite de utilização.  Uma dimensão com uso infinito aparecerá em listas de dimensões para um plano visto pelos clientes, com a indicação de que nunca incorrerá numa taxa para este plano.

>[!Note] 
>Os seguintes cenários são explicitamente apoiados: <br> - Pode adicionar uma nova dimensão a um novo plano.  A nova dimensão não será ativada para quaisquer planos já publicados. <br> - Pode publicar um plano **forfeccionado** sem dimensões, depois adicionar um novo plano e configurar uma nova dimensão para esse plano. A nova dimensão não será ativada para planos já publicados.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>Fixação do preço de dimensão por unidade por mercado suportado

Tal como os preços fixos, os preços de dimensão da faturação podem ser definidos por país ou região suportados. É necessário utilizar a funcionalidade de importação e exportação de dados de preços no Partner Center, da seguinte forma.

1. Defina as dimensões desejadas e marque quais os mercados que são apoiados. 
1. Exporte estes dados para um ficheiro.
1. Adicione os preços corretos por país/região e importe o ficheiro no Partner Center.

A UI do contador mudará para refletir que os preços da dimensão só podem ser vistos no ficheiro.

[![dimensões do serviço de medição de mercado comercial](media/metering-service-dimensions.png "Clique para ver ampliada")](media/metering-service-dimensions.png)

### <a name="private-plan"></a>Plano privado

Tal como os planos de taxa fixa, um plano com dimensões pode ser definido como plano privado, acessível apenas pelo público definido do plano.

## <a name="constraints"></a>Restrições

### <a name="trial-behavior"></a>Comportamento experimental

A faturação medido utilizando o serviço de medição do mercado comercial não é compatível com a oferta de um teste gratuito.  Não é possível configurar um plano para utilizar a faturação medido e um julgamento gratuito.

### <a name="locking-behavior"></a>Comportamento de bloqueio

Uma vez que uma dimensão utilizada com o serviço de medição do mercado comercial representa uma compreensão de como um cliente vai pagar pelo serviço, todos os detalhes para uma dimensão já não são editáveis após a sua publicação.  É importante que tenha as suas dimensões totalmente definidas para um plano antes de publicar.

Uma vez que uma oferta é publicada com uma dimensão, os detalhes de nível de oferta para essa dimensão não podem mais ser alterados:

- ID
- Nome a Apresentar
- Unidade de Medida

Uma vez publicado um plano, os detalhes do plano já não podem ser alterados:

- Preço por unidade em USD
- Quantidade mensal incluída na base
- Quantidade anual incluída na base
- Se a dimensão está ativada para o plano ou não

### <a name="upper-limits"></a>Limites superiores

O número máximo de dimensões que podem ser configuradas para uma única oferta é de 30 dimensões únicas.

## <a name="get-support"></a>Obter suporte

Se tiver um dos seguintes problemas, pode abrir um bilhete de apoio.

- Problemas técnicos com o serviço de medição de mercado API.
- Um problema que precisa de ser agravado devido a um erro ou erro do seu lado (ex. evento de utilização errada).
- Quaisquer outras questões relacionadas com a faturação medido.

Para compreender as opções de suporte do editor e abrir um bilhete de suporte com a Microsoft, siga as instruções de [Suporte para o programa de marketplace comercial no Partner Center](../support.md).

## <a name="next-steps"></a>Passos seguintes

- [APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis.md)

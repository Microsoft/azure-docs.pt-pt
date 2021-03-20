---
title: Faturação medido para aplicações geridas utilizando o serviço de medição do mercado | Mercado Azure
description: Esta documentação é um guia para os ISVs que publicam aplicações Azure com modelos de faturação flexíveis.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d015cec30e516541b50c2acfac38fad898965e1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96436356"
---
# <a name="managed-application-metered-billing"></a>Faturação gerida com medidos de aplicação 

Com o serviço de medição marketplace, pode criar planos de aplicação geridos para ofertas de aplicações Azure que são cobradas de acordo com unidades não padrão. Antes de publicar esta oferta, você define as dimensões de faturação tais como largura de banda, bilhetes ou e-mails processados. Os clientes pagam então de acordo com o seu consumo destas dimensões.  O seu sistema informará a Microsoft através do serviço de medição do Marketplace API de eventos faturantes à medida que ocorrem.

## <a name="prerequisites-for-metered-billing"></a>Pré-requisitos para faturação medido

Para que um plano de candidatura gerido utilize faturação medido, deve:

* Satisfaça todos os requisitos de oferta conforme descrito na [Create a azure application offer](../create-new-azure-apps-offer.md).
* Configure **preços** para cobrar aos clientes o custo mensal do seu serviço. O preço pode ser zero se não quiser cobrar uma taxa fixa e, em vez disso, depender inteiramente da faturação medido.
* Definir **as dimensões de faturação** para os eventos de medição que o cliente pagará em cima da taxa fixa.
* Integre-se com o [serviço de medição de marketplace APIs](./marketplace-metering-service-apis.md) para informar a Microsoft de eventos faturantes.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Como a faturação medido se encaixa com os preços

Quando se trata de definir a oferta juntamente com os seus modelos de preços, é importante compreender a hierarquia da oferta.

* Cada oferta de aplicação Azure pode ter modelo de solução ou planos de aplicação geridos.
* A faturação medido é implementada apenas com planos de candidatura geridos.
* Cada plano de candidatura gerido tem um modelo de preços associado a ele. 
* O modelo de preços tem uma taxa mensal recorrente, que pode ser fixada em $0.
* Além da taxa recorrente, o plano também pode incluir dimensões opcionais usadas para cobrar aos clientes pelo uso não incluído na taxa fixa. Cada dimensão representa uma unidade faturada que o seu serviço comunicará à Microsoft utilizando o [serviço de medição do Marketplace API](marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Oferta de amostras

Como exemplo, a Contoso é uma editora com um serviço de aplicação gerido chamado Contoso Analytics (CoA). A CoA permite que os clientes analisem uma grande quantidade de dados para reporte e armazenamento de dados. A Contoso está registada como editora no Partner Center para o programa de marketplace comercial para publicar ofertas a clientes da Azure. Existem dois planos associados à CoA, descritos abaixo:

* Plano base
    * Analise 100 GB e gere 100 relatórios por $0/mês
    * Além dos 100 GB, pague $10 por cada 1 GB
    * Além dos 100 relatórios, pague $1 por cada relatório.
* Plano Premium
    * Analise 1000 GB e gere 1000 relatórios por $350/mês
    * Além dos 1000 GB, pague $100 por cada 1 TB
    * Além dos 1000 relatórios, pague $0,5 por cada relatório.

Um cliente Azure que subscreve o serviço CoA pode analisar e gerar relatórios por mês com base no plano selecionado. A Contoso mede o uso até à quantidade incluída sem enviar quaisquer eventos de utilização para a Microsoft. Quando os clientes consomem mais do que a quantidade incluída, não têm de alterar planos ou fazer nada de diferente. A Contoso medirá a sobreaque na quantidade incluída e começará a emitir eventos de utilização à Microsoft para utilização adicional utilizando o [serviço de medição marketplace API](./marketplace-metering-service-apis.md). Por sua vez, a Microsoft cobrará ao cliente a utilização adicional, conforme especificado pela editora.

## <a name="billing-dimensions"></a>Dimensões de faturação

As dimensões de faturação são usadas para comunicar ao cliente sobre como serão faturadas para usar o software.  Estas dimensões também são usadas para comunicar eventos de uso à Microsoft. São definidos da seguinte forma:

* **Identificador de dimensão**: o identificador imutável referenciado ao emitir eventos de utilização.
* **Nome de dimensão**: o nome de exibição associado à dimensão, por exemplo "mensagens de texto enviadas".
* **Unidade de medida**: descrição da unidade de faturação, por exemplo "por mensagem de texto" ou "por 100 e-mails".
* **Preço por unidade:** o preço de uma unidade da dimensão.
* **Quantidade incluída para o prazo mensal:** quantidade de dimensão incluída por mês para os clientes que pagam a mensalidade recorrente, deve ser um número inteiro.

As dimensões da faturação são partilhadas em todos os planos para uma oferta. Alguns atributos aplicam-se à dimensão em todos os planos, e outros atributos são específicos do plano.

Os atributos, que definem a dimensão em si, são partilhados em todos os planos para uma oferta. Antes de publicar a oferta, uma alteração feita a estes atributos a partir do contexto de qualquer plano irá afetar a definição de dimensão em todos os planos. Uma vez publicada a oferta, estes atributos deixarão de ser editáveis. Os atributos são:

* Identificador
* Name
* Unidade de medida

Os outros atributos de uma dimensão são específicos de cada plano e podem ter valores diferentes de plano a plano.  Antes de publicar o plano, pode editar estes valores e só este plano será afetado. Uma vez publicado o plano, estes atributos deixarão de ser editáveis. Os atributos são:

* Preço por unidade
* Quantidade incluída para clientes mensais 
* Quantidade incluída para clientes anuais 

As dimensões também têm dois conceitos especiais, "habilitados" e "infinitos":

* **Ativado** indica que este plano participa nesta dimensão.  É melhor deixar esta opção desmarcada se estiver a criar um novo plano que não envie eventos de utilização com base nesta dimensão. Além disso, quaisquer novas dimensões adicionadas após a publicação de um plano pela primeira vez aparecerão como "não habilitadas" no plano já publicado.  Uma dimensão desativada não aparecerá em nenhuma lista de dimensões para um plano visto pelos clientes.
* **Infinito,** representado pelo símbolo do infinito "∞", indica que este plano participa nesta dimensão, sem utilização medido contra esta dimensão. Se quiser indicar aos seus clientes que a funcionalidade representada por esta dimensão está incluída no plano, mas sem limite de utilização.  Uma dimensão com uso infinito aparecerá em listas de dimensões para um plano visto pelos clientes.  Este plano nunca incorrerá numa acusação.

>[!Note] 
>Os seguintes cenários são explicitamente apoiados:  <br> - Pode adicionar uma nova dimensão a um novo plano.  A nova dimensão não será ativada para quaisquer planos já publicados. <br> - Pode publicar um plano com uma taxa mensal fixa e sem dimensões, depois adicionar um novo plano e configurar uma nova dimensão para esse plano. A nova dimensão não será ativada para planos já publicados.

## <a name="constraints"></a>Restrições

### <a name="locking-behavior"></a>Comportamento de bloqueio

Uma dimensão utilizada com o serviço de medição marketplace representa uma compreensão de como um cliente vai pagar pelo serviço.  Todos os detalhes de uma dimensão já não são editáveis uma vez que uma oferta é publicada.  Antes de publicar a sua oferta, é importante que tenha as suas dimensões totalmente definidas.

Uma vez que uma oferta é publicada com uma dimensão, os detalhes de nível de oferta para essa dimensão não podem mais ser alterados:

* Identificador
* Name
* Unidade de medida

Uma vez publicado um plano, os detalhes do plano já não podem ser alterados:

* Preço por unidade
* Quantidade incluída para o termo mensal
* Se a dimensão está ativada para o plano

>[!Note]
>A faturação medido utilizando o serviço de medição do mercado ainda não é suportada na Nuvem do Governo de Azure.

### <a name="upper-limits"></a>Limites superiores

O número máximo de dimensões que podem ser configuradas para uma única oferta é de 30 dimensões únicas.

## <a name="get-support"></a>Obter suporte

Se tiver um dos seguintes problemas, pode abrir um bilhete de apoio.

* Problemas técnicos com o serviço de medição de mercado API.
* Um problema que precisa de ser agravado devido a um erro ou erro do seu lado (ex. evento de utilização errada).
* Quaisquer outras questões relacionadas com a faturação medido.

Siga a instrução de [Suporte ao programa de marketplace comercial no Partner Center](../support.md) para entender as opções de suporte dos editores e abrir o bilhete de suporte com a Microsoft.

## <a name="next-steps"></a>Passos seguintes

- Consulte [o serviço de medição de mercado APIs](./marketplace-metering-service-apis.md) para obter mais informações.

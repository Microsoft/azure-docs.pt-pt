---
title: Faturação medição para aplicações geridas utilizando o serviço de medição do mercado Mercado Azure
description: Esta documentação é um guia para isVs publicando aplicações Azure com modelos de faturação flexíveis.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 180015a6bab576c02dcfaaea589628e40c6adde4
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736178"
---
# <a name="managed-application-metered-billing"></a>Faturação medição de aplicação gerida 

Com o serviço de medição marketplace, pode criar planos de aplicação geridos para ofertas de Aplicação Azure que são cobradas de acordo com unidades não standard. Antes de publicar esta oferta, define as dimensões de faturação, tais como largura de banda, bilhetes ou e-mails processados. Os clientes pagam então de acordo com o seu consumo destas dimensões.  O seu sistema informará a Microsoft através do serviço de medição do Marketplace API de eventos faturados à medida que ocorrem.

## <a name="prerequisites-for-metered-billing"></a>Pré-requisitos para a faturação medição

Para que um plano de aplicação gerido utilize a faturação medição, deve:

* Cumprir todos os requisitos de oferta, tal como descrito na [Create a Azure, oferece uma oferta de aplicação.](create-new-azure-apps-offer.md)
* Configure **os preços** para cobrar aos clientes o custo mensal do seu serviço. O preço pode ser zero se não quiser cobrar uma taxa fixa e, em vez disso, depender inteiramente da faturação mediante.
* Detete **as dimensões** da faturação para os eventos de medição que o cliente pagará em cima da taxa fixa.
* Integre com o serviço de medição do [Marketplace APIs](./marketplace-metering-service-apis.md) para informar a Microsoft de eventos faturados.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Como a faturação medição se encaixa com os preços

Quando se trata de definir a oferta juntamente com os seus modelos de preços, é importante compreender a hierarquia da oferta.

* Cada oferta de Aplicação Azure pode ter modelo de Solução ou planos de aplicação geridos.
* A faturação medição é implementada apenas com planos de candidatura geridos.
* Cada plano de aplicação gerido tem um modelo de preços associado ao mesmo. 
* O modelo de preços tem uma taxa mensal recorrente, que pode ser fixada para $0.
* Além da taxa recorrente, o plano também pode incluir dimensões opcionais usadas para cobrar aos clientes pelo uso não incluído na taxa fixa. Cada dimensão representa uma unidade faturada que o seu serviço comunicará à Microsoft utilizando o serviço de [medição do Marketplace API](marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Oferta de amostras

Como exemplo, a Contoso é uma editora com um serviço de aplicação gerido chamado Contoso Analytics (CoA). O COA permite que os clientes analisem uma grande quantidade de dados para reporte e armazenamento de dados. A Contoso está registada como editora no Partner Center para o programa de marketplace comercial para publicar ofertas aos clientes Azure. Existem dois planos associados à CoA, delineados abaixo:

* Plano base
    * Analise 100 GB e gere 100 relatórios por $0/mês
    * Além dos 100 GB, pague $10 por cada 1 GB
    * Além dos 100 relatórios, pague $1 por cada relatório
* Plano Premium
    * Analise 1000 GB e gere 1000 relatórios por $350/mês
    * Além dos 1000 GB, pague $100 por cada 1 TB
    * Além dos 1000 relatórios, pague $0,5 por cada relatório.

Um cliente Azure que subscreva o serviço CoA pode analisar e gerar relatórios por mês com base no plano selecionado. Contoso mede o uso até à quantidade incluída sem enviar quaisquer eventos de uso para a Microsoft. Quando os clientes consomem mais do que a quantidade incluída, não têm de alterar planos ou fazer nada de diferente. A Contoso medirá o excesso para além da quantidade incluída e começará a emitir eventos de utilização para a Microsoft para utilização adicional utilizando o serviço de [medição marketplace API](./marketplace-metering-service-apis.md). A Microsoft, por sua vez, cobrará ao cliente o uso adicional especificado pela editora.

## <a name="billing-dimensions"></a>Dimensões de faturação

As dimensões de faturação são usadas para comunicar ao cliente sobre como serão faturadas para usar o software.  Estas dimensões também são usadas para comunicar eventos de uso à Microsoft. São definidos da seguinte forma:

* **Identificador de dimensão:** o identificador imutável referenciado durante a emissão de eventos de utilização.
* **Nome de dimensão**: o nome do visor associado à dimensão, por exemplo " mensagens de texto enviadas".
* **Unidade de medida**: a descrição da unidade de faturação, por exemplo "por mensagem de texto" ou "por cada 100 e-mails".
* **Preço por unidade:** o preço de uma unidade da dimensão.
* **Quantidade incluída para o período mensal**: a quantidade de dimensão incluída por mês para os clientes que pagam a taxa mensal recorrente, deve ser um inteiro.

As dimensões da faturação são partilhadas em todos os planos para uma oferta. Alguns atributos aplicam-se à dimensão em todos os planos, e outros atributos são específicos do plano.

Os atributos, que definem a própria dimensão, são partilhados em todos os planos para uma oferta. Antes de publicar a oferta, uma alteração feita a estes atributos a partir do contexto de qualquer plano afetará a definição de dimensão em todos os planos. Uma vez publicada a oferta, estes atributos deixarão de ser editáveis. Os atributos são:

* Identificador
* Name
* Unidade de medida

Os outros atributos de uma dimensão são específicos de cada plano e podem ter valores diferentes de plano a plano.  Antes de publicar o plano, pode editar estes valores e só este plano será afetado. Uma vez publicado o plano, estes atributos deixarão de ser editáveis. Os atributos são:

* Preço por unidade
* Quantidade incluída para clientes mensais 
* Quantidade incluída para clientes anuais 

As dimensões têm ainda dois conceitos especiais, "habilitados" e "infinitos":

* **Ativado** indica que este plano participa nesta dimensão.  É melhor deixar esta opção desmarcada se estiver a criar um novo plano que não envie eventos de utilização com base nesta dimensão. Além disso, quaisquer novas dimensões adicionadas após a publicação de um plano aparecerão como "não habilitadas" no plano já publicado.  Uma dimensão deficiente não aparecerá em nenhuma lista de dimensões para um plano visto pelos clientes.
* **Infinito,** representado pelo símbolo infinito "≤", indica que este plano participa nesta dimensão, sem uso medido contra esta dimensão. Se quiser indicar aos seus clientes que a funcionalidade representada por esta dimensão está incluída no plano, mas sem limite de utilização.  Uma dimensão com uso infinito aparecerá em listas de dimensões para um plano visto pelos clientes.  Este plano nunca incorrerá numa acusação.

>[!Note] 
>Os seguintes cenários são explicitamente apoiados:  <br> - Pode adicionar uma nova dimensão a um novo plano.  A nova dimensão não será ativada para quaisquer planos já publicados. <br> - Pode publicar um plano com uma mensalidade fixa e sem dimensões, depois adicionar um novo plano e configurar uma nova dimensão para esse plano. A nova dimensão não será ativada para planos já publicados.

## <a name="constraints"></a>Restrições

### <a name="locking-behavior"></a>Comportamento de bloqueio

Uma dimensão utilizada com o serviço de medição marketplace representa uma compreensão de como um cliente vai pagar pelo serviço.  Todos os detalhes de uma dimensão já não são editáveis uma vez que uma oferta é publicada.  Antes de publicar a sua oferta, é importante que tenha as suas dimensões totalmente definidas.

Uma vez que uma oferta é publicada com uma dimensão, os detalhes ao nível da oferta para essa dimensão já não podem ser alterados:

* Identificador
* Name
* Unidade de medida

Uma vez publicado um plano, os detalhes ao nível do plano já não podem ser alterados:

* Preço por unidade
* Quantidade incluída para o período mensal
* Se a dimensão está ativada para o plano

>[!Note]
>A faturação mediante o serviço de medição do mercado ainda não é suportada na Nuvem do Governo de Azure.

### <a name="upper-limits"></a>Limites superiores

O número máximo de dimensões que podem ser configuradas para uma única oferta é de 18 dimensões únicas.

## <a name="get-support"></a>Obter suporte

Se um dos seguintes casos se aplicar, pode abrir um bilhete de apoio.

* Problemas técnicos com o serviço de medição de mercado API.
* Um problema que precisa de ser escalado devido a um erro ou bug do seu lado (ex. evento de utilização errado).
* Quaisquer outras questões relacionadas com a faturação medição.

Siga os passos abaixo para submeter o seu bilhete de apoio:

1. Vá para a página de [apoio.](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff) Os primeiros menus suspensos são automaticamente preenchidos para si. Para suporte ao Marketplace, identifique a família do produto como **Cloud e Serviços Online,** o produto como **Marketplace Publisher**. Não altere as seleções de menus de dropdown pré-povoadas.
2. Em "Selecione a versão do produto", selecione gestão de oferta ao **vivo.**
3. Em "Selecione uma categoria que melhor descreva o problema", escolha oferta de **Aplicações Azure.**
4. Em "Selecione um problema que melhor descreva o problema", selecione **faturação medid**.
5. Ao selecionar o botão **Seguinte,** será direcionado para a página de dados do **Problema,** onde poderá introduzir mais detalhes sobre o seu problema.

Para mais opções de suporte da editora, consulte [o suporte para o programa de marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).

## <a name="next-steps"></a>Passos seguintes

- Consulte o serviço de [medição do Marketplace APIs](./marketplace-metering-service-apis.md) para obter mais informações.

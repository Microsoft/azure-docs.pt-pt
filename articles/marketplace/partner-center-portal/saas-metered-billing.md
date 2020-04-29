---
title: Faturação medição utilizando o serviço de medição do mercado. Mercado Azure
description: Esta documentação é um guia para a publicação de SaaS com modelos de faturação flexíveis.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80281328"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Faturação medição utilizando o serviço de medição do mercado

Com o serviço de medição marketplace, pode criar ofertas de software como um serviço (SaaS) no programa de marketplace comercial que são cobrados de acordo com unidades não standard.  Antes de publicar esta oferta, define as dimensões de faturação, tais como largura de banda, bilhetes ou e-mails processados.  Os clientes pagam então de acordo com o seu consumo destas dimensões, com o seu sistema a informar a Microsoft através do serviço de medição do Marketplace API de eventos faturados à medida que ocorrem.  

## <a name="prerequisites-for-metered-billing"></a>Pré-requisitos para a faturação medição

Para que uma oferta SaaS utilize faturação memétrica, deve:

* Cumpra todos os requisitos de oferta para uma venda através da oferta da [Microsoft,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) tal como delineado na [oferta Create a SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integre com as APIs de cumprimento do [SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para que os clientes ofereçam e se conectem à sua oferta.  
* Esteja configurado para o modelo de preços **de taxa fixa** para cobrar aos clientes pelo seu serviço.  As dimensões são uma extensão opcional ao modelo de preços fixos. 
* Integre com o serviço de medição do [Marketplace APIs](./marketplace-metering-service-apis.md) para informar a Microsoft de eventos faturados.

>[!Note]
>O serviço de medição do mercado está disponível apenas para o modelo de faturação de taxa fixa, e não se aplica ao modelo de faturação por utilizador.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Como a faturação medição se encaixa com os preços

Quando se trata de definir a oferta juntamente com os seus modelos de preços, é importante compreender a hierarquia da oferta.

* Cada oferta SaaS está configurada para vender através da Microsoft ou não.  Esta definição não pode ser alterada após a publicação de uma oferta.
* Cada oferta SaaS, configurada para vender através da Microsoft, pode ter um ou mais planos. Um utilizador subscreve a oferta SaaS, mas é adquirida através da Microsoft no âmbito de um plano.
* Cada plano tem um modelo de preços associado a ele: **taxa fixa** ou **por utilizador**. Todos os planos de uma oferta devem ser associados ao mesmo modelo de preços. Por exemplo, não pode haver uma oferta em que um dos seus planos é o modelo de preços fixos, e outro é por modelo de preços de utilizador.
* Dentro de cada plano configurado para um modelo de faturação de taxa fixa, pelo menos uma taxa recorrente (que pode ser $0) está incluída:
    * Taxa **mensal** recorrente: taxa mensal plana que é pré-paga numa recorrência mensal quando o utilizador compra o plano.
    * Taxa **anual** recorrente: taxa anual fixa que é pré-paga numa recorrência anual quando o utilizador compra o plano.
* Além das taxas recorrentes, o plano também pode incluir dimensões opcionais usadas para cobrar aos clientes pelo uso não incluído na taxa fixa.   Cada dimensão representa uma unidade faturada que o seu serviço comunicará à Microsoft utilizando o serviço de [medição do Marketplace API](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Oferta de amostras

Como exemplo, a Contoso é uma editora com um serviço SaaS chamado Contoso Notification Services (CNS). O CNS permite que os clientes enviem notificações por e-mail ou por sms. A Contoso está registada como editora no Partner Center para o programa de marketplace comercial para publicar ofertas aos clientes Azure.  Existem dois planos associados ao SNC, descritos abaixo:

* Plano base
    * Envie 10000 e-mails e 1000 textos por $0/mês
    * Além dos 10.000 e-mails, pague $1 por cada 100 e-mails
    * Além dos 1000 textos, pague $0,02 por cada texto
* Plano Premium
    * Envie 50000 e-mails e 10000 textos por $350/mês
    * Além dos 50.000 e-mails, pague $0,5 por cada 100 e-mails
    * Além dos 10.000 textos, pague $0,01 por cada texto

Um cliente Azure que subscreva o serviço CNS poderá enviar a quantidade incluída de texto e e-mails por mês com base no plano selecionado.  Contoso mede o uso até à quantidade incluída sem enviar quaisquer eventos de uso para a Microsoft.  Quando os clientes consomem mais do que a quantidade incluída, não têm de alterar planos ou fazer nada de diferente.  A Contoso medirá o excesso para além da quantidade incluída e começará a emitir eventos de utilização para a Microsoft para utilização adicional utilizando o serviço de [medição marketplace API](./marketplace-metering-service-apis.md).  A Microsoft, por sua vez, cobrará ao cliente o uso adicional especificado pela editora.

## <a name="billing-dimensions"></a>Dimensões de faturação

As dimensões de faturação são usadas para comunicar ao cliente sobre como serão faturadas para usar o software, e também para comunicar eventos de uso à Microsoft. São definidos da seguinte forma:

* **Identificador de dimensão:** o identificador imutável referenciado durante a emissão de eventos de utilização.
* **Nome de dimensão**: o nome do visor associado à dimensão, por exemplo, "mensagens de texto enviadas".
* **Unidade de medida**: a descrição da unidade de faturação, por exemplo, "por mensagem de texto" ou "por cada 100 e-mails".
* **Preço por unidade:** o preço de uma unidade da dimensão.  
* **Quantidade incluída para o período mensal**: a quantidade de dimensão incluída por mês para os clientes que pagam a taxa mensal recorrente, deve ser um inteiro.
* **A quantidade incluída para**o prazo anual : a quantidade de dimensão incluída por mês para os clientes que pagam a taxa anual recorrente deve ser um inteiro.

As dimensões da faturação são partilhadas em todos os planos para uma oferta.  Alguns atributos aplicam-se à dimensão em todos os planos, e outros atributos são específicos do plano.

Os atributos que definem a dimensão em si são partilhados em todos os planos para uma oferta.  Antes de publicar a oferta, uma alteração feita a estes atributos a partir do contexto de qualquer plano afetará a definição de dimensão em todos os planos.  Uma vez publicada a oferta, estes atributos deixarão de ser editáveis.  Estes atributos são:

* Identificador
* Nome
* Unidade de medida

Os outros atributos de uma dimensão são específicos de cada plano e podem ter valores diferentes de plano a plano.  Antes de publicar o plano pode editar estes valores e só este plano será afetado.  Uma vez publicado o plano, estes atributos deixarão de ser editáveis.  Estes atributos são:

* Preço por unidade
* Quantidade incluída para clientes mensais 
* Quantidade incluída para clientes anuais 

As dimensões têm ainda dois conceitos especiais, "habilitados" e "infinitos":

* **Ativado** indica que este plano participa nesta dimensão.  É melhor deixar isto descontrolado se estiver a criar um novo plano que não envie eventos de uso com base nesta dimensão.  Além disso, quaisquer novas dimensões adicionadas após a publicação de um plano aparecerão como "não habilitadas" no plano já publicado.  Uma dimensão deficiente não aparecerá em nenhuma lista de dimensões para um plano visto pelos clientes.
* **Infinito,** representado pelo símbolo infinito "≤", indica que este plano participa nesta dimensão, mas não mede o uso desta dimensão.  Se quiser indicar aos seus clientes que a funcionalidade representada por esta dimensão está incluída no plano, mas sem limite de utilização.  Uma dimensão com uso infinito aparecerá em listas de dimensões para um plano visto pelos clientes, com a indicação de que nunca incorrerá numa cobrança por este plano.

>[!Note] 
>Os seguintes cenários são explicitamente apoiados: <br> - Pode adicionar uma nova dimensão a um novo plano.  A nova dimensão não será ativada para quaisquer planos já publicados. <br> - Pode publicar um plano **forronésio** sem dimensões, depois adicionar um novo plano e configurar uma nova dimensão para esse plano. A nova dimensão não será ativada para planos já publicados.

## <a name="constraints"></a>Restrições

### <a name="trial-behavior"></a>Comportamento experimental

A faturação medição utilizando o serviço de medição do mercado não é compatível com a oferta de um teste gratuito.  Não é possível configurar um plano para utilizar tanto a faturação medição como um teste gratuito.

### <a name="locking-behavior"></a>Comportamento de bloqueio

Uma vez que uma dimensão utilizada com o serviço de medição do Marketplace representa uma compreensão de como um cliente vai pagar pelo serviço, todos os detalhes para uma dimensão já não são editáveis uma vez que o publica.  É importante que tenhas as tuas dimensões totalmente definidas para um plano antes de publicares.
  
Uma vez que uma oferta é publicada com uma dimensão, os detalhes ao nível da oferta para essa dimensão já não podem ser alterados:

* Identificador
* Nome
* Unidade de medida

Uma vez publicado um plano, os detalhes ao nível do plano já não podem ser alterados:

* Preço por unidade
* Quantidade incluída para o período mensal
* Quantidade incluída para o termo anual
* Se a dimensão está ativada para o plano

### <a name="upper-limits"></a>Limites superiores

O número máximo de dimensões que podem ser configuradas para uma única oferta é de 18 dimensões únicas.

## <a name="get-support"></a>Obter suporte

Se tiver um dos seguintes, pode abrir um bilhete de apoio.

* Problemas técnicos com o serviço de medição de mercado API.
* Um problema que precisa de ser escalado devido a um erro ou bug do seu lado (ex. evento de utilização errado).
* Quaisquer outras questões relacionadas com a faturação medição. 

Siga os passos abaixo para submeter o seu bilhete de apoio:

1. Vá para a página de [apoio.](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff) Os primeiros menus suspensos são automaticamente preenchidos para si. Para suporte ao Marketplace, identifique a família do produto como **Cloud e Serviços Online,** o produto como **Marketplace Publisher**.  Não altere as seleções de menus de dropdown pré-povoadas.
2. Em "Selecione a versão do produto", selecione gestão de oferta ao **vivo.**
3. Em "Selecione uma categoria que melhor descreva o problema", escolha **aplicações SaaS**.
4. Em "Selecione um problema que melhor descreva o problema", selecione **faturação medid**.
5. Ao selecionar o botão **Seguinte,** será direcionado para a página de dados do **Problema,** onde poderá introduzir mais detalhes sobre o seu problema.

Consulte [o Suporte para o programa de marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) para mais opções de suporte da editora.

## <a name="next-steps"></a>Passos seguintes

- Consulte o serviço de [medição do Marketplace APIs](./marketplace-metering-service-apis.md) para obter mais informações.

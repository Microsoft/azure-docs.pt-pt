---
title: Compreender a sua fatura do Azure | Microsoft Docs
description: Saiba como ler e compreender a utilização e a faturação da sua subscrição do Azure
services: ''
documentationcenter: ''
author: bandersmsft
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 77c1a85136b2117af7396b8eec2d8b92b335d61d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "60369965"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Compreender os termos na sua fatura do Microsoft Azure

A fatura disponibiliza um resumo dos seus custos e instruções para o pagamento. Está disponível para transferência em .pdf (Portable Document Format) a partir do [portal do Azure](https://portal.azure.com/) ou pode ser enviada por e-mail. Para obter mais informações, veja [Como obter a fatura e os dados de utilização diária do Azure](billing-download-azure-invoice-daily-usage-date.md).

Algumas coisas a notar:

-   Se estiver a utilizar uma subscrição de avaliação gratuita, pode obter as informações de utilização detalhada no portal do Azure, mas não terá uma fatura.

-   A fatura atual poderá mostrar até 24 horas de utilização relativas ao fim do período de faturação anterior.

-   Os custos apresentados nos extratos de faturação dos clientes internacionais destinam-se apenas a estimativas. Os bancos poderão ter custos diferentes para as taxas de câmbio.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Termos e descrições detalhados da sua fatura
As secções seguintes apresentam uma lista dos termos importantes que vê na fatura e as descrições de cada termo.

### <a name="account-information"></a>Informações da conta

A secção de informações da conta da fatura encontra-se na parte superior da primeira página e mostra informações sobre o seu perfil e subscrição.

![Secção de informações da conta da fatura](./media/billing-understand-your-invoice/1.png)

| Termo | Descrição |
| --- | --- |
| N.º da OC do cliente |Um número de ordem de compra opcional, atribuído por si para controlo |
| N.º da Fatura |Um número de fatura exclusivo gerado pela Microsoft utilizado para fins de controlo |
| Ciclo de faturação |Intervalo de datas abrangido pela fatura |
| Data da fatura |Data em que a fatura foi gerada, normalmente um dia após o fim do ciclo de faturação |
| Método de pagamento |O tipo de pagamento utilizado na conta (fatura ou cartão de crédito) |
| Faturar a |Endereço de faturação apresentado para a conta |
| Oferta de subscrição ("Pay As You Go") |O tipo de oferta de subscrição que foi comprada (Pay As You Go, BizSpark Plus, Azure Pass, etc.). Para obter mais informações, veja [Tipos de oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/). |
| E-mail do proprietário da conta | O endereço de e-mail da conta sob o qual a conta do Microsoft Azure está registada. <br /><br />Para alterar o endereço de e-mail, veja [Como alterar as informações de perfil da sua conta do Azure, como o e-mail de contacto, endereço e número de telefone](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Compreender o resumo da fatura
A secção **Resumo da Fatura** da fatura apresenta uma lista dos montantes totais das transações desde o seu último período de faturação e os custos de utilização atuais.

![Secção Resumo da fatura](./media/billing-understand-your-invoice/2.png)

O nome da Subscrição ("Armazenamento de Produção") é o nome da subscrição para esta fatura.

#### <a name="understand-the-previous-charges"></a>Compreender os custos anteriores
A secção de saldo anterior, pagamentos e saldo pendente da fatura resume as transações ocorridas desde o seu último período de faturação.

| Termo | Descrição |
| --- | --- |
| Saldo anterior |O montante total por liquidar do último período de faturação |
| Pagamentos |Os pagamentos e créditos totais aplicados ao último período de faturação |
| Saldo pendente (do ciclo de faturação anterior) |Quaisquer créditos ou saldo restante na sua conta desde o último período de faturação |

#### <a name="understand-the-current-charges"></a>Compreender os encargos atuais
A secção Custos Atuais da fatura mostra detalhes sobre os custos mensais para o período de faturação atual.

| Termo | Descrição |
| --- | --- |
| Custos de utilização |Os custos de utilização são os custos mensais totais de uma subscrição para o período de faturação atual.|
| Descontos |Descontos de serviço aplicados ao seu período de faturação atual|
| Ajustes |Os créditos diversos (Utilização Gratuita, Créditos, etc.) ou custos pendentes aplicados ao período de faturação atual.<br/><br/>Por exemplo, se tiver a oferta Visual Studio Enterprise com MSDN, verá um crédito mensal. Se cancelar a sua subscrição, verá os custos de utilização mensal que excedem o crédito mensal que obtém com a sua oferta de subscrição. Os custos são aplicados no início do período de faturação atual até à data de cancelamento da subscrição. |

#### <a name="sold-to-and-payment-instructions"></a>Destino da venda e instruções de pagamento

A tabela a seguir descreve o destino da venda e as instruções de pagamento mostradas na segunda página da fatura.

| Termo |Descrição |
| --- | --- |
| Vendido a |O endereço do perfil que está na conta. <br/><br/>Se precisar de alterar o endereço, veja [Como alterar as informações de perfil da sua conta do Azure, como o e-mail de contacto, endereço e número de telefone](billing-how-to-change-azure-account-profile.md).|
| Instruções de pagamento |Instruções sobre como pagar consoante o método de pagamento (por exemplo, por cartão de crédito ou por fatura). |

#### <a name="usage-charges"></a>Custos de Utilização

A secção Custos de utilização da fatura apresenta informações de nível de medidor sobre os seus custos.

![Secção Custos de utilização](./media/billing-understand-your-invoice/3.png)

A tabela a seguir descreve os cabeçalhos de coluna dos custos de utilização apresentados na Fatura.

| Termo |Descrição |
| --- | --- |
| Nome |Identifica o serviço de nível superior para a utilização |
| Tipo |Define o tipo de serviço do Azure que pode afetar a tarifa |
| Recurso |Identifica a unidade de medida do medidor a ser consumido |
| Região |Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter |
| Consumido |O valor do medidor utilizado durante o período de faturação |
| Incluída |A quantidade do medidor que está incluída gratuitamente no seu período de faturação atual |
| A Cobrar |Mostra a diferença entre a Quantidade Consumida e a Quantidade Incluída. Este montante ser-lhe-á cobrado. Para as ofertas "Pay As You Go" sem quantidade incluída com a oferta, este total equivale à Quantidade Consumida |
| Tarifa |A tarifa que lhe é cobrada por unidade |
| Valor |Apresenta o resultado da multiplicação entre a coluna Quantidade Excedida e a coluna Tarifa. Se a Quantidade Consumida não exceder a Quantidade Incluída, não haverá custos nesta coluna. |
| Subtotal |A soma de todos os custos antes dos impostos para este período de faturação |
| Total Geral |A soma de todos os custos depois dos impostos para este período de faturação |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Como certificar-se de que os custos na fatura estão corretos?
Se existir um custo na fatura sobre o qual gostaria de obter mais informações, veja como [Compreender a sua fatura do Microsoft Azure.](billing-understand-your-bill.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

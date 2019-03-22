---
title: Compreender a fatura do Azure | Documentos da Microsoft
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897457"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Compreender os termos na sua fatura do Microsoft Azure

A fatura disponibiliza um resumo dos seus custos e instruções para o pagamento. Está disponível para download no formato de documento (. pdf) portáteis do [portal do Azure](https://portal.azure.com/) ou podem ser enviados por e-mail. Para obter mais informações, consulte [como obter a faturação da nota fiscal e diário de dados de utilização do Azure](billing-download-azure-invoice-daily-usage-date.md).

Alguns aspetos a ter em conta:

-   Se estiver a utilizar uma subscrição de avaliação gratuita, pode obter as informações de utilização detalhada do portal do Azure, mas não têm uma nota fiscal.

-   Até 24 horas de utilização no final do período de faturação anterior pode aparecer na sua fatura atual.

-   Os custos apresentados nos extratos de faturação dos clientes internacionais destinam-se apenas a estimativas. Bancos podem ter custos diferentes para as taxas de câmbio.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Termos detalhados e as descrições da sua fatura
As secções seguintes listam os termos importantes que vê na sua fatura e as descrições para cada termo.

### <a name="account-information"></a>Informações da conta

A secção de informações de conta da nota fiscal está no topo da página de primeiro e mostra informações sobre o perfil e a subscrição.

![Secção de informações de conta de nota fiscal](./media/billing-understand-your-invoice/1.png)

| Termo | Descrição |
| --- | --- |
| N º da oC do cliente |Um número de ordem de compra opcional, atribuído por si para o controlo de |
| N.º da Fatura |Um número de nota fiscal de Microsoft gerado exclusivo, utilizado para efeitos de controlo |
| Ciclo de faturação |Intervalo de datas que abordam a esta nota fiscal |
| Data da fatura |Data em que a fatura foi gerada, normalmente, um dia após o final do ciclo de faturação |
| Método de pagamento |O tipo de pagamento utilizado na conta (fatura ou cartão de crédito) |
| Faturar a |Endereço listado para a conta de cobrança |
| Subscrição da oferta ("pay as you go") |Tipo de oferta de subscrição que tenha sido comprado (pay as you go, BizSpark Plus, Azure Pass, etc.). Para obter mais informações, consulte [tipos de oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/). |
| E-mail do proprietário da conta | O endereço de e-mail da conta no qual a conta do Microsoft Azure está registada. <br /><br />Para alterar o endereço de e-mail, consulte [como alterar as informações de perfil da sua conta do Azure como o e-mail de contacto, endereço e número de telefone](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Compreender o resumo da fatura
O **resumo da fatura** secção da nota fiscal lista os valores da transação total desde o último período de faturação e as cobranças de utilização atuais.

![Secção de resumo de nota fiscal](./media/billing-understand-your-invoice/2.png)

O nome da subscrição ("armazenamento de produção") é o nome da subscrição para esta nota fiscal.

#### <a name="understand-the-previous-charges"></a>Compreender os encargos anteriores
O saldo anterior, pagamentos e saldo pendente secção da nota fiscal resume as transações desde o último período de faturação.

| Termo | Descrição |
| --- | --- |
| Saldo anterior |O montante total devido último período de faturação |
| Pagamentos |Total de pagamentos e créditos aplicados a seu último período de faturação |
| Saldo pendente (do ciclo de faturação anterior) |Quaisquer créditos ou o restante saldo na sua conta desde o último período de faturação |

#### <a name="understand-the-current-charges"></a>Compreender os encargos atuais
A secção encargos atuais da nota fiscal mostra detalhes sobre as suas Cobranças mensais para o período de faturação atual.

| Termo | Descrição |
| --- | --- |
| Custos de utilização |Custos de utilização são as total das cobranças mensais de uma subscrição para o período de faturação atual|
| Descontos |Descontos de serviço aplicados para o período de faturação atual|
| Ajustes |(Utilização gratuita, os créditos, etc.) os créditos diversos ou cobranças por liquidar aplicadas ao período de faturação atual.<br/><br/>Por exemplo, se tiver o Visual Studio Enterprise com a oferta MSDN, verá um crédito mensal. Se cancelar a sua subscrição, verá quaisquer custos de utilização mensal que excederem o crédito mensal que obtém com a oferta de subscrição. Os custos de incorrem no início do período de faturação atual até à data de cancelamento da subscrição. |

#### <a name="sold-to-and-payment-instructions"></a>Vendido a e instruções de pagamento

A tabela seguinte descreve o vendidos para e instruções de pagamento apresentadas na segunda página da sua fatura.

| Termo |Descrição |
| --- | --- |
| Vendido a |Endereço do perfil que está na conta. <br/><br/>Se precisar de alterar o endereço, veja [como alterar as informações de perfil da sua conta do Azure como o e-mail de contacto, endereço e número de telefone](billing-how-to-change-azure-account-profile.md).|
| Instruções de pagamento |Instruções sobre como pagar consoante o método de pagamento (como, por exemplo, como de crédito cartão ou por fatura). |

#### <a name="usage-charges"></a>Custos de Utilização

A secção de encargos de utilização da nota fiscal apresenta informações de nível de medidor em custos.

![Secção de encargos de utilização](./media/billing-understand-your-invoice/3.png)

A tabela seguinte descreve os cabeçalhos de coluna utilização de custos na sua fatura.

| Termo |Descrição |
| --- | --- |
| Name |Identifica o serviço de nível superior para a utilização |
| Type |Define o tipo de serviço do Azure que pode afetar a tarifa |
| Recurso |Identifica a unidade de medida para o medidor de consumo |
| Região |Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter |
| Consumido |A quantidade de medidor utilizado durante o período de faturação |
| Incluída |A quantidade do medidor de que está incluída sem custos no período de faturação atual |
| A Cobrar |Mostra a diferença entre a quantidade consumida e a quantidade incluída. É-lhe cobrada durante este período. Para as ofertas sem quantidade incluída com a oferta pay as you go, é este total equivale à quantidade consumida |
| Tarifa |A tarifa que lhe é cobrada por unidade faturável |
| Value |Mostra o resultado da multiplicação entre a coluna de quantidade de utilização excedida, a coluna tarifa. Se a quantidade consumida não exceder a quantidade incluída, não é sem encargos, nesta coluna. |
| Total de secundárias |A soma de todos os seus custos pré-imposto para este período de faturação |
| Total geral |A soma de todos os custos depois de imposto para este período de faturação |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Como posso Certifique-se de que os encargos na minha fatura estão corretos?
Se existe uma cobrança na sua fatura que deseja obter mais detalhes, consulte o artigo [compreender a sua fatura do Microsoft Azure.](billing-understand-your-bill.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

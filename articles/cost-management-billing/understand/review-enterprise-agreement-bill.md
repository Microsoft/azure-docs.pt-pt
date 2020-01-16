---
title: Examine sua fatura de Enterprise Agreement do Azure
description: Saiba como ler e compreender a utilização e a faturação dos Contratos Enterprise do Azure.
author: banders
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2020
ms.author: banders
ms.openlocfilehash: 2beb1d3e4468ff96f629dd6ec7015376a7f3a904
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987265"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Compreenda a faturação do Contrato Enterprise do Azure

Os clientes do Azure com um Contrato Enterprise recebem uma fatura quando excedem o crédito da organização ou quando utilizam serviços que não são cobertos pelo crédito.

O crédito da sua organização inclui a alocação monetária. A alocação monetária é o montante que a sua organização pagou antecipadamente para a utilização dos serviços do Azure. Pode adicionar fundos de alocação monetária ao Contrato Enterprise ao contactar o seu revendedor ou o gestor de conta Microsoft.

Este tutorial se aplica somente aos clientes do Azure com um Enterprise Agreement do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Revisar encargos faturados
> * Examinar encargos excedentes do serviço
> * Examinar a fatura do Marketplace

## <a name="prerequisites"></a>Pré-requisitos

Para analisar e verificar os custos presentes na fatura, tem de ser um Administrador do Enterprise. Para obter mais informações, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](../manage/understand-ea-roles.md). Se não souber quem é o Administrador do Enterprise da sua organização, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Revisar encargos faturados para a maioria dos clientes

Esta secção não se aplica a clientes do Azure na Austrália, Japão ou Singapura.

Receberá uma fatura do Azure quando ocorrer qualquer uma das seguintes situações durante o período de faturação:

- Excedente de **serviço**: os encargos de uso da sua organização ultrapassam o saldo de crédito.
- **Encargos cobrados separadamente**: os serviços que sua organização usou não são cobertos pelo crédito. É faturado pelos seguintes serviços independentemente do seu saldo de crédito:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Utilizador Registado
    - OpenLogic
    - Utilizador Registado do Remote Access Rights XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (Mensal)
    - Visual Studio Enterprise (Anual)
    - Visual Studio Professional (Mensal)
    - Visual Studio Professional (Anual)
- **Cobranças do Marketplace**: as compras e o uso do Azure Marketplace não são cobertos pelo crédito da sua organização. Portanto, será faturado pelos custos do Marketplace independentemente do seu saldo de crédito. No Enterprise Portal, um Administrador do Enterprise pode ativar e desativar compras do Marketplace.

A fatura mostra toda a utilização do Azure, seguida por quaisquer custos do Marketplace. Se tiver um saldo de crédito, também se aplicará à utilização do Azure.

Compare o montante total combinado mostrado no Enterprise Portal em **Relatórios** > **Resumo da Utilização** com a sua fatura do Azure. Os montantes no **Resumo da Utilização** não incluem impostos.

Entre no portal de [ea do Azure](https://ea.azure.com). Em seguida, selecione **relatórios**. No canto superior direito do separador, mude a vista de **M** para **C** e escolha o período da fatura.  

![Captura de tela que mostra a opção M + C no Resumo de uso.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

O valor combinado da **Utilização Total** e do **Azure Marketplace** deve corresponder ao **Montante Total Alargado** na fatura. Para obter mais detalhes sobre os custos, aceda a **Transferir Utilização**.  

![Captura de tela mostrando a guia uso de download](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Revisar encargos faturados para outros clientes

Esta seção aplica-se apenas a clientes do Azure na Austrália, Japão ou Singapura.

Receberá uma ou mais faturas do Azure quando ocorrer qualquer uma das seguintes situações:

- Excedente de **serviço**: os encargos de uso da sua organização ultrapassam o saldo de crédito.
- **Encargos cobrados separadamente**: os serviços que sua organização usou não são cobertos pelo crédito. Será faturado pelos seguintes serviços:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Utilizador Registado
    - OpenLogic
    - Utilizador Registado do Remote Access Rights XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (Mensal)
    - Visual Studio Enterprise (Anual)
    - Visual Studio Professional (Mensal)
    - Visual Studio Professional (Anual)
- **Cobranças do Marketplace**: as compras e o uso do Azure Marketplace não são cobertos pelo crédito da sua organização e são cobrados separadamente. No Enterprise Portal, um Administrador do Enterprise pode ativar e desativar compras do Marketplace.

Quando tem custos referentes a utilização excedida de serviço e custos que são faturados separadamente durante o período de faturação, receberá uma fatura. A fatura inclui os dois tipos de custos. Os custos dos Marketplaces são sempre faturados separadamente.

## <a name="review-service-overage-charges-for-other-customers"></a>Examinar encargos excedentes de serviço para outros clientes

Esta secção aplica-se apenas se estiver na Austrália, no Japão ou em Singapura.

Compare o montante de utilização total no Enterprise Portal em **Relatórios** > **Resumo da Utilização** com a sua fatura de utilização excedida do serviço. A fatura de utilização excedida do serviço inclui a utilização que excede o crédito da sua organização e/ou os serviços que não são cobertos pelo crédito. Os montantes no **Resumo da Utilização** não incluem impostos.

Entre no portal de [ea do Azure](https://ea.azure.com) e selecione **relatórios**. No canto superior direito do separador, mude a vista de **M** para **C** e escolha o período da fatura.  

![Captura de tela que mostra a opção M + C no Resumo de uso.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

O montante de **Utilização Total** deve corresponder ao **Montante Total Alargado** na fatura de utilização excedida do serviço. Para obter mais informações sobre os custos, aceda a **Transferir Utilização** > **Transferir Relatório Avançado**. Este relatório não inclui impostos nem custos relativos a custos de reservas ou do marketplace.  

![Captura de tela que mostra o download de relatório avançado na guia uso de download.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

A tabela a seguir lista os termos e as descrições apresentados na fatura e no **Resumo de Utilização** no Enterprise Portal:

|Termo da fatura|Termo do Resumo da Utilização|Descrição|
|---|---|---|
|Montante Alargado Total|Utilização Total|O custo de utilização total antes dos impostos relativo ao período especificado antes do crédito ser aplicado.|
|Utilização de Alocação|Utilização de Alocação|O crédito aplicado durante esse período específico.|
|Venda Total|Utilização Excedida Total|O custo total de utilização que excede o montante do seu crédito. Este montante não inclui impostos.|
|Montante dos Impostos|Não aplicável|Imposto que se aplica ao montante total de venda para o período especificado.|
|Montante Total|Não aplicável|O montante devido da fatura depois do crédito ser aplicado e do imposto ser adicionado.|

### <a name="review-marketplace-invoice"></a>Examinar a fatura do Marketplace

Esta secção aplica-se apenas se estiver na Austrália, no Japão ou em Singapura.

Compare o total do Azure Marketplace em **Relatórios** > **Resumo de Utilização** no Enterprise Portal com a sua fatura do marketplace. A fatura do Marketplace diz respeito apenas a compras e utilização do Azure Marketplace. Os montantes no **Resumo da Utilização** não incluem impostos.

Entre no [Enterprise Portal](https://ea.azure.com) e, em seguida, selecione **relatórios**. No canto superior direito do separador, mude a vista de **M** para **C** e escolha o período da fatura.  

![Captura de tela que mostra a opção M + C no Resumo de uso.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

O total do **Azure Marketplace** deve corresponder à **Venda Total** na fatura do marketplace. Para obter mais informações sobre os custos com base na utilização, aceda a **Transferir Utilização**. Em **Custos do Marketplace**, selecione **Transferir**. Este relatório não inclui impostos e não mostra compras únicas.  

![Captura de tela que mostra a opção de download em encargos do Marketplace.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Revisar encargos faturados
> * Examinar encargos excedentes do serviço
> * Examinar a fatura do Marketplace

Continue no próximo artigo para saber mais usando o portal de EA do Azure.

> [!div class="nextstepaction"]
> [Introdução ao Azure EA Portal](../manage/ea-portal-get-started.md)

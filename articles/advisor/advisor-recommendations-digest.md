---
title: Recomendação digestão para Azure Advisor
description: Receba um resumo periódico para as suas recomendações ativas
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79502467"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Configure resumo periódico para recomendações

As **digestão de recomendação** do conselho proporcionam uma forma fácil e proativa de se manter em cima das suas recomendações ativas, em diferentes categorias. A funcionalidade fornece a capacidade de configurar notificações periódicas para o resumo de todas as suas recomendações ativas, em diferentes categorias. Pode escolher o seu canal pretendido para notificações como e-mail, sms ou outros, utilizando grupos de ação. Este artigo mostra-lhe como configurar uma **recomendação que digere** para as suas recomendações do Advisor.


## <a name="setting-up-your-recommendation-digest"></a>Configurar a sua recomendação digerir 

A experiência de criação **de recomendação digerida** ajuda-o a configurar o resumo. Pode selecionar parâmetros abaixo para configurações:
1. Categoria: Temos categorias de recomendações como custo, alta disponibilidade, desempenho e excelência operacional. A capacidade ainda não está disponível para recomendações de segurança.
2. Frequência de digestão: A frequência para as notificações resumida pode ser semanal, bi-semanal e mensal.
3. Grupo de ação: Pode selecionar um grupo de ação existente ou criar um novo grupo de ação. Para saber mais sobre grupos de ação, consulte [criar e gerir grupos de ação.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
4. Linguagem para a digestão
5. Recomendação digestão nome: Pode utilizar uma corda fácil de utilizar para acompanhar melhor e monitorizar as digestão.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Passos para criar recomendação digerir no portal Azure

Aqui estão os passos para criar **a digestão da recomendação:**
* **Passo 1:** No portal Azure, vá ao **Advisor** e na secção **de Monitorização,** selecione **Recomendação digerir** 

   ![Recomendação digerir ponto de entrada](./media/digest-0.png)

* **Passo 2:** Selecione **Nova recomendação digerir** a partir da barra superior como abaixo:

   ![Criar digestão de recomendação](./media/digest-5.png)

* **Passo 3:** Na secção **de âmbito,** selecione a **subscrição** para a sua digestão

   ![Fornecer entradas de digestão de recomendação](./media/digest-1.png)

* **Passo 4:** Na secção **de condições,** selecione as configurações como **categoria,** **frequência** e **linguagem**

   ![Fornecer condições de entrada de digestão de recomendação](./media/digest-2.png)

* **Passo 5:** Na secção **de grupo de ação,** selecione o **grupo de ação** para a digestão. Pode aprender mais aqui - [Criar e gerir grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![Fornecer grupo de ação de entrada de digestão de recomendação](./media/digest-3.png)

* **Passo 6:** Nesta secção final para **obter detalhes,** pode atribuir nome e estado à sua recomendação digerir. A **imprensa cria recomendação digestão** para completar a configuração.
   ![Criação completa de recomendação digestão](./media/digest-4.png)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Conselheiro Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Custos Do Consultor](advisor-cost-recommendations.md)
* [Recomendações de Desempenho do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de Segurança do Conselheiro](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional Do Consultor](advisor-operational-excellence-recommendations.md)
* [Conselheiro REST API](https://docs.microsoft.com/rest/api/advisor/)

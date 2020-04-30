---
title: Recomendação digestiva para Consultor Azure
description: Obtenha resumo periódico para as suas recomendações ativas
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502467"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Configurar resumo periódico para recomendações

As **digestões de recomendações** de aconselhamento proporcionam uma forma fácil e proativa de se manter em cima das suas recomendações ativas, em diferentes categorias. A funcionalidade fornece a capacidade de configurar notificações periódicas para o resumo de todas as suas recomendações ativas, em diferentes categorias. Pode escolher o seu canal pretendido para notificações como e-mail, sms ou outros, utilizando grupos de ação. Este artigo mostra-lhe como configurar uma **recomendação** para as suas recomendações do Advisor.


## <a name="setting-up-your-recommendation-digest"></a>Configuração da sua recomendação digestiva 

A experiência de criação **de recomendação digesta** ajuda-o a configurar o resumo. Pode selecionar os parâmetros abaixo para configurações:
1. Categoria: Temos categorias de recomendações como custo, elevada disponibilidade, desempenho e excelência operacional. A capacidade ainda não está disponível para recomendações de segurança.
2. Frequência da digestão: A frequência das notificações sumárias pode ser semanal, bisemanal e mensal.
3. Grupo de ação: Pode selecionar um grupo de ação existente ou criar um novo grupo de ação. Para saber mais sobre grupos de ação, consulte [criar e gerir grupos](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)de ação.
4. Linguagem para a digestão
5. Recomendação digestine nome: Você pode usar uma corda fácil de usar para melhor rastrear e monitorizar as digestões.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Passos para criar recomendação digestão no portal Azure

Aqui estão os passos para criar **recomendação digestão:**
* **Passo 1:** No portal Azure, vá ao **Advisor** e sob a secção **de Monitorização,** selecione **Recomendação digest** 

   ![Ponto de entrada de digestão recomendação](./media/digest-0.png)

* **Passo 2:** Selecione **Nova recomendação digesta** a partir da barra superior como abaixo:

   ![Criar recomendação digestão](./media/digest-5.png)

* **Passo 3:** Na secção **de âmbito,** selecione a **subscrição** para a sua digestão

   ![Fornecer inputs de digestão de recomendação](./media/digest-1.png)

* **Passo 4:** Na secção **condição,** selecione as configurações como **categoria,** **frequência** e **idioma**

   ![Fornecer condições de entrada digestivas de recomendação](./media/digest-2.png)

* **Passo 5:** Na secção do grupo de **ação,** selecione o **grupo de ação** para a digestão. Você pode aprender mais aqui - [Criar e gerir grupos](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) de ação

   ![Fornecer grupo de ação de input digest recomendação digest](./media/digest-3.png)

* **Passo 6:** Nesta secção final para **detalhes de digestão,** pode atribuir nome e estado à sua recomendação digestiva. Pressione **criar recomendação digestiva** para completar a configuração.
   ![Recomendação completa digestão criação](./media/digest-4.png)

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Consultor Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Custos Consultivos](advisor-cost-recommendations.md)
* [Recomendações de Desempenho Do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional Consultiva](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)

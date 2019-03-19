---
title: 'O Azure Analysis Services lição 13 do tutorial: Implementar | Documentos da Microsoft'
description: Descreve como implementar o projeto de tutorial do Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 72fa9bf211139b971afa443af812d644c76062fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123475"
---
# <a name="deploy"></a>Implementação

Nesta lição, vai configurar as propriedades da implementação, especificando um servidor do Azure Analysis Services onde implementar e um nome para o modelo. Em seguida, irá implementar o modelo nessa instância. Depois de o modelo ser implementado, os utilizadores podem ligar-se ao mesmo através de uma aplicação de relatório para clientes. Para obter mais informações, consulte [Deploy to Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy) (Implementar no Azure Analysis Services).  
  
Tempo estimado para concluir esta lição: **5 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este artigo faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 12: Analisar no Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

> [!IMPORTANT]  
> Tem de ter [Permissões de administrador](../analysis-services-server-admins.md) na instância remota do servidor do Analysis Services para a implementação.  

> [!IMPORTANT]  
> Se tiver instalado a base de dados de exemplo AdventureWorksDW2014 num SQL Server no local e se estiver a implementar o seu modelo num servidor do Azure Analysis Services, é necessário um [gateway de dados no local](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>Implementar o modelo  
  
#### <a name="to-configure-deployment-properties"></a>Para configurar propriedades de implementação  

  
1.  No **Explorador de Soluções**, clique com o botão direito do rato no projeto **AW Internet Sales** e clique em **Propriedades**.  
  
2.  Na caixa de diálogo **Páginas de propriedades de vendas na Internet da AW**, em **Servidor de Implementação**, na propriedade **Servidor**, introduza o servidor completo.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  Nas propriedades da **base de dados**, digite **Vendas na Internet Adventure Works**.  
  
4.  Nas propriedades do **nome do modelo**, digite **Modelo de vendas na Internet Adventure Works**.  
  
5.  Verifique suas seleções e, em seguida, clique em **OK**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Para implementar as Vendas na Internet Adventure Works
  
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto **Vendas na Internet AW** > **Criar**.  

2. Clique com botão direito do rato no projeto **Vendas na Internet AW** > **Implementar**.

   Ao implementar no Azure Analysis Services, pode ser-lhe solicitado que entre na sua conta. Entre na conta da sua organização e introduza a palavra-passe, por exemplo nancy@adventureworks.com. Esta conta tem de ser Administrador no servidor.
  
   É apresentada a caixa de diálogo Implementar que exibe o estado da implementação dos metadados e cada tabela incluída no modelo.  
    
   ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Quando a implementação for concluída com êxito, avance e clique em **Fechar**.  
  

Esta aula descreve o método mais comum e mais fácil de implementar um modelo de tabela do SSDT. Opções de implementação avançada, como o Assistente de Implementação ou automatização com XMLA e AMO oferecem maior flexibilidade, consistência e implementações agendadas. Para obter mais informações, veja [Implementação da solução de modelo de tabela](https://docs.microsoft.com/sql/analysis-services/tabular-models/tabular-model-solution-deployment-ssas-tabular).

## <a name="conclusion"></a>Conclusão  
Parabéns! Acabou de criar e implementar o seu primeiro modelo do Analysis Services Tabular. Este tutorial ajudou na conclusão das tarefas mais comuns na criação de um modelo de tabela. Agora que o modelo das Vendas na Internet Adventure Works é implementado, pode usar o SQL Server Management Studio para gerir o modelo; crie scripts de processo e um plano de cópia de segurança. Agora, os utilizadores também podem ligar-se ao modelo através de uma aplicação de relatório para clientes, como o Microsoft Excel ou Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Passos seguintes?
[Ligar com o Power BI Desktop](../analysis-services-connect-pbi.md)   
[Lição Suplementar - segurança dinâmica](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Lição Suplementar - linhas Detalhadas](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Lição suplementar - Hierarquias desbalanceadas](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   

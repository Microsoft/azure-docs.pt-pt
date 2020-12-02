---
title: Implementar um modelo para a Azure Analysis Services utilizando o Visual Studio Microsoft Docs
description: Aprenda a implementar um modelo tabular num servidor Azure Analysis Services utilizando o Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9df10760164dcd0d207663c14107f72c46b76d25
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501248"
---
# <a name="deploy-a-model-from-visual-studio"></a>Implementar um modelo do Visual Studio

Depois de criar um servidor na sua subscrição do Azure, está pronto para implementar uma base de dados de modelo de tabela no mesmo. Pode utilizar projetos de Visual Studio com Serviços de Análise para construir e implementar um projeto de modelo tabular em que está a trabalhar. 

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* **Servidor Analysis Services** no Azure. Para obter mais informações, consulte [Criar um servidor Azure Analysis](analysis-services-create-server.md).
* **Projeto de modelo tabular** no Visual Studio ou um modelo tabular existente ao nível de compatibilidade de 1200 ou mais elevado. Nunca criou um? Experimente o [tutorial do modelo de tabela de vendas na Internet Adventure Works](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Gateway local** - se uma ou mais origens de dados se encontrarem no local da rede da sua organização, é necessário instalar um [gateway de dados no local](analysis-services-gateway.md). O gateway é necessário para que o servidor na nuvem se ligue às suas origens de dados no local para processar e atualizar os dados no modelo.

> [!TIP]
> Antes de implementar, certifique-se de que pode processar os dados nas tabelas. No Estúdio Visual, clique em **Model**  >  **Process**  >  **Process All**. Se o processamento falhar, a implementação não é efetuada com êxito.
> 
> 

## <a name="get-the-server-name"></a>Obtenha o nome do servidor

No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Para implementar a partir do Estúdio Visual

1. No Visual Studio > **Solution Explorer,** clique com o botão direito no projeto > **Properties.** Em seguida, no **Servidor de Implementação**  >  **Server** cole o nome do servidor.   
   
    ![Colar o nome do servidor na propriedade de implementação do servidor](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. Em **Explorador de Soluções**, clique com o botão direito do rato em **Propriedades** e, em seguida, clique em **Implementar**. Ser-lhe-á pedido que inicie sessão no Azure.
   
    ![Implementar no servidor](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    O estado de implementação é exibido na janela de saída e na implementação.
   
    ![Estado da implementação](./media/analysis-services-deploy/aas-deploy-status.png)

E é tudo o que é preciso!


## <a name="troubleshooting"></a>Resolução de problemas

Se a implementação falhar ao implementar metadados, é provável que o Visual Studio não consiga ligar-se ao seu servidor. Certifique-se de que pode ligar-se ao seu servidor utilizando o SQL Server Management Studio (SSMS). Em seguida, verifique se a propriedade de implementação do servidor é a correta para o projeto.

Se a implementação falhar numa tabela, provavelmente não foi possível estabelecer ligação entre o servidor e a origem de dados. Se a sua origem de dados se encontra na rede da sua organização, certifique-se de que instala um [gateway de dados no local](analysis-services-gateway.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem o modelo de tabela implementado no seu servidor, está pronto para se estabelecer ligação com o mesmo. Pode [ligar-se a ele com o SQL Server Management Studio (SSMS)](analysis-services-manage.md) para o gerir. E pode [ligá-lo através de uma ferramenta de cliente](analysis-services-connect.md) como o Power BI, o Power BI Desktop ou o Excel e começar a criar relatórios.   

Para saber mais sobre métodos de implementação avançados, consulte [a implementação da solução de modelo Tabular](/analysis-services/deployment/tabular-model-solution-deployment?view=azure-analysis-services-current&preserve-view=true).
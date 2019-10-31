---
title: Implantar em Azure Analysis Services usando o Visual Studio | Microsoft Docs
description: Saiba como implantar um modelo de tabela em um servidor Azure Analysis Services usando o Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 60681739854515078a521a4ff795e52aa7d74183
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146962"
---
# <a name="deploy-a-model-from-visual-studio"></a>Implementar um modelo do Visual Studio

Depois de criar um servidor na sua subscrição do Azure, está pronto para implementar uma base de dados de modelo de tabela no mesmo. Você pode usar o Visual Studio com projetos Analysis Services para criar e implantar um projeto de modelo de tabela no qual você está trabalhando. 

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* **Servidor Analysis Services** no Azure. Para obter mais informações, consulte [Criar um servidor Azure Analysis](analysis-services-create-server.md).
* **Projeto de modelo de tabela** no Visual Studio ou um modelo de tabela existente no nível de compatibilidade 1200 ou superior. Nunca criou um? Experimente o [tutorial do modelo de tabela de vendas na Internet Adventure Works](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Gateway local** - se uma ou mais origens de dados se encontrarem no local da rede da sua organização, é necessário instalar um [gateway de dados no local](analysis-services-gateway.md). O gateway é necessário para que o servidor na nuvem se ligue às suas origens de dados no local para processar e atualizar os dados no modelo.

> [!TIP]
> Antes de implementar, certifique-se de que pode processar os dados nas tabelas. No Visual Studio, clique em **modelo** > **processo** > **processar tudo**. Se o processamento falhar, a implementação não é efetuada com êxito.
> 
> 

## <a name="get-the-server-name"></a>Obter o nome do servidor

No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Para implantar a partir do Visual Studio

1. No Visual Studio > **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Propriedades**. Em seguida, em **Implementação** > **Servidor** cole o nome do servidor.   
   
    ![Colar o nome do servidor na propriedade de implementação do servidor](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. Em **Explorador de Soluções**, clique com o botão direito do rato em **Propriedades** e, em seguida, clique em **Implementar**. Ser-lhe-á pedido que inicie sessão no Azure.
   
    ![Implementar no servidor](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    O estado de implementação é exibido na janela de saída e na implementação.
   
    ![Estado da implementação](./media/analysis-services-deploy/aas-deploy-status.png)

E é tudo o que é preciso!


## <a name="troubleshooting"></a>Resolução de problemas

Se a implantação falhar durante a implantação de metadados, é provável que o Visual Studio não tenha se conectado ao servidor. Verifique se é possível estabelecer ligação ao seu servidor com o SSMS. Em seguida, verifique se a propriedade de implementação do servidor é a correta para o projeto.

Se a implementação falhar numa tabela, provavelmente não foi possível estabelecer ligação entre o servidor e a origem de dados. Se a sua origem de dados se encontra na rede da sua organização, certifique-se de que instala um [gateway de dados no local](analysis-services-gateway.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem o modelo de tabela implementado no seu servidor, está pronto para se estabelecer ligação com o mesmo. Você pode [se conectar a ele com o SQL Server Management Studio (SSMS)](analysis-services-manage.md) para gerenciá-lo. E pode [ligá-lo através de uma ferramenta de cliente](analysis-services-connect.md) como o Power BI, o Power BI Desktop ou o Excel e começar a criar relatórios.


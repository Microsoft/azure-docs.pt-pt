---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/31/2019
ms.openlocfilehash: e571c65e64fad73c646aa05366cab685aa745caa
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968920"
---
1. Entre no [portal do Azure](https://portal.azure.com/) usando as credenciais para a assinatura do Azure que você usa. 

1. No canto superior esquerdo da portal do Azure, selecione **criar um recurso**.

1. Use a barra de pesquisa para localizar **Machine Learning espaço de trabalho de serviço**.

1. Selecione **Machine Learning espaço de trabalho de serviço**.

1. No painel **espaço de trabalho do serviço ml** , selecione **criar** para começar.

1. Configure seu novo espaço de trabalho fornecendo o nome, a assinatura, o grupo de recursos e o local do espaço de trabalho.

    ![Criar área de trabalho](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Campo|Descrição 
   ---|---
   Nome da área de trabalho |Insira um nome exclusivo que identifique seu espaço de trabalho. Neste exemplo, usamos **docs-WS**. Os nomes devem ser exclusivos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferencie os espaços de trabalho criados por outras pessoas.  
   Subscription |Selecione a subscrição do Azure que pretende utilizar.
   Resource group | Use um grupo de recursos existente em sua assinatura ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos contém recursos relacionados para uma solução do Azure. Neste exemplo, usamos **docs-AML**. 
   Location | Selecione o local mais próximo de seus usuários e os recursos de dados para criar seu espaço de trabalho.

1. Depois de concluir a configuração do espaço de trabalho, selecione **criar**. 

   Pode levar alguns minutos para criar o espaço de trabalho.

   Quando o processo for concluído, uma mensagem de êxito de implantação será exibida. Ele também está presente na seção notificações. Para exibir o novo espaço de trabalho, selecione **ir para o recurso**.

   ![Status de criação do espaço de trabalho](./media/aml-create-in-portal/notifications.png)

1. Se você for usar uma opção sem código no portal, como a interface visual ou experimentos de ML automatizados, você estará pronto agora. Se você for criar uma [VM do bloco de anotações](../articles/machine-learning/service/tutorial-1st-experiment-sdk-setup.md#azure) , também será feito. 

1. Se você planeja usar o código em seu ambiente local que faz referência a esse espaço de trabalho, selecione **baixar config. JSON** na seção **visão geral** do espaço de trabalho.  

   ![Transferir o config.json](./media/aml-create-in-portal/configure.png)
   
   Coloque o arquivo na estrutura de diretório com seus scripts Python ou Jupyter notebooks. Ele pode estar no mesmo diretório, um subdiretório chamado *. azureml*ou em um diretório pai. Quando você cria uma VM de notebook, esse arquivo é adicionado ao diretório correto na VM para você.

    


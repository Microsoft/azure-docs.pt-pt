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
ms.date: 11/04/2019
ms.openlocfilehash: 8ccd3e6129f4a061eacf83a1f4e70174c697480f
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73633723"
---
1. Entre no [portal do Azure](https://portal.azure.com/) usando as credenciais para sua assinatura do Azure.

1. No canto superior esquerdo da portal do Azure, selecione **+ criar um recurso**.

    ![Criar um novo recurso](media/aml-create-in-portal/create-workspace.gif)

1. Use a barra de pesquisa para localizar **Machine Learning**.

1. Selecione **Machine Learning**.

1. No painel **Machine Learning** , selecione **criar** para começar.

1. Forneça as seguintes informações para configurar seu novo espaço de trabalho:

   Campo|Descrição 
   ---|---
   Nome do espaço de trabalho |Insira um nome exclusivo que identifique seu espaço de trabalho. Neste exemplo, usamos **docs-WS**. Os nomes devem ser exclusivos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferencie os espaços de trabalho criados por outras pessoas.  
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Use um grupo de recursos existente em sua assinatura ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos contém recursos relacionados para uma solução do Azure. Neste exemplo, usamos **docs-AML**. 
   Localização | Selecione o local mais próximo de seus usuários e os recursos de dados para criar seu espaço de trabalho.
   Edição do espaço de trabalho | Selecione **básico** como o tipo de espaço de trabalho para este tutorial. O tipo de espaço de trabalho (básico & Enterprise) determina os recursos aos quais você terá acesso e preços. Tudo neste tutorial pode ser executado com um espaço de trabalho básico ou empresarial.

1. Depois de concluir a configuração do espaço de trabalho, selecione **revisar + criar**. 

   > [!Warning] 
   > Pode levar vários minutos para criar seu espaço de trabalho na nuvem.

   Quando o processo for concluído, uma mensagem de êxito de implantação será exibida. 
 
 1. Para exibir o novo espaço de trabalho, selecione **ir para o recurso**.


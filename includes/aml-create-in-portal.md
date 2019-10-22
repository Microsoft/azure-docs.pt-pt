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
ms.date: 08/14/2019
ms.openlocfilehash: 1da2afc6c0dfa0571d6e7ccbf358bb574aa736c5
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71830151"
---
1. Entre no [portal do Azure](https://portal.azure.com/) usando as credenciais para a assinatura do Azure que você usa. 

1. No canto superior esquerdo da portal do Azure, selecione **+ criar um recurso**.

      ![Criar um novo recurso](media/aml-create-in-portal/portal-create-resource.png)

1. Use a barra de pesquisa para localizar **Machine Learning espaço de trabalho de serviço**.

1. Selecione **Machine Learning espaço de trabalho de serviço**.

1. No painel **Machine Learning espaço de trabalho do serviço** , selecione **criar** para começar.

1. Forneça as seguintes informações para configurar seu novo espaço de trabalho:

   Campo|Descrição 
   ---|---
   Nome do espaço de trabalho |Insira um nome exclusivo que identifique seu espaço de trabalho. Neste exemplo, usamos **docs-WS**. Os nomes devem ser exclusivos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferencie os espaços de trabalho criados por outras pessoas.  
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Use um grupo de recursos existente em sua assinatura ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos contém recursos relacionados para uma solução do Azure. Neste exemplo, usamos **docs-AML**. 
   Localização | Selecione o local mais próximo de seus usuários e os recursos de dados para criar seu espaço de trabalho.

1. Depois de concluir a configuração do espaço de trabalho, selecione **criar**. 

   > [!Warning] 
   > Pode levar vários minutos para criar seu espaço de trabalho na nuvem.

   Quando o processo for concluído, uma mensagem de êxito de implantação será exibida. 
 
 1. Para exibir o novo espaço de trabalho, selecione **ir para o recurso**.


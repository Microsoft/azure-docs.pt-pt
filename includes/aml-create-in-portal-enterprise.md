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
ms.date: 10/04/2019
ms.openlocfilehash: 8b333dbd043ea0a0fe6fb1042e255e7e9c07ade5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493411"
---
1. Entre no [portal do Azure](https://portal.azure.com/) usando as credenciais para sua assinatura do Azure. 

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
   Edição do espaço de trabalho | Selecione **Enterprise**.  Este tutorial requer o uso do Enterprise Edition.  A edição Enterprise está em versão prévia e não adiciona nenhum custo extra no momento.  

1. Depois de concluir a configuração do espaço de trabalho, selecione **criar**. 

   > [!Warning] 
   > Pode levar vários minutos para criar seu espaço de trabalho na nuvem.

   Quando o processo for concluído, uma mensagem de êxito de implantação será exibida. 
 
 1. Para exibir o novo espaço de trabalho, selecione **ir para o recurso**.


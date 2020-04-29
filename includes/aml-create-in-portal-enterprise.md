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
ms.openlocfilehash: b44d624db419919823ec85bcb599007fb75c92b9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73929647"
---
1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando as credenciais para a sua subscrição Azure. 

1. No canto superior esquerdo do portal Azure, selecione **+ Crie um recurso**.

      ![Criar um novo recurso](media/aml-create-in-portal/create-workspace.gif)

1. Utilize a barra de pesquisa para encontrar **machine learning**.

1. Selecione **Machine Learning**.

1. No painel **machine learning,** selecione **Criar** para começar.

1. Forneça as seguintes informações para configurar o seu novo espaço de trabalho:

   Campo|Descrição 
   ---|---
   Nome da área de trabalho |Introduza um nome único que identifique o seu espaço de trabalho. Neste exemplo, utilizamos **docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de recordar e para diferenciar dos espaços de trabalho criados por outros. 
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Utilize um grupo de recursos existente na sua subscrição ou introduza um nome para criar um novo grupo de recursos. Um grupo de recursos detém recursos relacionados para uma solução Azure. Neste exemplo, utilizamos **docs-aml**. 
   Localização | Selecione a localização mais próxima dos seus utilizadores e os recursos de dados para criar o seu espaço de trabalho.
   Edição do espaço de trabalho | Selecione **Enterprise**. Este tutorial requer o uso da edição da Enterprise. A edição da Enterprise está em pré-visualização e não adiciona atualmente custos adicionais. 

1. Depois de terminar de configurar o espaço de trabalho, selecione **Criar**. 

   > [!WARNING] 
   > Pode levar vários minutos para criar o seu espaço de trabalho na nuvem.

   Quando o processo está concluído, aparece uma mensagem de sucesso de implantação. 
 
 1. Para ver o novo espaço de trabalho, selecione **Ir para o recurso**.


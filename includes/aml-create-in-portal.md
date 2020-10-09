---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 11/04/2019
ms.openlocfilehash: f5f132d257e30cd8f4fa1153087bf0df2f0f5b2c
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841861"
---
1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando as credenciais para a sua subscrição Azure.

1. No canto superior esquerdo do portal Azure, selecione **+ Criar um recurso**.

    ![Screenshot que mostra a opção Criar uma opção de recurso.](media/aml-create-in-portal/create-workspace.gif)

1. Utilize a barra de pesquisa para encontrar **machine learning**.

1. Selecione **Machine Learning**.

1. No painel **de aprendizagem** automática, selecione **Criar** para começar.

1. Forneça as seguintes informações para configurar o seu novo espaço de trabalho:

   Campo|Descrição
   ---|---
   Nome da área de trabalho |Insira um nome único que identifique o seu espaço de trabalho. Neste exemplo, **usamos docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de recordar e diferenciar dos espaços de trabalho criados por outros.
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Utilize um grupo de recursos existente na sua subscrição ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos detém recursos relacionados para uma solução Azure. Neste exemplo, **usamos docs-aml**. 
   Localização | Selecione a localização mais próxima dos seus utilizadores e os recursos de dados para criar o seu espaço de trabalho.
   Edição do espaço de trabalho | Selecione **Basic** como o tipo de espaço de trabalho para este tutorial. O tipo de espaço de trabalho determina as funcionalidades às quais terá acesso e preços. Tudo neste tutorial pode ser realizado com um espaço de trabalho básico ou empresarial.

1. Depois de terminar de configurar o espaço de trabalho, selecione **Review + Create**.

   > [!Warning]
   > Pode levar vários minutos para criar o seu espaço de trabalho na nuvem.

   Quando o processo está concluído, aparece uma mensagem de sucesso de implantação.
 
 1. Para visualizar o novo espaço de trabalho, selecione **Ir para o recurso**.


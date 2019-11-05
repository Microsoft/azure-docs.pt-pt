---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: 08833f869a7838fe893b8e941072078f89033c2b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517855"
---
1. Entre no [Azure Machine Learning Studio](https://ml.azure.com).

1. Atualize seu espaço de trabalho para a edição Enterprise.

    Após a atualização, todos os experimentos da interface visual serão convertidos em rascunhos do pipeline no designer.
    
    > [!NOTE]
    > Você não precisa atualizar para a Enterprise Edition para converter os serviços Web da interface visual em pontos de extremidade em tempo real.
    
1. Vá para a seção designer do espaço de trabalho para exibir sua lista de rascunhos de pipeline. 
    
    Os serviços Web convertidos podem ser encontrados navegando até **pontos de extremidade** > **pontos de extremidade em tempo real**.

1. Selecione um rascunho de pipeline para abri-lo.

    Se houver um erro durante o processo de conversão, uma mensagem de erro será exibida com instruções para resolver o problema. 

### <a name="known-issues"></a>Problemas conhecidos

 Abaixo estão os problemas de migração conhecidos que precisam ser resolvidos manualmente:

- **Importar dados** ou **Exportar** módulos de dados
        
    Se você tiver um módulo **importar dados** ou **exportar dados** no experimento, precisará atualizar a fonte de dados para usar os armazenamentos. Para saber como criar um armazenamento de dados, confira [como acessar os dados nos serviços de armazenamento do Azure](../articles/machine-learning/service/how-to-access-data.md). As informações da sua conta de armazenamento em nuvem foram adicionadas nos comentários do módulo **importar dados** ou **exportar dados** para sua conveniência. 
      
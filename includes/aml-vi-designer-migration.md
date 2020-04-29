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
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75541806"
---
1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com)

1. Atualize o seu espaço de trabalho para a edição da Enterprise.

    Após a atualização, todas as suas experiências de interface visual converter-se-ão em rascunhos de pipeline no designer.
    
    > [!NOTE]
    > Não é necessário fazer upgrade para a edição da Enterprise para converter serviços web de interface visual em pontos finais em tempo real.
    
1. Vá à secção de design do espaço de trabalho para ver a sua lista de rascunhos de pipeline. 
    
    Os serviços web convertidos podem ser encontrados navegando até **pontos finais** > em**tempo real.**

1. Selecione um rascunho de gasoduto para abri-lo.

    Se houve um erro durante o processo de conversão, aparecerá uma mensagem de erro com instruções para resolver o problema. 

### <a name="known-issues"></a>Problemas conhecidos

 Abaixo estão conhecidas questões de migração que precisam de ser abordadas manualmente:

- **Dados de Importação** ou Módulos de **Dados de Exportação**
        
    Se tiver um módulo de **Dados de Importação** ou Dados de **Exportação** na experiência, precisa de atualizar a fonte de dados para utilizar uma datastore. Para aprender a criar uma loja de dados, consulte Como Aceder a Dados nos serviços de [armazenamento do Azure.](../articles/machine-learning/how-to-access-data.md) As informações da sua conta de armazenamento na nuvem foram adicionadas nos comentários do módulo de Dados de **Importação** ou Dados de **Exportação** para sua conveniência. 
      
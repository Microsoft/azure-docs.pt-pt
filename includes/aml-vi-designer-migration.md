---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75541806"
---
1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. Atualize o seu espaço de trabalho para a edição da Enterprise.

    Após a atualização, todas as suas experiências de interface visual serão convertidas em rascunhos de pipeline no designer.
    
    > [!NOTE]
    > Não precisa de fazer upgrade para a edição da Enterprise para converter serviços web de interface visual em pontos finais em tempo real.
    
1. Vá à secção de designers do espaço de trabalho para ver a sua lista de rascunhos de gasodutos. 
    
    Os serviços web convertidos podem ser encontrados navegando para **endpoints**  >  **pontos finais em tempo real.**

1. Selecione um rascunho de gasoduto para abri-lo.

    Se houve um erro durante o processo de conversão, aparecerá uma mensagem de erro com instruções para resolver o problema. 

### <a name="known-issues"></a>Problemas conhecidos

 Abaixo estão conhecidas as questões migratórias que precisam de ser abordadas manualmente:

- **Dados de Importação** ou Módulos **de Dados de Exportação**
        
    Se tiver um módulo **de Dados de Importação** ou **Dados de Exportação** na experiência, precisa de atualizar a fonte de dados para utilizar uma datas-loja. Para aprender a criar uma loja de dados, consulte [Como Aceder aos Dados nos serviços de armazenamento Azure.](../articles/machine-learning/how-to-access-data.md) As informações da sua conta de armazenamento em nuvem foram adicionadas nos comentários do módulo **de Dados** de Importação ou **Dados de Exportação** para sua conveniência. 
      
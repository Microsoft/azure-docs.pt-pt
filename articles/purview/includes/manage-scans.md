---
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.openlocfilehash: ac83f455d2af82c3f6970077fccb17f07e53cccc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98901143"
---
## <a name="creating-and-running-a-scan"></a>Criar e executar uma digitalização

> [!Note] 
> Os passos e imagens mostrados abaixo ilustram o processo geral de gestão de digitalizações em diferentes tipos de fonte de dados. As suas opções podem diferir ligeiramente dependendo dos tipos de fontes de dados com que está a trabalhar.

Para criar e executar uma nova digitalização, faça o seguinte:

1. Navegar para as **Fontes**

1. Selecione a fonte de dados que registou.

1. Selecione **+ Nova digitalização**

1. Selecione a credencial para ligar à sua fonte de dados. 

   :::image type="content" source="media/manage-scans/set-up-scan.png" alt-text="Configurar digitalização":::

1. Pode analisar a sua verificação em partes específicas da fonte de dados, tais como pastas, coleções ou esquemas, verificando os itens apropriados na lista.

   :::image type="content" source="media/manage-scans/scope-your-scan.png" alt-text="Coloque o seu scan":::

1. Selecione uma regra de digitalização definida para o seu scan. Pode escolher entre o padrão do sistema, os personalizados existentes ou criar um novo inline.

   :::image type="content" source="media/manage-scans/scan-rule-set.png" alt-text="Conjunto de regras de digitalização":::

1. Escolha o seu gatilho de digitalização. Pode configurar um horário ou fazer a verificação uma vez.

   :::image type="content" source="media/manage-scans/trigger-scan.png" alt-text="gatilho":::

1. Reveja a sua digitalização e **selecione Guardar e executar**.

## <a name="viewing-your-scans-and-scan-runs"></a>Visualizar os seus exames e digitalizar corre

Para visualizar as análises existentes, faça o seguinte:

1. Navegue para o centro de gestão. Selecione **fontes de dados** sob as fontes e a secção **de digitalização.** 

2. Selecione a fonte de dados desejada. Verá uma lista de digitalizações existentes nessa fonte de dados.

3. Selecione a verificação cujos resultados está interessado em visualizar.

4. Esta página irá mostrar-lhe todas as verificações anteriores, juntamente com métricas e estado para cada execução de digitalização. Também irá mostrar se a sua digitalização foi programada ou manual, quantos ativos tinham classificações aplicadas, quantos ativos totais foram descobertos, o início e o fim da verificação, e a duração total da verificação.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gerir as suas digitalizações - editar, excluir ou cancelar

Para gerir ou apagar uma verificação, faça o seguinte:

1. Navegue para o centro de gestão. Selecione **fontes de dados** sob as **Fontes e a** secção de digitalização e, em seguida, selecione na fonte de dados desejada.

2. Selecione a digitalização que gostaria de fazer. Pode editar a digitalização selecionando **Editar.**

3. Pode eliminar a sua verificação selecionando **Delete**. 

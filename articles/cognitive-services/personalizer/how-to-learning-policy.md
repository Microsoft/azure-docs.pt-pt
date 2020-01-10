---
title: Gerenciar política de aprendizado-personalizador
description: Este artigo contém respostas para perguntas frequentes sobre solução de problemas sobre o personalizador.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 707ba9fe7b73ef74e21321533ba02b11f2bad850
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75843484"
---
# <a name="manage-learning-policy"></a>Gerenciar política de aprendizado

As configurações de política de aprendizado determinam os _hiperparâmetros_ do treinamento do modelo. A política de aprendizado é definida em um arquivo de `.json`.

## <a name="import-a-new-learning-policy"></a>Importar uma nova política de aprendizado

1. Abra o [portal do Azure](https://portal.azure.com)e selecione o recurso personalizador.
1. Selecione **configurações de modelo e aprendizado** na seção **Gerenciamento de recursos** .
1. Para **Importar configurações de aprendizado** , selecione o arquivo que você criou com o formato JSON especificado acima e, em seguida, selecione o botão **carregar** .

    Aguarde a notificação de que a política de aprendizado foi carregada com êxito.

## <a name="export-a-learning-policy"></a>Exportar uma política de aprendizado

1. Abra o [portal do Azure](https://portal.azure.com)e selecione o recurso personalizador.
1. Selecione **configurações de modelo e aprendizado** na seção **Gerenciamento de recursos** .
1. Para **Importar configurações de aprendizado** , selecione o botão **exportar configurações de aprendizado** . Isso salva o arquivo de `json` em seu computador local.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [conceitos](concept-active-learning.md#learning-settings) de política de aprendizagem

[Saiba mais sobre a disponibilidade de região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
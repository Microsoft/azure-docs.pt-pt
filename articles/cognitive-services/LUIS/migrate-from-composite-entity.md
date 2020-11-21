---
title: Atualizar entidade composta - LUIS
description: Atualizar a entidade composta para entidade de aprendizagem automática com processo de upgrade no portal LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 46e9ece70d9f980065c719ee1205eb46591b45c0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025247"
---
# <a name="upgrade-composite-entity-to-machine-learning-entity"></a>Atualizar a entidade composta para a entidade de aprendizagem automática

Atualizar a entidade composta para a entidade de machine-learning para construir uma entidade que recebe previsões mais completas com melhor decomposição para depurar a entidade.

## <a name="current-version-model-restrictions"></a>Restrições de modelo de versão atual

O processo de upgrade cria entidades de machine-learning, com base nas entidades compósitas existentes encontradas na sua app, numa nova versão da sua app. Isto inclui entidades compósitas crianças e papéis. O processo também muda as etiquetas em palavras de exemplo para usar a nova entidade.

## <a name="upgrade-process"></a>Processo de atualização

O processo de atualização:
* Cria uma nova entidade de aprendizagem automática para cada entidade composta.
* Entidades infantis:
    * Se a entidade infantil for utilizada apenas em entidade composta, esta só será adicionada à entidade de aprendizagem automática.
    * Se a entidade infantil for utilizada em compósitos _e_ como entidade separada (rotulada por palavras de exemplo), será adicionada à versão como entidade e como entidade de aprendizagem automática.
    * Se a entidade infantil utilizar um papel, cada função será convertida numa sub-entidade com o mesmo nome.
    * Se a entidade infantil for uma entidade que não é máquina de aprendizagem (expressão regular, entidade de lista ou entidade pré-construída), uma nova entidade é criada com o mesmo nome, e a nova entidade tem uma funcionalidade utilizando a entidade de não aprendizagem automática com a funcionalidade necessária adicionada.
* Os nomes são retidos, mas devem ser únicos ao mesmo nível de entidade/irmão. Consulte [os limites de nomeação únicos.](./luis-limits.md#name-uniqueness)
* As etiquetas, por exemplo, são mudadas para nova entidade de aprendizagem automática com subentências.

Use o seguinte gráfico para entender como o seu modelo muda:

|Objeto antigo|Novo objeto|Notas|
|--|--|--|
|Entidade composta|entidade de aprendizagem automática com estrutura|Ambos os objetos são objetos-mãe.|
|A entidade infantil da Compósita é **uma entidade simples**|subgrupo|Ambos os objetos são objetos infantis.|
|A entidade infantil da Compósita é **entidade pré-construída,** como o Número|subgrupo com nome de entidade pré-construída, como Número, e sub-entidade tem _característica_ de entidade número pré-construído com opção de restrição definida para _verdadeiro_.|a subinidade contém funcionalidade com restrição a nível de subinidade.|
|A entidade infantil da Compósita é **entidade pré-construída,** como o Número, e a entidade pré-construída tem um **papel**|sub-entidade com nome de função, e sub-entidade tem característica de entidade número pré-construído com opção de restrição definida para verdadeiro.|a subinidade contém funcionalidade com restrição a nível de subinidade.|
|Função|subgrupo|O nome da função torna-se o nome da sub-entidade. A subgrupo é descendente direto da entidade de aprendizagem automática.|

## <a name="begin-upgrade-process"></a>Iniciar o processo de upgrade

Antes de atualizar, certifique-se de:

* Alterar versões se não estiver na versão correta para atualizar


1. Inicie o processo de atualização a partir da notificação ou pode esperar até ao próximo aviso agendado.

    > [!div class="mx-imgBorder"]
    > ![Iniciar atualização a partir de notificações](./media/update-composite-entity/notification-begin-update.png)

1. No pop-up, selecione **Upgrade agora**.

1. Reveja o **Que acontece quando atualiza** as informações e selecione **Continue**.

1. Selecione as entidades compostas da lista para atualizar e, em seguida, **selecione Continue**.

1. Pode mover quaisquer alterações não treinadas da versão atual para a versão atualizada selecionando a caixa de verificação.

1. **Selecione Continue** a iniciar o processo de atualização.

1. A barra de progresso indica o estado do processo de atualização.

1. Quando o processo estiver concluído, você está em uma nova versão treinada com as novas entidades de aprendizagem automática. Selecione **Experimente as suas novas entidades** para ver a nova entidade.

    Se a atualização ou formação falhou para a nova entidade, uma notificação fornece mais informações.

1. Na página da lista de Entidades, as novas entidades são marcadas com **NOVA** junto ao nome do tipo.

## <a name="next-steps"></a>Próximos passos

* [Autores e colaboradores](luis-how-to-collaborate.md)
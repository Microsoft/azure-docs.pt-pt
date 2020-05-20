---
title: Entidade composta de upgrade - LUIS
description: Upgrade entidade composta para entidade de machine-learning com processo de upgrade no portal LUIS.
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 58b546a27c1ff1e90e1b70026f430063a47a09e8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684081"
---
# <a name="upgrade-composite-entity-to-machine-learning-entity"></a>Atualizar entidade composta para entidade de aprendizagem automática

Atualize a entidade composta para a entidade de machine-learning para construir uma entidade que receba previsões mais completas com melhor descomposição para depurar a entidade.

## <a name="current-version-model-restrictions"></a>Restrições de modelo de versão atual

O processo de upgrade cria entidades de machine-learning, com base nas entidades compósitas existentes encontradas na sua app, numa nova versão da sua app. Isto inclui entidades compostas crianças e papéis. O processo também muda os rótulos em declarações exemplo para usar a nova entidade.

## <a name="upgrade-process"></a>Processo de atualização

O processo de atualização:
* Cria uma nova entidade de aprendizagem automática para cada entidade composta.
* Entidades infantis:
    * Se a entidade infantil for utilizada apenas em entidade seleto, só será adicionada à entidade de machine-learning.
    * Se a entidade infantil for utilizada em compósito _e_ como entidade separada (rotulada por exemplo, serão adicionadas à versão como entidade e como uma subentidade à nova entidade de machine-learning.
    * Se a entidade infantil utilizar um papel, cada papel será convertido numa subentidade com o mesmo nome.
    * Se a entidade infantil for uma entidade não-machine-learning (expressão regular, entidade de lista ou entidade pré-construída), é criada uma nova subentidade com o mesmo nome, e a nova subentidade tem uma funcionalidade utilizando a entidade não-machine-learning com a funcionalidade necessária adicionada.
* Os nomes são mantidos, mas devem ser únicos ao mesmo nível de subentidade/irmão. Consulte [os limites únicos de nomeação.](luis-boundaries.md#name-uniqueness)
* As etiquetas em exemplo são mudadas para nova entidade de aprendizagem automática com subentidades.

Utilize o gráfico seguinte para entender como o seu modelo muda:

|Objeto antigo|Novo objeto|Notas|
|--|--|--|
|Entidade composta|entidade de machine-learning com estrutura|Ambos os objetos são objetos dos pais.|
|A entidade infantil da Composite é **uma entidade simples**|subentidade|Ambos os objetos são objetos infantis.|
|A entidade infantil da Composite é **entidade pré-construída,** como o Número|subentidade com nome de entidade pré-construída, como Número, e subentidade tem _característica_ da entidade Número Pré-Construído com opção de restrição definida para _verdade_.|subentidade contém recurso com restrição ao nível da subentidade.|
|A entidade infantil da Composite é **entidade pré-construída,** como o Número, e a entidade pré-construída tem um **papel**|subentidade com nome de papel, e subentidade tem característica da entidade Número Pré-Construído com opção de restrição definida para verdade.|subentidade contém recurso com restrição ao nível da subentidade.|
|Função|subentidade|O nome do papel torna-se o nome da subentidade. A subentidade é descendente direta da entidade de machine-learning.|

## <a name="begin-upgrade-process"></a>Iniciar processo de upgrade

Antes de atualizar, certifique-se de:

* Altere as versões se não estiver na versão correta para atualizar


1. Inicie o processo de atualização a partir da notificação ou pode esperar até ao próximo pedido agendado.

    > [!div class="mx-imgBorder"]
    > ![Comece a atualizar a partir de notificações](./media/update-composite-entity/notification-begin-update.png)

1. No pop-up, selecione **Upgrade agora**.

1. Reveja o **que acontece quando atualizar** informações e selecione **Continuar**.

1. Selecione as entidades compostas da lista para atualizar e, em seguida, **selecione Continuar**.

1. Pode mover quaisquer alterações não treinadas da versão atual para a versão atualizada selecionando a caixa de verificação.

1. Selecione **Continuar** a iniciar o processo de atualização.

1. A barra de progresso indica o estado do processo de atualização.

1. Quando o processo estiver concluído, está numa nova versão treinada com as novas entidades de machine-learning. Selecione **Experimente as suas novas entidades** para ver a nova entidade.

    Se a atualização ou formação falhar para a nova entidade, uma notificação fornece mais informações.

1. Na página da lista da Entidade, as novas entidades estão marcadas com **NOVA** junto ao nome do tipo.

## <a name="next-steps"></a>Passos seguintes

* [Autores e colaboradores](luis-how-to-collaborate.md)
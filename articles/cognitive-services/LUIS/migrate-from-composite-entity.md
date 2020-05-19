---
title: Entidade composta de upgrade - LUIS
description: Upgrade entidade composta para entidade aprendida por máquinas com processo de upgrade no portal LUIS.
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 9bbb03a2009bdcb6e2fa5d20aefadd4c7c99f025
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599427"
---
# <a name="upgrade-composite-entity-to-machine-learned-entity"></a>Atualizar entidade compósita para entidade aprendida por máquinas

Atualize a entidade composta para entidade aprendida por máquinas para construir uma entidade que receba previsões mais completas com melhor descomposição para depurar a entidade.

## <a name="current-version-model-restrictions"></a>Restrições de modelo de versão atual

O processo de upgrade cria entidades aprendidas com máquinas, com base nas entidades compósitas existentes encontradas na sua app, numa nova versão da sua app. Isto inclui entidades compostas crianças e papéis. O processo também muda os rótulos em declarações exemplo para usar a nova entidade.

## <a name="upgrade-process"></a>Processo de atualização

O processo de atualização:
* Cria uma nova entidade aprendida por máquinas para cada entidade composta.
* Entidades infantis:
    * Se a entidade infantil for utilizada apenas em entidade seleto, só será adicionada à entidade aprendida por máquinas.
    * Se a entidade infantil for utilizada em compósito _e_ como entidade separada (rotulada por exemplo, serão adicionadas à versão como entidade e como uma subentidade à nova entidade aprendida por máquinas.
    * Se a entidade infantil utilizar um papel, cada papel será convertido numa subentidade com o mesmo nome.
    * Se a entidade infantil for uma entidade não-aprendida por máquinas (expressão regular, entidade de lista ou entidade pré-construída), é criada uma nova subentidade com o mesmo nome, e a nova subentidade tem uma funcionalidade utilizando a entidade não-metida com a funcionalidade necessária adicionada.
* Os nomes são mantidos, mas devem ser únicos ao mesmo nível de subentidade/irmão. Consulte [os limites únicos de nomeação.](luis-boundaries.md#name-uniqueness)
* As etiquetas em exemplo são mudadas para uma nova entidade aprendida por máquinas com subentidades.

Utilize o gráfico seguinte para entender como o seu modelo muda:

|Objeto antigo|Novo objeto|Notas|
|--|--|--|
|Entidade composta|Entidade aprendida por máquinas com estrutura|Ambos os objetos são objetos dos pais.|
|A entidade infantil da Composite é **uma entidade simples**|subentidade|Ambos os objetos são objetos infantis.|
|A entidade infantil da Composite é **entidade pré-construída,** como o Número|subentidade com nome de entidade pré-construída, como Número, e subentidade tem _característica_ da entidade Número Pré-Construído com opção de restrição definida para _verdade_.|subentidade contém recurso com restrição ao nível da subentidade.|
|A entidade infantil da Composite é **entidade pré-construída,** como o Número, e a entidade pré-construída tem um **papel**|subentidade com nome de papel, e subentidade tem característica da entidade Número Pré-Construído com opção de restrição definida para verdade.|subentidade contém recurso com restrição ao nível da subentidade.|
|Função|subentidade|O nome do papel torna-se o nome da subentidade. A subentidade é descendente direta da entidade aprendida por máquinas.|

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

1. Quando o processo estiver concluído, está numa nova versão treinada com as novas entidades aprendidas com máquinas. Selecione **Experimente as suas novas entidades** para ver a nova entidade.

    Se a atualização ou formação falhar para a nova entidade, uma notificação fornece mais informações.

1. Na página da lista da Entidade, as novas entidades estão marcadas com **NOVA** junto ao nome do tipo.

## <a name="next-steps"></a>Passos seguintes

* [Autores e colaboradores](luis-how-to-collaborate.md)
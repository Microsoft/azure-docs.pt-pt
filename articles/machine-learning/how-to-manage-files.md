---
title: Crie e gere ficheiros no seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como criar e gerir ficheiros no seu espaço de trabalho no estúdio Azure Machine Learning.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101590"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Como criar e gerir ficheiros no seu espaço de trabalho

Saiba como criar e gerir os ficheiros no seu espaço de trabalho Azure Machine Learning.  Estes ficheiros são armazenados no armazenamento predefinido do espaço de trabalho. Os ficheiros e pastas podem ser partilhados com qualquer outra pessoa com acesso lido ao espaço de trabalho, e podem ser usados a partir de quaisquer instâncias de cálculo no espaço de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um espaço de trabalho de aprendizagem automática. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Criar ficheiros

Para criar um novo ficheiro na sua pasta predefinida `Users > yourname` ():

1. Abra o seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com)
1. Do lado esquerdo, selecione **Cadernos**.
1. Selecione a **+** imagem.
1. Selecione a nova imagem **do ficheiro Criar.**

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Criar novo ficheiro":::

1. Diga o nome do ficheiro.
1. Selecione um tipo de ficheiro.
1. Selecione **Criar**.

Os blocos de notas e a maioria dos tipos de ficheiros de texto são apresentados na secção de pré-visualização.  A maioria dos outros tipos de ficheiros não tem pré-visualização.

Para criar um novo ficheiro numa pasta diferente:
1. Selecione o "..." no final da pasta
1. **Selecione Criar novo ficheiro**.

> [!IMPORTANT]
> Os conteúdos em cadernos e scripts podem potencialmente ler dados das suas sessões e aceder a dados sem a sua organização em Azure.  Apenas carregue ficheiros de fontes fidedignas. Para obter mais informações, consulte [as melhores práticas do código Secure](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

## <a name="manage-files-with-git"></a>Gerir ficheiros com o Git

[Use um terminal de casos de computação](how-to-access-terminal.md#git) para clonar e gerir repositórios de Git.

## <a name="clone-samples"></a>Amostras de clones

O seu espaço de trabalho contém uma pasta **de cadernos sample** com cadernos projetados para ajudá-lo a explorar o SDK e servir como exemplos para seus próprios projetos de aprendizagem automática.   solitários estes cadernos na sua própria pasta para executá-los e editá-los.  

Por exemplo, consulte [Tutorial: Crie a sua primeira experiência ML](tutorial-1st-experiment-sdk-setup.md#azure).

## <a name="share-files"></a>Partilhar ficheiros

Copie e cole o URL para partilhar um ficheiro.  Apenas outros utilizadores do espaço de trabalho podem aceder a este URL.  Saiba mais sobre [a concessão de acesso ao seu espaço de trabalho.](how-to-assign-roles.md)

## <a name="delete-a-file"></a>Eliminar um ficheiro

*Não é possível* eliminar os **ficheiros de cadernos sample.**  Estes ficheiros fazem parte do estúdio e são atualizados cada vez que um novo SDK é publicado.  

*Pode* eliminar ficheiros encontrados na secção **Ficheiros** de qualquer forma:

* No estúdio, selecione o **...** no final de uma pasta ou arquivo.  Certifique-se de que utiliza um browser suportado (Microsoft Edge, Chrome ou Firefox).
* [Utilize um terminal](how-to-access-terminal.md) de qualquer instância de computação no seu espaço de trabalho. A pasta **~/cloudfiles** está mapeada para armazenamento na sua conta de armazenamento do espaço de trabalho.
* No Jupyter ou no JupyterLab com as suas ferramentas.

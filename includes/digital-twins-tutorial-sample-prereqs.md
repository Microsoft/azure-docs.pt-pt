---
author: baanders
description: incluir arquivo para tutoriais Azure Digital Twins - pré-requisitos para o projeto de amostra
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 5a1baf9631f2d30dd14ff16d2d34beda04605c6c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660526"
---
## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, **crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** antes de começar.

Também antes de iniciar, **instale [o Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), versão 16.5 ou posterior** na sua máquina de desenvolvimento. Se já tiver uma versão mais antiga instalada, pode abrir a aplicação *Visual Studio Installer* na sua máquina e seguir as instruções para atualizar a sua instalação.

O tutorial é conduzido por um projeto de amostra escrito em C#. A amostra está localizada aqui: [Azure Digital Twins amostras de ponta a ponta.](/samples/azure-samples/digital-twins-samples/digital-twins-samples) **Obtenha o projeto de amostra** na sua máquina navegando para a ligação de amostra e selecionando o botão de código *Browse* por baixo do título. Isto irá levá-lo ao gitHub repo para as amostras, que você pode baixar como um *. ZIP* selecionando o botão *Código* e *Descarregando ZIP*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Vista para as amostras digitais de gémeos no GitHub. O botão Código é selecionado, produzindo uma pequena caixa de diálogo onde é realçado o botão Download ZIP." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Isto irá descarregar um *. Pasta ZIP* para a sua máquina **comodigital-twins-samples-master.zip**. Desaperte a pasta e extraa os ficheiros.

### <a name="prepare-an-azure-digital-twins-instance"></a>Prepare uma instância Azure Digital Twins

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]

---
author: baanders
description: incluir arquivo para tutoriais Azure Digital Twins - pré-requisitos para o projeto de amostra
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622022"
---
## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste tutorial, você precisará primeiro completar os seguintes pré-requisitos. 

Se não tiver uma subscrição do Azure, **crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** antes de começar.

### <a name="get-required-resources"></a>Obtenha os recursos necessários

Para completar este tutorial, **instale [o Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), versão 16.5 ou posterior** na sua máquina de desenvolvimento. Se já tiver uma versão mais antiga instalada, pode abrir a aplicação *Visual Studio Installer* na sua máquina e seguir as instruções para atualizar a sua instalação.

>[!NOTE]
> Certifique-se de que a sua instalação do Visual Studio 2019 inclui a **[Carga de Trabalho para o Desenvolvimento do Azure.](/dotnet/azure/configure-visual-studio)** Esta carga de trabalho permite que uma aplicação publique funções Azure e execute outras tarefas de desenvolvimento do Azure.

O tutorial é conduzido por um projeto de amostra escrito em C#. A amostra está localizada aqui: [Azure Digital Twins amostras de ponta a ponta.](/samples/azure-samples/digital-twins-samples/digital-twins-samples) **Obtenha o projeto de amostra** na sua máquina navegando para a ligação de amostra e selecionando o botão de código *Browse* por baixo do título. Isto irá levá-lo ao gitHub repo para as amostras, que você pode baixar como um *. ZIP* selecionando o botão *Código* e *Descarregando ZIP*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Vista para as amostras digitais de gémeos no GitHub. O botão Código é selecionado, produzindo uma pequena caixa de diálogo onde é realçado o botão Download ZIP." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Isto irá descarregar um *. Pasta ZIP* para a sua máquina **comodigital-twins-samples-master.zip**. Desaperte a pasta e extraa os ficheiros.

### <a name="prepare-an-azure-digital-twins-instance"></a>Prepare uma instância Azure Digital Twins

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

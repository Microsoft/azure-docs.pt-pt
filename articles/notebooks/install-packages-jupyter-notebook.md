---
title: Instalar pacotes no Jupyter notebooks – versão prévia do Azure Notebooks
description: Saiba como instalar o Python, o R e F# pacotes de dentro de um notebook Jupyter em execução no Azure.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646233"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Instalar pacotes de dentro do Azure Notebooks versão prévia

Embora seja possível configurar o [ambiente para o bloco de notas no nível de projeto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), pode querer instalar pacotes diretamente dentro de um bloco de notas individual.

Pacotes instalados do bloco de notas aplicam-se apenas para a atual sessão do servidor. Instalações de pacotes não são mantidas depois do servidor está a ser encerrado.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

Pacotes em Python podem ser instalados através do pip ou conda utilizando os comandos dentro das células de código:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Se a saída do comando indica que o requisito é satisfeito já, em seguida, blocos de notas do Azure pode incluir o pacote por predefinição. O pacote também pode ser instalado através de um [o passo de configuração do ambiente de projeto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R.

Os pacotes em R podem ser instalados de CRAN ou GitHub usando a função `install.packages` em uma célula de código:

```r
install.packages("package_name")
```

Você também pode instalar versões de pré-lançamento e outros pacotes de desenvolvimento do GitHub usando a biblioteca devtools:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Pacotes no F# pode ser instalada a partir [nuget.org](https://www.nuget.org) chamando a dependência de Paket manager a partir de dentro das células de código. Primeiro, carregue o Gestor de Paket:

```fsharp
#load "Paket.fsx"
```

Em seguida, instale pacotes:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Em seguida, carregue o gerador de paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Abra a biblioteca:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Passos seguintes

- [Como: configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
- [Como: apresentar uma apresentação de diapositivos](present-jupyter-notebooks-slideshow.md)

---
title: Instale pacotes em cadernos Jupyter - Pré-visualização de cadernos Azure
description: Aprenda a instalar pacotes Python, R e F# de dentro de um caderno Jupyter em execução no Azure.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646233"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Instale pacotes a partir de dentro da Pré-visualização de cadernos Azure

Embora possa configurar o [ambiente para o seu caderno ao nível do projeto,](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)poderá querer instalar pacotes diretamente dentro de um caderno individual.

Os pacotes instalados a partir do caderno aplicam-se apenas à sessão atual do servidor. As instalações do pacote não são persistidas uma vez que o servidor é desligado.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

As embalagens em Python podem ser instaladas utilizando pip ou conda utilizando comandos dentro de células de código:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Se a saída de comando indicar que a exigência já está satisfeita, então os Cadernos Azure podem incluir a embalagem por defeito. O pacote também pode ser instalado através de uma etapa de [configuração](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)do ambiente do projeto .

## <a name="r"></a>R

As embalagens em R podem ser instaladas `install.packages` a partir de CRAN ou GitHub utilizando a função numa célula de código:

```r
install.packages("package_name")
```

Também pode instalar versões pré-lançamento e outros pacotes de desenvolvimento do GitHub utilizando a biblioteca de devtools:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Os pacotes em F# podem ser instalados a partir de [nuget.org,](https://www.nuget.org) ligando para o gestor de dependência paket de dentro de células de código. Primeiro, carregue o gerente do Paket:

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

Em seguida, carregue o gerador Paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Abra a biblioteca:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Passos seguintes

- [Como: Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
- [Como: Apresentar uma apresentação de diapositivos](present-jupyter-notebooks-slideshow.md)

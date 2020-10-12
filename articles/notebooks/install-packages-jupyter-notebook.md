---
title: Instalar pacotes em cadernos Jupyter - Pré-visualização de cadernos Azure
description: Saiba como instalar pacotes Python, R e F# dentro de um caderno Jupyter em execução em Azure.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 43d38c9f3a4b0095967ab3e103ea729ec86ea2bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85831221"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Instalar pacotes a partir de Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Embora possa configurar o [ambiente para o seu caderno ao nível do projeto,](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)é possível que queira instalar pacotes diretamente dentro de um caderno individual.

Os pacotes instalados a partir do portátil aplicam-se apenas à sessão atual do servidor. As instalações dos pacotes não são persistidos quando o servidor é desligado.

## <a name="python"></a>Python

As embalagens em Python podem ser instaladas utilizando pip ou conda utilizando comandos dentro de células de código:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Se a saída do comando indicar que o requisito já está preenchido, então os Cadernos Azure podem incluir o pacote por predefinição. O pacote também pode ser instalado através de um passo de [configuração do ambiente do projeto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

As embalagens em R podem ser instaladas a partir de CRAN ou GitHub utilizando a `install.packages` função numa célula de código:

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

Os pacotes em F# podem ser instalados a partir de [nuget.org,](https://www.nuget.org) chamando o gestor de dependência de Paket de dentro de células de código. Primeiro, carregue o gerente do Paket:

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

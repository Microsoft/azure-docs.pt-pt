---
title: Instalar pacotes num bloco de notas do Jupyter no Azure
description: Como instalar o Python, R, e F# pacotes de dentro de um bloco de notas do Jupyter em execução no Azure.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 5d85c8e936ce7c8bf38ec7bc9c27d9066cc8b155
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277540"
---
# <a name="install-packages-from-within-a-notebook"></a>Instalar pacotes a partir de um bloco de notas

Embora você possa configurar o [ambiente para o seu notebook no nível do projeto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), convém instalar pacotes diretamente em um notebook individual.

Pacotes instalados do bloco de notas aplicam-se apenas para a atual sessão do servidor. Instalações de pacotes não são mantidas depois do servidor está a ser encerrado.

## <a name="python"></a>Python

Pacotes em Python podem ser instalados através do pip ou conda utilizando os comandos dentro das células de código:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Se a saída do comando indica que o requisito é satisfeito já, em seguida, blocos de notas do Azure pode incluir o pacote por predefinição. O pacote também pode ser instalado por meio de uma [etapa de configuração do ambiente de projeto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

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

Os pacotes F# no podem ser instalados de [NuGet.org](https://www.nuget.org) chamando o Gerenciador de dependências do paket de dentro de células de código. Primeiro, carregue o Gestor de Paket:

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

Abra o biblioteca:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Passos seguintes

- [Como: configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
- [Como: apresentar uma apresentação de slides](present-jupyter-notebooks-slideshow.md)

---
title: Adicione e gerencie bibliotecas para Apache Spark em Azure Synapse Analytics
description: Saiba como adicionar e gerir bibliotecas utilizadas pela Apache Spark no Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 80414ccd6d5797614dd15bd61af8f37b3d2be05c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870369"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Adicione e gerencie bibliotecas para Apache Spark em Azure Synapse Analytics

A Apache Spark depende de muitas bibliotecas para fornecer funcionalidade. Estas bibliotecas podem ser aumentadas ou substituídas por bibliotecas adicionais ou versões atualizadas de mais antigas.

Os pacotes Python podem ser adicionados ao nível da piscina Spark (pré-visualização) e os pacotes baseados em .jar podem ser adicionados ao nível da definição de trabalho spark.

## <a name="adding-or-updating-python-libraries"></a>Adicionar ou atualizar bibliotecas Python

A Apache Spark em Azure Synapse Analytics tem uma instalação completa de Anacondas mais bibliotecas adicionais. A lista completa de bibliotecas pode ser encontrada no suporte da [versão Apache Spark.](apache-spark-version-support.md)

Quando uma instância spark começa, um novo ambiente virtual é criado usando esta instalação como base. Além disso, um ficheiro *requirements.txt* (saída do `pip freeze` comando) pode ser usado para atualizar o ambiente virtual. Os pacotes listados neste ficheiro para instalação ou atualização são descarregados da PyPi no momento do arranque do cluster. Este ficheiro de requisitos é usado sempre que uma instância spark é criada a partir dessa piscina Spark.

> [!IMPORTANT]
>
> - Se a embalagem que está a instalar for grande ou demorar muito tempo a instalar, isto afeta o tempo de arranque da instância Spark.
> - Não são suportados pacotes que exijam suporte ao compilador no momento da instalação, como o GCC.
> - Os pacotes não podem ser desclassificados, apenas adicionados ou atualizados.

### <a name="requirements-format"></a>Formato de requisitos

O seguinte corte mostra o formato para o ficheiro de requisitos. O nome do pacote PyPi está listado juntamente com uma versão exata. Este ficheiro segue o formato descrito na documentação de referência do [pip freeze.](https://pip.pypa.io/en/stable/reference/pip_freeze/) Este exemplo pins uma versão específica. Também pode especificar versões "não maiores do que" e "menos do que" neste ficheiro.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Interface de utilizador da biblioteca Python

O UI para adicionar bibliotecas está no separador de **configurações adicionais** da página de **piscina Create Apache Spark** no portal Azure.

Faça upload do ficheiro de configuração do ambiente utilizando o seletor de ficheiros na secção **Pacotes** da página.

![Adicionar bibliotecas Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Adicionar bibliotecas Python")

### <a name="verifying-installed-libraries"></a>Verificação de bibliotecas instaladas

Para verificar se as versões corretas das bibliotecas corretas são instaladas, execute o seguinte código

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação de Faísca apache](https://spark.apache.org/docs/2.4.4/)

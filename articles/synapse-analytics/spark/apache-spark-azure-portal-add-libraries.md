---
title: Adicione e gere bibliotecas para Apache Spark
description: Saiba como adicionar e gerir bibliotecas usadas pela Apache Spark em Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: c0d34d80df77b5c6fcdefc39b3bc3b1619a93705
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496258"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Adicione e gere bibliotecas para Apache Spark em Azure Synapse Analytics

Apache Spark depende de muitas bibliotecas para fornecer funcionalidades. Estas bibliotecas podem ser aumentadas ou substituídas por bibliotecas adicionais ou versões atualizadas das mais antigas.

Os pacotes Python podem ser adicionados ao nível da piscina Spark (pré-visualização) e os pacotes baseados em .jar podem ser adicionados ao nível de definição de trabalho spark.

## <a name="add-or-update-python-libraries"></a>Adicionar ou atualizar bibliotecas Python

Apache Spark in Azure Synapse Analytics tem uma instalação completa de Anacondas mais bibliotecas adicionais. A lista completa de bibliotecas pode ser encontrada no [suporte da versão Apache Spark.](apache-spark-version-support.md)

Quando uma instância Spark começa, um novo ambiente virtual é criado usando esta instalação como base. Além disso, um ficheiro *requirements.txt* (saída do `pip freeze` comando) pode ser usado para melhorar o ambiente virtual. Os pacotes listados neste ficheiro para instalação ou upgrade são descarregados a partir de PyPi no momento do cluster startup. Este ficheiro de requisitos é usado sempre que uma instância Spark é criada a partir dessa piscina Spark.

> [!IMPORTANT]
>
> - Se a embalagem que está a instalar for grande ou demorar muito tempo a ser instalada, isto afeta o tempo de arranque da instância Spark.
> - Os pacotes que requerem suporte do compilador no momento da instalação, como o CCG, não são suportados.
> - Os pacotes não podem ser desclassificados, apenas adicionados ou atualizados.

### <a name="requirements-format"></a>Formato de requisitos

O seguinte corte mostra o formato do ficheiro de requisitos. O nome do pacote PyPi está listado juntamente com uma versão exata. Este ficheiro segue o formato descrito na documentação de referência de congelamento de [pip.](https://pip.pypa.io/en/stable/reference/pip_freeze/) Este exemplo fixa uma versão específica. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Interface de utilizador da biblioteca python

A UI para adicionar bibliotecas está no separador **definições adicionais** da página de **piscina Create Apache Spark** no portal Azure.

Faça o upload do ficheiro de configuração do ambiente utilizando o seletor de ficheiros na secção **Pacotes** da página.

![Adicionar bibliotecas Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Adicionar bibliotecas Python")

### <a name="verify-installed-libraries"></a>Verificar bibliotecas instaladas

Para verificar se as versões corretas das bibliotecas corretas são instaladas, executar o seguinte código

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação de faíscas apache](https://spark.apache.org/docs/2.4.4/)

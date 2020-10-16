---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90709671"
---
## <a name="create-azure-context"></a>Criar contexto azul

Para utilizar comandos Docker para executar contentores em Instâncias de Contentores Azure, primeiro faça login em Azure:

```bash
docker login azure
```

Quando solicitado, insira ou selecione as suas credenciais Azure.


Crie um contexto ACI executando `docker context create aci` . Este contexto associa o Docker a um grupo de subscrição e recursos Azure para que possa criar e gerir casos de contentores. Por exemplo, para criar um contexto chamado *myacicontext*:

```
docker context create aci myacicontext
```

Quando solicitado, selecione o seu ID de subscrição Azure e, em seguida, selecione um grupo de recursos existente ou **crie um novo grupo de recursos**. Se escolher um novo grupo de recursos, é criado com um nome gerado pelo sistema. O Azure Container Instances, como todos os recursos do Azure, tem de ser implementados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.


Corra `docker context ls` para confirmar que adicionou o contexto ACI aos seus contextos Docker:

```
docker context ls
```
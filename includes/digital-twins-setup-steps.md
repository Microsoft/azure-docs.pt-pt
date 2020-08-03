---
author: baanders
description: incluir arquivo para visão geral de passos na configuração de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407527"
---
>[!NOTE]
>Estas operações destinam-se a ser concluídas por um utilizador com uma função *de Proprietário* na subscrição do Azure. Embora algumas peças possam ser concluídas sem esta autorização elevada, a cooperação do proprietário será necessária para configurar completamente um caso utilizável. Veja mais informações sobre esta informação na secção [*Pré-Requisitos: Permissões necessárias*](#prerequisites-permission-requirements) abaixo.

A configuração completa para um novo exemplo de Azure Digital Twins consiste em três partes:
1. **Criar o caso**
2. **Configuração das permissões**de acesso ao utilizador : Os utilizadores do Azure precisam de ter o papel *de Azure Digital Twins Owner (Preview)* na instância Azure Digital Twins para poderem geri-lo e os seus dados. Neste passo, você como Proprietário na subscrição Azure irá atribuir este papel à pessoa que vai gerir a sua instância Azure Digital Twins. Pode ser você ou outra pessoa na sua organização.
3. **Configurar permissões de acesso para aplicações de clientes**: É comum escrever uma aplicação de cliente que será usada para interagir com o seu exemplo. Para que essa aplicação de clientes aceda aos seus Azure Digital Twins, é necessário configurar um *registo de aplicações* no [Azure Ative Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) que a aplicação do cliente utilizará para autenticar o caso.

Para prosseguir, você precisará de uma assinatura Azure. Pode configurar um de graça [aqui.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

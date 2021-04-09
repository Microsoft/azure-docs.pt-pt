---
author: baanders
description: incluir arquivo para visão geral de passos na configuração de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560794"
---
A configuração completa para um novo exemplo de Azure Digital Twins consiste em duas partes:
1. **Criar o caso**
2. **Configuração das permissões** de acesso ao utilizador : Os utilizadores do Azure precisam de ter a função *de Proprietário de Dados Azure Digital Twins* na instância Azure Digital Twins para poderem geri-lo e os seus dados. Neste passo, você como Proprietário/administrador da subscrição Azure irá atribuir esta função à pessoa que irá gerir a sua instância Azure Digital Twins. Pode ser você ou outra pessoa na sua organização.
 
>[!NOTE]
>Estas operações destinam-se a ser concluídas por um utilizador com o poder de gerir tanto os recursos como o acesso do utilizador na subscrição do Azure. Embora alguns passos possam ser concluídos com permissões mais baixas, a cooperação de alguém com estas permissões será necessária para configurar completamente um caso utilizável. Veja mais informações sobre esta informação na secção [*Pré-Requisitos: Permissões necessárias*](#prerequisites-permission-requirements) abaixo.
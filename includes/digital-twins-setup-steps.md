---
author: baanders
description: incluir arquivo para visão geral de passos na configuração de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: bf442da43fd7945bf69fbb889ef0c517b8832809
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478879"
---
>[!NOTE]
>Estas operações destinam-se a ser concluídas por um utilizador com o poder de gerir tanto os recursos como o acesso do utilizador na subscrição do Azure. Embora alguns passos possam ser concluídos com permissões mais baixas, a cooperação de alguém com estas permissões será necessária para configurar completamente um caso utilizável. Veja mais informações sobre esta informação na secção [*Pré-Requisitos: Permissões necessárias*](#prerequisites-permission-requirements) abaixo.

A configuração completa para um novo exemplo de Azure Digital Twins consiste em duas partes:
1. **Criar o caso**
2. **Configuração das permissões**de acesso ao utilizador : Os utilizadores do Azure precisam de ter a função *de Proprietário de Dados Azure Digital Twins* na instância Azure Digital Twins para poderem geri-lo e os seus dados. Neste passo, você como Proprietário/administrador da subscrição Azure irá atribuir esta função à pessoa que irá gerir a sua instância Azure Digital Twins. Pode ser você ou outra pessoa na sua organização.

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]
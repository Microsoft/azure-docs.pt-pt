---
author: baanders
description: incluir arquivo para visão geral de passos na configuração de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 5ab6812d144122c61018ad740892db869a7ba43d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205502"
---
>[!NOTE]
>Estas operações destinam-se a ser concluídas por um utilizador com o poder de gerir tanto os recursos como o acesso do utilizador na subscrição do Azure. Embora alguns passos possam ser concluídos com permissões mais baixas, a cooperação de alguém com estas permissões será necessária para configurar completamente um caso utilizável. Veja mais informações sobre esta informação na secção [*Pré-Requisitos: Permissões necessárias*](#prerequisites-permission-requirements) abaixo.

A configuração completa para um novo exemplo de Azure Digital Twins consiste em duas partes:
1. **Criar o caso**
2. **Configuração das permissões**de acesso ao utilizador : Os utilizadores do Azure precisam de ter o papel *de Azure Digital Twins Owner (Preview)* na instância Azure Digital Twins para poderem geri-lo e os seus dados. Neste passo, você como Proprietário/administrador da subscrição Azure irá atribuir esta função à pessoa que irá gerir a sua instância Azure Digital Twins. Pode ser você ou outra pessoa na sua organização.
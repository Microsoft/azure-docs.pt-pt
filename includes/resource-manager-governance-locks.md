---
title: incluir ficheiro
description: incluir ficheiro
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825088"
---
Bloqueios de recursos impedem os utilizadores na sua organização acidentalmente eliminem ou modifiquem recursos críticos. Ao contrário do controlo de acesso baseado em funções, os bloqueios de recursos aplicam uma restrição a todos os utilizadores e a todas as funções. 

Pode definir o nível do bloqueio para **CanNotDelete** ou **ReadOnly**. No portal, os níveis de bloqueios são exibidos como **elimine** e **só de leitura** , respetivamente.

* **CanNotDelete** significa que os utilizadores autorizados ainda podem ler e modificar um recurso, mas eles não é possível eliminar o recurso. 
* **Só de leitura** significa que os utilizadores autorizados podem ler um recurso, mas não é possível eliminar ou atualizar o recurso. Aplicar esse bloqueio é semelhante ao restringir todos os utilizadores autorizados para as permissões concedidas pela **leitor** função. 

> [!TIP]
> Tenha cuidado ao aplicar um **só de leitura** bloqueio. Algumas operações que parecem ler operações, na verdade, exigem ações adicionais. Por exemplo, um **só de leitura** bloqueio numa conta de armazenamento impede que todos os utilizadores de listar as chaves. A lista de operação de chaves é tratada por meio de um pedido POST porque as chaves retornadas estão disponíveis para operações de escrita. R **só de leitura** bloqueio num recurso de serviço de aplicações impede que o Visual Studio Server Explorer de apresentar ficheiros para o recurso porque essa interação requer acesso de escrita.

Quando aplica um bloqueio num âmbito principal, todos os recursos dentro daquele escopo herdam o mesmo bloqueio. Recursos até mesmo que adicionar posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança tem precedência.

Bloqueios de Gestor de recursos aplicam-se apenas às operações que ocorrem no plano de gestão, que consiste em operações enviadas para `https://management.azure.com`. Os bloqueios não restringem como recursos de processam de suas próprias funções. As alterações de recursos são restritas, mas as operações de recursos não restritas. Por exemplo, um bloqueio só de leitura numa base de dados SQL impede-eliminem ou modifiquem o banco de dados. Ele não impede de criar, atualizar ou eliminar dados na base de dados. As transações de dados são permitidas porque essas operações não são enviadas para `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quem pode criar ou eliminar bloqueios na sua organização
Para criar ou eliminar bloqueios de gestão, tem de ter acesso a ações `Microsoft.Authorization/locks/*`. Das funções incorporadas, apenas **Proprietário** e **Administrador de Acesso dos Utilizadores** têm acesso a essas ações.

---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017692"
---
Utilize sempre [os grupos de segurança Azure AD](../articles/active-directory/fundamentals/active-directory-manage-groups.md) como o principal designado numa entrada ACL. Resista à oportunidade de atribuir diretamente aos utilizadores individuais ou aos principais de serviço. A utilização desta estrutura permitir-lhe-á adicionar e remover utilizadores ou diretores de serviço sem a necessidade de reaplicar ACLs a toda uma estrutura de diretório. Em vez disso, pode apenas adicionar ou remover utilizadores e principais de serviço do grupo de segurança Azure AD apropriado. 

Há muitas maneiras diferentes de criar grupos. Por exemplo, imagine que tem um diretório chamado **/LogData** que contém dados de registo que são gerados pelo seu servidor. A Azure Data Factory (ADF) ingere dados nessa pasta. Utilizadores específicos da equipa de engenharia de serviços irão carregar registos e gerir outros utilizadores desta pasta, e vários clusters databricks irão analisar registos dessa pasta. 

Para ativar estas atividades, pode criar um `LogsWriter` grupo e um `LogsReader` grupo. Em seguida, pode atribuir permissões da seguinte forma:

- Adicione o `LogsWriter` grupo à ACL do diretório **/LogData** com `rwx` permissões.
- Adicione o `LogsReader` grupo à ACL do diretório **/LogData** com `r-x` permissões.
- Adicione o objeto principal de serviço ou identidade de serviço gerido (MSI) para ADF ao `LogsWriters` grupo.
- Adicione os utilizadores na equipa de engenharia de serviços ao `LogsWriter` grupo.
- Adicione o objeto principal de serviço ou MSI para databricks ao `LogsReader` grupo.

Se um utilizador da equipa de engenharia de serviços deixar a empresa, pode simplesmente removê-los do `LogsWriter` grupo. Se não adicionou esse utilizador a um grupo, mas em vez disso, adicionou uma entrada ACL dedicada para esse utilizador, teria de remover essa entrada ACL do diretório **/LogData.** Também teria de remover a entrada de todas as subdireções e ficheiros em toda a hierarquia do diretório do diretório **/LogData.** 

Para criar um grupo e adicionar membros, consulte [criar um grupo básico e adicionar membros usando o Azure Ative Directory](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md).
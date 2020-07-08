---
title: Criar grupos IP em Azure Firewall
description: Os Grupos IP permitem-lhe agrupar e gerir endereços IP para as regras do Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85602538"
---
# <a name="create-ip-groups"></a>Criar Grupos IP

Os Grupos IP permitem-lhe agrupar e gerir endereços IP para as regras do Azure Firewall. Podem ter um único endereço IP, vários endereços IP ou um ou mais intervalos de endereço IP.

## <a name="create-an-ip-group"></a>Criar um Grupo IP

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Digite **grupos IP** na caixa de texto de pesquisa e, em seguida, selecione **Grupos IP**.
3. Selecione **Criar**.
4. Selecione a sua subscrição.
5. Selecione um grupo de recursos ou crie um novo.
6. Digite um nome único para si, Grupo IP, e, em seguida, selecione uma região.

6. Selecione **Seguinte: endereços IP**.
7. Digite um endereço IP, vários endereços IP ou intervalos de endereço IP.

   Existem duas formas de introduzir endereços IP:
   - Pode inseri-los manualmente
   - Pode importá-los de um ficheiro

   Para importar de um ficheiro, **selecione Import from a file**. Pode arrastar o seu ficheiro para a caixa ou selecionar **procurar ficheiros**. Se necessário, pode rever e editar os seus endereços IP carregados.

   Quando digita um endereço IP, o portal valida-o para verificar se há problemas de sobreposição, duplicação e formatação.

5. Quando terminar, selecione **Review + Create**.
6. Selecione **Criar**.


## <a name="next-steps"></a>Próximos passos

- [Saiba mais sobre grupos IP](ip-groups.md)
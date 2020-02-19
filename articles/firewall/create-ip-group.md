---
title: Criar grupos IP em Firewall Azure
description: Os Grupos IP permitem-lhe agrupar e gerir endereços IP para as regras da Firewall Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444540"
---
# <a name="create-ip-groups-preview"></a>Criar grupos IP (pré-visualização)

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Os Grupos IP permitem-lhe agrupar e gerir endereços IP para as regras da Firewall Azure. Podem ter um único endereço IP, vários endereços IP ou uma ou mais gamas de endereços IP.

## <a name="create-an-ip-group"></a>Criar um Grupo IP

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Digite **grupos IP** na caixa de texto de pesquisa e, em seguida, selecione **Grupos IP**.
3. Selecione **Criar**.
4. Selecione a sua subscrição.
5. Selecione um grupo de recursos ou crie um novo.
6. Digite um nome único para si ip Group e, em seguida, selecione uma região.

6. Selecione **Seguinte: endereços IP**.
7. Digite um endereço IP, vários endereços IP ou intervalos de endereçoIP.

   Existem duas formas de introduzir endereços IP:
   - Pode inscrevê-los manualmente
   - Pode importá-los de um ficheiro.

   Para importar de um ficheiro, selecione **Import a partir de um ficheiro**. Pode arrastar o ficheiro para a caixa ou selecionar **Procurar ficheiros**. Se necessário, pode rever e editar os seus endereços IP enviados.

   Ao escrever um endereço IP, o portal valida-o para verificar se há questões sobrepostas, duplicadas e formatação.

5. Quando terminar, selecione **Review + Create**.
6. Selecione **Criar**.


## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre grupos IP](ip-groups.md)
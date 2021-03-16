---
title: Utilize políticas de acesso em Cache Azure HPC
description: Como criar e aplicar políticas de acesso personalizado para limitar o acesso do cliente a alvos de armazenamento em Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: eb9e71cc8ec463077e3b12b8738203a4945a2eab
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471789"
---
# <a name="control-client-access"></a>Controlar o acesso ao cliente

Este artigo explica como criar e aplicar políticas de acesso personalizado ao cliente para os seus alvos de armazenamento.

As políticas de acesso ao cliente controlam a forma como os clientes são autorizados a ligar-se às exportações-alvo de armazenamento. Você pode controlar coisas como squash raiz e ler/escrever acesso ao anfitrião do cliente ou nível de rede.

As políticas de acesso são aplicadas a uma via de espaço de nome, o que significa que você pode usar diferentes políticas de acesso para duas exportações diferentes em um sistema de armazenamento NFS.

Esta funcionalidade destina-se a fluxos de trabalho onde é necessário controlar a forma como diferentes grupos de clientes acedem aos alvos de armazenamento.

Se não precisar de controlo fino sobre o acesso ao alvo de armazenamento, pode utilizar a política predefinida, ou pode personalizar a política predefinida com regras extra. Por exemplo, se pretender ativar a squash de raiz para todos os clientes que se ligam através da cache, pode editar a política nomeada **por defeito** para adicionar a definição de abóbora raiz.

## <a name="create-a-client-access-policy"></a>Criar uma política de acesso ao cliente

Utilize a página de políticas de acesso ao **Cliente** no portal Azure para criar e gerir políticas. <!-- is there AZ CLI for this? -->

[![screenshot da página de políticas de acesso ao cliente. Várias políticas são definidas, e algumas são expandidas para mostrar as suas regras](media/policies-overview.png)](media/policies-overview.png#lightbox)

Cada política é feita de regras. As regras são aplicadas aos anfitriões de forma a ir do âmbito mais pequeno (hospedeiro) ao maior (padrão). A primeira regra que corresponde é aplicada e as regras posteriores são ignoradas.

Para criar uma nova política de acesso, clique no botão **de política de acesso + adicionar** no topo da lista. Dê um nome à nova política de acesso e introduza pelo menos uma regra.

![screenshot de políticas de acesso editar lâmina com várias regras preenchidas. Clique em ok para guardar a regra.](media/add-policy.png)

O resto desta secção explica os valores que pode utilizar nas regras.

### <a name="scope"></a>Âmbito

O termo de âmbito e o filtro de endereço trabalham em conjunto para definir quais os clientes que são afetados pela regra.

Utilize-os para especificar se a regra se aplica a um cliente individual (anfitrião), a uma série de endereços IP (rede) ou a todos os clientes (predefinido).

Selecione o valor de **âmbito** apropriado para a sua regra:

* **Anfitrião** - A regra aplica-se a um cliente individual
* **Rede** - A regra aplica-se aos clientes numa série de endereços IP
* **Padrão** - A regra aplica-se a todos os clientes.

As regras de uma política são avaliadas por esta ordem. Depois de um pedido de montagem de clientes corresponder a uma regra, os outros são ignorados.

### <a name="address-filter"></a>Filtro de endereço

O valor **do filtro Address** especifica quais os clientes que correspondem à regra.

Se definir o âmbito de aplicação do **anfitrião,** pode especificar apenas um endereço IP no filtro. Para a definição de âmbito **predefinido,** não é possível introduzir quaisquer endereços IP no campo **do filtro Address** porque o âmbito padrão corresponde a todos os clientes.

Especifique o endereço IP ou o intervalo de endereços para esta regra. Utilize a notação CIDR (exemplo: 0.1.0.0/16) para especificar um intervalo de endereços.

### <a name="access-level"></a>Nível de acesso

Desa estada quais os privilégios para conceder aos clientes que correspondam ao âmbito e ao filtro.

As opções são **lidas/escritas,** **apenas de leitura,** ou **sem acesso**.

### <a name="suid"></a>SUID

Verifique a caixa **SUID** para permitir que os ficheiros armazenados desemtiotem os IDs do utilizador no acesso.

O SUID é normalmente utilizado para aumentar temporariamente os privilégios de um utilizador para que o utilizador possa realizar uma tarefa relacionada com esse ficheiro.

### <a name="submount-access"></a>Acesso submontado

Verifique esta caixa para permitir que os clientes especificados montem diretamente as subdiretórios desta exportação.

### <a name="root-squash"></a>Abóbora de raiz

Escolha se deve ou não definir a squash de raiz para clientes que correspondam a esta regra.

Esta definição controla a forma como a Azure HPC Cache trata os pedidos do utilizador raiz nas máquinas do cliente. Quando a abóbora raiz está ativada, os utilizadores de raiz de um cliente são automaticamente mapeados para um utilizador não privilegiado quando enviam pedidos através da Cache Azure HPC. Também impede que os pedidos do cliente utilizem bits de permissão set-UID.

Se a abóbora raiz for desativada, um pedido do utilizador raiz do cliente (UID 0) é transmitido para um sistema de armazenamento NFS de back-end como raiz. Esta configuração pode permitir o acesso inadequado ao ficheiro.

Definir a radiada para os pedidos dos clientes pode ajudar a compensar a configuração necessária ``no_root_squash`` nos sistemas NAS que são usados como alvos de armazenamento. (Ler mais sobre [os pré-requisitos do alvo de armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements).) Também pode melhorar a segurança quando usado com alvos de armazenamento Azure Blob.

Se ligar squash de raiz, também deve definir o valor de id anónimo do utilizador. O portal aceita valores inteiros entre 0 e 4294967295. (Os valores antigos -2 e -1 são suportados para retrocompatibilidade, mas não recomendados para novas configurações.)

Estes valores são mapeados para valores específicos do utilizador:

* **-2** ou **65534** (ninguém)
* **-1** ou **65535** (sem acesso)
* **0** (raiz desfavorecida)

O seu sistema de armazenamento pode ter outros valores com significados especiais.

## <a name="update-access-policies"></a>Atualizar políticas de acesso

Pode editar ou eliminar as políticas de acesso da tabela na página políticas de acesso ao **Cliente.**

Clique no nome da apólice para abri-lo para edição.

Para eliminar uma apólice, marque a caixa de verificação ao lado do seu nome na lista e, em seguida, clique no botão **Eliminar** no topo da lista. Não é possível apagar a política denominada "padrão".

> [!NOTE]
> Não é possível apagar uma política de acesso que esteja em uso. Remova a política de quaisquer caminhos do espaço de nome que a incluam antes de tentar eliminá-la.

## <a name="next-steps"></a>Passos seguintes

* Aplique políticas de acesso nos caminhos do espaço de nome para os seus alvos de armazenamento. Leia [Configurar o espaço de nome agregado](add-namespace-paths.md) para saber como.

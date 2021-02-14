---
title: Modificar as definições de destino ao mover VMs Azure entre regiões com Azure Resource Mover
description: Saiba como modificar as definições do destino ao mover VMs Azure entre regiões com Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 936a667948c888f3ca7c53eaa5be9cc97facf5f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375362"
---
# <a name="modify-destination-settings"></a>Modificar as definições de destino

Este artigo descreve como modificar as definições de destino, ao mover recursos entre as regiões de Azure com [o Azure Resource Mover](overview.md).


## <a name="modify-vm-settings"></a>Modificar as definições de VM

Ao mover VMs Azure e recursos associados, pode modificar as definições do destino. 

- Recomendamos que altere apenas as definições de destino após a validação da recolha do movimento.
- Recomendamos que modifique as definições antes de preparar os recursos, porque algumas propriedades de destino podem não estar disponíveis para edição após a preparação estar concluída.

No entanto:
- Se estiver a mover o recurso de origem, normalmente pode modificar as definições do destino até iniciar o processo de movimento iniciado.
- Se atribuir um recurso existente na região de origem, pode modificar as definições do destino até que o compromisso de movimento esteja completo.

### <a name="settings-you-can-modify"></a>Definições que pode modificar

As definições de configuração que pode modificar são resumidas na tabela.

**Recurso** | **Opções** 
--- | --- | --- 
**o nome da VM** | Opções:<br/><br/> - Criar um novo VM com o mesmo nome na região de destino.<br/><br/> - Criar um novo VM com um nome diferente na região de destino.<br/><br/> - Utilize um VM existente na região de destino.<br/><br/> Se criar um novo VM, com exceção das definições modificadas, o novo VM de destino é atribuído às mesmas definições que a fonte.
**Zona de disponibilidade de VM** | A zona de disponibilidade em que será colocado o VM de destino. Selecione **Não aplicável** se não quiser alterar as definições de origem, ou se não quiser colocar o VM numa zona de disponibilidade.
**SKU da VM** | O [tipo VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (disponível na região de destino) que será utilizado para o destino VM.<br/><br/> O VM de destino selecionado não deve ser menor do que a fonte VM.
Conjunto de disponibilidade de **VM | A disponibilidade definida em que o VM de destino será colocado. **Selecione Não é aplicável** não pretende alterar as definições de origem, ou se não quiser colocar o VM num conjunto de disponibilidade.
**Cofre de chaves VM** | O cofre de chaves associado quando ativa a encriptação do disco Azure num VM.
**Conjunto de encriptação de disco** | O conjunto de encriptação do disco associado se o VM utilizar uma chave gerida pelo cliente para encriptação do lado do servidor.
**Grupo de recursos** | O grupo de recursos no qual será colocado o VM de destino.
**Recursos de rede** | Opções para interfaces de rede, redes virtuais (VNets/) e grupos de segurança de rede/interfaces de rede:<br/><br/> - Criar um novo recurso com o mesmo nome na região de destino.<br/><br/> - Criar um novo recurso com um nome diferente na região de destino.<br/><br/> - Utilize um recurso de rede existente na região de destino.<br/><br/> Se criar um novo recurso de destino, com exceção das definições modificadas, é atribuído às mesmas definições que o recurso de origem.
**Nome de endereço IP público, SKU e zona** | Especifica o nome, [SKU,](../virtual-network/public-ip-addresses.md#sku)e [zona](../virtual-network/public-ip-addresses.md#standard) para endereços IP públicos padrão.<br/><br/> Se quiser que seja uma zona redundante, entre como **Zona redundante.**
**Nome do balançador de carga, SKU, e zona ** | Especifica o nome, SKU (Básico ou Standard) e zona para o equilibrador de carga.<br/><br/> Recomendamos a utilização de SKU Standard.<br/><br/> Se quiser que seja uma zona redundante, especifique como **Zona redundante.**
**Dependências de recursos** | Opções para cada dependência:<br/><br/>- O recurso utiliza recursos dependentes de origem que se deslocarão para a região de destino.<br/><br/> - O recurso utiliza diferentes recursos dependentes localizados na região de destino. Neste caso, pode escolher entre quaisquer recursos semelhantes na região de destino.

### <a name="edit-vm-destination-settings"></a>Editar definições de destino VM

Se não quiser depender recursos da região de origem para o destino, tem algumas outras opções:

- Criar um novo recurso na região de destino. A menos que especifique diferentes definições, o novo recurso terá as mesmas definições que o recurso de origem.
- Utilize um recurso existente na região de destino.

O comportamento exato depende do tipo de recurso. [Saiba mais](modify-target-settings.md) sobre a modificação das definições do destino.

Modifica as definições de destino para um recurso utilizando a entrada de **configuração destino** na coleção de movimento de recursos. 

Para modificar uma definição: 

1. Na página **de regiões >** coluna **de configuração destino,** clique no link para a entrada de recursos.
2. Nas **definições de Configuração,** pode criar um novo VM na região de destino.
3. Atribua uma nova zona de disponibilidade, conjunto de disponibilidade ou SKU para o VM de destino. **Zona de disponibilidade** e **SKU.**

As alterações são feitas apenas para o recurso que está a editar. Tem de atualizar qualquer recurso dependente separadamente.


## <a name="modify-sql-settings"></a>Modificar as definições SQL

Ao mover os recursos da Base de Dados Azure SQL, pode modificar as definições de destino para a mudança. 

- Para a base de dados SQL:
    - Recomendamos que modifique as definições de configuração do destino antes de as preparar para se moverem.
    - Pode modificar as definições para a base de dados de destino e reduzir a zona para a base de dados.
- Para piscinas elásticas:
    -  Pode modificar a configuração do destino a qualquer momento antes de iniciar o movimento.
    - Você pode modificar a piscina elástica de destino, e redundância de zona para a piscina. 

### <a name="sql-settings-you-can-modify"></a>Definições SQL que pode modificar

**Definição** | **Base de dados SQL** | **Conjunto elástico**
--- | --- | ---
**Nome** | Criar uma nova base de dados com o mesmo nome na região de destino.<br/><br/> Criar uma nova base de dados com um nome diferente na região de destino.<br/><br/> Utilize uma base de dados existente na região de destino. | Crie uma nova piscina elástica com o mesmo nome na região de destino.<br/><br/> Crie uma nova piscina elástica com um nome diferente na região de destino.<br/><br/> Utilize uma piscina elástica existente na região de destino.
**Redundância entre zonas** | Passar de uma região que suporta a redundância de zona para uma região que não o faça, **digite Desativar** na zona.<br/><br/> Para passar de uma região que não suporta a redundância da zona para uma região que o faça, **digite Enable** na definição de zona. | Passar de uma região que suporta a redundância de zona para uma região que não o faça, **digite Desativar** na zona.<br/><br/> Para passar de uma região que não suporta a redundância da zona para uma região que o faça, **digite Enable** na definição de zona.

### <a name="edit-sql-destination-settings"></a>Editar definições de destino SQL

Modifica as definições de destino para um recurso Azure SQL Database da seguinte forma: 

1. Em **Regiões ,** para o recurso que pretende modificar, clique na entrada de **configuração destino.**
2. Nas **definições de configuração**, especifique as definições de destino resumidas na tabela acima.

## <a name="next-steps"></a>Passos seguintes

[Mova um VM Azure](tutorial-move-region-virtual-machines.md) para outra região.

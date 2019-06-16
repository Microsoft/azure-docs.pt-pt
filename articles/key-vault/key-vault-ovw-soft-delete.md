---
title: Eliminação de forma recuperável de Cofre de chaves do Azure | Documentos da Microsoft
ms.service: key-vault
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/19/2019
ms.openlocfilehash: 330337620f1732b9ccecfb2c95a0b4495476f97b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720522"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Descrição geral da eliminação de forma recuperável de Cofre de chaves do Azure

Funcionalidade de eliminação de forma recuperável do Key Vault permite que a recuperação dos cofres eliminados e objetos de cofre, conhecidos como eliminação de forma recuperável. Especificamente, vamos abordar os seguintes cenários:

- Suporte para eliminação recuperável de um cofre de chaves
- Suporte para eliminação recuperável de objetos de Cofre de chaves (ex. as chaves, segredos e certificados)

## <a name="supporting-interfaces"></a>Interfaces de suporte

A funcionalidade de eliminação de forma recuperável está inicialmente disponível através da [REST](/rest/api/keyvault/), [CLI](key-vault-soft-delete-cli.md), [PowerShell](key-vault-soft-delete-powershell.md) e [.NET /C# ](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) interfaces.

## <a name="scenarios"></a>Cenários

O Azure Key Vaults são recursos controlados, geridos pelo Azure Resource Manager. O Azure Resource Manager também especifica um comportamento bem definido para eliminação, que requer que uma operação de eliminação com êxito deve resultar nesse recurso não deixa de poder a estar acessível. A funcionalidade de eliminação de forma recuperável aborda a recuperação do objeto excluído, se a eliminação foi acidental ou intencional.

1. O cenário típico, um utilizador pode ter acidentalmente eliminado um cofre de chaves ou de um objeto do Cofre de chaves; se que cofre ou chave de objeto do Cofre de chaves fosse ser recuperada por um período predeterminado, o utilizador pode anular a eliminação e recuperar os seus dados.

2. Num cenário diferente, um utilizador não autorizado pode tentar eliminar um cofre de chaves ou de um objeto de Cofre de chaves, como uma chave no interior de um cofre, para fazer com que uma interrupção do negócio. Separar a eliminação do Cofre de chaves ou de objeto do Cofre de chaves a eliminação real dos dados subjacentes pode ser utilizado como uma medida de segurança, por exemplo, restringindo permissões de eliminação de dados para outro, fidedigno função. Essa abordagem requer efetivamente quórum para uma operação que caso contrário, poderá resultar numa perda de dados de imediato.

### <a name="soft-delete-behavior"></a>Comportamento de eliminação de forma recuperável

Com esta funcionalidade, a operação de eliminação num cofre de chaves ou o objeto do Cofre de chaves é uma eliminação de forma recuperável, com eficiência que contém os recursos para um período de retenção especificado (90 dias), dando a aparência que o objeto é eliminado. O serviço ainda mais fornece um mecanismo para recuperar o objeto excluído, desfazendo, essencialmente, a eliminação. 

É um comportamento opcional do Cofre de chaves de eliminação de forma recuperável sendo **não ativada por predefinição** nesta versão. Ele pode ser ativado através de [CLI](key-vault-soft-delete-cli.md) ou [Powershell](key-vault-soft-delete-powershell.md).

### <a name="purge-protection"></a>Remover proteção 

Quando remover a proteção está num cofre ou não é possível limpar um objeto no estado de eliminado, até que tenha passado o período de retenção de 90 dias. Estes cofres e os objetos podem ainda ser recuperados, os clientes de garantir que a política de retenção será seguida. 

Remover a proteção é um comportamento opcional do Key Vault sendo **não ativada por predefinição**. Ele pode ser ativado através de [CLI](key-vault-soft-delete-cli.md#enabling-purge-protection) ou [Powershell](key-vault-soft-delete-powershell.md#enabling-purge-protection).

### <a name="permitted-purge"></a>Remoção permitida

Permanentemente a eliminar, remoção, um cofre de chaves é possível através de uma operação de publicação do recurso de proxy e exija privilégios especiais. Em geral, apenas o proprietário da subscrição será capaz de remover um cofre de chaves. A operação de publicação aciona a eliminação de imediata e irrecuperável desse cofre. 

As exceções são:
- Quando a subscrição do Azure foi marcada como *undeletable*. Neste caso, apenas o serviço, em seguida, pode executar a eliminação real e faz isso como um processo agendado. 
- Quando a-- enable--proteção contra remoção sinalizador estiver ativado no próprio cofre. Neste caso, o Key Vault aguardará durante 90 dias do quando o objeto secreto original foi marcado para eliminação eliminar permanentemente o objeto.

### <a name="key-vault-recovery"></a>Recuperação de Cofre de chaves

Após eliminar um cofre de chaves, o serviço cria um recurso de proxy na subscrição, adicionar metadados suficiente para a recuperação. O recurso de proxy é um objeto armazenado, disponível na mesma localização que o Cofre de chaves eliminado. 

### <a name="key-vault-object-recovery"></a>Recuperação de objeto do Cofre de chaves

Após a eliminação de um objeto de Cofre de chaves, como uma chave, o serviço irá colocar o objeto num Estado eliminado, tornando inacessível para quaisquer operações de obtenção. Enquanto estiver neste estado, o objeto do Cofre de chaves pode apenas ser apresentado, recuperados ou forçadamente/permanentemente eliminado. 

Ao mesmo tempo, o Key Vault agendará a eliminação dos dados subjacentes correspondente para o Cofre de chaves eliminado ou o objeto do Cofre de chaves para execução após um intervalo de retenção predeterminado. O registo DNS correspondentes para o Cofre também é retido durante o intervalo de retenção.

### <a name="soft-delete-retention-period"></a>Período de retenção de eliminação de forma recuperável

Recursos de eliminado de forma recuperável são retidos durante um determinado período de tempo, 90 dias. Durante o intervalo de retenção de eliminação de forma recuperável, situações seguintes:

- Pode listar todos os cofres de chaves e os objetos de Cofre de chaves no estado de eliminação de forma recuperável para a sua subscrição, bem como informações de recuperação e eliminação de acesso sobre eles.
    - Apenas os utilizadores com permissões especiais podem listar os cofres eliminados. Recomendamos que nossos usuários criar uma função personalizada com estas permissões especiais para os cofres de manipulação de eliminado.
- Não é possível criar um cofre de chaves com o mesmo nome na mesma localização; do mesmo modo, um objeto do Cofre de chaves não é possível criar um cofre de determinado se o Cofre de chaves contém um objeto com o mesmo nome e o que está num Estado eliminado 
- Apenas um utilizador especificamente com privilégios pode restaurar um cofre de chaves ou o objeto do Cofre de chaves ao emitir um comando de recuperar o recurso de proxy correspondente.
    - O utilizador, o membro da função personalizada, que tem o privilégio de criar um cofre de chaves no grupo de recursos pode restaurar o cofre.
- Apenas um utilizador especificamente com privilégios a forçar pode eliminar um cofre de chaves ou o objeto do Cofre de chaves ao emitir um comando de eliminação do recurso de proxy correspondente.

A menos que um cofre de chaves ou o objeto do Cofre de chaves é recuperado, no final do intervalo de retenção, o serviço executa uma limpeza do Cofre de chaves eliminado de forma recuperável ou o objeto do Cofre de chaves e o respetivo conteúdo. Eliminação do recurso não pode ser reagendada.

### <a name="billing-implications"></a>Implicações de faturação

Em geral, quando um objeto (um cofre de chaves ou uma chave ou um segredo) está no Estado eliminado, existem apenas duas operações possíveis: "Remover" e 'recover'. Todas as outras operações irão falhar. Por conseguinte, mesmo que o objeto existe, não operações podem ser efetuadas e, por conseguinte, não irá ocorrer nenhuma utilização, assim, nenhuma fatura. No entanto, está a seguir exceções:

- ações "Remover" e 'recover' contam para operações do Cofre de chaves normal e serão cobradas.
- Se o objeto é uma chave de HSM, a cobrança de "HSM para chave protegida por" por versão da chave encargo mensal será aplicada se uma versão da chave tiver sido utilizada nos últimos 30 dias. Depois disso, uma vez que o objeto está num Estado eliminado, não existem operações podem ser executadas em relação a ele, então, sem custos serão aplicadas.

## <a name="next-steps"></a>Passos Seguintes

Os seguintes guias de dois oferecem os cenários de utilização principal para utilizar a eliminação de forma recuperável.

- [Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell](key-vault-soft-delete-powershell.md) 
- [Como utilizar a eliminação de forma recuperável do Key Vault com a CLI](key-vault-soft-delete-cli.md)


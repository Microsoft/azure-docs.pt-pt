---
title: Azure Key Vault soft-delete / Microsoft Docs
description: A eliminação suave no Cofre da Chave Azure permite-lhe recuperar cofres de chaves apagados e objetos-chave do cofre, tais como chaves, segredos e certificados.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: dd8f8e3ad1fe94f61f7c3d725f356b174a2b569c
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372132"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Descrição geral da eliminação recuperável do Azure Key Vault

A função de eliminação suave do Key Vault permite a recuperação dos cofres apagados e dos objetos do cofre, conhecidos como soft-delete. Especificamente, abordamos os seguintes cenários:

- Suporte para a supressão recuperável de um cofre chave
- Suporte para eliminação recuperável de objectos-chave do cofre (ex. chaves, segredos, certificados)

## <a name="supporting-interfaces"></a>Interfaces de apoio

A função soft-delete está inicialmente disponível através das interfaces [REST](/rest/api/keyvault/), [CLI,](soft-delete-cli.md) [PowerShell](soft-delete-powershell.md)e [.NET/C#,](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) bem como [modelos ARM](https://docs.microsoft.com/azure/templates/microsoft.keyvault/2019-09-01/vaults).

## <a name="scenarios"></a>Cenários

Os Cofres chave Azure são rastreados recursos, geridos pelo Azure Resource Manager. O Azure Resource Manager também especifica um comportamento bem definido para a eliminação, o que requer que uma operação de DELETE bem sucedida resulte em que esse recurso não seja mais acessível. A função de eliminação suave aborda a recuperação do objeto eliminado, quer a eliminação tenha sido acidental ou intencional.

1. No cenário típico, um utilizador pode ter apagado inadvertidamente um cofre de chaves ou um objeto de cofre chave; se o cofre de chaves ou o objeto do cofre chave forem recuperáveis por um período pré-determinado, o utilizador pode desfazer a eliminação e recuperar os seus dados.

2. Num cenário diferente, um utilizador desonesto pode tentar apagar um cofre ou um objeto de cofre chave, como uma chave dentro de um cofre, para causar uma perturbação do negócio. A separação do cofre-chave ou do objeto do cofre-chave da eliminação real dos dados subjacentes pode ser usada como medida de segurança, limitando, por exemplo, as permissões na eliminação de dados a um papel diferente e fidedigno. Esta abordagem requer efetivamente quórum para uma operação que, de outra forma, poderia resultar numa perda imediata de dados.

### <a name="soft-delete-behavior"></a>Comportamento de eliminação suave

Quando a eliminação suave é ativada, os recursos marcados como recursos eliminados são mantidos por um período especificado (90 dias por padrão). O serviço fornece ainda um mecanismo para a recuperação do objeto eliminado, essencialmente desfazendo a supressão.

Ao criar um novo cofre de chave, o soft-delete é ligado por defeito. Pode criar um cofre de chaves sem eliminar suavemente através do [Azure CLI](soft-delete-cli.md) ou [da Azure PowerShell](soft-delete-powershell.md). Uma vez que a eliminação suave é ativada num cofre de teclas, não pode ser desativada

O período de retenção predefinido é de 90 dias mas, durante a criação do cofre chave, é possível definir o intervalo de política de retenção para um valor de 7 a 90 dias através do portal Azure. A política de proteção da purga utiliza o mesmo intervalo. Uma vez definido, o intervalo da política de retenção não pode ser alterado.

Não é possível reutilizar o nome de um cofre-chave que foi apagado até que o período de retenção tenha passado.

### <a name="purge-protection"></a>Proteção de purga 

A proteção de purga é um comportamento opcional do Cofre de Chaves e não é **ativada por defeito**. A proteção contra a purga só pode ser ativada quando se ativa uma eliminação suave.  Pode ser ligado via [CLI](soft-delete-cli.md#enabling-purge-protection) ou [PowerShell](soft-delete-powershell.md#enabling-purge-protection).

Quando a proteção contra a purga estiver acesa, um cofre ou um objeto no estado apagado não podem ser purgados até que o período de retenção tenha passado. As abóbadas e objetos apagados ainda podem ser recuperados, garantindo que a política de retenção será seguida. 

O período de retenção por defeito é de 90 dias, mas é possível definir o intervalo de política de retenção para um valor de 7 a 90 dias através do portal Azure. Uma vez definido o intervalo de política de retenção e guardado, não pode ser alterado para o cofre. 

### <a name="permitted-purge"></a>Purga permitida

Eliminando, purgando permanentemente, um cofre chave é possível através de uma operação POST no recurso proxy e requer privilégios especiais. Geralmente, apenas o proprietário da subscrição será capaz de purgar um cofre chave. A operação POST despoleta a eliminação imediata e irrecuperável do cofre. 

As exceções são:
- Quando a subscrição do Azure tiver sido marcada como *indelével*. Neste caso, apenas o serviço pode então realizar a eliminação real, e fá-lo como um processo programado. 
- Quando o `--enable-purge-protection flag` ativo está no cofre em si. Neste caso, o Key Vault aguardará 90 dias a partir do momento em que o objeto secreto original foi marcado para eliminação para apagar permanentemente o objeto.

### <a name="key-vault-recovery"></a>Recuperação do cofre chave

Ao eliminar um cofre chave, o serviço cria um recurso de procuração sob a subscrição, adicionando metadados suficientes para a recuperação. O recurso proxy é um objeto armazenado, disponível no mesmo local que o cofre de chaves eliminado. 

### <a name="key-vault-object-recovery"></a>Recuperação de objetos de abóbada chave

Ao eliminar um objeto chave do cofre, como uma chave, o serviço colocará o objeto num estado apagado, tornando-o inacessível a quaisquer operações de recuperação. Enquanto neste estado, o objeto do cofre-chave só pode ser listado, recuperado ou eliminado com força/permanentemente. Para visualizar os objetos, certifique-se de que utiliza o `-InRemovedState` parâmetro descrito aqui para os [comandos Azure PowerShell](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#secrets).  

Ao mesmo tempo, o Key Vault irá agendar a eliminação dos dados subjacentes correspondentes ao cofre de chaves ou ao objeto do cofre-chave eliminado para execução após um intervalo de retenção pré-determinado. O registo DNS correspondente ao cofre também é mantido durante o intervalo de retenção.

### <a name="soft-delete-retention-period"></a>Período de retenção de eliminação suave

Os recursos eliminados por suaves são retidos por um período de tempo definido, 90 dias. Durante o intervalo de retenção de eliminação suave, aplicam-se os seguintes:

- Pode listar todos os cofres chave e objetos-chave do cofre no estado de eliminação suave para a sua subscrição, bem como informações de eliminação e recuperação de acessos sobre eles.
    - Apenas os utilizadores com permissões especiais podem listar cofres eliminados. Recomendamos que os nossos utilizadores criem uma função personalizada com estas permissões especiais para o manuseamento de cofres eliminados.
- Um cofre-chave com o mesmo nome não pode ser criado no mesmo local; correspondentemente, um objeto de cofre chave não pode ser criado em um dado cofre se esse cofre de chaves contiver um objeto com o mesmo nome e que está em um estado apagado 
- Apenas um utilizador especificamente privilegiado pode restaurar um cofre de chaves ou um objeto de cofre chave, emitindo um comando de recuperação no recurso de procuração correspondente.
    - O utilizador, membro do papel personalizado, que tem o privilégio de criar um cofre chave sob o grupo de recursos pode restaurar o cofre.
- Apenas um utilizador especificamente privilegiado pode apagar à força um cofre de chaves ou um objeto de cofre chave, emitindo um comando de eliminação no recurso de procuração correspondente.

A menos que um cofre chave ou objeto de cofre chave seja recuperado, no final do intervalo de retenção o serviço executa uma purga do cofre de chave ou do objeto-chave eliminado e seu conteúdo. A eliminação de recursos não pode ser reagendada.

### <a name="billing-implications"></a>Implicações de faturação

Em geral, quando um objeto (um cofre-chave ou uma chave ou um segredo) está em estado apagado, só existem duas operações possíveis: "purga" e "recuperação". Todas as outras operações vão falhar. Portanto, mesmo que o objeto exista, nenhuma operação pode ser realizada e, portanto, não ocorrerá nenhuma utilização, pelo que nenhuma fatura. No entanto, existem as seguintes exceções:

- As ações de "purga" e "recuperação" contarão para operações normais de cofre e serão faturadas.
- Se o objeto for uma chave HSM, a carga "tecla protegida de HSM" por versão chave por mês será aplicada se uma versão chave tiver sido utilizada nos últimos 30 dias. Depois disso, uma vez que o objeto está em estado apagado, não podem ser realizadas operações contra ele, pelo que não será aplicada qualquer taxa.

## <a name="next-steps"></a>Passos seguintes

Os dois guias seguintes oferecem os cenários de utilização primários para a utilização de soft-delete.

- [Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell](soft-delete-powershell.md) 
- [Como utilizar a eliminação de forma recuperável do Key Vault com a CLI](soft-delete-cli.md)


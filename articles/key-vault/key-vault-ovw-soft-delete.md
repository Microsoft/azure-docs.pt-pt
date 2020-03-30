---
title: Apaga suave mente o Cofre de Chaves Azure Microsoft Docs
description: O soft-delete no Cofre de Chaves Azure permite-lhe recuperar cofres de chaves apagados e objetos de cofre chave, tais como chaves, segredos e certificados.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 9c72b2ea71da94215fc9193ffdf3906449ec5571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457377"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Descrição geral da eliminação recuperável do Azure Key Vault

A função de eliminação suave do Key Vault permite a recuperação dos cofres apagados e objetos do cofre, conhecidos como soft-delete. Especificamente, abordamos os seguintes cenários:

- Suporte para eliminação recuperável de um cofre chave
- Suporte para eliminação recuperável de objetos chave do cofre (ex. chaves, segredos, certificados)

## <a name="supporting-interfaces"></a>Interfaces de suporte

A função soft-delete está inicialmente disponível através das interfaces [REST,](/rest/api/keyvault/) [CLI,](key-vault-soft-delete-cli.md) [PowerShell](key-vault-soft-delete-powershell.md) e [.NET/C#](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) .

## <a name="scenarios"></a>Cenários

Os Cofres-Chave Azure são recursos rastreados, geridos pelo Azure Resource Manager. O Azure Resource Manager também especifica um comportamento bem definido para a eliminação, o que requer que uma operação DELETE bem sucedida deve resultar em que esse recurso não seja mais acessível. A função de eliminação suave aborda a recuperação do objeto eliminado, quer a eliminação tenha sido acidental ou intencional.

1. No cenário típico, um utilizador pode ter apagado inadvertidamente um cofre chave ou um objeto de cofre chave; se esse cofre chave ou objeto de cofre chave for em recuperação por um período pré-determinado, o utilizador pode desfazer a eliminação e recuperar os seus dados.

2. Num cenário diferente, um utilizador desonesto pode tentar apagar um cofre chave ou um objeto de cofre chave, como uma chave dentro de um cofre, para causar uma perturbação no negócio. Separar a eliminação do cofre-chave ou do objecto-chave do cofre da eliminação real dos dados subjacentes pode ser usado como medida de segurança, limitando, por exemplo, permissões na eliminação de dados para um papel diferente e fidedigno. Esta abordagem requer efetivamente quórum para uma operação que, de outro modo, poderia resultar numa perda imediata de dados.

### <a name="soft-delete-behavior"></a>Comportamento de eliminação suave

Quando o soft-delete está ativado, os recursos marcados como recursos eliminados são retidos por um período determinado (90 dias por padrão). O serviço fornece ainda um mecanismo para recuperar o objeto eliminado, essencialmente desfazendo a eliminação.

Ao criar um novo cofre de chaves, o soft-delete está ligado por padrão. Pode criar um cofre chave sem apagar suavemente através do [Azure CLI](key-vault-soft-delete-cli.md) ou [Azure Powershell](key-vault-soft-delete-powershell.md). Uma vez que o soft-delete é ativado em um cofre chave não pode ser desativado

O período de retenção padrão é de 90 dias mas, durante a criação do cofre chave, é possível definir o intervalo da política de retenção para um valor de 7 a 90 dias através do portal Azure. A política de retenção de proteção da purga utiliza o mesmo intervalo. Uma vez definido, o intervalo da política de retenção não pode ser alterado.

Não é possível reutilizar o nome de um cofre chave que foi apagado até que o período de retenção tenha passado.

### <a name="purge-protection"></a>Proteção de purga 

A proteção da purga é um comportamento opcional do Cofre chave e não é **ativada por padrão**. Pode ser ligado via [CLI](key-vault-soft-delete-cli.md#enabling-purge-protection) ou [Powershell](key-vault-soft-delete-powershell.md#enabling-purge-protection).

Quando a proteção da purga está em funcionação, um cofre ou um objeto no estado apagado não podem ser purgados até que o período de retenção tenha passado. Cofres e objetos apagados ainda podem ser recuperados, garantindo que a política de retenção será seguida. 

O período de retenção por defeito é de 90 dias, mas é possível definir o intervalo da política de retenção para um valor de 7 a 90 dias através do portal Azure. Uma vez definido o intervalo da política de retenção e guardado, não pode ser alterado para o cofre. 

### <a name="permitted-purge"></a>Purga permitida

Eliminação permanente, purgação, um cofre chave é possível através de uma operação POST no recurso proxy e requer privilégios especiais. Geralmente, apenas o proprietário da subscrição será capaz de purgar um cofre chave. A operação POST desencadeia a eliminação imediata e irrecuperável do cofre. 

As exceções são:
- Quando a assinatura Azure tiver sido marcada como *indeleável.* Neste caso, apenas o serviço pode então realizar a eliminação real, e fá-lo como um processo programado. 
- Quando a bandeira de proteção de purga ativa ré ativa da própria abóbada. Neste caso, o Cofre Chave vai esperar 90 dias a partir do momento em que o objeto secreto original foi marcado para a eliminação para apagar permanentemente o objeto.

### <a name="key-vault-recovery"></a>Recuperação do cofre chave

Ao apagar um cofre chave, o serviço cria um recurso proxy sob a subscrição, adicionando metadados suficientes para recuperação. O recurso proxy é um objeto armazenado, disponível no mesmo local que o cofre de chaves apagado. 

### <a name="key-vault-object-recovery"></a>Recuperação de objeto de cofre chave

Ao eliminar um objeto chave do cofre, como uma chave, o serviço colocará o objeto em estado apagado, tornando-o inacessível a quaisquer operações de recuperação. Enquanto neste estado, o objeto chave do cofre só pode ser listado, recuperado ou eliminado com força/permanentemente. 

Ao mesmo tempo, o Cofre-Chave irá agendar a eliminação dos dados subjacentes correspondentes ao cofre de chaves apagado ou objeto de cofre chave para execução após um intervalo de retenção pré-determinado. O registo DNS correspondente ao cofre também é mantido durante a duração do intervalo de retenção.

### <a name="soft-delete-retention-period"></a>Período de retenção soft-delete

Os recursos eliminados suaves são retidos por um período de tempo definido, 90 dias. Durante o intervalo de retenção de eliminação suave, aplicam-se os seguintes:

- Pode listar todos os cofres chave e objetos de cofre chave no estado de eliminação suave para a sua subscrição, bem como informações de eliminação e recuperação de acesso sobre eles.
    - Apenas utilizadores com permissões especiais podem listar cofres apagados. Recomendamos que os nossos utilizadores criem um papel personalizado com estas permissões especiais para manusear cofres apagados.
- Um cofre chave com o mesmo nome não pode ser criado no mesmo local; correspondentemente, um objeto chave cofre não pode ser criado em um dado cofre se esse cofre-chave contiver um objeto com o mesmo nome e que está em um estado eliminado 
- Apenas um utilizador especificamente privilegiado pode restaurar um cofre chave ou um objeto de cofre chave, emitindo um comando de recuperação no recurso proxy correspondente.
    - O utilizador, membro do papel personalizado, que tem o privilégio de criar um cofre chave sob o grupo de recursos pode restaurar o cofre.
- Apenas um utilizador especificamente privilegiado pode eliminar à força um cofre chave ou um objeto de cofre chave, emitindo um comando de exclusão no recurso proxy correspondente.

A menos que um cofre chave ou um objeto de cofre chave sejam recuperados, no final do intervalo de retenção o serviço realiza uma purga do cofre de chaves apagado ou do objeto do cofre chave e do seu conteúdo. A eliminação do recurso não pode ser reagendada.

### <a name="billing-implications"></a>Implicações de faturação

Em geral, quando um objeto (um cofre chave ou uma chave ou um segredo) está em estado apagado, só existem duas operações possíveis: "purgar" e "recuperar". Todas as outras operações vão falhar. Portanto, mesmo que o objeto exista, nenhuma operação pode ser realizada e, portanto, não haverá uso, pelo que não haverá fatura. No entanto, há exceções que se seguem:

- As ações de "purga" e de "recuperação" contarão para as operações normais do cofre e serão faturadas.
- Se o objeto for uma chave HSM, a carga 'HSM Protected key' por versão chave por mensalcarga será aplicada se uma versão chave tiver sido utilizada nos últimos 30 dias. Depois disso, uma vez que o objeto está em estado apagado, nenhuma operação pode ser realizada contra ele, pelo que nenhuma taxa será aplicada.

## <a name="next-steps"></a>Passos seguintes

Os dois guias seguintes oferecem os cenários de utilização primários para a utilização de soft-delete.

- [Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell](key-vault-soft-delete-powershell.md) 
- [Como utilizar a eliminação de forma recuperável do Key Vault com a CLI](key-vault-soft-delete-cli.md)


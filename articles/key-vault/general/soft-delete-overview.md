---
title: Azure Key Vault soft-delete / Microsoft Docs
description: A eliminação suave no Cofre da Chave Azure permite-lhe recuperar cofres de chaves apagados e objetos-chave do cofre, tais como chaves, segredos e certificados.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: 184003e42a14e786cc52c4f585c6781f7b9fe0e7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452186"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Descrição geral da eliminação recuperável do Azure Key Vault

> [!IMPORTANT]
> Tem de ativar a eliminação suave dos cofres das chaves imediatamente. A capacidade de excluir o soft-delete será depreciada até ao final do ano, e a proteção de eliminação suave será automaticamente ligada para todos os cofres-chave.  Veja todos os detalhes [aqui](soft-delete-change.md)

A funcionalidade de eliminação suave do Key Vault permite a recuperação dos cofres apagados e dos objetos do cofre de chaves eliminados (por exemplo, chaves, segredos, certificados), conhecidos como soft-delete. Especificamente, abordamos os seguintes cenários: Esta salvaguarda oferece as seguintes proteções:

- Uma vez que um cofre secreto, chave, certificado ou cofre chave é eliminado, permanecerá recuperável por um período configurável de 7 a 90 dias de calendário. Se não for especificada nenhuma configuração, o período de recuperação predefinido será definido para 90 dias. Isto proporciona aos utilizadores tempo suficiente para notar uma eliminação acidental e secreta e responder.
- Devem ser feitas duas operações para eliminar permanentemente um segredo. Primeiro, um utilizador deve apagar o objeto, que o coloca no estado de eliminação suave. Em segundo lugar, um utilizador deve expurgar o objeto no estado de eliminação suave. A operação de purga requer permissões adicionais da política de acesso. Estas proteções adicionais reduzem o risco de um utilizador apagar acidentalmente ou maliciosamente um cofre secreto ou um cofre de chaves.  
- Para expurgar um segredo no estado de eliminação suave, deve ser concedida a um diretor de serviço uma permissão adicional de política de acesso "purga". A permissão da política de acesso à purga não é concedida por defeito a qualquer principal de serviço, incluindo os principais proprietários de cofres e subscrições e deve ser deliberadamente definida. Ao exigir uma autorização de política de acesso elevado para purgar um segredo apagado, reduz a probabilidade de acidentalmente apagar um segredo.

## <a name="supporting-interfaces"></a>Interfaces de apoio

A função soft-delete está disponível através da [API REST,](/rest/api/keyvault/)do [Azure CLI,](./key-vault-recovery.md) [do Azure PowerShell](./key-vault-recovery.md)e das interfaces [.NET/C#,](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) bem como [dos modelos ARM](/azure/templates/microsoft.keyvault/2019-09-01/vaults).

## <a name="scenarios"></a>Cenários

Os Cofres chave Azure são rastreados recursos, geridos pelo Azure Resource Manager. O Azure Resource Manager também especifica um comportamento bem definido para a eliminação, o que requer que uma operação de DELETE bem sucedida resulte em que esse recurso não seja mais acessível. A função de eliminação suave aborda a recuperação do objeto eliminado, quer a eliminação tenha sido acidental ou intencional.

1. No cenário típico, um utilizador pode ter apagado inadvertidamente um cofre de chaves ou um objeto de cofre chave; se o cofre de chaves ou o objeto do cofre chave forem recuperáveis por um período pré-determinado, o utilizador pode desfazer a eliminação e recuperar os seus dados.

2. Num cenário diferente, um utilizador desonesto pode tentar apagar um cofre ou um objeto de cofre chave, como uma chave dentro de um cofre, para causar uma perturbação do negócio. A separação do cofre-chave ou do objeto do cofre-chave da eliminação real dos dados subjacentes pode ser usada como medida de segurança, limitando, por exemplo, as permissões na eliminação de dados a um papel diferente e fidedigno. Esta abordagem requer efetivamente quórum para uma operação que, de outra forma, poderia resultar numa perda imediata de dados.

### <a name="soft-delete-behavior"></a>Comportamento de eliminação suave

Quando a eliminação suave é ativada, os recursos marcados como recursos eliminados são mantidos por um período especificado (90 dias por padrão). O serviço fornece ainda um mecanismo para a recuperação do objeto eliminado, essencialmente desfazendo a supressão.

Ao criar um novo cofre de chave, o soft-delete é ligado por defeito. Pode criar um cofre de chaves sem eliminar suavemente através do [Azure CLI](./key-vault-recovery.md) ou [da Azure PowerShell](./key-vault-recovery.md). Uma vez que a eliminação suave é ativada num cofre de teclas, não pode ser desativada

O período de retenção predefinido é de 90 dias mas, durante a criação do cofre chave, é possível definir o intervalo de política de retenção para um valor de 7 a 90 dias através do portal Azure. A política de proteção da purga utiliza o mesmo intervalo. Uma vez definido, o intervalo da política de retenção não pode ser alterado.

Não é possível reutilizar o nome de um cofre-chave que foi apagado até que o período de retenção tenha passado.

### <a name="purge-protection"></a>Proteção de purga

A proteção de purga é um comportamento opcional do Cofre de Chaves e não é **ativada por defeito**. A proteção contra a purga só pode ser ativada quando se ativa uma eliminação suave.  Pode ser ligado via [CLI](./key-vault-recovery.md?tabs=azure-cli) ou [PowerShell](./key-vault-recovery.md?tabs=azure-powershell).

Quando a proteção contra a purga estiver acesa, um cofre ou um objeto no estado apagado não podem ser purgados até que o período de retenção tenha passado. As abóbadas e objetos apagados ainda podem ser recuperados, garantindo que a política de retenção será seguida.

O período de retenção por defeito é de 90 dias, mas é possível definir o intervalo de política de retenção para um valor de 7 a 90 dias através do portal Azure. Uma vez definido o intervalo de política de retenção e guardado, não pode ser alterado para o cofre.

### <a name="permitted-purge"></a>Purga permitida

Eliminando, purgando permanentemente, um cofre chave é possível através de uma operação POST no recurso proxy e requer privilégios especiais. Geralmente, apenas o proprietário da subscrição será capaz de purgar um cofre chave. A operação POST despoleta a eliminação imediata e irrecuperável do cofre. 

As exceções são:
- Quando a subscrição do Azure tiver sido marcada como *indelével*. Neste caso, apenas o serviço pode então realizar a eliminação real, e fá-lo como um processo programado. 
- Quando o `--enable-purge-protection flag` ativo está no cofre em si. Neste caso, o Key Vault aguardará 90 dias a partir do momento em que o objeto secreto original foi marcado para eliminação para apagar permanentemente o objeto.

Para os passos, consulte [Como utilizar o Key Vault soft-delete com CLI: Purgar um cofre](./key-vault-recovery.md?tabs=azure-cli#key-vault-cli) de chaves ou como usar o Key Vault [soft-delete com PowerShell: Purgar um cofre de chaves](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell).

### <a name="key-vault-recovery"></a>Recuperação do cofre chave

Ao eliminar um cofre chave, o serviço cria um recurso de procuração sob a subscrição, adicionando metadados suficientes para a recuperação. O recurso proxy é um objeto armazenado, disponível no mesmo local que o cofre de chaves eliminado. 

### <a name="key-vault-object-recovery"></a>Recuperação de objetos de abóbada chave

Ao eliminar um objeto chave do cofre, como uma chave, o serviço colocará o objeto num estado apagado, tornando-o inacessível a quaisquer operações de recuperação. Enquanto neste estado, o objeto do cofre-chave só pode ser listado, recuperado ou eliminado com força/permanentemente. Para visualizar os objetos, utilize o comando Azure CLI `az keyvault key list-deleted` (como documentado em [Como utilizar o Key Vault soft-delete com CLI),](./key-vault-recovery.md)ou o parâmetro Azure PowerShell `-InRemovedState` (como descrito em Como utilizar o [Key Vault soft-delete com PowerShell).](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)  

Ao mesmo tempo, o Key Vault irá agendar a eliminação dos dados subjacentes correspondentes ao cofre de chaves ou ao objeto do cofre-chave eliminado para execução após um intervalo de retenção pré-determinado. O registo DNS correspondente ao cofre também é mantido durante o intervalo de retenção.

### <a name="soft-delete-retention-period"></a>Período de retenção de eliminação suave

Os recursos eliminados por suaves são retidos por um período de tempo definido, 90 dias. Durante o intervalo de retenção de eliminação suave, aplicam-se os seguintes:

- Pode listar todos os cofres chave e objetos-chave do cofre no estado de eliminação suave para a sua subscrição, bem como informações de eliminação e recuperação de acessos sobre eles.
  - Apenas os utilizadores com permissões especiais podem listar cofres eliminados. Recomendamos que os nossos utilizadores criem uma função personalizada com estas permissões especiais para o manuseamento de cofres eliminados.
- Um cofre-chave com o mesmo nome não pode ser criado no mesmo local; correspondentemente, um objeto de cofre chave não pode ser criado num dado cofre se o cofre de teclas contiver um objeto com o mesmo nome e que está em estado de apagação.
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

- [Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell](./key-vault-recovery.md) 
- [Como utilizar a eliminação de forma recuperável do Key Vault com a CLI](./key-vault-recovery.md)
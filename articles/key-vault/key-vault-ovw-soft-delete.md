---
title: Exclusão reversível de Azure Key Vault | Microsoft Docs
ms.service: key-vault
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 89b7dc639a3140f17a62087c5ba0d05fb6df4d7f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883129"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Visão geral da exclusão reversível Azure Key Vault

O recurso de exclusão reversível do Key Vault permite a recuperação dos cofres excluídos e dos objetos de cofre, conhecidos como exclusão reversível. Especificamente, abordamos os seguintes cenários:

- Suporte para exclusão recuperável de um cofre de chaves
- Suporte para exclusão recuperável de objetos do cofre de chaves (por exemplo, chaves, segredos, certificados)

## <a name="supporting-interfaces"></a>Interfaces de suporte

O recurso de exclusão reversível está inicialmente disponível por meio do [REST](/rest/api/keyvault/), da [CLI](key-vault-soft-delete-cli.md), do [PowerShell](key-vault-soft-delete-powershell.md) e do [.NET/C# ](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) interfaces.

## <a name="scenarios"></a>Cenários

Os Azure Key Vaults são recursos controlados, gerenciados pelo Azure Resource Manager. Azure Resource Manager também especifica um comportamento bem definido para exclusão, o que exige que uma operação de exclusão bem-sucedida deva fazer com que o recurso não fique mais acessível. O recurso de exclusão reversível resolve a recuperação do objeto excluído, independentemente de a exclusão ser acidental ou intencional.

1. No cenário típico, um usuário pode ter excluído inadvertidamente um cofre de chaves ou um objeto de cofre de chaves; Se esse Key Vault ou o objeto do cofre de chaves fossem recuperáveis por um período predeterminado, o usuário poderá desfazer a exclusão e recuperar seus dados.

2. Em um cenário diferente, um usuário não autorizado pode tentar excluir um cofre de chaves ou um objeto de cofre de chaves, como uma chave dentro de um cofre, para causar uma interrupção nos negócios. A separação da exclusão do cofre de chaves ou do objeto do cofre de chaves da exclusão real dos dados subjacentes pode ser usada como medida de segurança por, por exemplo, restringindo permissões na exclusão de dados para uma função diferente e confiável. Essa abordagem exige efetivamente o quorum para uma operação que, de outra forma, pode resultar em uma perda de dados imediata.

### <a name="soft-delete-behavior"></a>Comportamento de exclusão reversível

Com esse recurso, a operação de exclusão em um cofre de chaves ou objeto de cofre de chaves é uma exclusão reversível, mantendo efetivamente os recursos para um determinado período de retenção (90 dias), enquanto dá a aparência de que o objeto é excluído. O serviço fornece ainda mais um mecanismo para recuperar o objeto excluído, essencialmente desfazendo a exclusão. 

A exclusão reversível é um comportamento opcional de Key Vault e **não é habilitada por padrão** nesta versão. Ele pode ser ativado via [CLI](key-vault-soft-delete-cli.md) ou [PowerShell](key-vault-soft-delete-powershell.md).

### <a name="purge-protection"></a>Limpar proteção 

Quando a proteção de limpeza está ativada, um cofre ou um objeto no estado excluído não pode ser limpo até que o período de retenção de 90 dias tenha passado. Esses cofres e objetos ainda podem ser recuperados, garantindo aos clientes que a política de retenção será seguida. 

A proteção de limpeza é um comportamento opcional de Key Vault e **não é habilitada por padrão**. Ele pode ser ativado via [CLI](key-vault-soft-delete-cli.md#enabling-purge-protection) ou [PowerShell](key-vault-soft-delete-powershell.md#enabling-purge-protection).

### <a name="permitted-purge"></a>Limpeza permitida

A exclusão permanente, a limpeza de um cofre de chaves é possível por meio de uma operação POST no recurso de proxy e requer privilégios especiais. Em geral, somente o proprietário da assinatura poderá limpar um cofre de chaves. A operação POST aciona a exclusão imediata e irrecuperável desse cofre. 

As exceções são:
- Quando a assinatura do Azure tiver sido marcada como *undeletable*. Nesse caso, somente o serviço pode executar a exclusão real e faz isso como um processo agendado. 
- Quando o sinalizador--enable-remove-Protection está habilitado no cofre em si. Nesse caso, Key Vault aguardará 90 dias a partir do momento em que o objeto secreto original foi marcado para exclusão para excluir permanentemente o objeto.

### <a name="key-vault-recovery"></a>Recuperação do cofre de chaves

Após a exclusão de um cofre de chaves, o serviço cria um recurso de proxy na assinatura, adicionando metadados suficientes para recuperação. O recurso de proxy é um objeto armazenado, disponível no mesmo local que o cofre de chaves excluído. 

### <a name="key-vault-object-recovery"></a>Recuperação de objeto do cofre de chaves

Ao excluir um objeto do cofre de chaves, como uma chave, o serviço coloca o objeto em um estado excluído, tornando-o inacessível para qualquer operação de recuperação. Enquanto estiver nesse estado, o objeto do cofre de chaves só poderá ser listado, recuperado ou forçado/excluído permanentemente. 

Ao mesmo tempo, Key Vault agendará a exclusão dos dados subjacentes correspondentes ao cofre de chaves ou objeto do cofre de chaves excluído para execução após um intervalo de retenção predeterminado. O registro DNS correspondente ao cofre também é retido durante o intervalo de retenção.

### <a name="soft-delete-retention-period"></a>Período de retenção de exclusão reversível

Os recursos com exclusão reversível são retidos por um determinado período de tempo, 90 dias. Durante o intervalo de retenção de exclusão reversível, o seguinte se aplica:

- Você pode listar todos os cofres de chaves e objetos do cofre de chaves no estado de exclusão reversível para sua assinatura, bem como informações de exclusão e recuperação de acesso sobre eles.
    - Somente usuários com permissões especiais podem listar cofres excluídos. Recomendamos que nossos usuários criem uma função personalizada com essas permissões especiais para lidar com cofres excluídos.
- Um cofre de chaves com o mesmo nome não pode ser criado no mesmo local; de forma correspondente, um objeto do cofre de chaves não poderá ser criado em um determinado cofre se esse cofre de chaves contiver um objeto com o mesmo nome e que estiver em um estado excluído 
- Somente um usuário com privilégios específicos pode restaurar um cofre de chaves ou um objeto do cofre de chaves emitindo um comando de recuperação no recurso de proxy correspondente.
    - O usuário, membro da função personalizada, que tem o privilégio para criar um cofre de chaves no grupo de recursos pode restaurar o cofre.
- Somente um usuário com privilégios específicos pode forçar a exclusão de um cofre de chaves ou objeto de cofre de chaves emitindo um comando delete no recurso de proxy correspondente.

A menos que um cofre de chaves ou um objeto do cofre de chaves seja recuperado, ao final do intervalo de retenção, o serviço executará uma limpeza do cofre de chaves ou do objeto do cofre de chaves com exclusão reversível e seu conteúdo. A exclusão de recursos não pode ser reagendada.

### <a name="billing-implications"></a>Implicações de cobrança

Em geral, quando um objeto (um cofre de chaves ou uma chave ou um segredo) está em estado excluído, há apenas duas operações possíveis: ' limpar ' e ' recuperar '. Todas as outras operações falharão. Portanto, embora o objeto exista, nenhuma operação pode ser executada e, portanto, nenhum uso ocorrerá, portanto, nenhuma fatura. No entanto, há exceções a seguir:

- as ações ' limpar ' e ' recuperar ' serão consideradas para operações normais do cofre de chaves e serão cobradas.
- Se o objeto for uma chave HSM, a cobrança "chave protegida por HSM" por versão de chave por custo mensal será aplicada se uma versão de chave tiver sido usada nos últimos 30 dias. Depois disso, como o objeto está no estado excluído, nenhuma operação pode ser executada em relação a ele, portanto, nenhum encargo será aplicado.

## <a name="next-steps"></a>Passos Seguintes

Os dois guias a seguir oferecem os principais cenários de uso para usar a exclusão reversível.

- [Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell](key-vault-soft-delete-powershell.md) 
- [Como utilizar a eliminação de forma recuperável do Key Vault com a CLI](key-vault-soft-delete-cli.md)


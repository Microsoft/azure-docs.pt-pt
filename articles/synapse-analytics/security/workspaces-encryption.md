---
title: Encriptação Azure Synapse Analytics
description: Um artigo que explica a encriptação no Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d4bc59a9bd5299698bff9949aaaa881fbdf385ee
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526277"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Encriptação das áreas de trabalho do Azure Synapse Analytics

Este artigo descreverá:
* Encriptação de dados em repouso em espaços de trabalho Synapse Analytics.
* Configuração de espaços de trabalho synapse para permitir encriptação com uma chave gerida pelo cliente.
* Chaves de gestão usadas para encriptar dados em espaços de trabalho.

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso

Uma solução completa de encriptação-em-repouso garante que os dados nunca são persistidos de forma não encriptada. A dupla encriptação de dados em repouso atenua as ameaças com duas camadas separadas de encriptação para proteger contra compromissos de qualquer camada. O Azure Synapse Analytics oferece uma segunda camada de encriptação para os dados no seu espaço de trabalho com uma chave gerida pelo cliente. Esta chave está salvaguardada no seu [Azure Key Vault,](../../key-vault/general/overview.md)que lhe permite tomar posse da gestão e rotação de chaves.

A primeira camada de encriptação para os serviços Azure está ativada com chaves geridas pela plataforma. Por padrão, discos Azure e dados nas contas de Armazenamento Azure são automaticamente encriptados em repouso. Saiba mais sobre como a encriptação é usada no Microsoft Azure na Visão Geral de [Encriptação Azure](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Encriptação Azure Synapse

Esta secção irá ajudá-lo a entender melhor como a encriptação de chaves gerida pelo cliente é ativada e aplicada em espaços de trabalho da Synapse. Esta encriptação utiliza chaves existentes ou chaves novas geradas no Cofre da Chave Azure. Uma única chave é usada para encriptar todos os dados num espaço de trabalho. Os espaços de trabalho da Sinaapse suportam chaves RSA com teclas de byte 2048 e 3072.

> [!NOTE]
> Os espaços de trabalho da Sinapse não suportam a utilização de teclas de Criptografia Elliptic-Curve (ECC) para encriptação.

Os dados dos seguintes componentes da Sinapse são encriptados com a chave gerida pelo cliente configurada ao nível do espaço de trabalho:
* Conjuntos de SQL
 * Piscinas SQL dedicadas
 * Piscinas SQL sem servidor
* Conjuntos do Apache Spark
* Tempos de integração da Azure Data Factory, oleodutos, conjuntos de dados.

## <a name="workspace-encryption-configuration"></a>Configuração de encriptação do espaço de trabalho

Os espaços de trabalho podem ser configurados para permitir a dupla encriptação com uma chave gerida pelo cliente no momento da criação do espaço de trabalho. Selecione a opção "Ativar a encriptação dupla utilizando uma chave gerida pelo cliente" no separador "Segurança" ao criar o seu novo espaço de trabalho. Pode optar por introduzir um URI identificador de chave ou selecionar a partir de uma lista de cofres chave na **mesma região** que o espaço de trabalho. O cofre-chave em si precisa de ter **proteção de purga ativada**.

> [!IMPORTANT]
> A definição de configuração para dupla encriptação não pode ser alterada após a criação do espaço de trabalho.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Este diagrama mostra a opção que deve ser selecionada para permitir um espaço de trabalho para dupla encriptação com uma chave gerida pelo cliente.":::

### <a name="key-access-and-workspace-activation"></a>Acesso chave e ativação do espaço de trabalho

O modelo de encriptação Azure Synapse com chaves geridas pelo cliente envolve o espaço de trabalho que acede às chaves no Cofre da Chave Azure para encriptar e desencriptar conforme necessário. As chaves são tornadas acessíveis ao espaço de trabalho através de uma política de acesso ou [do acesso RBAC do Azure Key Vault](../../key-vault/general/rbac-guide.md). Ao conceder permissões através de uma política de acesso a Azure Key Vault, escolha a opção ["Apenas para aplicação"](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) durante a criação de políticas (selecione a identidade gerida do espaço de trabalho e não a adicione como uma aplicação autorizada).

 A identidade gerida pelo espaço de trabalho deve ser concedida as permissões que necessita no cofre da chave antes de o espaço de trabalho poder ser ativado. Esta abordagem faseada da ativação do espaço de trabalho garante que os dados no espaço de trabalho são encriptados com a chave gerida pelo cliente. Note que a encriptação pode ser ativada ou desativada para piscinas SQL dedicadas, cada pool não está ativado para encriptação por padrão.

#### <a name="permissions"></a>Permissões

Para encriptar ou desencriptar dados em repouso, a identidade gerida pelo espaço de trabalho deve ter as seguintes permissões:
* WrapKey (para inserir uma chave no Cofre de Chaves ao criar uma nova chave).
* Desembrulhar a Chave (para obter a chave para a desencriptação).
* Obtenha (ler a parte pública de uma chave)

#### <a name="workspace-activation"></a>Ativação do espaço de trabalho

Após a criação do seu espaço de trabalho (com dupla encriptação ativada), permanecerá num estado "pendente" até que a ativação seja bem sucedida. O espaço de trabalho tem de ser ativado antes de poder utilizar totalmente todas as funcionalidades. Por exemplo, só pode criar um novo pool SQL dedicado assim que a ativação for bem sucedida. Conceda ao espaço de trabalho acesso de identidade gerido ao cofre de chaves e clique no link de ativação no banner do portal Azure do espaço de trabalho. Uma vez concluída a ativação com sucesso, o seu espaço de trabalho está pronto a ser utilizado com a garantia de que todos os dados nele são protegidos com a sua chave gerida pelo cliente. Como já foi dito, o cofre-chave deve ter proteção de purga ativada para que a ativação tenha sucesso.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Este diagrama mostra o banner com a ligação de ativação para o espaço de trabalho.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Gerir a chave gerida pelo cliente do espaço de trabalho 

Pode alterar a chave gerida pelo cliente utilizada para encriptar dados a partir da página **de Encriptação** no portal Azure. Também aqui pode escolher uma nova chave usando um identificador de chave ou selecionar a partir de Key Vaults a que tem acesso na mesma região que o espaço de trabalho. Se escolher uma chave num cofre de chaves diferente dos anteriormente utilizados, conceda o espaço de trabalho de identidade gerida "Get", "Wrap" e permissões "Desembrulhar" no novo cofre da chave. O espaço de trabalho validará o seu acesso ao novo cofre-chave e todos os dados no espaço de trabalho serão reencriptados com a nova chave.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Este diagrama mostra a secção de encriptação do espaço de trabalho no portal Azure.":::

>[!IMPORTANT]
>Ao alterar a chave de encriptação de um espaço de trabalho, mantenha a chave até a substituir no espaço de trabalho por uma nova chave. Isto é para permitir a desencriptação de dados com a tecla antiga antes de ser reencri encriptada com a nova chave.

As políticas Azure Key Vaults para rotação automática e periódica de teclas ou ações nas teclas podem resultar na criação de novas versões-chave. Pode optar por reen encriptar todos os dados do espaço de trabalho com a versão mais recente da chave ativa. Para reencriptá-la, altere a chave do portal Azure para uma chave temporária e, em seguida, volte a ser a chave que pretende utilizar para encriptação. Como exemplo, para atualizar a encriptação de dados utilizando a versão mais recente da chave ativa Key1, altere a chave gerida pelo cliente do espaço de trabalho para a chave temporária, Key2. Aguarde a encriptação com a Key2 para terminar. Em seguida, mude a chave gerida pelo cliente do espaço de trabalho de volta para os dados key1 no espaço de trabalho será reencri encriptada com a versão mais recente do Key1.

> [!NOTE]
> O Azure Synapse Analytics está a trabalhar ativamente na adição de funcionalidades para reencrimizar automaticamente os dados quando forem criadas novas versões chave. Até que esta funcionalidade esteja disponível, para garantir a consistência no seu espaço de trabalho, force a reencriminação de dados utilizando o processo descrito acima.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>Encriptação de dados transparentes SQL com chaves geridas pelo serviço

A SQL Transparent Data Encryption (TDE) está disponível para piscinas SQL dedicadas em espaços de trabalho *não* habilitados para dupla encriptação. Neste tipo de espaço de trabalho, uma chave gerida pelo serviço é usada para fornecer encriptação dupla para os dados nas piscinas SQL dedicadas. O TDE com a chave gerida pelo serviço pode ser ativado ou desativado para piscinas SQL individuais dedicadas.

## <a name="next-steps"></a>Passos seguintes

[Utilize políticas de Azure incorporadas para implementar proteção de encriptação para espaços de trabalho da Sinapse](../policy-reference.md)


---
title: Acesso seguro a um HSM gerido - Azure Key Vault Gerido HSM
description: Saiba como garantir o acesso ao HSM gerido utilizando o Azure RBAC e o HSM RBAC gerido localmente
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 18ffa0f878effda8888200c13ab312851aaebdcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91000898"
---
# <a name="secure-access-to-your-managed-hsms"></a>Acesso seguro aos seus HSMs geridos

Azure Key Vault Managed HSM é um serviço de nuvem que protege as chaves de encriptação. Uma vez que estes dados são sensíveis e críticos de negócio, é necessário garantir o acesso aos seus HSMs geridos, permitindo apenas que aplicações e utilizadores autorizados acedam aos mesmos. Este artigo fornece uma visão geral do modelo de controlo de acesso gerido do HSM. Explica a autenticação e a autorização e descreve como garantir o acesso aos seus HSMs geridos.

Este tutorial irá acompanhá-lo através de um exemplo simples que mostra como alcançar a separação de deveres e o controlo de acesso usando Azure RBAC e HSM RBAC gerido local. Consulte [o controlo de acesso gerido do HSM](access-control.md) para saber mais sobre o modelo de controlo de acesso gerido do HSM.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, tem de ter os seguintes itens:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A versão Azure CLI 2.12.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM gerido na sua subscrição. Consulte [Quickstart: Provisão e ativação de um HSM gerido utilizando o Azure CLI](quick-create-cli.md) para provisões e ativar um HSM gerido.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar seduca em Azure usando o CLI pode escrever:

```azurecli
az login
```

Para obter mais informações sobre as opções de login através do CLI, consulte [o login com o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="example"></a>Exemplo

Neste exemplo, estamos a desenvolver uma aplicação que usa uma chave RSA de 2.048 bits para operações de sinalização. A nossa aplicação funciona numa máquina virtual Azure (VM) com uma [identidade gerida.](../../active-directory/managed-identities-azure-resources/overview.md) Ambas as teclas RSA utilizadas para a assinatura estão armazenadas no nosso HSM gerido.

Identificámos as seguintes funções que gerem, implementam e auditam a nossa aplicação:

- **Equipa de segurança**: Pessoal de TI do gabinete do CSO (Chief Security Officer) ou colaboradores semelhantes. A equipa de segurança é responsável pela proteção adequada das chaves. As teclas RSA ou CE para a assinatura e as teclas RSA ou AES para encriptação de dados.
- **Desenvolvedores e operadores**: O pessoal que desenvolve a aplicação e a implementa em Azure. Os membros desta equipa não fazem parte da equipa de segurança. Não deviam ter acesso a dados sensíveis como as chaves RSA. Apenas a aplicação que implementam deve ter acesso a estes dados sensíveis.
- **Auditores**: Este papel é para os contribuintes que não são membros do pessoal de desenvolvimento ou de TI geral. Revejam o uso e manutenção de certificados, chaves e segredos para garantir o cumprimento das normas de segurança.

Há outro papel que está fora do âmbito da nossa aplicação: o administrador de subscrição (ou grupo de recursos). O administrador de subscrição estabelece permissões de acesso iniciais para a equipa de segurança. Concedem acesso à equipa de segurança utilizando um grupo de recursos que tem os recursos exigidos pela aplicação.

Precisamos de autorizar as seguintes operações para as nossas funções:

**Equipa de segurança**
- Crie o HSM gerido.
- Descarregue o domínio de segurança gerido do HSM (para recuperação de desastres)
- Ligue o registo.
- Gerar ou importar chaves
- Crie as cópias de segurança geridas do HSM para a recuperação de desastres.
- Definir O RBAC local gerido do HSM para conceder permissões aos utilizadores e aplicações para operações específicas.
- Enrole as chaves periodicamente.

**Programadores e operadores**
- Obtenha referência (key URI) da equipa de segurança que a chave RSA usou para a assinatura.
- Desenvolver e implementar a aplicação que acede à chave programáticamente.

**Auditores**
- Rever as datas de validade das chaves para garantir que as chaves estão atualizadas
- Monitorizar atribuições de funções para garantir que as chaves só podem ser acedidas por utilizadores/aplicações autorizados
- Reveja os registos geridos do HSM para confirmar a utilização adequada das chaves em conformidade com as normas de segurança de dados.

A tabela seguinte resume as atribuições de funções a equipas e recursos para aceder ao HSM gerido.

| Função | Papel do avião de gestão | Função do plano de dados |
| --- | --- | --- |
| Equipa de segurança | Colaborador gerido do HSM | Administrador gerido do HSM |
| Programadores e operadores | Nenhum | Nenhum |
| Auditores | Nenhum | Auditor cripto gerido do HSM |
| Identificação gerida do VM utilizado pela Aplicação| Nenhum | Utilizador crypto gerido do HSM |
| Identidade gerida da conta de Armazenamento utilizada pela Aplicação| Nenhum| Encriptação de serviço HSM gerida |

As três funções da equipa precisam de acesso a outros recursos, juntamente com permissões geridas do HSM. Para implementar VMs (ou a funcionalidade de Aplicações Web do Azure App Service), os desenvolvedores e operadores precisam de `Contributor` acesso a esses tipos de recursos. Os auditores precisam de ter acesso lido à conta de Armazenamento onde os registos geridos do HSM são armazenados.

Para atribuir funções de avião de gestão (Azure RBAC) pode utilizar o portal Azure ou qualquer outra interface de gestão, como a Azure CLI ou a Azure PowerShell. Para atribuir funções geridas de plano de dados HSM, deve utilizar o Azure CLI.

Os cortes Azure CLI nesta secção são construídos com os seguintes pressupostos:

- O administrador do Azure Ative Directory criou grupos de segurança para representar as três funções: Contoso Security Team, Contoso App DevOps e Contoso App Auditores. O administrador adicionou utilizadores aos respetivos grupos.
- Todos os recursos estão localizados no grupo de recursos **ContosoAppRG.**
- Os registos geridos do HSM são armazenados na conta de armazenamento **de contosologstorage.**
- A **ContatomHSM** geriu o HSM e a conta de armazenamento **de contosologstorage** estão na mesma localização Azure.

O administrador de subscrição atribui o `Managed HSM Contributor` papel à equipa de segurança. Esta função permite à equipa de segurança gerir os HSMs geridos existentes e criar novos. Se existirem HSMs geridos existentes, terão de ser atribuídos o papel de "Administrador gerido do HSM" para poderem geri-los.

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

A equipa de segurança configura o registo e atribui funções aos auditores e à aplicação VM.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

Este tutorial só mostra ações relevantes para o controlo de acesso na maior parte das vezes. Outras ações e operações relacionadas com a implementação de aplicações no seu VM, ligando a encriptação com a chave gerida pelo cliente para uma conta de armazenamento, criando HSM gerido não são mostrados aqui para manter o exemplo focado no controlo de acesso e gestão de funções.

O nosso exemplo descreve um cenário simples. Cenários da vida real podem ser mais complexos. Pode ajustar permissões ao seu cofre com base nas suas necessidades. Assumimos que a equipa de segurança fornece as referências chave e secretas (URIs e impressões digitais), que são usadas pelo pessoal da DevOps nas suas aplicações. Os desenvolvedores e operadores não requerem acesso a um avião de dados. Concentrámo-nos em como proteger o cofre das chaves. Tenha a mesma consideração quando garantir [os seus VMs,](https://azure.microsoft.com/services/virtual-machines/security/)contas de armazenamento e [outros](../../storage/blobs/security-recommendations.md)recursos Azure.

## <a name="resources"></a>Recursos

- [Documentação do RBAC do Azure](../../role-based-access-control/overview.md)
- [Azure RBAC: Papéis incorporados](../../role-based-access-control/built-in-roles.md)
- [Gerir o Azure RBAC com o Azure CLI](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>Passos seguintes

Para um tutorial de iniciação para um administrador, veja [o que é O HSM gerido?](overview.md)

Para obter mais informações sobre o registo de utilização para registos geridos do HSM, consulte [o registo gerido do HSM](logging.md).

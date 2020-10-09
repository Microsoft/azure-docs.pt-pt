---
title: O que fazer se houver uma rutura do serviço Azure que afete o Gerido HSM - Azure Key Vault Microsoft Docs
description: Saiba o que fazer se houver uma rutura do serviço Azure que afeta o HSM gerido.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 7dbb7b3fdc15c0a9d502fbe9a0d12d084f9ddf29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760398"
---
# <a name="managed-hsm-disaster-recovery"></a>Recuperação de desastres geridos do HSM

Pode desejar criar uma réplica exata do seu HSM se o original estiver perdido ou indisponível devido a qualquer uma das razões abaixo:

- Foi apagado e depois purgado.
- Uma falha catastrófica na região resultou na destruição de todas as divisórias dos membros.

Pode recriar o caso HSM na mesma região ou na mesma região se tiver o seguinte:
- O domínio de [segurança](security-domain.md) da fonte HSM.
- As teclas privadas (pelo menos o número do quórum) que encriptam o domínio de segurança.
- A mais recente cópia de [segurança](backup-restore.md) completa do HSM da fonte HSM.

Aqui estão os passos do processo de recuperação de desastres:

1. Criar uma nova instância HSM.
1. Ativar "Recuperação do Domínio de Segurança". Um novo par de chaves RSA (Chave de Troca de Domínio de Segurança) será gerado para transferência de Domínio de Segurança e enviado em resposta, que será descarregado como uma SecurityDomainExchangeKey (chave pública).
1. Crie e, em seguida, carre fique com o "Ficheiro de Transferência de Domínio de Segurança". Vai precisar das chaves privadas que encriptam o domínio de segurança. As chaves privadas são usadas localmente, e nunca são transferidas para nenhum lugar neste processo.
1. Faça uma cópia do novo HSM. É necessária uma cópia de segurança antes de qualquer restauro, mesmo quando o HSM está vazio. As cópias de segurança permitem um retrocesso fácil.
1. Restaurar o recente backup HSM da fonte HSM

O conteúdo do seu cofre-chave é replicado dentro da região e numa região secundária a pelo menos 250 km de distância, mas dentro da mesma geografia. Esta funcionalidade mantém uma elevada durabilidade das suas chaves e segredos. Consulte o documento das [regiões emparelhadas Azure](../../best-practices-availability-paired-regions.md) para obter detalhes sobre pares de regiões específicas.

## <a name="create-a-new-managed-hsm"></a>Criar um novo HSM gerido

Utilize o `az keyvault create` comando para criar um HSM gerido. Este script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome HSM e a localização geográfica.

Deve fornecer as seguintes entradas para criar um recurso Gerido HSM:

- O nome do HSM.
- O grupo de recursos onde será colocado na sua subscrição.
- A localização do Azure.
- Uma lista de administradores iniciais.

O exemplo abaixo cria um HSM chamado **ContosoMHSM,** no grupo de recursos  **ContosoResourceGroup,** residente na **localização East US 2,** com **o atual assinado no utilizador** como o único administrador.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> O comando de criação pode demorar alguns minutos. Assim que voltar com sucesso, está pronto para ativar o seu HSM.

A saída deste comando mostra propriedades do HSM gerido que criou. As duas propriedades mais importantes são:

* **nome**: No exemplo, o nome é ContosoMHSM. Vais usar este nome para outros comandos do Key Vault.
* **hsmUri**: No exemplo, o URI é ' https://contosohsm.managedhsm.azure.net .' As aplicações que utilizam o seu HSM através da sua API REST devem utilizar este URI.

A sua conta Azure está agora autorizada a realizar quaisquer operações neste HSM gerido. Até agora, ninguém mais está autorizado.

## <a name="activate-the-security-domain-recovery-mode"></a>Ativar o modo de recuperação do domínio de segurança

No processo de criação normal, inicializamos e descarregamos um novo Domínio de Segurança neste momento. No entanto, uma vez que estamos a executar um procedimento de recuperação de desastres, pedimos ao HSM para introduzir o Modo de Recuperação do Domínio de Segurança e descarregar uma Chave de Troca de Domínio de Segurança. A Chave de Troca de Domínio de Segurança é uma chave pública RSA que será usada para encriptar o domínio de segurança antes de o enviar para o HSM. A chave privada correspondente está protegida dentro do HSM, para manter o conteúdo do domínio de segurança seguro durante a transferência.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Carregar domínio de segurança para destino HSM

Para este passo, você precisará seguinte:
- A Chave de Troca de Domínio de Segurança que descarregou no passo anterior.
- O domínio de segurança da fonte HSM.
- Pelo menos o número de chaves privadas que foram usadas para encriptar o domínio de segurança.

O `az keyvault security-domain upload` comando realiza as seguintes operações:

- Desencriptar o domínio de segurança da fonte HSM com as chaves privadas que fornece. 
- Crie uma bolha de upload de domínio de segurança encriptada com a Chave de Troca de Domínio de Segurança que descarregamos no passo anterior e, em seguida,
- Faça o upload da bolha de segurança para o HSM para concluir a recuperação do domínio de segurança

No exemplo abaixo, utilizamos o Domínio de Segurança a partir do **ContosoMHSM**, os 2 das chaves privadas correspondentes, e enviamo-lo para **ContosoMHSM2,** que está à espera de receber um Domínio de Segurança. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

Agora tanto a fonte HSM (ContosoMHSM) como o destino HSM (ContosoMHSM2) têm o mesmo domínio de segurança. Podemos agora restaurar uma cópia de segurança completa da fonte HSM para o destino HSM.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Crie uma cópia de segurança (como ponto de restauração) do seu novo HSM

É sempre uma boa ideia fazer uma cópia de segurança completa antes de executar uma restauração completa do HSM, para que tenha um ponto de restauro no caso de algo correr mal com a restauração.

Para criar uma cópia de segurança HSM, vai precisar do seguinte
- Uma conta de armazenamento onde a cópia de segurança será armazenada
- Um recipiente de armazenamento de bolhas nesta conta de armazenamento onde o processo de backup criará uma nova pasta para armazenar cópias de segurança encriptadas

Utilizamos `az keyvault backup` o comando para a cópia de segurança HSM no contentor de armazenamento **mhsmbackupcontainer**, que está na conta de armazenamento **ContosoBackup** para o exemplo abaixo. Criamos um token SAS que expira em 30 minutos e fornecemos isso ao Managed HSM para escrever a cópia de segurança.

```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Restaurar a cópia de segurança da fonte HSM

Para este passo, precisa do seguinte:

- A conta de armazenamento e o recipiente blob onde estão armazenados os backups da HSM.
- O nome da pasta de onde pretende restaurar a cópia de segurança. Se criar cópias de segurança regulares, haverá muitas pastas dentro deste recipiente.


```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

Agora que completaste um processo completo de recuperação de desastres. O conteúdo da fonte HSM quando a cópia de segurança foi recolhido são copiados para o destino HSM, incluindo todas as teclas, versões, atributos, etiquetas e atribuições de funções.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Domínio de Segurança ver [Sobre o Domínio de Segurança gerido do HSM](security-domain.md)
- Acompanhe [as melhores práticas geridas do HSM](best-practices.md)

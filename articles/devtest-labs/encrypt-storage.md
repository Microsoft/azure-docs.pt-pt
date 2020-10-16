---
title: Criptografe uma conta de armazenamento Azure usada por um laboratório em Azure DevTest Labs
description: Saiba como configurar a encriptação de um armazenamento Azure usado por um laboratório em Azure DevTest Labs
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 3c1d1531084deeabbe9a8d261e93554a2c691eb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87433595"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Encriptar armazenamento Azure usado por um laboratório em Azure DevTest Labs
Todos os laboratórios criados na Azure DevTest Labs são criados com uma conta de armazenamento Azure associada. A conta de armazenamento é utilizada para os seguintes fins: 

- Armazenar documentos de [fórmula](devtest-lab-manage-formulas.md) que podem ser usados para criar máquinas virtuais.
- Armazenar resultados de artefactos que incluem registos de implantação e extensão gerados pela aplicação de artefactos. 
- [Carregar discos rígidos virtuais (VHDs) para criar imagens personalizadas no laboratório.](devtest-lab-create-template.md)
- Caching frequentemente usado [artefactos](add-artifact-vm.md) e [modelos Azure Resource Manager](devtest-lab-create-environment-from-arm.md) para uma recuperação mais rápida durante a criação de máquinas virtuais/ambiente.

> [!NOTE]
> A informação acima é fundamental para o laboratório operar. É armazenado para a vida do laboratório (e recursos de laboratório) a menos que explicitamente eliminado. Eliminar manualmente estes recursos pode levar a erros na criação de VMs de laboratório e/ou fórmulas tornando-se corruptos para uso futuro. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Localize a conta de armazenamento e veja o seu conteúdo

1. Na página inicial do laboratório, selecione o **grupo de recursos** na página **'Vista Geral'.** Deve ver a página do **grupo de recursos** para o grupo de recursos que contém o laboratório. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Selecione o grupo de recursos na página 'Vista Geral'":::
1. Selecione a conta de armazenamento Azure do laboratório. A convenção de nomeação para a conta de armazenamento do laboratório é: `a<labNameWithoutInvalidCharacters><4-digit number>` . Por exemplo, se o nome do laboratório `contosolab` for, o nome da conta de armazenamento pode ser `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Selecione o grupo de recursos na página 'Vista Geral'":::
3. Na página da **conta de Armazenamento,** selecione **Storage Explorer (pré-visualização)** no menu esquerdo e, em seguida, selecione **OS CONTENTORES BLOB** para encontrar conteúdo relevante relacionado com o laboratório. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Selecione o grupo de recursos na página 'Vista Geral'" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Criptografe a conta de armazenamento de laboratório
O Azure Storage encripta automaticamente os seus dados quando estes são persistidos na nuvem. A encriptação do Azure Storage protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade. Para obter mais informações, consulte [a encriptação do Azure Storage para obter dados em repouso](../storage/common/storage-service-encryption.md).

Os dados na conta de armazenamento do laboratório são encriptados com uma **chave gerida pela Microsoft.** Pode confiar nas teclas geridas pela Microsoft para a encriptação dos seus dados, ou pode gerir a encriptação com as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves para a conta de armazenamento do laboratório, pode especificar uma **chave gerida pelo cliente** com o Azure Key Vault para usar para encriptar/desencriptar dados no armazenamento blob e nos Ficheiros Azure. Para obter mais informações sobre as chaves geridas pelo cliente, consulte [utilize as teclas geridas pelo cliente com o Azure Key Vault para gerir a encriptação do Azure Storage](../storage/common/encryption-customer-managed-keys.md).

Para saber como configurar chaves geridas pelo cliente para encriptação de armazenamento Azure, consulte os seguintes artigos: 

- [Portal do Azure](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [CLI do Azure](../storage/common/storage-encryption-keys-cli.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Gerir o ciclo de vida de armazenamento Azure Blob
Como mencionado, a informação armazenada na conta de armazenamento do Laboratório é fundamental para o laboratório operar sem erros. A menos que explicitamente eliminados, estes dados continuarão a permanecer na conta de armazenamento do laboratório para a vida do laboratório ou a vida de máquinas virtuais específicas do laboratório, dependendo do tipo de dados.

### <a name="uploaded-vhds"></a>VHDs carregados
Estes VHDs são usados para criar imagens personalizadas. Removê-las tornará mais possível criar imagens personalizadas a partir destes VHDs.

### <a name="artifacts-cache"></a>Cache de Artefactos
Estes caches serão recriado sempre que os artefactos forem aplicados. Serão refrescados com os mais recentes conteúdos dos respetivos repositórios referenciados. Assim, se eliminar esta informação para salvar as despesas relacionadas com o Armazenamento, o alívio será temporário.

### <a name="azure-resource-manager-template-cache"></a>Cache do modelo do gestor de recursos Azure
Estes caches serão recriados sempre que os repositórios de modelo baseados em Azure Resource Manager estiverem ligados e fiados no laboratório. Serão refrescados com os mais recentes conteúdos dos respetivos repositórios referenciados. Assim, se eliminar esta informação para salvar as despesas relacionadas com o Armazenamento, o alívio será temporário.

### <a name="formulas"></a>Fórmulas
Estes documentos são utilizados para apoiar a opção de criar fórmulas a partir de VM existentes e criar VMs a partir de fórmulas. A supressão destes documentos de fórmula pode conduzir a erros durante as seguintes operações:

- Criar uma fórmula a partir de um VM de laboratório existente
- Criar ou atualizar fórmulas 
- Criar um VM a partir de uma fórmula.

### <a name="artifact-results"></a>Resultados do artefacto
À medida que os artefactos são aplicados, o tamanho dos respetivos resultados do artefacto pode aumentar com o tempo, dependendo do número e tipo de artefactos que estão sendo executados em VMs de laboratório. Então, como dono de laboratório, talvez queira controlar o ciclo de vida de tais documentos. Para obter mais informações, consulte [gerir o ciclo de vida de armazenamento Azure Blob](../storage/blobs/storage-lifecycle-management-concepts.md).

> [!IMPORTANT]
> Recomendamos que faça este passo para reduzir as despesas associadas à conta de Armazenamento Azure. 

Por exemplo, a seguinte regra é usada para definir uma regra de expiração de 90 dias especificamente para os resultados dos artefactos. Garante que os resultados dos artefactos mais antigos sejam reciclados da conta de armazenamento numa cadência regular.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes
Para saber como configurar chaves geridas pelo cliente para encriptação de armazenamento Azure, consulte os seguintes artigos: 

- [Portal do Azure](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [CLI do Azure](../storage/common/storage-encryption-keys-cli.md)



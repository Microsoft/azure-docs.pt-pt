---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: c28441b6fe25b3480a55b79682d5067b19e3023a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58051832"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Manter os ficheiros no Azure Cloud Shell
Cloud Shell utiliza o armazenamento de ficheiros do Azure para manter os ficheiros em sessões. No início inicial, o Cloud Shell pede-lhe para associar uma partilha de ficheiros novos ou existentes para manter os ficheiros em sessões.

> [!NOTE]
> Bash e o PowerShell partilham a mesma partilha de ficheiros. Apenas uma partilha de ficheiros pode ser associada com a montagem automática no Cloud Shell.

## <a name="create-new-storage"></a>Criar novo armazenamento

Quando utilizar as definições básicas e selecionar apenas uma subscrição, o Cloud Shell cria três recursos em seu nome na região suportada mais próxima:
* Grupo de recursos: `cloud-shell-storage-<region>`
* Conta de armazenamento: `cs<uniqueGuid>`
* Partilha de ficheiros: `cs-<user>-<domain>-com-<uniqueGuid>`

![A definição de subscrição](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

A partilha de ficheiros monta como `clouddrive` no seu `$Home` diretório. Esta é uma ação única e a partilha de ficheiros monta automaticamente nas sessões subsequentes. 

> [!NOTE]
> Para segurança, cada utilizador deve aprovisionar a sua própria conta de armazenamento.  Para controlo de acesso baseado em funções (RBAC), os utilizadores tem de ter acesso de contribuinte ou acima no armazenamento de conta de nível.

A partilha de ficheiros também contém uma imagem de 5 GB, que é criada para si que automaticamente mantém os dados no seu `$Home` diretório. Isto aplica-se para o Bash e o PowerShell.

## <a name="use-existing-resources"></a>Utilizar recursos existentes

Ao utilizar a opção avançada, pode associar recursos existentes. Quando selecionar uma região do Cloud Shell tem de selecionar uma conta de armazenamento de backup colocalizada na mesma região. Por exemplo, se a sua região atribuído é E.U.A. oeste, que tem de associar uma partilha de ficheiros que residem no Oeste dos E.U.A., também.

Quando for apresentada a linha de comandos do programa de configuração da armazenamento, selecione **Mostrar definições avançadas** para ver opções adicionais. O filtro de opções de armazenamento preenchida para o armazenamento localmente redundante (LRS), armazenamento georredundante (GRS) e contas de armazenamento com redundância de zona (ZRS). 

> [!NOTE]
> Contas de armazenamento com o GRS ou ZRS são recomendadas para uma resiliência adicional de segurança de partilha de ficheiros. Que tipo de redundância depende de suas metas e a preferência de preço. [Saiba mais sobre as opções de replicação para contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![A definição de grupo de recursos](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>Regiões de armazenamento suportadas
Associados a contas têm de residir na mesma região que a máquina do Cloud Shell que está a montagem-los para o armazenamento do Azure. Para localizar a sua região atual pode executar `env` no Bash e localize a variável `ACC_LOCATION`. Partilhas de ficheiros recebem uma imagem de 5 GB criada por si manter sua `$Home` diretório.

Máquinas do cloud Shell existem nas seguintes regiões:

|Área|Região|
|---|---|
|Américas|Este dos E.U.A., Centro-Sul dos E.U.A., oeste dos E.U.A.|
|Europa|Europa do Norte, Europa Ocidental|
|Ásia-Pacífico|Ásia Central, Sudeste do Índia|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir a criação de recursos com uma política de recursos do Azure
Contas de armazenamento que criou no Cloud Shell são marcadas com `ms-resource-usage:azure-cloud-shell`. Se quiser não que os utilizadores de criar contas de armazenamento no Cloud Shell, crie uma [política de recursos do Azure para etiquetas](../articles/azure-policy/json-samples.md) que são acionados por esta etiqueta específica.

---
title: Utilize o inventário de armazenamento Azure para gerir dados blob (pré-visualização)
description: O inventário de Armazenamento Azure é uma ferramenta para ajudar a obter uma visão geral de todos os seus dados blob dentro de uma conta de armazenamento.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: a625ad780d01f3d19d26f2b9626ead3ae455b86b
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631477"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Utilize o inventário de bolhas de armazenamento Azure para gerir dados blob (pré-visualização)

A funcionalidade de inventário de blob de armazenamento Azure fornece uma visão geral dos seus dados blob dentro de uma conta de armazenamento. Use o relatório de inventário para entender o tamanho total dos dados, idade, estado de encriptação, e assim por diante. O relatório fornece uma visão geral dos seus dados para requisitos de negócio e conformidade. Uma vez ativado, um relatório de inventário é automaticamente criado diariamente.

## <a name="availability"></a>Disponibilidade

O inventário blob é suportado tanto para as contas de armazenamento de blob de finalidade geral 2 (GPv2) como para as contas de armazenamento de blocos premium blob. Esta funcionalidade é suportada com ou sem a funcionalidade [de espaço hierárquico](data-lake-storage-namespace.md) ativada.

> [!IMPORTANT]
> O inventário Bob está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

### <a name="preview-regions"></a>Pré-visualização de regiões

A pré-visualização do inventário blob está disponível nas contas de armazenamento nas seguintes regiões:

- França Central
- Canadá Central
- Leste do Canadá
- E.U.A. Leste
- E.U.A. Leste 2

### <a name="pricing-and-billing"></a>Preços e faturação

A taxa para relatórios de inventário não é cobrada durante o período de pré-visualização. Os preços serão determinados quando esta funcionalidade estiver geralmente disponível.

## <a name="enable-inventory-reports"></a>Ativar relatórios de inventário

Ativar os relatórios de inventário blob adicionando uma política à sua conta de armazenamento. Adicione, edite ou remova uma política utilizando o [portal Azure](https://portal.azure.com/).

1. Navegue até ao [portal Azure](https://portal.azure.com/)
1. Selecione uma das suas contas de armazenamento
1. Sob **o serviço Blob**, selecione **inventário Blob**
1. Certifique-se de que **o inventário Blob ativado** está selecionado
1. **Selecione Adicionar uma regra**
1. Diga o nome da sua nova regra
1. Selecione os **tipos Blob** para o seu relatório de inventário
1. Adicione uma correspondência de prefixo para filtrar o seu relatório de inventário
1. Selecione se **inclui versões blob** e **inclua instantâneos** no seu relatório de inventário. As versões e instantâneos devem ser ativados na conta para guardar uma nova regra com a opção correspondente ativada.
1. Selecione **Guardar**

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Screenshot mostrando como adicionar uma regra de inventário blob usando o portal Azure":::

As políticas de inventário são lidas ou escritas na íntegra. Atualizações parciais não são suportadas.

> [!IMPORTANT]
> Se ativar regras da firewall na conta de armazenamento, os pedidos de inventário poderão ser bloqueados. Pode desbloquear estes pedidos ao indicar exceções para serviços Microsoft fidedignos. Para obter mais informações, consulte a secção Exceções em [firewalls Configure e redes virtuais.](../common/storage-network-security.md#exceptions)

Uma execução de inventário de bolhas é automaticamente programada todos os dias. Pode levar até 24 horas para uma execução de inventário para completar. Um relatório de inventário é configurado adicionando uma política de inventário com uma ou mais regras.

## <a name="inventory-policy"></a>Política de inventário

Uma política de inventário é uma coleção de regras num documento da JSON.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Consulte o JSON para obter uma política de inventário selecionando o separador **'Vista código'** na secção de **inventário Blob** do portal Azure.

| Nome do parâmetro | Tipo de parâmetro        | Notas | Necessário? |
|----------------|-----------------------|-------|-----------|
| destino    | String                | O contentor de destino onde todos os ficheiros de inventário serão gerados. O contentor de destino já deve existir. | Yes |
| ativado        | Booleano               | Usado para desativar toda a apólice. Quando definido como **verdadeiro,** o campo ativado pelo nível de regra sobrepõe-se a este parâmetro. Quando desativado, o inventário para todas as regras será desativado. | Yes |
| regras          | Matriz de objetos de regra | Pelo menos uma regra é necessária numa política. Até 10 regras são apoiadas. | Yes |

## <a name="inventory-rules"></a>Regras de inventário

Uma regra captura as condições de filtragem e os parâmetros de saída para gerar um relatório de inventário. Cada regra cria um relatório de inventário. As regras podem ter prefixos sobrepostos. Uma bolha pode aparecer em mais de um inventário dependendo das definições de regras.

Cada regra dentro da política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro                 | Notas | Necessário? |
|----------------|--------------------------------|-------|-----------|
| name           | String                         | Um nome de regra pode incluir até 256 caracteres alfanuméricos sensíveis a casos. O nome deve ser único dentro de uma política. | Yes |
| ativado        | Booleano                        | Uma bandeira que permite ativar ou desativar uma regra. O valor predefinido é **verdadeiro.** | Yes |
| definição     | Definição de regra de inventário JSON | Cada definição é composta por um conjunto de filtro de regras. | Yes |

O inventário global **blob habilitado** a bandeira tem precedência sobre o parâmetro *ativado* numa regra.

### <a name="rule-filters"></a>Filtros de regras

Vários filtros estão disponíveis para personalizar um relatório de inventário de bolhas:

| Nome do filtro         | Tipo de filtro                     | Notas | Necessário? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Matriz de valores enum predefinidos | Valores válidos são `blockBlob` e `appendBlob` para contas hierárquicas habilitados para o espaço de `blockBlob` `appendBlob` nome, e , e para `pageBlob` outras contas. | Yes |
| prefixOSatch         | Matriz de até 10 cordas para prefixos a combinar. Um prefixo deve começar com um nome de recipiente, por exemplo, "container1/foo" | Se não definir *prefixoSatch* ou fornecer um prefixo vazio, a regra aplica-se a todas as bolhas dentro da conta de armazenamento. | No |
| incluiSnapshots    | Booleano                         | Especifica se o inventário deve incluir instantâneos. O padrão é **falso.** | No |
| incluem Inversões | Booleano                         | Especifica se o inventário deve incluir versões blob. O padrão é **falso.** | No |

Consulte o JSON para obter regras de inventário selecionando o separador **'Vista código'** na secção de **inventário Blob** do portal Azure. Os filtros são especificados dentro de uma definição de regra.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Saída de inventário

Cada execução de inventário gera um conjunto de ficheiros formatados de CSV no recipiente de destino de inventário especificado. A saída do inventário é gerada no seguinte caminho: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` onde:

- *contaName* é o nome da conta Azure Blob Storage
- *inventário-destino-contentor* é o contentor de destino especificado na política de inventário
- *YYYY/MM/DD/HH-MM-SS* é o momento em que o inventário começou a funcionar

### <a name="inventory-files"></a>Ficheiros de inventário

Cada execução de inventário gera os seguintes ficheiros:

- **Inventário ficheiro CSV**: Um ficheiro de valores separados de vírgula (CSV) para cada regra de inventário. Cada ficheiro contém objetos compatíveis e os seus metadados. A primeira linha em cada ficheiro formatado por CSV é sempre a linha de esquema. A imagem a seguir mostra um ficheiro CSV de inventário aberto no Microsoft Excel.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Screenshot de um ficheiro CSV de inventário aberto no Microsoft Excel":::

- **Ficheiro manifesto**: Um manifest.jsno ficheiro que contenha os detalhes dos ficheiros de inventário gerados para cada regra nessa execução. O ficheiro manifesto também captura a definição de regra fornecida pelo utilizador e o caminho para o inventário para essa regra.

- **Ficheiro checkum**: Um ficheiro manifesto.checksum contendo a parte de verificação MD5 do conteúdo de manifest.jsarquivado. A geração do ficheiro manifesto.checksum marca a conclusão de uma execução de inventário.

## <a name="inventory-completed-event"></a>Evento concluído de inventário

Subscreva o evento concluído do inventário para ser notificado quando o inventário terminar. Este evento é gerado quando o ficheiro manifesto checkum é criado. O evento concluído do inventário também ocorre se a execução do inventário falhar no erro do utilizador antes de começar a ser executado. Por exemplo, uma política inválida ou um erro de destino que não apresente irá desencadear o evento. O evento é publicado no Tema do Inventário Blob.

Evento da amostra:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>Passos seguintes

- [Calcular a contagem e o tamanho total das bolhas por recipiente](calculate-blob-count-size.md)
- [Gerir o ciclo de vida do Armazenamento Azure Blob](storage-lifecycle-management-concepts.md)

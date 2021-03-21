---
title: Utilizar a Azure Import/Export para transferir dados de e para a Azure Storage | Microsoft Docs
description: Saiba como criar postos de trabalho de importação e exportação no portal Azure para a transferência de dados de e para o Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b1f1560fc1a00577e1e4b30d922fc7d4cae0ab92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181859"
---
# <a name="what-is-azure-importexport-service"></a>O que é o serviço Azure Import/Export?

O serviço Azure Import/Export é utilizado para importar de forma segura grandes quantidades de dados para o armazenamento de Azure Blob e para a Azure Files através do envio de discos para um datacenter Azure. Este serviço também pode ser utilizado para transferir dados do Armazenamento de blobs do Azure para unidades de disco rígido e enviar para os sites no local. Os dados de uma ou mais unidades de disco podem ser importados quer para o armazenamento da Azure Blob, quer para os Ficheiros Azure.

Forneça as suas próprias unidades de disco e transfira dados com o serviço Azure Import/Export. Também pode utilizar unidades de disco fornecidas pela Microsoft.

Se pretender transferir dados utilizando unidades de disco fornecidas pela Microsoft, pode utilizar [o Disco Azure Data Box](../databox/data-box-disk-overview.md) para importar dados para o Azure. A Microsoft envia até 5 unidades de disco de estado sólido encriptada (SSDs) com uma capacidade total de 40 TB por encomenda, para o seu datacenter através de uma transportadora regional. Pode configurar rapidamente as unidades de disco, copiar dados para discos que conduzem sobre uma ligação USB 3.0 e enviar as unidades do disco de volta para Azure. Para mais informações, aceda à [visão geral do disco da Caixa de Dados Azure](../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Casos de importação/exportação de Azure

Considere usar o serviço Azure Import/Export quando carregar ou descarregar dados através da rede é demasiado lento, ou obter largura de banda de rede adicional é proibitivo em termos de custos. Utilize este serviço nos seguintes cenários:

* **Migração de dados para a nuvem**: Mover grandes quantidades de dados para Azure rapidamente e custos eficazes.
* **Distribuição de conteúdos**: Envie rapidamente dados para os seus sites de clientes.
* **Backup**: Leve cópias de segurança dos seus dados no local para armazenar no Azure Storage.
* **Recuperação de dados**: Recupere uma grande quantidade de dados armazenados no armazenamento e entregue-os no local.

## <a name="importexport-components"></a>Componentes de importação/exportação

O serviço de importação/exportação utiliza os seguintes componentes:

* **Serviço de importação/exportação**: Este serviço disponível no portal Azure ajuda o utilizador a criar e rastrear empregos de importação de dados (upload) e exportação (download).  

* **Ferramenta WAImportExport**: Esta é uma ferramenta de linha de comando que faz o seguinte:
  * Prepara as suas unidades de disco que são enviadas para importação.
  * Facilita a cópia dos seus dados para a unidade.
  * Encripta os dados da unidade com BitLocker AES de 256 bits. Pode utilizar um protetor de chave externo para proteger a sua chave BitLocker.
  * Gera os ficheiros do diário de unidade utilizados durante a criação de importação.
  * Ajuda a identificar o número de unidades necessárias para os postos de trabalho na exportação.

> [!NOTE]
> A ferramenta WAImportExport está disponível em duas versões, versões 1 e 2. Recomendamos que utilize:
>
> * Versão 1 para importação/exportação para o armazenamento da Azure Blob.
> * Versão 2 para importar dados em ficheiros Azure.
>
> A ferramenta WAImportExport só é compatível com o sistema operativo Windows de 64 bits. Para versões específicas de SO suportadas, aceda aos [requisitos de importação/exportação do Azure.](storage-import-export-requirements.md#supported-operating-systems)

* **Unidades de disco**: Pode enviar unidades de estado sólido (SSDs) ou unidades de disco rígido (HDDs) para o centro de dados Azure. Ao criar um trabalho de importação, envia discos contendo os seus dados. Ao criar um trabalho de exportação, envia-se unidades vazias para o centro de dados Azure. Para tipos específicos de discos, aceda aos [tipos de discos suportados](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Como funciona a Importação/Exportação?

O serviço Azure Import/Export permite a transferência de dados para a Azure Blobs e Azure Files através da criação de empregos. Utilize o portal Azure ou o Azure Resource Manager REST API para criar empregos. Cada trabalho está associado a uma única conta de armazenamento.

Os postos de trabalho podem ser postos de trabalho de importação ou exportação. Uma tarefa de importação permite-lhe importar dados em ficheiros Azure Blobs ou Azure, enquanto a empresa de exportação permite que os dados sejam exportados da Azure Blobs. Para um trabalho de importação, você envia unidades contendo os seus dados. Quando crias um trabalho de exportação, envias unidades vazias para um centro de dados Azure. Em cada caso, pode enviar até 10 discos por trabalho.

### <a name="inside-an-import-job"></a>Dentro de um trabalho de importação

A um nível elevado, um trabalho de importação envolve as seguintes etapas:

1. Determine os dados a serem importados, o número de unidades necessárias, a localização do blob de destino para os seus dados no armazenamento do Azure.
2. Utilize a ferramenta WAImportExport para copiar dados para unidades de discos. Criptografe as unidades do disco com o BitLocker.
3. Crie um trabalho de importação na sua conta de armazenamento alvo no portal Azure. Faça o upload dos ficheiros do diário de unidade.
4. Forneça o endereço de devolução e o número da conta da transportadora para enviar as unidades de volta para si.
5. Envie o disco para o endereço de envio fornecido durante a criação de emprego.
6. Atualize o número de rastreio de entrega nos dados do trabalho de importação e submeta o trabalho de importação.
7. As unidades são recebidas e processadas no centro de dados Azure.
8. As unidades são enviadas usando a sua conta transportadora para o endereço de devolução fornecido no trabalho de importação.

> [!NOTE]
> Para envios locais (dentro do data center country/região), por favor, partilhe uma conta de transportadora doméstica.
>
> Para envios no estrangeiro (fora do data center country/região), por favor partilhe uma conta de transportadora internacional.

 ![Figura 1:Fluxo de trabalho de importação](./media/storage-import-export-service/import-job.png)

Para instruções passo a passo sobre a importação de dados, aceda a:

* [Dados de importação em Azure Blobs](storage-import-export-data-to-blobs.md)
* [Dados de importação em Ficheiros Azure](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Dentro de um trabalho de exportação

> [!IMPORTANT]
> O serviço só suporta a exportação de Azure Blobs. A exportação de ficheiros Azure não é suportada.

A um nível elevado, um trabalho de exportação envolve as seguintes etapas:

1. Determine os dados a exportar, número de unidades necessárias, bolhas de origem ou caminhos de contentores dos seus dados no armazenamento blob.
2. Crie um trabalho de exportação na sua conta de armazenamento de origem no portal Azure.
3. Especifique as bolhas de origem ou as vias do contentor para os dados a exportar.
4. Forneça o endereço de devolução e o número da conta da transportadora para enviar as unidades de volta para si.
5. Envie o disco para o endereço de envio fornecido durante a criação de emprego.
6. Atualize o número de rastreio de entrega nos dados do trabalho de exportação e submeta o trabalho de exportação.
7. As unidades são recebidas e processadas no centro de dados Azure.
8. As unidades são encriptadas com BitLocker e as chaves estão disponíveis através do portal Azure.  
9. As unidades são enviadas usando a sua conta transportadora para o endereço de devolução fornecido no trabalho de importação.

> [!NOTE]
> Para envios locais (dentro do data center country/região), por favor, partilhe uma conta de transportadora doméstica.
>
> Para envios no estrangeiro (fora do data center country/região), por favor partilhe uma conta de transportadora internacional.
  
 ![Figura 2:Fluxo de emprego de exportação](./media/storage-import-export-service/export-job.png)

Para instruções passo a passo sobre a exportação de dados, aceda aos [dados de exportação da Azure Blobs](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Disponibilidade de região

O serviço Azure Import/Export suporta a cópia de dados de e para todas as contas de armazenamento da Azure. Pode enviar discos para um dos locais listados. Se a sua conta de armazenamento estiver numa localização Azure que não está especificada aqui, é fornecida uma localização de envio alternativa quando cria o trabalho.

### <a name="supported-shipping-locations"></a>Locais de envio suportados

|País/Região  |País/Região  |País/Região  |País/Região  |
|---------|---------|---------|---------|
|E.U.A. Leste    | Europa do Norte        | Índia Central        |US Gov - Iowa         |
|E.U.A. Oeste     |Europa Ocidental         | Sul da Índia        | US DoD - Leste        |
|E.U.A. Leste 2    | Ásia Leste        |  Oeste da Índia        | US DoD Centro        |
|E.U.A. Oeste 2     | Sudeste Asiático        | Canadá Central        | Leste da China         |
|E.U.A. Central     | Leste da Austrália        | Leste do Canadá        | Norte da China        |
|E.U.A. Centro-Norte     |  Austrália Sudeste       | Sul do Brasil        | Sul do Reino Unido        |
|E.U.A. Centro-Sul     | Oeste do Japão        |Coreia do Sul Central         | Alemanha Central        |
|E.U.A. Centro-Oeste     |  Leste do Japão       | US Gov - Virginia        | Nordeste da Alemanha        |
|Oeste da África do Sul   |  Norte da África do Sul |

## <a name="security-considerations"></a>Considerações de segurança

Os dados da unidade são encriptados utilizando encriptação bitLocker Drive AES de 256 bits. Esta encriptação protege os seus dados enquanto este se encontra em trânsito.

Para os trabalhos de importação, as unidades são encriptadas de duas maneiras.  

* Especifique a opção ao utilizar *dataset.csv* ficheiro durante a preparação da unidade.

* Ativar a encriptação BitLocker manualmente na unidade. Especifique a chave de encriptação no *driveset.csv* ao executar a linha de comando da ferramenta WAImportExport durante a preparação da unidade. A chave de encriptação BitLocker pode ser ainda mais protegida utilizando um protetor de chave externo (também conhecido como chave gerida pela Microsoft) ou uma chave gerida pelo cliente. Para obter mais informações, consulte como [utilizar uma chave gerida pelo cliente para proteger a sua chave BitLocker](storage-import-export-encryption-key-portal.md).

Para trabalhos de exportação, depois de os seus dados serem copiados para as unidades, o serviço encripta a unidade usando o BitLocker antes de enviá-lo de volta para si. A chave de encriptação é fornecida através do portal Azure. A unidade tem de ser desbloqueada utilizando a ferramenta WAImporExport utilizando a chave.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Preços

**Taxa de manuseamento de unidades**

Existe uma taxa de manuseamento de unidades para cada unidade processada como parte do seu trabalho de importação ou exportação. Consulte os detalhes sobre os [preços de importação/exportação da Azure.](https://azure.microsoft.com/pricing/details/storage-import-export/)

**Custos de envio**

Quando envia para Azure, paga o custo de envio para a transportadora. Quando a Microsoft devolve as unidades para si, o custo de envio é cobrado na conta da transportadora que forneceu no momento da criação de emprego.

**Custos de transação**

[A taxa normal de transação de armazenamento](https://azure.microsoft.com/pricing/details/storage/) aplica-se durante a importação, bem como a exportação de dados. Os encargos de saída padrão também são aplicáveis, juntamente com os encargos de transação de armazenamento quando os dados são exportados do Azure Storage. Para obter mais informações sobre os custos de saída, consulte [os preços de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar o serviço de importação/exportação para:

* [Dados de importação para Azure Blobs](storage-import-export-data-to-blobs.md)
* [Dados de exportação de Azure Blobs](storage-import-export-data-from-blobs.md)
* [Dados de importação para ficheiros Azure](storage-import-export-data-to-files.md)

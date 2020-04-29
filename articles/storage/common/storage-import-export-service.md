---
title: Utilização da Importação/Exportação Azure para transferir dados de e para o Armazenamento Azure [ Microsoft Docs
description: Aprenda a criar postos de trabalho de importação e exportação no portal Azure para a transferência de dados de e para o Armazenamento Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/15/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eee0fc2797fbe0666a6b848fde574c7807f47cc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80282448"
---
# <a name="what-is-azure-importexport-service"></a>O que é o serviço azure import/exportação?

O serviço azure import/exportação é utilizado para importar com segurança grandes quantidades de dados para o armazenamento de Azure Blob e ficheiros Azure através do envio de discos para um datacenter Azure. Este serviço também pode ser usado para transferir dados do armazenamento azure Blob para unidades de disco e enviar para os seus locais no local. Os dados de uma ou mais unidades de disco podem ser importados quer para o armazenamento de Blob Azure quer para os Ficheiros Azure.

Forneça as suas próprias unidades de disco e transfira dados com o serviço de importação/exportação Azure. Também pode utilizar unidades de disco fornecidas pela Microsoft.

Se pretender transferir dados utilizando unidades de disco fornecidas pela Microsoft, pode utilizar o Disco de Caixa de [Dados Do Azure](../../databox/data-box-disk-overview.md) para importar dados para o Azure. A Microsoft envia até 5 unidades de disco de estado sólido encriptadas (SSDs) com uma capacidade total de 40 TB por encomenda, para o seu datacenter através de uma transportadora regional. Pode configurar rapidamente as unidades de disco, copiar dados para discos sobre uma ligação USB 3.0 e enviar as unidades do disco de volta para Azure. Para mais informações, consulte a visão geral do Disco de Caixas de [Dados do Azure](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Casos de importação/exportação de Azure

Considere utilizar o serviço de importação/exportação Azure ao carregar ou descarregar dados através da rede é demasiado lento, ou obter largura de banda adicional da rede é proibitivo em termos de custos. Utilize este serviço nos seguintes cenários:

* **Migração**de dados para a nuvem : Mover grandes quantidades de dados para Azure rapidamente e custar eficazmente.
* **Distribuição de conteúdos**: Envie rapidamente dados para os seus sites de clientes.
* **Backup**: Leve cópias de segurança dos seus dados no local para armazenar no Armazenamento Azure.
* **Recuperação de dados**: Recupere uma grande quantidade de dados armazenados no armazenamento e envie-os para a sua localização no local.

## <a name="importexport-components"></a>Componentes de importação/exportação

O serviço de importação/exportação utiliza os seguintes componentes:

* **Serviço de importação/exportação**: Este serviço disponível no portal Azure ajuda o utilizador a criar e rastrear os postos de trabalho na importação de dados (upload) e na exportação (download).  

* **Ferramenta WAImportExport**: Esta é uma ferramenta de linha de comando que faz o seguinte:
  * Prepara as suas unidades de disco que são enviadas para importação.
  * Facilita a cópia dos seus dados para a unidade.
  * Encripta os dados na unidade com BitLocker de 128 bits AES. Pode utilizar um protetor de teclas externo para proteger a sua tecla BitLocker.
  * Gera os ficheiros do diário de unidade usados durante a criação de importação.
  * Ajuda a identificar o número de unidades necessárias para os trabalhos de exportação.

> [!NOTE]
> A ferramenta WAImportExport está disponível em duas versões, as versões 1 e 2. Recomendamos que utilize:
>
> * Versão 1 para importação/exportação para armazenamento De Blob Azure.
> * Versão 2 para importar dados em ficheiros Azure.
>
> A ferramenta WAImportExport só é compatível com o sistema operativo Windows de 64 bits. Para versões específicas de SO suportadas, vá aos requisitos de [importação/exportação do Azure.](storage-import-export-requirements.md#supported-operating-systems)

* **Unidades de disco**: Pode enviar unidades de estado sólido (SSDs) ou discos rígidos (HDDs) para o centro de dados Azure. Ao criar um trabalho de importação, envie discos com os seus dados. Ao criar um trabalho de exportação, envie unidades vazias para o centro de dados Azure. Para tipos específicos de disco, vá a tipos de [disco suportados](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Como funciona a Importação/Exportação?

O serviço Azure Import/Export permite a transferência de dados para a Azure Blobs e para os Ficheiros Azure através da criação de empregos. Utilize o portal Azure ou o Gestor de Recursos Azure REST API para criar empregos. Cada trabalho está associado a uma única conta de armazenamento.

Os postos de trabalho podem ser empregos de importação ou exportação. Um trabalho de importação permite-lhe importar dados para ficheiros Azure Blobs ou Azure, enquanto o trabalho de exportação permite que os dados sejam exportados a partir de Blobs Azure. Para um trabalho de importação, você envie unidades contendo os seus dados. Quando se cria um trabalho de exportação, enviamos unidades vazias para um centro de dados Azure. Em cada caso, pode enviar até 10 discos por trabalho.

### <a name="inside-an-import-job"></a>Dentro de um trabalho de importação

A um nível elevado, um trabalho de importação envolve os seguintes passos:

1. Determine os dados a importar, número de unidades que precisa, localização de destino blob para os seus dados no armazenamento azure.
2. Utilize a ferramenta WAImportExport para copiar dados para discos. Criptografe as unidades de disco com bitLocker.
3. Crie um trabalho de importação na sua conta de armazenamento alvo no portal Azure. Faça upload dos ficheiros do diário de unidade.
4. Forneça o endereço de devolução e o número da conta transportadora para o envio das unidades de volta para si.
5. Envie o disco para o endereço de envio fornecido durante a criação de emprego.
6. Atualize o número de rastreio de entrega nos dados do trabalho de importação e submeta o emprego de importação.
7. As unidades são recebidas e processadas no centro de dados Azure.
8. As unidades são enviadas utilizando a sua conta transportadora para o endereço de devolução fornecido no trabalho de importação.

> [!NOTE]
> Para envios locais (dentro de data center país/região), por favor partilhe uma conta transportadora doméstica.
>
> Para envios no estrangeiro (centro de dados externos país/região), por favor partilhe uma conta transportadora internacional.

 ![Figura 1:Fluxo de emprego de importação](./media/storage-import-export-service/importjob.png)

Para instruções passo a passo sobre a importação de dados, vá a:

* [Dados de importação em Blobs Azure](storage-import-export-data-to-blobs.md)
* [Dados de importação em Ficheiros Azure](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Dentro de um trabalho de exportação

> [!IMPORTANT]
> O serviço apenas apoia a exportação de Blobs Azure. A exportação de ficheiros Azure não é suportada.

A um nível elevado, um trabalho de exportação envolve os seguintes passos:

1. Determine os dados a exportar, o número de unidades de que necessita, bolhas de origem ou caminhos de contentores dos seus dados no armazenamento blob.
2. Crie um trabalho de exportação na sua conta de armazenamento de origem no portal Azure.
3. Especifique as bolhas de origem ou os caminhos dos contentores para os dados a exportar.
4. Forneça o endereço de devolução e o número da conta transportadora para o envio das unidades de volta para si.
5. Envie o disco para o endereço de envio fornecido durante a criação de emprego.
6. Atualize o número de rastreio de entrega nos dados do trabalho de exportação e submeta o trabalho de exportação.
7. As unidades são recebidas e processadas no centro de dados Azure.
8. As unidades estão encriptadas com o BitLocker e as chaves estão disponíveis através do portal Azure.  
9. As unidades são enviadas utilizando a sua conta transportadora para o endereço de devolução fornecido no trabalho de importação.

> [!NOTE]
> Para envios locais (dentro de data center país/região), por favor partilhe uma conta transportadora doméstica.
>
> Para envios no estrangeiro (centro de dados externos país/região), por favor partilhe uma conta transportadora internacional.
  
 ![Figura 2:Fluxo de emprego de exportação](./media/storage-import-export-service/exportjob.png)

Para instruções passo a passo sobre a exportação de dados, vá aos [dados de exportação da Azure Blobs](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Disponibilidade de região

O serviço Azure Import/Export suporta a cópia de dados de e para todas as contas de armazenamento do Azure. Pode enviar discos para um dos locais listados. Se a sua conta de armazenamento estiver num local Azure que não esteja especificado aqui, é fornecido um local de envio alternativo quando criar o trabalho.

### <a name="supported-shipping-locations"></a>Locais de envio suportados

|País/Região  |País/Região  |País/Região  |País/Região  |
|---------|---------|---------|---------|
|E.U.A. Leste    | Europa do Norte        | Índia Central        |US Gov - Iowa         |
|E.U.A. Oeste     |Europa ocidental         | Sul da Índia        | US DoD - Leste        |
|E.U.A. Leste 2    | Ásia Leste        |  Oeste da Índia        | US DoD Centro        |
|E.U.A.Oeste 2     | Ásia Sudeste        | Canadá Central        | Leste da China         |
|E.U.A. Central     | Leste da Austrália        | Leste do Canadá        | Norte da China        |
|E.U.A. Centro-Norte     |  Austrália Sudeste       | Sul do Brasil        | Sul do Reino Unido        |
|E.U.A. Centro-Sul     | Oeste do Japão        |Coreia do Sul Central         | Alemanha Central        |
|E.U.A. Centro-Oeste     |  Leste do Japão       | US Gov - Virginia        | Nordeste da Alemanha        |

## <a name="security-considerations"></a>Considerações de segurança

Os dados da unidade são encriptados utilizando encriptação BitLocker Drive de 128 bits AES 128 bit. Esta encriptação protege os seus dados durante o seu transporte.

Para os empregos de importação, os discos são encriptados de duas formas.  

* Especifique a opção ao utilizar o ficheiro *dataset.csv* durante a execução da ferramenta WAImportExport durante a preparação da unidade.

* Ativar a encriptação BitLocker manualmente na unidade. Especifique a chave de encriptação no *driveset.csv* ao executar a linha de comando da ferramenta WAImportExport durante a preparação da unidade. A chave de encriptação BitLocker pode ser mais protegida utilizando um protetor de chave externo (também conhecido como a chave gerida pela Microsoft) ou uma chave gerida pelo cliente. Para mais informações, consulte como utilizar uma chave gerida pelo [cliente para proteger a sua chave BitLocker](storage-import-export-encryption-key-portal.md).

Para trabalhos de exportação, depois de os seus dados serem copiados para as unidades, o serviço encripta a unidade utilizando o BitLocker antes de enviá-lo de volta para si. A chave de encriptação é-lhe fornecida através do portal Azure. A unidade tem de ser desbloqueada utilizando a ferramenta WAImporExport utilizando a tecla.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Preços

**Taxa de manuseamento de unidades**

Existe uma taxa de manuseamento de unidades por cada unidade processada como parte do seu trabalho de importação ou exportação. Consulte os detalhes sobre o preço de [importação/exportação de Azure.](https://azure.microsoft.com/pricing/details/storage-import-export/)

**Custos de envio**

Quando envia saques para Azure, paga o custo de envio para a transportadora. Quando a Microsoft devolve as unidades a si, o custo de envio é cobrado à conta transportadora que forneceu no momento da criação de emprego.

**Custos de transação**

As taxas padrão de transação de [armazenagem](https://azure.microsoft.com/pricing/details/storage/) aplicam-se durante a importação, bem como a exportação de dados. Os encargos padrão de egress também são aplicáveis, juntamente com os encargos de transação de armazenamento quando os dados são exportados do Armazenamento Azure. Para obter mais informações sobre os custos de egress, consulte os preços de transferência de [dados. .](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar o serviço de importação/exportação para:

* [Dados de importação para Blobs Azure](storage-import-export-data-to-blobs.md)
* [Dados de exportação de Blobs Azure](storage-import-export-data-from-blobs.md)
* [Dados de importação para ficheiros Azure](storage-import-export-data-to-files.md)

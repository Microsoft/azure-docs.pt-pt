---
title: A utilização de importar/exportar do Azure para transferir dados para e do armazenamento do Azure | Documentos da Microsoft
description: Saiba como criar a importar e exportar tarefas no portal do Azure para transferir dados para e do armazenamento do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 02/14/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 4850dd82ca52a060c921569433035256f5b74cce
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58164931"
---
# <a name="what-is-azure-importexport-service"></a>O que é o serviço importar/exportar do Azure?

O serviço importar/exportar do Azure é utilizado para importar com segurança as grandes quantidades de dados para o armazenamento de Blobs do Azure e ficheiros do Azure envie unidades de disco num Datacenter do Azure. Este serviço também pode ser utilizado para transferir dados do armazenamento de Blobs do Azure para unidades de disco e disponibilize aos seus sites no local. Dados a partir de uma ou mais unidades de disco podem ser importados ou ficheiros do Azure ou de armazenamento de Blobs do Azure. 

Forneça as suas próprias unidades de disco e transferir dados com o serviço importar/exportar do Azure. Também pode utilizar unidades de disco fornecidas pela Microsoft. 

Se quiser transferir dados através de unidades de disco fornecidas pela Microsoft, pode utilizar [disco do Azure Data Box](../../databox/data-box-disk-overview.md) para importar dados para o Azure. Microsoft vem até 5 encriptadas disco unidades de estado sólido (SSDs) com uma capacidade total de 40 TB por ordem, no seu datacenter através de uma operadora regional. Pode rapidamente configurar unidades de disco, copiar os dados em unidades de disco através de uma ligação USB 3.0 e enviar as unidades de disco para o Azure. Para obter mais informações, aceda a [descrição geral do disco do Azure Data Box](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-usecases"></a>Usecases de importação/exportação do Azure

Considere utilizar o serviço importar/exportar do Azure quando carregar ou transferir dados através da rede é muito lento ou a obtenção de largura de banda de rede adicional é custo altíssimo. Utilize este serviço nos seguintes cenários:

* **Migração de dados para a cloud**: Mover grandes quantidades de dados para o Azure rapidamente e económica.
* **Distribuição de conteúdo**: Envie dados rapidamente para sites dos seus clientes.
* **Cópia de segurança**: Fazer cópias de segurança dos seus dados no local para armazenar no armazenamento do Azure.
* **Recuperação de dados**: Recuperar a grande quantidade de dados armazenados no armazenamento e tê-lo entregue para a sua localização no local.

## <a name="importexport-components"></a>Componentes de importação/exportação

O serviço importar/exportar utiliza os seguintes componentes:

- **Serviço de importação/exportação**: Este serviço disponível no portal do Azure ajuda o usuário criar e controlar a importação de dados (carregar) e exportar tarefas (download).  

- **Ferramenta de WAImportExport**: Esta é uma ferramenta de linha de comandos que faça o seguinte: 
    - Prepara a unidades de disco que é fornecida para importação.
    - Facilita a copiar os dados para a unidade.
    - Encripta os dados na unidade com o BitLocker.
    - Gera os ficheiros de diário de unidade utilizados durante a criação de importação.
    - Ajuda a identificar o número de unidades necessários para tarefas de exportação.
    
> [!NOTE]
> A ferramenta de WAImportExport está disponível em duas versões, versão 1 e 2. Recomendamos que utilize:
> - Versão 1 para importar/exportar para o armazenamento de Blobs do Azure. 
> - Versão 2 para importar dados para os ficheiros do Azure.
>
> A ferramenta de WAImportExport só é compatível com o sistema de operativo do Windows de 64 bits. Para versões de SO específicas suportadas, aceda a [requisitos de importar/exportar do Azure](storage-import-export-requirements.md#supported-operating-systems).

- **Unidades de disco**: Pode enviar unidades de estado sólido (SSDs) ou unidades de disco rígido (HDDs) para o datacenter do Azure. Ao criar uma tarefa de importação, entregar as unidades de disco que contém os dados. Ao criar uma tarefa de exportação, envie discos vazios para o datacenter do Azure. Para tipos de disco específico, aceda a [tipos de disco suportados](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Como funciona a importação/exportação?

O serviço importar/exportar do Azure permite a transferência de dados em Blobs do Azure e ficheiros do Azure através da criação de tarefas. Utilize o portal do Azure ou a API de REST do Azure Resource Manager para criar tarefas. Cada tarefa está associada uma única conta de armazenamento. 

As tarefas podem ser importar ou exportar tarefas. Uma tarefa de importação permite-lhe importar dados para Blobs do Azure ou os ficheiros do Azure, ao passo que a tarefa de exportação permite que os dados exportados dos Blobs do Azure. Para uma tarefa de importação, enviar unidades que contêm os dados. Quando cria uma tarefa de exportação, envie discos vazios num Datacenter do Azure. Em cada caso, pode enviar até 10 unidades de disco por tarefa.

### <a name="inside-an-import-job"></a>Dentro de uma tarefa de importação

Num alto nível, uma tarefa de importação envolve os seguintes passos:

1. Determine os dados sejam importados, número de unidades que precisar, localização de blob de destino para os seus dados no armazenamento do Azure.
2. Utilize a ferramenta de WAImportExport para copiar dados para unidades de disco. As unidades de disco com o BitLocker para encriptar.
3. Crie uma tarefa de importação na sua conta de armazenamento de destino no portal do Azure. Carregue os ficheiros de diário de unidade.
4. Forneça o endereço do remetente e o número de conta de operadora para as unidades de envio é com.
5. Envie as unidades de disco para o endereço de envio fornecido durante a criação da tarefa.
6. Atualize a entrega número nos detalhes da tarefa de importação de controle e submeter a tarefa de importação.
7. As unidades sejam recebidas e processadas no Centro de dados do Azure.
8. As unidades são fornecidas com a sua conta de operadora para o endereço do remetente fornecido na tarefa de importação.

> [!NOTE]
> Para remessas em locais (no país do Centro de dados), partilhe uma conta da transportadora nacionais 
>
> Para remessas abroad (fora do país do Centro de dados), partilhe uma conta da transportadora internacionais

 ![Fluxo de trabalho de 1:Import figura](./media/storage-import-export-service/importjob.png)

Para obter instruções passo a passo nos dados de importação, aceda a:

- [Importar dados para Blobs do Azure](storage-import-export-data-to-blobs.md)
- [Importar dados para ficheiros do Azure](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>Dentro de uma tarefa de exportação

> [!IMPORTANT]
> O serviço suporta apenas a exportação de Blobs do Azure. A exportação dos ficheiros do Azure não é suportada.

Num alto nível, uma tarefa de exportação envolve os seguintes passos:

1. Determinar os dados para ser exportada, número de unidades necessidade, blobs de origem ou caminhos do contentor dos seus dados no armazenamento de Blobs.
3. Crie uma tarefa de exportação na conta de armazenamento de origem no portal do Azure.
4. Especifique os blobs de origem ou de caminhos do contentor para os dados ser exportada.
5. Forneça o endereço do remetente e o número de conta de operadora para as unidades de envio é com.
6. Envie as unidades de disco para o endereço de envio fornecido durante a criação da tarefa.
7. Atualize a entrega número nos detalhes da tarefa de exportação de controle e submeter a tarefa de exportação.
8. As unidades sejam recebidas e processadas no Centro de dados do Azure.
9. As unidades são encriptadas com BitLocker e as chaves estão disponíveis através do portal do Azure.  
10. As unidades são fornecidas com a sua conta de operadora para o endereço do remetente fornecido na tarefa de importação.

> [!NOTE]
> Para remessas em locais (no país do Centro de dados), partilhe uma conta da transportadora nacionais 
>
> Para remessas abroad (fora do país do Centro de dados), partilhe uma conta da transportadora internacionais
  
 ![Fluxo de trabalho de 2:Export figura](./media/storage-import-export-service/exportjob.png)

Para obter instruções passo a passo sobre a exportação de dados, aceda a [exportar dados de Blobs do Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Disponibilidade de região 

O serviço importar/exportar do Azure suporta a cópia de dados de e para todas as contas de armazenamento do Azure. Pode enviar unidades de disco para uma das localizações indicadas. Se a sua conta de armazenamento é na localização do Azure que não é especificada aqui, uma localização alternativa de envio é fornecida quando criou a tarefa.

### <a name="supported-shipping-locations"></a>Suportado localizações de envio


|País  |País  |País  |País  |
|---------|---------|---------|---------|
|EUA Leste    | Europa do Norte        | Índia Central        |US Gov - Iowa         |
|EUA Oeste     |Europa Ocidental         | Sul da Índia        | US DoD Leste        |
|EUA Leste 2    | Ásia Oriental        |  Oeste da Índia        | US DoD Centro        |
|EUA Oeste 2     | Sudeste Asiático        | Canadá Central        | Leste da China         |
|EUA Central     | Leste da Austrália        | Leste do Canadá        | China Norte        |
|EUA Centro-Norte     |  Sudeste da Austrália       | Sul do Brasil        | Reino Unido Sul        |
|EUA Centro-Sul     | Oeste do Japão        |Coreia do Sul Central         | Alemanha Central        |
|EUA Centro-Oeste     |  Leste do Japão       | Gov (US) - Virginia        | Alemanha Nordeste        |


## <a name="security-considerations"></a>Considerações de segurança

Os dados na unidade são encriptados com encriptação de unidade BitLocker. Esta encriptação protege os dados enquanto estiverem em trânsito.

Para tarefas de importação, as unidades são encriptadas de duas formas.  


- Especificar a opção ao usar *dataset.csv* ficheiro ao executar a ferramenta de WAImportExport durante a preparação da unidade. 

- Ative a encriptação de disco BitLocker manualmente na unidade. Especifique a chave de encriptação no *driveset.csv* ao executar linha de comandos de ferramenta WAImportExport durante a preparação da unidade.


Para tarefas de exportação, depois dos dados são copiados para as unidades, o serviço encripta a unidade usando o BitLocker antes de remessa é com. A chave de encriptação é fornecida através do portal do Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Preços

**Taxa de processamento da unidade**

Existe uma taxa de processamento de unidade para cada unidade processada como parte da importação ou exportação tarefa. Consulte os detalhes sobre o [preços de importação/exportação do Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Custos de envio**

Quando entregar unidades para o Azure, paga o custo de envio para a envio de operadora. Quando a Microsoft devolve as unidades para, o custo de envio é cobrado para a conta de operadora que indicou no momento da criação da tarefa.

**Custos de transação**

Não existem não existem custos de transação além dos custos de transação de armazenamento standard ao importar dados para o armazenamento do Azure. Os custos de saída padrão são aplicáveis quando dados são exportados a partir do armazenamento de Blobs. Para obter mais informações sobre os custos de transação, consulte [preços de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="next-steps"></a>Passos Seguintes

Saiba como utilizar o serviço de importação/exportação para:
* [Importar dados para Blobs do Azure](storage-import-export-data-to-blobs.md)
* [Exportar os dados dos Blobs do Azure](storage-import-export-data-from-blobs.md)
* [Importar dados para ficheiros do Azure](storage-import-export-data-to-files.md)


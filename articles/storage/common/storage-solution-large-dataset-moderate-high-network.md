---
title: Opções de transferência de dados azure para grandes conjuntos de dados, largura de banda de rede moderada a alta. Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados quando tiver largura de banda de rede moderada a alta no seu ambiente e planeia transferir grandes conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: f7177a95bdd585ff2822c9ac8c94a85d12f9259b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900371"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Data transfer for large datasets with moderate to high network bandwidth (Transferência de dados para conjuntos de dados grandes com largura de banda de rede moderada ou alta)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando você tem largura de banda de rede moderada a alta no seu ambiente e você está planejando transferir grandes conjuntos de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher uma solução de transferência de [dados Azure.](storage-choose-data-transfer-solution.md)

## <a name="scenario-description"></a>Descrição do cenário

Os grandes conjuntos de dados referem-se aos tamanhos de dados na ordem dos TBs aos PBs. Largura de banda moderada a alta da rede refere-se a 100 Mbps a 10 Gbps.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas neste cenário dependem se tem largura de banda de rede moderada ou largura de banda de rede elevada.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Largura de banda moderada da rede (100 Mbps - 1 Gbps)

Com largura de banda de rede moderada, é necessário projetar o tempo para a transferência de dados pela rede.

Utilize a tabela seguinte para estimar o tempo e com base nisso, escolha entre uma transferência offline ou sobre a transferência de rede. O quadro mostra o tempo previsto para a transferência de dados da rede, para várias larguras de banda disponíveis (assumindo uma utilização de 90%).  

![Transferência de rede ou transferência offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Se a transferência de rede for projetada como demasiado lenta, deverá utilizar um dispositivo físico. As opções recomendadas neste caso são os dispositivos de transferência offline da família Azure Data Box ou da Azure Import/Export utilizando os seus próprios discos.

    - **Família Azure Data Box para transferências offline** – Utilize dispositivos de dispositivos Data Box fornecidos pela Microsoft para mover grandes quantidades de dados para o Azure quando estiver limitado pelo tempo, disponibilidade da rede ou custos. Copie os dados no local utilizando ferramentas como a Robocopy. Dependendo do tamanho dos dados destinados à transferência, pode escolher entre Data Box Disk, Data Box ou Data Box Heavy.
    - **Azure Import/Export** – Use o serviço de importação/exportação Azure enviando as suas próprias unidades de disco para importar com segurança grandes quantidades de dados para o armazenamento da Blob Azure e ficheiros Azure. Este serviço também pode ser usado para transferir dados do armazenamento azure Blob para unidades de disco e enviar para os seus locais no local.

- Se a transferência de rede for razoável, então pode utilizar qualquer uma das seguintes ferramentas detalhadas na largura de [banda da alta rede](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Largura de banda de alta rede (1 Gbps - 100 Gbps)

Se a largura de banda de rede disponível for elevada, utilize uma das seguintes ferramentas.

- **AzCopy** - Utilize esta ferramenta de linha de comando para copiar facilmente dados de e para o armazenamento de Blobs, Ficheiros e Mesa seletivas com um desempenho ótimo. A AzCopy suporta conmoeda e paralelismo, e a capacidade de retomar as operações de cópia quando interrompida.
- **Azure Storage REST APIs/SDKs** – Ao construir uma aplicação, pode desenvolver a aplicação contra APIs REST DE Armazenamento Azure e utilizar os SDKs Azure oferecidos em vários idiomas.
- **Família Azure Data Box para transferências online** – Data Box Edge e Data Box Gateway são dispositivos de rede online que podem mover dados para dentro e para fora do Azure. Utilize o dispositivo físico Data Box Edge quando houver uma necessidade simultânea de ingestão contínua e pré-processamento dos dados antes do upload. Data Box Gateway é uma versão virtual do dispositivo com as mesmas capacidades de transferência de dados. Em cada caso, a transferência de dados é gerida pelo dispositivo.
- **A Azure Data Factory** – Data Factory deve ser utilizada para escalar uma operação de transferência e, se houver necessidade de capacidades de orquestração e de monitorização de nível empresarial. Utilize a Data Factory para transferir regularmente ficheiros entre vários serviços Azure, no local ou uma combinação dos dois. com data factory, pode criar e programar fluxos de trabalho baseados em dados (chamados oleodutos) que ingebem dados de lojas de dados díspares e automatizam movimento de dados e transformação de dados.

## <a name="comparison-of-key-capabilities"></a>Comparação de capacidades-chave

As tabelas seguintes resumem as diferenças nas capacidades-chave para as opções recomendadas.

### <a name="moderate-network-bandwidth"></a>Largura de banda moderada da rede

Se utilizar a transferência de dados offline, utilize a tabela seguinte para compreender as diferenças nas capacidades-chave.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Importação/Exportação                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Tamanho dos dados                        |    Até 35 TBs                 |    Até 80 TBs por dispositivo                       |    Até 800 TB por dispositivo               |    Variável                            |
|    Tipo de dados                        |    Blobs do Azure                  |    Blobs do Azure<br>Ficheiros do Azure                    |    Blobs do Azure<br>Ficheiros do Azure            |    Blobs do Azure<br>Ficheiros do Azure          |
|    Fator de forma                      |    5 SSDs por encomenda             |    1 X 50 libras. dispositivo do tamanho de um ambiente de trabalho por encomenda    |    1 X ~500 lbs. grande dispositivo por ordem    |    Até 10 HDDs/SSDs por encomenda        |
|    Tempo inicial de configuração               |    Baixa <br>(15 minutos)            |    Baixo a moderado <br> (<30 minutos)               |    Moderado<br>(1-2 horas)               |    Moderado a difícil<br>(variável) |
|    Enviar dados para O Azure               |    Sim                          |    Sim                                           |    Sim                                   |    Sim                                 |
|    Exportar dados do Azure           |    Não                           |    Não                                            |    Não                                    |    Sim                                 |
|    Encriptação                       |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    Hardware                         |     Microsoft fornecido          |    Microsoft fornecido                            |    Microsoft fornecido                    |    Cliente fornecido                   |
|    Interface de rede                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integração de parceiros              |    Alguns                         |    [Alto](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Alto](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Alguns                                |
|    Shipping                         |    A Microsoft geriu            |    A Microsoft geriu                             |    A Microsoft geriu                     |    Cliente gerido                    |
| Utilizar quando os dados se movem         |Dentro de um limite de comércio|Dentro de um limite de comércio|Dentro de um limite de comércio|Além das fronteiras geográficas, por exemplo, EUA para a UE|
|    Preços                          |    [Preços](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Preços](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preços](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preços](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Se utilizar a transferência de dados on-line, utilize a tabela na secção seguinte para uma largura de banda de alta rede.

### <a name="high-network-bandwidth"></a>Largura de banda de alta rede

|                                     |    Ferramentas AzCopy, <br>Azure PowerShell, <br>CLI do Azure             |    Azure Storage REST APIs, SDKs                   |    Gateway de caixa de dados ou borda de caixa de dados          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Tipo de dados                  |    Blobs Azure, Arquivos Azure, Mesas Azure    |    Blobs Azure, Arquivos Azure, Mesas Azure    |    Azure Blobs, Ficheiros Azure                           |   Suporta mais de 70 conectores de dados para lojas e formatos de dados    |
|    Fator de forma                |    Ferramentas de linha de comandos                        |    Interface programática                    |    Microsoft fornece um virtual <br>ou dispositivo físico     |    Serviço no portal Azure                                            |
|    Configuração única inicial     |    Fácil               |    Moderado                       |    Fácil (<30 minutos) a moderado (1-2 horas)            |    Extensa                                                          |
|    Pré-processamento de dados              |    Não                                        |    Não                                        |    Sim (com a computação Edge)                               |    Sim                                                                |
|    Transferência de outras nuvens       |    Não                                        |    Não                                        |    Não                                                    |    Sim                                                                |
|    Tipo de utilizador                        |    IT Pro ou dev                                       |    Dev                                       |    Profissional de TI                                                |    Profissional de TI                                                             |
|    Preços                          |    Aplicam-se gratuitamente as taxas de egress de dados         |    Aplicam-se gratuitamente as taxas de egress de dados         |    [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Passos seguintes

- [Saiba como transferir dados com importe/exportação.](/azure/storage/common/storage-import-export-data-to-blobs)
- Entender como

    - [Transferir dados com disco](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal)de caixa de dados .
    - [Transferir dados com Caixa](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)de Dados.
- [Transferir dados com a AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Entenda como:
    - [Transferir dados com Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transforme os dados com data box Edge antes de enviar para o Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Saiba como transferir dados com a Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)
- Use as APIs rest para transferir dados

    - [Em .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [Em Java](https://docs.microsoft.com/java/api/overview/azure/storage)

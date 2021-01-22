---
title: Opções de transferência de dados Azure para grandes conjuntos de dados, largura de banda de rede moderada a alta| Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados quando tiver largura de banda de rede moderada a alta no seu ambiente e planeia transferir grandes conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: cf0e423648db174433f0717f2e5971ac49697b42
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704628"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Data transfer for large datasets with moderate to high network bandwidth (Transferência de dados para conjuntos de dados grandes com largura de banda de rede moderada ou alta)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando você tem largura de banda de rede moderada a alta no seu ambiente e você está planejando transferir grandes conjuntos de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher [uma solução de transferência de dados Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrição do cenário

Grandes conjuntos de dados referem-se a tamanhos de dados na ordem das TBs a PBs. Largura de banda de rede moderada a alta refere-se a 100 Mbps a 10 Gbps.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas neste cenário dependem do facto de ter largura de banda de rede moderada ou alta.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Largura de banda de rede moderada (100 Mbps - 1 Gbps)

Com largura de banda de rede moderada, é necessário projetar o tempo para a transferência de dados pela rede.

Utilize a tabela seguinte para estimar o tempo e com base nisso, escolha entre uma transferência offline ou sobre a transferência de rede. A tabela mostra o tempo previsto para a transferência de dados de rede, para várias larguras de banda de rede disponíveis (assumindo uma utilização de 90%).  

![Transferência de rede ou transferência offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Se se projetar que a transferência de rede seja demasiado lenta, deve utilizar um dispositivo físico. As opções recomendadas neste caso são os dispositivos de transferência offline da família Azure Data Box ou Azure Import/Export utilizando os seus próprios discos.

    - **Família Azure Data Box para transferências offline** – Utilize dispositivos de dispositivos Data Box fornecidos pela Microsoft para mover grandes quantidades de dados para o Azure quando estiver limitado pelo tempo, disponibilidade da rede ou custos. Copie dados no local com ferramentas como o Robocopy. Conforme o tamanho dos dados que se pretende transferir, escolha entre Data Box Disk, Data Box ou Data Box Heavy.
    - **Azure Import/Export** – Use o serviço Azure Import/Export enviando as suas próprias unidades de disco para importar de forma segura grandes quantidades de dados para o armazenamento e arquivos Azure Blob. Este serviço também pode ser utilizado para transferir dados do Armazenamento de blobs do Azure para unidades de disco rígido e enviar para os sites no local.

- Se a transferência de rede for projetada como razoável, então pode utilizar qualquer uma das seguintes ferramentas detalhadas em [largura de banda de alta rede](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Largura de banda de rede alta (1 Gbps - 100 Gbps)

Se a largura de banda de rede disponível for elevada, utilize uma das seguintes ferramentas.

- **AzCopy** - Utilize esta ferramenta de linha de comando para copiar facilmente dados de e para Azure Blobs, Ficheiros e Armazenamento de Mesa com o melhor desempenho. O AzCopy suporta simultaneidade e paralelismo, além da capacidade de retomar operações de cópia interrompidas.
- **Azure Storage REST APIs/SDKs** – Ao construir uma aplicação, pode desenvolver a aplicação contra ASPis de ARMAZENAMENTO Azure e utilizar os SDKs Azure oferecidos em vários idiomas.
- **Família Azure Data Box para transferências on-line** – Data Box Edge e Data Box Gateway são dispositivos de rede online que podem mover dados para dentro e para fora do Azure. Utilize o dispositivo físico do Data Box Edge quando houver uma necessidade simultânea de ingestão de dados contínua e pré-processamento dos dados antes do carregamento. O Data Box Gateway é uma versão virtual do dispositivo com as mesmas capacidades de transferência de dados. Em ambos os casos, a transferência de dados é gerida pelo dispositivo.
- **Azure Data Factory** – Data Factory deve ser usada para escalar uma operação de transferência, e se houver necessidade de capacidades de orquestração e monitorização de nível empresarial. Utilize o Data Factory para transferir regularmente ficheiros entre vários serviços do Azure, no local ou uma combinação dos dois. Com o Data Factory, pode criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que ingerem dados a partir de diferentes arquivos de dados e automatizam o movimento e a transformação de dados.

## <a name="comparison-of-key-capabilities"></a>Comparação das capacidades-chave

As tabelas a seguir em que resumem as diferenças nas capacidades-chave para as opções recomendadas.

### <a name="moderate-network-bandwidth"></a>Largura de banda de rede moderada

Se utilizar a transferência de dados offline, utilize a tabela seguinte para compreender as diferenças nas capacidades-chave.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Importação/Exportação                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Tamanho dos dados**                    |    Até 35 TBs                 |    Até 80 TBs por dispositivo                       |    Até 800 TB por dispositivo               |    Variável                            |
|    **Tipo de dados**                    |    Blobs do Azure                  |    Blobs do Azure<br>Ficheiros do Azure                    |    Blobs do Azure<br>Ficheiros do Azure            |    Blobs do Azure<br>Ficheiros do Azure          |
|    **Fator de forma**                  |    5 SSDs por encomenda             |    1 X 50-lbs. dispositivo do tamanho de desktop por ordem    |    1 X ~500-lbs. grande dispositivo por ordem    |    Até 10 HDDs/SSDs por encomenda        |
|    **Tempo inicial de configuração**               |    Baixo <br>(15 minutos)            |    Baixo a moderado <br> (<30 minutos)               |    Moderado<br>(1-2 horas)               |    Moderado a difícil<br>(variável) |
|    **Enviar dados para a Azure**           |    Sim                          |    Sim                                           |    Sim                                   |    Sim                                 |
|    **Exportar dados do Azure**           |    Não                           |    Não                                            |    Não                                    |    Sim                                 |
|    **Encriptação**                   |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    **Hardware**                     |     Microsoft fornecida          |    Microsoft fornecida                            |    Microsoft fornecida                    |    Cliente fornecido                   |
|    **Interface de rede**            |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    **Integração de parceiros**          |    Algum                         |    [Alto](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Alto](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Algum                                |
|    **Envio**                     |    Microsoft gerido            |    Microsoft gerido                             |    Microsoft gerido                     |    Gerido pelo cliente                    |
| **Use quando os dados se movem**     |Dentro de um limite de comércio|Dentro de um limite de comércio|Dentro de um limite de comércio|Além das fronteiras geográficas, por exemplo, eua à UE|
|    **Preços**                          |    [Preços](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Preços](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preços](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preços](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Se utilizar a transferência de dados on-line, utilize a tabela na secção seguinte para uma largura de banda de alta rede.

### <a name="high-network-bandwidth"></a>Largura de banda de alta rede

|                                     |    Ferramentas AzCopy, <br>Azure PowerShell, <br>CLI do Azure             |    Azure Storage REST APIs, SDKs                   |    Gateway de caixa de dados ou borda da caixa de dados          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    **Tipo de dados**              |    Azure Blobs, Ficheiros Azure, Tabelas Azure    |    Azure Blobs, Ficheiros Azure, Tabelas Azure    |    Azure Blobs, Ficheiros Azure                           |   Suporta mais de 70 conectores de dados para lojas de dados e formatos    |
|    **Fator de forma**            |    Ferramentas de linha de comandos                        |    Interface programática                    |    Microsoft fornece um virtual <br>ou dispositivo físico     |    Serviço no portal Azure                                            |
|    **Configuração inicial de uma vez** |    Fácil               |    Moderado                       |    Fácil (<30 minutos) a moderado (1-2 horas)            |    Extensa                                                          |
|    **Pré-processamento de dados**          |    Não                                        |    Não                                        |    Sim (Com o cálculo Edge)                               |    Sim                                                                |
|    **Transferência de outras nuvens**   |    Não                                        |    Não                                        |    Não                                                    |    Sim                                                                |
|    **Tipo de utilizador**                    |    IT Pro ou dev                                       |    Dev                                       |    Profissional de TI                                                |    Profissional de TI                                                             |
|    **Preços**                      |    Taxas gratuitas e de saída de dados aplicam-se         |    Taxas gratuitas e de saída de dados aplicam-se         |    [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Próximos passos

- [Saiba como transferir dados com Importação/Exportação.](../../import-export/storage-import-export-data-to-blobs.md)
- Compreender como

    - [Transferir dados com o disco da caixa de dados.](../../databox/data-box-disk-quickstart-portal.md)
    - [Transferir dados com a Caixa de Dados.](../../databox/data-box-quickstart-portal.md)
- [Transferir dados com a AzCopy](./storage-use-azcopy-v10.md).
- Compreender como:
    - [Transferir dados com data box gateway](../../databox-gateway/data-box-gateway-deploy-add-shares.md).
    - [Transforme os dados com data box edge antes de enviar para o Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Saiba como transferir dados com a Azure Data Factory.](../../data-factory/quickstart-create-data-factory-portal.md)
- Utilize as APIs REST para transferir dados

    - [Em .NET](/dotnet/api/overview/azure/storage)
    - [Em Java](/java/api/overview/azure/storage)
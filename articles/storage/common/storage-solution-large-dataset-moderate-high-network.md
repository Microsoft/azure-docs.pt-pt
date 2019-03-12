---
title: Opções para grandes conjuntos de dados entre moderada e alta largura de banda de transferência de dados do Azure | Documentos da Microsoft
description: Saiba como escolher uma solução do Azure para a transferência de dados, quando tem largura de banda de rede entre moderada e alta no seu ambiente e estiver a planear transferir grandes conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 4a8a014b365974bb8c138c74197d3d89cc63e42e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771985"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Transferência de dados para grandes conjuntos de dados com moderada para elevada largura de banda
 
Este artigo fornece uma descrição geral da transferência de dados soluções quando tem largura de banda de rede entre moderada e alta no seu ambiente e estiver a planear transferir grandes conjuntos de dados. O artigo também descreve as opções de transferência de dados recomendada e a matriz de respetivas capacidades-chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, aceda a [escolher uma solução de transferência de dados do Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrição do cenário

Grandes conjuntos de dados referem-se a tamanhos de dados por ordem de TB para PBs. Moderada para elevada largura de banda refere-se para 100 Mbps para 10 Gbps.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas neste cenário dependem se tiver de largura de banda de rede moderada ou alta largura de banda.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Largura de banda de rede moderada (100 Mbps - 1 Gbps)

Com largura de banda de rede moderada, precisa projetar o tempo de transferência de dados através da rede.

Utilize a seguinte tabela para estimar o tempo e com base nisso, escolha entre uma transferência offline ou sobre a transferência de rede. A tabela mostra a hora prevista para transferência de dados de rede, para várias larguras de banda disponível da rede (partindo do princípio de 90% da utilização).  

![Transferência de rede ou transferência offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Se a transferência de rede está projetada para ser muito lento, deve usar um dispositivo físico. Neste caso, as opções recomendadas são os dispositivos de transferência offline da família de Azure Data Box ou importar/exportar do Azure com seus próprios discos.

    - **O Azure Data Box família para transferências offline** – utilizar dispositivos a partir de dispositivos de fornecido pelo Microsoft Data Box para mover grandes quantidades de dados para o Azure quando está limitado pelo tempo, disponibilidade de rede ou custos. Copie dados no local através de ferramentas, tal como Robocopy. Dependendo do tamanho de dados que se destina a transferência, pode escolher entre o disco Data Box, caixa de dados ou dados caixa pesada.
    - **Importar/exportar do Azure** – serviço de importação/exportação de utilização do Azure envie as suas próprias unidades de disco para importar com segurança grandes quantidades de dados para o armazenamento de Blobs do Azure e ficheiros do Azure. Este serviço também pode ser utilizado para transferir dados do armazenamento de Blobs do Azure para unidades de disco e disponibilize aos seus sites no local.

- Se a transferência de rede está projetada para ser razoável, então pode usar qualquer uma das seguintes ferramentas detalhadas [alta largura de banda](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Alta largura de banda (1 Gbps - 100 Gbps)

Se a largura de banda de rede disponível é alta, utilize uma das seguintes ferramentas.

- **AzCopy** - Utilize esta ferramenta da linha de comandos para copiar facilmente os dados de e para os Blobs do Azure, ficheiros e o armazenamento com um desempenho ideal de tabelas. AzCopy oferece suporte a simultaneidade e paralelismo e a capacidade para retomar as operações de cópia quando interrompido.
- **Azure Storage REST APIs/SDKs** – ao criar uma aplicação, pode desenvolver a aplicação em relação a APIs de REST de armazenamento do Azure e utilizar os SDKs do Azure disponível em vários idiomas.
- **O Azure Data Box família para transferências online** – Edge de caixa de dados e dados de caixa de Gateway são dispositivos de rede online que podem mover dados para dentro e fora do Azure. Utilize dados de caixa de borda precisa de um dispositivo físico quando existe um simultâneas para ingestão contínua e o processamento prévio do antes de dados para carregar. Gateway de caixa de dados é uma versão virtual do dispositivo com as mesmas capacidades de transferência de dados. Em cada caso, a transferência de dados é gerida pelo dispositivo.
- **O Azure Data Factory** – Data Factory deve ser utilizado para aumentar horizontalmente uma operação de transferência, e se for necessário para o nível de orquestração e o enterprise capacidades de monitorização. Utilize o Data Factory para regularmente transferir ficheiros entre vários serviços do Azure, no local ou uma combinação dos dois. com o Data Factory, pode criar e agendar condicionada por dados fluxos de trabalho (denominados pipelines) que ingerir dados de arquivos de dados diferentes e automatizam o movimento de dados e transformação de dados.

## <a name="comparison-of-key-capabilities"></a>Comparação dos principais recursos

As tabelas seguintes resumem as diferenças nos principais capacidades para as opções recomendadas.

### <a name="moderate-network-bandwidth"></a>Largura de banda de rede moderada

Se utilizar a transferência de dados offline, utilize a tabela seguinte para compreender as diferenças nos recursos de chave.

|                                     |    Disco do Data Box (pré-visualização)    |    Data Box                                      |    Dados caixa pesadas (pré-visualização)              |    Importação/Exportação                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Tamanho dos dados                        |    Até 35 TB                 |    Até 80 TB por dispositivo                       |    Até 800 TB por dispositivo               |    Variável                            |
|    Tipo de dados                        |    Blobs do Azure                  |    Blobs do Azure<br>Ficheiros do Azure                    |    Blobs do Azure<br>Ficheiros do Azure            |    Blobs do Azure<br>Ficheiros do Azure          |
|    Fator de formulário                      |    5 SSDs por ordem             |    1 x 50-lbs. dispositivo de porte no ambiente de trabalho de mensagens em fila por ordem    |    1 X ~ 500-lbs. dispositivo grandes por ordem    |    Até 10 HDDs/SSDs por ordem        |
|    Tempo de configuração inicial               |    Baixa <br>(15 min)            |    Baixo a moderado <br> (< 30 minutos)               |    Moderado<br>(1 a 2 horas)               |    Moderar a difícil<br>(variável) |
|    Enviar dados para o Azure               |    Sim                          |    Sim                                           |    Sim                                   |    Sim                                 |
|    Exportar dados do Azure           |    Não                           |    Não                                            |    Não                                    |    Sim                                 |
|    Encriptação                       |    AES 128 bits                  |    AES de 256 bits                                   |    AES de 256 bits                           |    AES 128 bits                         |
|    Hardware                         |     Microsoft fornecido          |    Microsoft fornecido                            |    Microsoft fornecido                    |    Cliente fornecido                   |
|    Interface de rede                |    3.1/SATA DE USB                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integração de parceiros              |    Alguns                         |    [Alta](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Alta](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Alguns                                |
|    Envio                         |    Gerida pela Microsoft            |    Gerida pela Microsoft                             |    Gerida pela Microsoft                     |    Gerida pelo cliente                    |
| Quando move os dados de utilização         |Num limite de comércio|Num limite de comércio|Num limite de comércio|Através das fronteiras geográficas, por exemplo, da UE|
|    Preços                          |    [Preços](https://azure.microsoft.com/pricing/details/storage/databox/disk/)                    |   [Preços](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preços](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preços](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Se utilizar a transferência de dados online, utilize a tabela na secção seguinte para alta largura de banda.

### <a name="high-network-bandwidth"></a>Largura de banda de rede elevada

|                                     |    AzCopy de ferramentas, <br>Azure PowerShell, <br>CLI do Azure             |    REST APIs, SDKs de armazenamento do Azure                   |    Gateway de caixa de dados ou de extremidade de caixa de dados (pré-visualização)           |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Tipo de dados                  |    Blobs do Azure, os ficheiros do Azure, as tabelas do Azure    |    Blobs do Azure, os ficheiros do Azure, as tabelas do Azure    |    Blobs do Azure, os ficheiros do Azure                           |   Suporta 70 conectores de dados para arquivos de dados e formatos    |
|    Fator de formulário                |    Ferramentas de linha de comandos                        |    Interface programática                    |    Microsoft fornece um virtual <br>ou o dispositivo físico     |    Serviço no portal do Azure                                            |
|    Configuração inicial de uso individual     |    Fácil               |    Moderado                       |    Fácil (< 30 minutos) para moderado (1 a 2 horas)            |    Extensa                                                          |
|    Processamento prévio de dados              |    Não                                        |    Não                                        |    Sim (computação) com o Edge)                               |    Sim                                                                |
|    Transferir a partir de outras clouds       |    Não                                        |    Não                                        |    Não                                                    |    Sim                                                                |
|    Tipo de utilizador                        |    IT Pro ou desenvolvimento                                       |    Dev                                       |    Profissional de TI                                                |    Profissional de TI                                                             |
|    Preços                          |    Gratuito, são aplicáveis encargos de saída de dados         |    Gratuito, são aplicáveis encargos de saída de dados         |    [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Passos Seguintes

- [Aprender a transferir dados com a importação/exportação](/azure/storage/common/storage-import-export-data-to-blobs).
- Compreender como

    - [Transferir dados com dados de caixa de disco](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transferir dados com o Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Transferir dados com AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Compreender como:
    - [Transferir dados com dados de caixa de Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformar dados com o Edge de caixa de dados antes de enviar para o Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Aprender a transferir dados com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Utilizar as APIs REST para transferir dados

    - [No .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [In Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
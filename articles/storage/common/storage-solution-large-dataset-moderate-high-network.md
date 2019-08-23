---
title: Opções do Azure Data Transfer para grandes conjuntos de dados, moderada para alta largura de banda de rede | Microsoft Docs
description: Saiba como escolher uma solução do Azure para transferência de dados quando você tiver uma largura de banda de rede moderada e alta em seu ambiente e estiver planejando transferir grandes conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: f7177a95bdd585ff2822c9ac8c94a85d12f9259b
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900371"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Transferência de dados para grandes conjuntos com largura de banda de rede moderada para alta
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando você tem uma largura de banda de rede de moderada a alta em seu ambiente e está planejando transferir grandes DataSets. O artigo também descreve as opções de transferência de dados recomendadas e a respectiva matriz de capacidade de chave para esse cenário.

Para entender uma visão geral de todas as opções de transferência de dados disponíveis, vá para [escolher uma solução de transferência de dados do Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrição do cenário

Grandes conjuntos de dados referem-se a tamanhos de dado na ordem de TBs para o PBs. A largura de banda de rede moderada a alta refere-se a 100 Mbps a 10 Gbps.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas neste cenário dependem de se você tem largura de banda de rede moderada ou alta largura de banda de rede.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Largura de banda de rede moderada (100 Mbps-1 Gbps)

Com a largura de banda de rede moderada, você precisa projetar o tempo de transferência de dados pela rede.

Use a tabela a seguir para estimar a hora e com base nela, escolha entre uma transferência offline ou pela transferência de rede. A tabela mostra o tempo projetado para a transferência de dados de rede, para várias larguras de banda de rede disponíveis (supondo 90% de utilização).  

![Transferência de rede ou transferência offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Se a transferência de rede for projetada para ser muito lenta, você deverá usar um dispositivo físico. As opções recomendadas neste caso são os dispositivos de transferência offline da família Azure Data Box ou importação/exportação do Azure usando seus próprios discos.

    - **Família de Azure data box para transferências offline** – use dispositivos de dispositivos data Box fornecidos pela Microsoft para mover grandes quantidades de dados para o Azure quando você estiver limitado por tempo, disponibilidade de rede ou custos. Copie dados locais usando ferramentas como o Robocopy. Dependendo do tamanho dos dados pretendidos para transferência, você pode escolher entre Disco do Data Box, Data Box ou Data Box Heavy.
    - **Importação/exportação do Azure** – use o serviço de importação/exportação do Azure enviando suas próprias unidades de disco para importar com segurança grandes quantidades de dados para o armazenamento de BLOBs do Azure e os arquivos do Azure. Esse serviço também pode ser usado para transferir dados do armazenamento de BLOBs do Azure para as unidades de disco e enviar para seus sites locais.

- Se a transferência de rede for projetada para ser razoável, você poderá usar qualquer uma das ferramentas a seguir detalhadas em [alta largura de banda de rede](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Alta largura de banda de rede (1 Gbps-100 Gbps)

Se a largura de banda de rede disponível estiver alta, use uma das ferramentas a seguir.

- **AzCopy** – Use essa ferramenta de linha de comando para copiar facilmente dados de e para BLOBs, arquivos e armazenamento de tabelas do Azure com desempenho ideal. O AzCopy dá suporte à simultaneidade e ao paralelismo e à capacidade de retomar operações de cópia quando interrompido.
- **APIs/SDKS REST do armazenamento do Azure** – ao criar um aplicativo, você pode desenvolver o aplicativo em relação às APIs REST do armazenamento do Azure e usar os SDKs do Azure oferecidos em vários idiomas.
- **Família de Azure data box para transferências online** – Data Box Edge e gateway do data box são dispositivos de rede online que podem mover dados para dentro e fora do Azure. Use Data Box Edge dispositivo físico quando houver uma necessidade simultânea de ingestão contínua e pré-processamento dos dados antes do carregamento. Gateway do Data Box é uma versão virtual do dispositivo com os mesmos recursos de transferência de dados. Em cada caso, a transferência de dados é gerenciada pelo dispositivo.
- **Azure data Factory** – data Factory deve ser usado para escalar horizontalmente uma operação de transferência e, se houver a necessidade de recursos de monitoramento de nível empresarial e orquestração. Use Data Factory para transferir regularmente arquivos entre vários serviços do Azure, locais ou uma combinação dos dois. com Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que ingerirão dados de armazenamentos de dados diferentes e automatizar a movimentação de dados e a transformação de dados.

## <a name="comparison-of-key-capabilities"></a>Comparação dos principais recursos

As tabelas a seguir resumem as diferenças nos principais recursos para as opções recomendadas.

### <a name="moderate-network-bandwidth"></a>Largura de banda de rede moderada

Se estiver usando a transferência de dados offline, use a tabela a seguir para entender as diferenças nos principais recursos.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Importação/Exportação                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Tamanho dos dados                        |    Até 35 TBs                 |    Até 80 TBs por dispositivo                       |    Até 800 TB por dispositivo               |    Variável                            |
|    Tipo de dados                        |    BLOBs do Azure                  |    BLOBs do Azure<br>Ficheiros do Azure                    |    BLOBs do Azure<br>Ficheiros do Azure            |    BLOBs do Azure<br>Ficheiros do Azure          |
|    Fator forma                      |    5 SSDs por pedido             |    1 X 50-lbs. dispositivo de tamanho de área de trabalho por pedido    |    1 X ~ 500-lbs. dispositivo grande por pedido    |    Até 10 HDDs/SSDs por pedido        |
|    Tempo de configuração inicial               |    Baixa <br>(15 min)            |    Baixo para moderado <br> (< 30 minutos)               |    Moderado<br>(1-2 horas)               |    Moderado a difícil<br>Ela |
|    Enviar dados para o Azure               |    Sim                          |    Sim                                           |    Sim                                   |    Sim                                 |
|    Exportar dados do Azure           |    Não                           |    Não                                            |    Não                                    |    Sim                                 |
|    Encriptação                       |    AES 128 bits                  |    AES 256 bits                                   |    AES 256 bits                           |    AES 128 bits                         |
|    Hardware                         |     Fornecido pela Microsoft          |    Fornecido pela Microsoft                            |    Fornecido pela Microsoft                    |    Fornecido pelo cliente                   |
|    Interface de rede                |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integração de parceiros              |    Alguns                         |    [Elevada](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Elevada](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Alguns                                |
|    Envio                         |    Gerenciado pela Microsoft            |    Gerenciado pela Microsoft                             |    Gerenciado pela Microsoft                     |    Gerenciado pelo cliente                    |
| Usar quando os dados forem movidos         |Dentro de um limite de comércio|Dentro de um limite de comércio|Dentro de um limite de comércio|Entre limites geográficos, por exemplo, US para a UE|
|    Preços                          |    [Preços](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Preços](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preços](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preços](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Se estiver usando transferência de dados online, use a tabela na seção a seguir para alta largura de banda de rede.

### <a name="high-network-bandwidth"></a>Largura de banda de rede elevada

|                                     |    Ferramentas AzCopy, <br>Azure PowerShell, <br>CLI do Azure             |    APIs REST do armazenamento do Azure, SDKs                   |    Gateway do Data Box ou Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Tipo de dados                  |    BLOBs do Azure, arquivos do Azure, tabelas do Azure    |    BLOBs do Azure, arquivos do Azure, tabelas do Azure    |    BLOBs do Azure, arquivos do Azure                           |   Dá suporte a mais de 70 conectores de dados para armazenamentos de dados e formatos    |
|    Fator forma                |    Ferramentas de linha de comandos                        |    Interface programática                    |    A Microsoft fornece um virtual <br>ou dispositivo físico     |    Serviço no portal do Azure                                            |
|    Configuração de uso único inicial     |    Fácil               |    Moderado                       |    Fácil (< 30 minutos) a moderado (1-2 horas)            |    Ampla                                                          |
|    Pré-processamento de dados              |    Não                                        |    Não                                        |    Sim (com computação de borda)                               |    Sim                                                                |
|    Transferência de outras nuvens       |    Não                                        |    Não                                        |    Não                                                    |    Sim                                                                |
|    Tipo de utilizador                        |    Profissional de ti ou desenvolvimento                                       |    Dev                                       |    Profissional de TI                                                |    Profissional de TI                                                             |
|    Preços                          |    Gratuito, as cobranças de saída de dados se aplicam         |    Gratuito, as cobranças de saída de dados se aplicam         |    [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Passos Seguintes

- [Saiba como transferir dados com importação/exportação](/azure/storage/common/storage-import-export-data-to-blobs).
- Entenda como

    - [Transferir dados com disco do data Box](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transferir dados com data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Transferir dados com AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Entenda como:
    - [Transferir dados com gateway do data Box](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transforme dados com data Box Edge antes de enviar para o Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Saiba como transferir dados com Azure data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Usar as APIs REST para transferir dados

    - [No .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [Em Java](https://docs.microsoft.com/java/api/overview/azure/storage)

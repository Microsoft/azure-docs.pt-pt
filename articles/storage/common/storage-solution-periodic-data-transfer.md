---
title: Escolha uma solução Azure para transferência periódica de dados. Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados quando está a transferir dados periodicamente.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: fb49802adf6242f445b700d06622d7e6aa336b4d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67357046"
---
# <a name="solutions-for-periodic-data-transfer"></a>Solutions for periodic data transfer (Soluções para a transferência de dados periódica)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando está a transferir dados periodicamente. A transferência periódica de dados sobre a rede pode ser classificada como recorrente em intervalos regulares ou movimento contínuo de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher uma solução de transferência de [dados Azure.](storage-choose-data-transfer-solution.md)

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas para transferência periódica de dados recaem em duas categorias, dependendo se a transferência é recorrente ou contínua.

- **Ferramentas script/programáticas** – Para a transferência de dados que ocorre em intervalos regulares, utilize as ferramentas scriptadas e programáticas tais como AzCopy e Azure Storage REST APIs. Estas ferramentas são direcionadas para profissionais e desenvolvedores de TI.

    - **AzCopy** - Utilize esta ferramenta de linha de comando para copiar facilmente dados de e para o armazenamento de Blobs, Ficheiros e Mesa seletivas com um desempenho ótimo. A AzCopy suporta conmoeda e paralelismo, e a capacidade de retomar as operações de cópia quando interrompida.
    - **Azure Storage REST APIs/SDKs** – Ao construir uma aplicação, pode desenvolver a aplicação contra APIs REST DE Armazenamento Azure e utilizar os SDKs Azure oferecidos em vários idiomas. As APIs rest também podem alavancar a Biblioteca de Movimentos de Dados de Armazenamento Azure projetada especialmente para a cópia de dados de alto desempenho de e para o Azure.

- **Ferramentas contínuas de ingestão** de dados – Para uma ingestão contínua e contínua de dados, pode selecionar um dispositivo de transferência online data Box ou a Azure Data Factory. Estas ferramentas são criadas por profissionais de TI e podem automatizar de forma transparente a transferência de dados.

    - **A Azure Data Factory** – Data Factory deve ser utilizada para escalar uma operação de transferência e, se houver necessidade de capacidades de orquestração e de monitorização de nível empresarial. Utilize a Azure Data Factory para criar um oleoduto em nuvem que transfere regularmente ficheiros entre vários serviços Azure, no local ou uma combinação dos dois. A Azure Data Factory permite-lhe orquestrar fluxos de trabalho baseados em dados que ingerirem dados de lojas de dados díspares e automatizar o movimento de dados e a transformação de dados.
    - **Família Azure Data Box para transferências on-line** - Data Box Edge e Data Box Gateway são dispositivos de rede online que podem mover dados para dentro e para fora do Azure. Data Box Edge utiliza inteligência artificial (AI) ativada por Edge computacionapara pré-processar dados antes de ser carregado. Data Box Gateway é uma versão virtual do dispositivo com as mesmas capacidades de transferência de dados.


## <a name="comparison-of-key-capabilities"></a>Comparação de capacidades-chave

A tabela que se segue resume as diferenças nas capacidades-chave.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Transferência de dados de rede script/programática

| Capacidade                  | AzCopy                                 | APIs de repouso de armazenamento azure       |
|-----------------------------|----------------------------------------|-------------------------------|
| Fator de forma                 | Ferramenta de linha de comando da Microsoft       | Os clientes desenvolvem-se contra o Armazenamento <br> REST APIs usando bibliotecas de clientes Azure |
| Configuração única inicial     | Mínimo                                | Esforço moderado e variável de desenvolvimento    |
| Formato de Dados                 | Blobs Azure, Arquivos Azure, Mesas Azure | Blobs Azure, Arquivos Azure, Mesas Azure   |
| Desempenho                 | Já otimizado                      | Otimizar à medida que se desenvolve                  |
| Preços                     | Aplicam-se gratuitamente as taxas de egress de dados      | Aplicam-se gratuitamente as taxas de egress de dados        |

### <a name="continuous-data-ingestion-over-network"></a>Ingestão contínua de dados sobre a rede

| Funcionalidade                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Fator de forma                                   | Dispositivo virtual             | Dispositivo físico          | Serviço no portal Azure, agente no local                                                            |
| Hardware                                      | O seu hipervisor            | Fornecido pela Microsoft    | ND                                                            |
| Esforço inicial de configuração                          | Baixa (<30 minutos.)            | Moderado (~algumas horas) | Grande (~dias)                                                 |
| Formato de Dados                                   | Azure Blobs, Ficheiros Azure   | Azure Blobs, Ficheiros Azure | [Suporta mais de 70 conectores de dados para lojas e formatos de dados](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Pré-processamento de dados                           | Não                         | Sim, via computação Edge    | Sim                                                           |
| Cache local<br>(para armazenar dados no local)    | Sim                        | Sim                      | Não                                                            |
| Transferência de outras nuvens                    | Não                         | Não                       | Sim                                                           |
| Preços                                       | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Passos seguintes

- [Transferir dados com a AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Mais informações sobre transferência de dados com APIs REST de Armazenamento](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).
- Entenda como:
    - [Transferir dados com Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transforme os dados com data box Edge antes de enviar para o Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Saiba como transferir dados com a Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal)

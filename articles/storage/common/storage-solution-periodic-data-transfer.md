---
title: Escolha uma solução Azure para transferência periódica de dados Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados quando estiver a transferir dados periodicamente.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 9ffa35e158d34a1fc6945ee2730dcf136d13edb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85504343"
---
# <a name="solutions-for-periodic-data-transfer"></a>Solutions for periodic data transfer (Soluções para a transferência de dados periódica)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando está a transferir dados periodicamente. A transferência periódica de dados sobre a rede pode ser classificada como recorrente em intervalos regulares ou movimento contínuo de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher [uma solução de transferência de dados Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas para a transferência periódica de dados caem em duas categorias, dependendo se a transferência é recorrente ou contínua.

- **Ferramentas scriptadas/programáticas** – Para transferência de dados que ocorra em intervalos regulares, utilize as ferramentas scriptadas e programáticas, tais como APIs de AzCopy e Azure Storage REST. Estas ferramentas são direcionadas para profissionais de TI e desenvolvedores.

    - **AzCopy** - Utilize esta ferramenta de linha de comando para copiar facilmente dados de e para Azure Blobs, Ficheiros e Armazenamento de Mesa com o melhor desempenho. A AzCopy suporta a concordância e o paralelismo, e a capacidade de retomar as operações de cópia quando interrompidas.
    - **Azure Storage REST APIs/SDKs** – Ao construir uma aplicação, pode desenvolver a aplicação contra ASPis de ARMAZENAMENTO Azure e utilizar os SDKs Azure oferecidos em vários idiomas. As APIs REST também podem alavancar a Biblioteca de Movimentos de Dados de Armazenamento Azure projetada especialmente para a cópia de alto desempenho de dados de e para Azure.

- **Ferramentas contínuas de ingestão** de dados – Para ingestão contínua e contínua de dados, pode selecionar um dos dispositivos de transferência online da Data Box ou a Azure Data Factory. Estas ferramentas são criadas por profissionais de TI e podem automatizar de forma transparente a transferência de dados.

    - **Azure Data Factory** – Data Factory deve ser usada para escalar uma operação de transferência, e se houver necessidade de capacidades de orquestração e monitorização de nível empresarial. Utilize a Azure Data Factory para configurar um pipeline em nuvem que transfere regularmente ficheiros entre vários serviços Azure, no local ou uma combinação dos dois. A Azure Data Factory permite-lhe orquestrar fluxos de trabalho baseados em dados que ingerem dados de lojas de dados díspares e automatizam o movimento de dados e a transformação de dados.
    - **Família Azure Data Box para transferências on-line** - Data Box Edge e Data Box Gateway são dispositivos de rede online que podem mover dados para dentro e para fora do Azure. Data Box Edge utiliza o cálculo edge habilitado para a inteligência artificial (IA) para pré-processar dados antes do upload. Data Box Gateway é uma versão virtual do dispositivo com as mesmas capacidades de transferência de dados.


## <a name="comparison-of-key-capabilities"></a>Comparação das capacidades-chave

A tabela seguinte resume as diferenças nas capacidades-chave.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Transferência de dados de rede scripted/programática

| Funcionalidade                  | AzCopy                                 | Azure Storage REST APIs       |
|-----------------------------|----------------------------------------|-------------------------------|
| Fator de forma                 | Ferramenta de linha de comando da Microsoft       | Os clientes desenvolvem-se contra o Armazenamento <br> REST APIs usando bibliotecas de clientes Azure |
| Configuração inicial de uma vez     | Mínimo                                | Esforço de desenvolvimento moderado e variável    |
| Formato de Dados                 | Azure Blobs, Ficheiros Azure, Tabelas Azure | Azure Blobs, Ficheiros Azure, Tabelas Azure   |
| Desempenho                 | Já otimizado                      | Otimizar à medida que se desenvolve                  |
| Preços                     | Taxas gratuitas e de saída de dados aplicam-se      | Taxas gratuitas e de saída de dados aplicam-se        |

### <a name="continuous-data-ingestion-over-network"></a>Ingestão contínua de dados sobre rede

| Funcionalidade                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Fator de forma                                   | Dispositivo virtual             | Dispositivo físico          | Serviço no portal Azure, agente no local                                                            |
| Hardware                                      | O seu hipervisor            | Fornecido pela Microsoft    | ND                                                            |
| Esforço inicial de configuração                          | Baixo (<30 minutos.)            | Moderado (~duas horas) | Grande (~dias)                                                 |
| Formato de Dados                                   | Azure Blobs, Ficheiros Azure   | Azure Blobs, Ficheiros Azure | [Suporta mais de 70 conectores de dados para lojas de dados e formatos](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Pré-processamento de dados                           | Não                         | Sim, via edge compute    | Sim                                                           |
| Cache local<br>(para armazenar dados no local)    | Sim                        | Sim                      | Não                                                            |
| Transferência de outras nuvens                    | Não                         | Não                       | Sim                                                           |
| Preços                                       | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Passos seguintes

- [Transferir dados com a AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Mais informações sobre transferência de dados com APIs de repouso de armazenamento](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).
- Compreender como:
    - [Transferir dados com data box gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transforme os dados com data box edge antes de enviar para o Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Saiba como transferir dados com a Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal)

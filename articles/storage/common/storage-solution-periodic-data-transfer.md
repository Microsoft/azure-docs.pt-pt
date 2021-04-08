---
title: Escolha uma solução Azure para transferência periódica de dados| Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados quando estiver a transferir dados periodicamente.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: a15ebd43861e2116ddbb2d9055b289645962e203
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573923"
---
# <a name="solutions-for-periodic-data-transfer"></a>Solutions for periodic data transfer (Soluções para a transferência de dados periódica)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando está a transferir dados periodicamente. A transferência periódica de dados sobre a rede pode ser classificada como recorrente em intervalos regulares ou movimento contínuo de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher [uma solução de transferência de dados Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas para a transferência de dados periódica enquadram-se em duas categorias, consoante a transferência seja recorrente ou contínua.

- **Ferramentas scriptadas/programáticas** – Para transferência de dados que ocorra em intervalos regulares, utilize as ferramentas scriptadas e programáticas, tais como APIs de AzCopy e Azure Storage REST. Estas ferramentas destinam-se a programadores e profissionais de TI.

    - **AzCopy** - Utilize esta ferramenta de linha de comando para copiar facilmente dados de e para Azure Blobs, Ficheiros e Armazenamento de Mesa com o melhor desempenho. O AzCopy suporta simultaneidade e paralelismo, além da capacidade de retomar operações de cópia interrompidas.
    - **Azure Storage REST APIs/SDKs** – Ao construir uma aplicação, pode desenvolver a aplicação contra ASPis de ARMAZENAMENTO Azure e utilizar os SDKs Azure oferecidos em vários idiomas. As APIs REST também podem alavancar a Biblioteca de Movimentos de Dados de Armazenamento Azure projetada especialmente para a cópia de alto desempenho de dados de e para Azure.

- **Ferramentas contínuas de ingestão** de dados – Para ingestão contínua e contínua de dados, pode selecionar um dos dispositivos de transferência online da Data Box ou a Azure Data Factory. Estas ferramentas são configuradas por profissionais de TI e podem automatizar de forma transparente a transferência de dados.

    - **Azure Data Factory** – Data Factory deve ser usada para escalar uma operação de transferência, e se houver necessidade de capacidades de orquestração e monitorização de nível empresarial. Utilize o Azure Data Factory para configurar um pipeline na cloud que transfere regularmente ficheiros entre vários serviços do Azure, no local ou uma combinação dos dois. O Azure Data Factory permite orquestrar fluxos de trabalho condicionados por dados que ingerem dados a partir de diferentes arquivos de dados, além de automatizar o movimento e a transformação de dados.
    - **Família Azure Data Box para transferências on-line** - Data Box Edge e Data Box Gateway são dispositivos de rede online que podem mover dados para dentro e para fora do Azure. O Data Box Edge utiliza computação edge ativada por inteligência artificial (IA) para pré-processar os dados antes do carregamento. O Data Box Gateway é uma versão virtual do dispositivo com as mesmas capacidades de transferência de dados.


## <a name="comparison-of-key-capabilities"></a>Comparação das capacidades-chave

A tabela seguinte resume as diferenças entre as principais capacidades.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Transferência de dados de rede scripted/programática

| Funcionalidade                  | AzCopy                                 | APIs REST do Armazenamento do Microsoft Azure       |
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
| Formato de Dados                                   | Azure Blobs, Ficheiros Azure   | Azure Blobs, Ficheiros Azure | [Suporta mais de 70 conectores de dados para lojas de dados e formatos](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| Pré-processamento de dados                           | No                         | Sim, via edge compute    | Yes                                                           |
| Cache local<br>(para armazenar dados no local)    | Yes                        | Yes                      | No                                                            |
| Transferência de outras nuvens                    | No                         | No                       | Yes                                                           |
| Preços                                       | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Passos seguintes

- [Transferir dados com a AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Mais informações sobre transferência de dados com APIs de repouso de armazenamento](/dotnet/api/overview/azure/storage).
- Compreender como:
    - [Transferir dados com data box gateway](../../databox-gateway/data-box-gateway-deploy-add-shares.md).
    - [Transforme os dados com data box edge antes de enviar para o Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Saiba como transferir dados com a Azure Data Factory.](../../data-factory/tutorial-bulk-copy-portal.md)
---
title: Escolha uma solução do Azure para a transferência de dados periódica | Documentos da Microsoft
description: Saiba como escolher uma solução do Azure para a transferência de dados, quando estiver transferindo dados periodicamente.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: ba6d5e20f0d6a004fd85d7e9dc57584547d52ba3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772862"
---
# <a name="solutions-for-periodic-data-transfer"></a>Soluções para a transferência de dados periódica
 
Este artigo fornece uma descrição geral da transferência de dados soluções quando estiver transferindo dados periodicamente. Transferência de dados periódica através da rede pode ser categorizada como recorrente em intervalos regulares ou de movimento de dados contínua. O artigo também descreve as opções de transferência de dados recomendada e a matriz de respetivas capacidades-chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, aceda a [escolher uma solução de transferência de dados do Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas para a transferência de dados periódica enquadram-se em duas categorias, dependendo da transferência é recorrente ou contínua.

- **Ferramentas de script/programática** – para transferência de dados que ocorre em intervalos regulares, para utilização com script e programática ferramentas como o AzCopy e o armazenamento do Azure, REST APIs. Essas ferramentas são voltadas para profissionais de TI e desenvolvedores.

    - **AzCopy** - Utilize esta ferramenta da linha de comandos para copiar facilmente os dados de e para os Blobs do Azure, ficheiros e o armazenamento com um desempenho ideal de tabelas. AzCopy oferece suporte a simultaneidade e paralelismo e a capacidade para retomar as operações de cópia quando interrompido.
    - **Azure Storage REST APIs/SDKs** – ao criar uma aplicação, pode desenvolver a aplicação em relação a APIs de REST de armazenamento do Azure e utilizar os SDKs do Azure disponível em vários idiomas. As APIs REST também pode aproveitar a biblioteca de movimento do dados do Azure armazenamento concebido especialmente para a cópia de alto desempenho dos dados para e do Azure.

- **Ferramentas de ingestão de dados contínuos** – contínua, em curso para ingestão de dados, pode selecionar um dispositivo de transferência online do Data Box ou o Azure Data Factory. Essas ferramentas são configuradas por profissionais de TI e transparente podem automatizar a transferência de dados.

    - **O Azure Data Factory** – Data Factory deve ser utilizado para aumentar horizontalmente uma operação de transferência, e se for necessário para o nível de orquestração e o enterprise capacidades de monitorização. Utilize o Azure Data Factory para configurar um pipeline de cloud que regularmente transfere arquivos entre vários serviços do Azure, no local ou uma combinação dos dois. O Azure Data Factory permite que orquestra condicionada por dados os fluxos de trabalho ingerir dados de arquivos de dados diferentes e automatizam o movimento de dados e transformação de dados.
    - **O Azure Data Box família para transferências online** -Edge de caixa de dados e dados de caixa de Gateway são dispositivos de rede online que podem mover dados para dentro e fora do Azure. Edge de caixa de dados utiliza inteligência artificial (IA)-capacidade de computação de borda processar previamente os dados antes de carregar. Gateway de caixa de dados é uma versão virtual do dispositivo com as mesmas capacidades de transferência de dados.


## <a name="comparison-of-key-capabilities"></a>Comparação dos principais recursos

A tabela seguinte resume as diferenças nas capacidades principais.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Transferência de dados de rede com script/Programmatic

| Capacidade                  | AzCopy                                 | APIs REST do armazenamento do Azure       |
|-----------------------------|----------------------------------------|-------------------------------|
| Fator de formulário                 | Ferramenta de linha de comando da Microsoft       | Os clientes desenvolvem no armazenamento <br> APIs REST com bibliotecas de cliente do Azure |
| Configuração inicial de uso individual     | Mínimo                                | Esforço de desenvolvimento moderada, a variável    |
| Formato de dados                 | Blobs do Azure, os ficheiros do Azure, as tabelas do Azure | Blobs do Azure, os ficheiros do Azure, as tabelas do Azure   |
| Desempenho                 | Já otimizado                      | Otimizar ao desenvolver                  |
| Preços                     | Gratuito, são aplicáveis encargos de saída de dados      | Gratuito, são aplicáveis encargos de saída de dados        |

### <a name="continuous-data-ingestion-over-network"></a>Ingestão de dados contínua através de rede

| Funcionalidade                                       | Gateway de caixa de dados (pré-visualização) | Edge de caixa de dados (pré-visualização)  | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Fator de formulário                                   | Dispositivo virtual             | Dispositivo físico          | Serviço no portal do Azure, o agente no local                                                            |
| Hardware                                      | O hipervisor            | Fornecido pela Microsoft    | N/D                                                            |
| Esforço de configuração inicial                          | Baixa (< 30 min.)            | Moderado (~ acoplar horas) | Grande (~ dias)                                                 |
| Formato de dados                                   | Blobs do Azure, os ficheiros do Azure   | Blobs do Azure, os ficheiros do Azure | [Suporta 70 conectores de dados para arquivos de dados e formatos](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Processamento prévio de dados                           | Não                         | Sim, através de computação do Edge    | Sim                                                           |
| Cache local<br>(para armazenar dados no local)    | Sim                        | Sim                      | Não                                                            |
| Transferir a partir de outras clouds                    | Não                         | Não                       | Sim                                                           |
| Preços                                       | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Passos Seguintes

- [Transferir dados com AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Obter mais informações sobre dados de transferência com APIs REST do armazenamento](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
- Compreender como:
    - [Transferir dados com dados de caixa de Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformar dados com o Edge de caixa de dados antes de enviar para o Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Aprender a transferir dados com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).

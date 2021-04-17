---
title: Comparação de ferramentas de migração de armazenamento Azure - Dados não estruturados
description: Funcionalidade básica e comparação entre ferramentas utilizadas para a migração de dados não estruturados
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 862feace6aab4f49ad3482c4ccd6510669c876a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576538"
---
# <a name="comparison-matrix"></a>Matriz de comparação

A seguinte matriz de comparação mostra a funcionalidade básica de diferentes ferramentas que podem ser usadas para a migração de dados não estruturados. 

## <a name="supported-azure-services"></a>Serviços do Azure suportados

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nome da solução**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Suporte a ficheiros Azure (todos os níveis)** | Yes                          | Yes                      | Yes            | Yes                            |
| **Suporte a ficheiros Azure NetApp**      | No                           | Yes                      | Yes            | Yes                            |
| **Suporte Azure Blob Hot / Cool**   | No                           | Sim (via pré-visualização NFS)    | Yes            | Yes                            |
| **Suporte de nível Azure Blob Archive** | No                           | No                       | No             | Sim (como destino de migração) |
| **Suporte de armazenamento do Lago de Dados Azure** | No                           | No                       | No             | No                             |
| **Origens Suportadas**      | Windows Server 2012 R2 e até | Sistemas de ficheiros em nuvem nas & nas NA | Qualquer NAS e S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Protocolos suportados (fonte/destino)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome da solução**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Yes | Yes | Yes | Yes |
| **SMB 3.0**       | Yes | Yes | Yes | Yes |
| **SMB 3.1**       | Yes | Yes | Yes | Yes |
| **NFS v3**        | No  | Yes | Yes | Yes |
| **NFS v4.1**      | No  | Yes | No  | Yes |
| **Blob REST API** | No  | No  | Yes | Yes |
| **S3**            | No  | Yes | Yes | Yes |

## <a name="extended-features"></a>Características estendidas

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nome da solução**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Remapping UID /SID**                   | No  | Yes                        | Yes | No                             |
| **Remapping protocolo ACL**                | No  | No                         | No  | No                             |
| **Suporte DFS**                           | Yes | Yes                        | Yes | Yes                            |
| **Apoio de estrangulamento**                    | Yes | Yes                        | Yes | Yes                            |
| **Exclusões de padrões de ficheiros**               | No  | Yes                        | Yes | Sim (usando a funcionalidade de cópia) |
| **Suporte para atributos de ficheiros seletivos** | Yes | Yes                        | Yes | Sim (para atributos estendidos)  |
| **Eliminar propagações**                   | Yes | Yes                        | Yes | Yes                            |
| **Siga as junções NTFS**                 | No  | Yes                        | No  | Yes                            |
| **Substituição do proprietário e dono do grupo**    | Yes | Yes                        | Yes | No                             |
| **Relatório de cadeia de custódia**            | No  | Yes                        | No  | Yes                            |
| **Suporte para fluxos de dados alternativos**    | No  | Yes                        | Yes | No                             |
| **Agendamento para a migração**              | No  | Yes                        | Yes | Yes                            |
| **Preservação da ACL**                        | No  | Yes                        | Yes | Yes                            |
| **Suporte DACL**                          | Yes | Yes                        | Yes | Yes                            |
| **Suporte SACL**                          | Yes | Yes                        | Yes | No                             |
| **Preservação do tempo de acesso**                | Yes | Yes                        | Yes | Yes                            |
| **Preservação do tempo modificado**              | Yes | Yes                        | Yes | Yes                            |
| **Preservando o tempo de criação**              | No  | Yes                        | Yes | Yes                            |
| **Suporte a caixa de dados Azure**       | Yes | Yes                        | No  | No                             |
| **Migração de instantâneos**                | No  | Manual                     | Yes | No                             |
| **Suporte simbólico de ligação**                 | No  | Yes                        | No  | Yes                            |
| **Suporte de ligação rígida**                     | No  | Migrado como ficheiros separados | Yes | Yes                            |
| **Suporte para ficheiros abertos /bloqueados**       | Yes | Yes                        | Yes | Yes                            |
| **Migração incremental**                 | Yes | Yes                        | Yes | Yes                            |
| **Suporte de transição**                    | No  | Yes                        | Yes | Não (apenas manual)               |
| **[Outras funcionalidades](#other-features)**         | [Ligação](#azure-file-sync)| [Ligação](#dobimigrate) | [Ligação](#data-mobility-and-migration) | [Ligação](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Avaliação e reporte

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome da solução**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Capacidade**                        | No      | Yes | Yes | Yes            |
| **# de ficheiros / pastas**            | No      | Yes | Yes | Yes            |
| **Distribuição etária ao longo do tempo**      | No      | Yes | Yes | Yes            |
| **Tempo de acesso**                     | No      | Yes | Yes | Yes            |
| **Tempo modificado**                   | No      | Yes | Yes | Yes            |
| **Tempo de criação**                   | No      | Yes | Yes | Yes            |
| **Por ficheiro/ estado de relatório de objeto** | Parcial | Yes | Yes | Yes            |

## <a name="licensing"></a>Licenciamento

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome da solução**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | N / A | Yes | Yes | Yes |
| **Compromisso Azure** | Yes   | Yes | Yes | Yes |

## <a name="other-features"></a>Outras funcionalidades

### <a name="azure-file-sync"></a>Azure File Sync

- Validação interna do haxixe

### <a name="dobimigrate"></a>DobiMigrate

- Pré-verificação da migração
- Planeamento migratório
- Dry Run para corte sobre testes
- Detetar e alertar sobre a atividade do utilizador do lado do alvo antes de cortar
- Migrações orientadas pela política
- Iterações de cópia agendadas
- Opções configuráveis para lidar com a segurança do diretório de raiz
- Execuções de verificação a pedido
- Os dados lêem a verificação da origem e do destino
- Fluxo de trabalho gráfico e interativo de manipulação de erros
- Capacidade de restringir certas operações de propagação como eliminações e atualizações
- Capacidade de preservar o tempo de acesso na fonte (além do destino)
- Capacidade de executar o revés para a fonte durante a transição de migração
- Capacidade de migrar atributos de ficheiro SMB selecionados
- Capacidade de limpar descritores de segurança NTFS
- Capacidade de anular permissões NFSv3 e escrever novos bits de modo para o alvo
- Capacidade de converter projeto NFSv3 POSIX ACLS para NFSv4 ACLS
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Mobilidade de Dados e Migração

- Validação de haxixe

### <a name="intelligent-data-management"></a>Gestão inteligente de dados

- Migrações baseadas em projetos/diretórios
- Repetição automática de falhas
- Avaliação/reporte: Tipos de ficheiros, tamanho de ficheiro, baseado em projeto
- Avaliação/ reporte: Pesquisas personalizadas baseadas em metadados
- Solução completa de gestão do ciclo de vida dos dados para arquivo, replicação, análise
- Aceder a análises baseadas no tempo em dados Blob, S3
- Identificação
- Suporte 24 x 7 x 365
- Validação de haxixe

*A lista foi verificada pela última vez em 31 de março de 2021.*

## <a name="see-also"></a>Ver também

- [Visão geral da migração de armazenamento](../../../common/storage-migration-overview.md)
- [Escolher uma solução do Azure para a transferência de dados](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migrar para partilhas de ficheiros do Azure](/azure/storage/files/storage-files-migration-overview)
- [Migrar para o armazenamento do Lago de Dados com a plataforma WANdisco LiveData para o Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Copiar ou mover dados para o Azure Storage com a AzCopy](https://aka.ms/azcopy)
- [Migrar grandes conjuntos de dados para o Azure Blob Storage com AzReplicate (aplicação da amostra)](https://github.com/Azure/AzReplicate/tree/master/)

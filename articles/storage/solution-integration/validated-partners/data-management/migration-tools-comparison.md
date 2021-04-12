---
title: Comparação de ferramentas de migração de armazenamento Azure - Dados não estruturados
description: Funcionalidade básica e comparação entre ferramentas utilizadas para a migração de dados não estruturados
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15daeb0e6bf320a0727d8e6ea502063a30e67ad0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231588"
---
# <a name="comparison-matrix"></a>Matriz de comparação

A seguinte matriz de comparação mostra a funcionalidade básica de diferentes ferramentas que podem ser usadas para a migração de dados não estruturados. 

## <a name="supported-azure-services"></a>Serviços do Azure suportados

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nome da solução**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Suporte a ficheiros Azure (todos os níveis)** | Sim                          | Sim                      | Sim            | Sim                            |
| **Suporte a ficheiros Azure NetApp**      | Não                           | Sim                      | Sim            | Sim                            |
| **Suporte Azure Blob Hot / Cool**   | Não                           | Sim (via pré-visualização NFS)    | Sim            | Sim                            |
| **Suporte de nível Azure Blob Archive** | Não                           | Não                       | Não             | Sim (como destino de migração) |
| **Suporte de armazenamento do Lago de Dados Azure** | Não                           | Não                       | Não             | Não                             |
| **Origens Suportadas**      | Windows Server 2012 R2 e até | Sistemas de ficheiros em nuvem nas & nas NA | Qualquer NAS e S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Protocolos suportados (fonte/destino)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome da solução**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Sim | Sim | Sim | Sim |
| **SMB 3.0**       | Sim | Sim | Sim | Sim |
| **SMB 3.1**       | Sim | Sim | Sim | Sim |
| **NFS v3**        | Não  | Sim | Sim | Sim |
| **NFS v4.1**      | Não  | Sim | Não  | Sim |
| **Blob REST API** | Não  | Não  | Sim | Sim |
| **S3**            | Não  | Sim | Sim | Sim |

## <a name="extended-features"></a>Características estendidas

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nome da solução**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Remapping UID /SID**                   | Não  | Sim                        | Sim | Não                             |
| **Remapping protocolo ACL**                | Não  | Não                         | Não  | Não                             |
| **Suporte DFS**                           | Sim | Sim                        | Sim | Sim                            |
| **Apoio de estrangulamento**                    | Sim | Sim                        | Sim | Sim                            |
| **Exclusões de padrões de ficheiros**               | Não  | Sim                        | Sim | Sim (usando a funcionalidade de cópia) |
| **Suporte para atributos de ficheiros seletivos** | Sim | Sim                        | Sim | Sim (para atributos estendidos)  |
| **Eliminar propagações**                   | Sim | Sim                        | Sim | Sim                            |
| **Siga as junções NTFS**                 | Não  | Sim                        | Não  | Sim                            |
| **Substituição do proprietário e dono do grupo**    | Sim | Sim                        | Sim | Não                             |
| **Relatório de cadeia de custódia**            | Não  | Sim                        | Não  | Sim                            |
| **Suporte para fluxos de dados alternativos**    | Não  | Sim                        | Sim | Não                             |
| **Agendamento para a migração**              | Não  | Sim                        | Sim | Sim                            |
| **Preservação da ACL**                        | Não  | Sim                        | Sim | Sim                            |
| **Suporte DACL**                          | Sim | Sim                        | Sim | Sim                            |
| **Suporte SACL**                          | Sim | Sim                        | Sim | Não                             |
| **Preservação do tempo de acesso**                | Sim | Sim                        | Sim | Sim                            |
| **Preservação do tempo modificado**              | Sim | Sim                        | Sim | Sim                            |
| **Preservando o tempo de criação**              | Não  | Sim                        | Sim | Sim                            |
| **Suporte a caixa de dados Azure**       | Sim | Sim                        | Não  | Não                             |
| **Migração de instantâneos**                | Não  | Manual                     | Sim | Não                             |
| **Suporte simbólico de ligação**                 | Não  | Sim                        | Não  | Sim                            |
| **Suporte de ligação rígida**                     | Não  | Migrado como ficheiros separados | Sim | Sim                            |
| **Suporte para ficheiros abertos /bloqueados**       | Sim | Sim                        | Sim | Sim                            |
| **Migração incremental**                 | Sim | Sim                        | Sim | Sim                            |
| **Suporte de transição**                    | Não  | Sim                        | Sim | Não (apenas manual)               |
| **[Outras funcionalidades](#other-features)**         | [Ligação](#azure-file-sync)| [Ligação](#dobimigrate) | [Ligação](#data-mobility-and-migration) | [Ligação](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Avaliação e reporte

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome da solução**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Capacidade**                        | Não      | Sim | Sim | Sim            |
| **# de ficheiros / pastas**            | Não      | Sim | Sim | Sim            |
| **Distribuição etária ao longo do tempo**      | Não      | Sim | Sim | Sim            |
| **Tempo de acesso**                     | Não      | Sim | Sim | Sim            |
| **Tempo modificado**                   | Não      | Sim | Sim | Sim            |
| **Tempo de criação**                   | Não      | Sim | Sim | Sim (apenas SMB) |
| **Por ficheiro/ estado de relatório de objeto** | Parcial | Sim | Sim | Sim            |

## <a name="licensing"></a>Licenciamento

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dinâmica de Dados](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome da solução**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilidade de Dados e Migração](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestão inteligente de dados](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | N / A | Sim | Sim | Sim |
| **Compromisso Azure** | Sim   | Sim | Sim | Sim |

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

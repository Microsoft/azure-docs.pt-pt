---
title: Visão geral da cópia de segurança offline
description: Conheça os componentes da cópia de segurança offline. Incluem cópiade segurança offline baseada na Caixa de Dados Azure e cópia de segurança offline com base no serviço de importação/exportação Azure.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: a33810c633d8c7ca79c559355935d3c11853bc0a
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160926"
---
# <a name="overview-of-offline-backup"></a>Visão geral da cópia de segurança offline

Este artigo dá uma visão geral da cópia de segurança offline.

As cópias de segurança completas iniciais para o Azure normalmente transferem grandes quantidades de dados on-line e requerem mais largura de banda da rede em comparação com as cópias de segurança subsequentes que transferem apenas alterações incrementais. Escritórios remotos ou datacenters em certas geografias nem sempre têm largura de banda de rede suficiente. Por esta razão, estes backups iniciais demoram vários dias. Durante este período, as cópias de segurança utilizam continuamente a mesma rede que foi disponibilizada para aplicações em execução no centro de dados no local.

O Azure Backup suporta cópias de segurança offline, que transfere os dados iniciais de backup offline, sem a utilização da largura de banda da rede. Fornece um mecanismo para copiar dados de backup em dispositivos de armazenamento físico. Os dispositivos são então enviados para um centro de dados azure próximo e enviados para um cofre dos Serviços de Recuperação. Este processo garante uma transferência robusta de dados de backup sem utilizar qualquer largura de banda da rede.

## <a name="offline-backup-options"></a>Opções de backup offline

A cópia de segurança offline é oferecida em dois modos com base na propriedade dos dispositivos de armazenamento:

- Backup offline baseado na Caixa de Dados Azure (pré-visualização)
- Backup offline com base no serviço azure import/exportação

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Backup offline baseado na Caixa de Dados Azure (pré-visualização)

Este modo é atualmente suportado com o Agente de Serviços de Recuperação do Microsoft Azure (MARS), em pré-visualização. Esta opção aproveita a Caixa de [Dados Azure](https://azure.microsoft.com/services/databox/) para enviar aparelhos de transferência resistentes à Microsoft com conectores USB para o seu datacenter ou escritório remoto. Os dados de cópia de segurança estão diretamente escritos nestes dispositivos. Esta opção poupa o esforço necessário para adquirir os seus próprios discos e conectores compatíveis com o Azure ou para fornecer armazenamento temporário como local de paragem. A Microsoft também lida com a logística de transferência sem fim, que pode rastrear através do portal Azure.

Uma arquitetura que descreve o movimento de dados de backup com esta opção é mostrada aqui.

![Arquitetura de caixa de dados de backup azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Aqui está um resumo da arquitetura:

1. A Azure Backup copia diretamente os dados de backup para estes dispositivos pré-configurados.
2. Em seguida, pode enviar estes dispositivos de volta para um centro de dados Azure.
3. A Azure Data Box copia os dados numa conta de armazenamento do cliente.
4. A Azure Backup copia automaticamente os dados de backup da conta de armazenamento para o cofre designado dos Serviços de Recuperação. Estão agendados backups online incrementais.

Para utilizar cópias de segurança offline baseadas na Caixa de Dados Azure, consulte a cópia de segurança offline utilizando a Caixa de [Dados Azure](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Backup offline com base no serviço azure import/exportação

Esta opção é suportada pelo Microsoft Azure Backup Server (MABS), System Center Data Protection Manager (DPM) DPM-A e pelo Agente MARS. Utiliza o [serviço de importação/exportação Azure.](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) Pode transferir dados de backup iniciais para o Azure utilizando os seus próprios discos e conectores compatíveis com o Azure. Esta abordagem requer que você provisão de armazenamento temporário conhecido como a localização de encenação e use utilitários pré-construídos para formatar e copiar os dados de backup em discos propriedade do cliente.

Uma arquitetura que descreve o movimento de dados de backup com esta opção é mostrada aqui.

![Arquitetura de serviço de importação/exportação de backup azure](./media/offline-backup-overview/azure-backup-import-export.png)

Aqui está um resumo da arquitetura:

1. Em vez de enviar os dados de backup sobre a rede, o Azure Backup escreve os dados de backup para um local de paragem.
2. Os dados no local de paragem são escritos para um ou mais discos SATA utilizando um utilitário personalizado.
3. Como parte do trabalho preparatório, o utilitário cria um emprego de importação Azure. As unidades SATA são enviadas para o centro de dados Azure mais próximo e referenciam o trabalho de importação para ligar as atividades.
4. No centro de dados Azure, os dados dos discos são copiados para uma conta de armazenamento Azure.
5. A Azure Backup copia os dados de backup da conta de armazenamento para o cofre dos Serviços de Recuperação. Os reforços incrementais estão agendados.

Para utilizar cópias de segurança offline com base no serviço Azure Import/Export com o Agente MARS, consulte o fluxo de trabalho de [backup offline em Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Para utilizar o mesmo juntamente com MABS ou DPM-A, consulte o fluxo de trabalho de [backup offline para DPM e Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Resumo de suporte de backup offline

A tabela seguinte compara as duas opções disponíveis para que possa fazer as escolhas apropriadas com base no seu cenário.

| **Consideração**                                            | **Backup offline baseado na Caixa de Dados Azure**                     | **Backup offline com base no serviço azure import/exportação**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelos de implementação de backup Azure                              | Agente MARS (pré-visualização)                                              | MARS Agent, MABS, DPM-A                                           |
| Dados máximos de backup por servidor (MARS) ou por grupo de proteção (MABS, DPM-A) | Disco da Caixa de [Dados Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7.2 TB <br> [Caixa de Dados Azure](https://docs.microsoft.com/azure/databox/data-box-overview) - 80 TB       | 80 TB (até 10 discos de 8 TB cada)                          |
| Segurança (dados, dispositivo e serviço)                           | [Dados](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES 256 bits encriptados <br> [Dispositivo](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - Caso acidentado, interface proprietária, baseada na credencial para copiar dados <br> [Serviço](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - Protegido por funcionalidades de segurança Azure | Dados - BitLocker encriptado                                 |
| Fornecimento temporário de localização de encenação                     | Não é necessária                                                | Mais ou igual ao tamanho estimado dos dados de backup        |
| Regiões suportadas                                           | [Regiões de discos azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Regiões da Caixa de Dados Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Regiões de serviços de importação/exportação de Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Transporte marítimo de corta-mato                                     | Não suportado  <br>    Endereço de origem e centro de dados Azure deve estar no mesmo país* | Suportado                                                    |
| Transferir logística (entrega, transporte, recolha)           | Totalmente gerido pela Microsoft                                     | Cliente gerido                                            |
| Preços                                                      | [Preços da Caixa de Dados Azure](https://azure.microsoft.com/pricing/details/databox/) <br> [Preços do disco da Caixa de Dados Azure](https://azure.microsoft.com/pricing/details/databox/disk/) | [Preços do serviço de importação/exportação de Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Se o seu país não tem um datacenter Azure, precisa enviar os seus discos para um datacenter Azure noutro país.

## <a name="next-steps"></a>Passos seguintes

- [Backup offline Azure Backup utilizando caixa de dados Azure](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Fluxo de trabalho de backup offline em Backup Azure](backup-azure-backup-import-export.md)
- [Fluxo de trabalho de backup offline para DPM e Servidor de Backup Azure](backup-azure-backup-server-import-export-.md)

---
title: Visão geral do backup offline
description: Saiba mais sobre os componentes da cópia de segurança offline. Incluem cópias de segurança offline baseadas na Caixa de Dados Azure e cópias de segurança offline baseadas no serviço Azure Import/Export.
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: c5e0f4e722e2dd15b7277a484af2a101844344e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86503630"
---
# <a name="overview-of-offline-backup"></a>Visão geral do backup offline

Este artigo dá uma visão geral da cópia de segurança offline.

As cópias de segurança completas iniciais para o Azure normalmente transferem grandes quantidades de dados on-line e requerem mais largura de banda de rede quando comparadas com as cópias de segurança subsequentes que transferem apenas alterações incrementais. Escritórios remotos ou centros de dados em certas geografias nem sempre têm largura de banda de rede suficiente. Por esta razão, estes backups iniciais demoram vários dias. Durante este período, as cópias de segurança utilizam continuamente a mesma rede que estava prevista para aplicações em execução no datacenter no local.

O Azure Backup suporta a cópia de segurança offline, que transfere os dados de backup iniciais offline, sem a utilização da largura de banda da rede. Fornece um mecanismo para copiar dados de backup em dispositivos de armazenamento físico. Os dispositivos são então enviados para um datacenter Azure próximo e enviados para um cofre dos Serviços de Recuperação. Este processo garante uma transferência robusta de dados de backup sem utilizar qualquer largura de banda de rede.

## <a name="offline-backup-options"></a>Opções de backup offline

A cópia de segurança offline é oferecida em dois modos com base na propriedade dos dispositivos de armazenamento:

- Backup offline baseado na Caixa de Dados do Azure (pré-visualização)
- Backup offline baseado no serviço Azure Import/Export

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Backup offline baseado na Caixa de Dados do Azure (pré-visualização)

Este modo é atualmente suportado com o Agente microsoft Azure Recovery Services (MARS), em pré-visualização. Esta opção aproveita [a Azure Data Box](https://azure.microsoft.com/services/databox/) para enviar aparelhos de transferência seguros e resistentes à Microsoft com conectores USB para o seu datacenter ou escritório remoto. Os dados de cópia de segurança estão diretamente escritos nestes dispositivos. Esta opção poupa o esforço necessário para obter os seus próprios discos e conectores compatíveis com o Azure ou para providenciar armazenamento temporário como local de preparação. A Microsoft também lida com a logística de transferências de ponta a ponta, que pode rastrear através do portal Azure.

Uma arquitetura que descreve o movimento de dados de backup com esta opção é mostrada aqui.

![Arquitetura Azure Backup Data Box](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Aqui está um resumo da arquitetura:

1. O Azure Backup copia diretamente dados de backup para estes dispositivos pré-configurados.
2. Em seguida, pode enviar estes dispositivos de volta para um datacenter Azure.
3. A Azure Data Box copia os dados numa conta de armazenamento do cliente.
4. O Azure Backup copia automaticamente os dados de backup da conta de armazenamento para o cofre designado dos Serviços de Recuperação. Estão agendadas cópias de segurança online incrementais.

Para utilizar a cópia de segurança offline com base na Caixa de Dados Azure, consulte [a cópia de segurança offline utilizando a Caixa de Dados Azure](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Backup offline baseado no serviço Azure Import/Export

Esta opção é suportada pelo Microsoft Azure Backup Server (MABS), pelo System Center Data Protection Manager (DPM) DPM-A e pelo Agente MARS. Utiliza o [serviço de importação/exportação Azure.](../storage/common/storage-import-export-service.md) Pode transferir dados de cópia de segurança iniciais para o Azure utilizando os seus próprios discos e conectores compatíveis com o Azure. Esta abordagem requer que você provisa armazenamento temporário conhecido como localização de localização de localização e use utilitários pré-construídos para formatar e copiar os dados de backup em discos propriedade do cliente.

Uma arquitetura que descreve o movimento de dados de backup com esta opção é mostrada aqui.

![Azure Backup Import/Export service architecture](./media/offline-backup-overview/azure-backup-import-export.png)

Aqui está um resumo da arquitetura:

1. Em vez de enviar os dados de backup através da rede, o Azure Backup escreve os dados de backup para um local de preparação.
2. Os dados no local de localização de localização são escritos para um ou mais discos SATA utilizando um utilitário personalizado.
3. Como parte do trabalho preparatório, o utilitário cria um trabalho de importação Azure. As unidades SATA são enviadas para o centro de dados Azure mais próximo e referem o trabalho de importação para ligar as atividades.
4. No datacenter Azure, os dados dos discos são copiados para uma conta de armazenamento Azure.
5. A Azure Backup copia os dados de reserva da conta de armazenamento para o cofre dos Serviços de Recuperação. Estão agendadas cópias de segurança incrementais.

Para utilizar cópias de segurança offline baseadas no serviço Azure Import/Export com o agente MARS, consulte o [fluxo de trabalho de backup offline em Azure Backup](./backup-azure-backup-import-export.md).

Para utilizar o mesmo juntamente com mABS ou DPM-A, consulte o [fluxo de trabalho de backup offline para DPM e Azure Backup Server](./backup-azure-backup-server-import-export.md).

## <a name="offline-backup-support-summary"></a>Resumo de suporte de backup offline

A tabela seguinte compara as duas opções disponíveis para que possa fazer as escolhas apropriadas com base no seu cenário.

| **Consideração**                                            | **Backup offline baseado na Caixa de Dados Azure**                     | **Backup offline baseado no serviço Azure Import/Export**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelos de implementação de backup Azure                              | Agente MARS (pré-visualização)                                              | AGENTE MARS, MABS, DPM-A                                           |
| Dados de backup máximos por servidor (MARS) ou por grupo de proteção (MABS, DPM-A) | [Disco Azure Data Box](../databox/data-box-disk-overview.md) - 7.2 TB <br> [Caixa de Dados Azure](../databox/data-box-overview.md) - 80 TB       | 80 TB (até 10 discos de 8 TB cada)                          |
| Segurança (dados, dispositivo e serviço)                           | [Dados](../databox/data-box-security.md#data-box-data-protection) - AES 256 bits encriptados <br> [Dispositivo](../databox/data-box-security.md#data-box-device-protection) - Caso acidentado, interface proprietária e credencial para copiar dados <br> [Serviço](../databox/data-box-security.md#data-box-service-protection) - Protegido por funcionalidades de segurança Azure | Dados - BitLocker encriptado                                 |
| Provisão temporária de localização                     | não é necessário                                                | Mais ou igual ao tamanho estimado dos dados de backup        |
| Regiões suportadas                                           | [Regiões de disco Azure Data Box](../databox/data-box-disk-overview.md#region-availability) <br> [Regiões da Caixa de Dados Azure](../databox/data-box-disk-overview.md#region-availability) | [Regiões de serviços de importação/exportação de Azure](../storage/common/storage-import-export-service.md#region-availability) |
| Envio cross-country                                     | Não suportado  <br>    Endereço de origem e destino O centro de dados Azure deve estar no mesmo país/região* | Suportado                                                    |
| Transporte logístico (entrega, transporte, recolha)           | Totalmente a Microsoft geriu                                     | Gerido pelo cliente                                            |
| Preços                                                      | [Preços da Caixa de Dados Azure](https://azure.microsoft.com/pricing/details/databox/) <br> [Preços do disco Azure Data Box](https://azure.microsoft.com/pricing/details/databox/disk/) | [Preços dos serviços de importação/exportação da Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Se o seu país/região não tiver um datacenter Azure, precisa enviar os seus discos para um datacenter Azure em outro país/região.

## <a name="next-steps"></a>Passos seguintes

- [Backup Azure backup offline usando Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Fluxo de trabalho de backup offline em Azure Backup](backup-azure-backup-import-export.md)
- [Fluxo de trabalho de backup offline para DPM e Azure Backup Server](backup-azure-backup-server-import-export.md)

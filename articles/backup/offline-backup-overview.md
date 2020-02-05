---
title: Visão geral da Cópia de Segurança Offline
description: Conheça os vários componentes da Backup Offline, incluindo a Azure Data Box baseada em Backup Offline e Azure Import/Export Backup offline.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027020"
---
# <a name="overview-of-offline-backup"></a>Visão geral da Cópia de Segurança Offline

Este artigo dá uma visão geral da Backup Offline.

As cópias de segurança completas iniciais para o Azure normalmente transferem grandes quantidades de dados on-line e requerem mais largura de banda da rede em comparação com as cópias de segurança subsequentes que transferem apenas alterações incrementais. Escritórios remotos ou datacenters em certas geografias nem sempre têm largura de banda de rede suficiente. Por isso, estas cópias de segurança iniciais demoram vários dias e durante este tempo utilizam continuamente a mesma rede que foi disponibilizada para aplicações em execução no centro de dados no local.

O Azure Backup suporta o "Offline Backup", que permite transferir dados de backup iniciais offline, sem a utilização da largura de banda da rede. Fornece um mecanismo para copiar dados de backup em dispositivos de armazenamento físico que são depois enviados para um Centro de Dados Azure próximo e enviados para um Cofre de Serviços de Recuperação. Este processo garante uma transferência robusta de dados de backup sem utilizar qualquer largura de banda da rede.

## <a name="offline-backup-options"></a>Opções de backup offline

Offline-Backup é oferecido em dois modos com base na propriedade dos dispositivos de armazenamento.

1. Backup offline da Caixa de Dados Azure (pré-visualização)
2. Backup offline baseado em importação de Azure/exportação

## <a name="azure-data-box-based-offline-backup-preview"></a>Caixa de dados Azure baseada em Backup Offline (Pré-visualização)

Este modo é suportado atualmente com o Agente MARS, em pré-visualização. Esta opção aproveita o [serviço Azure Data Box](https://azure.microsoft.com/services/databox/) para enviar aparelhos de transferência resistentes à Microsoft com conectores USB, para o seu datacenter ou escritório remoto e os dados de backup estão diretamente escritos nestes dispositivos. **Esta opção poupa o esforço necessário para adquirir os seus próprios discos e conectores compatíveis com o Azure ou o fornecimento de armazenamento temporário como local de paragem.** Além disso, a Microsoft trata da logística de transferência de ponta a ponta, que pode rastrear através do portal Azure. Uma arquitetura que descreve o movimento de dados de backup com esta opção é mostrada abaixo.

![Arquitetura de caixa de dados de backup azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Aqui está um resumo da arquitetura:

1. A Cópia de Segurança Azure copia diretamente os dados de backup para estes dispositivos pré-configurados.
2. Em seguida, pode enviar estes dispositivos de volta para um Centro de Dados Azure.
3. O serviço Azure Data Box copia os dados numa conta de armazenamento do cliente.
4. A Azure Backup copia automaticamente os dados de backup da conta de armazenamento para o cofre designado de Serviços de Recuperação e estão agendados backups online incrementais.

Para utilizar a Caixa de Dados Azure baseada em Backup Offline, consulte [este artigo](offline-backup-azure-data-box.md).

## <a name="azure-importexport-based-offline-backup"></a>Backup offline baseado em importação de Azure/exportação

Esta opção é suportada pelo Azure Backup Server (MABS) / DPM-A / MARS Agent. Aproveita o [serviço Azure Import/Export](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) que lhe permite transferir dados iniciais de backup para o Azure utilizando os seus próprios discos e conectores compatíveis com o Azure. Esta abordagem requer o fornecimento de um armazenamento temporário conhecido como Local de **Encenação** e utilização de utilitários pré-construídos para formatar e copiar os dados de backup em discos de propriedade do cliente. Uma arquitetura que descreve o movimento de dados de backup com esta opção é mostrada abaixo:

![Arquitetura de importação/exportação de backup azure](./media/offline-backup-overview/azure-backup-import-export.png)

Aqui está um resumo da arquitetura:

1. Em vez de enviar os dados de backup sobre a rede, o Azure Backup escreve os dados de backup para um local de paragem.
2. Os dados no local de paragem são escritos a um ou mais discos SATA utilizando um utilitário personalizado.
3. Como parte do trabalho preparatório, o utilitário cria um trabalho de Importação Azure. As unidades SATA são enviadas para o centro de dados Azure mais próximo, e referenciam o trabalho de importação para ligar as atividades.
4. No centro de dados Azure, os dados dos discos são copiados para uma conta de armazenamento Azure.
5. O Azure Backup copia os dados de backup da conta de armazenamento para o cofre dos Serviços de Recuperação, e estão agendados backups incrementais.

Para utilizar o Azure Import/Export based Offline Backup com o Agente MARS, consulte [este artigo](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Para utilizar o mesmo juntamente com MABS/DPM-A, consulte [este artigo](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Resumo de suporte de backup offline

A tabela abaixo compara as duas opções disponíveis para que possa fazer as escolhas apropriadas com base no seu cenário.

| **Consideração**                                            | **Caixa de dados Azure Baseada em Backup offline**                     | **Backup offline baseado em importações de Azure/exportação**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelos de implantação de backup Azure                              | Agente MARS (Pré-visualização)                                              | Agente MARS, Servidor de Backup Azure (MABS), DPM-A                                           |
| Dados máximos de backup por servidor (MARS) ou por Grupo de Proteção (MABS, DPM-A) | Disco de caixa de [dados Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7.2 TB <br> [Caixa de Dados Azure](https://docs.microsoft.com/azure/databox/data-box-overview) - 80 TB       | 80 TB (até 10 discos de 8 TB cada)                          |
| Segurança (dados, dispositivo e serviço)                           | [Dados](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES-256 Bit Encriptado <br> [Dispositivo](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - Caso acidentado, interface baseada em credenciais proprietárias para copiar dados <br> [Serviço](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - Protegido por funcionalidades de Segurança Azure | Dados - BitLocker Encriptado                                 |
| Fornecimento temporário de localização de encenação                     | Não é necessário                                                | Mais ou igual ao tamanho estimado dos dados de backup        |
| Regiões Apoiadas                                           | [Regiões de discos de caixa de dados azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Regiões da Caixa de Dados Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Regiões de Importação e Exportação de Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Envio cross-country*                                     | **Não apoiado**  <br>    *Endereço de origem e Centro de Dados Destination Azure deve estar no mesmo país* | Suportadas                                                    |
| Transfer Logistics (entrega, transporte, pick-up)           | Totalmente gerido pela Microsoft                                     | Cliente gerido                                            |
| Preços                                                      | [Preços da caixa de dados azure](https://azure.microsoft.com/pricing/details/databox/) <br> [Preços do disco de caixa de dados azure](https://azure.microsoft.com/pricing/details/databox/disk/) | [Importação azure/preços de exportação](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *Se o seu país não tem um Centro de Dados Azure, então precisa enviar os seus discos para um Centro de Dados Azure noutro país.*

## <a name="next-steps"></a>Passos seguintes

* [Caixa de dados Azure Baseada em Backup Offline para agente MARS](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Azure Import/Export based Offline Backup para agente MARS](backup-azure-backup-import-export.md)  
* [Azure Import/Export based Offline Backup para MABS/DPM-A](backup-azure-backup-server-import-export-.md)

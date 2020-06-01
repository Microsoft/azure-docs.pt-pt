---
title: Matriz de suporte de cópia de segurança do SAP HANA
description: Neste artigo, conheça os cenários e limitações suportados quando utilizar o backup Azure para fazer backup nas bases de dados SAP HANA em VMs Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: ed7b3abacd5da3fd06e621d9f35a8dcdddcb9000
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234783"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte da cópia de segurança de bases de dados SAP HANA nas VMs do Azure

A Azure Backup suporta a cópia de segurança das bases de dados SAP HANA para a Azure. Este artigo resume os cenários suportados e as limitações presentes quando utiliza o Azure Backup para fazer backup nas bases de dados SAP HANA em VMs Azure.

> [!NOTE]
> A frequência da cópia de segurança do registo pode agora ser definida para um mínimo de 15 minutos. As cópias de segurança de registo só começam a fluir depois de concluída uma cópia de segurança completa bem sucedida para a base de dados.

## <a name="scenario-support"></a>Suporte de cenário

| **Cenário**               | **Configurações suportadas**                                | **Configurações não suportadas**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA a funcionar apenas em VMs Azure Linux                    | HANA Grandes Instâncias (HLI)                                   |
| **Regiões**                   | **GA:**<br> **Américas** – Central EUA, Leste dos EUA 2, Leste dos EUA, Norte Central EUA, Centro Sul dos EUA, West Central US, West Central US, West Us, Canadá Central, Canadá Leste, Brasil Sul <br> **Ásia Pacífico** – Austrália Central, Austrália Central 2, Austrália Leste, Austrália Sudeste, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul, Ásia Oriental, Sudeste Asiático, Índia Central, Índia Central, Índia Ocidental, China Leste, China Norte, China Leste2, China Norte 2 <br> **Europa** – Europa Ocidental, Norte da Europa, França Central, Reino Unido Sul, Reino Unido Oeste, Alemanha Norte, Alemanha Central Ocidental, Suíça Norte, Suíça Oeste, Suíça Norte, Suíça Norte <br> **África / ME** - África do Sul Norte, África do Sul Oeste, Emirados Árabes Unidos Norte, Uae Central  <BR>  **Regiões do Azure Government** | França Sul, Alemanha Central, Alemanha Nordeste, EUA Gov IOWA |
| **Versões de SO**            | SLES 12 com SP2, SP3 ou SP4; SLES 15 com SP1                              | RHEL                                                |
| **Versões HANA**          | SDC em HANA 1.x, MDC em HANA 2.x <= SPS04 Rev 46       | -                                                            |
| **Implantações hana**       | SAP HANA em um único Azure VM - Escalar apenas. <br><br> Para implementações de alta disponibilidade, ambos os nós nas duas máquinas diferentes são tratados como nós individuais com cadeias de dados separadas.               | Aumentar <br><br> Em implementações de alta disponibilidade, a cópia de segurança não falha automaticamente no nó secundário. A cópia de segurança configurante deve ser feita separadamente para cada nó.                                           |
| **Instâncias HANA**         | Um único caso SAP HANA em um único Azure VM - escala apenas | Múltiplas instâncias SAP HANA em um único VM                  |
| **Tipos de base de dados HANA**    | Único Recipiente de Base de Dados (SDC) ON 1.x, Recipiente Multi-Base de Dados (MDC) em 2.x | MDC em HANA 1.x                                              |
| **Tamanho da base de dados HANA**     | 2-TB tamanho de backup completo como relatado pela HANA)                   |                                                              |
| **Tipos de cópia de segurança**           | Backups completos, diferenciais e de registo                          | Incrementais, Instantâneos                                       |
| **Tipos de restauro**          | Consulte a Nota [HANA HANA 1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber mais sobre os tipos de restauro suportados |                                                              |
| **Limites de backup**          | Até 2 TB de tamanho de backup completo por instância SAP HANA         |                                                              |
| **Configurações especiais** |                                                              | SAP HANA + Tiering Dinâmico <br>  Clonagem através de LaMa        |

------

>[!NOTE]
>O Azure Backup não se ajusta automaticamente às alterações de horário de verão ao fazer o backup de uma base de dados SAP HANA em funcionamento num VM Azure.
>
>Modifique a apólice manualmente, se necessário.


> [!NOTE]
> Agora pode [monitorizar os trabalhos de backup e restauro](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#monitor-manual-backup-jobs-in-the-portal) (para a mesma máquina) desencadeados a partir de clientes nativos da HANA (SAP HANA Studio/ Cockpit/ DBA Cockpit) no portal Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [fazer backup sap HANA bases de dados em VMs Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Saiba como restaurar as [bases de dados SAP HANA em execução em VMs Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerir as bases de dados SAP HANA que são apoiadas através do Azure Backup](sap-hana-db-manage.md)
* Saiba como [resolver problemas comuns ao fazer backup das bases de dados SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)

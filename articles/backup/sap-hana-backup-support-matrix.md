---
title: Matriz de suporte de cópia de segurança do SAP HANA
description: Neste artigo, conheça os cenários e limitações suportados quando utilizar o backup Azure para apoiar as bases de dados SAP HANA em VMs Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382450"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte da cópia de segurança de bases de dados SAP HANA nas VMs do Azure

O Azure Backup suporta a cópia de segurança das bases de dados SAP HANA para o Azure. Este artigo resume os cenários suportados e as limitações presentes quando utiliza o Azure Backup para apoiar as bases de dados SAP HANA em VMs Azure.

> [!NOTE]
> A frequência de cópia de segurança de registo pode agora ser definida num mínimo de 15 minutos. As cópias de segurança de log só começam a fluir depois de concluída uma cópia de segurança completa para a base de dados.

## <a name="scenario-support"></a>Suporte ao cenário

| **Cenário**               | **Configurações suportadas**                                | **Configurações não suportadas**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA a correr apenas em VMs Azure Linux                    | HANA Grandes Instâncias (HLI)                                   |
| **Geos**                   | **GA:**<br> **Américas** – Centro dos EUA, Leste dos EUA 2, Leste dos EUA, Norte Central DOS, Centro-Sul dos EUA, Oeste DOS 2, Oeste dos EUA, Oeste dos EUA, Canadá Central, Canadá Leste, Brasil Sul <br> **Ásia-Pacífico** – Austrália Central, Austrália Central 2, Austrália Leste, Austrália Sudeste, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul, Ásia Oriental, Sudeste Asiático, Índia Central, Índia Meridional, Índia Ocidental, China Leste, China Norte, China Leste2, China Norte 2 <br> **Europa** – Europa Ocidental, Norte da Europa, França Central, Reino Unido Sul, Reino Unido Oeste, Alemanha Norte, Alemanha West Central, Suíça Norte, Suíça Oeste, Suíça Central Norte <br> **África / ME** - África do Sul Norte, África do Sul Oeste, Emirados Unidos Norte, Emirados Emirados Ocidentais  <BR>  **Regiões do Governo de Azure** | França Sul, Alemanha Central, Alemanha Nordeste, EUA Gov IOWA |
| **Versões osso**            | SLES 12 com SP2, SP3 ou SP4; SLES 15 com SP1                              | RHEL                                                |
| **Versões HANA**          | SDC em HANA 1.x, MDC em HANA 2.x <= SPS04 Rev 46       | -                                                            |
| **Destacamentos HANA**       | SAP HANA em um único Azure VM - Escala apenas. <br><br> Para implementações de alta disponibilidade, ambos os nós das duas máquinas diferentes são tratados como nós individuais com cadeias de dados separadas.               | Escala para fora <br><br> Em implementações de alta disponibilidade, a cópia de segurança não falha automaticamente no nó secundário. Configurar a cópia de segurança deve ser feita separadamente para cada nó.                                           |
| **Casos HANA**         | Um único caso SAP HANA em um único Azure VM - escala apenas | Múltiplas instâncias SAP HANA em um único VM                  |
| **Tipos de bases de dados HANA**    | Recipiente único de base de dados (SDC) EM 1.x, Recipiente Multi-Base de Dados (MDC) em 2.x | MDC em HANA 1.x                                              |
| **Tamanho da base de dados HANA**     | 2-TB tamanho total de backup conforme reportado pela HANA)                   |                                                              |
| **Tipos de backup**           | Backups completos, diferenciais e de registo                          | Incremental, Snapshots                                       |
| **Restaurar tipos**          | Consulte a Nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber sobre os tipos de restauro suportados |                                                              |
| **Limites de backup**          | Até 2 TB de tamanho de reserva completo por instância SAP HANA         |                                                              |
| **Configurações especiais** |                                                              | SAP HANA + Tiering Dinâmico <br>  Clonagem através de LaMa        |

------

> [!NOTE]
> As operações de backup e restauro de clientes nativos Da SAP HANA (SAP HANA Studio/ Cockpit/ DBA Cockpit) não são atualmente suportadas.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [backup sAP HANA bases de dados em execução em VMs Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Saiba como restaurar as bases de [dados SAP HANA em execução em VMs Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como gerir as bases de [dados SAP HANA que são apoiadas usando o Azure Backup](sap-hana-db-manage.md)
* Saiba como [resolver problemas comuns ao apoiar as bases de dados SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)

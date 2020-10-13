---
title: Matriz de suporte de cópia de segurança do SAP HANA
description: Neste artigo, conheça os cenários e limitações suportados quando utilizar o Azure Backup para fazer backup nas bases de dados SAP HANA em VMs Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: e3bfc5ab9a91ae3aee73d7ed24161acae60211ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022331"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte da cópia de segurança de bases de dados SAP HANA nas VMs do Azure

A Azure Backup suporta a cópia de segurança das bases de dados SAP HANA para a Azure. Este artigo resume os cenários suportados e as limitações presentes quando utiliza o Azure Backup para fazer backup nas bases de dados SAP HANA em VMs Azure.

> [!NOTE]
> A frequência da cópia de segurança do registo pode agora ser definida para um mínimo de 15 minutos. As cópias de segurança de registo só começam a fluir depois de concluída uma cópia de segurança completa bem sucedida para a base de dados.

## <a name="scenario-support"></a>Suporte de cenário

| **Cenário**               | **Configurações suportadas**                                | **Configurações não suportadas**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA a funcionar apenas em VMs Azure Linux                    | HANA Grandes Instâncias (HLI)                                   |
| **Regiões**                   | **GA:**<br> **Américas** – Central EUA, Leste dos EUA 2, Leste dos EUA, Norte Central EUA, Centro Sul dos EUA, West Central US, West Central US, West Us, Canadá Central, Canadá Leste, Brasil Sul <br> **Ásia Pacífico** – Austrália Central, Austrália Central 2, Austrália Leste, Austrália Sudeste, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul, Ásia Oriental, Sudeste Asiático, Índia Central, Índia Central, Índia Ocidental, China Leste, China Norte, China Leste2, China Norte 2 <br> **Europa** – Europa Ocidental, Norte da Europa, França Central, Reino Unido Sul, Reino Unido Oeste, Alemanha Norte, Alemanha Central Ocidental, Suíça Norte, Suíça Oeste, Suíça Norte, Noruega Leste, Noruega Oeste <br> **África / ME** - África do Sul Norte, África do Sul Oeste, Emirados Árabes Unidos Norte, Uae Central  <BR>  **Regiões do Azure Government** | França Sul, Alemanha Central, Alemanha Nordeste, EUA Gov IOWA |
| **Versões de SO**            | SLES 12 com SP2, SP3 e SP4; SLES 15 com SP0 e SP1 <br><br>  A partir de 1 de agosto de 2020, o backup SAP HANA para a RHEL (7.4, 7.6, 7,7 & 8.1) está geralmente disponível.                |                                             |
| **Versões HANA**          | SDC em HANA 1.x, MDC em HANA 2.x <= SPS04 Rev 48, SPS05 (ainda a ser validado para cenários habilitados para encriptação)      |                                                            |
| **Implantações hana**       | SAP HANA em um único Azure VM - Escalar apenas. <br><br> Para implementações de alta disponibilidade, ambos os nós nas duas máquinas diferentes são tratados como nós individuais com cadeias de dados separadas.               | Aumentar <br><br> Em implementações de alta disponibilidade, a cópia de segurança não falha automaticamente no nó secundário. A cópia de segurança configurante deve ser feita separadamente para cada nó.                                           |
| **Instâncias HANA**         | Um único caso SAP HANA em um único Azure VM - escala apenas | Múltiplas instâncias SAP HANA em um único VM                  |
| **Tipos de base de dados HANA**    | Único Recipiente de Base de Dados (SDC) ON 1.x, Recipiente Multi-Base de Dados (MDC) em 2.x | MDC em HANA 1.x                                              |
| **Tamanho da base de dados HANA**     | Bases de dados HANA de tamanho <= 2 TB (este não é o tamanho da memória do sistema HANA)               |                                                              |
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
> Agora pode [monitorizar a cópia de segurança e restaurar os](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) trabalhos (para a mesma máquina) acionados a partir de clientes nativos da HANA (SAP HANA Studio/ Cockpit/ DBA Cockpit) no portal Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [fazer backup nas bases de dados SAP HANA em execução em VMs Azure](./backup-azure-sap-hana-database.md)
* Saiba como restaurar as [bases de dados SAP HANA em execução em VMs Azure](./sap-hana-db-restore.md)
* Saiba como [gerir as bases de dados SAP HANA que são apoiadas através do Azure Backup](sap-hana-db-manage.md)
* Saiba como [resolver problemas comuns ao fazer backup das bases de dados SAP HANA](./backup-azure-sap-hana-database-troubleshoot.md)

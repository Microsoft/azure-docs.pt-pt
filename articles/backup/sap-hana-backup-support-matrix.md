---
title: Matriz de suporte de cópia de segurança do SAP HANA
description: Neste artigo, saiba mais sobre os cenários e limitações com suporte ao usar o backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 82d844385290ab0dc2953537c1f9a3387dd7b2b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842636"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte da cópia de segurança de bases de dados SAP HANA nas VMs do Azure

O backup do Azure dá suporte ao backup de bancos de dados SAP HANA no Azure. Este artigo resume os cenários com suporte e as limitações presentes quando você usa o backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.

## <a name="onboard-to-the-public-preview"></a>Integração à visualização pública

Integre à visualização pública da seguinte maneira:

* No portal, registre sua ID de assinatura para o provedor de serviço de serviços de recuperação [seguindo este artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Para o PowerShell, execute este cmdlet. Ele deve ser concluído como "registrado".

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> A frequência do backup de log agora pode ser definida para um mínimo de 15 minutos. Os backups de log só começam a fluir após a conclusão de um backup completo bem-sucedido para o banco de dados.

## <a name="scenario-support"></a>Suporte a cenários

| **Cenário**               | **Configurações com suporte**                                | **Configurações sem suporte**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA em execução somente em VMs Linux do Azure                    | HLI (HANA Large instances)                                   |
| **Áreas geográficas**                   | **GA:**<br />**Europa** – Europa Ocidental, Norte da Europa, França Central, Reino Unido Sul, Reino Unido Oeste, Alemanha Norte, Alemanha West Central, Suíça Norte, Suíça West<br />**Ásia-Pacífico** – Austrália Central, Austrália Central 2, Austrália Leste, Austrália Sudeste, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul<br /><br>**previsualizar:**<br />**Américas** – Centro dos EUA, Leste dos EUA 2, Leste dos EUA, Norte Central DOS, Centro-Sul dos EUA, Oeste DOS 2, Oeste dos EUA, Oeste dos EUA, Canadá Central, Canadá Leste <br />**Ásia-Pacífico** - Ásia Oriental, Sudeste Asiático, Índia Central, Índia do Sul | China Leste, China Norte, China Leste2, China Norte 2, Índia Ocidental, Suíça Central Norte, África do Sul Norte, África do Sul Oeste, Emirados Emirados Unidos Norte, Emirados Unidos Central, Regiões do Governo Azure, França Sul, Brasil Sul |
| **Versões do sistema operacional**            | SLES 12 com SP2, SP3 ou SP4                                | SLES 15, RHEL                                                |
| **Versões do HANA**          | SDC no HANA 1. x, MDC no HANA 2. x < = SPS04 Rev 44            | -                                                            |
| **Implantações do HANA**       | SAP HANA em uma única VM do Azure-escalar somente verticalmente               | Expandir                                                    |
| **Instâncias do HANA**         | Uma única instância de SAP HANA em uma única VM do Azure – escalar verticalmente somente | Várias instâncias de SAP HANA em uma única VM                  |
| **Tipos de banco de dados HANA**    | Contêiner de Banco de Dados Individual (SDC) em 1. x, contêiner de vários bancos de dados (MDC) em 2. x | MDC no HANA 1. x                                              |
| **Tamanho do banco de dados HANA**     | tamanho de backup completo de 2 TB conforme relatado pelo HANA)                   |                                                              |
| **Tipos de backup**           | Backups completos, diferenciais e de log                          | Incrementais, instantâneos                                       |
| **Tipos de restauração**          | Consulte a SAP HANA nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber mais sobre os tipos de restauração com suporte |                                                              |
| **Limites de backup**          | Até 2 TB de tamanho de backup completo por instância de SAP HANA         |                                                              |
| **Configurações especiais** |                                                              | SAP HANA + camadas dinâmicas <br>  Clonagem por meio de LaMa        |

------

> [!NOTE]
> Atualmente, não há suporte para operações de backup e restauração de SAP HANA clientes nativos (SAP HANA Studio/cockpit/DBA cockpit).



## <a name="next-steps"></a>Passos seguintes

* Saiba como [fazer backup de bancos de dados SAP Hana em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Saiba como [restaurar SAP Hana bancos de dados em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar SAP Hana bancos de dados cujo backup é feito usando o backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP Hana](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)

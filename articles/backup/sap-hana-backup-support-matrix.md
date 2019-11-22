---
title: Matriz de suporte de backup SAP HANA
description: Neste artigo, saiba mais sobre os cenários e limitações com suporte ao usar o backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 0d847d2131a9a9c21fde14cae40a184de4195223
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288233"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte para backup de bancos de dados SAP HANA em VMs do Azure

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
| **Áreas geográficas**                   | Leste da Austrália, leste da Austrália do Brasil Centro-Sul do Canadá, sul do Canadá Ásia Oriental, Ásia Oriental leste dos EUA, leste dos EUA 2, Oeste EUA Central, oeste dos EUA, oeste dos EUA 2, norte EUA Central, EUA Central, Sul EUA Central Índia central, Índia sul do Japão, leste do Japão St central da Coreia, Europa Setentrional do Sul da Coreia, Europa Ocidental Sul do Reino Unido Oeste do Reino Unido | Austrália Central, Austrália Central 2 Leste da China, Norte da China, China 2, Norte da China 2 West Índia central, França South Norte da Alemanha, Centro-oeste da Alemanha Norte da Suíça, Oeste da Suíça África do Sul, norte da África do Sul, Norte dos EAU, dos EAU central l regiões do Azure governamental |
| **Versões do sistema operacional**            | SLES 12 com SP2, SP3 ou SP4           | SLES 15, RHEL                                                |
| **Versões do HANA**          | SDC no HANA 1. x, MDC no HANA 2. x < = SPS04 Rev 44           | -                                                            |
| **Implantações do HANA**       | SAP HANA em uma única VM do Azure-escalar somente verticalmente               | Expandir                                                    |
| **Instâncias do HANA**         | Uma única instância de SAP HANA em uma única VM do Azure – escalar verticalmente somente | Várias instâncias de SAP HANA em uma única VM                  |
| **Tipos de banco de dados HANA**    | Contêiner de Banco de Dados Individual (SDC) em 1. x, contêiner de vários bancos de dados (MDC) em 2. x | MDC no HANA 1. x                                              |
| **Tamanho do banco de dados HANA**     | tamanho de backup completo de 2 TB após a compactação (série M 2 TB, 4 TB de RAM) |                                                              |
| **Tipos de backup**           | Backups completos, diferenciais e de log                           | Incrementais, instantâneos                                       |
| **Tipos de restauração**          | Consulte a SAP HANA nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber mais sobre os tipos de restauração com suporte |                                                              |
| **Limites de backup**          | Até 2 TB de tamanho de backup completo por instância de SAP HANA  |                                                              |
| **Configurações especiais** |                                                              | SAP HANA + camadas dinâmicas <br>  Clonagem por meio de LaMa            |

------

> [!NOTE]
> Atualmente, não há suporte para operações de backup e restauração de SAP HANA clientes nativos (SAP HANA Studio/cockpit/DBA cockpit).



## <a name="next-steps"></a>Passos seguintes

* Saiba como [fazer backup de bancos de dados SAP Hana em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Saiba como [restaurar SAP Hana bancos de dados em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar SAP Hana bancos de dados cujo backup é feito usando o backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP Hana](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)

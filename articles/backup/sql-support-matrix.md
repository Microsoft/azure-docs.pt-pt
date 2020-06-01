---
title: Matriz de suporte de backup Azure para backup do servidor SQL em VMs Azure
description: Fornece um resumo das definições e limitações de suporte ao fazer o backup do SQL Server em VMs Azure com o serviço de Backup Azure.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: f9594b9157f84a0536ffd4b62f792fd86fb1c243
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234254"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matriz de suporte para backup do servidor SQL em VMs Azure

Pode utilizar o Azure Backup para fazer backup nas bases de dados do SQL Server em VMs Azure hospedados na plataforma cloud microsoft Azure. Este artigo resume as configurações e limitações gerais de suporte para cenários e implementações de Backup do Servidor SQL em VMs Azure.

## <a name="scenario-support"></a>Suporte de cenário

**Suporte** | **Detalhes**
--- | ---
**Implantações apoiadas** | Sql Marketplace Azure VMs e VMs não-Marketplace (SQL Server instalado manualmente) são suportados.
**Regiões suportadas** | Austrália Sudeste (ASE), Austrália Oriental (AE), Austrália Central (AC), Austrália Central 2 (AC) <br> Sul do Brasil (BRS)<br> Canadá Central (CNC), Canadá Leste (CE)<br> Sudeste Asiático (SEA), Ásia Oriental (EA) <br> Leste dos EUA (EUS), Leste dos EUA 2 (EUS2), Centro Ocidental dos EUA (WCUS), Eua Ocidental (WUS); West US 2 (WUS 2) North Central US (NCUS) Central US (CUS) South Central US (SCUS) <br> India Central (INC), Índia Sul (INS), Índia Oeste <br> Japão Leste (JPE), Japão Oeste (JPW) <br> Coreia Central (KRC), Coreia do Sul (KRS) <br> Norte da Europa (NE), Europa Ocidental <br> Reino Unido Sul (Reino Unido), UK West (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Alemanha Norte, Alemanha Centro Ocidental <br> Suíça Norte, Suíça Oeste <br> França Central <br> China Leste, China Leste 2, China Norte, China Norte 2
**Sistemas operativos suportados** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux não é apoiado.
**Versões suportadas do SQL Server** | SQL Server 2019, SQL Server 2017 conforme detalhado na [página de lifecycle](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)do produto Search , SQL Server 2016 e SPs conforme detalhado na [página de lifecycle do produto Search](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Empresa, Standard, Web, Developer, Express.
**Versões suportadas .NET** | .NET Quadro 4.5.2 ou posteriormente instalado no VM

## <a name="feature-consideration-and-limitations"></a>Consideração de recursos e limitações

* A cópia de segurança do SQL Server pode ser configurada no portal Azure ou **no PowerShell**. Não apoiamos a CLI.
* A solução é suportada em ambos os tipos de [implementações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) - VMs Azure Resource Manager e VMs clássicos.
* VM executando SQL Server requer conectividade de internet para aceder a endereços IP públicos Azure.
* SQL Server **Failover Cluster Instance (FCI)** não é suportado.
* As operações de back-up e restauro de bases de dados de espelhos e de bases de dados não são suportadas.
* Utilizar mais de uma solução de backup para fazer backup da sua instância autónoma do SQL Server ou SQL Sempre no grupo de disponibilidade pode levar a falhas de backup; abster-se de fazê-lo.
* O backup de dois nós de um grupo de disponibilidade individualmente com soluções iguais ou diferentes, também pode levar a falhas de backup.
* A Azure Backup suporta apenas tipos de backup completos e completos para bases de dados **só de leitura**
* Bases de dados com um grande número de ficheiros não podem ser protegidas. O número máximo de ficheiros suportados é **~1000**.  
* Pode voltar até **~2000** bases de dados do SQL Server num cofre. Pode criar vários cofres no caso de ter um maior número de bases de dados.
* Pode configurar backup para até **50** bases de dados de uma só vez; esta restrição ajuda a otimizar as cargas de backup.
* Apoiamos bases de dados até **2 TB** em tamanho; para tamanhos maiores do que os problemas de desempenho podem surgir.
* Para ter uma noção de quantas bases de dados podem ser protegidas por servidor, considere fatores como largura de banda, tamanho VM, frequência de backup, tamanho da base de dados, e assim por diante. [Faça](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) o download do planejador de recursos para calcular o número aproximado de bases de dados que pode ter por servidor com base nos recursos VM e na política de backup.
* Quando os grupos de disponibilidade são configurados, as cópias de segurança são retiradas dos diferentes nós com base em alguns fatores. O comportamento de backup para um grupo de disponibilidade é resumido abaixo.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Comportamento da cópia de segurança com grupos de disponibilidade Always On

Recomendamos que a cópia de segurança seja configurada apenas num nó de um grupo de disponibilidade (AG). Configuure sempre o backup na mesma região que o nó primário. Por outras palavras, precisa sempre do nó primário para estar presente na região onde está a configurar o backup. Se todos os nós da AG estão na mesma região onde o backup está configurado, não há nenhuma preocupação.

#### <a name="for-cross-region-ag"></a>Para a AG transversal

* Independentemente da preferência de backup, as cópias de segurança só irão funcionar a partir dos nós que estão na mesma região onde a cópia de segurança está configurada. Isto porque os reforços transversais não são apoiados. Se tiver apenas dois nóns e o nó secundário estiver na outra região, os backups continuarão a funcionar a partir do nó primário (a menos que a sua preferência de backup seja 'apenas secundária').
* Se um nó falhar numa região diferente daquela em que o backup está configurado, os backups falharão nos nós na região falhada.

Dependendo da preferência de backup e dos tipos de backups (completo/diferencial/log/copy-only full), as cópias são retiradas de um nó particular (primário/secundário).

#### <a name="backup-preference-primary"></a>Preferência de backup: Primário

**Tipo de Cópia de Segurança** | **Nó**
--- | ---
Completa | Primário
Diferencial | Primário
Registar |  Primário
Apenas cópia cheia |  Primário

#### <a name="backup-preference-secondary-only"></a>Preferência de backup: Apenas secundário

**Tipo de Cópia de Segurança** | **Nó**
--- | ---
Completa | Primário
Diferencial | Primário
Registar |  Secundária
Apenas cópia cheia |  Secundária

#### <a name="backup-preference-secondary"></a>Preferência de backup: Secundário

**Tipo de Cópia de Segurança** | **Nó**
--- | ---
Completa | Primário
Diferencial | Primário
Registar |  Secundária
Apenas cópia cheia |  Secundária

#### <a name="no-backup-preference"></a>Sem preferência de backup

**Tipo de Cópia de Segurança** | **Nó**
--- | ---
Completa | Primário
Diferencial | Primário
Registar |  Secundária
Apenas cópia cheia |  Secundária

## <a name="next-steps"></a>Passos seguintes

Saiba como [fazer o back up de uma base de dados](backup-azure-sql-database.md) do SQL Server que está a funcionar num VM Azure.

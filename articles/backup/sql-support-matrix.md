---
title: Matriz de suporte de backup Azure para Backup do Servidor SQL em VMs Azure
description: Fornece um resumo das definições de suporte e limitações ao apoiar o SQL Server em VMs Azure com o serviço de backup Azure.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79410002"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matriz de suporte para Backup do Servidor SQL em VMs Azure

Pode utilizar o Azure Backup para fazer backup nas bases de dados do SQL Server em VMs Azure hospedados na plataforma cloud do Microsoft Azure. Este artigo resume as definições e limitações gerais de suporte para cenários e implementações de Backup SQL Server em VMs Azure.

## <a name="scenario-support"></a>Suporte ao cenário

**Suporte** | **Detalhes**
--- | ---
**Implementações apoiadas** | Os VMs SQL Marketplace Azure e os VMs não-Marketplace (SQL Server instalados manualmente) são suportados.
**Geos apoiados** | Austrália Sudeste (ASE), Austrália Oriental (AE), Austrália Central (AC), Austrália Central 2 (AC) <br> Sul do Brasil (BRS)<br> Canadá Central (CNC), Canadá Leste (CE)<br> Sudeste Asiático (SEA), Leste asiático (EA) <br> LESTE DOS EUA (EUS), Leste dos EUA 2 (EUS2), West Central US (WCUS), West US (WUS); Oeste DOS EUA 2 (WUS 2) Centro Norte DOS EUA (NCUS) Centro DOS EUA (CUS) Centro Sul DOS EUA (SCUS) <br> Índia Central (INC), Índia Sul (INS), Índia Oeste <br> Japão Leste (JPE), Japão Oeste (JPW) <br> Coreia Central (KRC), Coreia do Sul (KRS) <br> Norte da Europa (NE), Europa Ocidental <br> Reino Unido Sul (Reino Unido), Reino Unido Oeste (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Alemanha Norte, Alemanha West Central <br> Suíça Norte, Suíça Oeste <br> França Central <br> China Leste, China Leste 2, China Norte, China Norte 2
**Sistemas operativos suportados** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux não é apoiado atualmente.
**Versões SQL Server suportadas** | SQL Server 2019, SQL Server 2017 conforme detalhado na página de ciclo de vida do [produto Search](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 e SPs conforme detalhado na página de ciclo de vida do produto [Search](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versões suportadas .NET** | .QUADRO LÍQUIDO 4.5.2 ou posteriormente instalado no VM

## <a name="feature-consideration-and-limitations"></a>Consideração e limitações de recurso

* A cópia de segurança do SQL Server pode ser configurada no portal Azure ou **powerShell**. Não apoiamos o CLI.
* A solução é suportada em ambos os tipos de [implementações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) - VMs do Gestor de Recursos Azure e VMs clássicos.
* VM executando SQL Server requer conectividade de internet para aceder a endereços IP públicos Azure.
* A Instância de Cluster failover do servidor SQL **(FCI)** não é suportada.
* As operações de back up e restauro para bases de dados de espelhos e fotografias de bases de dados não são suportadas.
* A utilização de mais de uma solução de backup para fazer backup a sua instância autónoma do SQL Server ou o Grupo SQL Always no grupo de disponibilidade pode levar a falhas de backup; abster-se de fazê-lo.
* Apoiar dois nós de um grupo de disponibilidade individualmente com soluções iguais ou diferentes, também pode levar a falhas de backup.
* O Azure Backup suporta apenas tipos de cópia completos e completos para bases de dados **apenas** de leitura
* Bases de dados com um grande número de ficheiros não podem ser protegidas. O número máximo de ficheiros suportados é **~1000**.  
* Pode fazer o back até **~2000** sQL Server bases de dados num cofre. Pode criar vários cofres caso tenha um maior número de bases de dados.
* Pode configurar backup para até **50** bases de dados de uma só vez; esta restrição ajuda a otimizar as cargas de backup.
* Apoiamos bases de dados até **2 TB** em tamanho; para tamanhos maiores do que os problemas de desempenho podem surgir.
* Para ter uma noção de quantas bases de dados podem ser protegidas por servidor, considere fatores como largura de banda, tamanho VM, frequência de backup, tamanho da base de dados, e assim por diante. [Descarregue](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) o planejador de recursos para calcular o número aproximado de bases de dados que pode ter por servidor com base nos recursos VM e na política de backup.
* Quando os grupos de disponibilidade são configurados, as cópias de segurança são retiradas dos diferentes nós com base em alguns fatores. O comportamento de backup para um grupo de disponibilidade é resumido abaixo.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Back up comportamento com Sempre em grupos de disponibilidade

Recomendamos que a cópia de segurança esteja configurada apenas num nó de um grupo de disponibilidade (AG). Configure sempre a cópia de segurança na mesma região que o nó principal. Por outras palavras, precisa sempre do nó principal para estar presente na região onde está a configurar o backup. Se todos os nós da AG estiverem na mesma região onde o backup está configurado, não há nenhuma preocupação.

#### <a name="for-cross-region-ag"></a>Para AG transversal

* Independentemente da preferência de backup, os backups só serão executados a partir dos nós que estão na mesma região onde a cópia de segurança está configurada. Isto porque os reforços transfronteiriços não são apoiados. Se tiver apenas dois nós e o nó secundário estiver na outra região, os backups continuarão a correr a partir do nó principal (a menos que a sua preferência de reserva seja "apenas secundária").
* Se um nó falhar numa região diferente daquela em que o backup está configurado, os backups falharão nos nós da região falhada.

Dependendo da preferência de reserva e dos tipos de backup (completo/diferencial/log/copy-only full), as cópias de segurança são retiradas de um nó particular (primário/secundário).

#### <a name="backup-preference-primary"></a>Preferência de cópia de segurança: Primária

**Tipo de Cópia de Segurança** | **Node**
--- | ---
Completa | Primária
Diferencial | Primária
Registar |  Primária
Cópia Apenas Cheia |  Primária

#### <a name="backup-preference-secondary-only"></a>Preferência de backup: Secundário

**Tipo de Cópia de Segurança** | **Node**
--- | ---
Completa | Primária
Diferencial | Primária
Registar |  Secundária
Cópia Apenas Cheia |  Secundária

#### <a name="backup-preference-secondary"></a>Preferência de backup: Secundário

**Tipo de Cópia de Segurança** | **Node**
--- | ---
Completa | Primária
Diferencial | Primária
Registar |  Secundária
Cópia Apenas Cheia |  Secundária

#### <a name="no-backup-preference"></a>Sem preferência de backup

**Tipo de Cópia de Segurança** | **Node**
--- | ---
Completa | Primária
Diferencial | Primária
Registar |  Secundária
Cópia Apenas Cheia |  Secundária

## <a name="next-steps"></a>Passos seguintes

Aprenda a fazer o back up a uma base de [dados do SQL Server](backup-azure-sql-database.md) que está a funcionar num VM Azure.
